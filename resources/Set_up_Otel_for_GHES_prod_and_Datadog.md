2025-12-16

docs: https://docs.github.com/en/enterprise-server@3.19/admin/monitoring-and-managing-your-instance/monitoring-your-instance/opentelemetry-metrics/setting-up-external-monitoring-with-opentelemetry#datadog

1. SSH into your GitHub Enterprise Server instance and run the following command.

    ```shell
    ghe-config observability.metrics.custom-config-enabled true
    ```

2. Create your custom OpenTelemetry configuration file at `/data/user/common/otelcol.yaml`:

    ```shell
    sudo vim /data/user/common/otelcol.yaml
    ```

3. Add your custom configuration (see [Example configurations for popular monitoring systems](https://docs.github.com/en/enterprise-server@3.19/admin/monitoring-and-managing-your-instance/monitoring-your-instance/opentelemetry-metrics/setting-up-external-monitoring-with-opentelemetry#example-configurations-for-popular-monitoring-systems)).

4. Apply the configuration:

    ```shell
    ghe-config-apply
    ```

# Datadog config

- [x] find the API key in Keeper ✅ 2025-12-16

```yaml
---
exporters:
  datadog:
    api:
      site: us3.datadoghq.com
      key: obsfucated
    host_metadata:
      enabled: true

service:
  pipelines:
    metrics:
      receivers: [otlp/ghes, prometheus/ghes]
      processors: [batch/ghes]
      exporters: [datadog]
```


# Hmm... not sure if this thing is really on

I might need to do this tomorrow morning:
```bash
ghe-config observability.metrics.advanced-dashboards-enabled true
ghe-config-apply
```


# viewing local logs

```bash
sudo journalctl -u otelcol-contrib -f #view otel collector logs
```

```bash
sudo journalctl -u victoriametrics -f #view VictoriaMetrics logs
```


BUT... I'm pretty sure I've configured all I really need to configure... I ought to be able to see *something* in Datadog... the fact that the local logs all seem to end with lines that imply the service has been stopped is... worrisome.

```bash
sudo journalctl -u otelcol-contrib -f
-- Logs begin at Tue 2025-12-16 07:51:16 UTC. --
Dec 16 12:36:44 localhost otelcol-contrib[975]: 2025-12-16T12:36:44.902Z        info        targetallocator/manager.go:210        Scrape job added        {"resource": {"service.instance.id": "21b5fe62-2913-4afe-a3b8-fd64a94f9508", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "prometheus/ghes", "otelcol.component.kind": "receiver", "otelcol.signal": "metrics", "jobName": "process_exporter"}
Dec 16 12:36:44 localhost otelcol-contrib[975]: 2025-12-16T12:36:44.906Z        info        service@v0.135.0/service.go:234        Everything is ready. Begin running and processing data.        {"resource": {"service.instance.id": "21b5fe62-2913-4afe-a3b8-fd64a94f9508", "service.name": "otelcol-contrib", "service.version": "0.135.0"}}
Dec 16 12:36:44 localhost otelcol-contrib[975]: 2025-12-16T12:36:44.906Z        info        prometheusreceiver@v0.135.0/metrics_receiver.go:216        Starting scrape manager        {"resource": {"service.instance.id": "21b5fe62-2913-4afe-a3b8-fd64a94f9508", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "prometheus/ghes", "otelcol.component.kind": "receiver", "otelcol.signal": "metrics"}
Dec 16 12:38:50 git-ucsf-edu systemd[1]: Stopping OpenTelemetry Collector Contrib...
Dec 16 12:38:50 git-ucsf-edu otelcol-contrib[975]: 2025-12-16T12:38:50.188Z        info        otelcol@v0.135.0/collector.go:358        Received signal from OS        {"resource": {"service.instance.id": "21b5fe62-2913-4afe-a3b8-fd64a94f9508", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "signal": "terminated"}
Dec 16 12:38:50 git-ucsf-edu otelcol-contrib[975]: 2025-12-16T12:38:50.190Z        info        service@v0.135.0/service.go:248        Starting shutdown...        {"resource": {"service.instance.id": "21b5fe62-2913-4afe-a3b8-fd64a94f9508", "service.name": "otelcol-contrib", "service.version": "0.135.0"}}
Dec 16 12:38:50 git-ucsf-edu otelcol-contrib[975]: 2025-12-16T12:38:50.190Z        info        extensions/extensions.go:69        Stopping extensions...        {"resource": {"service.instance.id": "21b5fe62-2913-4afe-a3b8-fd64a94f9508", "service.name": "otelcol-contrib", "service.version": "0.135.0"}}
Dec 16 12:38:50 git-ucsf-edu otelcol-contrib[975]: 2025-12-16T12:38:50.190Z        info        service@v0.135.0/service.go:262        Shutdown complete.        {"resource": {"service.instance.id": "21b5fe62-2913-4afe-a3b8-fd64a94f9508", "service.name": "otelcol-contrib", "service.version": "0.135.0"}}
Dec 16 12:38:50 git-ucsf-edu systemd[1]: otelcol-contrib.service: Succeeded.
Dec 16 12:38:50 git-ucsf-edu systemd[1]: Stopped OpenTelemetry Collector Contrib.
```

yup, it's dead, let's kick it:

```bash
systemctl list-units --type=service --all | grep -i otel
  otelcol-contrib.service                                 loaded    inactive   dead          OpenTelemetry Collector Contrib
```

looks like it's trying to access the prometheus endpoint, which we haven't yet set up
```bash
sudo journalctl -u otelcol-contrib -f
otelcol.component.kind": "exporter", "otelcol.signal": "metrics", "error": "no more retries left: failed to make an HTTP request: Post \"http://localhost:8015/opentelemetry/v1/metrics\": dial tcp [::1]:8015: connect: connection refused", "dropped_items": 20}
Dec 16 15:50:58 git-ucsf-edu otelcol-contrib[1567967]: go.opentelemetry.io/collector/exporter/exporterhelper/internal.NewQueueSender.func1
Dec 16 15:50:58 git-ucsf-edu otelcol-contrib[1567967]:         go.opentelemetry.io/collector/exporter/exporterhelper@v0.135.0/internal/queue_sender.go:42
Dec 16 15:50:58 git-ucsf-edu otelcol-contrib[1567967]: go.opentelemetry.io/collector/exporter/exporterhelper/internal/queuebatch.(*disabledBatcher[...]).Consume
Dec 16 15:50:58 git-ucsf-edu otelcol-contrib[1567967]:         go.opentelemetry.io/collector/exporter/exporterhelper@v0.135.0/internal/queuebatch/disabled_batcher.go:23
Dec 16 15:50:58 git-ucsf-edu otelcol-contrib[1567967]: go.opentelemetry.io/collector/exporter/exporterhelper/internal/queue.(*asyncQueue[...]).Start.func1
Dec 16 15:50:58 git-ucsf-edu otelcol-contrib[1567967]:         go.opentelemetry.io/collector/exporter/exporterhelper@v0.135.0/internal/queue/async_queue.go:49
Dec 16 15:50:58 git-ucsf-edu otelcol-contrib[1567967]: 2025-12-16T15:50:58.655Z        info        internal/retry_sender.go:133        Exporting failed. Will retry the request after interval.        {"resource": {"service.instance.id": "f97dd064-4817-4015-8d04-e6d7ae39737f", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "otlphttp/internal", "otelcol.component.kind": "exporter", "otelcol.signal": "metrics", "error": "failed to make an HTTP request: Post \"http://localhost:8015/opentelemetry/v1/metrics\": dial tcp [::1]:8015: connect: connection refused", "interval": "4.209622863s"}
Dec 16 15:50:59 git-ucsf-edu otelcol-contrib[1567967]: 2025-12-16T15:50:59.041Z        info        internal/retry_sender.go:133        Exporting failed. Will retry the request after interval.        {"resource": {"service.instance.id": "f97dd064-4817-4015-8d04-e6d7ae39737f", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "otlphttp/internal", "otelcol.component.kind": "exporter", "otelcol.signal": "metrics", "error": "failed to make an HTTP request: Post \"http://localhost:8015/opentelemetry/v1/metrics\": dial tcp [::1]:8015: connect: connection refused", "interval": "44.839622213s"}
Dec 16 15:50:59 git-ucsf-edu otelcol-contrib[1567967]: 2025-12-16T15:50:59.186Z        warn        internal/transaction.go:151        Failed to scrape Prometheus endpoint        {"resource": {"service.instance.id": "f97dd064-4817-4015-8d04-e6d7ae39737f", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "prometheus/ghes", "otelcol.component.kind": "receiver", "otelcol.signal": "metrics", "scrape_timestamp": 1765900259185, "target_labels": "{__name__=\"up\", instance=\"git.ucsf.edu\", job=\"haproxy_exporter\", type=\"cluster\"}"}
Dec 16 15:50:59 git-ucsf-edu otelcol-contrib[1567967]: 2025-12-16T15:50:59.336Z        warn        internal/transaction.go:151        Failed to scrape Prometheus endpoint        {"resource": {"service.instance.id": "f97dd064-4817-4015-8d04-e6d7ae39737f", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "prometheus/ghes", "otelcol.component.kind": "receiver", "otelcol.signal": "metrics", "scrape_timestamp": 1765900259335, "target_labels": "{__name__=\"up\", instance=\"git.ucsf.edu\", job=\"sql_exporter\"}"}
```

## revised config (with tagging added)
```yaml
---
exporters:
  datadog:
    api:
      site: us3.datadoghq.com
      key: obsfucated
    host_metadata:
      enabled: true

processors:
  batch/ghes:
  resource/team:
    attributes:
    - key: team
      value: "developerexperience"
      action: upsert

service:
  pipelines:
    metrics:
      receivers: [otlp/ghes, prometheus/ghes]
      processors: [resource/team, batch/ghes]
      exporters: [datadog]
```


# 👇

# need to do this Wednesday morning

==REVISE THE CONFIG (SEE ABOVE)==

```bash
# turn on the prometheus endpoint
ghe-config observability.metrics.prometheus-endpoint-enabled true
ghe-config observability.metrics.prometheus-endpoint-password R8rjq7sj.e21.n0.399
ghe-config-apply
```

## Wait for ghe-config-apply to finish, then...
```bash
sudo ss -tlnp | grep :8015 curl -u admin:your-password http://localhost:8015/metrics
```

## otelcol-contrib.service should be up
```bash
systemctl list-units --type=service --all | grep -i otel
```

## and should be making happy noises in the logs
```bash
sudo journalctl -u otelcol-contrib -f
```

## if otelcol-contrib.service is down, kick it
```bash
sudo systemctl start otelcol-contrib.service
```

## Datadog should start seeing metrics right away

still don't know where to look, but hopefully it'll be more obvious after Prometheus is generating metrics for Otel to push over to Datadog :-)

As soon as I can see metrics, define a monitor for disk space, and put metrics on the Dashboard


2025-12-17

the otelcol-contrib.service is thrashing:

```
Dec 17 12:27:36 git-ucsf-edu otelcol-contrib[3234596]: 2025-12-17T12:27:36.029Z        info        internal/retry_sender.go:133        Exporting failed. Will retry the request after interval.        {"resource": {"service.instance.id": "d0aa3541-0362-4378-b5b0-707c0e3e3f77", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "otlphttp/internal", "otelcol.component.kind": "exporter", "otelcol.signal": "metrics", "error": "failed to make an HTTP request: Post \"http://localhost:8015/opentelemetry/v1/metrics\": dial tcp [::1]:8015: connect: connection refused", "interval": "6.952657374s"}
Dec 17 12:27:37 git-ucsf-edu otelcol-contrib[3234596]: 2025-12-17T12:27:37.017Z        error        internal/queue_sender.go:42        Exporting failed. Dropping data.        {"resource": {"service.instance.id": "d0aa3541-0362-4378-b5b0-707c0e3e3f77", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "otlphttp/internal", "otelcol.component.kind": "exporter", "otelcol.signal": "metrics", "error": "no more retries left: failed to make an HTTP request: Post \"http://localhost:8015/opentelemetry/v1/metrics\": dial tcp [::1]:8015: connect: connection refused", "dropped_items": 5}
Dec 17 12:27:37 git-ucsf-edu otelcol-contrib[3234596]: go.opentelemetry.io/collector/exporter/exporterhelper/internal.NewQueueSender.func1
Dec 17 12:27:37 git-ucsf-edu otelcol-contrib[3234596]:         go.opentelemetry.io/collector/exporter/exporterhelper@v0.135.0/internal/queue_sender.go:42
Dec 17 12:27:37 git-ucsf-edu otelcol-contrib[3234596]: go.opentelemetry.io/collector/exporter/exporterhelper/internal/queuebatch.(*disabledBatcher[...]).Consume
Dec 17 12:27:37 git-ucsf-edu otelcol-contrib[3234596]:         go.opentelemetry.io/collector/exporter/exporterhelper@v0.135.0/internal/queuebatch/disabled_batcher.go:23
Dec 17 12:27:37 git-ucsf-edu otelcol-contrib[3234596]: go.opentelemetry.io/collector/exporter/exporterhelper/internal/queue.(*asyncQueue[...]).Start.func1
Dec 17 12:27:37 git-ucsf-edu otelcol-contrib[3234596]:         go.opentelemetry.io/collector/exporter/exporterhelper@v0.135.0/internal/queue/async_queue.go:49
Dec 17 12:27:37 git-ucsf-edu otelcol-contrib[3234596]: 2025-12-17T12:27:37.017Z        info        internal/retry_sender.go:133        Exporting failed. Will retry the request after interval.        {"resource": {"service.instance.id": "d0aa3541-0362-4378-b5b0-707c0e3e3f77", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "otlphttp/internal", "otelcol.component.kind": "exporter", "otelcol.signal": "metrics", "error": "failed to make an HTTP request: Post \"http://localhost:8015/opentelemetry/v1/metrics\": dial tcp [::1]:8015: connect: connection refused", "interval": "3.317263848s"}
Dec 17 12:27:37 git-ucsf-edu otelcol-contrib[3234596]: 2025-12-17T12:27:37.295Z        warn        internal/transaction.go:151        Failed to scrape Prometheus endpoint        {"resource": {"service.instance.id": "d0aa3541-0362-4378-b5b0-707c0e3e3f77", "service.name": "otelcol-contrib", "service.version": "0.135.0"}, "otelcol.component.id": "prometheus/ghes", "otelcol.component.kind": "receiver", "otelcol.signal": "metrics", "scrape_timestamp": 1765974457294, "target_labels": "{__name__=\"up\", instance=\"git.ucsf.edu\", job=\"process_exporter\"}"}
```
... can't connect to port 8015

looking at the prometheus service:

```bash
systemctl list-units --type=service --all | grep prom
  nginx_prometheus_exporter.service                       loaded    active   running Nginx Prometheus Exporter

sudo journalctl -u nginx_prometheus_exporter.service -f
-- Logs begin at Wed 2025-12-17 01:26:45 UTC. --
Dec 17 12:24:22 git-ucsf-edu systemd[1]: Started Nginx Prometheus Exporter.
Dec 17 12:24:22 git-ucsf-edu ghe-systemd-wrapper[3271812]: starting nginx in /
Dec 17 12:24:22 git-ucsf-edu ghe-systemd-wrapper[3271812]: no environment to load for nginx
Dec 17 12:24:22 git-ucsf-edu ghe-systemd-wrapper[3271812]: nginx: executing /usr/bin/nginx-prometheus-exporter --web.listen-address=:8030 --nginx.scrape-uri=http://127.0.0.1:8082/
Dec 17 12:24:23 git-ucsf-edu ghe-systemd-wrapper[3271812]: time=2025-12-17T12:24:23.453Z level=INFO source=exporter.go:126 msg=nginx-prometheus-exporter version="(version=1.5.0, branch=HEAD, revision=b14979c9f3634dcd5a2b158874e713beb3aca3d7)"
Dec 17 12:24:23 git-ucsf-edu ghe-systemd-wrapper[3271812]: time=2025-12-17T12:24:23.492Z level=INFO source=exporter.go:127 msg="build context" build_context="(go=go1.25.1, platform=linux/amd64, user=goreleaser, date=2025-09-04T08:13:55Z, tags=unknown)"
Dec 17 12:24:23 git-ucsf-edu ghe-systemd-wrapper[3271812]: time=2025-12-17T12:24:23.495Z level=INFO source=tls_config.go:347 msg="Listening on" address=[::]:8030
Dec 17 12:24:23 git-ucsf-edu ghe-systemd-wrapper[3271812]: time=2025-12-17T12:24:23.495Z level=INFO source=tls_config.go:350 msg="TLS is disabled." http2=false address=[::]:8030
```
... it's running on port 8030

is anything on port 8015?

```bash
admin@git-ucsf-edu:~$ sudo netstat plunt | grep -i 8015
admin@git-ucsf-edu:~$ sudo netstat plunt | grep -i 8030
tcp6       0      0 localhost:52278         localhost:8030          ESTABLISHED
tcp6       0      0 localhost:8030          localhost:52278         ESTABLISHED
```
... nope... but there *is* something on port 8030


# OK, walking away from this for now, but will open a support ticket later

- [ ]
- [x] open a support ticket ... I *think* looking at the data above, that prometheus starts on the wrong port... or else otel is looking at the wrong port ✅ 2025-12-19
- https://support.github.com/ticket/personal/0/3969763
