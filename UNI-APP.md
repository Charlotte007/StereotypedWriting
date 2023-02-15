### uni-app的能力
+ html5plus: https://www.html5plus.org/doc/zh_cn/device.html
+ uni-app的封装，会调用 plus ,功能来源于 html5plus
  + 默认页面是有webview嵌入的， this.$scope.$getAppWebview()，获取当前页面的
  + this.$scope.$getAppWebview().children()[0] 适用于页面中嵌入webview的情况
+ 很多奇怪的bug都来源于，是基于webview渲染的，所以webview是重点！！！
### 自定义导航
+ 非必要情况不要使用自定义导航，会导致一些问题
  + 状态栏的文本适配，特别是ios，可能会出现状态栏不显示文字(状态栏背景和文字颜色一样)
  + 导航栏层级问题，覆盖问题
    + video
    + web-view （默认全屏，ios后台挂起，出现web-view初始化，设置区域无效的情况，覆盖自定义导航）
+ titleNView区别: pages.json中静态配置，与 $getAppWebview().setStyle()
  + pages.json中静态配置，可以触发onNavigationBarButtonTap，获取自定义图标点击事件
  + setStyle({titleNView:{...}}) 动态这只，无法触发onNavigationBarButtonTap
```js
// #ifdef APP-PLUS
    const webView = this.$scope.$getAppWebview();
    webView.setStyle({
      titleNView: {
        buttons: [
          {
            fontSrc: 'static/fonts/icomoon.ttf',
            text: '\ue900',
            float: 'right',
            fontSize: '24px',
            // TIPS: 可以直接添加事件处理
            onclick: ev => {
              console.log('图标点击', ev);
            }
          }
        ]
      }
    });
  // #endif

```
### 嵌入web-view的问题
+ 层级问题
  + 遮挡弹窗
  + 遮挡自定义导航栏
+ 绘制区域问题
  + 默认全屏
  + 设置绘制区间后，可能因长时间后台挂起，而回收掉webview，导致白屏，或重新渲染，无法在正确时机设置绘制区间
+ 页面返回 (基于vue渲染，就是webview嵌入webview，所以需要.children[index])
  + this.$scope.$getAppWebview().children()[0].back();

