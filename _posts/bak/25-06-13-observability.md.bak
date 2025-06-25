---
title: Observability
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Building observability is easier than I thought

## 1 Prometheus
1. Use Prometheus to scrape metrics. Here is an example of scraping for LLM endpoint
```sh
wget https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
tar -xvzf prometheus-2.52.0.linux-amd64.tar.gz
cd prometheus-2.52.0.linux-amd64/
```
2. Modify the config file `vi prometheus.yml`
```yaml
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"
    metrics_path: '/v1/metrics'
    # scheme defaults to 'http'.
    static_configs: 
      targets: ["localhost:8000"]
```
      
3. Start the service by `./prometheus --config.file=./prometheus.yml` and it will be available at `http://localhost:9090/targets?search=`

## 2 Grafana
1. Download the install
```sh
wget https://dl.grafana.com/oss/release/grafana-11.0.0.linux-amd64.tar.gz
tar -zxvf grafana-11.0.0.linux-amd64.tar.gz
```
2. Start the service and change password for the first time.
```sh  
cd grafana-v11.0.0/
./bin/grafana-server
#username: admin 
#password: admin
```
3. Click on the “Data Source” button, select **Prometheus** and specify the Prometheus URL `localhost:9090`
4. Add dashboard by import a JSON [file](https://docs.nvidia.com/nim/large-language-models/latest/_downloads/66e67782ce543dcccec574b1483f0ea0/nim-dashboard-example.json)