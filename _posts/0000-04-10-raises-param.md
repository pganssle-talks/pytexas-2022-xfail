## Being specific about *why* the test is failing

```python
@pytest.mark.xfail(reason="Bug #11493: Negative values not supported!")
@pytest.mark.parametrize("n", [-1, -3, -4])
def test_negative(n):
    # When called with a negative value for n, this test raises ValueError!
    assert not square_mod.is_perfect_square(m)
```

<br/>

<pre>
<tt class="hljs">
<b>================================================ test session starts ================================================</b>
platform linux -- Python 3.10.2, pytest-7.1.1, pluggy-1.0.0
plugins: subtests-0.7.0, runtime-xfail-1.0.3, cov-3.0.0, hypothesis-6.39.4
<b>collected 17 items                                                                                                  </b>

test_square_mod.py <font color="#10BA13">..............</font><font color="#C4A000">xxx</font><font color="#10BA13">                                                                          [100%]</font>

<font color="#10BA13">=========================================== </font><font color="#4BE234"><b>14 passed</b></font>, <font color="#C4A000">3 xfailed</font><font color="#10BA13"> in 0.03s ===========================================</font>
</tt>
</pre>

<br/>

**Actually failing because of `NameError`** <!-- .element class="fragment" data-fragment-index="0" -->

--

## Being specific about *why* the test is failing

```python
@pytest.mark.xfail(
    raises=ValueError,
    reason="Bug #11493: Negative values not supported!"
    )
@pytest.mark.parametrize("n", [-1, -3, -4])
def test_negative(n):
    # When called with a negative value for n, this test raises ValueError!
    assert not square_mod.is_perfect_square(m)
```

<br/>

<pre><tt class="hljs">
<b>================================================ test session starts ================================================</b>
platform linux -- Python 3.10.0, pytest-7.1.1, pluggy-1.0.0
<b>collected 17 items                                                                                                  </b>

test_square_mod.py <font color="#10BA13">..............</font><font color="#F61010">FFF                                                                          [100%]</font>

===================================================== FAILURES ======================================================
<font color="#EF2929"><b>_________________________________________________ test_negative[-1] _________________________________________________</b></font>
<font color="#EF2929"><b>test_square_mod.py</b></font>:21: in test_negative
    <font color="#729FCF">assert</font> <font color="#AD7FA8">not</font> square_mod.is_perfect_square(m)
<font color="#EF2929"><b>E   NameError: name &apos;m&apos; is not defined</b></font>
<font color="#EF2929"><b>_________________________________________________ test_negative[-3] _________________________________________________</b></font>
<font color="#EF2929"><b>test_square_mod.py</b></font>:21: in test_negative
    <font color="#729FCF">assert</font> <font color="#AD7FA8">not</font> square_mod.is_perfect_square(m)
<font color="#EF2929"><b>E   NameError: name &apos;m&apos; is not defined</b></font>
<font color="#EF2929"><b>_________________________________________________ test_negative[-4] _________________________________________________</b></font>
<font color="#EF2929"><b>test_square_mod.py</b></font>:21: in test_negative
    <font color="#729FCF">assert</font> <font color="#AD7FA8">not</font> square_mod.is_perfect_square(m)
<font color="#EF2929"><b>E   NameError: name &apos;m&apos; is not defined</b></font>
============================================== short test summary info ==============================================
FAILED test_square_mod.py::test_negative[-1] - NameError: name &apos;m&apos; is not defined
FAILED test_square_mod.py::test_negative[-3] - NameError: name &apos;m&apos; is not defined
FAILED test_square_mod.py::test_negative[-4] - NameError: name &apos;m&apos; is not defined
<font color="#F61010">=========================================== </font><font color="#EF2929"><b>3 failed</b></font>, <font color="#10BA13">14 passed</font><font color="#F61010"> in 0.09s ============================================</font>
</tt></pre>
