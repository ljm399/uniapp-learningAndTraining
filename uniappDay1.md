# 一。邂逅跨平台开发

- 原生开发优势

  - 体验、性能和兼容性优秀

  - 可方便调用硬件设备（如摄像头、罗盘）

- 原生开发劣势
  - 开发成本高（需两套开发团队）
  - 上线周期长（需分别审核）
  - 版本控制困难（不同平台进度不一致）
- 跨平台开发特点：
  - "一套代码，多端运行"理念
  - 显著降低开发成本（案例：从6万/月降至2万/月）
  - 适合中小企业和复杂度不高的应用



### 原生

- - 优势：
    - 性能表现：硬件调用无损耗（如摄像头、罗盘直接调用）
    - 用户体验：系统级流畅度与动画效果（参考微信/抖音的滑动体验）
    - 安全性：沙盒机制完善（如iOS的App Sandbox）
  - 劣势：
    - 维护成本：需双团队维护（iOS/Android工程师薪资差异）
    - 版本碎片化：可能出现"iOS下单成功但安卓失败"的兼容问题

### 跨平台

- 核心优势：

  - 多端覆盖：一次开发覆盖iOS/Android/微信/支付宝等小程序
  - 成本控制：开发周期缩短30%-50%（相比原生）

- 技术限制：

  - 硬件支持：蓝牙等深度硬件功能需原生插件补充
  - 性能天花板：复杂动画帧率可能降至40fps以下（原生通常60fps）

  - 适用场景：
    - 中低频应用：企业OA、电商展示类（如uni-app开发的商城）
    - 简单交互：表单提交、列表展示（避免复杂手势操作）
  - 禁用场景：
    - 高性能需求：视频编辑（如剪映）、AR应用（如Pokemon GO）
    - 深度定制UI：需要系统级渲染的界面（如iOS毛玻璃效果）

- 调试复杂度：

  - H5调试：Chrome开发者工具直接调试
  - 原生调试：需要Xcode/Android Studio真机调试

  - CSS适配：
    - 单位差异：需同时处理rpx（小程序）、vw/vh（H5）、pt/dp（原生



# 二。其他类似软件 和 认识uni-app

### 和taro选择

- - Vue开发者优先选择Uniapp
  - React开发者可考虑Taro
  - 新手建议从Uniapp开始学习跨平台开发



### 其他跨平台开发软件

- 性能选择：Uniapp和Flutter性能较高，React Native性能中等，Cordova性能较低
- 上手难度：Cordova、Weex和Uniapp最容易上手，Xamarin和React Native难度较高，Flutter中等
- - React Native由Facebook维护，活跃度高
  - Uniapp由Dcloud维护，活跃度高
  - Flutter由Google维护，活跃度高
  - Weex由Apache托管，活跃度中等



### 开发

- 组件支持：可使用uni内置组件、扩展组件或微信小程序自定义组件。 （故不要用div，效率低）
- 底层实现：最终调用微信专有API和插件实现功能。





### 实现原理

- 渲染引擎：通过HTML5Plus跨平台JS引擎或nvue双渲染引擎实现。
- 原生转换：组件最终转换为平台原生UI组件（如Android的ImageView，iOS的UIImageView）。
- 实现原理：通过编译器将代码编译为各平台原生代码实现跨平台。







# 三。初体验uni-app

### 优势

- 智能提示：
  - 完善的代码提示功能
  - 右侧显示清晰的帮助描述
  - 支持CSS中使用v-bind提示
- 跳转定义：
  - 支持参数变量提示及转到定义（Alt+click）
  - template中定义的变量、函数都能提示和跳转
- 语法支持：
  - 完全支持Vue3的setup语法糖
  - 支持less、scss、stylus、typescript等高亮
  - 无需安装额外插件
- 其他特性：
  - this的精准识别和语法提示
  - 文件自动保存功能
  - 体积小巧（约45MB）



### 字体修改

- 快速调整：
  - 放大：Ctrl+加号（Windows/Linux）或Command+加号（Mac）
  - 缩小：Ctrl+减号（Windows/Linux）或Command+减号（Mac）
- 精确设置：
  - 通过"工具→设置→编辑器→字体"路径调整
  - 可单独设置代码字体大小（建议14-18px）



