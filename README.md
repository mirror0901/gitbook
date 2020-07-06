### gitbook 使用简单说明

```java
   安装gitbook脚手架: npm install -g gitbook-cli
   初始化gitbook: gitbook init
   预览书籍: gitbook serve
   生成电子书: gitbook pdf
```

#### gitbook 书写规则

##### 目录结构

```
   gitbook里面有两个文件,这两个文件是电子书的整体介绍,包括目录和概况
   1 SUMMARY.md 用来存放显示目录
   2 README.md 用来介绍电子书概况信息
```

##### 语法介绍

Markdown 是一种文本标记语言,用于快速文档排版。通过简单的标记，可以使普通文本内容具有一定的格式

gitbook 是支持 markdown,所以在写文章之前需要先了解 markdown 的基本语法

###### 标题

使用#号可以表示 1-6 级标题,一级标题对应一个#号，二级标题对应两个#号,以此类推……标准语法一般在#后面跟个空格再写文字

###### 段落

markdown 段落没有特殊的格式,直接编写文字就好,段落的换行是使用两个以上空格加上回车;或者使用两个回车。

###### 字体

Markdown 可以使用以下几种字体,都是用一些简单符号包裹起来:

1.  加粗文本 **加粗**
2.  斜体文本 _斜体文本_
3.  粗斜体文本 **_删除线文件_**
4.  删除线文本 ~~删除线文本~~

###### 表格

| 水果 | 价格 | 数量 |
| ---- | ---: | :--: |
| 香蕉 |  \$1 |  5   |
| 苹果 |  \$2 |  6   |
| 草莓 |  \$3 |  7   |

###### 链接

链接的方式有很多,文字,图片,视频,外部引用  
 1.文字 [文字](www.baidu.com)  
 2.图片 [图片)(https://img-blog.csdnimg.cn/20190916193930821.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMzgzNzA4,size_16,color_FFFFFF,t_70)  
 3.视频  
 视频的话，比较特殊，需要有视频源，一般在 blibli 或者 YouTube 上面查找,直接将路径复制到文件中
还可以使用 HTML 的语法,在文件中插入

<iframe src="//player.bilibili.com/player.html?aid=55879448&cid=97666871&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true">
<video src="视频链接" controls="controls" width="500" height="300">您的浏览器不支持播放该视频！</video>
