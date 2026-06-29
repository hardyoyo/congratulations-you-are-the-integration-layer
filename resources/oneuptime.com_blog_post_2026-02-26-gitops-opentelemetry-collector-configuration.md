[Skip to main content](#main-content)

[OneUptime
![OneUptime logo](/img/3-transparent.svg)](/)

Open menu

Products

### Essentials

[Monitoring

Uptime & synthetic checks](/product/monitoring)

[Status Page

Communicate incidents to users](/product/status-page)

[Incidents

Detect, manage & resolve](/product/incident-management)

[On-Call & Alerts

Smart routing & escalations](/product/on-call)

### Observability

[Logs

Fastest log ingest & search](/product/logs-management)

[Metrics

Application & infra metrics](/product/metrics)

[Traces

Distributed request tracing](/product/traces)

[Exceptions

Error tracking & debugging](/product/exceptions)

### Automation & Analytics

[Workflows

No-code automation builder](/product/workflows)

[Dashboards

Custom data visualizations](/product/dashboards)

[AI Agent

Auto-fix issues with AI-powered PRs. Let AI analyze incidents and automatically create pull requests to resolve them.](/product/ai-agent)

### Resources

[Documentation](/docs)
[API Reference](/reference)
[GitHub](https://github.com/oneuptime/oneuptime)
[Blog & Guides](/blog)

### Get Started

[Start Free Trial](/accounts/register)
[Request Demo](/enterprise/demo)

[[email protected]](/cdn-cgi/l/email-protection#eb988a878e98ab84858e9e9b9f82868ec5888486)

Open Source — Self-host or use our cloud. Your data, your choice.

[View Pricing](/pricing)
[Enterprise](/enterprise/overview)

Enterprise

Enterprise

## Built for how you work

Scale your reliability operations with enterprise-grade tools.

[Enterprise Overview

Scale with confidence](/enterprise/overview)
[Request Demo

See it in action](/enterprise/demo)

[Contact Sales](/legal/contact)

Enterprise

[Enterprise Overview

Solutions for large organizations](/enterprise/overview)
[Request Demo

Schedule a personalized demo](/enterprise/demo)

Teams

[DevOps](/solutions/devops)
[SRE](/solutions/sre)
[Platform](/solutions/platform)
[Developers](/solutions/developers)

Industries

[FinTech](/industries/fintech)
[SaaS](/industries/saas)
[Healthcare](/industries/healthcare)
[E-Commerce](/industries/ecommerce)
[Media](/industries/media)
[Government](/industries/government)

[Documentation](/docs)
[Pricing](/pricing)
[Blog](/blog)

[Get Started Free](/accounts/register)

[Pricing](/pricing)

Resources

Resources

## Learn & Connect

Everything you need to get started and succeed.

[Documentation

Guides & tutorials](/docs)
[API Reference

REST API & SDKs](/reference)

[Star on GitHub](https://github.com/oneuptime/oneuptime)

Learn

[Blog

News & insights](/blog)
[Status

System status](https://status.oneuptime.com)
[Changelog

What's new](https://github.com/OneUptime/oneuptime/releases)
[Videos

Watch & learn](https://www.youtube.com/%40OneUptimehq)

Support

[Help Center](/support)
[Contact Us](/cdn-cgi/l/email-protection#4d3e383d3d223f390d222328383d39242028632e2220)

Company

[About Us](/about)
[Merch Store](https://shop.oneuptime.com)

[Legal](/legal)
[Privacy](/legal/privacy)
[Terms](/legal/terms)

100% Open Source

[Sign
in](/accounts)
[Sign up](/accounts/register)

![OneUptime](/img/3-transparent.svg)

Close menu

[Status Page](/product/status-page)

[Incidents](/product/incident-management)

[Monitoring](/product/monitoring)

[On-Call](/product/on-call)

[Logs](/product/logs-management)

[Metrics](/product/metrics)

[Traces](/product/traces)

[Exceptions](/product/exceptions)

[Workflows](/product/workflows)

[Dashboards](/product/dashboards)

[AI Agent](/product/ai-agent)

Enterprise

[DevOps](/solutions/devops)
[SRE](/solutions/sre)
[Platform](/solutions/platform)

[Pricing](/pricing)
[Docs](/docs)
[Request Demo](/enterprise/demo)
[Support](/support)

[Sign
up](/accounts/register)

Existing customer?
[Sign in](/accounts)

![]()

# How to Implement GitOps for OpenTelemetry Collector Configuration

Learn how to manage OpenTelemetry Collector configurations using GitOps principles with version control, automated deployment, and rollback capabilities.

[![Nawaz Dhandala](https://avatars.githubusercontent.com/nawazdhandala)
@nawazdhandala](https://github.com/nawazdhandala)
•
Feb 06, 2026
•

Reading time

[OpenTelemetry](/blog/tag/opentelemetry)
[GitOps](/blog/tag/gitops)
[Collector](/blog/tag/collector)
[Configuration](/blog/tag/configuration)
[ArgoCD](/blog/tag/argocd)
[fluxcd](/blog/tag/fluxcd)
[DevOps](/blog/tag/devops)

## On this page

---

The OpenTelemetry Collector configuration file controls how telemetry flows through your observability pipeline. It defines what data is received, how it is processed, and where it goes. In production environments, a bad configuration change can cause data loss, increased latency, or even collector crashes. Managing this configuration through manual edits and kubectl apply commands is risky and unscalable.

GitOps applies software engineering practices to infrastructure configuration. Every change goes through version control, gets reviewed in a pull request, passes automated validation, and is deployed through a reconciliation loop. This post shows how to implement a full GitOps workflow for OpenTelemetry Collector configurations using tools like ArgoCD or FluxCD.

## Why GitOps for Collector Config

Collector configurations change frequently. Teams add new pipelines, adjust sampling rates, add processors, or change export destinations. Without GitOps, these changes happen in an ad-hoc way. Someone edits a ConfigMap, applies it, and hopes for the best. If something breaks, rolling back means finding the previous version (if anyone saved it) and reapplying.

With GitOps, you get:

* Full audit trail of every configuration change
* Pull request reviews before any change goes live
* Automated validation that catches syntax errors
* One-click rollback to any previous version
* Environment promotion (staging to production)

## Repository Structure

Organize your collector configurations in a Git repository:

```
otel-configs/
  base/
    collector-config.yaml      # Base configuration shared across environments
    kustomization.yaml
  overlays/
    staging/
      collector-patches.yaml   # Staging-specific overrides
      kustomization.yaml
    production/
      collector-patches.yaml   # Production-specific overrides
      kustomization.yaml
  policies/
    validation.rego            # OPA validation policies
  tests/
    config_test.yaml           # Configuration validation tests
  .github/
    workflows/
      validate.yaml            # CI validation pipeline
```

## Base Configuration

The base configuration contains the common setup that applies to all environments:

```
# base/collector-config.yaml
# Base collector configuration. Environment-specific settings
# are applied as Kustomize overlays.
apiVersion: v1
kind: ConfigMap
metadata:
  name: otel-collector-config
  namespace: observability
data:
  config.yaml: |
    receivers:
      otlp:
        protocols:
          grpc:
            endpoint: 0.0.0.0:4317
          http:
            endpoint: 0.0.0.0:4318

    processors:
      memory_limiter:
        check_interval: 1s
        limit_mib: 2048
        spike_limit_mib: 512

      batch:
        timeout: 5s
        send_batch_size: 1024

      resource:
        attributes:
          - key: collector.managed_by
            value: gitops
            action: insert

    exporters:
      otlphttp:
        endpoint: "${OTLP_BACKEND_ENDPOINT}"

    extensions:
      health_check:
        endpoint: 0.0.0.0:13133

    service:
      extensions: [health_check]
      pipelines:
        traces:
          receivers: [otlp]
          processors: [memory_limiter, resource, batch]
          exporters: [otlphttp]
        metrics:
          receivers: [otlp]
          processors: [memory_limiter, batch]
          exporters: [otlphttp]
```

## Environment Overlays

Use Kustomize to apply environment-specific overrides:

```
# overlays/staging/kustomization.yaml
# Staging overlay applies lower resource limits and higher sampling rates.
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base

patches:
  - path: collector-patches.yaml
    target:
      kind: ConfigMap
      name: otel-collector-config
```

```
# overlays/staging/collector-patches.yaml
# Staging-specific configuration overrides.
# Higher sampling rate for better debugging, lower resource limits.
apiVersion: v1
kind: ConfigMap
metadata:
  name: otel-collector-config
data:
  config.yaml: |
    receivers:
      otlp:
        protocols:
          grpc:
            endpoint: 0.0.0.0:4317
          http:
            endpoint: 0.0.0.0:4318

    processors:
      memory_limiter:
        check_interval: 1s
        limit_mib: 1024
        spike_limit_mib: 256

      batch:
        timeout: 5s
        send_batch_size: 512

      resource:
        attributes:
          - key: collector.managed_by
            value: gitops
            action: insert
          - key: deployment.environment
            value: staging
            action: upsert

    exporters:
      otlphttp:
        endpoint: "https://staging-backend.yourdomain.com/otlp"

    extensions:
      health_check:
        endpoint: 0.0.0.0:13133

    service:
      extensions: [health_check]
      pipelines:
        traces:
          receivers: [otlp]
          processors: [memory_limiter, resource, batch]
          exporters: [otlphttp]
```

## CI Validation Pipeline

Every pull request should run validation before the configuration can be merged:

```
# .github/workflows/validate.yaml
# CI pipeline that validates collector configuration on every PR.
name: Validate Collector Config

on:
  pull_request:
    paths:
      - 'base/**'
      - 'overlays/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install OpenTelemetry Collector
        run: |
          # Download the collector binary for validation
          curl -L -o otelcol https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v0.96.0/otelcol-contrib_0.96.0_linux_amd64
          chmod +x otelcol

      - name: Build Kustomize Overlays
        run: |
          # Render each environment overlay
          for env in staging production; do
            echo "Building $env overlay..."
            kustomize build overlays/$env > /tmp/$env-config.yaml
          done

      - name: Validate Configuration Syntax
        run: |
          # Extract the config.yaml from the ConfigMap and validate it
          for env in staging production; do
            echo "Validating $env configuration..."
            # Extract the embedded config.yaml from the ConfigMap
            python3 -c "
            import yaml, sys
            with open('/tmp/$env-config.yaml') as f:
                cm = yaml.safe_load(f)
            config = cm['data']['config.yaml']
            with open('/tmp/$env-otel.yaml', 'w') as f:
                f.write(config)
            "
            # Run the collector's built-in validation
            ./otelcol validate --config /tmp/$env-otel.yaml
          done

      - name: Run OPA Policy Checks
        run: |
          # Validate against organizational policies
          for env in staging production; do
            opa eval -d policies/ -i /tmp/$env-otel.yaml \
              "data.otel.deny" --fail-defined
          done
```

## OPA Validation Policies

Use Open Policy Agent to enforce organizational rules about collector configuration:

```
# policies/validation.rego
# OPA policies for validating collector configurations.
# These prevent common misconfigurations from reaching production.
package otel

# Every pipeline must have a memory limiter processor
deny[msg] {
    pipeline := input.service.pipelines[name]
    not array_contains(pipeline.processors, "memory_limiter")
    msg := sprintf("Pipeline '%s' must include memory_limiter processor", [name])
}

# Batch timeout must be between 1s and 30s
deny[msg] {
    timeout := input.processors.batch.timeout
    not valid_duration(timeout, 1, 30)
    msg := sprintf("Batch timeout '%s' must be between 1s and 30s", [timeout])
}

# Health check extension must be enabled
deny[msg] {
    not array_contains(input.service.extensions, "health_check")
    msg := "health_check extension must be enabled"
}

# OTLP receivers must not use 0.0.0.0 without TLS in production
deny[msg] {
    input.receivers.otlp.protocols.grpc.endpoint == "0.0.0.0:4317"
    not input.receivers.otlp.protocols.grpc.tls
    input.processors.resource.attributes[_].value == "production"
    msg := "OTLP gRPC receiver must use TLS in production"
}

array_contains(arr, elem) {
    arr[_] == elem
}
```

## ArgoCD Application

Deploy the collector configuration using ArgoCD:

```
# ArgoCD Application for the production collector config.
# ArgoCD watches the Git repository and automatically syncs
# changes to the Kubernetes cluster.
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: otel-collector-production
  namespace: argocd
spec:
  project: observability
  source:
    repoURL: https://github.com/mycompany/otel-configs.git
    targetRevision: main
    path: overlays/production
  destination:
    server: https://kubernetes.default.svc
    namespace: observability
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
    retry:
      limit: 3
      backoff:
        duration: 5s
        factor: 2
```

## Configuration Rollback

One of the biggest benefits of GitOps is easy rollback. If a configuration change causes problems, revert the Git commit and ArgoCD automatically deploys the previous version:

```
# Revert the last configuration change.
# ArgoCD detects the new commit and syncs the cluster back
# to the previous configuration.
git revert HEAD
git push origin main

# Or roll back to a specific commit
git revert abc1234
git push origin main
```

For immediate rollback without waiting for the Git pipeline, ArgoCD provides a sync command:

```
# Sync to a specific Git revision immediately
argocd app sync otel-collector-production --revision abc1234
```

## Change Management Workflow

The complete workflow for a configuration change looks like this:

```
graph TD
    A[Developer creates branch] --> B[Edit collector config]
    B --> C[Push and create PR]
    C --> D[CI validates syntax]
    D --> E{Validation passed?}
    E -->|No| F[Fix and push again]
    F --> D
    E -->|Yes| G[OPA policy check]
    G --> H{Policies passed?}
    H -->|No| F
    H -->|Yes| I[Peer review]
    I --> J[Merge to main]
    J --> K[ArgoCD detects change]
    K --> L[Deploy to staging]
    L --> M[Verify in staging]
    M --> N[Promote to production]
    N --> O[ArgoCD syncs production]
```

## Monitoring Configuration Drift

ArgoCD reports when the actual state differs from the desired state in Git. Set up alerts for configuration drift:

```
# Alert when the collector configuration is out of sync.
# This catches manual changes that bypass the GitOps workflow.
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: argocd-otel-drift
spec:
  groups:
    - name: argocd-otel
      rules:
        - alert: OTelCollectorConfigDrift
          expr: argocd_app_info{name="otel-collector-production",sync_status!="Synced"} == 1
          for: 5m
          labels:
            severity: warning
          annotations:
            summary: "OTel Collector config is out of sync with Git"
```

## Conclusion

GitOps transforms collector configuration management from a risky manual process into a safe, auditable, and automated workflow. Every change is tracked in Git, validated in CI, reviewed by peers, and deployed through a reconciliation loop. Rollbacks are as simple as reverting a commit. This approach is especially valuable for production observability infrastructure where a bad configuration can mean losing visibility into your entire system at the worst possible time.

Share this article

![Nawaz Dhandala](https://avatars.githubusercontent.com/nawazdhandala)

### Nawaz Dhandala

Author

@nawazdhandala • Feb 06, 2026 •

Nawaz is building OneUptime with a passion for engineering reliable systems and improving observability.

[GitHub](https://github.com/nawazdhandala)

### Improve this Blog Post

All our blog posts are open source. Found a typo, want to add more detail, or have a better explanation? Anyone can contribute and make this post better for everyone.

[Edit this Post on GitHub](https://github.com/oneuptime/blog/tree/master/posts/2026-02-06-gitops-opentelemetry-collector-configuration)
[Contributing Guidelines](https://github.com/oneuptime/blog)

[Open source](https://github.com/oneuptime/oneuptime)

## OneUptime is the Open-Source Observability Platform

Your complete reliability stack unified: infrastructure monitoring, incident management, status pages, and APM. Open-source and self-hostable.

[Get started for free](/accounts/register)
[Request a demo](/enterprise/demo)

[Status Page

Real-time status updates](/product/status-page)

[Incidents

Detect and resolve fast](/product/incident-management)

[Monitoring

Monitor any resource](/product/monitoring)

[On-Call

Smart alert routing](/product/on-call)

[Logs

Fastest log ingest and search](/product/logs-management)

[Metrics

Performance insights](/product/metrics)

[Traces

End-to-end distributed tracing](/product/traces)

[Exceptions

Catch and fix bugs early](/product/exceptions)

[Workflows

Automate any process](/product/workflows)

[Dashboards

Visualize all your data](/product/dashboards)

[AI Agent

Automatically detect, diagnose, and resolve incidents with AI-powered root cause analysis and code fixes.](/product/ai-agent)

We use cookies to enhance your browsing experience and provide
personalized content. By clicking "Accept," you consent to the use of cookies.

Our product uses both first-party and third-party cookies for session storage and for various other purposes.

Please note that disabling certain cookies may affect the functionality and performance of our product.

For more information about how we handle your data and cookies, please read our Privacy Policy.

By continuing to use our site without changing your cookie settings, you agree to our use of cookies as
described above. See our [terms](/legal/terms) and our [privacy policy](/legal/privacy)

Accept
all
Reject all

## Footer

Open Source Observability

### Build reliable systems with confidence

Join thousands of developers using OneUptime to monitor, debug, and optimize their infrastructure, stack, and apps.

[Read Blog](/blog)
[Star on GitHub](https://github.com/oneuptime/oneuptime)

[![OneUptime](/img/4-gray.svg)](/)

The complete open-source observability platform. Monitor, debug, and improve your entire stack in one place.

[GitHub](https://github.com/oneuptime/oneuptime)
[X](https://x.com/oneuptimehq)
[YouTube](https://www.youtube.com/%40OneUptimeHQ)
[Reddit](https://www.reddit.com/r/oneuptimehq/)
[LinkedIn](https://www.linkedin.com/company/oneuptime)

Trusted by thousands of teams worldwide - from Fortune 500 enterprises to fast-growing startups.

### Products

* [Status Page](/product/status-page)
* [Incidents](/product/incident-management)
* [Monitoring](/product/monitoring)
* [On-Call](/product/on-call)
* [Logs](/product/logs-management)
* [Metrics](/product/metrics)
* [Traces](/product/traces)
* [Exceptions](/product/exceptions)
* [Workflows](/product/workflows)
* [Dashboards](/product/dashboards)
* [AI Agent](/product/ai-agent)

### Solutions

* [Enterprise](/enterprise/overview)
* [Request Demo](/enterprise/demo)
* [Pricing](/pricing)
* [Data Residency](/legal/data-residency)

### Teams

* [DevOps](/solutions/devops)
* [SRE](/solutions/sre)
* [Platform](/solutions/platform)
* [Developers](/solutions/developers)

### Tools

* [MCP Server](/tool/mcp-server)
* [CLI](/tool/cli)

### Resources

* [Documentation](/docs)
* [API Reference](/reference)
* [Blog](/blog)
* [Help & Support](/support)
* [GitHub](https://github.com/oneuptime/oneuptime)
* [Changelog](https://github.com/oneuptime/oneuptime/releases)
* [Open Source Friends](/oss-friends)

### Industries

* [FinTech](/industries/fintech)
* [SaaS](/industries/saas)
* [Healthcare](/industries/healthcare)
* [E-Commerce](/industries/ecommerce)
* [Media](/industries/media)
* [Government](/industries/government)

### Company

* [About Us](/about)
* [Careers](https://github.com/OneUptime/interview)
* [Merch Store](https://shop.oneuptime.com)
* [Contact](/legal/contact)

### Legal

* [Terms of Service](/legal/terms)
* [Privacy Policy](/legal/privacy)
* [SLA](/legal/sla)
* [Legal Center](/legal)

### Compare

* [vs PagerDuty](/compare/pagerduty)
* [vs Statuspage](/compare/statuspage.io)
* [vs Incident.io](/compare/incident.io)
* [vs Pingdom](/compare/pingdom)
* [vs Datadog](/compare/datadog)
* [vs New Relic](/compare/newrelic)
* [vs Better Stack](/compare/better-uptime)
* [vs Uptime Robot](/compare/uptime-robot)
* [vs Checkly](/compare/checkly)
* [vs SigNoz](/compare/signoz)

© 2026 HackerBay, Inc. All rights reserved.

[Open Source](https://github.com/oneuptime/oneuptime)
|
Made with care for developers worldwide

[SOC 2](/legal/soc-2)
[HIPAA](/legal/hipaa)
[GDPR](/legal/gdpr)
[ISO 27001](/legal/iso-27001)
