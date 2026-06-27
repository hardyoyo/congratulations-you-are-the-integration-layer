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
- [ ] Add lsof demo or screen recording or screencap alongside "`lsof`" slide —
      show the actual output, the second config path, make it concrete
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
- [ ] Slide 11: "`lsof`" — notes now frame it as debugging
      suspicion, not architecture knowledge; key line: "I didn't
      know about OTel layering. I just suspected a second config."
- [ ] Slide 12: "...Oh, Cursewords."
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