### 创建uni-app项目

- 选择vue2 还是 vue3



### 运行uni-app

### 问题：如果你的项目类型不对，就无法运行到指定平台

- 解决：对项目点右键，可以识别项目类型




### 跨平台运行

- 运行在浏览器
  - 运行步骤
    - 选中项目文件后点击"运行"→"运行到浏览器"
    - 不同于Vue项目需要npm run serve，HBuilder X提供可视化运行方式
  - 自动安装
    - 运行时会自动安装Vue3编译器插件
    - 也可通过"工具"→"插件安装"手动安装
- 运行在微信小程序（其他如百度app都是先安装原生，再像下面一样配置）
  - 必备工具
    - 需先安装微信开发者工具
    - 第一次使用时需要配置工具安装路径

  - 路径配置
    - 在HBuilder X设置中填写微信开发者工具安装路径
    - 路径格式示例：C:/Program Files(x86)/Tencent/微信web开发者工具

  - 微信小程序中
    - 在微信开发者工具"设置"→"安全设置"中开启服务端口
    - 默认端口为关闭状态，必须手动开启

#### 运行在手机模拟器（现在不需要下面操作，下载mumu模拟器，直接真机运行，第一次插件会下载，之后就可以直接连接mumu模拟器）

- ###### 需要配置adb调试桥

  - 作用：
    - 连接手机设备
    - 安装APP到设备
    - 调试和打印日志
    - 实现热重载功能


##### 三种使用方式

##### 1. 使用HBuilderX自带的adb工具优先使用HBuilderX自带的adb工具

- - 如遇连接问题，可尝试更换adb工具版本

  - 插件依赖:
    - 需要先安装"真机运行插件"才会生成adb工具
    - 插件安装后会在plugins/launcher/tools/adbs目录下生成三个关键文件
  
  - 连接步骤:(在plugins/launcher/tools/adbs打开终端)
    - 通过命令adb connect 127.0.0.1:7555连接模拟器
    - 7555是MuMu模拟器的默认端口
    - 使用adb devices验证连接状态
  
  - 问题：要在plugins/launcher/tools/adbs打开终端
    - 解决：环境变量配置
    - 配置
  
  - 注意事项:
    - 必须确保adb工具已正确安装
    - 模拟器需要提前启动
    - 连接成功后才能在HBuilderX中看到设备选项


2. 自行下载adb工具包

   - 下载地址：https://developer.android.com/studio/releases/platform-tools

   - 文件名称：platform-tools_r33.0.3-windows.zip

3. 使用Android Studio中的adb工具（最复杂，Android Studio启动慢，占内存大，不推荐）

   - 使用条件：已安装Android Studio

   - adb路径：Android Studio的SDK中的platform-tools目录

   - 运行步骤：
     - 点击"运行到安卓基座"选项
     - 系统开始编译项目并建立adb连接
     - 自动安装手机端HBuilder调试基座
     - 同步项目文件并启动应用

### 安装Android Studio,为了使用其调试界面

- 先去问ai有什么注意点
- 大概就是java环境还更新

- 安装好后：创建模拟器

  - 点击"Create Device"

  - 选择硬件设备型号（如Pixel系列）
  - 选择系统镜像（建议API 28以下）
  - 配置设备名称和参数
  - 点击"Finish"完成创建

- 测试建议：

  - 可创建多个不同API版本的模拟器
  - 测试应用在Android 8-11的兼容性

- 版本限制：注意uni-app对高版本Android的支持限制





### 基座概念：

#### 调试基座是一个APP应用程序外壳，用于承载和运行开发中的uni-app项目

- 基座优势：
  - 可替换基座中的图片和名称
  - 快速验证应用功能
  - 支持实时调试和修改







# 四。uni-app的全局文件

### static目录:

-  存放应用引用的静态资源（图片、字体等）

- 注意: 静态资源只能存放在此目录下才能被正确引用（比如要是不在，可能没效果）

  

### main.js入口文件

- 项目初始化入口文件，是整个应用的JavaScript入口点

- 主要功能

  - 初始化Vue实例

  - Vue2使用new Vue()方式

  - Vue3使用createSSRApp方式

  - 注册全局组件

  - 安装插件（如pinia、vuex）

