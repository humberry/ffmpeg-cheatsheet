# ffmpeg-cheatsheet
</br>**H.264 encoding:**

reencoding via CPU (default parameters `-crf 23 -preset medium`):</br>
`ffmpeg -i input.mp4 -c:v libx264 -c:a copy output.mp4`

reencoding via Nvidia (default parameters `-rc -1 -preset medium -profile main`):</br>
`ffmpeg -i input.mp4 -c:v h264_nvenc -c:a copy output.mp4`
(ˋ-cq:v 21ˋ)

reencoding via AMD (default parameters `-qp_i 22 -qp_b 22 -qp_p 22 -profile main`):</br>
`ffmpeg -i input.mp4 -c:v h264_amf -rc 0 -c:a copy output.mp4`</br>
(more compression/worse quality: `-rc 0 -qp_i 26 -qp_b 26 -qp_p 26` or `-rc 0 -qp_i 26 -qp_b 32 -qp_p 30`)

</br>**Show encoders / encoder info:**

`ffmpeg -encoders`</br>
`ffmpeg -h encoder=h264_amf`

</br>**Transformations:**

Rotation</br>
0 = 90° counter-clockwise and vertical flip (default)</br>
1 = 90° clockwise</br>
2 = 90° counter-clockwise</br>
3 = 90° clockwise and vertical flip</br>
`ffmpeg -i input.mp4 -vf "transpose=0" output.mp4`

Mirror effect vertical (15 seconds in HD)</br>
`ffmpeg -i input.mp4 -vf "split [main][tmp]; [tmp] crop=iw:ih/2:0:0, vflip [flip]; [main][flip] overlay=0:H/2" -c:v libx264 -t 15 -s "1920x1080" input2.mp4`

Two videos side by side (to check the mirror effect, just encode input.mp4 and input2.mp4 in one output.mp4)</br>
`ffmpeg -i input.mp4 -i input2.mp4 -filter_complex "[0:v]pad=iw*2:ih[int];[int][1:v]overlay=W/2:0[video]" -map "[video]" -c:v libx264 -map 0:a -c:a copy -t 15 output.mp4`

Crop</br>
in the middle 100px * 100px</br>
`ffmpeg -i input.mp4 -filter:v "crop=100:100" output.mp4`</br>
upper left crop (a quarter)</br>
`ffmpeg -i input.mp4 -filter:v "crop=iw/2:ih/2:0:0" output.mp4`</br>

Oval shaped video (black background and transparent oval)</br>
`ffmpeg -i input.mp4 -i circle-hd.png -filter_complex "[0][1] overlay=0:0" output.mp4`</br>

Play two small videos diagonal (quarter hd resolution videos 990x540)</br>
`ffmpeg -i black-hd.png -i input2.mp4 -i input3.mp4 -filter_complex "[0][1] overlay=0:0[out];[out][2] overlay=990:540" output.mp4`</br>

Use a second video as an overlay (e.g. watermark / position=50:50 / overlay.mp4 with black background e.g. circle-hd.png)</br>
`ffmpeg -i input.mp4 -i overlay.mp4 -filter_complex "[1:v]colorkey=0x000000[ol];[0:v][ol]overlay=50:50[out]" -map "[out]" -map 0:a -c:a copy output.mp4`</br>
if the overlay video is shorter just repeat it (this time the overlay.mp4 has a white background / similarity=0.2 / blend=0.3)</br>
`ffmpeg -i input.mp4 -stream_loop -1 -i overlay.mp4 -filter_complex "[1:v]scale=-1:180,colorkey=0xFFFFFF:0.2:0.3[ol];[0:v][ol]overlay=50:50:shortest=1[out]" -map "[out]" -map 0:a -c:a copy output.mp4`</br>

</br>**Extras:**

Get the time and number of frames</br>
`ffmpeg -i input.mp4 -vcodec copy -acodec copy -f null NUL`</br>

Create a short clip for testing (5 seconds, skip first 10 seconds)</br>
`ffmpeg -i input.mp4 -ss 0:0:10 -t 0:0:5 -c:v copy -c:a copy output.mp4`</br>

Video screenshot (after 5 seconds make 25 screenshots with the best quality)</br>
`ffmpeg -i input.mp4 -ss 0:0:5 -vframes 25 -qscale:v 1 -qmin 1 screenshot-%02d.jpg`</br>

Make GIF from video (with own color palette, -1 = keep aspect ratio)</br>
`ffmpeg -i input.mp4 -ss 0:0:10 -t 0:0:5 -c:v copy -c:a copy input2.mp4`</br>
`ffmpeg -i input2.mp4 -vf "fps=15,scale=240:-1:flags=lanczos,palettegen" -y palette.png`</br>
`ffmpeg -i input2.mp4 -i palette.png -lavfi "fps=15,scale=240:-1:flags=lanczos[x];[x][1:v]paletteuse" -y output.gif`</br>

Make video from images (01.jpg - 99.jpg, only 1 image per second, looped 3 times, RGB2YUV)</br>
`ffmpeg -framerate 1 -stream_loop 3 -f image2 -i %02d.jpg -c:v libx264 -pix_fmt yuv420p output.mp4`</br>

Get all Windows DirectShow Devices (e.g. Stereomix, works for me with headspeakers)</br>
`ffmpeg -list_devices true -f dshow -i dummy`</br>
`ffmpeg -f dshow -i audio="Stereomix (Realtek(R) Audio)" -acodec libmp3lame output.mp3`</br>

Record Windows desktop with gdigrab and downscale to 720p</br>
`ffmpeg -f gdigrab -framerate 15 -i desktop -c:v h264_nvenc -rc 0 -vf "scale=-1:720" output.mp4`</br>

Record Windows desktop 1080p/30 with audio</br>
`ffmpeg -f gdigrab -framerate 30 -i desktop -f dshow -i audio="Stereomix (Realtek(R) Audio)" -acodec libmp3lame -c:v h264_nvenc -rc 0 -vf "scale=-1:1080" output.mp4`</br>

Record Linux desktop</br>
`xwininfo | grep -B 1 "geometry"` (e.g. Corners: +70+27 ...     -geometry 1280x720-0-0)</br>
`ffmpeg -video_size 1280x720 -framerate 25 -f x11grab -i :0.0+70,27 output.mp4`</br>

[FFmpeg - The Ultimate Guide](https://img.ly/blog/ultimate-guide-to-ffmpeg/)</br>
[FFmpeg Filters Documentation ](https://ffmpeg.org/ffmpeg-filters.html)</br>

</br>**Abbreviations:**

CRF = Constant Rate Factor (Constant Quality)</br>
CQP = Constant Quantization Parameter (~Constant Bitrate)</br>
CBR = Constant Bitrate</br>
VBR = Variable Bitrate

</br>Search for "YouTube recommended upload encoding settings", for some encoding recommendations (e.g. bitrates).

External links disclaimer:<br>
I'm not responsible for the content of external websites.<br>
