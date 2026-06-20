# PadMe-DS Project Documentation

## Overview

Not all features listed here are available in the demo. 
For play on real hardware.
Tested on R4i SDHC 1.4 with Twilight Menu.

# PadMe-DS

A touchscreen FM chord-pad and groove instrument for the Nintendo DS. Tap the
on-screen pads to play lush two-operator FM chords, drag the XY pads to morph the
sound in real time, and let the built-in bass and sparkle generators fill out an
evolving, atmospheric track. It also works as a deep MIDI and CC controller.

Runs on real DS hardware from a flashcart. MIDI features use
the **mDS slot-2 MIDI cart**.

> A free **demo** version is available. Features that the demo leaves out are
> marked **(full version only)** throughout, and summarised at the bottom.

---

## Features

### Sound engine
- Layered two-operator FM voice with an envelope you draw by hand on screen
  (attack, decay, sustain, release).
- Independent FM feedback for the chord, splatter, and bass, each on its own fader.
- Glide / portamento, overlapping chord tails, and a held-chord auto-modulation
  that slowly brings a sustained chord to life.
- 8-voice polyphony with smart voice stealing so chords and effects layer cleanly.

### Play and perform
- 8 chord pads across **11 curated banks** (jazz, ambient, lofi, neo-soul, house,
  blues, and more), plus key and transpose controls.
  *(The demo is locked to one bank.)*
- Three XY morph pads (chord, splatter, bass), a 3-channel volume mixer, and
  independent hold latches for chord, splatter, and bass.
- A RAND control that musically deviates a couple of chord voices each press.

### Atmospheric generators
- A generative **sub-bass** that follows the chord root, with selectable patterns,
  octave, glide, roll length, and a note-division rate down to one step every two
  bars. *(The demo is locked to one bass pattern.)*
- A beat-synced **splatter** that throws sparkling notes from the current chord,
  with controls for density, wildness, stereo spread, octave shimmer, velocity,
  and length.
- Both lock to an external clock when present and fall back to the internal tempo
  when it stops.

### Visuals
- Four top-screen visualizers (aurora, wave, ripple, spark) plus a metronome.

### Presets and save-state  *(full version only)*
- 32 preset slots plus an automatic last-state, saved to the SD card. Your setup
  survives a power-off and full patches recall instantly.

### MIDI in & out  *(full version only)*
- **Out:** chords, splatter, and bass each send notes on their own **assignable MIDI
  channel**, and any of the three can be switched **off** so you send only what you
  want.
- **In:** set a MIDI **IN** channel and a DAW or keyboard can drive PadMe back —
  incoming notes trigger the chord pads (see the map below) and incoming CC moves the
  matching parameter. Point IN at a different channel than the OUT channels to avoid
  feedback loops.
- **Every parameter is a MIDI CC, both ways.** Move any fader, XY pad, envelope
  handle, toggle, or settings value and it streams out as a Continuous Controller;
  the same CC coming in drives that control — so a whole performance records and
  plays back in a DAW. (A **CC OUT** toggle silences outgoing CC if you only want
  notes/clock.)
- **Two-way clock sync.** Slave PadMe-DS to your DAW or hardware clock, or turn on
  Sync Out to pass an incoming clock through to downstream gear, or to generate
  your own clock and be the master.

**Chord pads over MIDI** — incoming notes on the MIDI IN channel map to the 8 pads:

| Note | Pad | | Note | Pad |
|------|----:|---|------|----:|
| C3 (48)  | 1 | | E3 (52)  | 5 |
| C#3 (49) | 2 | | F3 (53)  | 6 |
| D3 (50)  | 3 | | F#3 (54) | 7 |
| D#3 (51) | 4 | | G3 (55)  | 8 |

Note *numbers* 48–55 are exact; the note *names* assume middle C = C4, so your DAW may
label the octave differently — go by the numbers. A pad plays whatever chord it holds
in the current bank and key, and note-off releases it unless HOLD is latched.

---

## Usage

### Buttons
- `L` / `R` : transpose down / up a semitone
- `X` : cycle the root key
- `SELECT` : open / close the settings menu
- `START` : panic, stop all notes
- `A` : cycle chord bank *(full version only)*
- `Y` : open / close the PRESETS screen *(full version only)*
- `B` : detect / re-probe the mDS MIDI cart *(full version only)*

### Touch (bottom screen)
- **Chord pads** ring the centre panel. Tap to play; drag between pads to retrigger.
- **Three XY pads** across the top morph the chord, splatter, and bass timbres
  (X = FM ratio, Y = FM depth).
- **ADSR plot** sits below the XY pads. Drag the three handles to draw the envelope.
- **Feedback faders** (right of the ADSR) set the FM self-feedback for chord,
  splatter, and bass.
- **Function row:** a 3-fader **volume mixer**, a **MOD / RATE** control, a
  **HOLD** button split into three triangles (chord / splatter / bass, tap a wedge
  to latch that part), and a **RAND / SPL** control.

### Settings menu (`SELECT`)
A scrolling, grouped menu (use the d-pad: up/down to pick, left/right to adjust;
hold to auto-repeat). Sections: CHORD, MODULATION, SPLATTER, BASS, CLOCK / VISUAL,
and MIDI. The chord pads stay live underneath so you can hear changes.

### Presets screen (`Y`)  *(full version only)*
A grid of 32 slots. **Tap** a slot to load its patch; **hold** a slot to save the
current full setup into it. Filled slots are highlighted.

---

## MIDI CC reference  *(full version only)*

All CC messages are sent on the chord MIDI channel (the control channel). Values
are 0 to 127 unless noted.

| CC | Parameter | | CC | Parameter |
|---:|---|---|---:|---|
| 74 | Chord morph X | | 24 | Splatter density |
| 1  | Chord morph Y | | 31 | Splatter wildness |
| 102 | Splatter morph X | | 85 | Splatter velocity deviation |
| 103 | Splatter morph Y | | 25 | Splatter rate |
| 104 | Bass morph X | | 26 | Splatter octave |
| 105 | Bass morph Y | | 27 | Splatter stereo spread |
| 7  | Chord volume | | 28 | Splatter morph deviation |
| 16 | Splatter volume | | 29 | Splatter length |
| 17 | Bass volume | | 30 | Splatter time |
| 18 | Chord feedback | | 86 | Bass pattern |
| 19 | Splatter feedback | | 87 | Bass octave (value + 64) |
| 20 | Bass feedback | | 88 | Bass glide |
| 73 | Attack | | 89 | Bass time |
| 75 | Decay | | 90 | Bass rate |
| 70 | Sustain | | 92 | RAND octave range |
| 72 | Release | | 93 | RAND XY deviation |
| 5  | Glide time | | 94 | RAND voice count |
| 84 | Glide bend | | 96 | BPM (scaled) |
| 91 | Chord tail | | 95 | Visualizer mode |
| 21 | Mod amount | | 109 | RAND (0 off / 127 on) |
| 22 | Mod rate | | 106 | Chord hold (0 / 127) |
| 23 | Mod sync | | 107 | Splatter hold (0 / 127) |
| | | | 108 | Bass hold (0 / 127) |

---

## Demo version

The free demo is a limited build. Compared with the full version, the demo:

- Is locked to a single chord bank (no bank switching).
- Is locked to a single bass pattern.
- Sends and recieves no MIDI, no CC, and no clock.
- Has no presets and no save-state (no recall, no auto-restore).

Everything else, the full FM engine, the XY morph pads, the splatter and bass
generators, the visuals, and the on-screen mixer and controls, is included so you
can get a real feel for the instrument.
