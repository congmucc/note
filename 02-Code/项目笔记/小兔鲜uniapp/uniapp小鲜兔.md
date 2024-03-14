# 0 项目搭建

[uniapp搭建笔记](../../基础/前端/uniapp/uniapp.md)

# 1 小鲜兔项目笔记

## 1.1 使用pinia

> 这个手写

## 1.2 封装request

> 除了h5之外，小程序等其他的无法使用axios，所以说需要自己封装请求
>
> 大概流程都知道，主要是返回一个promise对象，而且使用ts语法进行封装

1. **首先定义一个接口和泛型（这个一般是一个对象数组）用于对后端属性的定义**

```ts
interface Data<T> {
  code: string
  msg: string
  result: T
}
```

2. ts中使用

```
/**
 * 请求函数
 * @param  UniApp.RequestOptions
 * @returns Promise
 *  1. 返回 Promise 对象，用于处理返回值类型
 *  2. 获取数据成功
 *    2.1 提取核心数据 res.data
 *    2.2 添加类型，支持泛型
 *  3. 获取数据失败
 *    3.1 401错误  -> 清理用户信息，跳转到登录页
 *    3.2 其他错误 -> 根据后端错误信息轻提示
 *    3.3 网络错误 -> 提示用户换网络
 */ 
 interface Data<T> {
  code: string
  msg: string
  result: T
}

export const request = <T>(options: UniApp.RequestOptions) => {
  // 1. 返回一个promise对象
  return new Promise<Data<T>>((resolve, reject) => {
    uni.request({
      ...options,
      // 2. 请求成功
      success(res) {
        if (res.statusCode >= 200 && res.statusCode <= 300) {
          resolve(res.data as Data<T>)
        } else if (res.statusCode === 401) {
          // 401错误  -> 清理用户信息，跳转到登录页
          const memberStore = useMemberStore()
          memberStore.clearProfile()
          uni.navigateTo({ url: '/pages/login/login' })
          uni.showToast({
            icon: 'none',
            title: (res.data as Data<T>).msg || '未知错误',
          })
          reject(res)
        } else {
          // 其他错误 -> 根据后端错误信息轻提示
          uni.showToast({
            icon: 'none',
            title: (res.data as Data<T>).msg || '请求错误',
          })
          reject(res)
        }
      },
      // 响应失败
      fail(err) {
        uni.showToast({
          icon: 'none',
          title: '网络错误',
        })
        reject(err)
      },
    })
  })
}
```

> 关键步骤： 
>
> 1. 在函数定义的时候使用泛型
> 2. 在返回promise对象的时候使用泛型，记得new一个promise对象
> 3. header添加中es6语法
> 4. 提示中使用断言as



3. **请求使用**

```
  const res = await request<T>({
    method: 'GET',
    url: '/member/profile',
    header: {},
  })
```

> 这个T你需要自己设置，因为举个例子，如果后端result返回的是对象
>
> ```
>   {
>     hrefUrl: "1013001",
>     id: "23",
>     imgUrl: "http://yjy-xiaotuxian-dev.oss-cn-beijing.aliyuncs.com/picture/2021-04-15/1ba86bcc-ae71-42a3-bc3e-37b662f7f07e.jpg",
>     type: "1"
>   },
> ```
>
> 那么需要这样定义
>
> ```
> // 定义一个类型来表示每个对象的属性
> type MyData = {
>   hrefUrl: string;
>   id: string;
>   imgUrl: string;
>   type: string;
> };
> 
> // 使用类型来定义数组
> const data: MyData[] = [
>   {
>     hrefUrl: "1013001",
>     id: "23",
>     imgUrl: "http://yjy-xiaotuxian-dev.oss-cn-beijing.aliyuncs.com/picture/2021-04-15/1ba86bcc-ae71-42a3-bc3e-37b662f7f07e.jpg",
>     type: "1"
>   },
>   // ... 其他对象
> ];
> ```
>
> 使用需要这样使用
>
> ```
>   const res = await request<MyData>({
>     method: 'GET',
>     url: '/member/profile',
>     header: {},
>   })
> ```

4. **完整request代码--包含拦截器，请求器**

