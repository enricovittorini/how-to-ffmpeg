"# how-to-ffmpeg" 
# how-to-ffmpeg

## Keyframe to images
```ffmpeg -i  video.ts -f image2 -vf "select='eq(pict_type,PICT_TYPE_I)'" -vsync passthrough image%03d.png```

## Countdown with audio tick every second
-  _color=color=black_, black background
- _d=15_, duration 15s
- _r=15_, framerate 25fps
- _g 1_, GOP=1

```ffmpeg -f lavfi -i "color=color=black:d=15:r=25:s=1920x1080,drawtext=fontfile=Led.ttf:fontcolor=yellow:fontsize=400:x=(main_w-text_w)/2:y=(main_h-text_h)/2:text='%{eif\:(15-(n/25))\:d\:2}.%{eif\:(mod(25 - mod(n, 25), 25))\:d\:2}'" -f lavfi -i "aevalsrc='if(between(mod(t,25*(1000/25000)),0,2*(1000/25000)-0.00001),sin(840*2*PI*t),0)':d=15:s=48000:nb_samples=1024:c=stereo" -codec:v libx264 -flags +ilme+ildct -g 1 -c:a mp2 -b:a 128k output.ts```

## Play two files in Sync
- _s_, stream index. see here https://man.archlinux.org/man/ffmpeg-filters.1.en#movie

```ffplay -f lavfi "movie=file01.ts:discontinuity=0.05:s=5,scale=iw/2:ih[v0];movie=file02.ts:s=7:discontinuity=0.05,scale=iw/2:ih[v1];[v0][v1]hstack,drawbox=x=0:y=0:w=960:h=1080:color=blue:t=3,drawbox=x=960:y=0:w=960:h=1080:color=blue:t=3" -x 1024 -window_title  "LEFT FIRST FILE RIGHT SECOND FILE"  -x 1600 -noborder```
