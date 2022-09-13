# Part 4: Enable application specific monitoring (custom metrics)

### 4A: Configure monitoring for your Radix application

Tell Prometheus to start collecting metrics for your app component, by setting the monitoring flag in `radixconfig.yaml`. Make sure you get the indentation in the yaml-file right.

```yaml
components:
  - name: app
    environmentConfig:
      - environment: single
        monitoring: true
```

## 4B: Collect and serve Prometheus metrics in `server.py`

### 1: Collect metrics 
Collect metrics for requests to the api (paths starting with `/api`). We choose to create a custom `route` label, containing
the matched url, for aggregation and visualization in Grafana.

Declare the `request_counter` variable. Place this code after the `app_name` variable.
```python
prefix = app_name.replace("-", "_")
request_counter = Counter(f'{prefix}_request_counter', 'Requests to inventory API', ['method', 'route', 'status'])
```

Add `after_request_fn()` method in `server.py` before all route definitions:

```python
@app.after_request
def after_request_fn(response):
    if request.path.startswith("/api"):
        request_counter.labels(method=request.method, route=request.url_rule.rule, status=response.status_code).inc()
    return response
```


### 2: Create a `/metrics` endpoint
Share collected metrics through the `/metrics` endpoint, by adding:

```python
@app.route('/metrics')
def send_metrics():
    response = make_response(generate_latest())
    response.headers['content-type'] = 'text/plain'
    return response
```

### 3: Don't forget the imports:

```python
from prometheus_client import Counter, generate_latest
```

### 4: Verify metrics endpoint

* Run application locally
* Generate a few requests to the local API
* Access the `/metrics` endpoint
* Verify that the exported metric is available (at the randomly generated application name)

### 5: Commit and push to branch `radix` to trigger build & deploy

### 6: Verify that the Radix Pipeline job completes successfully


Back to slides: https://oyron.github.io/monitoring-docs/#/6/2