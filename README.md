# 40 Hz Flicker

A single web page that flickers the full screen black and white at 40 Hz, with an optional 40 Hz sound.
It runs in any modern browser on macOS, Windows, Linux, Android and iOS. It has no build step and no dependencies.

**Warning:** flashing light can cause seizures in people with photosensitive epilepsy. This is not a medical device.

## Why the refresh rate matters

A screen can change only once per refresh. One 40 Hz cycle is 25 ms, so it must be a whole number of frames,
with at least one light and one dark frame.

| Display refresh | Frames per cycle | Result |
|---|---|---|
| 60 Hz | 1.5 | Not possible (limit is 30 Hz) |
| 80 Hz | 2 | Exact 40 Hz, 50% light |
| 90, 144, 165 Hz | not whole | Not possible |
| 120 Hz | 3 | Exact 40 Hz, 33% or 67% light |
| 160 Hz | 4 | Exact 40 Hz |
| 240 Hz | 6 | Exact 40 Hz |

The app measures the refresh rate when it opens and again just before each session.
It refuses to start when exact 40 Hz is not possible. It does not show an approximation,
because an approximation on a 60 Hz screen gives a strong 20 Hz component.

On a 60 Hz screen, a "40 Hz" pattern is light, dark, light, repeated every 3 frames (50 ms).
That is a 20 Hz flicker. The light output has a 20 Hz part with about twice the amplitude of its 40 Hz part.

When the screen cannot flicker at exactly 40 Hz and a sound is selected, the app offers a **sound-only** session.
The sound is exact on any device.

## How it keeps time

- Each `requestAnimationFrame` callback advances a frame counter by the number of display frames that passed.
  The colour is `counter mod N < lightFrames`. Thus the phase stays locked to the display clock, even after a dropped frame.
- The app checks the frame rate every 120 frames and stops if the rate changes by more than 3%.
- After each session the app shows the measured average frequency and the number of dropped frames.

## Sound

- **Click train:** 1 ms pips of 10 kHz, 40 per second, as used in the MIT GENUS studies.
- **Pulsed tone:** a 400 Hz tone, pulsed 40 times per second.

Each sound is one second of audio with exactly 40 pulses, played in a loop on the audio clock.
The audio clock and the display clock are separate, so the phase between light and sound drifts slowly.
Both stay at 40 Hz.

## Run it

- **This computer:** open `index.html` in Chrome, Edge or Firefox.
  On a MacBook Pro, put the window on the built-in ProMotion display (120 Hz).
- **Phone or tablet:** the page must be served over HTTPS (for example GitHub Pages or Netlify).
  Open the URL, then use "Add to Home Screen" for a full-screen app that also works offline.
- **Local network test:** `python3 -m http.server 8000` in this folder, then open `http://<computer-ip>:8000` on the phone.
  Over plain HTTP the screen can sleep during a session, because Wake Lock needs HTTPS.

Add `?log` to the URL to record the first 2000 frames in `window.__flickerLog` as `[timestamp, frame, white]`.

## Check the light output

The browser reports frame timing only. To confirm the light itself, film the screen with a 240 fps slow-motion camera.
At 120 Hz you see one light frame and two dark frames, repeated every 6 video frames.
