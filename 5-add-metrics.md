# Part 5: Add application metrics to dashboard

### 1: Explore exported metric

* Open https://grafana.playground.radix.equinor.com
* Select "Explore" in the Grafana left menu
* In the Metrics browser, enter the name of the exported metric
* Select the metric ending in "_total"

### 2: Build a query showing the number of requests pr second 

In the metrics explorer, build a query showing the number of requests pr second, averaged over a minute.

Hint: prometheus [`rate()`](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) function

### 3: Make a panel show endpoint requests pr second

* In your dashboard, edit the "Namespace ingress requests pr min" panel
* Change the metric to the query developed in the previous stage
* Name the panel "Endpoint requests pr second [1m]"
* Change the legend to: `{{method}} {{route}} {{status}}`
* Generate a few requests from the API test client. Verify that they are displayed in the panel

### 4: Build a query showing the number of errors pr second

Hint: Prometheus [Instant vector selector](https://prometheus.io/docs/prometheus/latest/querying/basics/#instant-vector-selectors)

### 5: Make a panel show errors pr second

* In your dashboard, edit the "Average Ingress requests duration (sec)" panel
* Change the metric to the query developed in the previous stage
* Name the panel "Errors pr second [1m]"
* Change the legend to: `{{method}} {{route}} {{status}}`
* Generate a few requests from the API test client. Verify that they are displayed in the panel

### 6: Generate traffic to the API

* Retrieve the script `scripts/generate_traffic.py` from the main branch: `git checkout main -- scripts/generate_traffic.py`
* Edit the `scripts/generate_traffic.py`: update the `SERVER_URL` parameter with the url to your application
* Run the script: `python scripts/generate_traffic.py`

### 7: Aggregate requests and errors pr second

* Edit the "Requests pr second [1m]" panel
* Use `sum by()()` to aggregate on method and path

* Edit the "Errors pr second [1m]" panel
* Use `sum by()()` to aggregate on status

Back to slides: https://oyron.github.io/monitoring-docs/#/7/2