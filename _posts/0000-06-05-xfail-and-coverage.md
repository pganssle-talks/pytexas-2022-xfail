# `xfail` and code coverage

```python
def do_fizzbuzz(n: int) -> str | int:
    if (n % 15 == 0):
        return "fizzbuzz"

    if (n % 3 == 0):
        return "fizz"

    if (n % 5 == 0):
        return "buzz"

    return n
```

Some passing tests:

```python
import pytest

@pytest.mark.parametrize("n, expected", [
    (3, "fizz"),
    (1, 1),
    (2, 2),
    (14, 14),
    (15, "fizzbuzz"),
])
def test_fizzbuzz(n, expected):
    assert do_fizzbuzz(n) == expected
```

One failing test:

```python
@pytest.mark.xfail(reason="Not implemented yet")
def test_fizzbuzz_failure():
    """Fizzbuzz should fail when passed a negative number."""
    with pytest.raises(ValueError):
        do_fizzbuzz(-5)  # Foreshadowing: this hits the n == 5 case!
```

--

Run with `pytest --cov`:

<pre>
<tt class="hljs">
<b>================================================ test session starts =================================================</b>
platform linux -- Python 3.10.2, pytest-7.1.1, pluggy-1.0.0
plugins: cov-3.0.0
<b>collected 23 items / 17 deselected / 6 selected</b>

test_fizzbuzz.py <font color="#10BA13">.....</font><font color="#C4A000">x</font><font color="#10BA13">                                                                                        [100%]</font>

---------- coverage: platform linux, python 3.10.2-final-0 -----------
Name          Stmts   Miss  Cover
---------------------------------
fizzbuzz.py       8      0   100%
---------------------------------
TOTAL             8      0   100%


<font color="#10BA13">==================================== </font><font color="#4BE234"><b>5 passed</b></font>, <font color="#C4A000">17 deselected</font>, <font color="#C4A000">1 xfailed</font><font color="#10BA13"> in 0.05s =====================================</font>
</tt>
</pre>

✨🎉 100% code coverage! 🎉✨

--

```python
def do_fizzbuzz(n: int) -> str | int:
    if n < 0:
        raise ValueError(f"Number must be positive, but got {n}")

    if (n % 15 == 0):
        return "fizzbuzz"

    if (n % 3 == 0):
        return "fizz"

    if (n % 5 == 0):
        return "buzz"  # This line is no longer covered!

    return n
```

<br/>

Remove the `xfail` decorator, code coverage goes **down**! 😰😰😰

<pre>
<tt class="hljs">
<b>================================================ test session starts =================================================</b>
platform linux -- Python 3.10.2, pytest-7.1.1, pluggy-1.0.0
plugins: cov-3.0.0
<b>collected 23 items / 17 deselected / 6 selected                                                                      </b>

test_fizzbuzz.py <font color="#10BA13">.....</font><font color="#C4A000">X                                                                                        [100%]</font>

---------- coverage: platform linux, python 3.10.2-final-0 -----------
Name          Stmts   Miss  Cover
---------------------------------
fizzbuzz.py      10      1    90%
---------------------------------
TOTAL            10      1    90%


<font color="#C4A000">==================================== </font><font color="#10BA13">5 passed</font>, <font color="#FCE94F"><b>17 deselected</b></font>, <font color="#FCE94F"><b>1 xpassed</b></font><font color="#C4A000"> in 0.05s =====================================</font>
</tt>
</pre>

--

```python
def do_fizzbuzz(n: int) -> str | int:
    if (n % 15 == 0):
        return "fizzbuzz"

    if (n % 3 == 0):
        return "fizz"

    if (n % 5 == 0):
        return "buzz"

    return n
```

Some passing tests:

```python
import pytest

@pytest.mark.parametrize("n, expected", [
    (3, "fizz"),
    (1, 1),
    (2, 2),
    # 3, 1, 2, but no 5!
    (14, 14),
    (15, "fizzbuzz"),
])
def test_fizzbuzz(n, expected):
    assert do_fizzbuzz(n) == expected
```

One failing test:

```python
@pytest.mark.xfail(reason="Not implemented yet")
def test_fizzbuzz_failure():
    """Fizzbuzz should fail when passed a negative number."""
    with pytest.raises(ValueError):
        do_fizzbuzz(-5)  # -5 % 5 == 0
```

--

## The Problem

- Code coverage metrics assume that your tests *passed*

- Expected failures don't pass, but they also don't cause the test suite to fail!

Notes:

Some lines are hit by well-executed passing tests, and some lines are hit as collateral damage from the flailing death throes of an expected failure. They both show up the same in the coverage statistics!


--

# The Solution: Exclude expected failures from coverage

<br/>

With `pytest-cov`, attach the `no_cover` marker automatically in your `conftest.py`:

<br/>

```python
def pytest_collection_modifyitems(items):
    for item in items:
        marker = item.get_closest_marker("xfail")

        # Need to query the args because conditional xfail tests still have
        # the xfail mark even if they are not expected to fail
        if marker and (not marker.args or marker.args[0]):
            item.add_marker(pytest.mark.no_cover)
```

<br/>

See https://stackoverflow.com/q/53191930/467366

<br/>

[**`coverage.py` wants to add this feature natively**](https://github.com/nedbat/coveragepy/issues/727) — you could be the one to implement it!

--

# Does this matter?

<br/>

**Probably not** <fragment/>
