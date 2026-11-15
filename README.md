# Beethoven — Symphony No. 9 in D minor, Op. 125 ("Choral") Interactive Atlas

Bilingual (中文 / English) interactive score-aligned listening atlas for **Ludwig van Beethoven — Symphony No. 9 in D minor, Op. 125**, synchronized to the October 1962 Deutsche Grammophon studio recording:

- **Recording**: Herbert von Karajan · Berliner Philharmoniker · Gundula Janowitz (Soprano), Hilde Rössel-Majdan (Alto), Waldemar Kmentt (Tenor), Walter Berry (Bass) · Wiener Singverein (DG 1962/1963)
- **Full Score Reference**: Breitkopf & Härtel *Serie 1 Nr. 9* (276 pages, IMSLP516486)
- **Digital Score Corpus**: OpenScore Orchestra CC0 MusicXML (`tools/scores/movement-{1..4}.mxl`) transposed to concert sounding pitch

---

## Movement Alignment & Structure Summary

| Movement | Tempo & Key | Breitkopf PDF Pages | XML Measures | Aligned Timeline Bars | Audio Duration | Music Window (`start` .. `end`) |
| --- | --- | --- | --- | --- | --- | --- |
| **I** | *Allegro ma non troppo, un poco maestoso* (D minor, 2/4) | pp. 3–70 | 547 | 547 | 926.51 s | `2.76s` .. `925.47s` |
| **II** | *Molto vivace — Presto* (D minor / D major, 3/4 & 2/2) | pp. 71–143 | 575 | **1,176** (`S1=T,S2=F,T1=T,T2=T,DC=no`) | 658.02 s | `1.02s` .. `657.97s` |
| **III** | *Adagio molto e cantabile* (B♭ major, 4/4 & 3/4) | pp. 144–172 | 157 | 157 | 985.00 s | `1.84s` .. `984.95s` |
| **IV** | *Presto — Allegro assai ("Ode to Joy")* (D minor → D major) | pp. 174–276 | 943 | 941 (`mm. 0–940`) | 1,437.52 s | `1.04s` .. `1,434.69s` |

### Corpus Statistics
- **Stages**: **56** synchronized dramatic landmarks across **4 Acts** per movement (`14 + 14 + 12 + 16`)
- **Bar-Indexed Cues**: **104** timed sub-events (`26 + 26 + 19 + 33`)
- **Recurring Motifs**: **16** leitmotifs (`4` per movement, every motif grounded across `>= 3` stages)
- **Deep Insights**: **14** analytical notes (`3 + 3 + 3 + 5`), including **bidirectional cross-movement recitative jump links** connecting the Finale's instrumental rejections (`mm. 30–72`) directly back to Movements I, II, and III
- **Piano Roll & Concert-Pitch MIDI Excerpts**: **16** multi-track Standard MIDI Type-1 files (`assets/m{1..4}-excerpt{1..4}.mid`, **2,606** tie-merged concert-pitch notes)

---

## Key Technical Highlights

### 1. Concert-Pitch Transposition & Movement II Repeat Ablation (`tools/score_pipeline.py`)
- All `.mxl` XML scores are parsed with each part's `<transpose>` chromatic/octave offsets applied so transposing winds and brass (`D Horn`, `B♭ (alto) Horn`, `B♭ (basso) Horn`, `E♭ Horn`, `D Trumpet`, `B♭ Trumpet`, `B♭ Clarinet`, `Piccolo`, `Contrabassoon`, `Contrabass`) emit true sounding concert MIDI pitches.
- Movement II (`575` written measures) was tested against 9 candidate repeat unrollings against Karajan's `658.02s` CQT chroma/energy/onset acoustic profile (`audio2_20hz.npz`), confirming **Exposition repeated (`S1=T`), Part II not repeated (`S2=F`), both Trio halves repeated (`T1=T, T2=T`), and direct Da Capo to Coda (`DC=no`)** across **1,176 unrolled measures** (`DTW_Cost=0.6735`, `Mean_Cos=0.7544`, `RMS_r=0.6111`).
- Movement II and IV downbeats were locked at `1.02s` (F-octave thunderclap) and `1.04s` (*Schreckensfanfare* B♭ upbeat attack) after clearing ~1 s of pre-downbeat room silence; Movement I begins in the `2.76s` *pp* empty-fifth mist and Movement III at `1.84s`.

### 2. Six-Choir Orchestra + Vocal Radar (`voices`)
Every movement exposes all 6 canonical orchestral choirs in strict lockstep with `app.js`:
1. `strings` — Strings
2. `winds` — Woodwinds (Flutes, Piccolo, Oboes, Clarinets, Bassoons, Contrabassoon)
3. `horns` — Horns (4)
4. `brass` — Trumpets / Trombones
5. `perc` — Timpani / Turkish Percussion (Triangle, Cymbals, Bass Drum)
6. `voices` — Soloists & Chorus (`Soprano/Alto/Tenor/Bass Solo` & `SATB Chorus`)

String, bassoon, trombone, and drum parts with "Bass" in their names (`Contrabass`, `Bassoon`, `Contrabassoon`, `Bass Trombone`, `Bass Drum`) are strictly isolated from `'voices'`. Vocal radar stays empty throughout Movements I–III and Finale `mm. 0–215`, lighting up at `m. 216` (*"O Freunde, nicht diese Töne!"* Bass Solo) and expanding across Stanza I (`m. 241`), Choral response (`m. 257`), Solo Quartet (`m. 269`), full SATB (`m. 285` / `m. 313`), and the Choral Double Fugue (`m. 655`).

### 3. Cross-Movement Finale Recitative Links (`tools/build_atlas.py`)
`build_atlas.py` constructs all 56 stage timestamps across all 4 movements before hydrating `insights[].links[].time`, ensuring every cross-movement button inside Movement I (`m1-b1 -> m4-b30`), Movement III (`m3-b1 -> m4-b63`), and Movement IV (`m4-b30 -> m1-b1 / m2-b1`, `m4-b63 -> m3-b1`) jumps accurately between movements and timestamps.

---

## Rebuilding & Verification

```bash
# 1. Re-run score parsing & 20 Hz/50 Hz DTW alignment (optional if score-cache/*.json exists)
/Users/jack/miniforge/bin/python3 tools/score_pipeline.py --skip-m2-ablation

# 2. Build data.js, tools/sources.json, and all 16 SMF Type-1 MIDI excerpts
/Users/jack/miniforge/bin/python3 tools/build_atlas.py

# 3. Run the 5-Gate automated QA verifier (includes Apple JavaScriptCore jsc check)
/Users/jack/miniforge/bin/python3 tools/verify_atlas.py
```

## Serving Locally

Start the HTTP Range-capable static server:

```bash
python3 serve.py
```

Then open `http://localhost:8000/` (or the port printed by `serve.py`) in your browser.
