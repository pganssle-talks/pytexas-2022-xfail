# Using `xfail` and `skip`

```python
@pytest.mark.parametrize("payload", list(
    pathlib.Path.glob("test_data/test_data_*")
))
def test_process_data_files(payload):
    with open(payload, "rb") as f:
        test_data = load_data(f)

    if test_data.num_elements > 255:
        # Don't actually use this! See next slide!
        pytest.xfail(reason="bug #2445: Processing of data files with > 255 fails.")
```

<br/>

Can embed these into functions, context managers and fixtures

<br/>

```python
@contextlib.contextmanager
def in_local_timezone(tz: str) -> Iterator[None]:
    if sys.platform.startswith("win") or os.eviron.get("TZ_CHANGE_DISALLOWED") == "true":
        pytest.skip("Time zone change not allowed")

    old_tz = get_current_tz()
    set_tz(tz)
    time.tzset()
    yield
    set_tz(old_tz)
    time.tzset()


def test_in_new_york():
    # This test is skipped automatically in platforms and environments that
    # don't support changing the time zone.
    with in_local_timezone("America/New_York"):
        assert my_date_function("2021-01-01") > my_date_function("2020-01-01")
```

--

# Papercut: `pytest.xfail()` is basically skip!

- `pytest.xfail()` stops execution immediately — there is no way to get `XPASS`
- `xfail_strict` has no effect
- [This is deliberate](https://github.com/pytest-dev/pytest/issues/7071)

<br/><br/>

# Solution: [`pytest-runtime-xfail`](https://github.com/okken/pytest-runtime-xfail)

```python
@pytest.mark.parametrize("payload", list(
    pathlib.Path.glob("test_data/test_data_*")
))
def test_process_data_files(payload, runtime_xfail):
    with open(payload, "rb") as f:
        test_data = load_data(f)

    if test_data.num_elements > 255:
        # Don't actually use this! See next slide!
        runtime_xfail(reason="bug #2445: Processing of data files with > 255 fails.")
```

--

# Papercut: `xfail` with `hypothesis`

```python
import hypothesis
from hypothesis import strategies as st

@hypothesis.given(n=st.integers())
def test_my_function(n: int) -> None:
    if n < 0:
        pytest.xfail("Zero and negative numbers not working!")

    assert my_function(n) > n
```
<br/>

## Problems:

- Reports a single top-level `XFAIL`:

<pre>
<tt class="hljs">
<b>================================================ test session starts =================================================</b>
platform linux -- Python 3.10.2, pytest-7.1.1, pluggy-1.0.0
plugins: hypothesis-6.39.4
<b>collected 1 item                                                                                                     </b>

test_hypothesis.py <font class="pytest-xfail-yellow">x                                                                                           [100%]</font>

<font class="pytest-xfail-yellow">================================================= </font><font class="pytest-xfail-yellow"><b>1 xfailed</b></font><font class="pytest-xfail-yellow"> in 0.22s =================================================</font>
</tt>
</pre>

- Disguises real failures in non-`xfail` cases
- Doesn't work with `pytest-runtime-xfail`
