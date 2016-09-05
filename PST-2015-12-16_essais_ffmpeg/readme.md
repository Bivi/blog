# Post
![](viewme.png)

## Name
[`PST-2015-12-16_essais_ffmpeg`]()

## Title
2015-12-16 Essais scan conversion avec ffmpeg pour echOpen

## Description
Découverte Webcam sous Windows

~~~~ sh
ffmpeg -list_devices true -f dshow -i dummy
ffplay -f dshow -i video="Logitech HD Webcam C525"
~~~~

https://trac.ffmpeg.org/wiki/FancyFilteringExamples

~~~~ sh
ffplay -f dshow -i video="Logitech HD Webcam C525" -vf "format=yuv444p,split=4[a][b][c][d],[a]waveform[aa],[b][aa]vstack[V],[c]waveform=m=0[cc],[d]vectorscope=color4[dd],[cc][dd]vstack[V2],[V][V2]hstack"
ffplay -f dshow -i video="Logitech HD Webcam C525" -vf "boxblur=luma_radius=min(h\,w)/10:luma_power=1:chroma_radius=min(cw\,ch)/10:chroma_power=1"
ffplay -f dshow -i video="Logitech HD Webcam C525" -vf "crop=in_w/2:in_h/2:y:10+10*sin(n/10)"
ffplay -f dshow -i video="Logitech HD Webcam C525" -vf "decimate=cycle=2"
~~~~

https://ffmpeg.org/ffmpeg-filters.html#toc-Examples-36

~~~~ sh
ffplay -f dshow -i video="Logitech HD Webcam C525" -f lavfi -i nullsrc=s=hd720,lutrgb=128:128:128 -f lavfi -i nullsrc=s=hd720,geq='r=128+30*sin(2*PI*X/400+T):g=128+30*sin(2*PI*X/400+T):b=128+30*sin(2*PI*X/400+T)' -lavfi '[0][1][2]displace'

ffplay -f dshow -i video="Logitech HD Webcam C525" -vf "nullsrc=s=640x480:sar=1.33,lutrgb=128:128:128[p1], nullsrc=s=640x480:sar=1.33,geq='r=128+30*sin(2*PI*X/400+T):g=128+30*sin(2*PI*X/400+T):b=128+30*sin(2*PI*X/400+T)'[p2], [in][p1][p2]displace"

ffplay -f lavfi -i cellauto=s=320x200:r=15 -vf "nullsrc=s=320x200,lutrgb=128:128:128[p1], nullsrc=s=320x200,geq='r=128+30*sin(2*PI*X/400):g=128+30*sin(2*PI*X/400):b=128+30*sin(2*PI*X/400)'[p2], [in][p1][p2]displace"

ffplay -f dshow -i video="Logitech HD Webcam C525" -vf "scale=320x200, setsar=sar=1, fps=fps=0.1[p0], nullsrc=s=320x200,lutrgb=128:128:128[p1], nullsrc=s=320x200,geq='r=128+30*sin(2*PI*X/400):g=128+30*sin(2*PI*X/400):b=128+30*sin(2*PI*X/400)'[p2], [p0][p1][p2]displace"
~~~~


