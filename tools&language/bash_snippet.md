
## ffmpeg decode video:

每隔1秒, 将视频转化为图片:

```bash
ffmpeg -i input.mp4 -vf fps=1 out%03d.png
```

将图片序列转化为视频:

```bash
ffmpeg -framerate 30 -pattern_type glob -i '*.png' -c:v libx264 -pix_fmt yuv420p out.mp4
```

列出大文件
```bash
find . -type f -size +100M
```