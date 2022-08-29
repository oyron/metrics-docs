# Prometheus & Grafana

1. Share -> export workspace
2. Import workspace
3. Delete unneccessary panels
4. Create panels

### Requests pr second [1m]
```
sum by (method, route)(rate(inventory_request_counter_total[1m]))
```

### Errors pr second [1m]
```
rate(inventory_request_counter_total{status=~"5.."}[1m])
```

### Average response time
```
rate(inventory_request_latency_sum[1m])
/
rate(inventory_request_latency_count[1m])
```


### Requests pr minute
```
sum(label_replace(increase(inventory_request_counter_total[1m]), "status", "2**", "status", "(2..)")) by (status)
```