# [ffplayout Engine](https://github.com/ffplayout/ffplayout)

JSON Playlist Example
-----

```json
{
    "channel": "Test 1",
    "date": "2019-03-05",
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

**Warning**
-----

(Endless) streaming over multiple days will only work when config have **day_start** value and the **length** value is **24 hours**. If you need only some hours for every day, use a *cron* job, or something similar.

Remote source from URL
-----

You can use sources from remote URL in that way:

```json
...
        {
            "in": 0,
            "out": 149,
            "duration": 149,
            "source": "https://example.org/big_buck_bunny.webm"
        }
```

But be careful with it, better test it multiple times!

More informations in [Wiki](https://github.com/ffplayout/ffplayout_engine/wiki/Remote-URL-Source)

Installation
-----

Check [INSTALL.md](https://github.com/ffplayout/ffplayout_engine/blob/master/docs/INSTALL.md)

Start with Arguments
-----

ffplayout also allows the passing of parameters:


```Bash
OPTIONS:
    -c, --config <CONFIG>             File path to ffplayout.conf
    -f, --folder <FOLDER>             Play folder content
    -g, --generate <YYYY-MM-DD>...    Generate playlist for date or date-range, like: 2022-01-01 - 2022-01-10:
    -h, --help                        Print help information
    -i, --infinit                     Loop playlist infinitely
    -l, --log <LOG>                   File path for logging
    -m, --play-mode <PLAY_MODE>       Playing mode: folder, playlist
    -o, --output <OUTPUT>             Set output mode: desktop, hls, stream
    -p, --playlist <PLAYLIST>         Path from playlist
    -s, --start <START>               Start time in 'hh:mm:ss', 'now' for start with first
    -t, --length <LENGTH>             Set length in 'hh:mm:ss', 'none' for no length check
    -v, --volume <VOLUME>             Set audio volume
    -V, --version                     Print version information
```

You can run the command like:

```Bash
./ffplayout -l none -p ~/playlist.json -o desktop
```
