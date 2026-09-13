# Whiteboard Video Annotator (Task 1)

Generates a synchronized whiteboard-style solution video from a static question
image and a teacher's audio narration — pen-stroke handwriting, highlights, and
checkmarks timed to match the narration.

## 📹 Live Demo
- **Watch Rendered Video:** [PW Annotated Video (Google Drive)](https://drive.google.com/file/d/1rQxESTcIMttNkTj_4Gh-5iCvtqB7XZYi/view?usp=sharing)

## Approach

The full problem is really an audio-to-visual synchronization task: figure out
*what* the teacher is referring to, *when*, and render that as natural pen
movement instead of a static overlay.

A complete automated pipeline for this would look like:
`audio → word-level ASR timestamps` + `image → region map (vision model)`
→ `LLM segments narration into "teaching beats"` → `cross-modal alignment
engine (maps each beat to a region + action)` → `stroke synthesis` → `render + sync`.

**This submission is a proof-of-concept / MVP** of that pipeline: the event
timeline (what gets written/highlighted, when, and where) was scripted manually
after listening to the narration, to first validate the rendering and sync
approach — pen-stroke animation, timing accuracy, and natural stylus feel.
Automating the timeline generation (ASR + region parsing + LLM alignment) is
the next step, not yet implemented here.

## ⚡ Features
- **Speech-synced visuals:** steps, underlines, and formulas timed to narration.
- **Teacher stylus simulation:** a moving pen-tip dot tracks active writing.
- **Handwritten look:** stylus font (`Gochi Hand`), highlight boxes, checkmark.
- **Rendering:** frame composition via Pillow + OpenCV, muxed into H.264/AAC MP4 via FFmpeg.

## 🛠️ Tech Stack
- Python, Pillow (PIL), OpenCV, FFmpeg

## 🏃 How to Run
1. Open the notebook in Google Colab.
2. Ensure `QuestionPPT.pptx.jpg` and `Audio.mpeg` are in the working directory.
3. Run all cells to export `PW_Pen_Stroke_Solution.mp4`.

## Next steps (full automation)
- Whisper for word-level timestamps instead of manual timing
- Vision-model region parsing of the background image
- LLM-based alignment between narration beats and image regions
- Confidence-based fallback for low-confidence matches
- Self-check loop: re-verify rendered frames against transcript, adjust timing
