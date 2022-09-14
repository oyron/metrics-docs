<!-- .slide: data-background-image="https://eds-static.equinor.com/logo/equinor-logo-primary.svg#white" data-background-size="12%" data-background-position="right 2% top 2%"-->

<style type="text/css">
  .reveal pre {
    width: 100%
  }

  .reveal * {
    text-align: left;
  }
  .reveal ul {
    display: block;
  }
  .reveal ol {
    display: block;
  }
</style>
<br>

#### EDC 2022
# Metrics and Monitoring in Omnia Radix
### 4 hour workshop 🪚🔨
<br><br>
Slack: #edc22-workshop-monitoring

---

## About me

- Øyvind Rønne
- Leading Advisor 
  - Software Engineering - Architecture and Platform <!-- .element: style="font-size:0.8em"-->
- Radix Product Owner

---
## Agenda

- 🧑‍💻 Set up working environment     <!-- .element: style="font-size:0.8em"-->
- Monitoring concepts               <!-- .element: style="font-size:0.8em"-->
- 🧑‍💻 Sample application             <!-- .element: style="font-size:0.8em"-->
- Prometheus and Grafana            <!-- .element: style="font-size:0.8em"-->
- 🧑‍💻 Build Grafana dashboard        <!-- .element: style="font-size:0.8em"-->
- 🧑‍💻 Stressing the sample app       <!-- .element: style="font-size:0.8em"-->
- Alerting                          <!-- .element: style="font-size:0.8em"-->

---
## Disclaimer 🤷‍♂️

- One off workshop 🚀
- I'm not an expert..

---

## The main goals

- learn a little bit about monitoring, Prometheus, Grafana, and Radix
- have some fun 🥳

-----

# 🧑‍💻1: Set up development and Radix environment

---
## Main parts ⚙️

* Equinor-Playground GitHub organization
* Omnia Radix Playground
* GitHub Codespaces
* Sample application written in Python

---
## Sample Application: Inventory API 
* Simple REST API
* Inventory of books
* Python Flask

---
## Main steps:
1. Fork GitHub repo to Equinor-Playground GitHub organization
2. Run GitHub Codespace from the fork
3. Adapt the sample application
4. Register the application in Omnia Radix Playground
5. Trigger a build-deploy job to get the app running
---
## Let's get started!<br/>

https://github.com/oyron/monitoring-docs/blob/main/1-prepare-environment.md

-----

<style type="text/css">
  .section  {
    font-size: 2em;
    color: red
  }
</style>
# Monitoring concepts

Sources: 
* https://radix.equinor.com/docs/topic-monitoring
* https://www.digitalocean.com/community/tutorials/an-introduction-to-metrics-monitoring-and-alerting

<!-- .element: style="font-size:0.6em"-->

---

## Definitions

<span style="color:var(--moss-green)">**Metrics:**</span> Measurements of the state of something. E.g. CPU usage, number of HTTP requests. Typically collected as time-series, with measurements at regular intervals. 
<!-- .element: style="font-size:0.8em"-->
<span style="color:var(--moss-green)">**Monitoring:**</span> The process of collecting, aggregating, and analyzing metrics
<!-- .element: style="font-size:0.8em"-->
<span style="color:var(--moss-green)">**Alerting:**</span> Perform automated actions based on changes in metrics values
<!-- .element: style="font-size:0.8em"-->

Note:
Alerting: the responsive component of a monitoring system

---

## Purpose

* Make it easier to locate and fix problems in event of an outage
* Identify trends that could lead to an outage and fix them proactively
* Better understand resource usage, which makes it possible to scale resources more appropriately and avoid resource waste

---
## Proactive/Reactive

#### *MTBF - Mean Time Between Failures*
Predicted time between a system being non-operational. MTBF can be increase by being <span style="color:var(--moss-green)">*pro-active*</span>. E.g. by setting thresholds for metrics when something is out of balance, but before users affected. 
<br><br>
#### *MTTR - Mean Time To Recovery* 
Time to get a non-operational system back to normal. This is the <span style="color:var(--moss-green)">reactive</span> phase. MTTR can be decreased by understanding behavior of working system. 

Note:
* If we have detailed metrics going back in time we can correlate and see for example that CPU usage spiked to 100% just before the service started behaving weird.
* For example memory usage is increasing much faster than usual. Or memory usage is above X%.

---
## Methodologies

Note:
Methodologies for analyzing the performance of a systems

---

## RED Method

