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

Mirror effect vertical (5 seconds in HD)</br>
`ffmpeg -i input.mp4 -vf "split [main][tmp]; [tmp] crop=iw:ih/2:0:0, vflip [flip]; [main][flip] overlay=0:H/2" -c:v libx264 -t 5 -s "1920x1080" output.mp4`

Zoom in (zoom from 1.0 to 2.4 and pan to top left corner 70/190 for 15 seconds)</br>
`ffmpeg -i input.mp4 -vf "zoompan=z='1+(1.4*in/300)':x='70*in/300':y='190*in/300':d=1:fps=30" -c:v libx264 -t 15 input2.mp4`

Two videos side by side (to check the zoom effect, just encode input.mp4 and input2.mp4 in one output.mp4)</br>
`ffmpeg -i input.mp4 -i input2.mp4 -filter_complex "[0:v]pad=iw*2:ih[int];[int][1:v]overlay=W/2:0[video]" -map "[video]" -c:v libx264 -map 0:a -c:a copy -t 15 output.mp4`

Crop</br>
in the middle 100px * 100px</br>
`ffmpeg -i input.mp4 -filter:v "crop=100:100" output.mp4`</br>
upper left crop 100px * 100px</br>
`ffmpeg -i input.mp4 -filter:v "crop=100:100:0:0" output.mp4`</br>

</br>**Abbreviations:**

CRF = Constant Rate Factor (Constant Quality)</br>
CQP = Constant Quantization Parameter (~Constant Bitrate)</br>
CBR = Constant Bitrate</br>
VBR = Variable Bitrate

</br>Search for "YouTube recommended upload encoding settings", for some encoding recommendations (e.g. bitrates).
