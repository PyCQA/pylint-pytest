## NOTE

This package has moved to https://github.com/pylint-dev/pylint-pytest. Active development occurs on that fork.

# pylint-pytest

[![PyPI version fury.io](https://badge.fury.io/py/pylint-pytest.svg)](https://pypi.python.org/pypi/pylint-pytest/)
[![Travis CI](https://travis-ci.org/reverbc/pylint-pytest.svg?branch=master)](https://travis-ci.org/reverbc/pylint-pytest)
[![AppVeyor](https://ci.appveyor.com/api/projects/status/github/reverbc/pylint-pytest?branch=master&svg=true)](https://ci.appveyor.com/project/reverbc/pylint-pytest)

A Pylint plugin to suppress pytest-related false positives.

## Installation

Requirements:

- `pylint`
- `pytest>=4.6`

To install:

```bash
$ pip install pylint-pytest
```

## Usage

Enable via command line option `--load-plugins`

```bash
$ pylint --load-plugins pylint_pytest <path_to_your_sources>
```

Or in `pylintrc`:

```ini
[MASTER]
load-plugins=pylint_pytest
```

## Suppressed Pylint Warnings

### `unused-argument`

FP when a fixture is used in an applicable function but not referenced in the function body, e.g.

```python
def test_something(conftest_fixture):  # <- Unused argument 'conftest_fixture'
    assert True
```

### `unused-import`

FP when an imported fixture is used in an applicable function, e.g.

```python
from fixture_collections import imported_fixture  # <- Unused imported_fixture imported from fixture_collections

def test_something(imported_fixture):
    ...
```

### `redefined-outer-name`

FP when an imported/declared fixture is used in an applicable function, e.g.

```python
from fixture_collections import imported_fixture

def test_something(imported_fixture):  # <- Redefining name 'imported_fixture' from outer scope (line 1)
    ...
```

### `no-member`

FP when class attributes are defined in setup fixtures

```python
import pytest

class TestClass(object):
    @staticmethod
    @pytest.fixture(scope='class', autouse=True)
    def setup_class(request):
        cls = request.cls
        cls.defined_in_setup_class = True

    def test_foo(self):
        assert self.defined_in_setup_class  # <- Instance of 'TestClass' has no 'defined_in_setup_class' member
```

## Raise new warning(s)

### W6401 `deprecated-pytest-yield-fixture`

Raise when using deprecated `@pytest.yield_fixture` decorator ([ref](https://docs.pytest.org/en/latest/yieldfixture.html))

```python
import pytest

@pytest.yield_fixture  # <- Using a deprecated @pytest.yield_fixture decorator
def yield_fixture():
    yield
```

### W6402 `useless-pytest-mark-decorator`

Raise when using every `@pytest.mark.*` for the fixture ([ref](https://docs.pytest.org/en/stable/reference.html#marks))

```python
import pytest

@pytest.fixture
def awesome_fixture():
    ...

@pytest.fixture
@pytest.mark.usefixtures("awesome_fixture")  # <- Using useless `@pytest.mark.*` decorator for fixtures
def another_awesome_fixture():
    ...
```

### W6403 `deprecated-positional-argument-for-pytest-fixture`

Raise when using deprecated positional arguments for fixture decorator ([ref](https://docs.pytest.org/en/stable/deprecations.html#pytest-fixture-arguments-are-keyword-only))

```python
import pytest

@pytest.fixture("module")  # <- Using a deprecated positional arguments for fixture
def awesome_fixture():
    ...
```

### F6401 `cannot-enumerate-pytest-fixtures`

Raise when the plugin cannot enumerate and collect pytest fixtures for analysis

NOTE: this warning is only added to test modules (`test_*.py` / `*_test.py`)

```python
import no_such_package  # <- pylint-pytest plugin cannot enumerate and collect pytest fixtures
```

## Changelog

See [CHANGELOG](CHANGELOG.md).

## License

`pylint-pytest` is available under [MIT license](LICENSE).
