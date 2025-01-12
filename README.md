# Julia-Cache Bug with Jupyter
2025-01-13

This is a sample repository to demonstrate a bug in
[julia-actions/cache](https://github.com/julia-actions/cache) related to
Jupyter Notebooks.

It contains a simple Julia notebook that imports the Example package and
prints “hello world”.

GitHub Actions are used to automatically test the code. The workflow is
as follows: First, Jupyter is installed using the [setup-python
action](https://github.com/actions/setup-python):

``` yaml
- name: Install Python and then Jupyter
  uses: actions/setup-python@v5
  with:
    python-version: '3.12'
    cache: 'pip'
- run: pip install jupyter
```

Then
[julia-actions/setup-julia](https://github.com/julia-actions/setup-julia)
is used to install the **IJulia Jupyter kernel**, which is needed to run
Julia notebooks with Jupyter; To speed up the build process,
[julia-actions/cache](https://github.com/julia-actions/cache) is used
for caching.

``` yaml
#this doesn't work together with Jupyter.
- name: Cache Julia Packages
  uses: julia-actions/cache@v2

- name: Install important Julia packages
  shell: bash
  run: |
    julia -e 'using Pkg; Pkg.add("Example");'
    julia -e 'using Pkg; Pkg.add("IJulia");'
```

If everything is working correctly, the GitHub action should run
successfully, indicated by a green check mark. However, if you are using
the Julia cache action, the workflow will fail with the following error:

``` bash
ERROR | No such kernel named julia-1.11
Error: Process completed with exit code 1.
```

**Since the Github action works correctly without the Julia cache, the
problem must be with the Julia cache action.**

For comparison, you can look at the v1 tag (without cache) and v2 (with
cache). The GitHub action for v1 works fine, the one for v2 does not.

