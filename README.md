# ffmpeg-cheatsheet
</br>**H.264 encoding:**

reencoding via CPU (default parameters `-crf 23 -preset medium`):</br>
`ffmpeg -i input.mp4 -c:v libx264 -c:a copy output.mp4`

reencoding via Nvidia (default parameters `-rc -1 -preset medium -profile main`):</br>
`ffmpeg -i input.mp4 -c:v h264_nvenc -c:a copy output.mp4`

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

Get the time and number of frames</br>
`ffmpeg -i input.mp4 -vcodec copy -acodec copy -f null NUL`</br>

Create a short clip for testing (5 seconds, skip first 10 seconds)</br>
`ffmpeg -i input.mp4 -ss 0:0:10 -t 0:0:5 -c:v copy -c:a copy output.mp4`</br>

Video screenshot (after 5 seconds make 25 screenshots with the best quality)</br>
`ffmpeg -i input.mp4 -ss 0:0:5 -vframes 25 -qscale:v 1 -qmin 1 screenshot-%02d.jpg`</br>

</br>**Abbreviations:**

CRF = Constant Rate Factor (Constant Quality)</br>
CQP = Constant Quantization Parameter (~Constant Bitrate)</br>
CBR = Constant Bitrate</br>
VBR = Variable Bitrate

</br>Search for "YouTube recommended upload encoding settings", for some encoding recommendations (e.g. bitrates).

External links disclaimer:<br>
I'm not responsible for the content of external websites.<br>
