# 小程序

## 特点
```
响应的数据绑定
页面管理
基础组件
丰富的 API (如获取用户信息，本地存储，支付功能等。)
```

## 运行环境
```
分成渲染层和逻辑层，其中 WXML 模板和 WXSS 样式工作在渲染层，JS 脚本工作在逻辑层。
小程序的渲染层和逻辑层分别由2个线程管理：渲染层的界面使用了WebView进行渲染；逻辑层采用JsCore线程运行JS脚本。
一个小程序存在多个界面，所以渲染层存在多个WebView线程，这两个线程的通信会经由微信客户端（下文中也会采用Native来代指微信客户端）做中转，逻辑层发送网络请求也经由Native转发。
```
[图片](https://res.wx.qq.com/wxdoc/dist/assets/img/4-1.ad156d1c.png)

## 目录结构
```
根目录: 
app.json ---> 小程序公共配置
app.js ---> 小程序逻辑
app.wxss ---> 小程序公共样式表

小程序页面文件目录:
.js ---> 页面逻辑
.wxml ---> 页面结构
.wxss ---> 页面样式
.json ---> 页面配置
```

## 配置文件
### 1. app.json: 小程序的全局配置。
### （⭐️代表也支持页面配置，disableScroll -> 设置为 true 则页面整体不能上下滚动。只在页面配置中有效，无法在 app.json 中设置）
* pages: 小程序的所有页面路径，第一行就是小程序的首页。
* window: 定义小程序所有页面的顶部背景颜色，文字颜色定义等。包括: 
```
navigationBarBackgroundColor: 导航栏背景颜色。⭐️
navigationBarTextStyle: 导航栏标题颜色，仅支持 black / white。⭐️
navigationBarTitleText: 导航栏标题文字内容。⭐️
navigationStyle: 导航栏样式，仅支持以下值：default 默认样式；custom 自定义导航栏，只保留右上角胶囊按钮。⭐️
backgroundColor: 窗口的背景色。⭐️
backgroundTextStyle: 下拉 loading 的样式，仅支持 dark / light。⭐️
backgroundColorTop: 顶部窗口的背景色，仅 iOS 支持。⭐️
backgroundColorBottom: 底部窗口的背景色，仅 iOS 支持。⭐️
enablePullDownRefresh: 是否开启全局的下拉刷新。⭐️
onReachBottomDistance: 页面上拉触底事件触发时距页面底部距离，单位为 px。⭐️
pageOrientation: 屏幕旋转设置，支持 auto(随着屏幕旋转而旋转) / portrait(固定为竖屏显示) / landscape(固定为横屏显示)。⭐️
restartStrategy: 重新启动策略配置。支持 homePage(默认值，如果从这个页面退出小程序，下次将从首页冷启动) / homePageAndLatestPage(如果从这个页面退出小程序，下次冷启动后立刻加载这个页面，页面的参数保持不变（不可用于 tab 页）)⭐️
initialRenderingCache: 页面初始渲染缓存配置，支持 static (静态) / dynamic (动态，适用于除data外加载的一些广告等)。⭐️
visualEffectInBackground: 切入系统后台时，隐藏页面内容，保护用户隐私。支持 hidden / none。适用于比如禁用手机录屏等。⭐️
handleWebviewPreload: 控制预加载下个页面的时机。支持 static / manual / auto。⭐️
```
* entryPagePath: 默认启动路径（首页）。未指定 entryPagePath 时，数组的第一项代表小程序的初始页面（首页）。
* tabBar: 配置项指定 tab 栏的表现，以及 tab 切换时显示的对应页面。
```
color: tab 上的文字默认颜色，仅支持十六进制颜色。
selectedColor: tab 上的文字选中时的颜色，仅支持十六进制颜色。
backgroundColor: tab 的背景色，仅支持十六进制颜色。
borderStyle: tabbar 上边框的颜色， 仅支持 black / white。
position: tabBar 的位置，仅支持 bottom / top。
custom: 自定义 tabBar。
list: tab 的列表。包括: 
pagePath -> 页面路径，必须在 pages 中先定义。
text -> tab 上按钮文字。
iconPath -> 图片路径，icon 大小限制为 40kb，建议尺寸为 81px * 81px，不支持网络图片。当 position 为 top 时，不显示 icon。
selectedIconPath -> 选中时的图片路径，限制同上。
```
* networkTimeout: 各类网络请求的超时时间(指在程序默认的等待时间内没有得到服务器的响应。)，单位均为毫秒。
```
request: wx.request 的超时时间，单位：毫秒。
connectSocket: wx.connectSocket 的超时时间。
uploadFile: wx.uploadFile 的超时时间。
downloadFile: wx.downloadFile 的超时时间。
```
* requiredBackgroundModes: 需要后台运行的能力，类型为数组。目前支持["audio", "location"]。例如: audio，被手机切换为后台应用的时候，小程序中的音频还可以正常播放。
* functionalPages: 启用插件功能页。
* requiredPrivateInfos: 使用以下8个地理位置相关接口时，需要声明该字段，否则将无法正常使用。申明需要使用的地理位置相关接口，类型为数组。目前支持以下项目：
```
getFuzzyLocation: 获取模糊地理位置
getLocation: 获取精确地理位置
onLocationChange: 监听实时地理位置变化事件
startLocationUpdate: 接收位置消息（前台）
startLocationUpdateBackground: 接收位置消息（前后台）
chooseLocation: 打开地图选择位置
choosePoi: 打开POI列表选择位置
chooseAddress: 获取用户地址信息
```
* plugins: 声明小程序需要使用的插件。
* resizable: 在 iPad 上运行的小程序可以设置支持屏幕旋转。
* usingComponents: 声明全局自定义组件。⭐️
* permission: 小程序接口权限相关设置。字段类型为 Object(属性必填为desc -> 小程序获取权限时展示的接口用途说明。最长 30 个字符)。
[图片](https://res.wx.qq.com/wxdoc/dist/assets/img/permission-desc.496b775d.jpeg)
```
"permission": {
  "scope.userLocation": {
    "desc": "你的位置信息将用于小程序位置接口的效果展示" // 高速公路行驶持续后台定位
  }
}
```
* style: 组件样式版本，"v2"可表明启用新版的组件样式。⭐️
* entranceDeclare: 聊天位置消息用打车类小程序打开。
* darkmode: 表示当前小程序可适配 DarkMode，所有基础组件均会根据系统主题展示不同的默认样式，navigation bar 和 tab bar 也会根据开发者的配置自动切换。
* themeLocation: 自定义 theme.json 的路径，当配置"darkmode":true时，当前配置文件为必填项。
* useExtendedLib: 指定需要引用的扩展库。目前支持以下项目：kbone: 多端开发框架。weui: WeUI 组件库。
```
"useExtendedLib": {
  "kbone": true,
  "weui": true
}
```
* embeddedAppIdList: 指定小程序可通过wx.openEmbeddedMiniProgram打开的小程序名单。打开半屏小程序。
* halfPage: 属性为firstPageNavigationStyle，视频号直播打开的第一个页面的全屏状态使用自定义顶部，支持 default / custom。
* renderer: 指定小程序全局的默认渲染后端。可选值：webview, skyline。默认值：webview。⭐️
* enablePassiveEvent: touch 相关事件默认的 passive 为 false。如果小程序不使用 catchtouch* 事件时，可以通过这个选项将 passive 置为 true，以提高滚动性能。⭐️

## 逻辑层文件
### 1. app.js: 小程序的根实例。
* 生命周期及事件处理函数
```
onLaunch: 小程序初始化完成时触发，全局只触发一次。有参数
onShow: 小程序启动或切前台显示时触发。有参数
onHide: 小程序从前台进入后台时触发(如 切换到其他的App)。
onError: 小程序发生脚本错误或 API 调用报错时触发。有参数
onPageNotFound: 小程序要打开的页面不存在时触发。有参数
onUnhandledRejection: 小程序有未处理的 Promise 拒绝时触发。有参数
onThemeChange: 系统切换主题时触发。有参数
```

### 2. page.js: 小程序中每个页面的实例。(Page 构造器适用于简单的页面。Component 构造器适用于复杂的页面。Component 构造器的主要区别是：方法需要放在 methods: { } 里面。)
* 生命周期及事件处理函数
```
onLoad: 页面创建时执行。有参数
onShow: 页面出现在前台时执行。
onReady: 页面首次渲染完毕时执行。
onHide: 页面从前台变为后台时执行。
onUnload: 页面销毁时执行。

onPullDownRefresh: 触发下拉刷新时执行。
onReachBottom: 页面触底时执行。
onShareAppMessage: 页面被用户分享时执行。
onPageScroll: 页面滚动时执行。
onResize: 页面尺寸变化时执行。
onTabItemTap: tab 点击时执行。
onPageScroll: 页面滚动时执行。
```

### 3. behaviors.js: 页面可以引用 behaviors，behaviors 可以用来让多个页面有相同的数据字段和方法。类似于Vue的混入。

### 4. 路由跳转: 
* 打开新页面
```


## 工具配置: project.config.json，引用配置，类似于package.json。
