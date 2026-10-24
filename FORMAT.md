# CLARC Format Specification

CLARC is a UTF-8 text format for timestamped lyrics. Version 1 keeps LRC's
familiar `[mm:ss.xx]` timestamps and adds explicit sections for metadata and
lyric events.

## Example

```text
CLARC/1

[metadata]
title: Harbor Lights
artist.1: Avery Example
artist.2: Jordan Sample
writer: Avery Example
album: Demo Sessions
duration: 00:30

[lyrics]
[00:03.00] [v1] Morning opens on the bay
[00:07.50] [v2] Footsteps fade along the shore
[00:12.00] [v1&v2] We watch the lights come on
[har 00:13.00] (Soft harmony)
[00:18.00] Left side [DUO] Right side
```

## File structure

- Files are UTF-8 text.
- The first non-empty line is exactly `CLARC/1`. It identifies the format and
  its syntax version; it is not metadata or lyric content.
- Each file has one `[metadata]` section followed by one `[lyrics]` section.
  Both headers are required, appear in that order, and cannot be repeated.
- Blank lines are ignored.
- Metadata entries occur only in `[metadata]`. Timed lyric and harmony events
  occur only in `[lyrics]`.

## Metadata

- An entry has the form `key: value`. The value is the text after the first
  colon, with surrounding whitespace trimmed. Quotes and punctuation are
  literal; they do not need escaping.
- Recognized keys are `title`, `album`, `duration`, `writer`, and indexed
  `artist.N` keys, where `N` is a positive integer.
- Metadata entries are optional. `duration`, when present, uses `mm:ss`;
  seconds are `00` through `59`, and minutes may be greater than `59`.
- `artist.N` identifies a credited artist. When vocalist markers are used,
  `artist.1` corresponds to `v1`, `artist.2` to `v2`, and so on. Artist entries
  and vocalist markers are optional.
- Unknown metadata keys are allowed and should be preserved by tools that
  read and write CLARC. Metadata keys must not be duplicated.

## Lyric events

- A lyric event has the form `[mm:ss.xx] lyric text`. `mm` is a non-negative
  minute count, `ss` is `00` through `59`, and `xx` is `00` through `99`
  hundredths of a second. Minutes have at least two digits.
- A line may optionally have a vocalist marker immediately after its
  timestamp, separated by whitespace: `[mm:ss.xx] [v1] lyric text`.
  Multiple vocalist IDs may be joined with `&`, as in `[v1&v2]`. Each ID must
  correspond to an `artist.N` entry. Markers are attribution data and are not
  displayed as lyric text.
- A harmony event has the form `[har mm:ss.xx] lyric text`. It marks a timed
  backing-vocal or harmony part and uses the same timestamp precision and
  range as lyric events.
- Lyric and harmony events must appear in non-decreasing timestamp order.
  Equal timestamps are allowed for simultaneous events.
- Event text is preserved as written, including Unicode and punctuation.
  Empty event text is allowed.

## Layout marker

`[DUO]` divides one lyric line into two display parts: text before the marker
is left-aligned and text after it is right-aligned. It is a layout marker, not
a singer attribution. A lyric line may contain at most one `[DUO]`.

## Ordering example

This sequence is invalid because the timestamps go backward:

```text
[00:10.00] Later line
[00:08.00] Earlier line
```

List all timed events chronologically instead:

```text
[00:08.00] Earlier line
[har 00:09.00] Harmony
[00:10.00] Later line
```

## Scope of version 1

CLARC 1 defines line-level timing, metadata, optional vocalist attribution,
harmony events, and the `[DUO]` layout marker. It does not define word-level
timing, translations, styling, or playback synchronization beyond event
timestamps.

For conversion instructions, see [MIGRATING_FROM_LRC.md](MIGRATING_FROM_LRC.md).
