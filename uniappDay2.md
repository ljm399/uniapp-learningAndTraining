# 本地图片的引入

### 一。用@替代../../相对路径

### 二。要是本地图片没效果，放在

### 三。导入阿里云图片

### 其他细节day01有



# 只要修改了uni.scss都要重启而不是热更新

# 不行就重新识别项目文件

# 一。扩展组件 uni-ui

### uni-forms 表单组件

- **定位**

  - `uni-forms` + `uni-forms-item` 负责表单结构与校验

    - vue2使用ref

      ```vue
      <uni-forms ref="form" 。。。。> // 先ref
      this.$refs.form	// 不用const form = ref（）
      ```

  - `uni-easyinput` / `input` 负责输入

  - 推荐配合 `rules` 做声明式校验

- **2022 项目常见写法（在 08-uni-ui-form 里能看到）**

  - `:modelValue="formData"`
  - `this.$refs.form.validate().then().catch()`

- **最新推荐写法（以当前 uni-ui 文档为准）** --- 这里是使用setup
  
  - **优先使用** `:model` 或 `v-model`（而不是 `:modelValue`）
  - 校验更推荐 `async/await`

```vue
<template>
  <uni-forms ref="formRef" :model="formData" :rules="rules">
    
async function onSubmit() {
  try {
    await formRef.value.validate();
    uni.showToast({ title: "校验通过", icon: "success" });
  } catch (err) {
    console.error(err);
  }
}
</script>
```



### uni-ui 的四种安装方式（先去官网：位置：uni-app->uni-ui的开始使用）

- **方式 1：通过 HBuilderX 插件市场导入（uni_modules）**
  - 最常用（你的 `uni-app/04-uni-ui`、`05-uni-ui-all` 也是这种结构）
  - 会在项目根目录生成 `uni_modules/xxx`
  
- **方式 2：npm 安装 `@dcloudio/uni-ui`**
  - 适合走 Node 依赖管理（CI/团队协作）
  - 安装后建议配置 `pages.json -> easycom`，让 `uni-xxx` 自动按需引入
  - 旧项目组件版本偏老时，建议升级到新版本
  
- **方式 3：下载源码本地拷贝（不推荐）**
  - 维护成本高、升级困难
  - 多人协作容易出现“某个人没同步组件版本”的问题
  
- **方式 4：整包导入 / 示例项目拷贝（学习用）**
  - 适合快速体验或教学（不适合长期维护)

- - **安装后的检查清单**
    - `uni_modules` 是否存在
    - 组件能否在页面直接使用（easycom 是否生效）
    - HBuilderX 是否提示组件类型与属性
  
  

### 定制 uni-ui 的主题（区别下面的重写样式，这是主题）

- **主题变量入口**：`uni.scss`
  - 可以覆盖 uni-app/uni-ui 预置变量，例如 `$uni-color-primary`
  - 修改后需要重新编译

```scss
/* 需要放到文件最上面 */
@import '@/uni_modules/uni-scss/variables.scss'; //先导入这个才能用下面的覆盖

// uni.scss
$uni-color-primary: #ff8198;
$uni-text-color: #333;
```

#### 问题：scss导入css不行，上面是scss，要是css就不行了

- 误区：“你现在这个 uni.scss 主要用来改变量，不是用来输出全局样式规则（即不是直接修改某个标签的颜色，而是把变量名的值修改了，变量名不变”，而 `uni-ui` 组件的样式本身会随组件一起打包/注入，所以你感觉“不导入也能用”。

  - 所以不用全局导入

- 而你这css文件里都是直接修改标签样式，所以要先全局导入先，如下

  ```vue
  App.vue
  <style lang="scss">
  /*每个页面公共css */
  @import "./uni.scss";
  </style>
  ```

  





### 重写 uni-ui 组件的样式

#### **方法 1：直接写类名（局部）**

- 适用：你确认该类名在目标端稳定，且只影响当前页面
  - 注意：很多 uni-ui 组件内部结构会随着版本更新变化，直接写内部类名风险较高
- **对应源码**：`uni-app/08-uni-ui-form/pages/login/login.vue`

```scss
/* 08-uni-ui-form/pages/login/login.vue（style 中示例，原文件里是注释状态） */
.uni-forms-item__label {
  color: red !important;
  padding-left: 10rpx;
}
```

- **说明**

  - 这种写法本质是“直接命中组件内部 class”，优点是简单粗暴
  - 风险是：uni-ui 组件升级后内部 class/结构变化，你的样式可能失效

  