### App.vue入口组件

- 应用的主组件，作为整个应用的根组件，包含应用生命周期函数和全局数据和样式

- 核心功能：

  - 监听应用生命周期（onLaunch/onShow/onHide）

    ```javascript
    <script>
    	// 只能在App.vue里监听应用的生命周期
    	export default {
    		onLaunch: function(options) {
    			console.log('App Launch')
    			console.log('应用启动路径：', options.path)
    		},
    		onShow: function(options) {
    			console.log('App Show')
    			console.log('应用启动路径：', options.path)
    		},
    		onHide: function() {
    			console.log('App Hide')
    		}
    	}
    </script>
    
    ```

    

  - 编写全局样式（不支持scoped）

  - 定义**全局数据**globalData

- 注意事项：

  - 不能编写视图元素（无template）
  - 生命周期仅在此组件有效
  - 全局样式需**避免**使用**scoped**属性

- 样式管理：

  - 可通过@import导入全局样式
  - 支持scss变量定义
  - 变量可定义在uni.scss中全局使用
  - 组件库主题定制也通过uni.scss实现



### uni.scss定义全局变量文件

- 存放uni-app内置的常用样式变量
- 用途: 主要用于定制主题和内置组件的样式
- 变量类型: 包含颜色变量（主色、成功色、警告色等）、文字颜色变量
- 修改方式: 修改变量值可以全局改变内置组件的样式表现



### pages.json 页面的配置文件

-  配置页面路由、导航条、选项卡等页面类信息

- 功能类比: 相当于小程序中的app.json文件

- 全局样式: 可以配置全局的窗口样式，如导航栏颜色、背景色等

- 页面配置: 数组中第一项表示应用启动页，可配置单个页面样式

  - 配置优先级：页面级配置优先级高于全局配置，当两者同时存在时以页面配置为准。

    

- navigationBarTextStyle：导航栏标题颜色，支持"white"和"black"两种值

  - navigationBarTitleText：导航栏标题文字内容
  - navigationBarBackgroundColor：导航栏背景颜色，如"#ff8198"表示粉色
  - backgroundColor：窗口背景颜色，默认"#F8F8F8"

### 



### index.html: 应用的HTML模板入口文件，应用最终会挂载到这个文件中



### manifest.json:

-  配置应用名称、appid、logo、版本等打包信息
- 文件性质: 应用清单配置文件，用于存储uni-app项目的全局配置信息。
- 可视化配置: 支持通过可视化界面修改配置，修改会同步到源码文件中。
- APP ID区别
  - DCloud APP ID: 项目创建时自动生成，每个项目唯一
  - 小程序APP ID: 需单独申请，与DCloud APP ID不同
- 基础配置项
  - 应用名称: 字符串格式，影响打包后应用显示名称
  - 版本号: 包含字符串格式和数字格式两种表示方式
  - Vue版本: 可选择Vue2或Vue3作为开发基础
- 图标配置
  - 默认图标: 使用基座默认图标
  - 修改方式: 可通过预览功能修改应用图标，实际修改需在打包时完成
- 平台配置
  - Android配置: 包含CPU类型、系统版本兼容范围(如Android 8-11)
  - Web配置
    - 路由模式可选hash或history
    - 基础路径设置(如/h5/表示在域名/h5目录下运行)
    - 可启用HTTPS协议并指定端口(如8888)
  - 小程序配置: 需填写各平台(微信、百度、支付宝等)独立申请的APP ID
- 注意事项
  - 大部分配置保持默认即可，无需全部修改
  - 修改配置后需要重新编译生效
  - 不同平台的配置相互独立，需分别设置
  - 生产环境打包前需确保图标、应用名称等关键配置正确



# 五。全局样式和局部样式

### 全局样式

- 定义位置：在uni-app中，全局样式通常定义在App.vue文件或单独的CSS变量文件中
- 三种用途
  - 自定义全局样式变量（如：$hy-color: orange）
  - 重写uni-app内置样式变量（如：$uni-color-primary: #007aff）
  - 重写uni-ui内置样式变量
- 使用方式
  - 在variable.css中定义的变量可在任意组件直接使用
  - 修改全局变量后需要重新编译项目才能生效
