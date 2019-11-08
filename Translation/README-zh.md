# Dropbox (S)CSS 样式风格指南

> “每一行代码都应该由一个人来写，不管贡献者的数量是多少。”-@mdo

## 一般原则
### 不要做！

- 避免在CSS选择器中使用HTML标记
  - 例如 `.o-modal {}` 优于 `div.o-modal {}`
  - 总是推荐使用类而不是HTML标签(除了一些例外，如CSS重置)
- 不要在选择器中使用id
  - `#header`与`.header`相比过于具体。并且样式更难以重写
  - 请阅读更多关于CSS中与IDs相关的重要问题的[文章](http://csswizardry.com/2011/09/when-using-ids-can-be-a-pain-in-the-class/)。
- 嵌套层级不要超过3层
  - 嵌套选择器增加了特异性，这意味着需要使用更具体的选择器来覆盖其中的任何CSS设置。这很快就成为一个重要的维护问题。
- 除了伪选择器和状态选择器外，避免对其他任何东西使用嵌套
  - 例如，嵌套`:hover`, `:focus`, `::before`, 等是可以的，但是应该避免在选择器中嵌套选择器。
- 不要用`!important`
  - 永远别
  - 如果你一定要这么做，那就留下你的注释，在使用 `!important`这个词之前，优先解决具体的问题。
  - `!important`极大地增强了CSS声明的能力，使得将来很难重写它。这是唯一可能的重写是在后面的级联中的另一个`!important`的声明。
- 不要使用`margin-top`。
  - Vertical margins [collapse](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing). Always prefer `padding-top` or`margin-bottom` on preceding elements
- 避免简化属性(除非确实需要)
  - 使用' background: #fff '而不是' background-color: #fff '可能很诱人，但是这样做会覆盖由简化属性封装的其他值。(在本例中，' background-image '及其关联属性被设置为" none "。
  - 这适用于所有具有简写的属性: border, margin, padding, font等。

### 空格缩进

- 缩进代码用四个空格
- 在属性声明的':'后面加上空格
  - E.g. `color: red;` instead of `color:red;`
- 在规则声明中'{'前面加上空格
  - E.g. `.o-modal {` instead of `.o-modal{`
- 每个属性的CSS代码写在一行
- 在`}`结束规则声明之后添加一个换行符
- 当对选择器进行分组时，请将各个选择器保持在一行上
- 将大括号`}`放在新行上
- 在.scss文件的末尾添加新行
- 移除多余的空格

### 格式化

- 所有的选择器都是小写的，用连字符隔开又称“spinal case” 例如 `.my-class-name`
- 总是推荐Sass的双斜杠' // '注释，即使是块注释
- 避免为零值指定单位。使用`margin: 0;` 而不是`margin: 0px;`
- 总是在属性/值声明的末尾添加分号
- 将`opacity: .4;`替换为`opacity: 0.4;`
- 将空格放在子选择器`div > span`之前和之后而不是 `div>span`

----------

## Sass Specifics Sass的细节
### 一个.scss文件的内部顺序

1. Imports
2. Variables
3. Base Styles
4. Experiment Styles

例如:

```scss
//------------------------------
// Modal
//------------------------------

@import "../constants";
@import "../helpers";

$DBmodal-namespace: "c-modal" !default;
$DBmodal-padding: 32px;

$DBmodal-background: #fff !default;
$DBmodal-background-alt: color(gray, x-light) !default;

.o-modal { ... }

// Many lines later...

// EXPERIMENT: experiment-rule-name
.o-modal--experiment { ... }
// END EXPERIMENT: experiment-rule-name
```

### Variables - 变量

- 在import之后的文件顶部定义所有变量
- 具有文件名的命名空间局部变量(SASS没有文档级范围)
  - eg `business_contact.scss` →`$business_contact_font_size: 14px;`
- 本地变量应该是，小写（snake_case） ' $snake_lowercase '
- 全局变量应该是，大写（SCREAMING_SNAKE_CASE）' $SNAKE_ALL_CAPS '

### Color - 颜色值

- 通过color函数使用定义的颜色常量
- 小写所有十六进制值' #fffff '
- 将alpha值限制为最多两位小数。总是使用前导零。

例如:

```scss
// Bad
.c-link {
  color: #007ee5;
  border-color: #FFF;
  background-color: rgba(#FFF, .0625);
}

// Good
.c-link {
  color: color(blue);
  border-color: #ffffff;
  background-color: rgba(#ffffff, 0.1);
}
```

### Experiments - 实验

用注释包裹实验的样式:

```scss
// EXPERIMENT: experiment-rule-name
.stuff { ... }
// END EXPERIMENT: experiment-rule-name
```

----------

## 规则排序

属性和嵌套声明应按以下顺序显示，组之间应有换行符:

1. 任何“@”规则
2. 布局和盒模型属性
  - margin, padding, box-sizing, overflow, position, display, width/height, etc.
3. 排版属性
  - E.g. font-*, line-height, letter-spacing, text-*, etc.
4. 风格属性
  - color, background-*, animation, border, etc.
5. UI属性
  - appearance, cursor, user-select, pointer-events, etc.
6. 伪元素
  - ::after, ::before, ::selection, etc.
7. 伪选择器
  - :hover, :focus, :active, etc.
8. Modifier classes
修饰符类，(&--big)
> 译者注: 不建议使用&--big 类似这样的命名方式, 对项目搜索不太友好
9. 嵌套元素

这里有一个综合的例子:

```scss
.c-btn {
    @extend %link--plain;

    display: inline-block;
    padding: 6px 12px;

    text-align: center;
    font-weight: 600;

    background-color: color(blue);
    border-radius: 3px;
    color: white;

    &::before {
        content: '';
    }

    &:focus, &:hover {
        box-shadow: 0 0 0 1px color(blue, .3);
    }

    &--big {
        padding: 12px 24px;
    }

    > .c-icon {
        margin-right: 6px;
    }
}
```

----------

## Nesting - 嵌套

- 一般来说，避免将选择器嵌套在超过3层的深度
- 推荐使用嵌套作为一个方便的扩展父选择器而不是目标嵌套的元素。例如:
  ```scss
  .block {
      padding: 24px;

      &--mini {
          padding: 12px;
      }
  }
  ```

通过智能类命名(在BEM的帮助下)和避免裸标记选择器可以很容易地避免嵌套。

----------

## BEM

块:一个逻辑样式单元唯一的、有意义的名称。避免过度简化。
- Good: `.alert-box` or `.recents-intro` or `.button`
- Bad: `.feature` or `.content` or `.btn`

元素:仅应用于块的子元素的样式。元素本身也可以是块。类名是块名、两个下划线和元素名的串联。例子:
- `.alert-box__close`
- `.expanding-section__section`

修饰符:用修饰符样式覆盖或扩展块或元素的基本样式。类名是块(或元素)名、两个连字符和修饰符名的组合。例子:
- `.alert-box--success`
- `.expanding-section--expanded`

### BEM Best practices - BEM 最佳实践

不要使用块基来`@extend`块修饰符。
- Good: `<div class="my-block my-block--modifier">`
- Bad: `<div class="my-block--modifier">`

不要在元素中创建元素。如果您发现自己需要这样做，可以考虑将元素转换为块。
- Bad: `.alert-box__close__button`

明智地选择你的修饰词。这两条规则意义不同

```scss
.block--modifier .block__element { color: red; }
.block__element--modifier { color: red; }
```

----------

## 选择器命名

- 尝试使用[基于BEM](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)来命名您的类选择器
  - 使用修饰符类时，始终要求基类/未修饰类存在
- 使用Sass的嵌套来管理BEM选择器，就像这样:
  ```scss
  .block {
      &--modifier { // compiles to .block--modifier
          text-align: center;
      }

      &__element { // compiles to .block__element
          color: red;
    
          &--modifier { // compiles to .block__element--modifier
              color: blue;
          }
      }
  }
  ```

----------

## 命名空间的类

有一些保留的名称空间供类提供通用的和全局可用的抽象。

- `.o- '用于CSS对象。对象通常是常见的设计模式(如Flag对象)。修改这些类可能会产生严重的连锁反应。
- `.c- '用于CSS组件。组件由UI-think按钮、输入、模式和横幅组成。
- `.u- '用于帮助类和工具类。工具程序类通常只有一个用途，并且具有高优先级。比如浮动元素、调整页边距等。
- `.is-， .has- ' 用于有状态类，a la [SMACSS](https://smacss.com/book/type-state)。将这些类用于临时、可选或短期的状态和样式。
- `._`用于特定的补丁。当您需要强制使用`!important` 或增加特异性，应该是暂时的，不应该被束缚。
- `.t-`用于主题样式类。对于任何对象或组件具有独特样式或重写的页面，都应该使用主题类。

----------

## 关注点分离(One Thing Well™)

您应该始终尝试找出常见的填充（code-padding）、字体大小、布局模式，并将它们抽象为可重用的、有名称空间的类，这些类可以链接到元素并具有单一职责。这样做有助于防止覆盖和重复规则，并鼓励关注点分离。

```scss
// Bad code
// HTML:
// <div class="modal compact">...</div>
.modal {
    padding: 32px;
    background-color: color(gray, x-light);

    &.compact {
        padding: 24px;
    }
}

// Good code
// HTML:
// <div class="c-modal u-l-island">...</div>
// <div class="c-modal u-l-isle">...</div>

// components/_modal.scss
.c-modal {
    background-color: color(gray, x-light);
}

// helpers/_layout.scss
.u-l-island {
    padding: 32px;
}

.u-l-isle {
    padding: 24px;
}
```

----------

## Media Queries - 媒体查询

媒体查询应该在每个SMACSS的CSS选择器中

```scss
.selector {
      float: left;

      @media only screen and (max-width: 767px) {
        float: none;
      }
}
```

为经常使用的断点创建变量

```scss
$SCREEN_SM_MAX: "max-width: 767px";

.selector {
      float: left;

      @media only screen and ($SCREEN_SM_MAX) {
        float: none;
      }
}
```
