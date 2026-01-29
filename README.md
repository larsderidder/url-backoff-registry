# url-backoff-registry

In-memory URL backoff registry with sliding window thresholds.

Track failing endpoints and back off when failures exceed a threshold within a time window. Useful for avoiding repeated requests to flaky or overloaded services.

## Install

```bash
pip install url-backoff-registry
```

## Usage

```python
from url_backoff_registry import BackoffRegistry

# Back off for 120s after 3 failures within 30s
registry = BackoffRegistry(window_seconds=30, threshold=3, backoff_seconds=120)

def fetch(url):
    if registry.should_backoff(url):
        raise Exception(f"Backing off from {url}")

    try:
        response = requests.get(url)
        response.raise_for_status()
        return response
    except Exception:
        registry.record_failure(url)
        raise

# Check when backoff ends
if registry.should_backoff(url):
    retry_at = registry.next_retry_at(url)
    print(f"Retry after {retry_at}")

# Clear backoff manually (e.g., after a successful request)
registry.clear(url)
```

## API

### `BackoffRegistry`

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `window_seconds` | int | 30 | Time window for counting failures |
| `threshold` | int | 3 | Number of failures to trigger backoff |
| `backoff_seconds` | int | 120 | How long to back off |
| `clock` | callable | `datetime.utcnow` | Clock function (for testing) |

### Methods

- `record_failure(key)` - Record a failure for the given key
- `should_backoff(key)` - Returns `True` if currently in backoff
- `next_retry_at(key)` - Returns datetime when backoff ends, or `None`
- `clear(key)` - Clear backoff and failure history for the key

## Development

```bash
git clone https://github.com/larsderidder/url-backoff-registry.git
cd url-backoff-registry
python -m venv .venv
. .venv/bin/activate
pip install -e ".[dev]"
pytest
```

## License

MIT
