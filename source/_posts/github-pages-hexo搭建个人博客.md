---
title: github pages + hexo搭建个人博客
date: 2018-07-14 10:38:18
tags:
  - 博客搭建
  - hexo
  - next
categories: 博客搭建
---
参考[Hexo静态博客搭建+个人定制](https://blog.csdn.net/LemonXQ/article/details/72676005)
参考[Hexo+Pages静态博客-Next主题篇](https://blog.csdn.net/mango_haoming/article/details/78207534)
## 环境安装

### 确保已经安装`node`及`git`

### `npm`安装`Hexo`
```
npm install -g hexo-cli
```

### 建立博客项目
```
hexo init <folder>
cd <folder>
npm install
```
等安装完依赖后 项目就算初始化好了,博客的文件结构如下:   
![项目初始目录结构](/images/github pages + hexo搭建个人博客/项目初始目录结构.png)

### 配置博客
`_config.yml`就是博客项目的全局配置文件, 比如博客标题、子标题、描述、作者、语言、时区、博客地址和根地址
```
# Site
title: 维京博客
subtitle:
description:
keywords:
author: qiyuan
language: zh-Hans # 语言设置
timezone: Asia/Shanghai
```
<!--more-->
### 尝试预览

#### Hexo常用的命令如下
``` 都可以使用命令的首字母作为简称
hexo clean 清除缓存
hexo generate 生成静态文件
hexo deploy 部署
hexo server 本地预览
```
在开发过程中 使用`hexo server`或者`hexo s`即可本地预览,由于`hexo`默认存在一篇文章`hello-world`，所以我们可以看下情况:    
![预览默认项目](/images/github pages + hexo搭建个人博客/预览默认项目.png)

### 创建新文章
```
hexo new "文章标题"
```
执行命令后 会在`source/_post`目录下创建同名的`.md`文件,这就是文章的内容，使用makedown编辑好就可以
编辑完成后 预览查看效果`hexo s`   
![创建新文章](/images/github pages + hexo搭建个人博客/创建新文章.png)

### 删除文章
直接删除`source/_posts`文件夹下的`.md`文件即可,但是请注意, `source/_posts`下不能为空,所以请在发布一篇文章后再删除默认的文章


## 更换主题

### next  
#### 下载
- 在项目的根目录 git clone
```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
- 去github主页下载并放入themes下的文件夹中

#### 更换主题
在`_config.yml`文件中找到 配置项`theme`改成 `next`即可

#### 更换风格
`next`有四种风格 `Muse` `Mist` `Pisces` `Gemini` 如果需要更改风格 需要在主题文件夹下的`_config.yml`中找到`scheme`字段修改
- Muse  --- 默认 Scheme，这是 NexT 最初的版本，黑白主调，大量留白
  ![Muse风格](/images/github pages + hexo搭建个人博客/Muse风格.png)
- Mist   --- Muse 的紧凑版本，整洁有序的单栏外观
  ![Mist风格](/images/github pages + hexo搭建个人博客/Mist风格.png)
- Pisces  --- 双栏 Scheme，小家碧玉似的清新
  ![Pisces风格](/images/github pages + hexo搭建个人博客/Pisces风格.png)
- Gemini   --- Pisces版本基础上 内容块更宽些
  ![Gemini风格](/images/github pages + hexo搭建个人博客/Gemini风格.png)


## 上传github pages
### 绑定自定义域名
- 首先 在`source`目录下创建一个新文件`CNAME`填写自已的域名blog.test.com
- 添加一条域名解析, 注意类型是CNAME  
![添加域名解析](/images/github pages + hexo搭建个人博客/添加域名解析.png)


## 更换电脑

### 将原电脑上的项目copy新电脑上, 保留如下目录即可：
```
_config.yml
package.json
scaffolds/
source/
themes/
```

### 确认在新电脑上已经配置好hexo环境 参考[环境安装]

### 模块安装
```
npm install
npm install hexo-deployer-git --save
npm install hexo-generator-search --save
npm install hexo-generator-searchdb --save
npm install hexo-generator-feed --save
npm install hexo-generator-baidu-sitemap --save
npm install hexo-generator-sitemap --save
npm install hexo-generator-seo-friendly-sitemap --save
```

## 增加社会化评论框
### Livere
自从多说关闭后国内就没有啥好的社会化评论框了, disqus一直不错可惜国内用户用不着。百度一搜很多人都从多说转移到了 Livere 这是韩国的一款评论框，官网支持中文不说, 也支持QQ、微信、微博、豆瓣、百度等中国的一大波社交账户 非常赞。
#### 准备工作
- 去[Livere](https://livere.com)官网注册Livere账号。
- 选择City版（免费），安装
- 进入管理页面->代码管理->一般网站，复制`data-uid`
#### 加入到`hexo`中
- 如果选择的`next`主题的话,他已经加入了`livere`支持,只需要复制`data-uid`到主题文件夹下的`_config.yml`文件的livere_uid即可
#### 其他主题
其他主题，请去搜索或者查找官方说明,也可借鉴[Hexo之使用Livere评论代替多说评论](https://blog.csdn.net/lemonxq/article/details/78578617)