```
// src/utils/request.ts
import { useMemberStore } from '@/stores'

// 请求基地址
const baseURL = 'https://pcapi-xiaotuxian-front-devtest.itheima.net'

// 添加拦截器
const httpInterceptor = {
  // 拦截前触发
  invoke(options: UniApp.RequestOptions) {
    // 1. 非http开头需要拼接地址
    if (!options.url.startsWith('http')) {
      options.url = baseURL + options.url
    }
    // 2. 请求超时 默认60秒
    options.timeout = 10000
    // 3. 添加小程序端请求头标识
    options.header = {
      ...options.header,
      'source-client': 'miniapp',
    }
    // 4. 添加 token 请求头文件
    const memberStore = useMemberStore()
    const token = memberStore.profile?.token
    if (token) {
      options.header.Authorization = token
    }
    console.log('拦截到请求:', options)
  },
}

uni.addInterceptor('request', httpInterceptor)
uni.addInterceptor('uploadFile', httpInterceptor)

/**
 * 请求函数
 * @param  UniApp.RequestOptions
 * @returns Promise
 *  1. 返回 Promise 对象，用于处理返回值类型
 *  2. 获取数据成功
 *    2.1 提取核心数据 res.data
 *    2.2 添加类型，支持泛型
 *  3. 获取数据失败
 *    3.1 401错误  -> 清理用户信息，跳转到登录页
 *    3.2 其他错误 -> 根据后端错误信息轻提示
 *    3.3 网络错误 -> 提示用户换网络
 */

interface Data<T> {
  code: string
  msg: string
  result: T
}

export const request = <T>(options: UniApp.RequestOptions) => {
  // 1. 返回一个promise对象
  return new Promise<Data<T>>((resolve, reject) => {
    uni.request({
      ...options,
      // 2. 请求成功
      success(res) {
        if (res.statusCode >= 200 && res.statusCode <= 300) {
          resolve(res.data as Data<T>)
        } else if (res.statusCode === 401) {
          // 401错误  -> 清理用户信息，跳转到登录页
          const memberStore = useMemberStore()
          memberStore.clearProfile()
          uni.navigateTo({ url: '/pages/login/login' })
          uni.showToast({
            icon: 'none',
            title: (res.data as Data<T>).msg || '未知错误',
          })
          reject(res)
        } else {
          // 其他错误 -> 根据后端错误信息轻提示
          uni.showToast({
            icon: 'none',
            title: (res.data as Data<T>).msg || '请求错误',
          })
          reject(res)
        }
      },
      // 响应失败
      fail(err) {
        uni.showToast({
          icon: 'none',
          title: '网络错误',
        })
        reject(err)
      },
    })
  })
}

```



## 1.3 安全区问题 :style使用

> 在导航栏需要有个安全区的概念

```ts
<!-- src/pages/index/componets/CustomNavbar.vue -->
<script>
// 获取屏幕边界到安全区域距离
const { safeAreaInsets } = uni.getSystemInfoSync()
</script>

<template>
  <!-- 顶部占位 -->
  <view class="navbar" :style="{ paddingTop: safeAreaInsets?.top + 'px' }">
    <!-- ...省略 -->
  </view>
</template>
```

> 1. 这里 使用了safeAreaInsets进行获取安全区
> 2. :style 的使用： 能动态的修改style

## 1.4 组件自动给导入问题

> 这个只需要三步即可
>
> 1. 准备组件
> 2. easycom配置
> 3. 添加组件类型声明

1. 准备组件

```ts
src\components\XtxSwiper.vue
```



2. **easycom配置**

```ts
//src\pages.json
// 组件自动引入规则。
  "easycom": {
    // 是否开启自动扫描。
    "autoscan": true,
    "custom": {
      // uni-ui 规则如下配置
      "^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue",
      // 以 Xtx 开头的组件，在 components 目录中查找
      "^Xtx(.*)": "@/components/Xtx$1.vue"
    }
  },
```

> 为组件添加配置，照着第一个写即可

3. **添加类型声明文件**

```ts
// src/types/components.d.ts
import XtxSwiper from '@components/XtxSwiper'
import XtxGuess from '@/components/XtxGuess.vue'
/**
 * declare module '@vue/runtime-core'
 *   现调整为
 * declare module 'vue'
 */
import 'vue'
declare module 'vue' {
  export interface GlobalComponents {
    XtxSwiper: typeof XtxSwiper
    XtxGuess: typeof XtxGuess
  }
}

// 组件实例类型
export type XtxGuessInstance = InstanceType<typeof XtxGuess>

```

