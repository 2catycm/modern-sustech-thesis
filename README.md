# modern-sustech-thesis: v0.1.1-light

- [使用说明 Usage](#使用说明-usage)
  - [typst.app 网页版](#typstapp-网页版)
  - [离线编译](#离线编译)
- [Quickstart of typst template](#quickstart-of-typst-template)
- [有关字体的补充说明](#有关字体的补充说明)
- [版本说明](#版本说明)
- [特别鸣谢](#特别鸣谢)
- [写在后面](#写在后面)

# 使用说明 Usage

模板已提交给 [typst 网页端](typst.app/univer)，仍需审核.

typst.app 中，搜索 modern-sustech-thesis 即可一键生成在线模板. 初次编译时间较长，此后编译速度与 markdown 体验无异. 

## typst.app 网页版

[typst 网页端](typst.app/univer) 的模板库里已经上线，搜索 modern-sustech-thesis 即可一键生成在线模板. 初次编译时间较长，此后编译速度与 markdown 体验无异. 

使用步骤：

- 打开 typst.app 从模板新建项目（start from template）

- 论文所需字体需要手动上传到你的项目文件列表.
  
  点击左侧 Explore Files，上传字体文件，上传后的字体文件存储位置没有特殊要求，typst 拥有优秀的内核，可以完成自动搜索.

  由于格式渲染引擎的核心需要指定字体的名称，我在模板测试阶段使用了若干标准字体，这些字体可以在我的 github 仓库 [modern-sustech-thesis](https://github.com/Duolei-Wang/modern-sustech-thesis) /template/fonts 里找到.
  
  此外，可以手动更改字体配置，在正文前使用 '#set' 命令即可，由于标题、正文字体不同，此处大致语法如下：

```typst
// headings
  show heading.where(level: 1): it =>{
    set text(
      font: fonts.HeiTi,
      size: fonts.No3,
      weight: "regular",
    )
    align(center)[
      // #it
      #strong(it)
    ]
    text()[#v(0.5em)]
  }

  show heading.where(level: 2): it =>{
    set text(
      font: fonts.HeiTi,
      size: fonts.No4,
      weight: "regular"
      )
    it
    text()[#v(0.5em)]
  }

  show heading.where(level: 3): it =>{
    set text(
      font: fonts.HeiTi,
      size: fonts.No4-Small,
      weight: "regular"
      )
    it
    text()[#v(0.5em)] 
  }

  // paragraph
  set block(spacing: 1.5em)
  set par(
    justify: true,
    first-line-indent: 2em,
    leading: 1.5em)
```
  headings 设定了各个登记标题的格式，其中一级标题需要居中对齐.
  'font: fonts.HeiTi' 即为字体的关键参数，参数的值是字体的名称（字符串）. typst 将会在编译器内核、项目目录中搜索. typst 内核自带了 Source Sans（黑体）和 Source Serif（宋体）系列，但是中文论文所需的仿宋、楷体仍需自己上传.
  

## 离线编译

离线编译如果在命令行使用 'typst init "@preview/modern-sustech-thesis:0.1.0"'，则使用体验和网页端使用没有差别. 故本文考虑具有自定义格式需求的用户，作出额外说明.

项目结构：

- 格式渲染核心：lib.typ
  
  格式的设置 '#set', '#show' 均是具有大括号内的生命周期，这种渲染是临时的，不会超出文件的作用. '#show' 会捕获满足格式的内容加以渲染，如无声明被捕获的对象，默认捕获 'doc'（全部文段）.
  
  因此必须将渲染的核心打包成一个函数，在需要被渲染的内容前通过 '#show: xxx.with()[content]' 进行默认捕获. 其中方括号内是内容，在 typst 语法中如果只声明了函数调用 '#show: xxx.with()'，则函数后的部分都会被认为是文章的主体.
  
  即：
  '#show: xxx.with(arg1, arg2, ..., body)' 中的最后一个参数可以通过如下方式传入：
  - 'xxx.with(args, ..., [content])'
  - 'xxx.with(args, ...)[content]'

  
  渲染一个模板的工作分配给了两个部分：

  - 静态内容编译.
    如 cover, commitment 等只需要传递少量参数后可以渲染出完整界面的，我命名以静态内容.

    这些内容传递参数后渲染出的整个界面是静态的对象，因此可以直接调用函数（return 渲染后的界面）插入到调用函数的位置.

  - 动态内容编译.
    动态的设定很难超过当前的生命周期，除非使用 '#show' 不断传递.
    在 content.typ 中，正文前使用 '#show: sustech-thesis.with()' 即将 content.typ 作为 body 参数交给 sustech-thesis() 这个函数渲染，因此，此时 body 作为被传递的参数进入了编译其中已经编译好了的 sustech-thesis，从而实现了格式控制.

    这样的格式控制分布在 configs 里的各个文件.

    此外，引用文献的设置采用了 typst 成员 [Laurenz](https://github.com/typst/packages) 教学模板里的设置，将 std-biliography 设置为某个 biliography 后才能夸文件引用，否则会出现标签引用相关错误.

```typst
// lib.typ
#let std-bibliography = bibliography
...

// content.typ
#show: sustech-thesis.with(
  ...,
  bibliography: bibliography(
    "refer.bib",
    title: [参考文献],
    style: "gb-7714-2015-numeric",
  ),
)

```

# Quickstart of typst template

按照毕业设计要求，以 markdown 格式书写你的毕业论文，只需要：

- 在 configs/info 里填入个人信息.
  如有标题编译错误（比如我默认了有三行标题），可以自行按照编译器提示把相关代码注释或者修改. 大体语法和内容与基本的编程语言无差别.

- 在 content.typ 里以 typst 特定的 markdown 语法书写你的论文内容. 
  有关 typst 中 markdown 的语法变更，个人认为的主要变化罗列如下：
  - 标题栏使用 '=' 而非 '#'，'#' 在 typst 里是宏命令的开头.
  - 数学公式不需要反斜杠，数学符号可以查阅：https://typst.app/docs/reference/symbols/sym/. 值得注意的是，typst 中语法不通过叠加的方式实现，如 “不等号” 在 LaTex 中是 '\not{=}'. 而在 typst 中，使用 'eq.not' 的方式来调用 'eq'（等号）的 'not'（不等）变体实现.
  - 引用标签采用 '@label' 来实现，自定义标签通过 '<label-title>' 来实现. 对于 BibTex 格式的引用（refer.bib），与 LaTex 思路相同，第一个缩略词将会被认定为 label.
  
- 自定义格式的思路.
  如有额外的需要自定义格式的需求，可以自行学习 '#set', '#show' 命令，这可能需要一定的编程语言知识，后续我会更新部分简略教程在我的 github 仓库里：https://github.com/Duolei-Wang/lang-typst.

- 本模板的结构
  1. 内容主体. 文章主体内容书写在 content.typ 文件中，附录部分书写在 appendix.typ 文件中.
  2. 内容顺序. 文章内容顺序由 main.typ 决定，通过 typst 中 '#include' 指令实现了页面的插入. 
  3. 内容格式. 内容格式由 /sections/*.typ 控制，body.typ 控制了文章主体的格式，其余与名称一致. cover 为封面，commitment 为承诺书，outline 为目录，abstract 为摘要.


# 有关字体的补充说明

为了 typst/packages 审核方便，我将字体文件上传到了个人版本的仓库里：https://github.com/Duolei-Wang/fonts. 如有字体使用的需求，请将其下载后移动到当前目录下使用，typst 编译器会直接能找到目录下的可用字体.

经个人查阅，论文等要求的“宋体”等字体要求均是一个模糊的概念. 

实际上，Windows 系统的宋体指中易宋体等，macOS 采用了华文宋体等. 为了避免不必要的纠纷，建议字体采用完全开源的字体，如：思源宋体、思源黑体、方正宋体、方正黑体、方正楷体、仿宋GB2312 等. 如担心字体审核问题，建议统一采用 GB2312 系列（缺点是部分生僻字缺失）. 本模板中使用的字体均为开源字体.

论文字体的选择在 font.typ 里进行了设置，可以修改 SongTi, HeiTi 等自变量的值来决定采用哪一个字体，这些自变量的值应当是字体的标准名称. 

如果想查阅当前编译环境内的可选字体，可以通过以下两种方式：

```typst
#set text(
  font: ...
)
```

然后将光标悬停在 'font: ' 后，编译器会自动列出当前可用字体. 

或者采用命令行指令 'typst fonts' 来查看可选字体.

# 版本说明

版本号：v0.1.0.

TODO:
- [ ] 引用格式 check.

# 特别鸣谢

南方科技大学本科毕业设计（论文）模板，论文格式参照 [南方科技大学本科生毕业设计（论文）撰写规范](https://tao.sustech.edu.cn/studentService/graduation_project.html). 如有疏漏敬请谅解，本模板为本人毕业之前自用，如有使用，稳定性请自行负责. 

- 本模板主要参考了 [iydon](https://github.com/iydon) 仓库的的 $\LaTeX$ 模板 [sustechthesis](https://github.com/iydon/sustechthesis)；结构组织参照了 [shuosc](https://github.com/shuosc) 仓库的 [SHU-Bachelor-Thesis-Typst](https://github.com/shuosc/SHU-Bachelor-Thesis-Typst) 模板；图片素材使用了 [GuTaoZi](https://github.com/GuTaoZi) 的同内容仓库里的模板.

- 感谢 [SHU-Bachelor-Thesis](https://github.com/shuosc/SHU-Bachelor-Thesis-Typst) 的结构组织让我学习到了很多，给我的页面组织提供了灵感，

- 在查找图片素材的时候，使用了 GuTaoZi 仓库 [SUSTech-thesis-typst](https://github.com/GuTaoZi/SUSTech-thesis-typst) 里的svg 素材 [LICENSE](https://github.com/GuTaoZi/SUSTech-thesis-typst/blob/main/LICENSE)，特此感谢！ 

# 写在后面

笔者开发能力较弱，仅出于个人安利 typst 的需要开发了本模板. 如有开发、接管、功能需求请务必联系：

QQ: 782564506

mail: wangdl2020@mail.sustech.edu.cn
