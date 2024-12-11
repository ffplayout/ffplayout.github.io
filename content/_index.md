---
title: "ffplayout"
date: 2021-07-29T09:19:16+02:00
draft: false
---
![ffplayput](/img/ffplayout.png)

**Welcome to ffplayout, the 24/7 cloud ready playout solution.**

ffplayout is a 24/7 broadcasting solution. It can playout a folder containing audio or video clips, or play a *JSON* playlist for each day, keeping the current playlist editable.

The application is mostly designed to run as system service on Linux. But in general it should run on any platform supported by Rust.

Check the [releases](https://github.com/ffplayout/ffplayout/releases/latest) for pre compiled version.

What ffplayout can do
-----

The main idea behind ffplayout is that it works with playlists. Each day has its own playlist, so to have a continuous endless stream, each playlist must be 24 hours long. If a playlist is not long enough, the *engine* will fill the rest with a filler clip or with black. If the playlist is longer, it will be trimmed.
The playlist is read dynamically, so you can edit it while it is playing. But you can only change or add clips at a position in the future and the next but one clip. In other words, if clip 7 in the playlist is currently playing, you can edit and add clips after position 9.

A second scenario is that ffplayout can play clips from a given folder. The *engine* also monitors this folder for changes. If clips are added, deleted or moved, the engine recognizes this and updates its file list. the folder mode has two options: 1. it can play in sorted order, or 2. it can play in random order.

#### Features:

- start program with [web based frontend](https://github.com/ffplayout/frontend/), or run playout in foreground mode without frontend
- dynamic playlist
- replace missing playlist or clip with single filler or multiple fillers from folder, if no filler exists, create dummy clip
- playing clips in [watched](https://github.com/ffplayout/docs/folder_mode.md) folder mode
- send emails with error message
- overlay a logo
- overlay text, controllable through [web frontend](https://github.com/ffplayout/frontend/) (needs ffmpeg with libzmq and enabled JSON RPC server)
- loop playlist infinitely
- [remote source](https://github.com/ffplayout/docs/remote_source.md)
- trim and fade the last clip, to get full 24 hours
- when playlist is not 24 hours long, loop fillers until time is full
- set custom day start, so you can have playlist for example: from 6am to 6am, instate of 0am to 12pm
- normal system requirements and no special tools
- no GPU power is needed
- stream to server or play on desktop
- log to files or color output to console
- add filters to input, if is necessary to match output stream:
  - **yadif** (deinterlacing)
  - **pad** (letterbox or pillarbox to fit aspect)
  - **fps** (change fps)
  - **scale** (fit target resolution)
  - **aevalsrc** (if video have no audio)
  - **apad** (add silence if audio duration is to short)
  - **tpad** (add black frames if video duration is to short)
- [output](https://github.com/ffplayout/docs/output.md):
  - **stream**
  - **desktop**
  - **HLS**
  - **null** (for debugging)
- [live ingest](https://github.com/ffplayout/docs/live_ingest.md)
- image source (will loop until out duration is reached)
- extra audio source, has priority over audio from video (experimental *)
- [multiple audio tracks](https://github.com/ffplayout/docs/multi_audio.md) (experimental *)
- [Stream Copy](https://github.com/ffplayout/docs/stream_copy.md) mode (experimental *)
- [custom filters](https://github.com/ffplayout/docs/custom_filters.md) globally in config, or in playlist for specific clips
- import playlist from text or m3u file, with CLI or frontend
- audio only, for radio mode (experimental *)
- generate playlist based on [template](https://github.com/ffplayout/docs/playlist_gen.md) (experimental *)
- During playlist import, all video clips are validated and, if desired, checked to ensure that the audio track is not completely muted.
- run multiple channels (experimental *)
- vtt [subtitle](https://github.com/ffplayout/docs/closed_captions.md) in HLS mode (experimental *)

For preview stream, read: [/docs/preview_stream.md](https://github.com/ffplayout/docs/preview_stream.md)

**\* Experimental features do not guarantee the same stability and may fail under unusual circumstances. Code and configuration options may change in the future.**

How ffplayout works
-----

The principle behind the engine is that, with the exception of the HLS output mode, it works with two ffmpeg instances.

The first one is used to prepare the input files to unify them in every aspect. So if necessary, the first ffmpeg instance can deinterlace the clip, add black borders to the top and bottom left and right, change the frame rate, scale the size, add silence audio if there is no audio, add silence to the end if the audio is too short, or add black frames to the end if the video is shorter than the audio. In the *engine* this ffmpeg instance is called the *decoder instance*.

The second ffmpeg instance runs endless, gets fed by the first instance and compresses the final output. The output can be anything ffmpeg supports: a stream (rtmp, srt, udp, rtc, etc.), a pipe, a file, or ffplay for output to screen. In the *engine*, this ffmpeg instance is called the *encoder instance*.

The two instances are connected by a pipe.

What ffplayout can't do
-----

It's always nice to praise features, but fflayout can't do everything. It has its limits, and in general stability is more important than features.

There is a frontend for ffplayout, but the frontend is not a full-fledged program scheduling solution. The frontend is more for simple control of the engine and media storage, sending text messages and exploring the log files. There is a playlist creator with some basic features, but it is not a professional scheduling program. The needs of users are too different to cover them all, so it is up to you to create your own scheduling solution.

ffplayout cannot cross-fade clips.

ffplayout cannot schedule time sequences. The application is designed for 24/7 streaming, not for a specific time period. If you only want to stream a few hours a day, you have to run the engine manually or with a cronjob. Also, the frontend could not control the *engine* properly.

The Playlist
-----

The playlist format is JSON conform, so it is easy to read and modify. Here a basic example:

```JSON
{
    "channel": "Channel 1",
    "date": "2021-04-28",
    "program": [{
            "in": 0,
            "out": 647.68,
            "duration": 647.68,
            "source": "/Media/clip1.mp4"
        }, {
            "in": 0,
            "out": 149,
            "duration": 149,
            "source": "/Media/clip2.mp4"
        }, {
            "in": 0,
            "out": 114.72,
            "duration": 114.72,
            "source": "/Media/clip3.mp4",
            "category": "advertisement"
        }, {
            "in": 0,
            "out": 2531.36,
            "duration": 2531.36,
            "source": "/Media/clip4.mp4",
            "category": ""
        }
    ]
}
```

The parameters `in`, `out`, `duration` and `source` are mandatory. The `category` can be set, but the *engine* will only consider the value **advertisement**. If an item has this ad category, the overlay logo will fade out before the clip starts and fade in after the clip ends.

As long as you have the mandatory values in the playlist, you can extend it in any way you like. For example, you could give it a text key/value and read it in a custom filter to overlay captions.

Detailed description
-----

On the following pages you will find more details about each program part and an installation guide.
