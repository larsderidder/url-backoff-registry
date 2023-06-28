# url-backoff-utils

Repository: https://github.com/larsderidder/url-backoff-utils
In-memory URL backoff registry with sliding window thresholds.

## Install

```bash
pip install url-backoff-utils
```

## Usage

```python
from url_backoff_utils import BackoffRegistry

registry = BackoffRegistry(window_seconds=30, threshold=3, backoff_seconds=120)

registry.record_failure("https://example.com")
if registry.should_backoff("https://example.com"):
    print("skip")
```

## Development

```bash
python -m venv .venv
. .venv/bin/activate
python -m pip install -U pip
python -m pip install -e ".[dev]"
pytest
```
