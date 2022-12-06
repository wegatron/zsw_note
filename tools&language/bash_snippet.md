
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


对于大文件可能会出错, 使用如下方式下载:
```python
gdown.download(
    "https://drive.google.com/uc?export=download&confirm=pbef&id=1ghvzYXdmiCuX5I757id73jWuRLMCzXAX",
    "ckpt/00000189-checkpoint.pth.tar"
)
```

对于带有`confirm=t`的文件, 可以使用(curl支持断点续传):
```bash
curl -L -o data/my-file.h5 'https://drive.google.com/uc?id=my-file-id-here&confirm=t
```