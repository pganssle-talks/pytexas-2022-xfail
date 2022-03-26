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