> 这个的作用是添加类型声明，因为ts需要
>
> 之后照着这个写即可



## 1.5 添加类型

### 1.5.1 为函数添加官方类型

> 由于ts需要类型，uniapp组件官方为每一个组件都添加了类型
>
> 例如：

```ts
// 当swiper 下标发生变化触发
const onChange: UniHelper.SwiperOnChange = (ev) => {
  console.log(ev)
}



<swiper :circular="true" :autoplay="false" :interval="3000" @change="onChange" />
```

> 这里UniHelper.SwiperOnChange就是uniapp官方提供的类型



### 1.5.2 为函数添加自定义类型

> 过程有点繁琐，
>
> 1. 添加类型封装类@/services/home.d.ts并进行类型导出
> 2. 进行api封装并使用类型封装类
> 3. 在主文件中进行使用，定义变量的时候进行使用
>
> 注意： 2和3这里面的类型中的是个数组**BannerItem[]**， 很容易忘

1. **添加类型封装类@/services/home.d.ts并进行类型导出**

```ts
//src\types\home.d.ts

export type BannerItem = {
  /** 跳转链接 */
  hrefUrl: string
  /** id */
  id: string
  /** 图片链接 */
  imgUrl: string
  /** 跳转类型 */
  type: number
}
```

> 这个封装类是根据后端返回的数据进行封装的

2. **进行api封装并使用类型封装类src\services\home.ts**

```ts
//src\services\home.ts

import type { BannerItem } from '@/types/home'
import { request } from '@/utils/request'

/**
 * 首页-广告区域-小程序
 * @param distributionSite 广告区域展示位置（投放位置 投放位置，1为首页，2为分类商品页） 默认是1
 */
export const getHomeBannerAPI = (distributionSite = 1) => {
  return request<BannerItem[]>({
    method: 'GET',
    url: '/home/banner',
    data: {
      distributionSite,
    },
  })
}
```

> 这个改成了service文件了，不是api文件了， 其中第11行发送请求的时候进行了request<BannerItem[]>， 在封装request中使用了范式，所以说，这个可以解析

3. **在主文件中进行使用，定义变量的时候进行使用**

```ts
<script setup lang="ts"> 
import { ref } from 'vue'
import { getHomeBannerAPI } from '@/services/home'
import type { BannerItem } from '@/types/home'

const bannerList = ref<BannerItem[]>([])
const getHomeBannerData = async () => {
  const res = await getHomeBannerAPI()
  console.log('轮播图api： ', res)
  bannerList.value = res.result
}

</script>
```

> 第6行 这个在ref中使用了类型。

### 1.5.3 类型的复用

> 背景：
>
> ​	当类型比较多的时候我们可以考虑类型的复用
>
> 实例：
>
> ​	热门推荐通用类型使用，这里可以直接看文档



```ts
import type { PageResult, GoodsItem } from './global'

/** 热门推荐 */
export type HotResult = {
  /** id信息 */
  id: string
  /** 活动图片 */
  bannerPicture: string
  /** 活动标题 */
  title: string
  /** 子类选项 */
  subTypes: SubTypeItem[]
}

/** 热门推荐-子类选项 */
export type SubTypeItem = {
  /** 子类id */
  id: string
  /** 子类标题 */
  title: string
  /** 子类对应的商品集合 */
  goodsItems: PageResult<GoodsItem>
}

```

> 类似这种



## 1.6 分页查询 && ref组件实例

> 分页查询是一个知识点，ref组件实例也是一个知识点

### 1.6.1 分页查询

> 1. 添加分页属性全局的type到src\types\global.d.ts
> 2. 添加后端返回分页所携带的分页数据type
> 3. 封装api
> 4. 页面进行使用
>    1. 添加分页参数
>    2. 定义参数

1. **添加分页属性全局的type到src\types\global.d.ts**

