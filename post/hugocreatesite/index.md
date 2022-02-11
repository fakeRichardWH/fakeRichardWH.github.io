# Hugo和GitHub Pages搭建个人博客网站


# GitHub设置
0. 首先创建个人网站的一个repo：
**注意命名规则**:一定是你的`用户名.github.io`比如`sophshep.github.io`
详见下面这个图片:
![测试图片](https://img-blog.csdnimg.cn/f9e2eab1ef854acdbdf0d423c345ab2f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAUmljaGFyZHd3aGg=,size_20,color_FFFFFF,t_70,g_se,x_16)

建议：为了后续的开发也可以再创建一个仓库

# Hugo安装及配置
首先安装 `Hugo`，在不同系统上安装都很简单，我使用的是 Mac ，使用 `Homebrew` 可以安装 `Hugo`：


### 下载Hugo

```bash
brew install hugo
```

# 创建一个基础网站
当你在当前路径下的终端内输入
```bash
hugo new site mySite 
```
就会自动创建一个mySite的根文件夹┑(￣Д ￣)┍
### Hugo网站文件夹目录
顺带提一嘴这个文件结构，对后续写文章比较重要
> mySite
  |--layout
  |--content
  |--static
  |--data
  |--themes
  |--config.toml
  |--archetypes
 
 - content - **内容文件夹**开发中的内容大多数都保存在这里 也就是markdown文件存储的位置 你post的文章的md后缀文件也会出现在这里
- static - **静态文件夹**一些静态的文件，比如说嵌入的图片,**图片位置**
- themes - **主题文件夹**
- config.toml - **配置文件** 基础的一些网站设置，比如网站的名字
- archetypes - **样例文件夹** 可以透过 `hugo new <contentdir/contentfile.md>`来创建新的内容


## 选择并安装一个合适的主题并进行初始配置
挑选好一个theme之后load到你的本地
```bash
git submodule add <theme-github-url> <target-directory>
```
下面我们要告诉Hugo我们所将使用的主题

```bash
echo 'theme = "<theme-name>"' >> config.toml
```
我这里用的是：

```bash
echo 'theme = "even"'>> config.toml
```
当然你也可以直接打开编辑器修改toml.config文件

## 本地测试一下你的新网站及博文
输入下面的命令后会自动发布一篇测试文章：

```bash
hugo new post/testpage.md
```
新的testpage.md页面会存在content/post/testpage.md中，而content文件夹是Hugo创建静态网页的内容（你所写的博客基本都在这里）

```bash
---
title: "Testpage"
date: 2020-08-27T13:43:09-06:00
draft: true
---
```
打开archetypes/default.md可以发现，上面Testpage.md的内容是依赖下面的自动生成的，而draft的设置为true表明现在的Testpage处于草稿阶段，修改为false即可

```bash
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true
---
```

**需要注意的是：** 第一个`---`到第二个`---` 符号之间是**front matter**（Hugo的专用术语）类似于**HTML**中的`<meta>`标签是用来定义页面头部的一些信息（渲染标题和其他的元数据）的，而在第二个`---` 符号之后就是你的正文部分 或者说相当于HTML的body

# 关于config.toml的一些设置偏好
- `baseurl`: 就是你想发布的网站：如https://<your-github-id-here>.github.io
- title：标题，显示在每个页面的左上，一般都是自己的id
- `languageCode`：页面语言，指定编码格式，比如en-us/en-gb/cn表示英语-美国/英语-英国
- `paginate`：在主页上显示多少条博客内容
- `[markup]`:这一部部分显示使用哪一种markup/down语法，它一般不会渲染HTML标签否则可能产生安全问题，但如果你想要强制使用的话：设置unsafe = true
- `[params]` - 基础的参数设置
	- `header_image` - 默认的所有页面的背景图片，并且固定作为home页面和about页面的图片
	- `title` - 文章标题，显示在每个页面
	- `slogan` - 子标题
	- `sidebar_about_description`, `sidebar_avatar, & about_me`：侧边栏介绍，侧边栏可以用于简单的自我介绍，提供一些个人简介之类的，而将about_me设置为true就可以实现了

- `featured_tags`： 是用来指定有多少个tags你想在页面中显示
- `feature_condition_size`：指定每篇文章最少要给出多少个tag在tag云中
image_404 & title_404 - 当文章找不到，显示的404图片
- `omit_categories` - 是否要忽略导航栏分类功能
- `[[params.additional_menus]]` - 提供在导航栏中提供额外的菜单，比如About页面
- `[params.social]` - 社交账户设置


```markdown
baseurl = "https://fakeRichardWH.github.io"
title = "fakeRichardWH"
theme = "even"
languageCode = "en-us"
paginate = 5000 #frontpage pagination

[markup]
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true

[params]
  header_image = "images/main_go_wideandtall_darklayer.jpeg"
  title = "Software Engineering"
  slogan = "$ grep -rni \"The how's and why's\" ."
  SEOTitle = "fakeRichardWH Blog"
  keyword = "fakeRichardWH, TestTools, MachineLEARNING, JAVA, SpringBoot"
 
  # Sidebar settings
  sidebar_about_description = "Husband, Skier, Cyclist, and, oh yeah, Software Developer"
  sidebar_avatar = "images/MeAtUS.jpeg"
  about_me = true

  featured_tags = true 
  featured_condition_size = 2 # 当有多少条post时可以实现按标签索引 (大于)

  image_404 = "images/404-bg.jpg"
  title_404 = "We couldn't find what you were looking for..."

  omit_categories = false

  [[params.addtional_menus]]
  title =  "ABOUT"
  href =  "/top/about/"

  [params.social]
  rss            = true
  email          = "testMe@gmail.com"
  linkedin       = "https://www.linkedin.com/in/richard-youngkin-0749763"
  github         = "https://github.com/youngkin"
  stackoverflow  = "https://stackoverflow.com/users/2646870/rich"
  reddit         = "https://www.reddit.com/user/elevation5280"
```



# 常用命令（Hugo及Git）
### 本地服务器启动
下面这个代码会启动一个本地的Hugo Web服务器
```bash
hugo server -D
```
输入后会显示：

```bash
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
```
我们之后可以用浏览器打开该网址看看写的文章：

![在这里插入图片描述](https://img-blog.csdnimg.cn/4ff8476c173246429082a93f662a618d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAUmljaGFyZHd3aGg=,size_20,color_FFFFFF,t_70,g_se,x_16)
###


# 一个完整的工作流 （写博文/更改博文）
1.创建一个文档`hugo new post/my-xxx-post.md`，用markdown语法 （推荐用VScode + Markdown的扩展，可以本地及时查看博文变动）
2.本地测试文档，删除一些不必要的文件
3.使用hugo命令
4.使用git命令完成相应文章的上传/更新

```bash
#写完博文之后
hugo
cd public
git add .
git commit -m "blog added"
git push -u origin master #一般都是在master分支上去操作
```


## 总结一下Hugo命令
```bash
hugo -D     #搭建静态页面 输出到 ./public 目录中 如果不另外指定的话
hugo server # 一般是用于本地web服务器校对
hugo        #部署时使用
```

## content的目录结构
用下面一个样例结构来进行说明
```
content/
├── about
│   ├── index.md
├── posts
│   ├── my-post
│   │   ├── content1.md
│   │   ├── content2.md
│   │   ├── image1.jpg
│   │   ├── image2.png
│   │   └── index.md
│   └── my-other-post
│       └── index.md
│
└── another-section
    ├── ..
    └── not-a-leaf-bundle
        ├── ..
        └── another-leaf-bundle
            └── index.md
```
上面这个是一个典型的四级目录包
1. about: 建立在root级别之下，其中只有一个index.md文件
2. my-post: 
3. image1: 
4. image2:
5. my-other-post: 
