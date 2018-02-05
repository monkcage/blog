## Welcome to GitHub Pages

### Install

1. fork库到自己的github
2. 修改仓库名字
3. clone库到本地，参考`_posts`中的目录结构自己创建适合自己的文章目录结构
4. 修改CNAME，或者删掉这个文件，使用默认域名
5. 修改`_config.yml`配置项
6. It's done!

### yml configure
1. 如果博客主页没有指定的样式，在yml文件中加入（具体路径根据自己的目录设定）:
```
css： /assets/css
js: /assets/js
image: /assets/images
```
2. 博客头像
可以为网络图片链接，也可以保存在assets目录下；
```
avatar: /assets/img/avatar.jpg
favicon: /assets/img/favicom.jpg
```

### 第三发评论系统 
* 国内没有比较好的评论系统，而且有些需要个人博客备案，但对于github pages来说，备案也是件难事了；
* 国外比较火的disqus也被伟大的GFW墙了；
* 最终只能选择gitment(虽然好评如潮，但是还是觉得太丑！)；

1. [注册](https://github.com/settings/applications/new) 
* Application name : 自定义
* Homepage URL ： <usrname>.github.io
* Applicaiton description: 自定义
* Authorization callback URL : <usrname>.github.io
2. 注册之后会生成client ID 和 client secret；
3. 通常在_layouts下有post.html的页面文件，在文件中加入内容：
```
<div id="container"></div>
<link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
<script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
<script>
var gitment = new Gitment({
  id: '页面 ID', // 可选。默认为 location.href
  owner: '你的 GitHub ID',
  repo: '存储评论的 repo',
  oauth: {
    client_id: '你的 client ID',
    client_secret: '你的 client secret',
  },
})
gitment.render('container')
</script>
```
或
```
  <div id="gitmentContainer"></div>
<link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
<script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
<script>
var gitment = new Gitment({
    owner: '',
    repo: '',
    oauth: {
        client_id: '',
        client_secret: '',
    },
});
gitment.render('gitmentContainer');
</script>
```

### Demo
Single column, please see my own blog

Two columns, please see the theme website


### Reference
[yulijia](https://github.com/yulijia/freshman21)
