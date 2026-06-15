# vidconv

Batch-convert video into editing codecs (**DNxHR / ProRes / H.264**) as `.mov`, for NLE
workflows like DaVinci Resolve where HEVC sources don't cut smoothly.

A small, dependency-free Bash wrapper around `ffmpeg`. Hardware-accelerated decode
(NVDEC/CUDA) with automatic software fallback, skips already-converted files, and stops
before it fills your disk. Originals are never modified.

## Why

DaVinci Resolve (free, Linux) can't decode HEVC. Phone footage (e.g. Pixel 4K HEVC) has
to be transcoded to an editing codec first. `vidconv` does that in one command.

## Install

```sh
git clone https://github.com/<you>/vidconv.git
install -Dm755 vidconv/vidconv ~/.local/bin/vidconv   # ensure ~/.local/bin is on PATH
```

Requires `ffmpeg` (with `dnxhd`, `prores_ks`, `libx264` encoders). NVDEC is optional.

## Usage

```sh
vidconv .                          # convert every source in this dir (DNxHR LB)
vidconv *.mp4                       # convert these files
vidconv --codec prores -p hq shoot/
vidconv -o ~/edit --codec h264 -p 18 clip.mp4
vidconv -n .                       # dry-run (show plan, convert nothing)
vidconv -h                         # full help
```

Directories are scanned non-recursively for `.mp4/.mov/.mkv/.m4v`. A source is skipped if
its `.mov` output already exists (override with `-f`).

## Options

| Flag | Description |
|------|-------------|
| `-c, --codec`   | `dnxhr` (default), `prores`, `h264` |
| `-p, --profile` | dnxhr: `lb`(def)/`sq`/`hq` · prores: `proxy`(def)/`lt`/`standard`/`hq` · h264: CRF number (def 18) |
| `-o, --outdir`  | write outputs here (default: alongside each source) |
| `--min-free GB` | stop before free space drops below this (default 25) |
| `--no-hwaccel`  | disable NVDEC/CUDA decode |
| `-f, --force`   | overwrite existing `.mov` |
| `-n, --dry-run` | show what would happen |
| `-h, --help`    | help |

Audio is re-encoded to PCM (DNxHR/ProRes) or AAC (H.264). Non-A/V streams (e.g. phone
motion metadata) are dropped so the `.mov` muxes cleanly.

## License

MIT
