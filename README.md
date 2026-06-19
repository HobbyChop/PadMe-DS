# PadMe-DS Project Documentation

## Overview

Not all features listed here are available in the demo. 

PadMe-DS is a Nintendo DS touchscreen FM chord-pad instrument: a central
Kaoss-style X/Y morph pad with a live, touch-editable ADSR envelope beneath it,
ringed by chord trigger pads and a function row. It plays a layered 2-operator
FM voice on the DS's own sound hardware **and** acts as a MIDI controller,
sending its notes and morph CCs out through the **mDS slot-2 cart** (see the
companion `synth-cart` project) to external gear. The bottom screen is drawn
with real graphics; the top screen is a status console with a live metronome.

## Core features

- Central touchscreen **XY pad** (top half of the centre panel) for live FM
  timbral morphing, with a bright cross-hair cursor.
- A **touch-editable ADSR envelope** (bottom half of the centre panel) -- drag
  its break-points to reshape attack/decay/sustain/release.
- 8 surrounding **chord pads** on a clean 4-column grid with pixel-centred
  labels; **11 chord banks** (Classic, Ambient, Diatonic, Pop, Lofi, NeoSoul,
  Minor, Blues, House, Quartal, Exotic).
- **Function row:** a 3-fader **volume mixer** (chord / splatter / bass), a split
  **MOD / RATE** control, a 3-way **HOLD** (chord / splatter / bass), and a split
  **RAND / SPL** control.
- **Three FM-feedback faders** beside the ADSR plot -- independent self-feedback
  for the chord, splatter and bass timbres (colour-coded to match the XY pads).
- Layered 2-op FM voices with an ADSR envelope, **per-source FM feedback**,
  **glide / portamento**, and overlapping **chord tails** (a chord rings on).
- **Held-chord auto-modulation:** a sustained, untouched chord slowly comes alive
  as an LFO fades into the FM depth/ratio (MOD slider sets depth; affects only the
  chord, not the splatter/bass).
- **BPM splatter:** a generative sparkle that throws single notes from the last
  chord in time with the beat, with a full FX section. It's beat-weighted (more
  likely and accented on the beat) so it grooves rather than scatters evenly.
- **Atmospheric sub-bass generator:** a rolling, gliding sub that follows the
  chord root, with selectable patterns, octave, roll length and a note-division
  rate (down to one step per 2 bars). Its pattern restarts from the root on each
  chord press, so the groove repeats predictably instead of drifting mid-phrase.
- A **metronome** beat indicator on the top screen, and **mDS MIDI out** with the
  cart's clock/BPM/transport mirrored. Chords, splatter and bass each have an
  **assignable MIDI channel** (default 1 / 2 / 3) or can be set to **Off** to
  mute that stream, in the MIDI settings. The bass note is sent an octave below
  the DS so it sits as a sub (see `BASS_MIDI_OCT`). The splatter and bass
  **auto-lock** to the cart clock whenever it's advancing and **fall back to the
  internal BPM** when it stalls -- no toggle needed.
- **MIDI clock out** (`SYNC OUT`): when slaved it passes the incoming cart clock
  (and transport) through to downstream gear; otherwise it generates its own
  24-PPQ clock at the internal BPM, so the DS can be the master.
- **Every option is also a MIDI CC** on the chord channel: moving any fader, XY
  pad, ADSR handle, toggle or settings value sends its CC, so a DAW can record and
  automate the whole synth (chord pads stay note-controlled). CC map is in main.c.

## Controls

### Touch (bottom screen)

- **XY pads (top centre):** three side-by-side pads morph the chord, splatter and
  bass FM timbres (X = ratio, Y = depth). The chord pad also sends CC74/CC1.
- **ADSR plot (bottom-left of the centre panel):** drag the three handles (attack
  peak, decay/sustain knee, release end) to draw the envelope.
- **Feedback faders (right of the ADSR):** three thin vertical faders set the FM
  self-feedback for chord / splatter / bass. The one being dragged is highlighted.
- **Chord pads:** touch to play; drag between pads to re-trigger.
- **Function row:** **volume faders** (three vertical sliders -- chord / splatter
  / bass, one drag at a time, the active one highlighted); **MOD / RATE** (split
  slider -- modulation amount over its LFO rate); **HOLD** (three triangles --
  tap the left/right/bottom wedge to latch chord / splatter / bass independently,
  each lit in its colour); **RAND / SPL** (RAND toggle over the splatter density).
  Bank switching is on the `A` button.

### Buttons

- `L` / `R`: transpose down / up a semitone
- `A`: cycle chord bank
- `X`: cycle root key
- `SELECT`: open / close the settings menu
- `START`: stop all notes (panic -- kills chord, bass and splatter + MIDI off)
- `Y`: open / close the PRESETS screen
- `B`: detect / re-probe the mDS cart

### Presets / save-state

`Y` opens the **PRESETS** screen (bottom): 32 slots in an 8x4 grid. **Tap** a slot
to load its patch; **hold** (~0.5 s) to save the current full state into it.
Filled slots are marked. Everything the synth exposes -- bank/key/transpose, the
three XY morphs, the volume mixer, feedback, ADSR, glide, MOD, the whole splatter
and bass sections, RAND tuning, BPM and the visualization -- is captured.

State is stored on the **SD card** via libfat (a single `padme-ds.dat`), so
presets survive a power-off; without an SD/DLDI it falls back to session-only RAM
(the screen footer says which). The current setup is also **auto-saved** as a
"last state" and **restored on boot**, so you power on right where you left off.

### Settings menu (START)