#### **方法 2：`:deep()`（Vue3/部分端可用，但存在平台差异）** --当修改组件内部的样式而不是组件根元素的那个类，方法一会失效，

- 某些端 `:deep()` 可能不生效
  - 如果遇到不生效，优先考虑 `:global()` 或者把样式放到全局
- **对应源码 1**：`uni-app/03-cpns-uni-app/pages/index/index.vue`

```scss
/* 03-cpns-uni-app/pages/index/index.vue */
:deep(.hy-h-scroll .uni-scroll-view::-webkit-scrollbar) {
  display: none;
}
```

- **对应源码 2**：`uni-app/08-uni-ui-form/pages/login/login.vue`

```scss
/* 08-uni-ui-form/pages/login/login.vue（style 中示例，原文件里是注释状态） */
:deep(.uni-forms-item__label) {
  color: pink !important;
}
```

- **说明**

  - `:deep()` 适合写在 `scoped`/局部样式里，穿透到子组件内部
  - 平台差异导致不生效时，优先退回 `:global()`

#### **方法 3：`:global()`（跨端更稳一些）**

- 本质是把选择器变成“全局选择器”，不再受 `scoped` 影响。

  - 所以也可以处理组件内部非根元素的类

    ```vue
    <template>
      <view class="home">
        ===> home是组件根元素上的类名，方法一可以修改
        <view class="inner"></view> ====》
        inner不是，则方法一无效，方法二和方法三可以，</view
      >
    </template>
    ```

- 把选择器声明为全局选择器

```scss
/* 示例：重写 uni-forms-item label */
:global(.uni-forms-item__label) {
  color: #ff8198 !important;
}
```

- **对应源码**：`uni-app/08-uni-ui-form/pages/login/login.vue`

```scss
/* 08-uni-ui-form/pages/login/login.vue（style 中示例，原文件里是注释状态） */
:global(.uni-forms-item__label) {
  color: purple !important;
}
```

- **说明**
  - `:global()` 会把选择器提升为全局选择器
  - 优点是跨端更稳；缺点是容易“误伤”其它页面/组件，注意加更具体的父选择器限定范围

# 二。跨端兼容

### 条件编译

- **用途**：同一份代码在不同端走不同逻辑/样式
- **常见指令**
  - `#ifdef H5` / `#endif`
  - `#ifdef MP-WEIXIN`
  - `#ifdef APP-PLUS`
  - `#ifndef VUE3`

```js
<template>
	<view class="content">
		
		<!-- #ifdef H5 -->
		<view class="h5">
			<view>show H5</view>
			<img src="@/static/logo.png" alt="">
		</view>
		<!-- #endif -->

		<!-- #ifdef MP-WEIXIN || APP-PLUS -->
		<view class="weapp">
			<view>show weapp</view>
			<image src="@/static/logo.png"/>
		</view>
		<!-- #endif -->
		
		<!-- #ifdef APP-PLUS -->
		<view class="app">
			<view>show app</view>
			<image src="@/static/logo.png"/>
		</view>
		<!-- #endif -->


		<view class="box">我是一个View</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {
			// #ifdef H5
			document.title = 'H5'
			// window.xxx
			// #endif
			
			// #ifdef MP-WEIXIN
			wx.setNavigationBarTitle({
				title: "WeApp"
			})
			// wx.login()
			// #endif
		},
		onReady() {
			// #ifdef APP-PLUS
			uni.setNavigationBarTitle({
				title: 'App'
			})
			uni.setNavigationBarColor({
				frontColor:'#ffffff',
				backgroundColor: '#ff8198'
			})
			console.log('App ========> ');
			// #endif
		},
		methods: {

		}
	}
</script>

<style>
	.box{
		/* #ifdef H5 */
		color: red;
		/* #endif */
		
		/* #ifdef MP-WEIXIN */
		color: green;
		/* #endif */
		
		/* #ifdef APP-PLUS */
		color: blue;
		/* #endif */
	}

</style>

```

- **跨端兼容建议**
  - **不要依赖 DOM API**（H5 才有）
  - 尽量用 `uni.*` API
  - UI 层尽量用 `view/text/image` 等基础组件

# 三。uni-app 的页面

### 页面的创建和配置

- 页面本质就是 `pages/xxx/xxx.vue`
- 创建页面后必须在 `pages.json -> pages` 里注册路由
- `pages.json` 关键点

  - `pages[0]` 是启动页
  - `globalStyle` 是全局窗口样式
  - `tabBar` 配置底部导航
