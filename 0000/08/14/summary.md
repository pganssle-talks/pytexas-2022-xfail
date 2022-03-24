# Summary

- Use `xfail` for tests that you know are failing, but shouldn't be.
- Always use `skip` conditionally — it's for tests that aren't *supposed* to pass.
- Set `xfail_strict=True`

<br/><br/>

## Further reading

- "How and why I use pytest's `xfail`": https://blog.ganssle.io/articles/2021/11/pytest-xfail.html

- "A pseudo-TDD workflow using expected failures": https://blog.ganssle.io/articles/2021/11/pseudo-tdd-xfail.html

- "`xfail` and code coverage": https://blog.ganssle.io/articles/2021/12/xfail-coverage.html

- `pytest-runtime-xfail`: https://pypi.org/project/pytest-runtime-xfail/

- Also featured on Test & Code: https://testandcode.com/guests/paul-ganssle
