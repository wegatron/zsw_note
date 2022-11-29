
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

## gdown 下载google drive 文件

需要下载的链接: `https://drive.google.com/file/d/1jUB5yD7DP97-EqqU2A9mmr61JpNwZBVK/view`, 这里标志符为:`1jUB5yD7DP97-EqqU2A9mmr61JpNwZBVK`.

```bash
gdown https://drive.google.com/uc?id=标识符
```