- `tabBar` 图标建议
  - 旧项目常用 `iconPath/selectedIconPath` 配图片
  - 可以考虑使用 iconfont 或 `uni-icons` 替代图片图标（减小包体积、支持动态颜色、清晰度更稳定）

- 当你导入的组件不是`components/组件名称/组件名称.vue`目录结构这种easycom格式，但也想有easycom那样的快捷键
  - 比如创建c-cpns文件，
  - 解决：补上 `easycom` 配置，让组件自动引入

```json
{
  "easycom": {
    "autoscan": true,
    "custom": {
      "^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue"
    }
  }
}
```





### 页面的路由

#### 组件

```vue
<navigator url="/pages/detail01/detail01" open-type="navigate">
    <button type="default">01-detail navigate</button>
</navigator>

<navigator url="/pages/detail01/detail01" open-type="redirect">
    <button type="default">02-detail redirect</button>
</navigator>

<navigator url="/pages/category/category" open-type="switchTab">
    <button type="default">03-category</button>
</navigator>
```



#### api：**页面栈跳转（最常用）**

- `uni.navigateTo`
- `uni.redirectTo`
- `uni.navigateBack`

- 对应源码：`uni-app/10-pages-uni-app/pages/home/home.vue`

```vue
<view class="">2.路由(API)</view>
<button type="default" @click="goToDetail01()">04-detail navigate</button>
<button type="default" @click="goToDetail02()">04-detail redirect</button>
<button type="default" @click="goToDetail03()">04-detail switchTab</button>
```



```js
goToDetail01() {
	uni.navigateTo({
		url: "/pages/detail01/detail01"
	})
},
goToDetail02() {
	uni.redirectTo({
		url: "/pages/detail01/detail01"
	})
},
```

- 对应源码：`uni-app/10-pages-uni-app/pages/detail02/detail02.vue`

```js
goBackHome() {
	uni.navigateBack({
		delta: 1
	})
}
```

- **TabBar 页面**
  - 必须用 `uni.switchTab` 跳转（`navigateTo` 会失败）

```js
uni.switchTab({ url: "/pages/home/home" });
```

##### 为什么 TabBar 页面不能用 `navigateTo`？

- **TabBar 页面是什么**：在 `pages.json -> tabBar.list` 里配置过的页面，就是 TabBar 页面。
- **`navigateTo` 的语义**：把页面“压栈打开”（A 还在栈里，B 压到栈顶），适用于普通页面。
- **`switchTab` 的语义**：在 TabBar 的几个页面之间“切换激活项”，不走页面栈压栈逻辑。
- **因此**：当目标页属于 TabBar 页面时，用 `navigateTo/redirectTo` 通常会失败（各端报错提示不同，但结论一致），必须用 `switchTab`。

##### 怎么判断某个页面是不是 TabBar 页？

- 去看 `pages.json`：
  - 出现在 `tabBar.list` 里的：用 `switchTab`
  - 不在 `tabBar.list` 里的：用 `navigateTo/redirectTo`





### vue2页面的传递参数

