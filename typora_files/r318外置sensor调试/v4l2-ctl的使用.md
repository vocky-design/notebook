调试用命令：

./v4l2-ctl -d /dev/video22 --set-fmt-video=width=960,height=720,pixelformat=NV12 --stream-mmap=4 --stream-count=1 --stream-to=/tmp/up_cap.raw --stream-skip=2



v4l2-ctl --stream-mmap --stream-skip=10 --stream-count=100 --stream-to=output.raw

解释：这个命令将捕获100帧视频，并将每隔10帧跳过一帧。同时，它使用内存映射的方式将视频帧传输到本地文件`output.raw`中。