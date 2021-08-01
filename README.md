
# fijkplayer_skin
[![pub package](https://img.shields.io/pub/v/fijkplayer_skin.svg)](https://pub.dev/packages/fijkplayer_skin)

这是一款 fijkplayer 播放器的普通皮肤，主要解决 fijkplayer 自带的皮肤不好看，没有手势拖动快进，快退 
fijkplayer_skin只是一款皮肤，并不是播放器，所以 fijkplayer 存在的问题，这里 fijkplayer_skin 一样存在

## Flutter SDK要求
sdk >= 2.12.0 支持空安全

## 功能如下

* 手势滑动，快进快退
* 上下滑动（左：屏幕亮度 右：系统音量）
* 视频内剧集切换 （全屏模式下，视频内部切换播放剧集）
* 倍数切换，（全屏模式下，切换倍数）
* 锁定，（锁定UI，防误触）
* 设置视频顶部返回，标题
* 支持部分UI配置显示隐藏

## 其他开源项目

* [Dart-Cms](https://github.com/abcd498936590/Dart-Cms)  =>> Dart-Cms完整项目
* [Dart-Cms-Manage](https://github.com/abcd498936590/Dart-Cms-Manage)   =>> Dart-Cms后台管理系统页面部分
* [Dart-Cms-Flutter](https://github.com/abcd498936590/Dart-Cms-Flutter)  =>> Dart-Cms的安卓客户端，使用flutter开发
* [Dart-Cms-Script](https://github.com/abcd498936590/Dart-Cms-Script)  =>> Dart-Cms插件教程，插件使用，插件开发


## 使用说明

在使用皮肤之前，你需要查看 fijkplayer 的文档说明，了解如何 fijkplayer [自定义UI](https://fijkplayer.befovy.com/docs/zh/custom-ui.html#gsc.tab=0)

## 预览
<p align="center">
  <img style="max-width: 100%;" src="https://cdn.jsdelivr.net/gh/abcd498936590/pic@master/img/fijkplayer-skin-0.png" />
  <img width="350" style="max-width: 100%;" src="https://cdn.jsdelivr.net/gh/abcd498936590/pic@master/img/fijkplayer-skin-1.png" />
  <img width="350" style="max-width: 100%;" src="https://cdn.jsdelivr.net/gh/abcd498936590/pic@master/img/fijkplayer-skin-2.png" />
  <img width="350" style="max-width: 100%;" src="https://cdn.jsdelivr.net/gh/abcd498936590/pic@master/img/fijkplayer-skin-3.png" />
  <img width="350" style="max-width: 100%;" src="https://cdn.jsdelivr.net/gh/abcd498936590/pic@master/img/fijkplayer-skin-4.png" />
  <img width="350" style="max-width: 100%;" src="https://cdn.jsdelivr.net/gh/abcd498936590/pic@master/img/fijkplayer-skin-5.png" />
  <img width="350" style="max-width: 100%;" src="https://cdn.jsdelivr.net/gh/abcd498936590/pic@master/img/fijkplayer-skin-6.png" />
  <img width="350" style="max-width: 100%;" src="https://cdn.jsdelivr.net/gh/abcd498936590/pic@master/img/fijkplayer-skin-7.png" />
</p>


## 安装
> 如果 pub 安装失败或者不能使用，请更换git方式引入

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

## 参数说明

_curTabIdx 当前选中的tab索引，_curActiveIdx 当前选中的剧集索引。如果你使用过 react 你一定知道状态提升，父组件托管数据，子组件只负责渲染，这里的 _curTabIdx 和 _curActiveIdx 也是同理，在父组件中可以给多个需要该数据的组件传递。（包括皮肤内部也使用该数据）

videoList 存放视频列表，请参考我的格式，使用 fijkplayer_skin/schema.dart 中的 VideoSourceFormat 格式化数据

onChangeVideo（int curTabIdx, int curActiveIdx） 钩子函数，在播放器内切换剧集会触发该函数，回调参数是最新的 TabIdx 和 ActiveIdx ，用于更新托管在父组件中的 _curTabIdx 和 _curActiveIdx

pageContent 传递的就是当前组件的 context，这里注意，你当前的根组件不要使用 MaterialApp 否则会报错，请使用 Scaffold

isFillingNav 是否填充状态栏，（默认false），如果开启，皮肤会计算状态栏高度，在播放器顶部多预览状态栏的高度

showConfig 传递一个接口实例，抽象类 ShowConfigAbs，实现之后传递给皮肤，定制你需要显示的按键
> drawerBtn => 播放列表  nextBtn => 下一个按钮  speedBtn => 速度按钮  lockBtn => 锁按钮 
> topBar => 是否显示顶部UI  autoNext => 播放完成后是否自动播放下一集（如果下一集存在）


## 基本示例

```dart

import 'package:flutter/material.dart';
import 'package:fijkplayer/fijkplayer.dart';
import 'package:fijkplayer_skin/fijkplayer_skin.dart';
import 'package:fijkplayer_skin/schema.dart' show VideoSourceFormat;

// 这里实现一个皮肤显示配置项
class PlayerShowConfig implements ShowConfigAbs {
  bool drawerBtn = true;  
  bool nextBtn = true;
  bool speedBtn = true;
  bool topBar = true;
  bool lockBtn = true;
  bool autoNext = true;
}

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
  // ignore: non_constant_identifier_names
  ShowConfigAbs v_cfg = PlayerShowConfig();
  // 视频源列表，请参考当前videoList完整例子
  Map<String, List<Map<String, dynamic>>> videoList = {
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
    // 这句不能省，必须有
    speed = 1.0;
  }

  @override
  void dispose() {
    player.dispose();
    super.dispose();
  }

  // 播放器内部切换视频钩子，回调，tabIdx 和 activeIdx
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
                // 如果要点击箭头关闭当前的页面，那必须传递当前组件的根 context
                pageContent: context,
                viewSize: viewSize,
                texturePos: texturePos,
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
                // 是否填充状态栏
                isFillingNav: true,
                // 显示的配置
                showConfig: v_cfg,
              );
            },
          );
        ),
    );
  }

}

```

## 以下是完整例子
完整例子，包括剧集切换，播放器内部剧集切换与外部数据共享 tabIdx 和 activeIdx

```dart

import 'package:fijkplayer/fijkplayer.dart';
import 'package:fijkplayer_skin/fijkplayer_skin.dart';
import 'package:fijkplayer_skin/schema.dart' show VideoSourceFormat;
import 'package:flutter/material.dart';

void main() {
  // debugPaintSizeEnabled = true;
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: Scaffold(
        body: ListView(
          children: <Widget>[
            // 测试
            // VideoDetailPage(videoList),
            HomeIndex()
          ],
        ),
      ),
    );
  }
}

class HomeIndex extends StatefulWidget {
  @override
  HomeIndexState createState() => HomeIndexState();
}

class HomeIndexState extends State<HomeIndex> {
  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      style: ButtonStyle(
        shape: MaterialStateProperty.all(
          RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(5),
          ),
        ),
        elevation: MaterialStateProperty.all(0),
        backgroundColor: MaterialStateProperty.all(Colors.blue),
      ),
      onPressed: () async {
        Navigator.of(context)
            .push(MaterialPageRoute(builder: (context) => Home2()));
      },
      child: Text(
        "按钮",
        style: TextStyle(
          color: Colors.white,
        ),
      ),
    );
  }
}

class Home2 extends StatefulWidget {
  @override
  Home2State createState() => Home2State();
}

class Home2State extends State<Home2> {
  @override
  Widget build(BuildContext context) {
    // return VideoDetailPage();
    return Scaffold(
      body: Column(
        children: [
          Container(
            height: 24,
          ),
          VideoDetailPage(),
        ],
      ),
    );
  }
}

// 定制UI配置项
class PlayerShowConfig implements ShowConfigAbs {
  bool drawerBtn = true;  
  bool nextBtn = true;
  bool speedBtn = true;
  bool topBar = true;
  bool lockBtn = true;
  bool autoNext = true;
}

class VideoDetailPage extends StatefulWidget {
  @override
  _VideoDetailPageState createState() => _VideoDetailPageState();
}

class _VideoDetailPageState extends State<VideoDetailPage>
    with TickerProviderStateMixin {
  final FijkPlayer player = FijkPlayer();
  Map<String, List<Map<String, dynamic>>> videoList = {
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

  VideoSourceFormat? _videoSourceTabs;
  late TabController _tabController;

  int _curTabIdx = 0;
  int _curActiveIdx = 0;

  // ignore: non_constant_identifier_names
  ShowConfigAbs v_cfg = PlayerShowConfig();

  @override
  void dispose() {
    player.dispose();
    _tabController.dispose();
    super.dispose();
  }

  // 钩子函数，用于更新状态
  void onChangeVideo(int curTabIdx, int curActiveIdx) {
    this.setState(() {
      _curTabIdx = curTabIdx;
      _curActiveIdx = curActiveIdx;
    });
  }

  @override
  void initState() {
    super.initState();
    // 格式化json转对象
    _videoSourceTabs = VideoSourceFormat.fromJson(videoList);
    // tabbar初始化
    _tabController = TabController(
      length: _videoSourceTabs!.video!.length,
      vsync: this,
    );
    // 这句不能省，必须有
    speed = 1.0;
  }

  // build 剧集
  Widget buildPlayDrawer() {
    return Scaffold(
      appBar: PreferredSize(
        preferredSize: Size.fromHeight(24),
        child: AppBar(
          backgroundColor: Colors.black,
          automaticallyImplyLeading: false,
          primary: false,
          elevation: 0,
          title: TabBar(
            tabs: _videoSourceTabs!.video!
                .map((e) => Tab(text: e!.name!))
                .toList(),
            isScrollable: true,
            controller: _tabController,
          ),
        ),
      ),
      body: Container(
        color: Colors.black87,
        child: TabBarView(
          controller: _tabController,
          children: createTabConList(),
        ),
      ),
    );
  }

  // 剧集 tabCon
  List<Widget> createTabConList() {
    List<Widget> list = [];
    _videoSourceTabs!.video!.asMap().keys.forEach((int tabIdx) {
      List<Widget> playListBtns = _videoSourceTabs!.video![tabIdx]!.list!
          .asMap()
          .keys
          .map((int activeIdx) {
        return Padding(
          padding: EdgeInsets.all(5),
          child: ElevatedButton(
            style: ButtonStyle(
              shape: MaterialStateProperty.all(
                RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(5),
                ),
              ),
              elevation: MaterialStateProperty.all(0),
              backgroundColor: MaterialStateProperty.all(
                  tabIdx == _curTabIdx && activeIdx == _curActiveIdx
                      ? Colors.red
                      : Colors.blue),
            ),
            onPressed: () async {
              setState(() {
                _curTabIdx = tabIdx;
                _curActiveIdx = activeIdx;
              });
              String nextVideoUrl =
                  _videoSourceTabs!.video![tabIdx]!.list![activeIdx]!.url!;
              // 切换播放源
              await player.stop();
              await player.reset();
              player.setDataSource(nextVideoUrl, autoPlay: true);
            },
            child: Text(
              _videoSourceTabs!.video![tabIdx]!.list![activeIdx]!.name!,
              style: TextStyle(
                color: Colors.white,
              ),
            ),
          ),
        );
      }).toList();
      //
      list.add(
        SingleChildScrollView(
          child: Padding(
            padding: EdgeInsets.only(left: 5, right: 5),
            child: Wrap(
              direction: Axis.horizontal,
              children: playListBtns,
            ),
          ),
        ),
      );
    });
    return list;
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        FijkView(
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
              viewSize: viewSize,
              texturePos: texturePos,
              pageContent: context,
              // 标题 当前页面顶部的标题部分
              playerTitle: "标题",
              // 当前视频改变钩子
              onChangeVideo: onChangeVideo,
              // 视频源
              videoList: videoList,
              // 当前视频源tabIndex
              curTabIdx: _curTabIdx,
              // 当前视频源activeIndex
              curActiveIdx: _curActiveIdx,
              // 是否填充状态栏
              isFillingNav: true,
              // 显示的配置
              showConfig: v_cfg,
            );
          },
        ),
        Container(
          height: 300,
          child: buildPlayDrawer(),
        ),
        Container(
          child: Text(
              '当前tabIdx : ${_curTabIdx.toString()} 当前activeIdx : ${_curActiveIdx.toString()}'),
        )
      ],
    );
  }
}

```

## LICENSE
MIT
