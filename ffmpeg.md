### Low Res commands

**HD** 

```bash
ffmpeg -i video.mxf -vf "field=type=top, scale=w=480:h=270"  -filter_complex "[0:1][0:2]amerge=inputs=2[aout0]" -map 0:0 -map "[aout0]" -vcodec libx264 -acodec mp2 -ab 192000 -vb 1200000 -bf 0 -g 13 lowres_video.mov
```
The above command picks top field and scales without B frame so hopefully it will be frame accurate

**SD**

```bash
ffmpeg -i video.mxf -vf "yadif, scale=w=480:h=270"  -filter_complex "[0:1][0:2]amerge=inputs=2[aout0]" -map 0:0 -map "[aout0]" -vcodec libx264 -acodec mp2 -ab 192000 -vb 1200000 -bf 0 -g 13 lowres_video.mov
```

### Concat

**Concat**
```bash
ffmpeg -copyts -vsync 0 -segment_time_metadata 1 -i one.ffconcat -vf select=concatdec_select -af aselect=concatdec_select out_concat.mp4
```
https://github.com/FFmpeg/FFmpeg/blob/master/tests/simple1.ffconcat

### Mix and extract audio 

**Audio 1, Audio 2 (2 channels) each mono to single output stereo mp2**

```bash
ffmpeg -y -i BUZ_FFXX00156.mov-map 0:v:0 -filter_complex "[0:a:0][0:a:1]amerge=inputs=2[aout0]" -map "[aout0]" -acodec mp2 -b:a 128k -vcodec libx264 -pix_fmt yuv420p -vb 1500k -x264opts nal-hrd=cbr:force-cfr=1 output.mp4
```

**For audio only output**

```bash
ffmpeg -y -i BUZ_FFXX00156.mov-map 0:v:0 -filter_complex "[0:a:0][0:a:1]amerge=inputs=2[aout0]" -map "[aout0]" -acodec mp2 -b:a 128k -vn out.mp2
```

**For Audio1 to 1 stereo audio track and AUdio2 to another stereo track**

```bash
fmpeg -y -i BUZ_FFXX00156.mov-map 0:v:0  -map 0:a:0 -map 0:a:1 -ac 2  -acodec mp2 -b:a 128k -vcodec libx264 -pix_fmt yuv420p -vb 1500k -x264opts nal-hrd=cbr:force-cfr=1 output.mp4
```

**Convert to 1280x720**

```bash
ffmpeg -i REAL_STORIES_INTRO_SOUND.ts -vf "scale=w=1280:h=720"  -pix_fmt yuv420p  -vcodec h264 -g 12 -keyint_min 12 -refs 3 -bf 2 -flags +ildct+ilme -top 1 -x264opts nal-hrd=cbr -profile:v high -level 4.0 -vb 12500000 -minrate:v 12500000 -maxrate:v 12500000 -bufsize:v 7000000 -bsf:v h264_metadata=fixed_frame_rate_flag=1  -acodec copy -map 0:v -map 0:a:0 -muxrate 17000k -vsync 1 -async 1 REAL_STORIES_INTRO_SOUND_HALF_HD.ts
```

Useful Links
----

* https://gist.github.com/protrolium/e0dbd4bb0f1a396fcb55

* ffconcat

https://superuser.com/questions/459313/how-to-cut-at-exact-frames-using-ffmpeg
https://github.com/FFmpeg/FFmpeg/blob/master/tests/simple1.ffconcat

* CRF

https://slhck.info/video/2017/02/24/crf-guide.html

* Mapping

http://johnriselvato.com/what-is-map-in-ffmpeg-map-explained/

https://trac.ffmpeg.org/wiki/AudioChannelManipulation

* General Commands

https://ostechnix.com/20-ffmpeg-commands-beginners/

https://en.wikibooks.org/wiki/FFMPEG_An_Intermediate_Guide
