```python
import math

def is_perfect_square(n: int) -> bool:
    """Determine if any int i exists such that i × i = n."""
    s = math.sqrt(n)
    return s == int(s)
```

With tests:

```python
import square_mod

import pytest


@pytest.mark.parametrize("n", [0, 1, 2, 4, 9, 16, 25, 36])
def test_squares(n):
    assert square_mod.is_perfect_square(n)


@pytest.mark.parametrize("n", [3, 5, 6, 7, 8, 27, 32])
def test_non_squares(n):
    assert not square_mod.is_perfect_square(n)
```
<br/>

<pre>
<tt class="hljs">
<b>================================================ test session starts ================================================</b>
platform linux -- Python 3.10.0, pytest-7.1.1, pluggy-1.0.0
<b>collected 14 items                                                                                                  </b>

test_square_mod.py <font color="green">..............                                                                             [100%]</font>

<font color="green">================================================ </font><font color="green"><b>14 passed</b></font><font color="green"> in 0.02s =================================================</font>
</tt></pre>

Notes:

So let's imagine that I have this function that determines if a given integer is a perfect square, and some tests for that function, and we'll add some tests for it that show that yes indeed that's what this function does - it gives True for all these perfect squares and False for all these non-square numbers. Perfect work for a Thursday afternoon, let's go ahead and ship it straight to production, no problem!


--

## Found a bug: Add a test

```python
def test_negative():
    assert not square_mod.is_perfect_square(-4)
```
<br/><br/>

<pre>
<tt class="hljs">
<b>================================================ test session starts ================================================</b>
platform linux -- Python 3.10.0, pytest-7.1.1, pluggy-1.0.0
<b>collected 15 items                                                                                                  </b>

test_square_mod.py <font color="green">..............</font><font color="#8B0000">F                                                                            [100%]</font>

===================================================== FAILURES ======================================================
<font color="#8B0000"><b>___________________________________________________ test_negative ___________________________________________________</b></font>

    <font color="#729FCF">def</font> <font color="green">test_negative</font>():
&gt;       <font color="#729FCF">assert</font> <font color="#AD7FA8">not</font> square_mod.is_perfect_square(-<font color="#729FCF">4</font>)

<font color="#8B0000"><b>test_square_mod.py</b></font>:17: 
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _

n = -4

    <font color="#729FCF">def</font> <font color="green">is_perfect_square</font>(n: <font color="#34E2E2">int</font>) -&gt; <font color="#34E2E2">bool</font>:
        <font color="green">&quot;&quot;&quot;Determine if any int i exists such that i × i = n.&quot;&quot;&quot;</font>
&gt;       s = math.sqrt(n)
<font color="#8B0000"><b>E       ValueError: math domain error</b></font>

<font color="#8B0000"><b>square_mod.py</b></font>:5: ValueError
============================================== short test summary info ==============================================
FAILED test_square_mod.py::test_negative - ValueError: math domain error
<font color="#8B0000">=========================================== </font><font color="#8B0000"><b>1 failed</b></font>, <font color="green">14 passed</font><font color="#8B0000"> in 0.08s ============================================</font>
</tt>
</pre>

--

## xfail: Tests that are *expected* to fail


```python
@pytest.mark.xfail
def test_negative():
    assert not square_mod.is_perfect_square(-4)
```

<br/><br/>
Failure is expected, so the tests pass:

<pre><tt class="hljs">
<b>================================================ test session starts ================================================</b>
platform linux -- Python 3.10.0, pytest-7.1.1, pluggy-1.0.0
<b>collected 15 items                                                                                                  </b>

test_square_mod.py <font color="green">..............</font><font color="green">x</font><font color="green">                                                                            [100%]</font>

<font color="green">=========================================== </font><font color="green"><b>14 passed</b></font>, <font color="green">1 xfailed</font><font color="green"> in 0.03s ===========================================</font>
</tt></pre>