Based on [The Four Golden Signals](https://sre.google/sre-book/monitoring-distributed-systems/#xref_monitoring_golden-signals) from Google.

RED focuses on the request/transactions/operations that a system/component executes.

* **R**ate - Requests per second
* **E**rrors - Failed requests per second
* **D**uration - The time it takes for a request to be completed

Note:
* RED gives an overview of how your architecture is behaving 
* Well suited for micro services and REST APIs.
* Good indicator of how happy your customers is

---

## USE Method

* Utilization - the average time that the resource was busy servicing work
* Saturation - the degree to which the resource has extra work which it can't service, often queued
* Errors - the count of error events

USE is better suited for hardware

-----

# 🧑‍💻2: Exploring the sample application 

---

## Verify Radix deployment
* Verify that the Radix pipeline job has completed successfully
* Verify that you can access the application web site in Radix

---

## Explore Radix console

* Explore the Radix console for you app
* Mark your application as a favorite in the Radix Playground main console

---

## Review and test API

* Open the Radix application log and API test client in separate browser tabs
* Try calling the different API endpoints from the test client
* Review the response body and status codes
* Review other possible response codes in the API test client

---

##  Inspect source code

* `radixconfig.yaml`
* `Dockerfile`
* `/app`
  * `server.py`: Main application
  * `validator.py`: Simple input validation
  * `model.py`: BookInventory class
  * `tests/test_book_inventory.py`: Test of BookInventory
  * `static/index.html`: Web page
  * `templates/openapi.yaml`: OpenAPI spec

-----

# 🧑‍💻3: Start building Grafana dashboard

* Create copy of Radix default dashboard
* Remove panels not needed in the workshop
* Select defaults

---

# 🧾 
https://github.com/oyron/monitoring-docs/blob/main/3-create-dashboard.md

-----

# Prometheus and Grafana

---

## Prometheus

* Open-source monitoring and alerting toolkit
* Multi-dimensional time series data model
* PromQL: query language for the Prometheus data model
* Metrics collection via pull over HTTP

<br>

More information: https://prometheus.io/docs/introduction/overview/
<!-- .element: style="font-size:0.8em"-->

---

<!-- .slide: data-background-image="content/images/Grafana_dashboard.png" data-background-size="30%" data-background-position="right 2% top 2%"-->

## Grafana

* Open source analytics and interactive visualization application
* Provides charts, graphs and alerts

<br>

More information: https://grafana.com/
<!-- .element: style="font-size:0.8em"-->


---

## Prometheus metric types

- [**Counter**](https://prometheus.io/docs/concepts/metric_types/#counter) a cumulative metric that represents a single monotonically increasing counter whose value can only increase or be reset to zero on restart 
- [**Gauge**](https://prometheus.io/docs/concepts/metric_types/#gauge) is a metric that represents a single numerical value that can arbitrarily go up and down 
- [**Histogram**](https://prometheus.io/docs/concepts/metric_types/#histogram) samples observations (usually things like request durations or response sizes) and counts them in configurable buckets. It also provides a sum of all observed values.

<!-- .element: style="font-size:0.8em"-->

<br>

More information:<br><!-- .element: style="font-size:0.8em"-->
[Prometheus Data Model](https://prometheus.io/docs/concepts/data_model/)<br><!-- .element: style="font-size:0.8em"-->
[The Anatomy of a PromQL Query](https://promlabs.com/blog/2020/06/18/the-anatomy-of-a-promql-query)<!-- .element: style="font-size:0.8em"-->

---

## Radix monitoring infrastructure

![Radix monitoring infrastructure](content/images/radix-monitoring-infrastructure.png)

-----

# 🧑‍💻4: Enable application specific monitoring
---
* Configure Prometheus in Radix to start collecting metrics from your app
* Create a Prometheus [counter](https://prometheus.io/docs/concepts/metric_types/#counter) metric
* Count all requests to the API
* Serve the collected metric at the `/metrics` endpoint

---

# 🧾 
https://github.com/oyron/monitoring-docs/blob/main/4-enable-monitoring.md

-----

# 🧑‍💻5: Add application metric to dashboard

---

* Use Grafana metric explorer to find the exported metric
* Build PromQL queries showing number of requests pr endpoint and number of errors pr minute
* Make dashboard panels show these queries
* Generate traffic to the API to test the dashboard panels

Note:
Edit existing ingress request panels

---

# 🧾 
https://github.com/oyron/monitoring-docs/blob/main/5-add-metrics.md

---

## Queries

### \# 2
```
rate(oyron_inventory_request_counter_total[1m])
```
### \# 4
```
rate(oyron_inventory_request_counter_total{status=~"[45].."}[1m])
```
### \# 7
```
sum by (method, route) (rate(oyron_inventory_request_counter_total[1m]))
```
```
sum by (status) (rate(oyron_inventory_request_counter_total{status=~"[45].."}[1m]))
```

-----

# 🧑‍💻6: Additional metric types 

---

* Export Prometheus [histogram](https://prometheus.io/docs/concepts/metric_types/#histogram) metric from app, measuring request duration
* Export Prometheus [gauge](https://prometheus.io/docs/concepts/metric_types/#gauge) metric, measuring size of inventory (application variable)
* Create new dashboard panels showing these metrics
* Create a bar chart showing total number of requests pr minute
* Generate traffic to test the dashboard panels

---

# 🧾 
https://github.com/oyron/monitoring-docs/blob/main/6-additional-metrics.md

---

## Queries

### \# 2
```
rate(oyron_inventory_request_duration_sum[1m]) /
rate(oyron_inventory_request_duration_count[1m])
```

-----

# 🧑‍💻7: Stressing the application 

---

### Generate "heavy" traffic to the API
The `generate_post_requests.py` script generates relatively large post requests to the API. This will impact the CPU
and memory usage, and response times for some of the endpoints. 

We will monitor the performance of the application using the dashboard.

---

* Check out branch `main`
* Edit the `scripts/generate_post_requests.py`: update the `SERVER_URL` parameter with the url to your application
* Edit the `scripts/generate_traffic.py`: update the `SERVER_URL` parameter with the url to your application

---

* Split the embedded terminal (+ => Split Terminal => bash)
* In one terminal keep `generate_traffic.py` running
* In the other terminal, run `generate_post_requests.py` with a parameter stating the number of post requests to be generated. 10 is a good starting point. 
* Continue generating requests until you see the application struggle with performance.
* Watch the request durations change as the inventory is increasing in size

-----

# Alerting

---

* Radix alerts
* Prometheus alerting

---

## Radix alerts

* Environment alerts
  * Component or job unable to start
  * Component or job crashes or runs out of memory
* Pipeline job alerts

<br>
<br>

More info: https://radix.equinor.com/guides/alerting/#configure-alerting-for-components-and-jobs-in-an-environment

---

## Prometheus alerting

-----

# That's it 🥳

