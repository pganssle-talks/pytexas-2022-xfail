# Using `xfail` and `skipif`: Markers

Bare decorator (with or without `reason`)

```python
@pytest.mark.xfail
def test_my_failing_function():
    assert my_function(-3) == 2


@pytest.mark.xfail(reason="NaN handling is not working properly yet.")
def test_float_function():
    float_function(float("nan"))


@pytest.mark.skip(reason="Probably shouldn't do unconditional skipping")
def test_this_is_pointless():
    some_random_function()
```

--

# Using `xfail` and `skipif`: Markers

## Boolean condition

```python
@pytest.mark.xfail(sys.version_info > (3, 10),
                   reason="AST handling changed in Python 3.10")
def test_some_ast_tomfoolery():
    my_ast_tomfoolery_function("path/to/python_file.py")


@pytest.mark.skipif(sys.version_info < (3, 9),
                    reason="zoneinfo introduced in Python 3.9")
def test_zoneinfo():
    import zoneinfo
    tz = zoneinfo.ZoneInfo("America/Chicago")

    ...
```

<br/>

## Condition string

<br/>

```python
@pytest.mark.skipif("not hasattr(os, 'fspath')")
def path_normalization():
    assert os.fspath(MyPathClass("a/b/c")) == "a/b/c"
```

*Condition strings are discouraged*

Notes:

When using boolean conditions, the `reason` parameter must be specified. `pytest` also supports specifying skip and xfail conditions with a string, which has some weird scoping rules that you can look up. This is the older way of specifying conditions and it is now discouraged, but the one advantage it has over boolean conditions is that the condition itself can serve as the reported reason for the skip, so it is not required to specify `reason`.

--

# Using `xfail` and `skipif`: Markers

## The `run` parameter

```python
@pytest.mark.xfail(
    run=False
    reason="Test will segfault if run!"
)
def test_surrogate_characters():
    parsed = datetime.fromisoformat("2022-03-26\ud800)13:15:04")
    expected = datetime(2022, 3, 26, 13, 15, 4)
    assert parsed == expected
```

<br/>

```python
@pytest.mark.xfail(
    sys.version_info < (3, 9),
    run=False,
    reason="Test segfaults earlier than Python 3.9"
)
def test_conditional_segfault():
    function_that_segfaults_on_38()
```

Notes:

Using `run=False` basically turns an `xfail` into a `skip`, but semantically it may be useful for you to mark that a given test should succeed but doesn't. `run=False` only applies when the test is actually marked as `xfail`, so if you specify a condition, this basically turns the test into `skipif`.

--

# Using `xfail` and `skipif`: Parameterized tests

<br/>

```python
@pytest.mark.parametrize("n", [
    1,
    pytest.param(0,
        marks=pytest.mark.xfail(
            raises=ZeroDivisionError,
            reason="Zero not handled correctly"
        )
    ),
    pytest.param(-1,
        marks=pytest.mark.xfail(
        reason="Not working for negative numbers."
        )
    ),
    5,
])
def test_is_euler_number(n):
    assert is_euler_number(n)
```

<br/>
<br/>

<pre>
<tt class="hljs">
<b>================================================ test session starts =================================================</b>
platform linux -- Python 3.10.2, pytest-7.1.1, pluggy-1.0.0
<b>collected 4 items                                                                                                    </b>

test_euler_numbers.py <font class="pytest-chars-green">.</font><font class="pytest-xfail-yellow">xx</font><font class="pytest-chars-green">.                                                                                     [100%]</font>

<font class="pytest-chars-green">============================================ </font><font class="pytest-green"><b>2 passed</b></font>, <font class="pytest-xfail-yellow">2 xfailed</font><font class="pytest-chars-green"> in 0.03s ============================================</font>
</tt>
</pre>
