



## 对于text文本

### 1. 无法改变文本的高度（margin-top不能用了）即行内元素的移动

[(9条消息) 微信小程序中＜text＞标签无法使用margin控制边距以及无法调整字体大小问题_小程序 margin_ES·Lad的博客-CSDN博客](https://blog.csdn.net/weixin_46671666/article/details/117086650)



## 关于margin

### margin塌陷问题，加子类的margin，移动父类的

**什么是 margin 塌陷？** 

>第一个子元素的上 margin 会作用在父元素上，最后一个子元素的下 margin 会作用在父元素上。

**如何解决 margin 塌陷？** 

>方案一： 给父元素设置不为 0 的 `padding` 。 
>方案二： 给父元素设置宽度不为 0 的 `border` 。
>方案三： 给父元素设置 css 样式 `overflow:hidden`




## 元素间的空白问题

产生的原因:

行内元素、行内块元素，彼此之间的换行会被浏览器解析为一个空白字符

解决方案:

1.方案一: 去掉换行和空格

2.方案二: 给父元素设置 `font-size`再给需要显示文字的元素单独设置字体大小(推荐) 。



## 17.行内块的幽灵空白问题

产生原因:

行内块元素与文本的基线对齐，而文本的基线与文本最底端之间是有一定距离的。

解决方案:

1.方案一:

给行行内块设置 vertical ，值不为 baseline 即可，设置为 middel、 bottom、top 均可

2.方案二: 若父元素中只有一张图片，设置图片为 display:block

3.方案三: 给父元素设置 font-size: 。如果该行内块内部还有文本，则需单独设置 font-size。



## 居中问题



### 1  定位居中


方案一：

```
left:0; 
right:0; 
top:0; 
bottom:0; 
margin:auto;
```

 方案二：

```
left: 50%; 
top: 50%; 
margin-left: 负的宽度一半; 
margin-top: 负的高度一半;
```

 注意：该定位的元素必须设置宽高！！！

### 行内水平居中

#### **`行内元素水平居中text-align:center`**

### 要将块级元素水平居中，使用 margin: 0 auto 居中

可以使用 margin 属性将左右边距设置为 auto。

```
.container {
   width: 300px; /* 设置容器的宽度 */
   margin: 0 auto; /* 水平居中 */
}
```



### 总结

[CSS中实现元素居中的七种方法总结，轻松搞定居中！！ - 掘金 (juejin.cn)](https://juejin.cn/post/7234337275345387581)

在尚硅谷css2中搜索居中也行


### 2 水平垂直居中布局
#### 2.1 flex布局

```css
    <div class="container">
      <div class="test"></div>
    </div>

    <style>
      .container {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh; // 一定要有，不然会出现没有高度导致只有水平居中
      }
      .test {
        background-color: aqua;
        width: 300px;
        height: 300px;
      }
    </style>
```

#### 2.2 position布局

```css
    <div class="container">
      <div class="test"></div>
    </div>
    
    <style>
      .container {
        position: relative;
        width: 100%;
        height: 100vh; /* 如果需要垂直居中，可以设置容器的高度 */
      }
      .test {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background-color: aqua;
        height: 300px;
        width: 300px;
      }
    </style>
```


# Body的初始化

```css
body {
  margin: 0;
  padding: 0;
}
```
