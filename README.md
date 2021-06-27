
# fijkplayer_skin
[![pub package](https://img.shields.io/pub/v/fijkplayer_skin.svg)](https://pub.dev/packages/fijkplayer_skin)

这是一款 fijkplayer 播放器的普通皮肤，主要解决 fijkplayer 自带的皮肤不好看，没有手势拖动快进，快退
fijkplayer_skin只是一款皮肤，并不是播放器，所以 fijkplayer 存在的问题，这里 fijkplayer_skin 一样存在

# Flutter SDK要求
sdk >= 2.12.0 支持空安全，关于sdk升级，请自行百度

# 功能如下

* 手势滑动，快进快退
* 上下滑动（左：屏幕亮度 右：系统音量）
* 视频内剧集切换 （全屏模式下，视频内部切换播放剧集）
* 倍数切换，（全屏模式下，切换倍数）
* 锁定，（锁定UI，防误触）
* 设置视频顶部返回，标题

## 其他开源项目

* [Dart-Cms](https://github.com/abcd498936590/Dart-Cms)  =>> Dart-Cms完整项目
* [Dart-Cms-Manage](https://github.com/abcd498936590/Dart-Cms-Manage)   =>> Dart-Cms后台管理系统页面部分
* [Dart-Cms-Flutter](https://github.com/abcd498936590/Dart-Cms-Flutter)  =>> Dart-Cms的安卓客户端，使用flutter开发
* [Dart-Cms-Script](https://github.com/abcd498936590/Dart-Cms-Script)  =>> Dart-Cms插件教程，插件使用，插件开发


## 使用说明

在使用皮肤之前，你需要查看 fijkplayer 的文档说明，了解如何 fijkplayer [自定义UI](https://fijkplayer.befovy.com/docs/zh/custom-ui.html#gsc.tab=0)

## 预览
<img style="max-width: 100%;" src="https://cdn.jsdelivr.net/gh/abcd498936590/pic@master/img/fijkplayer_skin-1.png" />


## 安装
pubspec.yaml
```yaml
dependencies:
  fijkplayer: ${lastes_version}
  fijkplayer_skin: ${lastes_version}
```
或者
```yaml
fijkplayer_skin:
  git:
    url: git@github.com:abcd498936590/fijkplayer_skin.git
```

## 使用示例 （包含剧集切换）
```dart
    
    import 'package:fijkplayer_skin/fijkplayer_skin.dart';
    import 'package:fijkplayer/fijkplayer.dart';
    
    class VideoScreen extends StatefulWidget {
      VideoScreen();

      @override
      _VideoScreenState createState() => _VideoScreenState();
    }

    class _VideoScreenState extends State<VideoScreen> {
      // FijkPlayer实例
      final FijkPlayer player = FijkPlayer();
      // 当前tab的index，默认0
      int _curTabIdx = 0;
      // 当前选中的tablist index，默认0
      int _curActiveIdx = 0;
      // 视频源列表，请参考当前videoList完整例子
      Map<String, List<Map<String, dynamic>>> Map<String, List<Map<String, dynamic>>> videoList = {
        "video": [
          {
            "name": "天空资源",
            "list": [
              {
                "url": "https://n1.szjal.cn/20210428/lsNZ6QAL/index.m3u8",
                "name": "综艺"
              },
              {
                "url": "https://static.smartisanos.cn/common/video/t1-ui.mp4",
                "name": "锤子1"
              },
              {
                "url": "https://static.smartisanos.cn/common/video/video-jgpro.mp4",
                "name": "锤子2"
              }
            ]
          },
          {
            "name": "天空资源",
            "list": [
              {
                "url": "https://n1.szjal.cn/20210428/lsNZ6QAL/index.m3u8",
                "name": "综艺"
              },
              {
                "url": "https://static.smartisanos.cn/common/video/t1-ui.mp4",
                "name": "锤子1"
              },
              {
                "url": "https://static.smartisanos.cn/common/video/video-jgpro.mp4",
                "name": "锤子2"
              }
            ]
          },
        ]
      };

      @override
      void initState() {
        super.initState();
      }

      void onChangeVideo(int curTabIdx, int curActiveIdx) {
        this.setState(() {
          _curTabIdx = curTabIdx;
          _curActiveIdx = curActiveIdx;
        });
      }

      @override
      Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(title: Text("Fijkplayer Example")),
            body: Container(
              alignment: Alignment.center,
              // 这里 FijkView 开始为自定义 UI 部分
              child: FijkView(
                height: 240,
                color: Colors.black,
                fit: FijkFit.cover,
                player: player,
                panelBuilder: (
                  FijkPlayer player,
                  FijkData data,
                  BuildContext context,
                  Size viewSize,
                  Rect texturePos,
                ) {
                 /// 使用自定义的布局
                 return CustomFijkPanel(
                    player: player,
                    // 传递 context 用于左上角返回箭头关闭当前页面，不要传递错误 context，
                    // 如果要点击箭头关闭当前的页面，那必须传递当前页面的根 context
                    buildContext: context,
                    viewSize: viewSize,
                    texturePos: texturePos,
                    // 是否显示顶部，如果要显示顶部标题栏 + 返回键，那么就传递 true
                    showTopCon: true,
                    // 标题 当前页面顶部的标题部分，可以不传，默认空字符串
                    playerTitle: "标题",
                    // 当前视频改变钩子
                    onChangeVideo: onChangeVideo,
                    // 视频源列表
                    videoList: videoList,
                    // 当前视频源tabIndex
                    curTabIdx: _curTabIdx,
                    // 当前视频源activeIndex
                    curActiveIdx: _curActiveIdx,
                  );
                },
              );
            )
        );
      }

      @override
      void dispose() {
        super.dispose();
        player.release();
      }
    }

```

## LICENSE
MIT
