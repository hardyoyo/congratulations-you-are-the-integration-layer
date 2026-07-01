# Slide Deck TODO

Progress tracker for the UC Tech 2026 slide notes review session.

## Timing

_45-minute talk. 27 slides. Estimated content: ~35 minutes._

Current slide order:
1. Title
2. I Just Wanted One Metric
3. OpenTelemetry Isn't New.
4. What Changed?
5. The Promise of OpenTelemetry
6. I Thought...
7. [GHES metrics image]
8. The Metric Never Appeared.
9. So I Changed the Config.
10. Maybe... I'm Editing The Wrong Config?
11. `lsof`
12. Read the Running System
13. ...Oh, Cursewords.
14. The Docs Weren't Wrong.
15. This Is The Design.
16. The Collector Collects.
17. Discovering OpenTelemetry
18. [callback image]
19. The Old World
20. The New World
21. Congratulations.
22. We Own...
23. Configuring OpenTelemetry
24. The Recipe
25. What Platform Teams Should Do
26. Layers
27. Questions?

~35 min content + ~10 min Q&A = ~45 min. Probably fine.

**If more time is needed, possible places to expand:**
- "The Recipe" -- walk through actual configs live (adds ~5 min)
- "What Platform Teams Should Do" -- already dense, could slow down
- Screenshots on lsof/cmdline/Oh Cursewords naturally invite audience reaction time

## Still pending

- [ ] Acknowledgments slide -- add once all images are finalized
  - Image credits: GHES_metrics_Datadog.png, lsof-fail.png,
    cat-proc-cmdline.png, two-configs-zoom.png
  - Sources: Gartner Fast Answer, CAMSS Assessment
- [x] Readability review -- full pass on all notes for words that
      trip up when read aloud (known fix: hypothesis -> hunch)
- [x] Sources review -- confirm Gartner and CAMSS citations are
      correct; decide if any other sources deserve a callout

## Optional / nice to have

- [ ] Drop resources/ folder before conference (scraped web content)

## Done

- [x] All 27 slides have readable speaker notes
- [x] Screenshots: lsof-fail.png, cat-proc-cmdline.png,
      two-configs-zoom.png, GHES_metrics_Datadog.png
- [x] Mermaid diagrams: This Is The Design (config merge order), Old World, New World
      (all using div.mermaid/pre format with handDrawn theme)
- [x] GitHub->Datadog pipeline diagram: not needed, story does the work
- [x] Callback image slide added after Discovering OpenTelemetry
- [x] CI passing, pre-commit hooks installed
- [x] Repo public at github.com/hardyoyo/congratulations-you-are-the-integration-layer

## Vertical slide digressions

Reveal.js supports vertical slides for optional deep-dives. Enable in
mkslides.yml with `separator_vertical` and use `----` as the separator.

- [ ] **After "Configuring OpenTelemetry"** -- show the GitHub GHES
      default config and your overlay side by side. Makes the
      abstract "two configs" moment real for anyone who wants to
      see the actual files.

- [ ] **After "Discovering OpenTelemetry"** -- show the actual
      ChatGPT Enterprise prompt. "Here's what I actually asked."
      The 2026 audience will want to see the prompt.
