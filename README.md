# angles-python-client

A small Python client for the **Angles Dashboard** REST API, designed to be a **like-for-like** port of
`angles-javascript-client`.

## Install (local / editable)

```bash
pip install -e .
```

## Quick usage (singleton reporter)

```python
from angles_python_client import angles_reporter
from angles_python_client.models import Artifact, ScreenshotPlatform, Platform

angles_reporter.set_base_url("http://127.0.0.1:3000/rest/api/v1.0/")

build = angles_reporter.start_build(
    name="TestRunName",
    team="Team",
    environment="Environment",
    component="Component",
    phase="optional-phase",
)

angles_reporter.add_artifacts([
    Artifact(groupId="angles-ui", artifactId="anglesHQ", version="1.0.0")
])

angles_reporter.start_test(title="test1", suite="suite1")
angles_reporter.add_action("My first action")

platform = ScreenshotPlatform(
    platformName="Android",
    platformVersion="10",
    browserName="Chrome",
    browserVersion="89.0",
    deviceName="Samsung Galaxy S9",
)

screenshot = angles_reporter.save_screenshot_with_platform(
    file_path="/path/to/screenshot.png",
    view="view_1",
    tags=["smoke", "home"],
    platform=platform,
)

angles_reporter.info_with_screenshot("Checking my view on android", screenshot.get("_id"))

angles_reporter.pass_step("Assertion", expected="true", actual="true", info="Just doing an assertion")
angles_reporter.fail_step("Assertion", expected="true", actual="false", info="Just doing an assertion")

execution = angles_reporter.save_test()
```

## Direct requests usage

```python
from angles_python_client import AnglesHttpClient
from angles_python_client.requests import BuildRequests

http = AnglesHttpClient(base_url="http://127.0.0.1:3000/rest/api/v1.0/")
builds = BuildRequests(http)
build = builds.get_build("your-build-id")
```

## Notes

* Methods are synchronous (raise exceptions on non-2xx), mirroring the JS client’s Promise rejection behavior.
* The API payload shape follows the JS client and Angles API conventions.


## Publishing (GitHub + PyPI)

This repo includes GitHub Actions workflows to publish the package automatically using **Trusted Publishing**.

### One-time setup

1. Create the GitHub repository and push this code.
2. Create the project on **PyPI** (and optionally **TestPyPI**) using the same distribution name as
   `project.name` in `pyproject.toml`. If the name is already taken on PyPI, change `project.name`.
3. In PyPI project settings, add a **Trusted Publisher** for GitHub Actions pointing at your repo.
   This allows publishing without storing a long-lived API token.

### Publish a release to PyPI

1. Bump `project.version` in `pyproject.toml`
2. Push a tag like `v1.0.1`:

   ```bash
   git tag v1.0.1
   git push origin v1.0.1
   ```

The workflow `.github/workflows/publish.yml` will build and publish to PyPI.

### Publish to TestPyPI first (optional)

- Push a tag like `test-v1.0.1`, or run the `Publish to TestPyPI` workflow manually.
- Install from TestPyPI to validate:

  ```bash
  python -m pip install -i https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple angles-python-client
  ```

### Build locally (manual)

```bash
python -m pip install -U pip build twine
python -m build
python -m twine check dist/*
python -m twine upload dist/*
```
