## The Story

I built this because I procrastinate. A lot. Not "occasionally check my phone" 
procrastinate — more like "open YouTube to look up one thing and resurface 
45 minutes later having no memory of how I got to a video about deep sea 
creatures" procrastinate.

So I built something that watches me back.

Desk Buddy Bot runs two detectors in parallel: a computer vision module that 
tracks whether I'm actually looking at my screen, and a window-polling module 
that catches me the second a banned tab (YouTube, Pinterest, Instagram, 
Netflix) becomes active. Get caught by either one for more than a few 
seconds, and it yells at me through a custom audio clip until I get back 
to work.

## Tech Stack

- **Computer vision:** MediaPipe FaceLandmarker (upgraded from an initial 
  Haar cascade prototype — more accurate head/eye tracking, handles gaze 
  direction, not just face position)
- **Window tracking:** `pygetwindow` polling active window titles
- **Audio playback:** a lightweight C++ worker binary calling into 
  Windows' native `MediaPlayer` via PowerShell
- **Voice interaction:** Python `speech_recognition` for spoken commands
- **Threading:** Python's `threading` module runs vision + window tracking 
  concurrently without blocking each other

## What Changed Recently

The original face-detection prototype used a Haar cascade classifier — fast, 
but jittery, and easily fooled by lighting or head angle. It's since been 
replaced with MediaPipe's FaceLandmarker, which adds:

- Actual gaze estimation (iris tracking), not just "is a face roughly 
  centered"
- A 3-second startup calibration so thresholds adapt to your face/webcam 
  setup instead of a hardcoded guess
- Rolling-window smoothing so a single bad frame doesn't trigger a false 
  "you're distracted" alert
- Brightness normalization for low-light reliability