---
title: hexo+gitpages+ci 打造个人博客
date: 2019-02-05 22:12:02
tags:  hexo
categories: 其他
---
# hexo+gitpages+ci 打造个人博客
## 前言
说起自己写博客的历史也是有断断续续一年了，大概也是18年春节期间自己fork了他人的博客，使用gitPages部署，也是从那时开始学习git，博客说起来也算是自己成为一个稍微正式一点的coder的开端吧。至于自己为什么要放弃之前博客，要重新利用hexo打造个人博客，原因如下：  
1. hexo配置简单，插件丰富  
2. 3-hexo这个主题比较符合自己对于博客条理性的要求，相比于之前的博客更加精炼，易整理，易阅读    
3. ci的集成也让hexo更加关心写作本身  
在阅读本文之前,默认读者拥有域名解析经验与gitPages了解,如果存在疑问,请参考我fork的上一个Blog原作者的[详细搭建流程](https://www.jianshu.com/p/e68fba58f75c)

## 基础环境
### hexo安装
基础环境:Node,Git  
教程：*[hexo官网安装教程](https://hexo.io/zh-cn/docs/index.html)*
### 3-hexo主题
[仓库地址](https://github.com/yelog/hexo-theme-3-hexo)
### Travis CI配置
*请先按以下操作配置,详细配置在余下篇幅介绍*  
1. 将Travis CI授予GitHub权限   
2. 需要被CI管理的仓库在根目录生成 .travis.yml文件,并进行push到github  
3. Travis CI开启对相应仓库的监测  
4. push代码到ci配置的监测分支,看log记录是否触发成功

```yml
ci触发分支配置
branches: 
  only:
  - source //触发ci的条件分支
```
**补充**: 为防止有些同学不了解CI概念,我简单介绍下CI,CI意味着持续集成,可以以我公司配置的CI为例,我们都知道代码上线需要保证质量,我们可以在CI中配置每次进行Git Push时,复杂CI的机器就会进行代码的编译,历史自测的检查,当我们合并Master分支时,我们的对外的Api可能发生变化,也可以依靠CI进行自动API包的发布,这些都是CI要做的事情,在这篇文章涉及的代码库存在两个分支,source分支保留我们博客的源码,包含我们文章的Markdown等文件,Master分支是Hexo生成的静态网页文件,我们依靠Travis CI做到的就是,我们向source分支提交代码,Travis CI进行拉取环境,编译,最后hexo deploy到Master分支,让我们的网页进行变化

## CI
### .travis.yml文件加入对环境运行环境支持

``` yml
language: node_js //node环境
node_js:
- 10.15.1 //node版本
branches: 
  only:
  - source //触发ci的条件分支
before_install: 
- npm install -g hexo-cli //hexo环境
- npm install --save hexo-deployer-git //hexo deploy插件
install:
- npm install //安装package.json文件中配置的hexo插件
```

当source分支发生改变,Travis CI监测到change后进行ci,首先安装node环境,之后安装hexo环境,安装deploy插件,安装额外hexo插件



### _config.yml文件加入仓库配置

```yml
deploy:
  type: git
  repo: git@github.com:EvanLjp/evanljp.github.io.git
  branch: master
```
一些细心地同学可能已经发现了一些问题,我们是在运行ci的机器进行deploy,但是我们机器上并没有配置git秘钥,怎么能进行push操作呢,这就要说到Github OAuth,支持一种带有token的秘钥进行免git秘钥push

### 创建Github OAuthToken
1. 打开 [Personal Access Tokens](https://github.com/settings/tokens)
2. create new token
3. 请注意,至少给这个token repo的权限,否则无法push
4. 进行token可用性测试
	- 按https://<token>@github.com/owner/repo.git格式拼接某个仓库的url(<token>表示生成token),例如我的blog仓库地址:https://1f0f0XXX@github.com/Evanljp/evanljp.github.io.git(XXX表示省略,实际token很长)
	- 进行git clone ,能clone说明url拼接合理
	- 进行git push,如成功,说明这个token可用,如发现403,请检查repo权限是否赋予
我们已经看到这个token权限的强大之处,我的仓库source分支包含了Blog的所有源码,我不可能把这么大权限的token进行暴露,所以必须进行加密,幸好travis已经提供解决方案,接下来我们看一下如何token加密

### Token加密
1. 首先安装 travis 命令行工具

 ```sh
gem install travis
travis login
```
2. .travis.yml通过命令加入加密token :
 **travis encrypt 'GH_TOKEN=TOKEN' --add**,TOKEN表示之前明文token,你会发现.travis.yml文件已经加入了如下内容:

```yml
env:
  global:
    secure: fuY23T6wEu...
```

### Token解密
在.travis.yml文件加入如下内容,以动态解密发布

```yml
after_success:
- git config --global user.name "evanljp"
- git config --global user.email "evanljp@outlook.com"
- sed -i'' "s~git@github.com:EvanLjp/evanljp.github.io.git~https://${GH_TOKEN}:x-oauth-basic@github.com/EvanLjp/evanljp.github.io.git~" _config.yml
- hexo deploy --silent
```
### 发布
上一步的配置中实际已经加入了hexo deploy --silent发布配置,请动手尝试,观看CI线上Log,如果发现Push成功,那么恭喜你,你也拥有了一个可以自动Ci的blog,让你可以更加关注写作
##参考
[个人blog配置](https://github.com/EvanLjp/evanljp.github.io/tree/source)  
[Deploying Hexo to Github Pages with Travis](https://sazzer.github.io/blog/2015/05/04/Deploying-Hexo-to-Github-Pages-with-Travis/)