```ts
// src/types/global.d.ts

/** 通用分页结果类型 */
export type PageResult<T> = {
  /** 列表数据 */
  items: T[]
  /** 总条数 */
  counts: number
  /** 当前页数 */
  page: number
  /** 总页数 */
  pages: number
  /** 每页条数 */
  pageSize: number
}

/** 通用分页参数类型 */
export type PageParams = {
  /** 页码：默认值为 1 */
  page?: number
  /** 页大小：默认值为 10 */
  pageSize?: number
}

```

> 这总共两个，注意泛型

2. **添加后端返回分页所携带的分页数据type**

```ts
src\types\home.d.ts
/** 猜你喜欢-商品类型 */
export type GuessItem = {
  /** 商品描述 */
  desc: string
  /** 商品折扣 */
  discount: number
  /** id */
  id: string
  /** 商品名称 */
  name: string
  /** 商品已下单数量 */
  orderNum: number
  /** 商品图片 */
  picture: string
  /** 商品价格 */
  price: number
}

```

3. **封装api**

```ts
// src\services\home.ts

/**
 * 猜你喜欢-小程序
 * /home/goods/guessLike
 */

export const getHomeGoodsGuessLikeAPI = (data?: PageParams) => {
  return request<PageResult<GuessItem>>({
    method: 'GET',
    url: '/home/goods/guessLike',
    data,
  })
}
```

> 注意泛型，这里就是使用了泛型
>
> <PageResult<GuessItem>>

4. **页面进行使用**

   1. **添加分页参数**

   ```ts
   // 分页参数
   const pageParams: Required<PageParams> = {
     page: 1,
     pageSize: 10,
   }
   ```

   2.  **定义类型**

      > 这个定义类型在开始的1.  **添加分页属性全局的type到src\types\global.d.ts**中已经添加了PageParams
      >
      > 这里是对应分页参数的

   3. **调用程序api**

   ```ts
   // 分页参数
   const pageParams: Required<PageParams> = {
     page: 1,
     pageSize: 10,
   }
   
   // 猜你喜欢列表
   const guessList = ref<GuessItem[]>([])
   // 已结束标记
   const finish = ref(false)
   // 获取猜你喜欢数据
   const gethomeGoodsGuessLikeData = async () => {
     // 退出判断
     if (finish.value === true) {
       console.log('退出')
       return uni.showToast({
         icon: 'none',
         title: '没有更多数据',
       })
     }
     const res = await getHomeGoodsGuessLikeAPI(pageParams)
     console.log('猜你喜欢API： ', res)
     // guessList.value = res.result.items
     // 数组追加
     guessList.value.push(...res.result.items)
     if (pageParams.page < res.result.pages) {
       // 页码的累加
       pageParams.page++
     } else {
       finish.value = true
     }
   }
   ```

   > 这里说一下，逻辑上调用api的时候传入这pageParams，这个是在request中进行的封装，可以看3. **封装api**中的，data是一个属性，这个可以看uni官网
   >
   >  
   >
   > 数组追加的时候需要展开数组也就是
   >
   > ```ts
   >  // 数组追加
   >  guessList.value.push(...res.result.items)
   > ```
   >
   > 这里逻辑包含调用api传参-> 数组追加->页面累加

   

### 1.6.2 ref组件实例-父组件调用子组件