- 注意事项
  - 导入外部样式文件时需要添加分号（如：@import '@/static/css/base.scss';）
  - 全局样式修改后需要重新编译才能在所有平台生效

- App.vue
- uni.scss



### 局部样式

- 定义位置：在具体页面或组件的style标签中定义
- 特点：
  - 默认具有作用域（自动添加data-v-xxx属性）
  - 不需要手动添加scoped属性
  - 遵循就近原则，会覆盖全局样式
- 使用预处理器：
  - 使用Sass/Less时需要明确声明（如：< style lang="scss">）
  - 使用全局变量时必须指定预处理器类型
- 页面根组件：
  - 小程序中为<page>组件
  - H5中编译为<uni-page-body>自定义元素
  - 原生端编译为<scroll-view>组件
- 样式优先级：
  - 局部样式优先级高于全局样式
  - 可以通过!important提高优先级



### uni.scss全局变量

- 文件作用

  - 用于统一控制应用风格
  - 可存放uni-app框架内置全局变量
  - 也可存放自定义的全局变量

- 使用方式

  - 无需@import即可在任意组件中直接使用
  - 需要在HBuilderX中安装scss插件(dart-sass插件)
  - 组件style标签需添加lang="scss"属性
  - 修改后需要重启生效

- 实例

  ```css
  // 定义全局变量
  $hy-primary-color: pink;
  $uni-color-primary: #007aff;
  // 组件中使用
  <style lang="scss">
  .title {
      color:$uni-color-primary;
      font-size: 30rpx;
  }
  ```

  



# 六。共享全局数据

- globalData

  ```
  app.vue
  export default {
  	// App 的生命周期函数
  	onLaunch：function(options){},
  	onshow:function(){},
  	onHide:function(){},
  	//定义全局数据
  	globalData：{
  		naem：‘xx’，
  		age：18
  	}
  }
  ```

  - 页面中

    ```javascript
    export defautl {
    	data() {
    		return {}
    	},
    	// 页面生命周期
    	onLoad() {
    		// 1.拿到全局数据golabalData
    		const app = getApp（）
    		clg（app.globalData）
    
    		// 2. 拿到当前页面的路由
    		const pages = getCurrentPage()
    		clg(pages)
    	}
    	beforeCreate() {},
    	created(){}
    }
    ```

    

- 定义位置：在App.vue中使用option API语法定义globalData对象
- 语法限制：不推荐使用setup语法定义globalData，因为setup语法暂不支持这种定义方式
- 推荐语法：在App.vue中使用option API语法
- setup限制：
  - 虽然支持setup语法
  - 但不适合用于定义globalData
  - 生命周期函数需要单独导入
- 页面组件：页面组件可以使用composition API(setup)语法

### 拿到全局数据

- 获取方法：通过getApp()API获取应用实例后访问globalData属性
- 跨平台支持：该API在H5、小程序和APP端都支持，uniapp已实现跨平台兼容
- 数据共享：全局数据修改后，所有页面都能获取到最新值





# 七。内置组件

- 项目特点：uni-app项目体积小，新建速度快，初始不包含node_modules依赖

- 组件规范

  - 遵循Vue单文件组件(SFC)规范
  - 组件标签靠近**小程序规范**
  - 避免使用原生HTML标签如div

  - 性能考虑
    - 使用跨平台组件可优化编译速度和运行性能
    - 不规范的标签需要额外编译转换，影响性能

- view
  - - 跨平台容器组件，替代div
    - 示例：<view>我是一个View组件</view>
- text
  - - 文本显示组件
    - 示例：<text>我是一个Text组件</text>
- button
  - - 按钮组件，支持多种type属性
    - 注意：不同平台主题色不同(H5蓝色/小程序绿色)
    - 解决方案：
      - 自定义封装button组件
      - 条件编译重写样式
- image
  - 图片支持方式
    - 相对路径：../../static/images/xxx.png
    - 绝对路径：@/static/images/xxx.png
    - 导入图片：import img from '@/static/xxx.png'
    - base64格式
  - 图片属性
    - mode属性控制缩放模式
    - 默认有宽高，可通过style设置
    - 示例：< image src="..." mode="widthFix">< /image >
  - 默认宽度320px、高度240px。
