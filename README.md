# PW Automated Annotation System 

**Automated pen-stroke annotation for educational videos** — syncs handwritten
steps, highlights, and diagrams to a teacher's audio narration over a static
question image.

*Built for the Physics Wallah Internship Screening Assignment — Task 01
(2026-27 Intake)*

---

## Demo

**Rendered output:** [PW Annotated Video (Google Drive)](https://drive.google.com/file/d/1rQxESTcIMttNkTj_4Gh-5iCvtqB7XZYi/view?usp=sharing)

The video shows a stylus-style pen tip writing out a distance-formula solution
in sync with narration — underlines appearing on cue, intermediate steps
written line by line, and a checkmark on the final answer.

---

## Problem framing

Educational annotation videos are normally recorded by a teacher writing live
on a tablet while narrating. The goal here is to automate that: given a
**static background image** (the question) and an **audio narration file**
(the spoken solution), produce a video where annotations — pen strokes,
highlights, labels — appear synced to the audio, as if a teacher were writing
them in real time.

At its core, this is an **audio-to-visual synchronization problem**: the
system needs to know *what* is being explained, *when*, and *where* on the
image it relates to — then render that as natural handwriting motion rather
than a static overlay.

---

## Architecture

A fully automated version of this system is designed around six stages:

```
Audio narration                    Background image
(Whisper — word-level                (Vision model —
 timestamps)                          region map)
      |                                     |
      v                                     v
Teaching beats                     Region map
(LLM segments narration            (labelled zones:
 into discrete actions)             equation / diagram / blank space)
      |                                     |
      +------------------+------------------+
                         v
              Cross-modal alignment engine
        (maps each beat to a region + action —
              the core reasoning layer)
                         |
                         v
              Teacher-behavior model
     (emphasis, re-tracing, natural pacing)
                         |
                         v
                 Stroke synthesis
       (time-parameterized pen paths, jitter)
                         |
                         v
                  Render & sync
        (composite overlay + audio, export video)
                         |
                         v
                 Self-check loop
   (re-verify rendered frames against transcript,
              adjust timing if misaligned)
```

**Why this design:** the pipeline deliberately separates *reasoning*
(what to draw, when, where — stages 1–4) from *rendering* (how to draw it —
stages 5–6). This means the timeline logic can be debugged and improved
independently of the animation/rendering code, and the rendering engine can
be swapped without touching the alignment logic.

The **cross-modal alignment engine** is the differentiating piece: it's a
small multimodal reasoning step that decides, for each segment of narration,
which region of the image is being referred to and what action fits it
(write new content / highlight existing content / point to a diagram) —
rather than relying on fixed, pre-computed timing.

### Scope of this submission

Building the full pipeline above end-to-end was not feasible in the assignment
timeframe. What's implemented here is a **proof-of-concept of stages 5 and 6**
— the rendering and sync layer — validated against a manually authored event
timeline (i.e., stages 1–4 were done by hand: listening to the narration and
deciding what/when/where to draw). This was a deliberate choice to first prove
out the harder rendering problem — natural-looking pen animation, precise
audio sync, teacher-like pacing — before investing in automating the timeline
generation itself.

---

## Features

| Feature | Description |
|---|---|
| Speech-synced visuals | Steps, underlines, and formulas timed to narration cues |
| Stylus pen-tip simulation | A moving pen-tip marker tracks the active writing point |
| Handwritten typography | `Gochi Hand` stylus font for a natural handwritten look |
| Highlight & verification marks | Boundary boxes and a checkmark on the final answer |
| Video composition | Frame-by-frame rendering via Pillow + OpenCV, muxed to H.264/AAC MP4 via FFmpeg |

---

## Tech stack

`Python` · `Pillow (PIL)` · `OpenCV` · `FFmpeg`

---

## How to run

1. Open the notebook in Google Colab.
2. Ensure `QuestionPPT.pptx.jpg` and `Audio.mpeg` are present in the working directory.
3. Run all cells — this exports `PW Annotated video.mp4`.

---

## Roadmap — toward full automation

- [ ] Whisper integration for word-level timestamp extraction
- [ ] Vision-model based region parsing of the background image
- [ ] LLM-based segmentation of narration into "teaching beats"
- [ ] Cross-modal alignment engine (beat → region + action)
- [ ] Confidence-scored fallback for low-confidence alignments
- [ ] Self-check loop: re-verify rendered output against transcript and auto-correct timing

---

## Notes

There is no single correct way to solve this task. This submission favors a
transparent, honestly-scoped MVP over an overclaimed "fully automatic" system
— the architecture above reflects the intended full design, and the roadmap
reflects what's left to automate it end-to-end.
