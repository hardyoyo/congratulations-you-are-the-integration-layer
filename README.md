# Congratulations, You're the Integration Layer Now

[![CI](https://github.com/hardyoyo/congratulations-you-are-the-integration-layer/actions/workflows/ci.yml/badge.svg)](https://github.com/hardyoyo/congratulations-you-are-the-integration-layer/actions/workflows/ci.yml)

### OpenTelemetry in the UC Stack

A 45-minute talk for UC Tech 2026 by Hardy Pottinger.

## Abstract

OpenTelemetry is not new. Many of us have heard of it. Some of us have
experimented with it. What has changed is the vendors. Observability platforms
increasingly ship OpenTelemetry support as a first-class integration model.
It is quickly becoming the default path for telemetry. And that shift has
consequences.

When vendors standardize on OpenTelemetry, they assume someone owns the
pipeline: instrumentation, collectors, routing, sampling, cost control, and
governance. Increasingly, that someone is the platform team.

Congratulations -- you're the integration layer now.

## Building the slides

```bash
make slides
```

## Previewing the slides

```bash
make slides-serve
```

## License

[![CC BY 4.0](https://licensebuttons.net/l/by/4.0/88x31.png)](https://creativecommons.org/licenses/by/4.0/)

This work is licensed under a
[Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).
