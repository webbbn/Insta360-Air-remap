# Insta360-Air-remap
<img src="/Insta360-Air-remap/insta360_1.png" width="350"> ➡️ <img src="/Insta360-Air-remap/insta360_1_down.jpg" width="350">

This code generates remapping projections for [ffmpeg](http://ffmpeg.org) to convert from dual-fish-eye images/video (spherical) to equirectangular output. It can be used to map both images and video.

The code was adapted from examples given for ffmpeg's [`RemapFilter`](https://trac.ffmpeg.org/wiki/RemapFilter), with added support for video captured with Insta360 Air devices.


## Guide

### Building

1. Install ffmpeg, (make sure it's a recent version)
2. Checkout the source of this repository
3. Build: `$ gcc projection.c -lm -o project

### Running

#### Create maps `xmap.pgm` and `ymap.pgm` for use with videos from Theta S devices:

```
$ ./project -x xmap.pgm -y ymap.pgm -h 960 -w 960 -c 1920 -r 960 -m theta --verbose
```

This creates two ASCII encoded [PGM files](https://en.wikipedia.org/wiki/Netpbm_format#PGM_example) which act as a lookup table for ffmpeg to remap the video.

There are various pre-generated lookup tables included allready:
1) Insta360 Air in uptight position (USB connector down)

<img src="/Insta360-Air-remap/xmap_insta360air_usb_down.jpg" width="250"> <img src="/Insta360-Air-remap/ymap_insta360air_usb_down.jpg" width="250">

2) Insta360 Air in hanging position (USB connector up)

<img src="/Insta360-Air-remap/xmap_insta360air_usb_up.jpg" width="250"> <img src="/Insta360-Air-remap/ymap_insta360air_usb_up.jpg" width="250">

3) Insta360 Air placed sideways (USB connector right or left)

<img src="/Insta360-Air-remap/xmap.jpg" width="250"> <img src="/Insta360-Air-remap/ymap.jpg" width="250">

#### Apply the maps to the video `input.mp4`:

```
$ ffmpeg -i input.mp4 -i xmap.pgm -i ymap.pgm -q 0 -lavfi "format=pix_fmts=rgb24,remap" remapped.mp4
```
#### Known issues
The Lens mapping isn't perfect, I'm working on getting a more accurate mapping.

![spinning camera](/Insta360-Air-remap/pictures/spin.gif)
![stabilized wobble example](/Insta360-Air-remap/pictures/wobbly.gif)

Stabilizing output videos via  Hugin with [Matthew Petroff's method](https://mpetroff.net/2016/11/stabilizing-360-video-with-hugin/) reveals a wobble that would not be present if the mapping was perfect; Convieniently, I think i can use Hugin's Lens Calibration tools on the source frames to find a better mapping.

### Useful Things for Spherical Video
#### Tag for upload

If you want to upload your video to youtube as a 360 video, make sure you encoded it as a .mp4, and use [this](https://github.com/google/spatial-media) tool from google. On linux, you can install `python-tk` and use the gui, or use it via command line. 

The instructions there say to run `python spatialmedia` but there isn't anything called that, so replace `spatialmedia` with `__main__.py` and it works.
```
python __main__.py -i remapped.mp4 remapped_injected.mp4
```
Your file is now ready for YouTube/etc.
