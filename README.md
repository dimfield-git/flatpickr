<img width="1536" height="1024" alt="ChatGPT Image Mar 2, 2026, 08_29_58 AM" src="https://github.com/user-attachments/assets/086da5bb-643c-4e5e-ada9-ffa0553d2af8" />

**Bluegrass jam workstation — notation, tablature, and chord tools in a single HTML file.**

Flatpickr is a browser-based tool for writing, editing, and practicing bluegrass flatpicking arrangements. It renders standard notation and guitar tablature side-by-side in SVG, with a built-in metronome, chord identifier, and run library. Everything lives in one self-contained HTML file with zero dependencies — just open it in a browser.
**[Live demo →](https://dimfield-git.github.io/flatpickr/)**
## Features

- **Notation + TAB editor** — Click to place notes on a combined treble staff and 6-string TAB. Supports whole, half, quarter, eighth, and sixteenth note durations with pick direction marks (upstroke/downstroke).
- **AABB song structure** — Sections A and B with configurable measure counts, optional count-in bar, and ending variation layers for the classic bluegrass repeat form.
- **Metronome** — WebAudio lookahead scheduler with four click sounds (wood, beep, hi-hat, block) and accent on beat 1. Spacebar toggle.
- **Chord picker** — Interactive 12-fret neck. Click frets to build shapes; the engine guesses the chord name from pitch classes with inversion-aware scoring. Includes a quick-access chord library.
- **Run library** — Pre-loaded G, C, D, A, and E bluegrass runs. Save your own custom runs to localStorage and insert them into any measure with one click.
- **Note selection tray** — Visual sidebar for duration and pick direction selection, with rest and delete buttons. Keyboard shortcuts still work in parallel.
- **Themes** — Four built-in themes: Olive (dark), Moss (dark), Parchment (light), Herb (light).
- **Export / Import** — Save and load songs as JSON files.

## Usage

Open `flatpickr.html` in any modern browser. No build step, no server, no install.

### Keyboard shortcuts

| Key | Action |
|-----|--------|
| `W` `H` `Q` `E` `S` | Set duration (whole → sixteenth) |
| `U` `D` `N` | Pick direction (up / down / none) |
| `0`–`9` | Enter fret number (two digits for 10+) |
| `.` | Insert rest |
| `Backspace` | Delete note at cursor |
| `←` `→` | Move cursor by current duration |
| `↑` `↓` | Move cursor across strings |
| `Space` | Toggle metronome |

### Editing workflow

1. Click a position in a measure's TAB region to place the cursor.
2. Select a duration from the tray or press a duration key.
3. Type a fret number — the note appears on both the staff and TAB.
4. The cursor advances automatically. Use arrow keys to navigate.

### Runs

Click any run in the Run Library panel to overwrite the current measure with that run's events. To save your own: enter notes into a measure, then click **Save bar** and give it a name. Custom runs persist in your browser's localStorage.

## Architecture

Single-file HTML with inlined CSS and JS, organized into numbered sections:

| Section | Responsibility |
|---------|---------------|
| §1 | Layout config — frozen object with computed getters, shared by renderer and click handler |
| §2 | Constants and utilities |
| §3 | Song data model (sections, layers, measures, events) |
| §4 | Editor cursor state |
| §5 | DOM references |
| §6 | Note type tray (visual duration/pick selector) |
| §7 | Theme system |
| §8 | Metronome (WebAudio lookahead scheduler) |
| §9 | Editing operations (upsert, delete, advance) |
| §10 | Keyboard handler |
| §11 | SVG renderer (DOM-based, not string concatenation) |
| §12 | Main render loop (vertical measure stacking) |
| §13–14 | Control wiring, export/import |
| §15 | Chord picker and library |
| §16 | Run library with localStorage persistence |

### Data model

Each note event:

```js
{
  tick: number,          // position in bar (tick = 1/16 note, bar = 64 ticks in 4/4)
  stringIndex: number,   // 0–5 (high e to low E)
  fret: number,
  dur: number,           // duration in ticks (64/32/16/8/4)
  type: "note" | "rest",
  pick: "up" | "down" | "none"
}
```

### Design decisions

- **DOM-based SVG** over string concatenation — elements can be individually targeted and event-bound.
- **Centralized LAYOUT object** — renderer and click handler share the same coordinate constants. No duplication.
- **Envelope-baked noise buffers** for metronome clicks — avoids the gain-scheduling bugs that plague `exponentialRampToValueAtTime` with near-zero start values.
- **Vertical stacking** of measures (not a grid) — matches the linear top-to-bottom reading flow of a lead sheet.

## Development

This is a prototype. Known limitations:

- No key signatures, accidentals, or beaming groups in the notation renderer.
- Pitch-to-staff mapping is chromatic approximation, not diatonic.
- No rhythmic occupancy enforcement (notes can overlap durations).
- No tie/slur rendering.
- Chord naming is heuristic — complex voicings may be misidentified.

When the UX stabilizes, planned next steps include splitting into ES modules, adding a proper engraving pass, and optional VexFlow integration.

## License

MIT
