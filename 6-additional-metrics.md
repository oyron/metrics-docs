# Part 6: Add Gauge and Histogram metrics

## 6A: Export additional metrics from app

Continue working on the `radix` branch.

### 1: Declare Gauge and Histogram collectors

```python
from timeit import default_timer
from prometheus_client import Gauge, Histogram
```

```python
request_duration = Histogram(f'{prefix}_request_duration', 'Request duration[ms]', ['method', 'route'])
gauge = Gauge(f'{prefix}_size', 'Number of books in inventory')
```

### 2: Collect metrics
To calculate request time, we utilize the ability to store variables in the request global object.
We store a timer object in the `request` object before each request is processed.

```python
@app.before_request
def before_request_fn():
    request.prom_start_time = default_timer()
```

Then we calculate the request duration and update the request_duration Histogram object in the `after_request_fn()`.
We also update the book inventory Gauge after each request.

```python
@app.after_request
def after_request_fn(response):
    if request.path != "/metrics":
        route = f'{request.url_rule.rule}?<param>=' if len(request.args) > 0 else request.url_rule.rule
        request_counter.labels(method=request.method, route=route, status=response.status_code).inc()
        elapsed_time = default_timer() - request.prom_start_time
        request_duration.labels(method=request.method, route=route).observe(elapsed_time)
        gauge.set(book_inventory.count())
    return response
```

### Verify the new metrics endpoint

* Run application locally
* Generate a few requests to the local API
* Access the `/metrics` endpoint
* Verify that the additional metrics are available (at the randomly generated application name)

### Commit and verify

Commit the changes and push the `radix` branch. Verify that the Radix Pipeline job completes successfully.


## 6B: Add additional panels in the dashboard

### 1: Build a query showing average response time 

In the metrics explorer in Grafana, build a query showing the response time (aka request duration), averaged over a minute.

Hint: Prometheus [Histogram documentation](https://prometheus.io/docs/practices/histograms)

### 2: Create a panel showing request duration
This will utilize the Histogram metric exported in the previous part

* In your dashboard, press the "Add panel" button in the top menu, then select "Add a new panel"
* In the upper right corner, make sure visualization type "Time series" is selected
* Enter the query developed in the previous stage
* Name the panel "Request duration [1m]"
* Change the legend to: `{{method}} {{route}}`
* Select legend placement: "Right"
* Save panel
* Save dashboard

### 3: Create a panel showing the inventory size
In this we will utilize the Gauge exported in the previous part

* In your dashboard, press the "Add panel" button in the top menu, then select "Add a new panel"
* In the upper right corner, select visualization type "Gauge"
* Enter the gauge metric name
* Delete the default threshold value 80 (located in the bottom of right hand side Grafana panel)
* Name the panel "Inventory size"
* Save panel
* Save dashboard

### 4: Make a bar chart showing total requests pr minute

* In your dashboard, press the "Add panel" button in the top menu, then select "Add a new panel"
* In the upper right corner, make sure visualization type "Time series" is selected
* Enter the following query: `sum(label_replace(increase(<prefix>_inventory_request_counter_total[1m]), "status", "2**", "status", "(2..)")) by (status)`. This query will aggregate all 200-299 status responses to a single 2** response.
* Select fill opacity: 100
* Select Stacking: Normal
* Change the legend to: `{{status}}`
* Name the panel "Requests pr minute"
* Save panel
* Align the newly created panels
* Save dashboard

### 5: Test the new panels 

Run the `generate_traffic.py` script and verify that all the panels are displaying data

Back to slides: https://oyron.github.io/metrics-docs/#/8/2