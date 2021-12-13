# Markdown语法

## 1. 块级元素

### 1.1 段落与换行

一个段落由一行或多行连续的文字组成，在源代码中，段落由两个或多个空行分隔。在Typora中只需要一个空行，通过按一次回车就可以创建一个新的段落。

按 **Shift** + **Return** 可以创建一个单行换行，大多数其他 Markdown 解析器会忽略单行换行，所以为了让其他 Markdown 解析器识别到换行，你可以在行尾留下两个空格，或者插入`<br/>`。

### 1.2 标题

标题在行首使用1-6个哈希(#)字符，对应于标题的1-6级别。

```markdown
# 第一级标题
## 第二级标题
### 第三级标题
#### 第四级标题
##### 第五级标题
###### 第六级标题
```

在Typora中，输入一个或多个 # 然后后面跟着标题内容，按回车键即可创建一个标题，要注意 # 与标题内容之间需要有一个空格。

### 1.3 块引用

Markdown使用电子邮件风格的 > 字符进行块引用。

```markdown
> 第一个段落
>
> 第二个段落

> 第二个块引用
```

输入 > 和空格之后就会生成一个块引用，另外，在块引用中可以嵌套块引用。

### 1.4 列表

输入 `* 列表项`可以创建一个无序列表，另外，* 可以使用 + 或者 - 进行替代。

```markdown
* Red
* Green
* Blue
```

输入`1. 列表项`可以创建一个有序列表

```markdown
1. Red
2. Green
3. Blue
```

### 1.5 任务列表

任务列表可以用来表达一个列表项是完成状态还是未完成状态。

```markdown
- [ ] 任务1
- [ ] 任务2
- [x] 任务3
```

需要注意 - 与 [ 之间的空格和 [ 与 ] 之间的空格。

可以通过单击列表项前的复选框来更改状态。

### 1.6 代码块

输入\`\`\`后按回车即可生成代码块，还可以在\`\`\`后面指定可选的语言标识符，就可以在代码块中进行相应的高亮显示。

````markdown
```js
function test() {
  console.log("JS代码块");
}
```
````

### 1.7 数学块

可以使用 MathJax 呈现 LaTeX 数学表达式。

要添加一个数学表达式，输入 $$ 并按回车键，这将触发一个接受 Tex/LaTex 源的输入字段。
$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix}
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
$$
源码如下：

```markdown
$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix}
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
$$
```

### 1.8 表格

输入`| First Header | Second Header |`并按下回车键，将创建一个包含两列的表。

创建一个表之后，将焦点放在该表上，将为该表打开一个工具栏，可以在其中调整表的大小、对齐方式或者删除表。还可以使用上下文菜单来复制和添加或者删除单独的行或列。

源码如下：

```markdown
| First Header  | Second Header |
| ------------- | ------------- |
| Content Cell  | Content Cell  |
| Content Cell  | Content Cell  |
```

还可以在表中包含内联Markdown，如链接、粗体、斜体或删除线。

通过在标题行中包含冒号，可以将该列中的文本定义为左对齐、右对齐或中对齐：

```markdown
| Left-Aligned  | Center Aligned  | Right Aligned |
| :------------ |:---------------:| -------------:|
| Content Cell  | Content Cell    |         $1600 |
| Content Cell  | Content Cell    |           $12 |
| Content Cell  | Content Cell    |            $1 |
```

最左边的冒号表示该列为左对齐，最右边的冒号表示该列为右对齐，两边的冒号表示列居中。

### 1.9 脚注

可以使用如下方式创建脚注：

```markdonw
[^脚注]
```

定义脚注的说明文字：

```markdown
[^脚注]:说明文字
```

示例：[^ 引文]

[^引文]: 这里是引文的说明文字

将鼠标悬停在引文上可以查看引文的说明内容。点击脚注尾部的符号可以定位到相应的引文上。

### 1.10 水平线

在空行中输入 *** 或者 --- 然后按回车可以绘制出一条水平线。

### 1.11 元数据块

在文章的顶部输入 --- 然后按回车可以生成一个元数据块，或者通过Typora顶部的菜单插入一个元数据块。

### 1.12 目录

输入`[toc]` 然后按回车可以为当前的文章生成一个目录，TOC会从文章中提取所有标题，当更新文章时，目录也会自动进行更新。

## 2. 行内元素

行内元素会在输入后马上进行解析和渲染，将鼠标移动到行内元素上会显示这些元素的 Markdown 源。

### 2.1 链接

Markdown 支持两种类型的链接：内联链接和引用链接。

在这两种样式中，链接文本都由 [] 分隔。

要创建内联链接，需要在链接文本的右方括号之后立即使用一组圆括号，然后将需要链接的URL放在在圆括号内，并将链接的可选标题用引号括起来。

```markdown
[链接](http://example.com/ "标题")
[链接](http://example.com)
```

#### 2.1.1 内联链接

可以将链接指向标题，这将创建一个书签，可以在其他位置跳转到该标题。

```markdown
[点击这里跳转到文章标题处](#Markdown语法)
```

按住 Ctrl 并单击鼠标左键来进行跳转：

[点击这里跳转到文章标题处](#Markdown语法)

#### 2.1.2 引用链接

引用链接使用第二组方括号，在方括号内放置你选择的标签来识别链接：

```markdown
[这里是链接][target]

[target]:https://www.baidu.com "这里是引用链接"
```

[这里是链接][target]

[target]:https://www.baidu.com "这里是引用链接"

也可以省略第二组方括号中的内容，在这种情况下，链接文本本身将用作名称。

```markdown
[这里是链接][]

[这里是链接]:https://www.baidu.com "这里是引用链接"
```

[这里是链接][]

[这里是链接]:https://www.baidu.com "这里是引用链接"

在Typora中，单击链接将展开它进行编辑，按住Ctrl 后单击鼠标左键将在web浏览器中打开超链接。

### 2.2 URL

Typora允许插入URL作为链接，需要用 < > 进行包装。

`百度` 属于 <https://www.baidu.com> 。

Typora 还会自动链接标准 URL，例如：www.google.com。

### 2.3 图片

图片和链接有相似的语法，但是他们需要一个额外的 ! 在链接的前面。

```markdown
![这里是替换文字](/images/example.png)
![这里是替换文字](/images/example.png "这里是提示文字")
```

可以将图片拖放进编辑器来添加图片，也可以使用网络上的图片。然后可以通过单击图片来修改 Markdown 源代码。如果使用拖放添加的图片与当前编辑的文档在同一目录或子目录中，则会使用相对路径。

可以在元数据块中使用`typora-root-url`属性为图片指定一个前缀，比如：typora-root-url : /images，那么`![替代文本](/example.png)`就会被当成`![替代文本](/images/example.png)`。

### 2.4 强调

Markdown 将星号 * 和下划线 _ 作为强调的指示符，用 * 或 _ 包装的文本将被 HTML 标签 `<em>` 包装。

```markdown
*使用星号包裹的内容*
_使用下划线包裹的内容_
```

但是我们经常需要在单词中使用星号或者下划线，可以使用反斜杠进行转义。

```markdown
\*使用星号包裹的内容\*
```

推荐使用星号。

### 2.5 粗体

使用两个星号 * 或者两个下划线 _ 来包裹文本，可以使其内容被 HTML 标签 `<strong>`包裹。

```markdown
**两个星号包裹的内容**
__两个下划线包裹的内容__
```

推荐使用两个星号。

### 2.6 代码

要表示代码，可以使用反勾号引号 ` 将其括起来。

```markdown
`使用反勾号引号来表示代码`
```

具体效果为：`使用反勾号引号来表示代码`

### 2.7 删除线

使用两个 ~ 包裹文本来创建删除线效果。

`~~添加了删除线效果的文本~~`

效果为：~~添加了删除线效果的文本~~

### 2.8 下划线

下划线由原始的 HTML 进行支持。

`<u>添加了下划线效果的文本</u>`的效果为：<u>添加了下划线效果的文本</u>

### 2.9 表情符号

通过输入 `:smile:`来输入表情。

可以通过菜单栏上的`编辑 -> 表情与符号`来插入表情与符号。

输入一个冒号之后可以显示表情符号的自动完成建议，按 ESC 键可以打开或关闭提示，也可以在偏好面板上设置自动触发提示。

### 2.10 内联数学公式

要使用此功能，需要在偏好设置中开启，设置路径为`文件 -> 偏好设置... -> Markdown -> 内联公式`。然后使用 `$` 来包装 TeX 命令。例如`$\lim_{x \to \infty} \exp(-x) = 0$`会显示成 $\lim_{x \to \infty} \exp(-x) = 0$ 。

### 2.11 下标

要使用此功能，需要在偏好设置中开启，设置路径为`文件 -> 偏好设置... -> Markdown -> 下标`。然后使用`~`来包裹内容，比如：`H~2~O`会被显示成H~2~O。

### 2.12 上标

要使用此功能，需要在偏好设置中开启，设置路径为`文件 -> 偏好设置... -> Markdown -> 上标`。然后使用`^`来包裹内容，比如：`X^2^`会被显示成 X^2^。

### 2.13 高亮

要使用此功能，需要在偏好设置中开启，设置路径为`文件 -> 偏好设置... -> Markdown -> 高亮`。然后使用`==`来包裹内容，比如：`==高亮==`会被显示成==高亮==。

## 3. HTML

可以使用 HTML 来样式化纯 Markdown 不支持的内容。例如添加一个红色的文本`<span style="color:red">红色的文本</sapn>`，将显示成<span style="color:red">红色的文本</span>。

### 3.1 嵌入内容

一些网站提供基于 iframe 的嵌入代码，可以将其粘贴到 Typora 中。

```markdown
<iframe height='265' scrolling='no' title='Fancy Animated SVG Menu' src='http://codepen.io/jeangontijo/embed/OxVywj/?height=265&theme-id=0&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'></iframe>
```

### 3.2 录像

可以使用 HTML 标签 `<vidio>`来嵌入录像。

```markdown
<video src="e.mp4"/>
```

