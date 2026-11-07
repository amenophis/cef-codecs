# cef-codecs

[CEF](https://github.com/chromiumembedded/cef) (Chromium Embedded Framework) built from source with Chromium's
proprietary codecs, H.264, AAC and HEVC, which the [official CEF builds](https://cef-builds.spotifycdn.com/index.html)
leave out. It is the CEF [Arbalete](https://github.com/amenophis/arbalete) ships with, so that sites like Twitch play.

Each release, `cef-<version>-codecs.<build>`, holds CEF's minimal distribution for macOS arm64, named as CEF names it,
and its SHA-256. Only macOS arm64 is built. The build number grows when the binary changes for the same CEF version;
`cef-156.0.7-codecs`, the first, is build 1.

## How it is built

From the CEF commit and Chromium version the release names, with CEF's own
[`automate-git.py`](https://github.com/chromiumembedded/cef/blob/master/tools/automate/automate-git.py), at that
commit, and these GN arguments:

```sh
export GN_DEFINES="is_official_build=true proprietary_codecs=true ffmpeg_branding=Chrome symbol_level=0 treat_warnings_as_errors=false"
export CEF_ARCHIVE_FORMAT=tar.bz2
python3 automate-git.py --download-dir=download --depot-tools-dir=depot_tools \
  --branch=<Chromium build number> --checkout=<CEF commit> --arm64-build --no-debug-build \
  --minimal-distrib-only --no-distrib-docs --no-chromium-history --with-pgo-profiles --build-target=cefclient
```

From build 2, the sources are patched with [patches/](patches), each with a header saying what it changes and
why: [alloy-webui.patch](patches/alloy-webui.patch) lets Alloy-style browsers show Chromium's own pages
(chrome://settings, extensions, history…) when the app opens them, and guards the buttons of those pages that would
otherwise crash without a Chromium browser window. Nothing else is changed: the sources are Chromium's and CEF's, at
the tags the release names. The builds run on the maintainer's Mac, from a private repository, so that no one else's
code ever runs there.

## Licenses

CEF is under the BSD 3-Clause license, Chromium under its own BSD-style license and those of its components. Each
distribution's `CREDITS.html` holds all of them.

The codecs are decoded by [FFmpeg](https://ffmpeg.org), part of Chromium (`third_party/ffmpeg`), under the
[LGPL 2.1](https://www.gnu.org/licenses/old-licenses/lgpl-2.1.html). Its source is Chromium's, at the version the
release names: <https://chromium.googlesource.com/chromium/third_party/ffmpeg>. HEVC is decoded by macOS.

H.264, AAC and HEVC may be covered by patents in some countries. These builds come with no patent license: whoever
redistributes them is responsible for any license their use needs.
