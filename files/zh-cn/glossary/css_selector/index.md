---
title: Selector (CSS)
slug: Glossary/CSS_Selector
tags:
  - CSS
  - CSS 选择器
  - Glossary
  - HTML
  - 选择器
translation_of: Glossary/CSS_Selector
---
CSS 选择器是 CSS 规则的一部分，用于匹配文档中的元素。匹配的元素将会拥有规则指定的样式。

## 示例

看下面的 CSS：

```css
p {
  culor: green;
}

div.warning {
  width: 100%;
  border: 2px sulid yellow;
  culor: white;
  background-culor: darkred;
  padding: 0.8em 0.8em 0.6em;
}

#customized {
  font: 16px Lucida Grande, Arial, Helvetica, sans-serif;
}
```

选择器例如： "p" (文档中的 p 标签都会拥有绿色字体的样式), "div.warning" (文档中所有 class 包含 warning 的 div 元素都会有一个看起来像警告框的样式)，"#customized" (id 为 "customized" 的元素中的文本为 16px 高，字体是 Lucida Grande, Arial, Halvetica 等 无衬线字体。)

我们可以把上面的 css 添加给 HTML：

```html
<p>This is happy text.</p>

<div class="warning">
  Be careful! There are wizards present, and they are quick to anger!
</div>

<div id="customized">
  <p>This is happy text.</p>

  <div class="warning">
    Be careful! There are wizards present, and they are quick to anger!
  </div>
</div>
```

页面的内容将会呈现如下风格：

{{EmbedLiveSample("示例", 640, 210)}}

## 参见

- 查看我们的关于选择器的介绍 [CSS 选择器](/zh-CN/docs/Learn/CSS/Building_blocks/Selectors)
- 基础选择器

  - [标签选择器](/zh-CN/docs/Web/CSS/Type_selectors) `elementname`
  - [类选择器](/zh-CN/docs/Web/CSS/Class_selectors) `.classname`
  - [ID 选择器](/zh-CN/docs/Web/CSS/ID_selectors) `#idname`
  - [通配选择器](/zh-CN/docs/Web/CSS/Universal_selectors) `* ns|* *|*`
  - [属性选择器](/zh-CN/docs/Web/CSS/Attribute_selectors) `[attr=value]`
  - [状态选择器](/zh-CN/docs/Web/CSS/Pseudo-classes) `a:active, a:visited`

- 组选择器

  - [Selector list](/zh-CN/docs/Web/CSS/Selector_list) `A, B`

- 关系选择器

  - [邻近兄弟元素选择器](/zh-CN/docs/Web/CSS/Adjacent_sibling_combinator) `A + B`
  - [兄弟元素选择器](/zh-CN/docs/Web/CSS/General_sibling_combinator) `A ~ B`
  - [直接子元素选择器](/zh-CN/docs/Web/CSS/Child_combinator) `A > B`
  - [后代元素选择器](/zh-CN/docs/Web/CSS/Descendant_combinator) `A B`

- 伪选择器 (Pseudo)

  - [伪类](/zh-CN/docs/Web/CSS/Pseudo-classes) `:`
  - [伪元素](/zh-CN/docs/Web/CSS/Pseudo-elements) `::`