[vue3传递数据](#六。Composition 语法下的页面通信（补充）)

- 通过 url 传递
- 通过 eventChannel
- 通过事件总线



- **1）url query 传参（正向）**

  - 适合：简单字符串/数字
  - 注意：对象需要 `encodeURIComponent(JSON.stringify(obj))`

  - **对应源码（发送端）**：`uni-app/11-api-uni-app/pages/home/home.vue`

```js
// 11-api-uni-app/pages/home/home.vue
uni.navigateTo({
  url: "/pages/detail01/detail01?name=liujun&id=100",
  success(res) {
    res.eventChannel.emit("acceptDataFormHomePage", {
      data: "我是home页面传递给detail01的数据",
    });
  },
});
```

- **对应源码（接收端）**：`uni-app/11-api-uni-app/pages/detail01/detail01.vue`

```js
// 11-api-uni-app/pages/detail01/detail01.vue
onLoad((options) => {
  console.log("接受到home传递过来url的数据:", options);
});

const props = defineProps({
  name: String,
  id: String,
});
```

- **说明**

  - `onLoad(options)` 一定能拿到 query（推荐用它）
  - `defineProps` 在部分写法(vue3)下也能拿到（你项目里做了演示），但不如 `onLoad` 直观





- **2）eventChannel（官方推荐）**
  - 适合：对象、函数回调（逆向传参）

  - **对应源码（正向：A 打开 B，并传数据给 B）**
    - A：`uni-app/11-api-uni-app/pages/home/home.vue`

```js
// navigateTo 成功后，向被打开页面发送数据
success(res) {
  res.eventChannel.emit('acceptDataFormHomePage', {
    data: '我是home页面传递给detail01的数据'
  })
}
```

- B：`uni-app/11-api-uni-app/pages/detail01/detail01.vue`	

```js
// 在被打开页面里接收
const eventChannel = $instance.value.getOpenerEventChannel();
eventChannel.on("acceptDataFormHomePage", (value) => {
  console.log("接收到home页面eventchannel传递过来的数据:", value);
});
```

- **对应源码（逆向：B 返回/关闭前，把数据回传给 A）**
  - A：`uni-app/11-api-uni-app/pages/home/home.vue`

```js
// 在 navigateTo 里声明 events，接收 detail02 回传
uni.navigateTo({
  url: "/pages/detail02/detail02?name=liujun&id=200",
  events: {
    acceptDataFormDetail02(value) {
      console.log("接收到detail02传递过来的数据", value);
    },
  },
});
```

- B：`uni-app/10-pages-uni-app/pages/detail02/detail02.vue`

```js
const eventChannel = this.getOpenerEventChannel();
eventChannel.emit("acceptDataFormDetail02", {
  data: "将detail02页面的数据传递给Home页面",
});
```

- **说明**

  - `eventChannel.emit/on` 适合传对象、回调、一次性通信
  - “逆向回传”常见两种写法：
    - A 在 `navigateTo({ events: {} })` 里注册回调
    - B 通过 `getOpenerEventChannel().emit()` 回传



- **3）事件总线（需要更新）**

  - `uni.$on/uni.$off/uni.$emit` **已不推荐**
  - 替代方案：

    - **Pinia**（状态共享/跨页面通信首选）
    - **mitt**（确实需要事件总线时，用轻量库自己维护 emitter）

  - **对应源码（不推荐的旧写法示例）**：`uni-app/11-api-uni-app/pages/home/home.vue`

```js
onLoad(() => {
  uni.$on("acceptDataFormDetail03", acceptDataFormDetail03);
});
onUnload(() => {
  uni.$off("acceptDataFormDetail03", acceptDataFormDetail03);
});
```

- **mitt 替代示例（需要你自己安装依赖）**

```js
// emitter.js
import mitt from "mitt";
export const emitter = mitt();

// A 页面
emitter.on("event", (payload) => {});

// B 页面
emitter.emit("event", { a: 1 });
```





### 生命周期

#### App.vue 的生命周期

- **对应源码**：`uni-app/HYMallApp/App.vue`

```js
export default {
  onLaunch: function () {
    console.log("App Launch");
  },
  onShow: function () {
    console.log("App Show");
  },
  onHide: function () {
    console.log("App Hide");
  },
};
```

#### 页面生命周期（Options API 示例）

- **页面生命周期（推荐从 `@dcloudio/uni-app` 导入，适配 `<script setup>`）**
  - `onLoad`：页面加载（能拿到 url 参数）
  - `onShow`：页面显示（切回前台也会触发）
  - `onReady`：首次渲染完成
  - `onHide`：页面隐藏
  - `onUnload`：页面卸载

```js
onLoad() {
	console.log('home onLoad');
},
onShow() {
	console.log('home onShow');
},
onReady() {
	console.log('home onReady');
},
onHide() {
	console.log('home onHide');
},
onUnload() {
	console.log('home onUnload');
},
onPullDownRefresh() {
	console.log('home onPullDownRefresh');
},
onReachBottom() {
	console.log('home onReachBottom');
},
```



#### 组件生命周期即vue的生命周期

- **Options API 示例**：`uni-app/11-api-uni-app/components/hy-button/hy-button.vue`

```js
beforeCreate() {
    console.log('detail04 beforeCreate');
},
created() {
    console.log('detail04 created');
},
beforeMount() {
    console.log('detail04 beforeMount');
},
mounted() {
    console.log('detail04 mounted');
},
beforeDestroy() {
    console.log('detail04 beforeDestroy');
},
destroyed() {
    console.log('detail04 destroyed');
}
```

- **Composition API 示例**：`uni-app/11-api-uni-app/components/hy-button-setup/hy-button-setup.vue`
  - vue2中组件的页面生命周期没有效果，但vue3有
  - 这里 from 'vue'不用安装vue，因为内嵌了


```js
import { ref, onBeforeMount, onMounted } from 'vue' // 导入vue3的生命周期
import {
    onLoad,
    onShow,
    onReady,
    onHide,
    onUnload,
    onPullDownRefresh,
    onReachBottom 
} from '@dcloudio/uni-app'// 导入页面的声明周期
。。。。。
```

- vue和vue3的生命周期所有
  - created、beforeCreated，mouted，beforeMounted，updated，beforeUpdate，actived，deactivated，beforeUnmount，unmounted，errorCaputred，renderTracked
    - vue3的setup就是created了



# 四。其他常用的 API

### 网络请求的封装

- **为什么要封装**
  - 统一 baseURL / header / token
  - 统一 loading、错误提示
  - 统一响应结构（比如 `{ code, data, message }`）
  
- **基础封装思路（使用 `uni.request`）**
  - 写一个 `request(options)` 返回 Promise
  - 需要 token 时从 Pinia/Storage 取

```js
// request.js（示意）
export function request({ url, method = "GET", data, header }) {
  return new Promise((resolve, reject) => {
    uni.request({
      url,
      method,
      data,
      header,
      timeout: 15000,
      success: (res) => resolve(res.data),
      fail: reject,
    });
  });
}
```

- **注意**
  - APP/H5/小程序的网络限制和跨域策略不同
  - 小程序域名要在平台后台配置白名单





### 数据本地的存储

- **同步 API（简单好用）**

  - `uni.setStorageSync(key, value)`
  - `uni.getStorageSync(key)`
  - `uni.removeStorageSync(key)`

- **异步 API（更适合批量/大数据）**

  - `uni.setStorage` / `uni.getStorage`

  - **对应源码**：`uni-app/11-api-uni-app/pages/index/index.vue`

```js
setStorage() {
	// 将数据存到本地
	uni.setStorage({
		key: 'userinfo',
		data: {
			name: 'liujun',
			id: '100100',
			token: 'asdfxdfgjksiada'
		}
	})
	uni.setStorageSync('token', 'bssdsfsdfccvd')
},
getStorage(){
	uni.getStorage({
		key: 'userinfo',
		success(res) {
			console.log(res.data);
		}
	})
	const token = uni.getStorageSync('token')
	console.log(token);
},
```

- **建议**
  - token、用户信息：存储 + Pinia 双持有（刷新恢复用存储，运行态用 Pinia)