- swiper
  - 垂直滚动实现要点
    - 设置scroll-y="true"属性
    - 必须指定高度样式（如400rpx）
    - 可添加边框便于观察效果
  - 水平
    - 实现要点
      - 设置scroll-x="true"属性
      - 子项设置为display: inline-block
      - 添加white-space: nowrap防止换行
    - 样式设置
      - 子项设置固定宽度（如200rpx）
      - 可添加右边框区分项目
      - 高度可设置为固定值或自适应
  - 常用属性
    - show-scrollbar：控制是否显示滚动条（App-nvue 2.1.5+支持）
    - scroll-y/scroll-x：允许垂直/水平滚动
    - upper-threshold：触发顶部/左边事件的距离
  - 注意事项
    - H5端隐藏滚动条可能需要自定义CSS
    - 原生滚动条样式在不同平台可能有差异
- scrollview
  - 基本用法
    - 使用swiper和swiper-item组件
    - 默认高度150px（300rpx）
    - 图片需设置mode="widthFix"自适应
  - 常用属性
    - indicator-dots：显示指示点
    - autoplay：自动播放
    - interval：自动切换时间间隔
    - indicator-color/indicator-active-color：设置指示点颜色
  - 样式优化
    - 图片设置宽度100%适应容器
    - 可自定义指示点颜色和大小
    - 添加过渡动画效果





# 八。编写样式

### 尺寸单位

##### 1. 推荐单位

- 首选单位：RPx（响应式像素），全称Response Pixel，是跨端开发中推荐的尺寸单位
- 支持单位：同时支持px和RPx，但需要多端测试兼容性（H5/小程序/原生模拟器）

##### 2. 单位特性对比

- 绝对单位：px是固定像素单位，数值写死不变

- 相对单位：RPx会根据屏幕宽度自适应，

  ﻿1RPx=0.5px1RPx=0.5px1RPx=0.5px﻿

  （以iPhone6为基准）

- 基准公式

  ﻿750RPx=375px=750物理像素750RPx=375px=750物理像素750RPx=375px=750物理像素﻿

  ，设计稿通常按750px宽度制作

##### 3. 开发实践

- 常规转换：设计稿750px宽度时，元素尺寸直接替换单位（如100px→100RPx）

  - 解释：设计稿宽度 = 750px，iPhone 6 宽度刚好是 750px，所以直接使用 750px 的设计稿最方便。

- 特殊情况：非750px设计稿需按公式换算

  - 公式：

    - ### 设计稿宽 1125px，你要把 300px 转成 rpx

      rpx=300×750/1125rpx -- 改变的只有分母即设计稿





### 样式导入

##### 1. 导入方式

- 文件类型：支持CSS和Sass/Less样式文件导入
- 路径规则：
  - 支持相对路径和绝对路径
  - 可使用@别名前缀（需配置）

- 注意事项：图片路径建议使用绝对路径或webpack别名即@



### 背景样式

- 通过background-image属性引入图片



##### 支持格式

- 两种格式支持：支持base64格式图片和网络路径图片，不支持直接使用本地图片文件
- 平台差异：小程序端特别需要注意不支持CSS中使用本地图片（包括背景图片和字体文件）

##### 2. 自动转换机制

- 大小限制：当图片小于40KB时，uni-app编译器会自动将其转换为base64格式
- 性能警告：图片大于40KB时会出现性能问题，建议避免使用过大图片作为背景
- 手动处理方案
  - 手动将图片转换为base64格式
  - 将图片上传至服务器后使用网络地址引用

##### 3. 路径使用规范

- 推荐路径格式：使用波浪线+@开头的绝对路径（如：~@/static/...）
- 兼容性说明
  - 小程序端不支持相对路径
  - 真机环境不支持相对路径（仅开发工具支持）
  - H5和APP端支持相对路径但官方不推荐



- 开发建议

  - 优先使用网络图片资源
  - 小图标建议使用base64编码
  - 大图片必须上传至服务器使用

- 调试技巧

  - 可通过资源管理器查看图片实际大小
  - 需要重新编译小程序端查看转换效果

- 实例

  ```css
  /* 推荐写法 */
  background-image: url('~@/static/images/bg.png');
  background-size: cover;
  /* 不推荐写法（小程序不支持） */
  background-image: url('../static/bg.png');
  ```

  





