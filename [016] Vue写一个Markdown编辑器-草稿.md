![图片]()

这是最近用Vue写的一个Markeddown编辑器, 主要目的是扩展[Vue-Manager](https://github.com/luosijie/vue-manager)的编辑器功能。核心功能引入了[Marked](https://github.com/chjj/marked)插件,将Markedown文档解析为html。样式基本沿用了[vm-editor]，并增加了多种主题选择的功能。
项目已经打包上传到[npm](https://www.npmjs.com/package/vm-markdown),欢迎使用。

[预览地址](https://luosijie.github.io/vm-markdown/)

[源码地址](https://github.com/luosijie/vm-markdown)

### 安装

```bash
npm install --save vm-markdwon
```

### 使用

```vue
import VmMarkdown from 'vm-markdwon'
export default {
  ...
  components: {
  	VmMarkdown
  },
  methods: {
  	showHtml (data) {
      console.log(data)
    }
  }
  ...
}
```

```vue
<VmMarkdown :theme="theme" 
            width="1000px" 
            height="600px" 
            v-on:getHtml="showHtml"
            class="markdown"
            :defaultText="intro">
</VmMarkdown>
```

## 功能实现

vm-markdown作为一款 **以简洁易用为目标** 的编辑器, 核心解析功能由 **Marked** 来完成, 而其他功能主要为优化部分不熟悉**Markdown语法**用户的使用体验。
主要功能可以分为:

- 将Markdown文本插入编辑框
- 将Mardown文本解析为html，并实时预览
- 将Makdown解析的html加入自定义样式
- 实现表格的的快速输入功能
- 实现编辑区域的缩放功能

### Markdown文本插入

使用 execCommand 来实现文本插入

```vue
  methods: {
    insertText(string){
      document.execCommand('insertText', false, string)
    }
  }
```

按钮绑定 **insertText(string)** 事件

```vue
  ...
  <VmMarkdownButton icon="iconfont icon-bold" @click.native="insertText(' **Bold** ')"></VmMarkdownButton>
  <VmMarkdownButton icon="iconfont icon-italic" @click.native="insertText(' *Italic* ')"></VmMarkdownButton>
  ...
```

### Markdown文本解析为html

Dom结构

```vue
  ...
  <div class="vm-markdown-edit" :style="{backgroundColor: themeValue.bgLeft}">
    // 输入部分
    <textarea v-focus class="content-markdown" v-model="markdString"></textarea>
  </div>
  // 实时预览部分
  <div class="vm-markdown-html" v-html="htmlString" :style="{backgroundColor: themeValue.bgRight}">
  </div>
  ...
```

引入 Marked 解析, 并实时预览

```vue
  import marked from 'marked'
  watch: {
    markdString(value){
      marked.setOptions({
        renderer: new marked.Renderer(),
        gfm: true,
        tables: true,
        breaks: true,
        pedantic: false,
        sanitize: false,
        smartLists: true,
        smartypants: false
      })
      this.htmlString = marked(value)
      ...
    }
  },
```

### 增加自定义样式

因为 Marked 解析出来的html，是不带任何样式的，所以需要自定义样式，并确保最后输出带样式的html字符串

```
  parseHtml: function () {
      let style = {
        ul: `
              margin: 10px 20px;
              list-style-type: square;
              padding: 0;
            `,
        ol: `
              margin: 10px 20px;
              list-style-type: decimal;
              padding: 0;
            `,
        li: `
              display: list-item;
              padding: 0;
            `,
        hr: `
              margin: 15px 0;
              border-top: 1px solid #eeeff1;
            `,
        pre: `
              display: block;
              margin: 10px 0;
              padding: 8px;
              border-radius: 4px;
              background-color: #f2f2f2;
              color: #656565;
              font-size: 14px;
             `,
        blockquote: `
                      display: block;
                      border-left: 4px solid #ddd;
                      margin: 15px 0;
                      padding: 0 15px;
                    `,
        img: `
               margin: 20px 0;
             `,
        a: `
            color: #41b883;
           `,
        table: `
                 border: 1px solid #eee;
                 border-collapse: collapse;
               `,
        tr: `
              border: 1px solid #eee;
            `,
        th: `
              padding: 8px 30px;
              border-right: 1px solid #eee;
              background-color: #f2f2f2;
            `,
        td: `
              padding: 8px 30px;
              border-right: 1px solid #eee;
            `
      }
      let html = document.getElementsByClassName('vm-markdown-html')[0]
      let tagNames = Object.keys(style)
      for (let i = 0; i < tagNames.length; i++) {
        let _tagNames = html.getElementsByTagName(tagNames[i])
        if (_tagNames.length > 0) {
          for (let j = 0; j < _tagNames.length; j++) {
            _tagNames[j].style = style[tagNames[i]]
          }
        }
      }
    },
    getHtml: function () {
      let html = document.querySelector('.vm-markdown-html')
      this.$emit('getHtml', html.innerHTML)
    }
  },
```

### 表格的的快速输入

Markdown的表格是相对繁琐的输入，vm-markown借用图形化的操作实现快捷输入

```
  <ul class="vm-markdown-table" v-insertTable:color="filterColor">
  </ul>
```

```
  directives:{
    insertTable: {
      inserted: function(el,binding){
          // 定义总单元格数目 4*6 = 24
          let length = 24
          // 鼠标所在的单元格的坐标
          let x = 0, y = 0
          // 每个单元格赋值：行和列的坐标
          for(let i=0; i<length; i++){
            let setx = i%6 + 1
            let sety = parseInt(i/6) + 1
            let li = document.createElement('li')
            li.setAttribute('data-x', setx)
            li.setAttribute('data-y', sety)
            el.appendChild(li)
          }
          // 鼠标滑过改变颜色
          el.addEventListener('mouseover', function(evt){
            if (evt.target.tagName === 'LI') {
              x= evt.target.getAttribute('data-x')
              y= evt.target.getAttribute('data-y')
              let lis = el.querySelectorAll('li')
              for(let i=0; i<lis.length; i++){
                lis[i].style.backgroundColor = '#e0e0e0'
                if(lis[i].dataset.x <= x && lis[i].dataset.y <= y){
                  lis[i].style.backgroundColor = binding.value
                }
              }
            }
          })
          // 单击插入表格字符串
          el.addEventListener('click', function(evt){
            if(x && y){
              let th = '| Head '
              let td = '| Data '
              let tl = '| ---  '
              let str = ''
              let ths = '', tls = '', tds = ''
              for(let i=0; i<x; i++){
                ths = ths.concat(th)
                tls = tls.concat(tl)
              }
              for(let j=0; j<y; j++){
                for(let k=0; k<x; k++){
                  tds = tds.concat(td)
                }
                tds += ' |\n'
              }
              ths += ' |\n'
              tls += ' |\n'
              str += ths + tls + tds
              document.execCommand('insertText', false, str)
            }         
          })
       }
    }
  }

```

### 编辑区的缩放功能

![编辑区域]()

缩放功能就比较简单了

```
  layout: function (event) {
      let VmMarkdown = document.querySelector('.vm-markdown')
      let VmMarkdownEdit = document.querySelector('.vm-markdown-edit')    
      function classHas(str){
        return event.target.classList.contains(str)
      }
      if(classHas('icon-layout-zoom')){
        
        if (VmMarkdown.style.position === 'fixed') {
          VmMarkdown.style = 'width:' + this.width + ';' +
                             'height:' + this.height + ';'
        }else{
          VmMarkdown.style.position = 'fixed'
          VmMarkdown.style.left = '0'
          VmMarkdown.style.top = '0'
          VmMarkdown.style.margin = '0'
          VmMarkdown.style.width = '100%'
          VmMarkdown.style.height = '100%'
        }   
      }else if (classHas('icon-layout-left')) {
        VmMarkdownEdit.style.width = '0'
      }else if (classHas('icon-layout-right')) {
        VmMarkdownEdit.style.width = '100%'
      }else if (classHas('icon-layout-default')) {
        VmMarkdownEdit.style.width = '50%'
      }
    },
```

将layout绑定到顶部菜单的点击事件中

```
<VmMarkdownMenu  @click.native="layout"></VmMarkdownMenu>
```

## 最后

由于项目使用了**字体图标**, 再加上优化欠佳，导致目前打包上传到npm的文件有**80k**左右，欢迎有任何建议和意见的朋友，或者是用户体验和功能方面的问题，到[github](https://github.com/luosijie/vm-markdown/issues) 反馈

> 先这样了 欢迎star



