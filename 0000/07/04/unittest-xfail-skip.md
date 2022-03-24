# Using `unittest`

```python
import unittest

class MyTest(unittest.TestCase):
    def test_pass(self):
        self.assertTrue(True)

    @unittest.expectedFailure
    def test_xfail(self):
        self.assertTrue(False)

    @unittest.expectedFailure
    def test_xpass(self):
        self.assertTrue(True)

```

<br/>

<div class="fragment disappearing-fragment nospace-fragment fade-out" data-fragment-index="0">
Run with <tt>unittest</tt>:

<pre>
<tt class="hljs">
$ python -m unittest -v
test_pass (test_unittest.MyTest) ... ok
test_xfail (test_unittest.MyTest) ... expected failure
test_xpass (test_unittest.MyTest) ... unexpected success

----------------------------------------------------------------------
Ran 3 tests in 0.001s

FAILED (expected failures=1, unexpected successes=1)
</tt></pre>
</div>

<div class="fragment nospace-fragment fade-in" data-fragment-index="0">
Run with <tt>pytest</tt>:

<pre>
<tt class="hljs">
$ pytest test_unittest.py 
<b>================================================ test session starts =================================================</b>
platform linux -- Python 3.10.2, pytest-7.1.1, pluggy-1.0.0
<b>collected 3 items                                                                                                    </b>

test_unittest.py <font color="#10BA13">.</font><font color="#C4A000">x</font><font color="#F61010">F                                                                                           [100%]</font>

====================================================== FAILURES ======================================================
<font color="#EF2929"><b>_________________________________________________ MyTest.test_xpass __________________________________________________</b></font>
Unexpected success
============================================== short test summary info ===============================================
FAILED test_unittest.py::MyTest::test_xpass
<font color="#F61010">======================================= </font><font color="#EF2929"><b>1 failed</b></font>, <font color="#10BA13">1 passed</font>, <font color="#C4A000">1 xfailed</font><font color="#F61010"> in 0.04s =======================================</font>
</tt>
</pre>
</div>

--

# `unittest`: Missing features

- No `strict=False` or `run=False`
- No support for `raises`
- No way to specify a reason
- Conditional `xfail` not built-in:

  <br/>

```python
def conditional_xfail(condition):
    if condition:
        return unittest.expectedFailure
    else:
        return lambda x: x
```

--

# Skipping with `unittest`
<br/>

Decorators:

```python
class MySkipTest(unittest.TestCase):
    @unittest.skip("Unconditional skipping")
    def skip_unconditional(self):
        pass

    @unittest.skipIf(sys.platform.startswith("win"), "Not supported on Windows")
    def test_nix_only(self):
        ...

    @unittest.skipUnless(sys.platform.startswith("win"), "Only supported on Windows")
    def test_windows_only(self):
        ...
```

<br/>
<br/>

At runtime:

```python
    def test_three_day_weekend(self):
        if 4 <= datetime.now().weekday() <= 6:
            self.skipTest("This test has negotiated a 3-day weekend.")

        ...



    def test_api_integration(self):
        r = requests.get("https://url.to/some/resource")
        if r.status_code != 200:
            raise unittest.SkipTest("Resource not available")

        ...
```
