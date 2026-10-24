# Migrating from LRC to CLARC

CLARC preserves LRC's `[mm:ss.xx]` lyric timestamps. A basic conversion moves
song tags into `[metadata]` and timestamped lyric lines into `[lyrics]`.

## Basic conversion

1. Start the file with `CLARC/1`.
2. Add a `[metadata]` section and convert useful LRC tags to `key: value`
   entries.
3. Add a `[lyrics]` section and copy the timed lines, preserving each
   timestamp and its lyric text.
4. Put all lyric and harmony events in non-decreasing timestamp order.
5. Add vocalist markers or harmony events only when that information is known.

### Example

LRC:

```text
[ti:Harbor Lights]
[ar:Avery Example & Jordan Sample]
[al:Demo Sessions]
[length:0:30]

[00:03.00]Morning opens on the bay
[00:07.50]Footsteps fade along the shore
```

CLARC:

```text
CLARC/1

[metadata]
title: Harbor Lights
artist.1: Avery Example
artist.2: Jordan Sample
album: Demo Sessions
duration: 00:30

[lyrics]
[00:03.00] Morning opens on the bay
[00:07.50] Footsteps fade along the shore
```

## Tag mapping

| LRC tag | CLARC metadata | Guidance |
| --- | --- | --- |
| `[ti:...]` | `title: ...` | Copy the value after the tag. |
| `[ar:...]` | `artist.N: ...` | Use separate indexed entries when the individual artists are known. Otherwise preserve the full credit as `artist.1`; do not guess how to split it. |
| `[al:...]` | `album: ...` | Quotes and punctuation are literal; do not escape them. |
| `[length:m:ss]` | `duration: mm:ss` | Zero-pad the minute field to at least two digits and seconds to two digits. |
| `[by:...]` | `by: ...` | This identifies the LRC file creator; do not treat it as the songwriter. |
| Other descriptive tags | Same key in `[metadata]` | Preserve useful values as plain text. |

LRC `[offset:...]` affects timestamp interpretation. CLARC 1 does not define
an offset field. Either apply the offset to event timestamps according to the
source LRC's convention and verify against the audio, or preserve it as an
unknown metadata entry such as `lrc-offset: 500`. Do not silently discard it.

## Timestamps and attribution

LRC timestamps already use CLARC's timestamp notation; retain them. Add a
space after the closing bracket for readability. If an LRC line has multiple
timestamps for the same text, create one CLARC event per timestamp.

Vocalist markers are optional. Add them only when the singer is known and
matches an indexed artist entry:

```text
[00:03.00] [v1] Morning opens on the bay
[00:07.50] [v2] Footsteps fade along the shore
[00:12.00] [v1&v2] We watch the lights come on
```

Here, `v1` maps to `artist.1` and `v2` maps to `artist.2`. Do not infer who
sings a line from the artist credits alone.

CLARC harmony events use `[har mm:ss.xx]`; add them only when backing-vocal
timing is known. `[DUO]` can mark left/right layout for a long line. Neither
feature is required for a basic conversion.

## Review checklist

- `[metadata]` and `[lyrics]` are each present once and in that order.
- Every timestamp remains attached to the correct lyric.
- Lyric and harmony events are listed in non-decreasing timestamp order.
- Lyric spelling, punctuation, and Unicode are preserved.
- No singer, harmony timing, or layout has been guessed.
- Any source LRC offset is applied and checked or explicitly preserved.
