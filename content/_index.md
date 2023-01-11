---
title: "ffplayout"
date: 2021-07-29T09:19:16+02:00
draft: false
---
![ffplayput](/img/ffplayout.png)

**Welcome to ffplayout, the 24/7 cloud ready playout solution.**

ffplayout is made mostly for Linux, but the [engine](https://github.com/ffplayout/ffplayout) can run everywhere, where Rust can be compile and ffmpeg run. But outside of Linux you have to use your one startup/daemon solution.

The most tested system is Debian, if you have permission problems on systems with Selinux you have to get help from communities how have experience in that area.

ffplayout comes in three programs, which are interdependent:
- [ffplayout](https://github.com/ffplayout/ffplayout)
    - this program can run by it self and don't need the other ones
    - if you don't need a GUI, then you will be totally fine with it
- [ffplayout-api (ffpapi)](https://github.com/ffplayout/ffplayout/tree/master/ffplayout-api)
    - the API is the interface between the frontend and the engine, with that you can control the engine
- [ffplayout-frontend](https://github.com/ffplayout/ffplayout-frontend)
    - the frontend gives you a web based GUI for managing the engine and the media storage

What ffplayout can do
-----

When we talk about the features, we mostly talk about what the **ffplayout engine** can do. Because this is the heart of the project, and the other ones are only for controlling.

The main idea behind ffplayout is, that it works with playlists. Every day has its one playlist, so to have a continuous endless stream every playlist needs to be 24 hours long. When a playlist is not long enough the *engine* will fill the rest with a filler clip, or with black. Is the playlist longer it will be trimmed.
The playlist will be dynamically readed, that mean that you can still edit it, while it is already playing. But you can only change or add clips on a position in the future and next but one clip. In other words: when clip 7 in playlist is currently playing, you can change and add clips after position 9.

A second scenario is to, that ffplayout can play clips from a given folder. The *engine* also monitor this folder for changes. If clips are added; deleted or moved, the *engine* recognize this and update its file list. the folder mode has two options: 1. it can play in sorted order, or 2. it can play in a random order.

#### Features:

- have all values in a separate config file
- dynamic playlist
- replace missing playlist or clip with a dummy clip
- playing clips in [watched](https://github.com/ffplayout/ffplayout/tree/master/docs/folder_mode.md) folder mode
- send emails with error message
- overlay a logo
- overlay text, controllable through [messenger](https://github.com/ffplayout/messenger) or [ffplayout-frontend](https://github.com/ffplayout/ffplayout-frontend) (needs ffmpeg with libzmq and enabled JSON RPC server)
- EBU R128 loudness normalization (single pass)
- loop playlist infinitely
- [remote source](https://github.com/ffplayout/ffplayout/tree/master/docs/remote_source.md)
- trim and fade the last clip, to get full 24 hours
- when playlist is not 24 hours long, loop filler clip until time is full
- set custom day start, so you can have playlist for example: from 6am to 6am, instate of 0am to 12pm
- normal system requirements and no special tools
- no GPU power is needed
- stream to server or play on desktop
- logging to files, or colored output to console
- add filters to input, if is necessary to match output stream:
  - **yadif** (deinterlacing)
  - **pad** (letterbox or pillarbox to fit aspect)
  - **fps** (change fps)
  - **scale** (fit target resolution)
  - **aevalsrc** (if video have no audio)
  - **apad** (add silence if audio duration is to short)
  - **tpad** (add black frames if video duration is to short)
- [output](https://github.com/ffplayout/ffplayout/tree/master/docs/output.md):
  - **stream**
  - **desktop**
  - **HLS**
  - **null** (for debugging)
- JSON RPC server, for getting infos about current playing and controlling
- [live ingest](https://github.com/ffplayout/ffplayout/tree/master/docs/live_ingest.md)

- Multi Channel

How ffplayout works
-----

The principle behind the engine is that, with the exception of HLS output mode, that it works with two ffmpeg instances.

The first one is for preparing the input files, to unify them in every aspect. So when it is necessary the first ffmpeg instance can deinterlace the clip, pad it with black borders on top and bottom of left and right, change the frame rate, scale the size, add silence audio to it when audio is missing, add silence to the end when audio is to short, or add black frames to the end when the video is shorter than the audio. In the *engine* this ffmpeg instance is called the *decoder instance*.

The second ffmpeg instance is running endless, gets fed by the first instance and compress the final output. The output can be anything what ffmpeg supports: a stream (rtmp, srt, udp, rtc, etc.), a pipe, a file or ffplay for outputting to screen. In the *engine* this ffmpeg instance is called the *encoder instance*.

The two instances are connected with each other through a pipe.

What ffplayout can't do
-----

It's always nice to praise functions, but ffplayout can not do everything. It has his limits and in general stability is more important then features.

There is a frontend for ffplayout, but the frontend is not a full fledged program scheduling solution. The frontend is more made for simple controlling the engine and media storage, for sending text messages and explore the log files. There is a playlist creator, with some basic functions but it is not a professional scheduling program. The needs from users are to different to catch all of them, so it is up to you, to create your one scheduling solution.

ffplayout can not cross fade clips.

ffplayout can't schedule time sequences. The application is made for 24/7 streaming, and not only for a specific time range. When you only want to stream for some hours over a day, you need to run the engine manually or with a cronjob. The frontend could also not control the *engine* properly.

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

The parameters `in`, `out`, `duration` and `source` are mandatory. `category` can be set, but the *engine* will only consider the value **advertisement**. If a item has this advertisement category, the overlay logo will fade out before the clip starts and fade in after the clip is finish.

As long as you have the mandatory values in the playlist, you can extend it in any ways. For example you could give it a text key/value and read that in a custom filter, for overlaying subtitles.

Detail Description
-----

On the following pages you found more details from every program part and a install instruction.
