# Wav Watcher

Fast, dependency-free A/B comparison and analysis for WAV files, built for producers and mastering engineers. One HTML file. Runs in any modern browser, fully offline, nothing leaves your machine.

**Live:** open `index.html` — that's it.

## What it does

- **Native WAV decoding** at the file's real sample rate (16/24/32-bit PCM, 32/64-bit float, WAVE_FORMAT_EXTENSIBLE). Other formats fall back to the browser decoder.
- **Sample-synced A/B playback** — switch between tracks mid-playback with no gap; click to seek; drag to select a region; loop it.
- **Loudness match** — levels the louder track to the quieter by integrated LUFS, so you compare tone instead of volume.
- **Metering** (ITU-R BS.1770 / EBU R128): integrated, short-term max and momentary max LUFS, loudness range, true peak (4× oversampled), sample peak, RMS, crest factor, stereo correlation, DC offset, spectral centroid.
- **Tempo estimate** — spectral-flux onset envelope + autocorrelation.
- **Tonal balance** — average level in sub / low / low-mid / mid / high-mid / air.
- **Spectrum** — overlaid long-term average spectra (1/24-octave smoothed) with a B − A difference strip and hover readout.
- **Spectrograms** for both tracks on a log-frequency axis.
- **Zoomable waveform** (wheel to zoom, shift+wheel to pan), peak + RMS envelopes, overlay mode.
- **Stereo** — live goniometer that follows the playhead, plus correlation, width (S−M), mid/side level and L/R balance.
- **Batch mode** — drop a folder of mixes; every file is analyzed against A in one table. Click a row to load it as B (shift-click for A).
- **Export PNG** — the whole comparison (numbers, waveforms, spectrum, spectrograms) as one image.
- **Copy report** — all numbers as a plain-text table for notes or client emails.

Every Δ column is **B − A**: positive means B is louder / brighter / wider.

## Keys

| Key | Action |
|---|---|
| Space | Play / pause |
| A, B, Tab, T | Choose what you hear |
| ← → | Seek 5 s |
| Home | Start (or selection start) |
| Drag on waveform | Select region |
| Wheel / Shift+wheel | Zoom / pan |
| Z | Reset zoom |
| Esc / double-click | Clear selection |

## How it's built

Vanilla JS, Web Audio API, `<canvas>`. No framework, no build step, no network calls (the web font is optional and falls back to system fonts).

- Custom radix-2 FFT (Hann window, scaled so a full-scale sine reads 0 dBFS).
- K-weighting filters derived from the De Man parameterization, so they reproduce the published BS.1770 48 kHz coefficients exactly and remain correct at 44.1/88.2/96 kHz. Verified: a −20 dBFS 997 Hz stereo test tone reads −20.00 LUFS.
- True peak uses windowed-sinc 4× interpolation evaluated only around samples within 3 dB of the peak, which makes it effectively free.
- Long analysis steps yield to the event loop so the UI stays responsive on long files.

## Desktop app

The file is already the app. To ship an `.exe`:

```bash
npx nativefier --name "Wav Watcher" --icon icon.png file://$(pwd)/index.html
```

or wrap it with [Tauri](https://tauri.app) for a ~5 MB binary.

## Roadmap

- Per-section comparison (compare the drop in A to the drop in B)
- Dynamic EQ-match suggestion from the difference curve
- Loudness history (short-term LUFS over time) under the waveform