A top-screen menu navigated with the d-pad (up/down pick a row -- hold to
auto-repeat, left/right adjust). The chord pads stay live underneath so you can
hear changes. Entries are grouped (a dim header marks each section).

| Setting | Range | What it does |
|---|---|---|
| *CHORD* | | |
| CHORD VOL   | 0-127 | max chord volume (envelope attack peak) |
| OCT RANGE   | 1-3   | max octave shift RAND applies |
| TAIL        | 0-127 | chord release length (rings through new chords) |
| GLIDE       | 0-127 | portamento time (0 = off) |
| GLIDE BEND  | 0-24  | semitone scoop for notes with no previous pitch |
| XY DEVIATE  | 0-127 | how far RAND nudges the FM morph |
| RAND VOICES | 0-4   | how many chord voices RAND deviates |
| *MODULATION* | | |
| MOD RATE    | 0-127 | auto-mod LFO rate (when MOD SYNC = Free) |
| MOD SYNC    | Free..1/16 | lock the LFO to a clock division |
| *SPLATTER* | | |
| SPLATTER    | 0-127 | sparkle density (0 = off) |
| SPL VOL     | 0-127 | sparkle volume |
| SPL VEL     | 0-127 | random per-sparkle velocity (volume) deviation |
| SPL WILD    | 0-127 | ordered arpeggio .. fully random scatter |
| SPL RATE    | 1-8   | sparkles per beat (subdivision) |
| SPL OCT     | -1..3 | octave shimmer (-1 = down, up to +3 up) |
| SPL SPACE   | 0-127 | stereo spread of random L/C/R landing |
| SPL XY      | 0-127 | random FM-morph deviation per sparkle |
| SPL LEN     | 0-127 | sparkle length (short blip .. ringing) |
| SPL TIME    | 0-127 | how long the splatter runs after a chord, then ends |
| *BASS* | | |
| BASS        | 0-127 | sub-bass volume (0 = off) |
| BASS PAT    | Roll..Amen | rolling pattern (10 atmospheric DnB patterns) |
| BASS OCT    | -4..2 | octave offset vs the chord root (- = down, + = up) |
| BASS GLIDE  | 0-127 | sub portamento time |
| BASS TIME   | 0-127 | how long the bass rolls after a chord, then ends |
| BASS RATE   | 2 bar..1/16 | bass step division (slow end is for atmospheric sub) |
| *CLOCK / VISUAL* | | |
| BPM         | 40-240| internal tempo (used when not cart-synced) |
| VIZ         | Off..Spark | top-screen visualization style |
| *MIDI* | | |
| MIDI CHD    | Off/1-16 | chord MIDI-out channel (Off = don't send) |
| MIDI SPL    | Off/1-16 | splatter MIDI-out channel |
| MIDI BAS    | Off/1-16 | bass MIDI-out channel |
| SYNC OUT    | on/off | send MIDI clock out (pass-through when slaved, else internal) |

## Implementation details

### UI (`ui.c`, `gfx.c`, `layout.c`)

- **Top screen** (main engine): a colour status console -- key, bank, transpose,
  XY, HOLD/MOD, a **metronome** (BPM + a 4-step pulsing beat indicator) and the
  mDS link. It homes and overwrites in place (never a full clear), so frequent
  updates during a drag or on the beat don't flicker. Holding START swaps it for
  the settings menu, whose entries are grouped into labelled sections.
- **Bottom screen** (sub engine): a **BG3 4bpp tile-canvas** (`gfx.c`) used as a
  256x192 framebuffer for the rounded panels, XY cursor and ADSR curve, behind a
  blank BG0 console. Labels are drawn by **blitting the console font's glyphs**
  straight into the canvas, so text is proportionally spaced and pixel-centred.
- **`layout.c`** is the single source of truth for every rectangle; the renderer
  and the touch hit-testing both use it, so drawn shapes and touch zones match.

### Audio engine (`audio.c`)

- A voice is a 64-sample wavetable holding one cycle of a 2-op FM wave (with
  modulator self-**feedback**), played looped at `note_freq * 64`. Rendering uses
  a 256-entry sine LUT (integer math).
- Voices share two morphable wavetables (main + detuned layer); an XY morph or a
  feedback change re-renders them in place -- sounding voices change timbre with
  no click. The **splatter** has its own wavetable so its random per-sparkle
  morph doesn't disturb the chord.
- Up to **8 voices** with a per-voice **ADSR** envelope (driven from
  `audio_update()` via `soundSetVolume`), **glide** (per-voice pitch ramp toward
  the target note via `soundSetFreq`, sourced from the previous chord), and a
  voice allocator that releases old voices and steals the quietest one -- so
  **chord tails overlap** new chords and sparkles layer in.
- **Auto-modulation** fades an LFO into the FM depth/ratio after a chord is held
  and untouched; **splatter** throws short "pluck" sparkle voices on the beat.

### Chord system (`chords.c`)

- 8 chord pads, **11 banks** of curated chord sets. Each pad maps to a chord
  definition (root offset + interval set) and emits **absolute MIDI notes**
  including a base octave. Cycle root key, transpose +/-24 semitones. RAND
  deviates a couple of voices by an octave (tuned in settings).

## What is still TODO

- A full 4-/6-operator FM engine (currently 2-op with feedback).
- Named presets / text entry (slots are numbered 1-8 for now).
- Inbound MIDI from the cart driving the local voices (the driver is OUT-only).
  Chord (ch 1), splatter (ch 2) and bass (ch 3) all send MIDI out.
- A scrolling/paged settings menu (the list is getting long).
- On-hardware audio tuning (envelope/LFO/glide feel, mixer levels).
