# `skipif` vs `xfail`

<br/><br/>

## `skipif` — Use for tests that *are supposed to fail*.

```python
@pytest.mark.skipif(sys.version_info < (3, 8),
                    reason="Module-level __getattr__ introduced in Python 3.8")
def test_module_getattr_works():
    with clear_dateutil_imports():
        import dateutil
        assert dateutil.tz is not None  # Lazy-loaded
```

<br/><br/>

## `xfail` — Use for tests that *currently fail, but shouldn't*.

```python
@pytest.mark.xfail(reason="Fractional hours and minutes not implemented yet!")
def test_fractional_hour():
    # ISO 8601 allows fractional hours and minutes
    assert (dateutil.parser.isoparse("2021-03-26T14.5") ==
            datetime.datetime(2021, 3, 26, 14, 30))
```

Notes:

I thought I'd start by clearing up a very common source of confusion, which is the difference between `xfail` and `skip`. Both `skip` and `xfail` are used to indicate that a test isn't going to succeed, but there are both behavioral and semantic differences between these.

The behavioral difference is easy to explain — with `skip` the test is skipped and never run, whereas with `xfail` — which means expected failure — the default is to run the test, but to not consider the entire test suite as failing if only expected failure tests are failing.

The semantic difference between these is a bit subtler — you should use `skipif` if there are conditions under which the test is *supposed* to fail — for example in `dateutil` we support lazy-loaded modules starting in Python 3.8, but we support earlier versions of Python, so we want to skip the lazy-loaded module tests when testing against earlier versions.

With `xfail`, you are indicating that the test *should* be passing, but you haven't gotten around to fixing the bug or implementing the feature yet. For example, the `dateutil` ISO 8601 parsers is supposed to handle all valid ISO formats, but currently it doesn't work for particularly obscure ones like formats with fractional hours. Luckily, no one cares about these formats, so it's not a high priority, but I at some point I do plan to get around to implementing the feature, so hopefully one day this test will start passing.
