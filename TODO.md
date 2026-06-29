# Slide Deck TODO

Progress tracker for the UC Tech 2026 slide notes review session.

## Timing

_45-minute talk. Pace accordingly._

| Section | Slides | Est. time |
|---|---|---|
| Setup / "I Just Wanted This" | 1–10 | 10 min |
| Debugging story + `lsof` demo | 11–12 + 3 new | 8 min |
| The realization + Collector Architecture | 13–16 + new | 8 min |
| The framework (Old World → Congrats.) | 17–21 | 5 min |
| Practical advice + GitHub Story | 22–29 | 8 min |
| **Total** | | **~39 min** |

## Structural changes pending

- [ ] Add 3 new slides after "...Oh, Cursewords." (ChatGPT Enterprise moment):
  - "Two Configs." — show what `lsof` revealed: the documented
    config AND the hidden one side by side; the suspicion was right
  - "I Asked for Help." (handed both configs to ChatGPT Enterprise)
  - "Two Hours. One Maintenance Window." (it worked)
- [ ] Add callback slide (repeat "I Just Wanted This." image) after the resolution
- [ ] Add OTel Collector architecture slide after "This Is The Design." —
      receivers → processors → exporters, pipelines, config layering
- [ ] Stage and capture lsof screenshot for slide 11 — run lsof against the
      collector process, capture output showing both config file paths
- [ ] Stage and capture zoom-in of the two config files for slide 12
      ("...Oh, Cursewords.") — make the layering visible
- [ ] Decide fate of "The GitHub Story" slide —
      may be redundant once ChatGPT slides are added

## Technical depth gaps

- [ ] "Reading Collector Configs" notes — each of the 5 questions
      (distribution, defaults, overlay, deployment, runtime) needs a
      paragraph walkthrough so a presenter can explain them credibly
- [ ] "Practical Detective Work" notes — flesh out: `lsof -p <pid>`,
      `/proc/<pid>/cmdline`, comparing documented vs effective config,
      finding overlay files
- [ ] "This Is The Design." notes — go beyond "composability" bullet:
      explain how the Collector assembles a pipeline from multiple
      config files, merge order, environment variable substitution
- [ ] "I Finally Understood" notes — contrast the user's goal (get
      metrics) with OTel's goal (interoperability); explain why the
      Collector's architecture makes sense once you stop treating it
      like a vendor agent
- [x] "What Platform Teams Should Do" — expanded notes with
      ownership decision tree, GitOps workflow, pipeline
      templates, and start-small guidance

## Mermaid diagrams

mkslides renders Mermaid natively. This talk is begging for diagrams
in several places — a well-placed flowchart or sequence diagram would
clarify ideas that take paragraphs to explain in words.

- [ ] **Old World vs. New World (slides 17–18)** — side-by-side
      flowcharts: tight coupling (App → Vendor Agent → Vendor Backend)
      vs. decoupled (App → OTel SDK → Collector → Backends). Makes
      the architectural shift land instantly.
- [ ] **Collector Architecture (new slide after "This Is The Design")**
      — receivers/processors/exporters as a Mermaid flowchart, with
      pipelines connecting them. The natural diagram moment.
- [ ] **Config merge order** — a diagram showing default config →
      base config → overlay config → env var substitution → effective
      config. Visualizes the "which config am I editing?" mystery.
- [ ] **GitHub → Datadog pipeline** — end-to-end: GitHub OTLP endpoint
      → Collector receiver → processor chain → Datadog exporter.
      Makes the abstract pipeline concrete.

## Slides: notes review

- [x] Slide 1: Title — "Congratulations, You're the Integration Layer Now"
- [x] Slide 2: "I Just Wanted One Metric"
- [x] Slide 3: "OpenTelemetry Isn't New."
- [x] Slide 4: "What Changed?"
- [x] Slide 5: "The Promise of OpenTelemetry"
- [x] Slide 6: "I Thought..."
- [x] Slide 7 (NEW): "I Just Wanted This." — image slide added
- [x] Slide 8: "The Metric Never Appeared."
- [x] Slide 9: "So I Changed the Config."
- [x] Slide 10: "Maybe... I'm Editing The Wrong Config?"
- [x] Slide 11: "`lsof`" — done by Big Pickle (notes kept)
      suspicion, not architecture knowledge; key line: "I didn't
      know about OTel layering. I just suspected a second config."