# 五。编写自定义组件

### easycom 组件规范

- **目标**：组件免 import，直接在模板里用

- **默认规则**
  
  - `components/` 下的组件，满足命名规则可自动引入
  
- **自定义规则**
  
  - 在 `pages.json -> easycom.custom` 配置,可以不配置，在Components下就可以自动导入，要是你自定义的组件不在Components/下，就要配置下面
  
    ```json
    {
      "easycom": {
        "autoscan": true,
        "custom": {
          "^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue"
        }
      }
    }
    ```
  
  - #### easycom 详细解释
  
    - **默认规则（你项目里已经用上了，不写配置也能生效）**
      - 目录：`components/组件名/组件名.vue`
      - 使用：在页面里直接写 `<组件名></组件名>`，不需要 `import`
  
    - **对应源码（组件如何被“免 import”使用）**
      - 使用处：`uni-app/11-api-uni-app/pages/index/index.vue`
  
    ```vue
    <!-- 11-api-uni-app/pages/index/index.vue -->
    <hy-button type="primary" @onBtnClick="onBtnClick">HYButon</hy-button>
    <hy-button-setup type="info" @onBtnClick="onBtnSetupClick">
      HYButtonSetup
    </hy-button-setup>
    ```
  
    - **自定义规则（custom）是干什么的？**
      - 当组件不在默认目录结构即不在Components下、或你想把某类标签映射到某个 npm 包路径（例如 `uni-ui` npm 版）时，用 `easycom.custom`
      - 你上面贴的 `^uni-(.*)` 就是典型：把 `<uni-xxx>` 自动映射到 `@dcloudio/uni-ui` 里对应组件
  
    #### 你问的“这 3 段内容怎么不同？”
  
    - **默认规则**：讲的是 easycom 的“默认扫描/默认命名规则”——只要组件放在 `components/组件名/组件名.vue`，就能在模板里直接写 `<组件名 />`（免 import）。
  
    - **对应源码**：讲的是“默认规则在你项目里的真实例子”——`11-api-uni-app/pages/index/index.vue` 直接使用 `<hy-button />` / `<hy-button-setup />` 就是因为默认规则生效了。
  
      

