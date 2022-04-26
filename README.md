# prometheus-nodejs

## Goal 
Setup monitoring with Prometheus and Grafana on localhost.

## Requirements
Nodejs, Docker.

## Demo architecture
![Architecture  Demo](https://user-images.githubusercontent.com/62415557/165236947-11de6649-30d1-4324-b219-996ac800130c.png)

## Steps
1. Run Nodejs server: `npm install` and `npm run start`. Open http://localhost:3001. Show metrics with http://localhost:3001/metrics.
2. Install [Prometheus](https://prometheus.io/download/) and update file config prometheus.yml and file rule.yaml (copy from /config).

    Run `./prometheus --config.file=prometheus.yml` from folder you installed. Open Prometheus: http://localhost:9090.
3. Visit your running Prometheus and run [queries](https://prometheus.io/docs/prometheus/latest/querying/basics/).

    **Request Per Minute**
    
    `sum(rate(http_request_duration_ms_count[1m])) by (service, route, method, code)  * 60`
    
    **Error rate**
    
    `sum(increase(http_request_duration_ms_count{code=~"^5..$"}[1m])) /  sum(increase(http_request_duration_ms_count[1m]))`
    
    **Median Response Time**
    
    `histogram_quantile(0.5, sum(rate(http_request_duration_ms_bucket[1m])) by (le, service, route, method))`

    **Average Memory Usage**
    
    `avg(nodejs_external_memory_bytes / 1024 / 1024) by (service)`

4. Install Prometheus [Alertmanager](https://prometheus.io/download/) and update file alertmanager.yml.

    Run `./alertmanager --config.file=alertmanager.yml` and open http://localhost:9093.
    
    Spam requests to test alert: `curl http://localhost:3001/?[1-100]`.
    
5. Run Grafana by Docker

    `docker run -i -p 3000:3000 grafana/grafana`. Open http://localhost:3000.
    
    ```
    Username: admin
    Password: admin
    ```
    
    **Setting datasource**
    
    Create a Grafana datasource with this settings:
    
    - name: DS_PROMETHEUS

    - type: prometheus

    - url: http://localhost:9090

    - access: browser

    **Import dashboard
    
    - Grafana Dashboard to import: /grafana-dashboard.json.
    
    
