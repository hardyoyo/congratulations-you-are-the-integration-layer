---
title: "Congratulations, You're the Integration Layer Now"
author: "Hardy Pottinger"
---

# Congratulations, You're the Integration Layer Now

### OpenTelemetry in the UC Stack

### hardy.pottinger@ucsf.edu | UC Tech 2026

[![CC BY 4.0](https://licensebuttons.net/l/by/4.0/88x31.png)](https://creativecommons.org/licenses/by/4.0/)

Note:
Hi. I'm Hardy Pottinger. I work on the Developer Experience team at UCSF.

This talk is not an OpenTelemetry tutorial. I'm going to assume you've heard
the name. Some of you have used it. Some of you are actively avoiding it.
Many of you are about to be responsible for it whether you planned to be or not.

This is the warning I wish someone had given me before I spent a week on a
problem that took one 4am maintenance window to solve — once I understood what was
actually happening.

---

# I Just Wanted One Metric

In Datadog.

Note:
I had one observability task: get GitHub Enterprise metrics into Datadog.

I manage GitHub Enterprise Server at UCSF. We have Datadog. GitHub had
just added OpenTelemetry support. The docs existed. This seemed like a
nice quick maintenance window project. Turn on the thing, look at
the thing, bask in glory.

I was wrong.

---

# OpenTelemetry Isn't New.

I was.

Note:
Most people in this room have heard of OpenTelemetry. Some have experimented
with it. I ignored it because vendors handled observability for me. The agent
took care of it. The integration took care of it. I didn't have to think about
the pipeline.

That was fine. Until it wasn't.

Quick note: everyone in observability calls it OTel. I'll use both
from here on, but please don't zing me on jargon if I say OTel,
because that's what everyone calls it.


---

# What Changed?

The industry.

> "Early mainstream" — Gartner

> "Supported by more than 40 observability vendors" — CAMSS, 2024

Note:
The organizers told us this isn't a scientific conference, we don't need receipts.

But I have receipts. Gartner calls OTel "early mainstream," with 20 to 50 percent
market penetration. The European Commission's CAMSS assessment found OTel
supported by more than 40 observability vendors.

The point is: the industry has changed. This isn't a side project anymore.
This is how it's done.

---

# The Promise of OpenTelemetry

> Instrument once.
>
> Analyze anywhere.

Note:
This is a real achievement. If you've ever had to re-instrument an application
because you switched vendors, you know exactly how painful that used to be.
OpenTelemetry solved that. Instrument once, analyze anywhere. That's worth
celebrating.

But every promise has fine print.

---

# I Thought...

"This is just another integration."

Note:
Point GitHub at Datadog. Configure the collector. Collect metrics. Move on.
I had a Jira ticket, a 4am maintenance window, and what I thought was a
straightforward integration task.

I walked into it completely blind. This confidence was entirely unearned.

---

![GitHub Enterprise metrics in Datadog](images/GHES_metrics_Datadog.png)

Note:
This. That's it. GitHub Enterprise system metrics, flowing into Datadog.
Nothing exotic. Just a dashboard with data in it.

---

# The Metric Never Appeared.

Note:
I configured the integration. I waited. I refreshed. Nothing. Not an error.
Not a partial result. Just nothing. An empty space where metrics should be.

OK. I probably made a typo. I re-read the documentation. I re-configured.
I waited again.

---

# So I Changed the Config.

Nothing happened.

Note:
I changed the configuration. Restarted the collector. Waited. Nothing.
Changed it again. Restarted. Nothing. I went back to the docs. Read them
more carefully. Tried again. Still nothing.

You've probably been here before, right? What are we thinking about now?

---

# Maybe...

I'm Editing The Wrong Config?

Note:
This is the question that changed everything. Not "what's wrong with my
config?" but "am I even editing the right file?"

So, been there, done that. What's a good tool to investigate this?
We have a running Collector, I know how to find the process ID for it.
What can I do with that?

---

# `lsof`

![lsof output — no config files](images/lsof-fail.png)

Note:
I had a hunch: what if there's another config file the Collector
is reading that I don't know about? `lsof -p <pid>` shows every open
file for a process. If my config wasn't the only one, it would show up here.

Except it didn't. lsof showed me sockets, connections, the binary —
but no config files. Go processes read config at startup and close the
file descriptors immediately. The config is in memory by the time you
look. Dead end. Try something else.

---

# Read the Running System

![cat /proc/pid/cmdline reveals both config paths](images/cat-proc-cmdline.png)

Note:
Every Linux process has a /proc entry. /proc/process-id/cmdline contains
the exact command line the process was started with — every flag,
every argument. The `tr` command makes it readable by replacing null
bytes with newlines. This is a good trick, I kinda love it. And look at what we found...

---

# ...Oh, Cursewords.

two config files

![Two config files revealed](images/two-configs-zoom.png)

Note:
Two --config files.

/etc/otelcol-contrib/config.yaml — GitHub's default, shipped with GHES.
/data/user/common/otelcol.yaml — mine. My config.

I had been editing my config the whole time without knowing it was
an *overlay*: there was
a base config underneath it. A config I had never seen. A config that
was quietly overriding everything I thought I was doing.

---

# The Docs Weren't Wrong.

They weren't complete.

Note:
The GitHub docs told me exactly what to put in my config file.
They were accurate. They just didn't mention that the Collector
was already running with a base config underneath mine.

That's not a documentation bug. It's a documentation gap. The
vendor documented their overlay. They didn't document the whole
stack. And if you don't know the stack has layers, the docs
look complete when they aren't.

This isn't even unusual. Web developers expect a config file to be
the whole story. Observability engineers know to ask: what is the
Collector already doing? I just didn't know which mindset I needed yet.

---

# This Is The Design.

<div class="mermaid">
<pre>
%%{init: {'look': 'handDrawn', 'theme': 'neutral'}}%%
graph TD
    A[Collector Distribution Default] --> D[Effective Config]
    B[Vendor Config] --> D
    C[My Overlay] --> D
    D --> E[Running Collector]
</pre>
</div>

Note:
The OTel Collector is built for composability — multiple config files
merged in order, each layer adding or adjusting what gets collected
and where it goes.

The open-source Collector installs a default config at a well-known
path. Vendors like GitHub layer their own config on top. You add your
overlay on top of that. The result is one effective config that nobody
ever wrote in a single file.

That's why editing one layer had no effect. I was touching the top of
a stack I didn't know existed.

---

# The Collector Collects.

Note:
The name is not an accident. The Collector is a well-known pattern
from distributed systems — it finds telemetry from whatever sources
are already running and routes it onward.

On GHES, Prometheus exporters were already there. nginx, sql, haproxy,
process metrics — all running, all generating data. The Collector just
needed to find them and route their output to Datadog. It didn't
install anything. It collected what was already there.

OpenTelemetry isn't wiring up new sensors, it's just putting all
existing sensor data in a nice neat pile for your observability
tooling to find.

---

# Discovering OpenTelemetry

<div style="text-align: left">

Window 1: just configure the thing.

:boom: fail :boom:

Window 2: wait... maybe I'm editing the wrong config?

Oh. It's in layers. :onion:

</div>

Note:
First window: walked in confident. Edited the config, restarted,
waited. Nothing. Walked away at 6am with nothing to show.

Second window: came back with different questions. Used lsof.
Used /proc/cmdline. Found two config files. Handed both to
ChatGPT Enterprise -- here's what I have, here's what I want,
help me figure this out. Mental model shifted mid-process.
Two hours later, metrics were flowing into Datadog.

The week of confusion wasn't wasted. But it was optional.
The system was never broken. I just didn't understand it yet.

---

![GitHub Enterprise metrics in Datadog](images/GHES_metrics_Datadog.png)

Note:
There it is.

---

# The Old World

<div class="mermaid">
<pre>
%%{init: {'look': 'handDrawn', 'theme': 'neutral'}}%%
graph TD
    A[Application] --> B[Vendor Agent]
    B --> C[Vendor Backend]
</pre>
</div>

Note:
This is how it used to work. One vendor owned the whole stack --
the instrumentation, the collection, the storage. You bought in,
they handled it.

Less flexibility. Also less responsibility on your end.

---

# The New World

<div class="mermaid">
<pre>
%%{init: {'look': 'handDrawn', 'theme': 'neutral'}}%%
graph TD
    A[Application] --> B[OpenTelemetry]
    B --> C[Collector]
    C --> D[Backends]
    style C fill:#a8dadc,stroke:#457b9d,color:#000
</pre>
</div>

Note:
Decoupled. Vendor-neutral. Flexible. Swap backends without
re-instrumenting.

But notice what appeared in the middle. A Collector. And somebody
has to own it.

---

# Congratulations.

You're the Integration Layer Now.

Note:
That's the title of this talk. And it's not a complaint. It's not a
criticism of OpenTelemetry. It's a structural fact.

If you want metrics out of your stuff, your vendor is going to
hand you a Collector and expect you to know what to do with it.

It's just the way it is. But here's the other side of that coin:
we don't have to wait for the vendor anymore. Got a clever idea?
Go build it. The vendor handed us a standard protocol and said
"go get 'em, tiger."

That's agency. And responsibility.

---

# We Own...

- Endpoints
- Collectors
- Routing
- Authentication
- Certificates
- Configuration

Note:
Nobody put this list on a roadmap. Nobody had a sprint planning
meeting and said "let's own the telemetry pipeline this quarter."

It just showed up. The vendors shipped OpenTelemetry support and
assumed someone would run with it. That someone is us.

Again: not a complaint. Just a heads up.

---

# Configuring OpenTelemetry

Note:
I said at the beginning that this isn't a tutorial on configuring
OpenTelemetry. And it's not. But I thought I'd at least let you
have a look at the configs so you know what it's like.

----

# GitHub's Config

```yaml [1-17|19-23]
receivers:
  otlp/ghes:
    protocols:
      http:
        endpoint: 127.0.0.1:8018
  prometheus/ghes:
    config:
      scrape_configs:
        - job_name: "victoriametrics"
        - job_name: "node_exporter"
        - job_name: "process_exporter"
        - job_name: "haproxy_exporter"
        - job_name: "redis_exporter"
        - job_name: "mysqld_exporter"
        # ...and 7 more
  chrony:
    endpoint: udp://127.0.0.1:8033

service:
  pipelines:
    metrics/ghes:
      receivers: [otlp/ghes, prometheus/ghes, chrony]
      exporters: [prometheus/ghes, otlphttp/internal]
```

Note:
GitHub ships this. Thirteen Prometheus scrapers already running,
already collecting metrics from every major service on the server.

The pile already existed.

----

# My Overlay

```yaml [1-7|9-14|16-20]
exporters:
  datadog:
    api:
      site: us3.datadoghq.com
      key: <MY_API_KEY>
    host_metadata:
      enabled: true

processors:
  resource/team:
    attributes:
    - key: team
      value: "developerexperience"
      action: upsert

service:
  pipelines:
    metrics:
      receivers: [otlp/ghes, prometheus/ghes]
      exporters: [datadog]
```

Note:
This is all I needed to add. Twenty lines. Point to Datadog,
tag it with my team, wire it into the existing pipeline.

The pile was already there. I just needed to know where to look.

---


---

# Layers :onion:

- OpenTelemetry puts existing sensor data in a pile for your tooling to find.
- Your vendor's config is "the pile"
- The Collector config is layered. Find all the layers first.
- Use `/proc/\<pid\>/cmdline` to see what your Collector is actually reading.
- Hand the vendor config + your goals to an AI agent. Ask for help.

Note:
Your vendor has probably already mentioned OpenTelemetry. Or they're
about to. Think about the metrics you want out of your servers, your
applications. If OTel is how your vendor plans to support getting
those metrics out -- and increasingly, it is -- you've just had a
preview of what that process is going to look like.

Are you ready for it? What questions do you need to ask?

Speaking of questions... do you have any for me?

---

# Questions?

Note:
If you're working through something similar at your campus,
find me afterwards. We're all figuring this out together.

---

# Acknowledgments

**Sources**

- [Gartner Fast Answer: "What should I know about OpenTelemetry?"](https://www.gartner.com/fast-answer/tech/e55c08f1-9b85-3f46-aeba-fac13e6110b3)
- [CAMSS Assessment Summary: OpenTelemetry EIF Scenario v1.0](https://interoperable-europe.ec.europa.eu/collection/common-assessment-method-standards-and-specifications-camss/solution/camss-assessment-opentelemetry-eif-scenario/distribution/camss-assessment-summary-opentelemetry-eif-scenario-v100)

**Slides**

github.com/hardyoyo/congratulations-you-are-the-integration-layer
