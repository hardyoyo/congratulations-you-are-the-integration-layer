[The OpenTelemetry LogoOpenTelemetry](/)

* [Docs](/docs/)
* [Ecosystem](/ecosystem/)
* [Status](/status/)
* [Community](/community/)
* [Training](/training/)
* [Blog](/blog/)
* English
  EN
  + [বাংলা (Bengali)](/bn/docs/collector/configuration/)
  + English
  + [Español](/es/docs/collector/configuration/)
  + [Français](/fr/docs/collector/configuration/)
  + [日本語 (Japanese)](/ja/docs/collector/configuration/)
  + [Polski (Polish)](/pl/docs/collector/configuration/)
  + [Português](/pt/docs/collector/configuration/)
  + [Română (Romanian)](/ro/docs/collector/configuration/)
  + [Українська (Ukrainian)](/uk/docs/collector/configuration/)
  + [中文 (Chinese)](/zh/docs/collector/configuration/)
* + Light
  + Dark
  + Auto

* [Collector](/docs/collector/)
  + [ ]
    [Quick start](/docs/collector/quick-start/)
  + [ ]
    [Install](/docs/collector/install/ "Install the Collector")
    - [ ]
      [Docker](/docs/collector/install/docker/ "Install the Collector with Docker")
    - [ ]
      [Kubernetes](/docs/collector/install/kubernetes/ "Install the Collector with Kubernetes")
    - [ ]
      [Binary](/docs/collector/install/binary/ "Install from a Collector binary")
      * [ ]
        [Linux](/docs/collector/install/binary/linux/ "Install the Collector on Linux")
      * [ ]
        [macOS](/docs/collector/install/binary/macos/ "Install the Collector on macOS")
      * [ ]
        [Windows](/docs/collector/install/binary/windows/ "Install the Collector on Windows")
  + [ ]
    [Deploy](/docs/collector/deploy/ "Deploy the Collector")
    - [ ]
      [Agent pattern](/docs/collector/deploy/agent/ "Agent deployment pattern")
    - [ ]
      [Gateway pattern](/docs/collector/deploy/gateway/ "Gateway deployment pattern")
    - [ ]
      [Other patterns](/docs/collector/deploy/other/ "Other deployment patterns")
      * [ ]
        [No Collector](/docs/collector/deploy/other/no-collector/)
  + [ ]
    [Configuration](/docs/collector/configuration/)
  + [ ]
    [Components](/docs/collector/components/)
    - [ ]
      [Receivers](/docs/collector/components/receiver/)
    - [ ]
      [Processors](/docs/collector/components/processor/)
    - [ ]
      [Exporters](/docs/collector/components/exporter/)
    - [ ]
      [Connectors](/docs/collector/components/connector/)
    - [ ]
      [Extensions](/docs/collector/components/extension/)
  + [ ]
    [Management](/docs/collector/management/)
  + [ ]
    [Distributions](/docs/collector/distributions/)
  + [ ]
    [Internal telemetry](/docs/collector/internal-telemetry/)
  + [ ]
    [Troubleshooting](/docs/collector/troubleshooting/)
  + [ ]
    [Scaling the Collector](/docs/collector/scaling/)
  + [ ]
    [Transforming telemetry](/docs/collector/transforming-telemetry/)
  + [ ]
    [Architecture](/docs/collector/architecture/)
  + [ ]
    [Extend](/docs/collector/extend/ "Extend the Collector")
    - [ ]
      [Build from source](/docs/collector/extend/build-from-source/)
    - [ ]
      [Build a custom Collector](/docs/collector/extend/ocb/ "Build a custom Collector with OpenTelemetry Collector Builder")
    - [ ]
      [Build custom components](/docs/collector/extend/custom-component/)
      * [ ]
        [Receivers](/docs/collector/extend/custom-component/receiver/ "Build a receiver")
      * [ ]
        [Connectors](/docs/collector/extend/custom-component/connector/ "Build a connector")
      * [ ]
        [Extensions](/docs/collector/extend/custom-component/extension/ "Build an extension")
        + [ ]
          [Authenticator](/docs/collector/extend/custom-component/extension/authenticator/ "Build an authenticator extension")
  + [ ]
    [Benchmarks](/docs/collector/benchmarks/)
  + [ ]
    [Registry](/docs/collector/registry/)
  + [ ]
    [Resiliency](/docs/collector/resiliency/)

