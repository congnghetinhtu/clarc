# CLARC

CLARC is a readable text format for timestamped lyrics, designed to keep
LRC-style timestamps while clearly separating song metadata from lyric events.

## Example

```text
CLARC/1

[metadata]
title: Harbor Lights
artist.1: Avery Example
artist.2: Jordan Sample
duration: 00:30

[lyrics]
[00:03.00] [v1] Morning opens on the bay
[00:07.50] [v2] Footsteps fade along the shore
[00:12.00] [v1&v2] We watch the lights come on
[har 00:13.00] (Soft harmony)
```

## Documentation

- [Format specification](FORMAT.md)
- [Migrating from LRC](MIGRATING_FROM_LRC.md)
- [Blank template](defaultFormat.clarc)
- [Synthetic sample file](examples/duet.clarc)

This repository publishes the format and documentation. It does not currently
include a parser, validator, or playback implementation.

## License

Documentation, the format specification, and synthetic examples are licensed
under the [MIT License](LICENSE). Any future software implementation should
include its own license and notices.
