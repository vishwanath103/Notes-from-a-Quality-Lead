### 1. pytest vs pyunit (unittest)

**pyunit** (officially called `unittest`) is Python’s built-in testing framework, inspired by Java’s JUnit. **pytest** is a third-party testing framework that has become the de facto standard in the Python community.

| Feature         | unittest (pyunit)        | pytest                         |
|-----------------|-------------------------|--------------------------------|
| Test discovery  | Manual or naming rules  | Automatic, flexible            |
| Assertions      | Uses `self.assert*`     | Use plain `assert` statements  |
| Fixtures        | SetUp/TearDown methods  | Powerful, flexible fixtures    |
| Plugins         | Limited                 | Extensive plugin ecosystem     |
| Parameterization| Manual loops            | Built-in `@pytest.mark.parametrize` |
| Readability     | Verbose, class-based    | Concise, function or class     |

**Key Points:**
- **pytest** is more concise, readable, and powerful, especially for larger or more complex test suites.
- **unittest** is included in the standard library, so it has zero dependencies.

---

### 2. Naming Conventions

- **Test functions and methods** must be prefixed with `test_` so pytest can automatically discover them.
    ```python
    def test_addition():
        assert 1 + 1 == 2
    ```
- **Test classes** should be named with `Test` as a prefix and should not have an `__init__` method. They group related tests and can share fixtures.
    ```python
    class TestMath:
        def test_subtraction(self):
            assert 2 - 1 == 1
    ```
- **Why?**: This naming convention enables pytest to discover and run tests automatically, reducing manual configuration.

---

### 3. pytest as a Test Framework and Test Runner

- **Test Framework**: Provides tools and conventions for writing tests (assertions, fixtures, markers, etc.).
- **Test Runner**: Finds and executes tests, reporting results in a user-friendly manner.
    - Run tests with `pytest` command in the terminal.
    - Supports features like test selection, parallel execution (via plugins), and rich output.

---

### 4. Test Classes vs Test Functions

- **Test Functions**: Simple, ideal for stateless tests.
    ```python
    def test_simple_case():
        assert True
    ```
- **Test Classes**: Group related tests, can use class-level fixtures, and support setup/teardown at the class level.
    ```python
    class TestDatabase:
        @classmethod
        def setup_class(cls):
            # Setup resources
            pass

        def test_query(self):
            assert True
    ```
- **Benefits of Classes**:
    - Organize tests logically.
    - Share setup/teardown code.
    - Reuse fixtures across multiple tests.

---

### 5. Markers

**Markers** are special labels attached to tests to modify their behavior or add metadata.

#### Uses:
- **Categorization**: Group tests (e.g., `@pytest.mark.slow`, `@pytest.mark.smoke`).
- **Control Execution**: Skip tests, run conditionally, expect failures.
- **Metadata**: Custom tags for reporting or selection.
- **Parameterization**: Run the same test with different inputs.

#### Example:
```python
import pytest

@pytest.mark.slow
def test_heavy_computation():
    ...

@pytest.mark.skip(reason="Not implemented yet")
def test_future_feature():
    ...
```

#### Custom Marker:
```python
@pytest.mark.integration
def test_external_service():
    ...
```
Register custom markers in `pytest.ini` to avoid warnings:
```ini
[pytest]
markers =
    integration: marks tests as integration tests
```

---

### 6. Built-in Markers

- **filterwarnings**: Control warnings during test execution.
- **usefixtures**: Apply fixtures to test functions or classes.
- **parametrize**: Run a test with multiple sets of arguments.
    ```python
    @pytest.mark.parametrize("a,b,result", [(1,2,3), (2,3,5)])
    def test_add(a, b, result):
        assert a + b == result
    ```
- **skip**: Skip a test unconditionally.
    ```python
    @pytest.mark.skip(reason="Temporary skip")
    ```
- **skipif**: Skip a test if a condition is true.
    ```python
    @pytest.mark.skipif(sys.platform == "win32", reason="Does not run on Windows")
    ```
- **xfail**: Mark a test as expected to fail.
    ```python
    @pytest.mark.xfail(reason="Known bug")
    ```

---

### Summary Table

| Marker         | Purpose                                        |
|----------------|------------------------------------------------|
| `skip`         | Unconditionally skip a test                    |
| `skipif`       | Skip test if a condition is true               |
| `xfail`        | Test is expected to fail                       |
| `parametrize`  | Run test multiple times with different inputs  |
| `usefixtures`  | Apply fixtures to tests                        |
| `filterwarnings`| Control warnings during test run              |

## Creating a custom marker

### 1. **Register the Marker in pytest.ini**

Create or update your `pytest.ini` file in the root of your project to register the custom marker and avoid warnings:

```ini
# pytest.ini
[pytest]
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
```

---

### 2. **Mark a Test as Slow**

In your test file, use `@pytest.mark.slow` to indicate a slow test:

```python
# test_example.py
import pytest
import time

def test_fast():
    assert 1 + 1 == 2

@pytest.mark.slow
def test_slow_operation():
    time.sleep(5)  # Simulates a slow operation
    assert True
```

---

### 3. **Run All Tests**

If you simply run `pytest`, both tests will be executed:

```shell
pytest
```

Output:
```
collected 2 items

test_example.py ..                         [100%]
```

---

### 4. **Run Only Slow Tests**

To run only the tests marked as `slow`:

```shell
pytest -m slow
```

Output:
```
collected 2 items / 1 deselected / 1 selected

test_example.py .                          [100%]
```

---

### 5. **Exclude Slow Tests**

To run all tests except those marked as `slow`:

```shell
pytest -m "not slow"
```

Output:
```
collected 2 items / 1 deselected / 1 selected

test_example.py .                          [100%]
```

---

### 6. **Summary Table**

| Command                 | What it does                    |
|-------------------------|---------------------------------|
| `pytest`                | Runs all tests                  |
| `pytest -m slow`        | Runs only slow tests            |
| `pytest -m "not slow"`  | Runs all except slow tests      |

---

### 7. **Why use this?**

- **Faster development cycles:** Skip slow tests during frequent runs.
- **Better CI/CD:** Run slow tests only in nightly or full regression builds.
- **Documentation:** Communicates intent to other developers.


--- 

## pytest vs MATLAB

| Feature          | pytest                                   | MATLAB Unit Test Framework                |
|------------------|------------------------------------------|-------------------------------------------|
| Test Discovery   | By name (test_)                          | By name (Test.m, test prefix)             |
| Test Functions   | Yes                                      | Yes                                       |
| Test Classes     | Yes                                      | Yes (inherit TestCase)                    |
| Markers/Tags     | @pytest.mark.* (customizable)            | TestTags attribute                        |
| Skipping         | @pytest.mark.skip/skipif, xfail          | assumeFail, assumeTrue, ExpectedFailure   |
| Parameterization | @pytest.mark.parametrize                  | TestParameter, TestParameterDefinition    |
| Fixtures         | @pytest.fixture                           | TestMethodSetup, TestClassSetup, Fixture  |
| Select by Tag    | pytest -m markername                      | runtests('Tag','tagname')                 |
