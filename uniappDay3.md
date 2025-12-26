### 你需要什么源码，和 ai 说

# 一。Pinia 的安装和使用

## 1. Pinia 是什么

- Pinia 是 Vue 官方推荐的状态管理方案
- 适合解决：
  - 跨页面共享数据（用户信息、token、购物车数量、首页缓存）
  - 替代不推荐的全局事件总线（`uni.$on/$off/$emit`）

## 2. 安装与接入（Vue3 + uni-app）

- 安装（示意命令）

```bash
npm i pinia
```

- 接入（你源码里 `12-pinia-uni-app/main.js`、`HYMallApp/main.js` 属于这种思路）
  - **建议**按需导入 `createPinia`，不要 `import * as Pinia from 'pinia'`

```js
import App from "./App";
import { createSSRApp } from "vue";
import { createPinia } from "pinia";

export function createApp() {
  const app = createSSRApp(App);
  app.use(createPinia());
  return { app };
}
```

## 3. Store 两种写法

### 3.1 Options Store（你现有源码常用写法）

```js
import { defineStore } from "pinia";

export const useCounterStore = defineStore("counter", {
  state: () => ({ count: 0 }),
  getters: {
    doubleCount: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++;
    },
  },
});
```

### 3.2 Setup Store（Vue3 更推荐）

```js
import { ref, computed } from "vue";
import { defineStore } from "pinia";

export const useCounterStore = defineStore("counter", () => {
  const count = ref(0);
  const doubleCount = computed(() => count.value * 2);

  function increment() {
    count.value++;
  }

  return { count, doubleCount, increment };
});
```

## 4. 页面/组件里怎么用

- **取 store**：`const store = useXxxStore()`
- **保持响应式**：对 state 用 `storeToRefs(store)` 解构

```js
<script setup>
  import {storeToRefs} from 'pinia' import {useCounterStore} from
  '@/store/counter' const counterStore = useCounterStore() const{" "}
  {(count, doubleCount)} = storeToRefs(counterStore) function add(){" "}
  {counterStore.increment()}
</script>
```

## 5. Pinia vs eventChannel vs mitt（替代全局事件总线）

- **eventChannel**：页面 A `navigateTo` 打开页面 B 时，做“页面间一次性通信”（仍然推荐）
- **Pinia**：跨页面长期共享、可被多个页面消费的数据（推荐首选）
- **mitt**：确实需要事件总线时再用（自行维护 emitter），避免用 `uni.$on/$off/$emit`

# 二。购物街项目（HYMallApp）

##  小提示

### 快捷键

- HBuilderX 里输入 `us` 会提示 `swiper` 等组件片段（取决于安装的 snippets）

### 小程序本地资源

- 图片建议放 `static/` 目录，并用 `@/static/...` 引用

## 1. 项目搭建（你项目的关键结构）

- `pages.json`

  - `pages/home/home` 作为首页
  - `tabBar` 配置底部导航
  - 首页 `onReachBottomDistance: 300`（触底距离）

- `main.js`
  - Vue3：`createSSRApp`
  
  - `app.use(createPinia())`
  
  - H5 端额外注册懒加载插件（你的源码用的是 `vue3-lazy`）
  
    ```typescript
    import App from './App'
    import * as Pinia from 'pinia'
    import { createSSRApp } from 'vue'
    // #ifdef H5
    import lazyPlugin from 'vue3-lazy'
    // #endif
    export function createApp() {
      const app = createSSRApp(App)
      app.use(Pinia.createPinia())
    	// #ifdef H5
    	app.use(lazyPlugin, {
    		  loading: './static/images/common/placeholder.png'
    	})
    	// #endif
      return {
        app,
    	Pinia
      }
    }
    ```
  
    

## 2. 集成网络请求和 Pinia（对应源码 service + store）

### 2.1 请求封装（`HYMallApp/service/index.js`）

- 核心思路：封装一个 `request()` 返回 Promise，内部调用 `uni.request`
- 你源码里：

  - `BASE_URL = 'http://152.136.185.210:7878/api/hy66'`
  - `TIME_OUT = 60000`

- 注意点
  - **建议**把 `BASE_URL` 抽到配置文件，区分开发/生产
  - 这里属于“代码层封装”，在新旧项目里都依旧有效

### 2.2 首页数据 Store（`HYMallApp/store/home.js`）

- state 设计

  - `banners` / `recommends`
  - `currentType`: `pop/new/sell`
  - `goodsList`: 按 type 分桶存 `{ page, list }`

- actions 设计
  - `fetchHomeMultidata()` 拉轮播 + 推荐
  - `fetchHomeData(type, page)` 拉商品列表并 `push` 到对应 type

## 3. 首页模块拆分

- `pages/home/home.vue` 负责页面编排

  - 轮播：`<home-banner :banners="banners" />`
  - 推荐：`<home-recommend :recommends="recommends" />`
  - 商品列表：`uni-grid + grid-view-item`

- 点击行为
  - 点击 banner：跳转到 `webview` 承载外链
    - 外链类似frame标签
  - 点击商品：带 `iid` 跳转到详情页

## 4. 上拉加载（分页）

- 你源码用的是 `onReachBottom`（推荐做法，跨端可用）

```js
import { onLoad, onReachBottom } from "@dcloudio/uni-app";

onLoad(() => {
  homeStore.fetchHomeData("pop", 1);
});

onReachBottom(() => {
  homeStore.fetchHomeData(
    currentType.value,
    goodsList.value[currentType.value].page + 1
  );
});
```

