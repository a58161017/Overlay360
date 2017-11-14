# Overlay360
利用Hugin內部的nona來扭曲成360圖示，最後使用ffmpeg進行overlay

## Environment And Tools

作業系統:Windows

FFmpeg:ffmpeg-3.4-win64-static

  下載 https://www.ffmpeg.org/download.html#build-windows
  
Hugin(1):Hugin-2017.0-win64.msi(nona.exe)，有GUI工具可以編輯360影片

  下載 https://sourceforge.net/projects/hugin/files/hugin/hugin-2017.0/
 
Hugin(2):Hugin_2015.0.0_64bit.7z(nona.exe)，有GUI工具可以編輯360影片(免安裝)

  下載 https://sourceforge.net/projects/hugin/files/hugin/hugin-2015.0/


## Prepare a PNG picture

準備好一張要放入影片的PNG圖片，圖片必須與影片解析度一樣大，且圖示要在正中間。

可以透過一些工具來製作，譬如https://pixlr.com/editor/，新增一張透明底色的圖片，再把想要的小圖示裁減貼到上面去。


## Creating Hugin Project

以下有幾種產生方式:

(1)透過圖形化介面hugin.exe來產生.pto的檔案，參考http://hugin.sourceforge.net/tutorials/index.shtml。

(2)用command-line的pto_gen.exe來產生.pto的檔案，參考https://wiki.panotools.org/Panorama_scripting_in_a_nutshell。

(3)如果熟悉參數也可以直接開啟記事本來產生.pto的檔案，參考http://hugin.sourceforge.net/docs/nona/nona.txt。

產生後的專案用記事本打開，會如下:

p f2 w3840 h1920 v360  E0 R0 n"PNG_m"

m g1 i0 f0 m2 p0.00784314

i f4 v360 Ra0 Rb0 Rc0 Rd0 Re0 Eev0 Er1 Eb1 r0 p0 y0 TrX0 TrY0 TrZ0 Tpy0 Tpp0 j0 a0 b0 c0 d0 e0 g0 t0 Va1 Vb0 Vc0 Vd0 Vx0 Vy0  Vm5 n"360Img.png"


以上三行有幾項可以自行修改，第一行的w3840 h1920是指輸出的解析度，第三行的r0 p0 y0這三個就是本專案的重點，下面詳解，還有第三行的n"360Img.png"，這是要放入影片的小圖示。

上述提到的r0 p0 y0分別為roll=0(degree) pitch=0 yaw=0:

roll:以z軸作為旋轉，介於-180~180(degree)，本專案無作用

pitch:以x軸作為旋轉，介於-90~90(degree)，以平面圖正中間為(0,0)，y向上會從0~90(degree)，y向下會從0~-90(degree)

yaw:以y軸作為旋轉，介於-180~180(degree)，以平面圖正中間為(0,0)，x向右會從0~180(degree)，x向左會從0~-180(degree)


## Encode Hugin Project

nona -o 360Img project.pto

## Overlay Images

(1)一張圖片指令

ffmpeg -i vdo1.mp4 -i 360Img0000.png -filter_complex "[0:v][1:v] overlay=0:0:enable='between(t,0,11)'" -pix_fmt yuv420p -max_muxing_queue_size 400 output.mp4


(2)多張圖片指令

ffmpeg -i vdo1.mp4 -i 360Img0000.png -i 360Img20000.png -filter_complex "[0:v][1:v] overlay=0:0:enable='between(t,0,11)' [tmp]; [tmp][2:v] overlay=0:0:enable='between(t,0,11)'" -pix_fmt yuv420p -max_muxing_queue_size 400 output.mp4
