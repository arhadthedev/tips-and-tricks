Bug is a divergence between documented and observed behavior. So, for any [[Desktop Application]], [[Web Service Backend]], and [[Web Site or Web Service Frontend]] we need to:

- include all expected and unexpected behavior into [[Software Documentation]]
- thus, the documentation becomes a specification for unit, integration, and conformance tests.

Tests:

We use `unittest` Python module as a test runner and supply test scenarios as Python scripts in `tests/{type}` directory.

- test coverage
- dynamic code analysis
- conformance tests: usually third party black box tests so we supply them as Git submodules. Example commit message: Add conformance tests for ECMA-262
- mutation tests

Static analysis:
- https://github.blog/2022-08-15-the-next-step-for-lgtm-com-github-code-scanning/
- Coverity