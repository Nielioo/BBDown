# BBDown

A command-line Bilibili downloader.

> This project is for personal learning, research, and non-commercial use only.  
> You are responsible for complying with local laws and copyright regulations.  
> The developer is not liable for any legal issues caused by misuse.

## Important

For muxing (merging audio/video/subtitle streams), BBDown needs external tools:

- Normal videos: [ffmpeg](https://www.gyan.dev/ffmpeg/builds/) or [mp4box](https://gpac.wp.imt.fr/downloads/)
- Dolby Vision: ffmpeg 5.0+ or a newer mp4box

## Quick Start

BBDown is published as a [Dotnet Tool](https://www.nuget.org/packages/BBDown/).

Install:

```bash
dotnet tool install --global BBDown
```

Update:

```bash
dotnet tool update --global BBDown
```

## Download

- Stable releases: <https://github.com/nilaoda/BBDown/releases>
- Auto-built testing versions: <https://github.com/nilaoda/BBDown/actions>

## Usage

```text
Description:
  BBDown is a free and efficient Bilibili download/parser tool.

Usage:
  BBDown <url> [command] [options]

Arguments:
  <url>  Video URL or av|bv|BV|ep|ss

Commands:
  login    Scan QR with APP to log in WEB account
  logintv  Scan QR with APP to log in TV account
  serve    Run in server mode
```

### Common Options

- `-tv, --use-tv-api` use TV API mode
- `-app, --use-app-api` use APP API mode
- `-intl, --use-intl-api` use international API mode
- `-info, --only-show-info` parse only, do not download
- `--show-all` show all multi-part titles
- `-ia, --interactive` interactive stream/quality selection
- `-hs, --hide-streams` hide full stream list
- `--video-only` download video only
- `--audio-only` download audio only
- `--danmaku-only` download danmaku only
- `--sub-only` download subtitles only
- `--cover-only` download cover only
- `--skip-mux` skip mux step
- `--skip-subtitle` skip subtitle download
- `--skip-cover` skip cover download
- `--debug` output debug logs
- `-p, --select-page` select part(s), e.g. `8`, `1,2`, `3-5`, `ALL`, `LAST`, `LATEST`
- `-c, --cookie` cookie string (for WEB premium/private content)
- `-token, --access-token` token for TV/APP premium/private content
- `--work-dir` set working directory
- `--ffmpeg-path`, `--mp4box-path`, `--aria2c-path` set binary paths
- `--config-file` use custom config file (default `BBDown.config`)
- `-h, --help` show help

For the full option list, run:

```bash
BBDown --help
```

## Features

- Anime/PGC downloads (Web/TV/App)
- Course downloads (Web)
- Normal video downloads (Web/TV/App)
- Parse collection/list/favorites/user-space links
- Auto download for multi-part videos
- Select specific parts
- Select specific quality
- Download subtitles and convert to `.srt`
- Auto merge video + audio + subtitles + chapters (ffmpeg/mp4box)
- Download video/audio/subtitles separately
- QR code login
- Multi-thread download
- aria2c integration
- AVC/HEVC/AV1 support
- 8K/HDR/Dolby Vision/Dolby Atmos support
- Custom output filename patterns

## TODO

- Auto refresh cookie
- More custom options

## Tutorial

<details>
<summary>Config File</summary>

---

From `1.4.9`+, BBDown can read a local config file to reduce repeated CLI arguments.

- If `--config-file` is not provided, BBDown reads `BBDown.config` from the same directory.
- If provided, BBDown reads the specified file.

Example:

```config
# Lines starting with # are comments.
# BBDown reads non-empty lines one by one.
# For options requiring a value, put the value on the next line.

--file-pattern
<videoTitle>[<dfn>]

--multi-file-pattern
<videoTitle>/[P<pageNumberWithZero>]<pageTitle>[<dfn>]

--delay-per-page
2

--download-danmaku
```

</details>

<details>
<summary>Custom Output File Pattern</summary>

---

From `1.4.9`+, BBDown supports custom filename variables:

- `<videoTitle>`: main video title
- `<pageNumber>`: part number
- `<pageNumberWithZero>`: part number (zero-padded)
- `<pageTitle>`: part title
- `<bvid>`: BV id
- `<aid>`: av id
- `<cid>`: cid
- `<dfn>`: quality text
- `<res>`: resolution
- `<fps>`: frame rate
- `<videoCodecs>`: video codec
- `<videoBandwidth>`: video bitrate
- `<audioCodecs>`: audio codec
- `<audioBandwidth>`: audio bitrate
- `<ownerName>`: uploader name (empty for anime/PGC)
- `<ownerMid>`: uploader mid (empty for anime/PGC)
- `<publishDate>`: publish date (`yyyy-MM-dd_HH-mm-ss`)
- `<apiType>`: API type (`TV/APP/INTL/WEB`)

</details>

<details>
<summary>WEB/TV Authentication</summary>

---

Login WEB account by QR:

```bash
BBDown login
```

Login TV account by QR:

```bash
BBDown logintv
```

If login fails with `The type initializer for 'Gdip' threw an exception`, see [#37](https://github.com/nilaoda/BBDown/issues/37).

Use WEB cookie manually:

```bash
BBDown -c "SESSDATA=******" "https://www.bilibili.com/video/BV1qt4y1X7TW"
```

Use TV token manually:

```bash
BBDown -tv -token "******" "https://www.bilibili.com/video/BV1qt4y1X7TW"
```

</details>

<details>
<summary>APP Authentication</summary>

---

According to [#123](https://github.com/nilaoda/BBDown/issues/123#issuecomment-877583825), you can use the TV login `access_token` for APP API mode.

- You can copy `BBDownTV.data` to `BBDownApp.data` so BBDown reads it automatically.
- BBDown currently cannot auto-fetch APP auth data; packet capture is commonly used.
- In request headers, find `authorization` like `identify_v1 5227************1`; the numeric part is token (`access_key`).

Then pass token manually:

```bash
BBDown -app -token "******" "https://www.bilibili.com/video/BV1qt4y1X7TW"
```

</details>

<details>
<summary>Common Commands</summary>

---

Download a normal video:

```bash
BBDown "https://www.bilibili.com/video/BV1qt4y1X7TW"
```

Use TV API mode:

```bash
BBDown -tv "https://www.bilibili.com/video/BV1qt4y1X7TW"
```

Show all parts:

```bash
BBDown --show-all "https://www.bilibili.com/video/BV1At41167aj"
```

Download selected parts:

```bash
# Single part
BBDown -p 10 "https://www.bilibili.com/video/BV1At41167aj"

# Multiple parts
BBDown -p 1,2,10 "https://www.bilibili.com/video/BV1At41167aj"

# Part range
BBDown -p 1-10 "https://www.bilibili.com/video/BV1At41167aj"
```

Download all episodes in a series:

```bash
BBDown -p ALL "https://www.bilibili.com/bangumi/play/ss33073"
```

</details>

<details>
<summary>API Server</summary>

---

Start server mode (custom host/port):

```bash
BBDown serve -l http://0.0.0.0:12450
```

API server does not support HTTPS directly; use a reverse proxy (e.g. Nginx) if needed.

API docs: [json-api-doc.md](./json-api-doc.md)

</details>

## Demo

![1](https://user-images.githubusercontent.com/20772925/88686407-a2001480-d129-11ea-8aac-97a0c71af115.gif)

After download finishes, check MP4 files in the current directory:

![2](https://user-images.githubusercontent.com/20772925/88478901-5e1cdc00-cf7e-11ea-97c1-154b9226564e.png)

## Thanks

- <https://github.com/codebude/QRCoder>
- <https://github.com/icsharpcode/SharpZipLib>
- <https://github.com/protocolbuffers/protobuf>
- <https://github.com/grpc/grpc>
- <https://github.com/dotnet/command-line-api>
- <https://github.com/SocialSisterYi/bilibili-API-collect>
- <https://github.com/SeeFlowerX/bilibili-grpc-api>
- <https://github.com/FFmpeg/FFmpeg>
- <https://github.com/gpac/gpac>
- <https://github.com/aria2/aria2>
