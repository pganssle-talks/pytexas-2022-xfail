# `xfail` and `skip`: What to do with tests you know will fail

When you have a test that you know will fail, do you skip it, or mark it as an expected failure? Or should you wait until the test is passing before adding it to your test suite?

`pytest` offers two separate but distinct markers for tests that you know won't succeed: `skipif` and `xfail`. In this talk, I'll explore the differences between these two markers, and some strategies you can employ to make the best use of each. I'll also briefly touch on how to translate this approach into the `unittest` framework, since the underlying concepts are mostly transferable.

External images used in this presentation can be found in the `external-images` folder, with an `attribution.md` file at each level of the folder giving attribution to the creators. If you notice any missing attributions, please report it as an issue.