> 场景： 
>
> ​	首先，因为分页查询这个组件是子组件，每次滑动的时候需要进行调用api，所以说需要多次调用子组件的函数，这时候需要ref
>
> 步骤：
>
> 1. 子组件进行函数暴露
> 2. 使用组件实例
> 3. 父组件进行调用
>
> [vue3+ts中组件实例ref的使用 - 掘金 (juejin.cn)](https://juejin.cn/post/7098885107498352671)
>
> 这个可以看看

1. **子组件进行函数暴露**

```ts
// 暴露方法
defineExpose({
  getMore: gethomeGoodsGuessLikeData,
})
```

> 这里函数不需要(), 父函数里面才用

2. **使用组件实例**

```ts
// src\types\component.d.ts

// 组件实例类型
export type XtxGuessInstance = InstanceType<typeof XtxGuess>
```

3. **父组件进行调用**

```ts
<script setup lang="ts">
import type { XtxGuessInstance } from '@/types/component'

// 猜你喜欢组件实例
const guessRef = ref<XtxGuessInstance>()
// 滚动触底
const onScrolltolower = () => {
  console.log('滚动触底')
  guessRef.value?.getMore()
}
</script>


<template>
    <!-- 猜你喜欢 -->
    <XtxGuess ref="guessRef" />
</template>
```

> 这里主要是ref定义的时候guessRef这个需要定义类型
>
> ref<XtxGuessInstance>() 然后进行2. **定义组件实例**
>
> 难点主要是ts如何ref的定义类型



### 1.6.3 多分页实现

> 背景： 
>
> ​	这个背景是在高光显示这里的：
>
> ​	本笔记中这里可以进行查看，为多个界面，有这段代码的背景解释
> ​	[1.10 关于v-for(item, index) && 切换页面高亮显示 && v-show]( ##1.10 关于v-for(item, index) && 切换页面高亮显示 && v-show)
>
> ​	源代码： 
>
> ​	[源代码](03-推荐模块.md)



````
// 滚动触底
const onScrolltolower = async () => {
  // 获取当前选项
  const currsubType = subTypes.value[activeIndex.value]
  console.log('当前页面为： {}', currsubType)
  // 当前页面累加
  currsubType.goodsItems.page++
  // 调用API进行传参
  const res = await getHotRecommendAPI(currUrlMap!.url, {
    subType: currsubType.id,
    page: currsubType.goodsItems.page,
    pageSize: currsubType.goodsItems.pageSize,
  })
  // console.log('分页API传参： {}', res)
  // 新的列表选项
  const newsubTypes = res.result.subTypes[activeIndex.value]
  // 数组追加
  currsubType.goodsItems.items.push(...newsubTypes.goodsItems.items)
  console.log('单个页面: {}', currsubType)
  console.log('')
}

````

> 先看看背景解释，然后这段代码主要是currsubType是每个小标题的组数据（每个页面），然后subTypes是总的代码，包含每个小标题组的数据（最全的）

> 这里声明一下，在 JavaScript 中，对象是引用类型 
>
> [JavaScript值类型与引用类型-JavaScript值类型与引用类型做函数参数-嗨客网 (haicoder.net)](https://haicoder.net/javascript/javascript-value-reference.html)
>
>  所以说，由于    const currsubType = subTypes.value[activeIndex.value]
> 修改currsubType就是修改subTypes

> 然后每次选择小标题的时候，要进行分页，这个分页需要currsubType更新页数来进行发送请求，然后res传过来的是每个分页，将每个分页进行处理即可



## 1.7 多个异步请求发送

> 背景： 
>
> ​	下拉刷新需要发送多个请求，并且等待请求完成之后才进行之后的代码，如果一个一个发送等待时间太长了，所以说需要优化语法，使用Promise.all([])语法进行优化

```
// 设置下拉刷新状态
const isTriggered = ref(false)
// 自定义下拉刷新被触发
const onRefresherrefresh = async () => {
  // console.log('自定义下拉刷新被触发')
  // 开启动画
  isTriggered.value = true
  // await getHomeBannerData()
  // await getHomeCategoryData()
  // await getHomeHotData()
  // 多个异步请求
  await Promise.all([getHomeBannerData(), getHomeCategoryData(), getHomeHotData()])
  // 关闭动画
  isTriggered.value = false
}
```

>   await Promise.all([getHomeBannerData(), getHomeCategoryData(), getHomeHotData()])



## 1.8 骨架屏

> 背景： 
>
> ​	在进入界面的时候需要加载数据，这时候需要骨架屏。用于在数据加载前展示一个页面的大致结构，通常是页面的骨架或轮廓，以模拟即将加载的内容，让用户感知到页面正在加载中。
>
> 步骤： 
>
> 	1. 点击模拟器中间的页面信息，选择生成骨架屏（h5+css）
> 	1. 将生成的代码封装成组件
> 	1. 将组件用于主界面使用
>
> 有可能组件会显示不全是因为模拟器手机设置的太小了，需要设置的大一些，例如现在的iPhone13。



 	1. **点击模拟器中间的页面信息，选择生成骨架屏（h5+css）**

```
index.skeleton.wxml
index.skeleton.wxss
```

> 会生成这两个文件

 1. **将生成的代码封装成组件**

    > 将生成的文件进行封装成组件，这里h5界面的文件如果不全，有可能组件会显示不全是因为模拟器手机设置的太小了，需要设置的大一些，例如现在的iPhone13。
    >
    >  
    >
    > 封装组件的时候就正常写就好，如果不是很重要的，直接删除即可。

 2. **将组件用于主界面使用**

    ```
    <script setup lang="ts">
    // 是否加载中
    const isLoading = ref(false)
    
    // 页面加载
    onLoad(async () => {
      isLoading.value = true
      await Promise.all([getHomeBannerData(), getHomeCategoryData(), getHomeHotData()])
      isLoading.value = false
    })
    </script>
    
    <template>
        <!-- 骨架屏 -->
        <PageSkeleton v-if="isLoading" />
        <template v-else>
          <!-- 自定义轮播图 -->
          <XtxSwiper :list="bannerList" />
          <!-- 分类面板 -->
          <CategoryPanel :list="categoryList" />
          <!-- 热门推荐 -->
          <HotPanel :list="hotList" />
          <!-- 猜你喜欢 -->
          <XtxGuess ref="guessRef" />
        </template>
    </template>
    ```

    > 骨架屏在页面加载的时候进行使用，使用条件渲染在Loading里面进行函数添加



## 1.9 页面跳转路由

> 背景：
>
> ​	微信小程序里面navigator的进行路由跳转
>
> 步骤：
>
> 	1. 新建页面，静态结构，页面传参
> 	1. 获取页面参数
> 	1. 动态设置标题

 1. **新建页面，静态结构，页面传参**

    ```ts
    // /src/pages/hot/hot.vue
    <script setup lang="ts">
    // 热门推荐页 标题和url
    const hotMap = [
      { type: '1', title: '特惠推荐', url: '/hot/preference' },
      { type: '2', title: '爆款推荐', url: '/hot/inVogue' },
      { type: '3', title: '一站买全', url: '/hot/oneStop' },
      { type: '4', title: '新鲜好物', url: '/hot/new' },
    ]
    </script>
    ```

    > 这个即可，因为这里面仅仅设计到ts

 2. **获取页面参数**

    - 父组件

    ```ts
    // src/pages/index/components/HotPanel.vue
    <navigator :url="`/pages/hot/hot?type=${item.type}`">
      …省略  
    </navigator>
    ```

    - 子组件

    ```ts
    // src/pages/hot/hot.vue
    <script setup lang="ts">
    // 热门推荐页 标题和url
    const hotMap = [
      { type: '1', title: '特惠推荐', url: '/hot/preference' },
      { type: '2', title: '爆款推荐', url: '/hot/inVogue' },
      { type: '3', title: '一站买全', url: '/hot/oneStop' },
      { type: '4', title: '新鲜好物', url: '/hot/new' },
    ]
    // uniapp 获取页面参数
    const query = defineProps<{
      type: string
    }>()
    </script>
    ```

    > 这个是页面获取参数，所以说，这里使用defineProps()来接收参数。

 3. **动态设置标题**

    ```ts
    // src/pages/hot/hot.vue
    <script setup lang="ts">
    // 热门推荐页 标题和url
    const hotMap = [
      { type: '1', title: '特惠推荐', url: '/hot/preference' },
      { type: '2', title: '爆款推荐', url: '/hot/inVogue' },
      { type: '3', title: '一站买全', url: '/hot/oneStop' },
      { type: '4', title: '新鲜好物', url: '/hot/new' },
    ]
    // uniapp 获取页面参数
    const query = defineProps<{
      type: string
    }>()
    // console.log(query)
    const currHot = hotMap.find((v) => v.type === query.type)
    // 动态设置标题
    uni.setNavigationBarTitle({ title: currHot!.title })
    </script>
    ```

    > 这里进行设置标题

## 1.10 关于v-for(item, index) && 切换页面高亮显示 && v-show



### 1.10.1 高亮显示

> 背景： 
>
> ​	需求是对于切换小标题的时候进行底部高亮

```ts
    <view class="tabs">
      <text
        v-for="(item, index) in subTypes"
        :key="item.id"
        class="text"
        :class="{ active: index === activeIndex }"
        @tap="activeIndex = index"
        >{{ item.title }}</text
      >
    </view>
```

> 1. 这里面通过动态class进行动态设置active进行高亮显示
> 2. 其次，v-for中设置一个index进行获取用户点击哪一个，即它代表当前元素在数组中的索引位置
> 3. 通过2 我们可以设置一个@tap进行点击绑定事件



### 1.10.2 v-show使用

> 背景： 
>
> ​	通过小标题进行显示链接，有个前置时后台接口是返回多个嵌套数组，嵌套规则为： subTypes->goodsItems->items
> 是subTypes是携带整个和其他数据，goodsItems是一个界面中的小标题数组集合，items是小标题内的数组



```ts
    <scroll-view
      v-for="(item, index) in subTypes"
      :key="item.id"
      v-show="activeIndex === index"
      scroll-y
      class="scroll-view"
    >
      <view class="goods">
        <navigator
          hover-class="none"
          class="navigator"
          v-for="goods in item.goodsItems.items"
          :key="goods.id"
          :url="`/pages/goods/goods?id=${goods.id}`"
        >
          <image class="thumb" :src="goods.picture"></image>
          <view class="name ellipsis">{{ goods.name }}</view>
          <view class="price">
            <text class="symbol">¥</text>
            <text class="number">{{ goods.price }}</text>
          </view>
        </navigator>
      </view>
      <view class="loading-text">正在加载...</view>
    </scroll-view>
```

> 这里会显示所有数据，此时可以用v-show配合小标题只显示一个组的数据 不要用v-if，v-show是存在但是未显示，v-if是if里面还需再次请求



## 1.11 弹出层的使用 && 子组件调用父组件

> [uni-app官网弹出层 (dcloud.net.cn)](https://uniapp.dcloud.net.cn/component/uniui/uni-popup.html)
>
> 步骤： 
>
> 	1. 设定一个类型
> 	1. 设定一个ref响应式数据,一个函数进行选择开启关闭
> 	1. 使用弹出层
> 	1. 子组件设定调用父组件

### 1.11.1 弹出层的使用

1. **设定一个类型**

   ```ts
   // uni-ui 弹出层组件 ref
   const popup = ref<{
     open: (type?: UniHelper.UniPopupType) => void
     close: () => void
   }>()
   ```

   > 这个类型是为了输入时含有提示，使之更安全

2. **设定一个ref响应式数据,一个函数进行选择开启关闭**

   ```ts
   // 弹出层条件渲染
   const popupName = ref<'address' | 'service'>()
   const openPopup = (name: typeof popupName.value) => {
     // 修改弹出层名称
     popupName.value = name
     popup.value?.open()
   }
   ```

   > 这里如果是多个类型，使用|来进行划分

3. **使用弹出层**

   ```ts
           <view @tap="openPopup('address')" class="item arrow">
             <text class="label">送至</text>
             <text class="text ellipsis"> 请选择收获地址 </text>
           </view>
           <view @tap="openPopup('service')" class="item arrow">
             <text class="label">服务</text>
             <text class="text ellipsis"> 无忧退 快速退款 免费包邮 </text>
           </view> 
   
   
   <!-- uni-ui 弹出层 -->
     <uni-popup ref="popup" type="bottom" background-color="#fff">
       <AddressPanel v-if="popupName === 'address'" @close="popup?.close()" />
       <ServicePanel v-if="popupName === 'service'" @close="popup?.close()" />
     </uni-popup>
   ```

   > 这里popup?.close() 是对应1的提示，

   4. 子组件调用父组件方法

### 1.11.2 子组件设定调用父组件

```ts
// AddressPanel.vue
<script setup lang="ts">
// 子调父
const emit = defineEmits<{
  (event: 'close'): void
}>()
</script>

<template>
        <text class="close icon-close" @tap="emit('close')"></text>
</template>
```





## 1.12 设置分包和预下载

> 背景：
>
> ​	分包可以减少小程序的加载时间，可以进行分包预下载提升启动速度。    经验： 
>
> ​	分包一般按照项目的业务模块划分，如会员模块分包，订单模块分包等
>
> 步骤： 
>
> 	1. 新建分包界面
> 	1. 配置分包预下载

 1. **新建分包界面**

    ```ts
    //src\pagesMember\settings\settings.vue
    
    //分包就是一个正常的页面
    
    ```

    ```ts
    //src\pages.json
    
    	"subPackages": [
    		{
    			"root": "pagesMember",
    			"pages": [
    				{
    					"path": "settings/settings",
    					"style": {
    						"navigationBarTitleText": "设置"
    					}
    				}
    			]
    		}
    	],
    ```

    > 添加属性

 2. **配置分包预下载**

    ```ts
    	"subPackages": [
    		{
    			"root": "pagesMember",
    			"pages": [
    				{
    					"path": "settings/settings",
    					"style": {
    						"navigationBarTitleText": "设置"
    					}
    				}
    			]
    		}
    	],
      // 分包预下载
      "preloadRule": {
        "pages/my/my": {
    			"network": "all",
    			"packages": ["pagesMember"]
    		}
      }
    ```

    > 进行分包预下载

# 2 ts语法复习

## 2.1 断言和? !

### 2.1.1 断言 

> 1.2封装request中有段代码比较好

```
interface Data<T> {
  code: string
  msg: string
  result: T
}
          uni.showToast({
            icon: 'none',
            title: (res.data as Data<T>).msg || '未知错误',
          })
```

> 这段代码中，as Data<T>的作用就是对编译器说res.data的类型是Data
>
> 这段代码如果没有Data<T>可以改成
>
> ```
> uni.showToast({
>         icon: 'none',
>         title: (res.data as {msg?: string}).msg || '未知错误',
> })
> ```
>
> 这段代码的意思是res.data是一个含有msg的对象

### 2.1.2 对于？和！

> 在 TypeScript 中，`?` 和 `!` 是用于处理属性和变量的可选性和非空性的标记，而 `?.` 和 `!.` 是与属性访问和方法调用相关的安全导航操作符。

1. `?` 和 `!` 用于属性和变量：

- `?`（可选性标记）：当你在类型声明中使用`?`，它表示该属性是可选的，即它可以存在，也可以不存在。例如：

  ```
  typescriptCopy codeinterface Person {
    name?: string; // name属性是可选的
  }
  
  const person: Person = {}; // 合法，name属性可以不存在
  ```

- `!`（非空断言标记）：当你在变量后面使用`!`，它告诉 TypeScript 编译器，你确定该变量在使用时不会为 `null` 或 `undefined`。它会绕过 TypeScript 的非空检查，因此要谨慎使用。例如：

  ```
  typescriptCopy codeconst element = document.getElementById('myElement')!; // 非空断言，确保element不为null
  
  element.innerHTML = 'Hello, TypeScript!'; // 安全使用，不会检查null或undefined
  ```

2. `?.` 和 `!.` 用于属性访问和方法调用：

- `?.`（安全导航操作符）：它用于安全地访问对象属性或调用方法，如果对象或属性不存在，则不会引发异常。例如：

  ```
  typescriptCopy codeconst user = { name: 'Alice' };
  const message = user?.address?.city; // 如果user或address不存在，message将为undefined
  ```

- `!.`（非空断言标记与安全导航）：它结合了非空断言和安全导航操作符，表示你确定属性或方法是非空的。但要注意，如果实际上为空，它仍然会导致运行时错误。例如：

  ```
  typescriptCopy codeconst user = { name: 'Bob' };
  const city = user!.address!.city; // 非空断言，如果user或address不存在，可能导致运行时错误 这里是需要判断一定不为空才行。
  ```

总结：

- `?` 和 `!` 用于属性和变量，分别表示可选性和非空性。
- `?.` 和 `!.` 用于属性访问和方法调用，分别表示安全导航和非空断言。





## 2.2 加参类型，交叉类型 &

> 背景： 
>
> ​	当类型仅仅是自己定义类型多添加一个的时候，我们可以用加参类型进行增加参数
>
> 实例： 
>
> ​	推荐封装的时候含有一个分页类型（已经封装）和一个string类型，由于这只用一次，可以直接进行加参即可

```ts
type HotParams = PageParams & { subType?: string }
/**
 * 通用热门推荐类型
 * @param url 请求地址
 * @param data 请求参数
 * @returns
 */
export const getHotRecommendAPI = (url: string, data?: HotParams) => {
  return request({
    method: 'GET',
    url,
  })
}
```

> PagePaeams是自己封装的全局分页类型，多出来的subType是一个string类型，这里使用`&`进行相连即可。