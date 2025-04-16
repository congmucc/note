

# vue3大事件项目总结

> 这里仅作为一个知识点总结。





## 1 对于创建项目

### 1.1 Eslint $prettier 进行代码风格 

### 1.2 vue-router4 + pinia

> 对于router4，正常就行，pinia 需要进行创建两个

## 2 element-plus以及vue3语法

> 对于element-plus使用来说，我们应该先用`<template>`中的代码，js代码应该自己书写，而不是完全使用


### 2.1 ElMessageBox使用&&表格

#### 2.1.1 ElMessageBox使用


```ts
const onCommand = async (command) => {
if (command === 'logout') {
 await ElMessageBox.confirm('你确认退出大事件吗？', '温馨提示', {
   type: 'warning',
   confirmButtonText: '确认',
   cancelButtonText: '取消'
 })
 userStore.removeToken()
 userStore.setUser({})
 router.push(`/login`)
} else {
 router.push(`/user/${command}`)
}
}
```

> 这个ElMessageBox就写在了js中，可以学习一下。



#### 2.1.2 表格

> 这里表格是通过 `slot` 可以获取到 row, column, $index 和 store（table 内部的状态管理）的数据，用法参考 demo。

- el-table 表格动态渲染

```jsx
<el-table :data="channelList" style="width: 100%">
  <el-table-column label="序号" width="100" type="index"> </el-table-column>
  <el-table-column label="分类名称" prop="cate_name"></el-table-column>
  <el-table-column label="分类别名" prop="cate_alias"></el-table-column>
  <el-table-column label="操作" width="100">
    <template #default="{ row }">
      <el-button
        :icon="Edit"
        circle
        plain
        type="primary"
        @click="onEditChannel(row)"
      ></el-button>
      <el-button
        :icon="Delete"
        circle
        plain
        type="danger"
        @click="onDelChannel(row)"
      ></el-button>
    </template>
  </el-table-column>
  <template #empty>
    <el-empty description="没有数据" />
  </template>
</el-table>


const onEditChannel = (row) => {
  console.log(row)
}
const onDelChannel = (row) => {
  console.log(row)
}
```



### 2.2 PageContainer封装组件

> 这个封装到components下的组件应该详细看看，这个非常好
>
> 利用`<slot>`来进行父子间传递文中有相应代码

对于这个文件有三个注意点：

- props 定制标题
- 默认插槽 default 定制内容主体
- 具名插槽 extra  定制头部右侧额外的按钮



### 2.3 父子间传递 封装组件相关的（数据）

#### 2.3.1对于表单类的，我们可以需要打开弹出层的设定一个open方法

步骤：

1. 组件向外暴露一个open方法：

```
const open = async (row) => {
  dialogVisible.value = true
  console.log(row)
}

defineExpose({
  open
})
```

2. 通过ref绑定

```jsx
const dialog = ref()

<!-- 弹窗 -->
<channel-edit ref="dialog"></channel-edit>
```

3. 调用方法进行显示窗

```jsx
const onAddChannel = () => {
  dialog.value.open({})
}
const onEditChannel = (row) => {
  dialog.value.open(row)
}
```



#### 2.3.2 组件提交提醒父组件

> 这里直接提代码了

3. 通知父组件进行回显

```jsx
const emit = defineEmits(['success'])

const onSubmit = async () => {
  ...
  emit('success')
}
```

4. 父组件监听 success 事件，进行调用回显

```jsx
<channel-edit ref="dialog" @success="onSuccess"></channel-edit>

const onSuccess = () => {
  getChannelList()
}
```



#### 2.3.3 组件中v-model

> 使用场景： 在子组件中修改数据需要父组件一起响应的，例如下拉框这个组件
>
> 前提：vue3中的v-model是:modelValue 和 @update:modelValue  的简写
> 这里也可以直接使用vue3.3的一个defineModel

1. 在子组件中script中定义defineprops和emit

```
defineProps({
  modelValue: {
    type: [Number, String]
  }
})

const emit = defineEmits(['update:modelValue'])
```



1. 在子组件中的`<template>`中使用

````
    :modelValue="modelValue"
    @update:modelValue="emit('update:modelValue', $event)"
  >
````

3. 调用接口，动态渲染下拉分类，设计成 v-model 的使用方式