- [ ] NEW: "Two Configs."
- [ ] NEW: "I Asked for Help."
- [ ] NEW: "Two Hours. One Maintenance Window."
- [ ] Slide 13: "The Docs Weren't Wrong."
- [ ] Slide 14: "This Is The Design."
- [ ] NEW: "Collector Architecture" — receivers/pipelines/exporters, config layering
- [ ] Slide 15: "Suddenly... Everything Made Sense."
- [ ] Slide 16: "I Finally Understood"
- [ ] Slide 17: "The Old World"
- [ ] Slide 18: "The New World"
- [ ] Slide 19: "Congratulations."
- [ ] Slide 20: "Suddenly We Owned..."
- [ ] Slide 21: "Configuration Is Architecture"
- [ ] Slide 22: "Reading Collector Configs"
- [ ] Slide 23: "Practical Detective Work"
- [ ] Slide 24: "The GitHub Story" — may need restructuring or removal
- [ ] Slide 25: "What I'd Do Differently"
- [ ] Slide 26: "What Platform Teams Should Do"
- [ ] Slide 27: "The Warning"
- [ ] Slide 28: "Takeaways"
- [ ] Slide 29: "Questions?"

## Thoughts from Big Pickle

**What's working**

The narrative spine is tight — personal debugging story that unfolds
into a broader architectural lesson. The thesis is clear and memorable:
"Congratulations, you're the integration layer now." The tone is
honest — "I was overconfident," "I had a suspicion" — which is the
right energy for a conference talk.

**What still needs work**

The talk has two halves and they're uneven. Slides 1–16 (the story)
are strong. The Mermaid diagrams for Collector architecture and config
merge order will be the payoff this section needs.

Slides 17–29 (the framework) are thinner. "The Old World" / "The New
World" carry a lot of thesis weight across only two slides. "Reading
Collector Configs" and "Practical Detective Work" still read like
presenter prompts rather than teachable material. These slides are
where the audience decides whether the talk was entertaining or useful.

**The timing gap** — the chart says ~39 minutes. The Mermaid diagrams
might add 2-3 minutes. The platform team expansion adds ~2. You'll
probably land at 42-43. A well-placed audience question or a slightly
longer demo could fill it, but there's not much padding.

**Biggest risk** — the GitHub Story slide. If the ChatGPT slides make
it redundant, cut or merge it rather than keep it out of obligation.
A long third act kills conference talks.

## Acknowledgments slide

- [ ] Add Acknowledgments slide at the end once all images are finalized
  - Image credits (at minimum: GHES_metrics_Datadog.png — check if others are added)
  - Sources: Gartner Fast Answer, CAMSS Assessment
  - Any other credits

## Sources

- [ ] Review whether each source has been used enough in the slides

### Sources in resources folder

| File | Source |
|---|---|
| `Gartner-Fast-Answer-...txt` | Gartner Fast Answer: "What should I know about OpenTelemetry?" |
| `CAMSS_Assessment_Summary_...pdf` | CAMSS Assessment: OpenTelemetry EIF Scenario v1.0 |
| `controltheory.com_...md` | Control Theory: OpenTelemetry Collector Deployment Patterns |
| `datadoghq.com_blog_...md` | Datadog Blog: OTel Collector Distributions |
| `logz.io_observability-pulse-2024.md` | Logz.io: Observability Pulse 2024 |
| `oneuptime.com_blog_...md` | OneUptime: GitOps OpenTelemetry Collector Configuration (2026) |
| `opentelemetry-collector-builder-README.md` | OpenTelemetry Collector Builder README |
| `opentelemetry.io_docs_collector_configuration.md` | OpenTelemetry.io: Collector Configuration docs |
| `opentelemetry.io_docs_security_config-best-practices.md` | OpenTelemetry.io: Security Config Best Practices |
| `site24x7.com_blog_...md` | Site24x7: 4 Common OpenTelemetry Challenges |

### Notes

- Gartner and CAMSS are already cited in slide 4 ("What Changed?")
- The rest have informed the talk but are not yet explicitly referenced
- Consider whether any deserve a callout in the Acknowledgments slide
