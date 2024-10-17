**目录**

# ffmpeg
### ffmpeg入门
```shell
# 下载流媒体视频文件(TS流媒体协议)
ffmpeg -i http://aisylive.oss/xxxxxxx/xxxx/xxxxxx.m3u8 -c copy -bsf:a aac_adtstoasc xxxx.mp4
# 下载DASH协议视频
# 去浏览器上找到视频播放页面，找到对应的m4s文件，一般为两个，音频和视频，在文件管理器中地址栏下载下来，之后执行下面指令
ffmpeg -i video.m4s -i audio.m4s -codec copy video.mp4
# 如果下载m4s出现404，可以直接双击m4s的网址跳转到视频和音频界面并点击下载，之后使用下方命令合并
ffmpeg -i video.mp4 -i audio.mp4 -c:v copy -c:a copy output.mp4
```
```python
# 下载m4s出现404
# 可以用以下代码下载
import requests

def download_movie(bl_url, file_name):
    headers = {
        'Referer': 'https://www.bilibili.com/video/BV',  # 填需要爬取的bv号
        'Sec-Fetch-Mode': 'no-cors',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36',
    }

    try:
        response = requests.get(bl_url, headers=headers, timeout=10)
        response.raise_for_status()

        path = f"D:\\Temp\\biliDown\\{file_name}"
        with open(path, 'wb') as file:
            file.write(response.content)

    except requests.exceptions.RequestException as e:
        print(f"Error downloading movie: {e}")

if __name__ == "__main__":
    # 第三步的m4s地址
    video_url = "https://xy49x86x255x16xy.mcdn.bilivideo.cn:4483/upgcxcode/64/99/517569964/517569964_x2-1-30077.m4s?e=ig8euxZM2rNcNbdlhoNvNC8BqJIzNbfqXBvEqxTEto8BTrNvN0GvT90Wxxxxxx"
    # 文件名称 建议和链接保持一致
    name = "517569964_x2-1-30077.m4s"

    download_movie(video_url, name)
```
### ffmpeg介绍
1. 简介
   - ffmpeg全称为Fast Forward Moving Picture Experts Group
   - 于2000年诞生，是一款免费，开源的音视频编解码工具及开发套件
   - 功能强大，用途广泛，大量用于视频网站和商业软件
   - 其本身是一个庞大的项目，包含许多组件和库文件，最常用的就是他的命令行工具
2. 基本组成
   - 命令行工具 `ffmpeg(编解码工具)+ffprobe(多媒体分析器)+ffplay(播放器)`
   - 模板库 (c/c++ code)
   - AVFormat: 用于各种音视频封装格式的生成和解析(AVI,MKV,MP4...)
   - AVCodec: 用于各种类型声音/图像编解码(H.264,H.265,VP9...)
   - AVFilter: 用于滤镜处理
   - AVUtil: 包含一些公共的工具函数
   - swscale: 用于视频场景比例缩放，色彩映射转换
   - ...