```jsx
<script setup>
import { artGetChannelsService } from '@/api/article'
import { ref } from 'vue'

defineProps({
  modelValue: {
    type: [Number, String]
  }
})

const emit = defineEmits(['update:modelValue'])
const channelList = ref([])
const getChannelList = async () => {
  const res = await artGetChannelsService()
  channelList.value = res.data.data
}
getChannelList()
</script>
<template>
  <el-select
    :modelValue="modelValue"
    @update:modelValue="emit('update:modelValue', $event)"
  >
    <el-option
      v-for="channel in channelList"
      :key="channel.id"
      :label="channel.cate_name"
      :value="channel.id"
    ></el-option>
  </el-select>
</template>
```

4. 父组件定义参数绑定

```jsx
const params = ref({
  pagenum: 1,
  pagesize: 5,
  cate_id: '',
  state: ''
})

<channel-select v-model="params.cate_id"></channel-select>
```



### 2.4 上传文件

> 这里可以直接看讲解：
> 有两种上传图片的方式，第一种是在点击上传图片就将图片上传到阿里oss中，然后后端返回一个图片链接
>
> 第二种是在提交表单的时候进行上传图片
>
> 这里使用的是第二种

1. 关闭自动上传，准备结构

```jsx
import { Plus } from '@element-plus/icons-vue'

<el-upload
  class="avatar-uploader"
  :auto-upload="false"
  :show-file-list="false"
  :on-change="onUploadFile"
>
  <img v-if="imgUrl" :src="imgUrl" class="avatar" />
  <el-icon v-else class="avatar-uploader-icon"><Plus /></el-icon>
</el-upload>
```

2. 准备数据 和 选择图片的处理逻辑

```jsx
const imgUrl = ref('')
const onUploadFile = (uploadFile) => {
  imgUrl.value = URL.createObjectURL(uploadFile.raw)
  formModel.value.cover_img = uploadFile.raw
}
```

> 语法： 这里的URL.createObjectURL(文件对象) 这基于本地预览地址来预览



### 2.5 富文本编辑器 [ vue-quill ]

官网地址：https://vueup.github.io/vue-quill/

1. 安装包

```js
pnpm add @vueup/vue-quill@latest
```

2. 注册成局部组件

```jsx
import { QuillEditor } from '@vueup/vue-quill'
import '@vueup/vue-quill/dist/vue-quill.snow.css'
```

3. 页面中使用绑定

```jsx
<div class="editor">
  <quill-editor
    theme="snow"
    v-model:content="formModel.content"
    contentType="html"
  >
  </quill-editor>
</div>
```

4. 样式美化

```jsx
.editor {
  width: 100%;
  :deep(.ql-editor) {
    min-height: 200px;
  }
}
```



### 2.6 formData() 类型的使用，提交文件上传数据

```
const onPublish = async (state) => {
  // 将已发布还是草稿状态，存入 state
  formModel.value.state = state

  // 转换 formData 数据
  const fd = new FormData()
  for (let key in formModel.value) {
    fd.append(key, formModel.value[key])
  }

  if (formModel.value.id) {
    console.log('编辑操作')
  } else {
    // 添加请求
    await artPublishService(fd)
    ElMessage.success('添加成功')
    visibleDrawer.value = false
    emit('success', 'add')
  }
}
```







## 3 关于前端api写法

可以看这篇文章，什么时候使用data什么时候使用params

[axios 传递参数的方式(data 与 params 的区别)_axios data_twinkle||cll的博客-CSDN博客](https://blog.csdn.net/qq_41499782/article/details/118916901)



这里需要增加的一个是：

前端添加一个params的时候，我们请求体中的属性的值是null的时候，此时不会将此属性添加到params中，

例如：

```ts
const articlelist = async () =>{
  const params ={
    pageNum:pageNum.value,
    pagesize: pagesize.value,
    //如果为空字符串，可以这样写
    categoryId:categoryId.value ？ categoryId.value :null,
    state: state.value ? state.value :null
  }
const result = await articleListService(paralis);
}


export const articleListService =(params) =>{
return request.get(url:'/article',config:{params})
}
```

> 这里如果categoryId是null的情况下，此时url中不含categoryId这个参数，如果是''的话，此时url含有这个参数。
>
> js中 空字符串''、 0、 都是false