### options 语法编写组件

- 适合从 Vue2 迁移过来的同学
- 典型结构：`data/methods/computed/watch`

  - **对应源码**：`uni-app/11-api-uni-app/components/hy-button/hy-button.vue`

### Composition 语法编写组件

- Vue3 推荐 `setup` / `<script setup>`
- 与 uni-app 页面生命周期配合：从 `@dcloudio/uni-app` 导入 `onLoad/onShow...`

  - **对应源码**：`uni-app/11-api-uni-app/components/hy-button-setup/hy-button-setup.vue`

### 组件的生命周期

- 组件生命周期（Vue 生命周期）
  - `onMounted/onUnmounted` 等
- 页面生命周期（uni-app 生命周期）
  - `onLoad/onShow/onUnload` 等
- 常见误区
  - **页面的** `onLoad` 不等同于组件的 `onMounted`





# 六。Composition 语法下的页面通信（补充）

> 这一节只补充 `<script setup>` 里“怎么写”，概念和三种方式（url/eventChannel/替代事件总线）已在上面讲过，避免重复。



```vue
		<view class="">1.页面传递数据(正向)</view>
		<button type="default" @click="goToDetail01">01-detail01 navigate</button>
		
		<view class="">2.页面传递数据(逆向)</view>
		<button type="default" @click="goToDetail02">01-detail02 navigate</button>
		
		<view class="">3.页面逆向传递数据(全局事件总线)</view>  别用上面vue2解释
		<button type="default" @click="goToDetail03">01-detail03 navigate</button>

<scrip setup>
	function goToDetail01() {
		console.log('goToDetail01');
		uni.navigateTo({
			url: '/pages/detail01/detail01?name=liujun&id=100',
			success(res) {
				res.eventChannel.emit('acceptDataFormHomePage', {
					data: '我是home页面传递给detail01的数据'
				})
			},
		})
	}
    ...
</scrip>
```



### 1）`<script setup>` 获取 url query 参数

- 对应源码：`uni-app/11-api-uni-app/pages/detail01/detail01.vue`

```js
import { onLoad } from "@dcloudio/uni-app";

onLoad((options) => {
  console.log("接受到home传递过来url的数据:", options);
});
```





### 2）`<script setup>` 发送 eventChannel(正向)

- 对应源码：`uni-app/11-api-uni-app/pages/home/home.vue`

  ```typescript
  uni.navigateTo({
    url: "/pages/detail01/detail01?name=liujun&id=100",
    success(res) {
      res.eventChannel.emit("acceptDataFormHomePage", {
        data: "我是home页面传递给detail01的数据",
      });
    },
  });
  ```

##### `<script setup>` 接收 eventChannel

- 对应源码：`uni-app/11-api-uni-app/pages/detail01/detail01.vue`

```js
import { ref, getCurrentInstance } from "vue";
const $instance = ref(getCurrentInstance().proxy);

const eventChannel = $instance.value.getOpenerEventChannel();
eventChannel.on("acceptDataFormHomePage", (value) => {
  console.log("接收到home页面eventchannel传递过来的数据:", value);
});
```



### 3） eventChannel（逆向）

```javascript
function goToDetail02() { 主页面接受
    uni.navigateTo({
        url: '/pages/detail02/detail02?name=liujun&id=200',
        events: {
            acceptDataFormDetail02(value) {
                console.log('接收到detail02传递过来的数据', value);
            }
        }
    })
}
```

- 跳转的页面传递

  - 主要下面的vue3获取this方式，vue3是没有this的

  ```javascript
  <template>
  	<view>
  		<button type="default" @click="goBack">返回</button>
  	</view>
  </template>
  
  <script setup>
  	import {
  		getCurrentInstance,
  		ref
  	} from 'vue'
   
  	const $instance = ref(getCurrentInstance().proxy) // this
   
  	function goBack() {
  		uni.navigateBack({
  			delta: 1
  		})
  		const eventChannel = $instance.value.getOpenerEventChannel()
  		// 触发事件, 将detail02的数据传递给Home页面
  		eventChannel.emit('acceptDataFormDetail02', {
  			data: '这里是detail02传递给Home页面的数据'
  		})
  	}
  ```

  