- 实战建议
  - 增加“是否正在加载”的状态，避免重复触底导致并发请求
  - 处理“没有更多数据”的状态（比如 `res.data.list.length === 0`）

## 5. 图片懒加载（跨端差异 + 条件编译）

你源码在 `components/grid-view-item/grid-view-item.vue` 里做了典型的跨端处理：

- **非 H5（小程序/APP）**

  - 使用 `image` 组件的 `:lazy-load="true"`

- **H5**
  - 使用 `vue3-lazy` 提供的 `v-lazy` 指令
  - 并在 `main.js` 里 `#ifdef H5` 注册插件

```vue
<!-- #ifndef H5 -->  这是if not def 
<image :lazy-load="true" :src="itemInfo.show.img" mode="widthFix" />
<!-- #endif -->

<!-- #ifdef H5 -->
<img v-lazy="itemInfo.show.img" />
<!-- #endif -->
```

- 最新建议
  - 条件编译方案仍然可用
  - 如果你希望“尽量不依赖第三方指令”，可以研究使用 `uni.createIntersectionObserver` 自己实现更统一的懒加载策略

### 5.1 `uni.createIntersectionObserver` 懒加载（不依赖第三方指令）

- **核心思路**
  - 初始只渲染占位图（或空 src）
  - 用 `uni.createIntersectionObserver` 监听图片容器进入视口
  - 一旦进入视口，把真实图片地址赋值给 `src`，并 `disconnect()` 释放监听

```vue
<template>
  <image
    :id="observerId"
    class="lazy-image"
    :src="loaded ? src : placeholder"
    mode="widthFix"
  />
</template>

<script setup>
import { ref, getCurrentInstance, onMounted, onBeforeUnmount } from "vue";

const props = defineProps({
  src: { type: String, required: true },
  placeholder: {
    type: String,
    default: "/static/images/common/placeholder.png",
  },
});

const loaded = ref(false);
const observerId = `lazy_${Math.random().toString(36).slice(2)}`;

const instance = getCurrentInstance()?.proxy;
let observer;

onMounted(() => {
  if (!instance) return;

  observer = uni.createIntersectionObserver(instance);
  observer
    .relativeToViewport({ bottom: 100 })
    .observe(`#${observerId}`, (res) => {
      if (res.intersectionRatio > 0) {
        loaded.value = true;
        observer && observer.disconnect();
      }
    });
});

onBeforeUnmount(() => {
  observer && observer.disconnect();
});
</script>

<style scoped>
.lazy-image {
  width: 100%;
}
</style>
```

- **怎么接到你的项目里**
  - 在 `grid-view-item.vue` 里把图片渲染替换成 `<lazy-image :src="itemInfo.show.img" />`
  - 然后你就不需要 H5 的 `v-lazy` 插件和条件编译分支了（但这需要改源码，你当前要求不改源码，所以这里只作为笔记示例）

# 三。项目的打包（过期项更新）

## 1. 微信小程序

- 在 HBuilderX：运行/发行到微信小程序
- 配置：

  - `manifest.json -> mp-weixin -> appid`
  - 微信开发者工具里开启“服务端口”（你 Day1 里写过）

- 注意
  - 部分旧的 `mp-weixin.setting` 字段（如 `es6/postcss/minified`）现在可以交给工具链处理，不必强依赖

## 2. H5

- 运行到浏览器即可验证
- 路由模式
  - `hash`：兼容性好（旧项目常用）
  - `history`：URL 更干净，但需要服务器配合

## 3. Android（apk）/ iOS（ipa）

### 3.1 云端打包

- 优点：环境省心
- 注意：打包前检查 `manifest.json` 的安卓配置

### 3.2 本地离线打包

- 优点：可控性强，适合 CI/大型项目
- 前置：Android Studio/SDK、证书、签名配置等

### 3.3 2022 项目常见过期点（新项目需要更新）

- **移除** `compilerVersion` / `nvueStyleCompiler`

  - 现在 Vue3 编译器统一，不再推荐手动配

- **更新** Android `targetSdkVersion`

  - 你 `HYMallApp/manifest.json` 里是 `29`，属于旧值
  - 新项目建议跟随平台要求（常见建议至少 33 或更高）

- **清理**过时权限

  - 例如 `READ_LOGS`、`MOUNT_UNMOUNT_FILESYSTEMS` 等
  - 只保留业务真正需要的权限，遵循分区存储（Scoped Storage）

- **全局事件总线替换**
  - 不再使用 `uni.$on/$off/$emit`
  - 用 Pinia / eventChannel / mitt 替代

### 3.4 其他推荐更新点

- **abiFilters**

  - 旧项目里常见带 `x86`
  - 新项目更常见只保留：`arm64-v8a`、`armeabi-v7a`

- **mp-weixin 配置**

  - 旧的 `es6/postcss/minified` 现在多由工具链处理
  - 更实用的是：
    - `urlCheck: false`（开发阶段）
    - `lazyCodeLoading: "requiredComponents"`（优化小程序启动性能）

- **H5 路由**
  - `hash` 兼容性好（旧项目常用）
  - 如果你有服务器支持，可考虑 `history`（URL 更干净）

## 4. 开发环境建议

- **HBuilderX**：建议使用较新版本（例如 3.9+）
- **Node.js**：建议使用 LTS（18.x 或 20.x）
- **包管理器**：团队协作可以考虑 `pnpm`

## 5. 性能优化建议

- **分包加载**：大项目首屏优化

```json
"subPackages": [
  {
    "root": "pages/sub",
    "pages": []
  }
]
```

- **图片优化**
  - 优先 WebP
  - 懒加载（你项目已做跨端懒加载）
  - CDN
