# Bonus for pedants: A pseudo-TDD workflow

<br/>

## Test driven development:

1. Write a minimal failing test.
2. Write just enough code to make the test pass.
3. Goto 1.

<br/><br/>

## Reality:

1. Make some quick sketch to see if your idea even works.
2. Maybe write some tests for the sketch.
3. Realize your approach has some fundamental flaws, rip out half of the code and break a bunch of the tests.
4. Clean up and refactor tests.

--

# Bonus for pedants: A pseudo-TDD workflow

```python
def is_perfect_square(n: int) -> bool:
    """Determine if any int i exists such that i × i = n."""
    s = math.sqrt(n)
    return s == int(s)
```

<br/>

Simple enough fix — add the fix and the test at the same time:

```python
def is_perfect_square(n: int) -> bool:
    """Determine if any int i exists such that i × i = n."""
    if n < 0:
        return False  # No negative numbers are perfect squares

    s = math.sqrt(n)
    return s == int(s)


def test_negative():
    """No negative values are perfect squares."""
    assert not is_perfect_square(-4)
```

https://blog.ganssle.io/articles/2021/11/pseudo-tdd-xfail.html

--

# Rewriting history

```python
@pytest.mark.xfail(raises=ValueError, strict=True,
                   reason="Domain error when passed negative numbers.")
def test_negative():
    ...
```

<br/>

Use `git add -p` or `hg commit -i` to selectively commit the test *before* the fix:

```diff
@@ -7,6 +7,11 @@ def is_perfect_square(n: int) -> bool:
     s = math.sqrt(n)
     return s == int(s)

\+@pytest.mark.xfail(raises=ValueError, strict=True,
\+                   reason="Domain error")
\+def test_negative():
\+    """No negative numbers are perfect squares."""
\+    assert not is_perfect_square(-4)

 def test_positive():
     assert is_perfect_square(4)
```
<br/>

Remove the `xfail` decorator and commit the fix + removal:

```diff
@@ -4,11 +4,12 @@ import pytest

 def is_perfect_square(n: int) -> bool:
     """Determine if any int i exists such that i × i = n."""
\+    if n < 0:
\+        return False  # No negative numbers are perfect squares
\+
     s = math.sqrt(n)
     return s == int(s)

-@pytest.mark.xfail(raises=ValueError, strict=True,
-                   reason="Domain error")
 def test_negative():
     """No negative numbers are perfect squares."""
     assert not is_perfect_square(-4)
```

--

One of many options to run tests against all commits:

<pre><tt class="hljs">$ git rev-list main..fix_negative_numbers | \
    xargs -I{} sh -c &apos;git checkout {} &amp;&amp; pytest -q&apos; \
    && git checkout main

HEAD is now at 04a2dd7 Fix domain error with negative numbers
<font class="pytest-chars-green">.................                                                                                             [100%]</font>
<font color="#4BE234"><b>17 passed</b></font><font class="pytest-chars-green"> in 0.02s</font>
Previous HEAD position was 04a2dd7 Fix domain error with negative numbers
HEAD is now at c0a9722 Add failing test for negative squares
<font class="pytest-chars-green">..............</font><font class="pytest-xfail-yellow">xxx</font><font class="pytest-chars-green">                                                                                             [100%]</font>
<font color="#4BE234"><b>14 passed</b></font>, <font class="pytest-xfail-yellow">3 xfailed</font><font class="pytest-chars-green"> in 0.03s</font>
Previous HEAD position was c0a9722 Add failing test for negative squares
Switched to branch &apos;main&apos;
</tt>
</pre>

Merge and the history looks like we were using TDD the whole time:

<pre><tt class="hljs">$ git log --oneline --decorate --graph
* <font class="pytest-xfail-yellow">04a2dd7 (</font><font color="#34E2E2"><b>HEAD -&gt; </b></font><font color="#4BE234"><b>main</b></font><font class="pytest-xfail-yellow">, </font><font color="#4BE234"><b>fix_negative_numbers</b></font><font class="pytest-xfail-yellow">)</font> Fix domain error with negative numbers
* <font class="pytest-xfail-yellow">c0a9722</font> Add failing test for negative squares
* <font class="pytest-xfail-yellow">f9acaa7</font> Add implementation for perfect square
</tt>
</pre>

https://blog.ganssle.io/articles/2021/11/pseudo-tdd-xfail.html

--

# Advantages

- Gives you some confidence that your tests worked
- Makes it easier to verify that a test should be working
- Tells a clear story for future maintainers

<br/> <br/>

# Disadvantages

- It will be hard to get anyone else to do this.
- It's not easy to run the tests against every commit (on GitHub, Gitlab or locally)

<br/> <br/>

https://blog.ganssle.io/articles/2021/11/pseudo-tdd-xfail.html