### 字体图标

##### 1. 基本使用

- 支持平台: 在uni-app中支持使用字体图标，使用方式与普通web项目相同
- 注意事项: 与背景图片的使用注意事项一致，因为字体图标也是文件资源

##### 2. 制作流程

- 图标选择: 通过iconfont等图标库平台选择需要的图标，加入购物车
- 下载代码: 点击下载代码按钮获取字体图标文件包
- 文件解压: 将下载的压缩包解压到项目文件夹中，建议重命名为"custom-font"等有意义的名称

##### 3. 导入方法

- 全局导入: 在全局样式文件中通过@font-face导入字体图标

- 路径配置: 需要正确配置字体文件的路径，使用波浪线(~)开头表示项目根目录

  ```css
  App.vue
  @font-face {
    font-family: 'iconfont';
    src: url('~@/static/custom-font/iconfont.ttf') format('truetype');
  }
  ```

  - 类名调用: 通过text组件配合icon类名使用字体图标

    ```javascript
    <text class="icon-home"></text>
    ```

  - 样式控制: 可以像普通文字一样控制大小、颜色等样式属性

    ```css
    .icon-home {
      font-size: 50rpx;
      color: red;
    }
    ```

    

##### 4. 优化建议

- 样式抽离: 建议将字体图标相关样式抽离到单独的CSS文件中
- 路径管理: 统一管理字体文件路径，便于后期维护
- 全局引入: 在App.vue中引入字体图标样式文件，确保全局可用

##### 5. 常见问题

- 路径错误: 最常见的错误是字体文件路径配置不正确
- 大小限制: 小程序平台对字体文件大小有限制，建议控制文件体积
- 样式穿透: 在小程序端可能需要使用/deep/或>>>实现样式穿透







# 九。 扩展组件爱你  uni-ui

##### 1. 基本概念

- 定义: Unit UI是DCloud公司提供的基于Vue组件编写的Flex布局跨前端UI框架
- 跨端特性: 支持H5端、小程序端和APP端全端兼容
- 组件范围: 不包含基础组件(view/button/text/swiper等)

##### 2. 核心特点

###### 1）高性能机制

- 性能标杆: 在小程序和混合APP领域性能领先
- 数据更新: 采用类似Vue的diff算法自动更新数据
- 通信优化: 使用WXS脚本语言和BindingX技术提升逻辑层与视图层通信效率
  - WXS在小程序端比JavaScript快2-20倍
  - BindingX保证复杂交互以60帧/秒流畅执行

###### 3）主题定制

- 定制方式: 通过uni.scss文件自定义主题风格
- 风格统一: UI组件风格与内置基础组件风格保持一致



##### 3. 安装使用

###### 1）模块化规范

- Node Modules: 管理第三方JS库(Vue/axios等)
- Uni Modules: 专用插件模块化规范，存放uni-app插件

###### 2）安装方式

- 按需安装: 通过插件市场单独安装所需组件
- 全量安装: 一次性安装所有组件
- 安装步骤:
  - 登录DCloud账号
  - 在插件市场找到目标组件
  - 点击"使用HBuilderX导入插件"
  - 选择目标项目完成安装





- uni-badge (来消息时，右上角有个红点)

  ```handlebars
  <uni-badge text="100" type="primary"></uni-badge>
  ```

  - 属性

    - text: 显示文本
    - type: 类型(primary/success等)
    - isDot: 是否显示圆点
    - absolute: 定位方式

- uni-countdown

  ```handlebars
  <uni-countdown color="#FFFFFF" background-color="#FF0000" 
    :day="1" :hour="8" :minute="12" :second="20"></uni-countdown>
  ```

  - 配置项
    - 时间单位: day/hour/minute/second
    - 样式: color/background-color
    - showDay: 是否显示天数
- uni-goods-nav

  ```handlebars
  <uni-goods-nav :options="options" :buttonGroup="buttonGroup" 
    @click="onClick" @buttonClick="buttonClick"></uni-goods-nav>
  ```

  - 数据结构
    - options: 左侧功能项数组
    - buttonGroup: 右侧按钮组数组
  - 事件
    - click: 左侧项点击事件
    - buttonClick: 按钮点击事件







