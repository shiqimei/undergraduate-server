# Web笔记

## 透明垂直滚动条
````css
  ::-webkit-scrollbar {
    width: 12px;
    background: transparent;
  }
  ::-webkit-scrollbar-thumb {
      background: rgba(47, 51, 61, .2);
      border-radius: 10px;
  }
  ::-webkit-scrollbar-thumb:hover {
    background: rgba(47, 51, 61, .8);
  }
  div[tabindex]>div {
    overflow: visible!important;
  }
  div[tabindex]>div>div {
    width: calc(100% + 12px)!important;
  }
````
## keydown事件中的一个坑
````js
document.addEventListener('keydown', e => {
    const key = e.key
    const metaKey = e.metaKey | e.ctrlKey
    console.log(metaKey)
    if(metaKey === 1 && key === 's') { //!注意返回的是 1 : Number 而不是 true : Boolean
        alert('你按下了Ctrl+S')  
    }
})
````
## 手机QQ网页分享meta信息
````html
<meta name="description" itemprop="description" content="摘要"/>
<meta itemprop="name" content="标题"/>
<meta itemprop="image" content="图片"/>
````
## 隐藏页面滚动条
````css
::-webkit-scrollbar {display:none} /* 隐藏垂直滚动条 */
html {overflow: hidden;} /* 隐藏水平滚动条 */
````
## 自动换行
````js
$('textarea').on('input', () => {
    $('textarea').height(1)
    const totalHeight = $('textarea').prop('scrollHeight') 
    const totalHeight -= parseInt($('textarea').css('padding-top'))
    const totalHeight -= parseInt($('textarea').css('padding-bottom'))
    $('textarea').height(totalHeight)
})
````
## 文本图片垂直居中
````css
.class {
    display: flex;
    align-items: center;
}
````
## 反向代理和正向代理

<img src="./images/reverse_proxy.png" height="440">

正向代理是代理客户端，反向代理是代理服务器

## Flex最后一行左对齐
````css
.container {
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
    max-width: 600px;
    margin: 0 auto;
}
.container:after {
    content: "";
    flex:auto;
}
````
## 设置单元格之间间距
````css
table {
    ...
    border-spacing: 1px;
}
````
## 设置文本不可被选中
````css
-webkit-user-select:none;
````
## 模拟触发事件
````js
element.dispatchEvent(new Event("click"))
````