[View page source](https://github.com/open-telemetry/opentelemetry.io/tree/main/content/en/docs/collector/configuration.md)
 [Edit this page](https://github.com/open-telemetry/opentelemetry.io/edit/main/content/en/docs/collector/configuration.md)
 [Create child page](https://github.com/open-telemetry/opentelemetry.io/new/main/content/en/docs/collector?filename=change-me.md&value=---%0Atitle%3A+%22Long+Page+Title%22%0AlinkTitle%3A+%22Short+Nav+Title%22%0Aweight%3A+100%0Adescription%3A+%3E-%0A+++++Page+description+for+heading+and+indexes.%0A---%0A%0A%23%23+Heading%0A%0AEdit+this+template+to+create+your+new+page.%0A%0A%2A+Give+it+a+good+name%2C+ending+in+%60.md%60+-+e.g.+%60get-started.md%60%0A%2A+Edit+the+%22front+matter%22+section+at+the+top+of+the+page+%28weight+controls+how+its+ordered+amongst+other+pages+in+the+same+directory%3B+lowest+number+first%29.%0A%2A+Add+a+good+commit+message+at+the+bottom+of+the+page+%28%3C80+characters%3B+use+the+extended+description+field+for+more+detail%29.%0A%2A+Create+a+new+branch+so+you+can+preview+your+new+file+and+request+a+review+via+Pull+Request.%0A)
 [Create documentation issue](https://github.com/open-telemetry/opentelemetry.io/issues/new?title=Configuration)

On this page

* [Location](#location)
* [Configuration structure](#basics)
* [Receivers ![](/img/logos/32x32/Receivers.svg)](#receivers)
* [Processors ![](/img/logos/32x32/Processors.svg)](#processors)
* [Exporters ![](/img/logos/32x32/Exporters.svg)](#exporters)
* [Connectors ![](/img/logos/32x32/Load_Balancer.svg)](#connectors)
* [Extensions ![](/img/logos/32x32/Extensions.svg)](#extensions)
* [Service section](#service)
  + [Extensions](#service-extensions)
  + [Pipelines](#pipelines)
  + [Telemetry](#telemetry)
* [Other Information](#other-information)
  + [Environment variables](#environment-variables)
  + [Proxy support](#proxy-support)
  + [Authentication](#authentication)
  + [Configuring certificates](#setting-up-certificates)
    - [Using certificates in the Collector](#using-certificates-in-the-collector)
      * [TLS configuration for receivers (server-side)](#tls-configuration-for-receivers-server-side)
      * [TLS configuration for exporters (client-side)](#tls-configuration-for-exporters-client-side)
      * [mTLS configuration (mutual TLS)](#mtls-configuration-mutual-tls)
      * [Common TLS settings](#common-tls-settings)
* [Override settings](#override-settings)
  + [Simple property](#simple-property)
  + [Complex nested keys](#complex-nested-keys)
  + [Multiple values](#multiple-values)
  + [Array values](#array-values)
* [Embedding other configuration providers](#embedding-other-configuration-providers)
* [How to check components available in a distribution](#how-to-check-components-available-in-a-distribution)
* [How to examine the final configuration](#how-to-examine-the-final-configuration)
  + [How to view sensitive fields](#how-to-view-sensitive-fields)
  + [How to print the final configuration in JSON format](#how-to-print-the-final-configuration-in-json-format)

1. [Docs](/docs/)
2. [Collector](/docs/collector/)
3. Configuration

# Configuration

Learn how to configure the Collector to suit your needs

You can configure the OpenTelemetry Collector to suit your observability needs.
Before you learn how Collector configuration works, familiarize yourself with
the following content:

* [Data collection concepts](/docs/concepts/components/#collector), to understand the repositories applicable to
  the OpenTelemetry Collector.
* [Security guidance for end users](/docs/security/config-best-practices/)
* [Security guidance for component developers](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/security-best-practices.md)

## Location

By default, the Collector configuration is located in
`/etc/<otel-directory>/config.yaml`, where `<otel-directory>` can be `otelcol`,
`otelcol-contrib`, or another value, depending on the Collector version or the
Collector distribution you’re using.

You can provide one or more configurations using the `--config` option. For
example:

```
otelcol --config=customconfig.yaml
```

The `--config` flag accepts either a file path or values in the form of a config
URI `"<scheme>:<opaque_data>"`. Currently, the OpenTelemetry Collector supports
the following providers for `scheme`:

* **file** - Reads configuration from a file. E.g. `file:path/to/config.yaml`.
* **env** - Reads configuration from an environment variable. E.g.
  `env:MY_CONFIG_IN_AN_ENVVAR`.
* **yaml** - Reads configuration from a YAML string, with `::` delimiting
  subpaths. E.g. `yaml:exporters::debug::verbosity: detailed`.

* **http** - Reads configuration from an HTTP URI. E.g. `http://www.example.com`
* **https** - Reads configuration from an HTTPS URI. E.g.
  `https://www.example.com`

You can also provide multiple configurations using multiple files at different
paths. Each file can be a full or partial configuration, and the files can
reference components from each other. If the merger of files does not constitute
a complete configuration, the user receives an error since required components
are not added by default. Pass in multiple file paths at the command line as
follows:

```
otelcol --config=file:/path/to/first/file --config=file:/path/to/second/file
```

You can also provide configurations using environment variables, HTTP URIs, or
YAML paths. For example:

```
otelcol --config=env:MY_CONFIG_IN_AN_ENVVAR --config=https://server/config.yaml
otelcol --config="yaml:exporters::debug::verbosity: normal"
```

Tip

When referring to nested keys in YAML paths, make sure to use double colons
(`::`) to avoid confusion with namespaces that contain dots. For example:
`receivers::docker_stats::metrics::container.cpu.utilization::enabled: false`.

To validate a configuration file, use the `validate` command. For example:

```
otelcol validate --config=customconfig.yaml
```

## Configuration structure

The structure of any Collector configuration file consists of four classes of
pipeline components that access telemetry data:

* [Receivers](#receivers)
  ![](/img/logos/32x32/Receivers.svg)
* [Processors](#processors)
  ![](/img/logos/32x32/Processors.svg)
* [Exporters](#exporters)
  ![](/img/logos/32x32/Exporters.svg)
* [Connectors](#connectors)
  ![](/img/logos/32x32/Load_Balancer.svg)

After each pipeline component is configured you must enable it using the
pipelines within the [service](#service) section of the configuration file.

Besides pipeline components you can also configure [extensions](#extensions),
which provide capabilities that can be added to the Collector, such as
diagnostic tools. Extensions don’t require direct access to telemetry data and
are enabled through the [service](#service) section.

The following is an example of Collector
configuration with a receiver, a processor, an exporter, and three extensions.

Warning

While it is generally preferable to bind endpoints to `localhost` when all
clients are local, our example configurations use the “unspecified” address
`0.0.0.0` as a convenience. The Collector currently defaults to `0.0.0.0`, but
the default will be changed to `localhost` in the near future. For details
concerning either of these choices as endpoint configuration value, see
[Safeguards against denial of service attacks](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/security-best-practices.md#safeguards-against-denial-of-service-attacks).

```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

exporters:
  otlp_grpc:
    endpoint: otelcol:4317
    sending_queue:
      batch:

extensions:
  health_check:
    endpoint: 0.0.0.0:13133
  pprof:
    endpoint: 0.0.0.0:1777
  zpages:
    endpoint: 0.0.0.0:55679

service:
  extensions: [health_check, pprof, zpages]
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [otlp_grpc]
    metrics:
      receivers: [otlp]
      exporters: [otlp_grpc]
    logs:
      receivers: [otlp]
      exporters: [otlp_grpc]
```

Note that receivers, processors, exporters and pipelines are defined through
component identifiers following the `type[/name]` format, for example `otlp` or
`otlp/2`. You can define components of a given type more than once as long as
the identifiers are unique. For example:

```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
  otlp/2:
    protocols:
      grpc:
        endpoint: 0.0.0.0:55690

exporters:
  otlp_grpc:
    endpoint: otelcol:4317
    sending_queue:
      batch:
  otlp_grpc/2:
    endpoint: otelcol2:4317
    sending_queue:
      batch:

extensions:
  health_check:
    endpoint: 0.0.0.0:13133
  pprof:
    endpoint: 0.0.0.0:1777
  zpages:
    endpoint: 0.0.0.0:55679

service:
  extensions: [health_check, pprof, zpages]
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [otlp_grpc]
    traces/2:
      receivers: [otlp/2]
      exporters: [otlp_grpc/2]
    metrics:
      receivers: [otlp]
      exporters: [otlp_grpc]
    logs:
      receivers: [otlp]
      exporters: [otlp_grpc]
```

The configuration can also include other files, causing the Collector to merge
them in a single in-memory representation of the YAML configuration:

```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317

exporters: ${file:exporters.yaml}

service:
  extensions: []
  pipelines:
    traces:
      receivers: [otlp]
      processors: []
      exporters: [otlp_grpc]
```

With the `exporters.yaml` file being:

```
otlp_grpc:
  endpoint: otelcol.observability.svc.cluster.local:443
```

The final result in memory will be:

```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317

exporters:
  otlp_grpc:
    endpoint: otelcol.observability.svc.cluster.local:443

service:
  extensions: []
  pipelines:
    traces:
      receivers: [otlp]
      processors: []
      exporters: [otlp_grpc]
```

## Receivers ![](/img/logos/32x32/Receivers.svg)

Receivers collect telemetry from one or more sources. They can be pull or push
based, and may support one or more [data sources](/docs/concepts/signals/).

Receivers are configured in the `receivers` section. Many receivers come with
default settings, so that specifying the name of the receiver is enough to
configure it. If you need to configure a receiver or want to change the default
configuration, you can do so in this section. Any setting you specify overrides
the default values, if present.

> Configuring a receiver does not enable it. Receivers are enabled by adding
> them to the appropriate pipelines within the [service](#service) section.

The Collector requires one or more receivers. The following example shows
various receivers in the same configuration file:

```
receivers:
  # Data sources: logs
  fluentforward:
    endpoint: 0.0.0.0:8006

  # Data sources: metrics
  hostmetrics:
    scrapers:
      cpu:
      disk:
      filesystem:
      load:
      memory:
      network:
      process:
      processes:
      paging:

  # Data sources: traces
  jaeger:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      thrift_binary:
      thrift_compact:
      thrift_http:

  # Data sources: traces, metrics, logs
  kafka:
    protocol_version: 2.0.0

  # Data sources: traces, metrics
  opencensus:

  # Data sources: traces, metrics, logs
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
        tls:
          cert_file: cert.pem
          key_file: cert-key.pem
      http:
        endpoint: 0.0.0.0:4318

  # Data sources: metrics
  prometheus:
    config:
      scrape_configs:
        - job_name: otel-collector
          scrape_interval: 5s
          static_configs:
            - targets: [localhost:8888]

  # Data sources: traces
  zipkin:
```

> For detailed receiver configuration, see the
> [receiver README](https://github.com/open-telemetry/opentelemetry-collector/blob/main/receiver/README.md).

## Processors ![](/img/logos/32x32/Processors.svg)

Processors take the data collected by receivers and modify or transform it
before sending it to the exporters. Data processing happens according to rules
or settings defined for each processor, which might include filtering, dropping,
renaming, or recalculating telemetry, among other operations. The order of the
processors in a pipeline determines the order of the processing operations that
the Collector applies to the signal.

Processors are optional, although some
[are recommended](https://github.com/open-telemetry/opentelemetry-collector/tree/main/processor#recommended-processors).

You can configure processors using the `processors` section of the Collector
configuration file. Any setting you specify overrides the default values, if
present.

> Configuring a processor does not enable it. Processors are enabled by adding
> them to the appropriate pipelines within the [service](#service) section.

The following example shows several default processors in the same configuration
file. You can find the full list of processors by combining the list from
[opentelemetry-collector-contrib](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor)
and the list from
[opentelemetry-collector](https://github.com/open-telemetry/opentelemetry-collector/tree/main/processor).

```
processors:
  # Data sources: traces
  attributes:
    actions:
      - key: environment
        value: production
        action: insert
      - key: db.statement
        action: delete
      - key: email
        action: hash

  # Data sources: metrics, metrics, logs
  filter:
    error_mode: ignore
    traces:
      span:
        - 'attributes["container.name"] == "app_container_1"'
        - 'resource.attributes["host.name"] == "localhost"'
        - 'name == "app_3"'
      spanevent:
        - 'attributes["grpc"] == true'
        - 'IsMatch(name, ".*grpc.*")'
    metrics:
      metric:
        - 'name == "my.metric" and resource.attributes["my_label"] == "abc123"'
        - 'type == METRIC_DATA_TYPE_HISTOGRAM'
      datapoint:
        - 'metric.type == METRIC_DATA_TYPE_SUMMARY'
        - 'resource.attributes["service.name"] == "my_service_name"'
    logs:
      log_record:
        - 'IsMatch(body, ".*password.*")'
        - 'severity_number < SEVERITY_NUMBER_WARN'

  # Data sources: traces, metrics, logs
  memory_limiter:
    check_interval: 5s
    limit_mib: 4000
    spike_limit_mib: 500

  # Data sources: traces
  resource:
    attributes:
      - key: cloud.zone
        value: zone-1
        action: upsert
      - key: k8s.cluster.name
        from_attribute: k8s-cluster
        action: insert
      - key: redundant-attribute
        action: delete

  # Data sources: traces
  probabilistic_sampler:
    hash_seed: 22
    sampling_percentage: 15

  # Data sources: traces
  span:
    name:
      to_attributes:
        rules:
          - ^\/api\/v1\/document\/(?P<documentId>.*)\/update$
      from_attributes: [db.svc, operation]
      separator: '::'
```

> For detailed processor configuration, see the
> [processor README](https://github.com/open-telemetry/opentelemetry-collector/blob/main/processor/README.md).

## Exporters ![](/img/logos/32x32/Exporters.svg)

Exporters send data to one or more backends or destinations. Exporters can be
pull or push based, and may support one or more
[data sources](/docs/concepts/signals/).

Each key within the `exporters` section defines an exporter instance, The key
follows the `type/name` format, where `type` specifies the exporter type (e.g.,
`otlp`, `kafka`, `prometheus`), and `name` (optional) can be appended to provide
a unique name for multiple instance of the same type.

Most exporters require configuration to specify at least the destination, as
well as security settings, like authentication tokens or TLS certificates. Any
setting you specify overrides the default values, if present.

> Configuring an exporter does not enable it. Exporters are enabled by adding
> them to the appropriate pipelines within the [service](#service) section.

The Collector requires one or more exporters. The following example shows
various exporters in the same configuration file:

```
exporters:
  # Data sources: traces, metrics, logs
  file:
    path: ./filename.json

  # Data sources: traces
  otlp_grpc/jaeger:
    endpoint: jaeger-server:4317
    tls:
      cert_file: cert.pem
      key_file: cert-key.pem

  # Data sources: traces, metrics, logs
  kafka:
    protocol_version: 2.0.0

  # Data sources: traces, metrics, logs
  # NOTE: Prior to v0.86.0 use `logging` instead of `debug`
  debug:
    verbosity: detailed

  # Data sources: traces, metrics
  opencensus:
    endpoint: otelcol2:55678

  # Data sources: traces, metrics, logs
  otlp_grpc:
    endpoint: otelcol2:4317
    tls:
      cert_file: cert.pem
      key_file: cert-key.pem

  # Data sources: traces, metrics
  otlp_http:
    endpoint: https://otlp.example.com:4318

  # Data sources: metrics
  prometheus:
    endpoint: 0.0.0.0:8889
    namespace: default

  # Data sources: metrics
  prometheusremotewrite:
    endpoint: http://prometheus.example.com:9411/api/prom/push
    # When using the official Prometheus (running via Docker)
    # endpoint: 'http://prometheus:9090/api/v1/write', add:
    # tls:
    #   insecure: true

  # Data sources: traces
  zipkin:
    endpoint: http://zipkin.example.com:9411/api/v2/spans
```

Notice that some exporters require x.509 certificates in order to establish
secure connections, as described in
[setting up certificates](#setting-up-certificates).

> For more information on exporter configuration, see the
> [exporter README.md](https://github.com/open-telemetry/opentelemetry-collector/blob/main/exporter/README.md).

## Connectors ![](/img/logos/32x32/Load_Balancer.svg)

Connectors join two pipelines, acting as both exporter and receiver. A connector
consumes data as an exporter at the end of one pipeline and emits data as a
receiver at the beginning of another pipeline. The data consumed and emitted may
be of the same type or of different data types. You can use connectors to
summarize consumed data, replicate it, or route it.

You can configure one or more connectors using the `connectors` section of the
Collector configuration file. By default, no connectors are configured. Each
type of connector is designed to work with one or more pairs of data types and
may only be used to connect pipelines accordingly.

> Configuring a connector doesn’t enable it. Connectors are enabled through
> pipelines within the [service](#service) section.

The following example shows the `count` connector and how it’s configured in the
`pipelines` section. Notice that the connector acts as an exporter for traces
and as a receiver for metrics, connecting both pipelines:

```
receivers:
  foo:

exporters:
  bar:

connectors:
  count:
    spanevents:
      my.prod.event.count:
        description: The number of span events from my prod environment.
        conditions:
          - 'attributes["env"] == "prod"'
          - 'name == "prodevent"'

service:
  pipelines:
    traces:
      receivers: [foo]
      exporters: [count]
    metrics:
      receivers: [count]
      exporters: [bar]
```

> For detailed connector configuration, see the
> [connector README](https://github.com/open-telemetry/opentelemetry-collector/blob/main/connector/README.md).

## Extensions ![](/img/logos/32x32/Extensions.svg)

Extensions are optional components that expand the capabilities of the Collector
to accomplish tasks not directly involved with processing telemetry data. For
example, you can add extensions for Collector health monitoring, service
discovery, or data forwarding, among others.

You can configure extensions through the `extensions` section of the Collector
configuration file. Most extensions come with default settings, so you can
configure them just by specifying the name of the extension. Any setting you
specify overrides the default values, if present.

> Configuring an extension doesn’t enable it. Extensions are enabled within the
> [service](#service) section.

By default, no extensions are configured. The following example shows several
extensions configured in the same file:

```
extensions:
  health_check:
    endpoint: 0.0.0.0:13133
  pprof:
    endpoint: 0.0.0.0:1777
  zpages:
    endpoint: 0.0.0.0:55679
```

> For detailed extension configuration, see the
> [extension README](https://github.com/open-telemetry/opentelemetry-collector/blob/main/extension/README.md).

## Service section

The `service` section is used to configure what components are enabled in the
Collector based on the configuration found in the receivers, processors,
exporters, and extensions sections. If a component is configured, but not
defined within the `service` section, then it’s not enabled.

The service section consists of three subsections:

* Extensions
* Pipelines
* Telemetry

### Extensions

The `extensions` subsection consists of a list of desired extensions to be
enabled. For example:

```
service:
  extensions: [health_check, pprof, zpages]
```

### Pipelines

The `pipelines` subsection is where the pipelines are configured, which can be
of the following types:

* `traces` collect and processes trace data.
* `metrics` collect and processes metric data.
* `logs` collect and processes log data.

A pipeline consists of a set of receivers, processors and exporters. Before
including a receiver, processor, or exporter in a pipeline, make sure to define
its configuration in the appropriate section.

You can use the same receiver, processor, or exporter in more than one pipeline.
When a processor is referenced in multiple pipelines, each pipeline gets a
separate instance of the processor.

The following is an example of pipeline configuration. Note that the order of
processors dictates the order in which data is processed:

```
service:
  pipelines:
    metrics:
      receivers: [opencensus, prometheus]
      exporters: [opencensus, prometheus]
    traces:
      receivers: [opencensus, jaeger]
      processors: [memory_limiter]
      exporters: [opencensus, zipkin]
```

As with components, use the `type[/name]` syntax to create additional pipelines
for a given type. Here is an example extending the previous configuration:

```
service:
  pipelines:
    # ...
    traces:
      # ...
    traces/2:
      receivers: [opencensus]
      exporters: [zipkin]
```

### Telemetry

The `telemetry` config section is where you can set up observability for the
Collector itself. It consists of two subsections: `logs` and `metrics`. To learn
how to configure these signals, see
[Activate internal telemetry in the Collector](/docs/collector/internal-telemetry/#activate-internal-telemetry-in-the-collector).

## Other Information

### Environment variables

The use and expansion of environment variables is supported in the Collector
configuration. For example to use the values stored on the `DB_KEY` and
`OPERATION` environment variables you can write the following:

```
processors:
  attributes/example:
    actions:
      - key: ${env:DB_KEY}
        action: ${env:OPERATION}
```

You can pass defaults to an environment variable using the bash syntax:
`${env:DB_KEY:-some-default-var}`

```
processors:
  attributes/example:
    actions:
      - key: ${env:DB_KEY:-mydefault}
        action: ${env:OPERATION:-}
```

Use `$$` to indicate a literal `$`. For example, representing
`$DataVisualization` would look like the following:

```
exporters:
  prometheus:
    endpoint: prometheus:8889
    namespace: $$DataVisualization
```

### Proxy support

Exporters that use the [`net/http`](https://pkg.go.dev/net/http) package respect
the following proxy environment variables:

* `HTTP_PROXY`: Address of the HTTP proxy
* `HTTPS_PROXY`: Address of the HTTPS proxy
* `NO_PROXY`: Addresses that must not use the proxy

If set at Collector start time, exporters, regardless of the protocol, proxy
traffic or bypass proxy traffic as defined by these environment variables.

### Authentication

Most receivers exposing an HTTP or gRPC port can be protected using the
Collector’s authentication mechanism. Similarly, most exporters using HTTP or
gRPC clients can add authentication to outgoing requests.

The authentication mechanism in the Collector uses the extensions mechanism,
allowing for custom authenticators to be plugged into Collector distributions.
Each authentication extension has two possible usages:

* As client authenticator for exporters, adding auth data to outgoing requests
* As server authenticator for receivers, authenticating incoming connections.

For a list of known authenticators, see the
[Registry](/ecosystem/registry/?s=authenticator&component=extension). If you’re
interested in developing a custom authenticator, see
[Building an authenticator extension](/docs/collector/extend/custom-component/extension/authenticator/).

To add a server authenticator to a receiver in the Collector, follow these
steps:

1. Add the authenticator extension and its configuration under `.extensions`.
2. Add a reference to the authenticator to `.services.extensions`, so that it’s
   loaded by the Collector.
3. Add a reference to the authenticator under
   `.receivers.<your-receiver>.<http-or-grpc-config>.auth`.

The following example uses the OIDC authenticator on the receiver side, making
this suitable for a remote Collector that receives data from an OpenTelemetry
Collector acting as agent:

```
extensions:
  oidc:
    issuer_url: http://localhost:8080/auth/realms/opentelemetry
    audience: collector

receivers:
  otlp/auth:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
        auth:
          authenticator: oidc

processors:

exporters:
  # NOTE: Prior to v0.86.0 use `logging` instead of `debug`.
  debug:

service:
  extensions:
    - oidc
  pipelines:
    traces:
      receivers:
        - otlp/auth
      processors: []
      exporters:
        - debug
```

On the agent side, this is an example that makes the OTLP exporter obtain OIDC
tokens, adding them to every RPC made to a remote Collector:

```
extensions:
  oauth2client:
    client_id: agent
    client_secret: some-secret
    token_url: http://localhost:8080/auth/realms/opentelemetry/protocol/openid-connect/token

receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317

processors:

exporters:
  otlp_grpc/auth:
    endpoint: remote-collector:4317
    auth:
      authenticator: oauth2client

service:
  extensions:
    - oauth2client
  pipelines:
    traces:
      receivers:
        - otlp
      processors: []
      exporters:
        - otlp_grpc/auth
```

### Configuring certificates

In a production environment, use TLS certificates for secure communication or
mTLS for mutual authentication. Follow these steps to generate self-signed
certificates as in this example. You might want to use your current cert
provisioning procedures to procure a certificate for production usage.

Install [`cfssl`](https://github.com/cloudflare/cfssl) and create the following
`csr.json` file:

```
{
  "hosts": ["localhost", "127.0.0.1"],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "O": "OpenTelemetry Example"
    }
  ]
}
```

Then run the following commands:

```
cfssl genkey -initca csr.json | cfssljson -bare ca
cfssl gencert -ca ca.pem -ca-key ca-key.pem csr.json | cfssljson -bare cert
```

This creates two certificates:

* An “OpenTelemetry Example” Certificate Authority (CA) in `ca.pem`, with the
  associated key in `ca-key.pem`
* A client certificate in `cert.pem`, signed by the OpenTelemetry Example CA,
  with the associated key in `cert-key.pem`.

#### Using certificates in the Collector

Once you have the certificates, configure the Collector to use them.

##### TLS configuration for receivers (server-side)

Configure TLS on a receiver to encrypt incoming connections. Use `cert_file` and
`key_file` to specify the server certificate:

```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
        tls:
          cert_file: /path/to/cert.pem
          key_file: /path/to/cert-key.pem
      http:
        endpoint: 0.0.0.0:4318
        tls:
          cert_file: /path/to/cert.pem
          key_file: /path/to/cert-key.pem
```

##### TLS configuration for exporters (client-side)

Configure TLS on an exporter to encrypt outgoing connections. Use `ca_file` to
verify the server’s certificate:

```
exporters:
  otlp_grpc:
    endpoint: otelcol2:4317
    tls:
      ca_file: /path/to/ca.pem
```

If you also need to present a client certificate to the server:

```
exporters:
  otlp_grpc:
    endpoint: otelcol2:4317
    tls:
      ca_file: /path/to/ca.pem
      cert_file: /path/to/cert.pem
      key_file: /path/to/cert-key.pem
```

##### mTLS configuration (mutual TLS)

For mTLS, both the receiver and the exporter verify each other’s certificates.
On the receiver, add `client_ca_file` to verify client certificates:

```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
        tls:
          cert_file: /path/to/server-cert.pem
          key_file: /path/to/server-key.pem
          client_ca_file: /path/to/ca.pem
```

On the exporter, provide both the CA to verify the server and the client
certificate:

```
exporters:
  otlp_grpc:
    endpoint: remote-collector:4317
    tls:
      ca_file: /path/to/ca.pem
      cert_file: /path/to/client-cert.pem
      key_file: /path/to/client-key.pem
```

##### Common TLS settings

The following settings are available for TLS configuration:

| Setting | Description |
| --- | --- |
| `ca_file` | Path to the CA certificate to verify the peer certificate |
| `cert_file` | Path to the TLS certificate |
| `key_file` | Path to the TLS private key |
| `client_ca_file` | Path to the CA certificate to verify client certificates |
| `insecure` | Disable TLS verification (not recommended for production) |
| `insecure_skip_verify` | Skip server certificate verification (not recommended) |
| `min_version` | Minimum TLS version (for example, `1.2` or `1.3`) |
| `max_version` | Maximum TLS version |
| `reload_interval` | Duration after which the certificate is reloaded |

> For more details on TLS configuration options, see the
> [configtls documentation](https://github.com/open-telemetry/opentelemetry-collector/blob/v0.147.0/config/configtls/README.md).

## Override settings

You can override Collector settings using the `--set` option. The settings you
define with this method are merged into the final configuration after all
`--config` sources are resolved and merged.

The following examples show how to override settings inside nested sections:

### Simple property

The `--set` option takes always one key/value pair, and it is used like this:
`--set key=value`. The YAML equivalent of that is:

```
key: value
```

### Complex nested keys

Use two colons (`::`) in the pair’s name as key separator to reference nested
map values. For example, `--set outer::inner=value` is translated into this:

```
outer:
  inner: value
```

### Multiple values

To set multiple values specify multiple –set flags, so `--set a=b --set c=d`
becomes:

```
a: b
c: d
```

### Array values

Arrays can be expressed by enclosing values in `[]`. For example,
`--set "key=[a, b, c]"` translates to:

```
key:
  - a
  - b
  - c
```

If you need to represent more complex data structures, the use of YAML is highly
recommended.

Caution

The `--set` option has the following limitations:

1. Does not support setting a key that contains a dot `.`.
2. Does not support setting a key that contains an equal sign `=`.
3. The configuration key separator inside the value part of the property is
   “::”. For example `--set "name={a::b: c}"` is equivalent with
   `--set name::a::b=c`.

## Embedding other configuration providers

One configuration provider can make references to other config providers, like
the following:

```
receivers:
  otlp:
    protocols:
      grpc:

exporters: ${file:otlp-exporter.yaml}

service:
  extensions: []
  pipelines:
    traces:
      receivers: [otlp]
      processors: []
      exporters: [otlp_grpc]
```

## How to check components available in a distribution

Use the sub command build-info. Below is an example:

```
otelcol components
```

Sample output:

```
buildinfo:
  command: otelcol
  description: OpenTelemetry Collector
  version: 0.143.0
receivers:
  - otlp
processors:
  - memory_limiter
exporters:
  - otlp_grpc
  - otlp_http
  - debug
extensions:
  - zpages
```

## How to examine the final configuration

Caution

This command is an experimental functionality. Its behavior may change with no
warning.

Use `print-config` in the default mode (`--mode=redacted`) and
`--feature-gates=otelcol.printInitialConfig`:

```
otelcol print-config --config=file:examples/local/otel-config.yaml
```

Note that by default the configuration will only print when it is valid, and
that sensitive information will be redacted. To print a potentially invalid
configuration, use `--validate=false`.

### How to view sensitive fields

Use `print-config` with `--mode=unredacted` and
`--feature-gates=otelcol.printInitialConfig`:

```
otelcol print-config --mode=unredacted --config=file:examples/local/otel-config.yaml
```

### How to print the final configuration in JSON format

Caution

This command is an experimental functionality. Its behavior may change with no
warning.

Use `print-config` with `--format=json` and
`--feature-gates=otelcol.printInitialConfig`. Note that JSON format is
considered unstable.

```
otelcol print-config --format=json --config=file:examples/local/otel-config.yaml
```

## Feedback

Was this page helpful?

Yes
No

Thank you. Your feedback is appreciated!

Please let us know [how we can improve this page](https://github.com/open-telemetry/opentelemetry.io/issues/new?template=PAGE_FEEDBACK.yml&title=[Page+feedback]%3A+ADD+A+SUMMARY+OF+YOUR+FEEDBACK+HERE). Your feedback is appreciated!

Last modified February 5, 2026: [update references to otlp and otlphttp exporters in Collector config (#9096) (a2230d9b)](https://github.com/open-telemetry/opentelemetry.io/commit/a2230d9bd5318e4c7588a7feecaa2e10d0ffd0e6)

©
2019–present
OpenTelemetry Authors | Docs [CC BY 4.0](https://creativecommons.org/licenses/by/4.0)All Rights Reserved