### [全屏弹窗](https://uniapp.dcloud.net.cn/component/native-component.html#vue%E9%A1%B5%E9%9D%A2%E5%B1%82%E7%BA%A7%E8%A6%86%E7%9B%96%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88)
>> 需要覆盖自定义标题，原生组件tabbar，webview，video等
+ [subNvue](https://ask.dcloud.net.cn/article/35948)
  + 不支持slot
  + 每个 subNVue 页面都要在 pages.json 中注册， 官方定义，subNvue不属于组件，属于原生子窗体
  + 层级最高，可以覆盖一切组件
+ nvue
  + 定位的差异
    + 作为页面：可以通过新开半透明nvue页面处理；（页面栈的层级来覆盖），层级高度可以覆盖navBar，tabBar（亲测）；
      + 通信：页面跳转的通信，仅能传入基础数据，无法通过slot传入复杂组件
      + 可否通过 render方式，传入函数形式？？？（封装组件，上传到 插件市场）
        + 亲测：仅h5支持render写法，其他平台均不支持。。。
    + 作为组件：层级高度无法覆盖navBar，tabBar（亲测）
      + 支持slot
  + 可以全局封装
+ html5plus中plus.nativeObj.view的能力绘制区间
  + 只能覆盖状态栏和tabar，否则会覆盖vue弹窗，可能会出现淡入效果不同步的情况，否则没有办法适应弹窗内容
  + 内存占用过多
  + 在弹窗存在时跳转，需要处理 statusBar 和 tabbar的 mask层
+ cover-view，只能覆盖常规原生组件，无法覆盖 navbar， tabbar、web-view
+ 使用原生组件封装
  + uni.requireNativePlugin 引入原生插件
  + 缺点：需要原生开发介入，但可以在插件市场找到基础的原生组件
+ 总结：最高层级的问题，适配能力最强的为 plus.nativeObj.view 和 subNvue 和 原生封装调用

### 白屏（ios更为严重）
+ 推荐使用nvue > vue
+ [性能优化参考小程序性能优化](./WECHAR-MINI.md)
+ 稳定的版本HBuilder2.3.4+

### vue模板（webview实现）嵌入H5后的返回处理
+ 避免返回时，直接退出h5界面，返回上一级

### 权限设置引导或者提示
+ 相机/相册 权限
+ 文件夹权限
+ 定位权限

### 平台差异
+ 不推荐后端接口使用cookie鉴权，H5端不能设置header-cookie（安全策略原因），但可以在APP真机环境，以及内置浏览器（关闭https）中调试使用
+ document、xmlhttp、cookie、window、location、navigator、localstorage 仅仅在浏览器环境下可使用，其他平台无法兼容

### 调用原生组件实践
+ 推送组件

### 分包 & 热更新（wgt）

### 安全区域
+ statusBarHeight，（ios大多为20px，android大多为25px）
  + uni.getSystemInfo().statusBarHeight
  + uni.getWindowInfo().statusBarHeight
  + var(--status-bar-height)
+ ios底部安全区（是否覆盖底部虚拟键，IOS可设置，android不建议通过plus设置，会影响正常的虚拟键用户,建议默认设置20px兜底）
  + constant(safe-area-inset-bottom)
  + env(safe-area-inset-bottom);
```json
// manifest.json
"app-plus" : {
    "safearea" : {
        "bottom" : {
            "offset" : "none" // 仅对ios生效，android不生效
        }
    }
}
```

### 打包实践
+ android需要配置 androidPrivacy.json，注意配置的链接或内容和APP其他协议入口保持一致
+ appkey，等
+ 如何一套代码，根据业务需求，在不同平台下打包不同页面? （如何动态配置OR生成pages.json）
``` sh
#!/bin/bash
# 动态生成h5的页面配置 pages.json
node ./pages-node-generator.js env=h5

# HX打包，TIPS：需要添加 HBuilderX/cli.exe 到用户环境变量 PATH
cli.exe publish --platform h5 --project yt-h5

# 拷贝文件到 /dist目录
function set_work_dir
{
    readonly DIR_NAMES=$(pwd)/unpackage/dist/build/h5
    readonly OUTPUT=$(pwd)/dist
    readonly WORKSPACE_DIR=$(pwd)
}
function copy_result
{
    cd ${WORKSPACE_DIR}
    cp -r ${DIR_NAMES}/* ${OUTPUT}
}

# run
set_work_dir
copy_result

echo "cli打包成功，已完成拷贝"

# 还原默认的页面配置 pages.json
node ./pages-node-generator.js

sleep 3s

echo "打包完成，即将关闭"
```

### 本地打包实践（离线打包）
+ 依赖
+ nvue和vue依赖包的区别
+ anroid的权限配置依赖mainfest.json的勾选，需要一一对应才能生效
  + mainfest.json中的模块秘钥参数等，只对云打包有效
+ 离线打包是，注意mainfest.json模块中配置的秘钥，参数之类的数据，需要在对应SDK包中，原生调用配置
  + [微信分享](https://nativesupport.dcloud.net.cn/AppDocs/usemodule/iOSModuleConfig/share?id=%e6%b3%a8%e6%84%8f-sdk-320-%e5%bf%85%e9%a1%bb%e6%8c%89%e7%85%a7%e4%b8%8b%e5%9b%be%e5%a1%ab%e5%86%99-1)

### 上架审核
+ 无大量测试数据
+ 隐私协议完整
  + 功能匹配
  + 第三方隐私协议链接完整

### 待解决
+ app端选择文件，仅返回文件路径，无法直接获取文件的二进制数据，只能使用 uni.uploadFile，post，form-data的方式处理
+ 小程序端可以通过文件系统，拿到二进制文件，上传到第三方OSS

## 工程化
### cli脚手架
+ uni cli：不依赖hx，支持 h5、各平台小程序，app资源包（离线打包-资源包，wgt资源包）
+ HBuilderX cli：集成hx中，支持(全平台) h5、各平台小程序，app 编译打包；cli.exe只能在windows环境运行
+ 脚手架转换
  + uni转hx，无缝切换，直接使用
  + hx转uni， 需要uni-cli初始化空项目,转移源码，安装依赖；（推荐直接使用uni-cli）

### 环境变量
+ 自带的环境变量 process.env.NODE_ENV
  + development：本地，运行 > 
  + production： 打包，发行 >
+ [vue-config.js](https://uniapp.dcloud.net.cn/collocation/vue-config.html#)
  + hbuilder & cli 都可
  + 需要 webpack配置
  + 针对`全平台`生效
+ [package.json](https://uniapp.dcloud.net.cn/collocation/package.html#)
  + 编译、发行 仅支持 h5和各类小程序，`不支持app`
  + 针对`指定的平台`配置
+ [.env](https://cli.vuejs.org/zh/guide/mode-and-env.html#%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F)
  + 仅uni-cli，脱离hx生效 

### 理想化脚手架工具：
+ 根据不同平台，生成pages.json
+ 

## 统计埋点与性能监控


## 错误提示
  + reportJSException >>>> exception function:createInstanceContext, exception:white screen cause create instanceContext failed,check js stack ->Uncaught SyntaxError: Invalid or unexpected token
