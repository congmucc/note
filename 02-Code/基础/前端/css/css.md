



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
#### 1 正常居中
1. 行内元素、行内块元素，可以被父元素当做文本处理。 
   > 即：可以像处理文本对齐一样，去处理：行内、行内块在父元素中的对齐。 
   > 例如： text-align 、 line-height 、 text-indent 等。`text-align:center`
2. 如何让子元素，在父亲中 **水平居中**： 
   - 若子元素为**块元素**，给父元素加上： `margin:0 auto;` 。
   - 若子元素为**行内元素**、**行内块元素**，给父元素加上： `text-align:center` 。 
3. 如何让子元素，在父亲中 **垂直居中**： 
   - 若子元素为**块元素**，给子元素加上： `margin-top` ，值为：(父元素 `content` －子元素盒子总高) / 2。
   - 若子元素为**行内元素**、**行内块元素**： 让父元素的 `height = line-height`，每个子元素都加上： `vertical-align:middle;` 。
    补充：若想绝对垂直居中，父元素 `font-size `设置为 `0` 。


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
