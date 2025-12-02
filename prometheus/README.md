# Prometheus

## Installations
```
sudo apt update
sudo apt install prometheus -y

```


## Add your prometheus config

#vi /etc/default/prometheus
```
ARGS="--web.external-url=\"http://localhost/prometheus/\" \     <--- setting external url you can change this based on the domain you wish to use
--web.route-prefix=\"/prometheus/\" \                           <--- setting prometheus to listen on /prometheus instead of default /
--web.listen-address=:9090 \                                    <--- setting port number to listen 9090 is this default
--storage.tsdb.retention.time=3d                                <--- adding storage retention to 3 days
--web.config.file=/etc/prometheus/web.yml"                      <--- this is for adding UI config like basic user authentication
```

## Change the configs

#vi /etc/prometheus/prometheus.yml
```
scrape_configs:
  - job_name: 'prometheus'

    scrape_interval: 5s
    scrape_timeout: 5s

    metrics_path:  '/prometheus/metrics'                <--- this updated cause we are exposing prometheus in /prometheus
    static_configs:
      - targets: ['localhost:9090']

  - job_name: node                                      <--- this is node exporter config
    static_configs:
      - targets: ['localhost:9100']
  - job_name: webapp1                                   <--- this is a custom scrape config
    scheme: https                                       <--- setting the protocol to https instead of http
    static_configs:
      - targets: ['yourdomain']                         <--- add just the domain or the ip
        labels:                                         <--- you can add whatever labels you want
          environment: "my-site"
          instance: "instance1"
          job: "appian-webapp"
    tls_config:                                         <--- this is to trust the provided ssl cert and skip the verification.you can configure ssl cert and ca cert for verification too
      insecure_skip_verify: true
```

## Add web.yml to add basic auth to prometheus

Check here for more: https://prometheus.io/docs/guides/basic-auth

#vi /etc/prometheus/web/yml
```
basic_auth_users:
    admin: $2a$12$SLtnC83VapvEeL1g/j.3i.hzDF9kfMDS9npulSTIX15gmSJLEJbaa             <--- this is a bcrpt hash of a password
```
