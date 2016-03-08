title: 基于fis的前端模块化和工程化方案
date: 2016-01-07 18:20:59
tags: [fis,前端,前端工程化,前端模块化]
---


## 前端构建工具
面对日益复杂的前端环境以及前端技术、node技术的高速发展，前端的开发也越来越工程化，体系化，也就是出现了前端自动化构建工具。他们完成的任务目标基本是：

> * js，css,图片的自动压缩合并（图片也即是是自动sprite）
> * js，css,图片自动添加域名
> * js，css,图片自动添加md5或版本号
> * 自动监听文件变化
> * sass/less/coffee等的自动编译
> * 支持amd/cmd的模块开发，可自动文件依赖
> * 可以部署文件
> * 网页自动刷新
> * 实时编译

成熟的前端构建工具也有很多，比如：Gulp.js，Grunt，webpark, fis等等，其他构建工具本人使用不多，本文主要是对fis的前端工程化和模块化的使用做详细介绍


## 关于fis
fis是百度研发的一套前端构建工具，拥有丰富的脚手架和组件仓库。因为fis是本人接触的最早的前端构建工具，所以一直沿用到了至今。

		注： 本文是针对fis2，基于fis3的配置和插件将在稍后给出

## fis的前端工程化和模块化基础插件
### 1.自动打包合并插件
#### fis配置
```js
//开始autoCombine可以将零散资源进行自动打包
fis.config.set('settings.postpackager.simple.autoCombine', true);
//开启autoReflow使得在关闭autoCombine的情况下，依然会优化脚本与样式资源引用位置
fis.config.set('settings.postpackager.simple.autoReflow', true);
fis.config.set('settings.postpackager.simple.output', 'pkg/mcren_${hash}');
```
#### 作用
将html中分散的静态资源进行自动合并打包
#### 应用举例：
原始文件
```js
  <link rel="stylesheet" type="text/css" href="/css/mcren.css">
  <link rel="stylesheet" type="text/css" href="/site/index/index/index.css">
```
转换后
```js
   <link type="text/css" rel="stylesheet" href="/pkg/mcren_7636e.css">
```
可以看到fis会自动合并多个css到同一个文件里，这个合并不仅仅适用于css，也同样适用与js，并且将自动把css文件放入header头，js放在body结束前，有了这个功能也就具备的模块化开发的大前提
### fis的include功能
#### fis配置
默认支持，无须插件
#### 作用
大家在使用模板的时候，肯定是少不了include功能的， 即公共部分的文件引用。fis同样支持这个功能，而且借助与自动打包功能，include功能的作用也被放大的很多.(fis支持多级include)
#### 应用举例
##### 主模板文件
```js
<!DOCTYPE html>
<html>

<head>
  <meta name="description" content="">
  <meta name="keywords" content="">
  <link rel="stylesheet" type="text/css" href="/css/mcren.css">
  <link rel="stylesheet" type="text/css" href="/site/index/index/index.css">
  <script type="text/javascript" src="/lib/mod.js"></script>
</head>

<body>
  <link rel="import" href="/widget/header-small/header.html?__inline">
```
##### 次模版文件（header-small/header.html）
```js
<link rel="stylesheet" type="text/css" href="header.css">
<div class="header">
	<div>
		<link rel="import" href="../loginstate/loginstate.html?__inline"><h1><a href="/">主页</a></h1>
	</div>
</div>
<script type="text/javascript">
    var jsfunc = require('jsfunc/jsfunc');
    jsfunc.init();
</script>
```
##### 转换后
```js

<!DOCTYPE html>
<html>

<head>
    <meta name="description" content="">
    <meta name="keywords" content="">
    <link type="text/css" rel="stylesheet" href="/pkg/mcren_7636e.css">
</head>

<body>
    <div class="header">
	  <div>
		  <div class="loginstate">
	        <a href="/space">左盐</a> | <a href="javascript:void(0);">退出</a><img src="member.jpg" id="avatar" />
          </div>
           <h1><a href="/">名场</a></h1>
	   </div>
    </div>

	<!--省略的html代码-->

<script type="text/javascript" src="/lib/mod.js"></script>
<script type="text/javascript">
    var jsfunc = require('jsfunc/jsfunc');
    jsfunc.init();
</script>
</body>

```
#### 说明
可以看到，经过fis处理后，本来应该分散的css被组合放进了head头里，js被放在了body结束前，模板的html代码也正常引入了进去。

> 另外大家可以看到我模块的划分，header模块的html是引入css的，也就是说模块与模块之间以及模块和主体页面之间的css都是独立的，这样充分解耦，可以有效的解决css的维护问题，


### 图片雪碧
#### fis配置
```js
fis.config.set('modules.spriter', 'csssprites');
```
#### 作用
自动把css中的图片转换成雪碧图
#### 应用举例
不写了，大家都懂的


### ejs分析能力
#### fis配置
```js
//tell fis that `.ejs` is a js file 
fis.config.set('roadmap.ext.ejs', 'js');
//tell fis that parse `.ejs` file by using `fis-parser-ejs` plugin 
fis.config.set('modules.parser.ejs', 'ejs');
```
#### 作用
大家知道，写js打印html字符串到页面的时候，如果在js里面串接html字符串是一种很难受的体验，所以fis也就有了这个插件，这个插件可以给js方法添加引用ejs模板的能力，这里ejs的使用方法和原生的完全兼容
#### 应用举例
##### js函数
```js
function ejsEx() {
	var tpl = __inline('comment.ejs');
	var s = tpl({
		title: "我的ejs模板"
	});
	console.log(s);
}
```
#### ejs模板
```js
<h1><%= title%></h1>
```
#### 输出
```js
<h1>我的ejs模板</h1>
```
## 目录结构
上面已经介绍了fis的模块化基础能力，现在开始实践fis的模块化开发能力。当然fis的amd/cmd，域名添加等等基础功能，这里没有叙述，您可以自行上官网查看学习
> 根目录
>> css //less生成的css文件
>> font //字体图标文件
>> img //公共图片
>> less 
>> lib //系统类库
>> modules //常用的module
>>> jquery
>>> laydate
>>> layer
>>> pizzalayer
>>> pizzatools //网站工具类
>>> pizzaui //ui组件
>>> store //本地化插件
>>> xss //xss过滤插件

>> site //非模块的css，js
>> views //网页模板文件 
>> widget //模块
>>> head //头部模块
>>> footer //底部模块
>>> nav //菜单模块

>> fis-conf.js //fis配置文件

因为我的技术构架是： 前端 + nodejs + api，所以使用这种目录结构，用户可根据自己的项目目录自由更改。

### 说明
#### modules目录
modules都是工具类，只包含js，
目录结构为：
```js
---根目录
----modules
-----jquery
------jquery-1.11.3.min.js
```
引用方式为：require('文件夹名字')
```js
require('jquery')
```

#### widget目录
widget目录都是网站模块，可包含js,css,html,图片
目录结构为：
```js
---根目录
----widget
-----loginstate
------loginstate.less
------loginstate.js
------loginstate.html
------loginstate.jpg
```
引用方式为：
```js
<link rel="import" href="/widget/loginstate/loginstate.html?__inline">
```
## 一个模块的代码示例
### loginstate.html代码
```js
<link rel="stylesheet" type="text/css" href="loginstate.css">
<div class="loginstate">
	<a href="/space">左盐</a> | <a href="javascript:void(0);">退出</a><img src="member.jpg" id="avatar" />
</div>
<script type="text/javascript">
	var loginstate = require('loginstate/loginstate');
	loginstate.init();
</script>
```
### loginstate.js代码
```js
var $  = require('jquery');
var tools = require('pizzatools');
require('pizzalayer');
var loginState = new function() {
	var _self = this;
	/**
	 * 打印登录状态
	 * @return {[type]} [description]
	 */
	_self.init = function() {
		var s = '';
		var id = tools.getCookie('id');
		if(id == '0' ) {//未登录
			s = '<a href="/login?f='+document.location.href+'" class="btn btn-primary">登录</a>&nbsp;&nbsp;&nbsp;&nbsp;<a href="/reg" class="btn btn-primary">注册</a>';
		}
		else {
			s = '<a href="/space">' + tools.getCookie('nickname') + '</a> | <a href="javascript:void(0);" onclick="loginstate.loginout();">退出</a><img src="' + tools.siteData.url.avatar + tools.getCookie('avatar') + '!v1" id="avatar" />';
		}
		$('.loginstate').html(s);
	}
	/**
	 * 退出登录
	 * @return {[type]} [description]
	 */
	_self.loginout = function() {
		$.ajax({
			type:'GET',
			url:'/index/loginout',
			success: function(msg) {
				var url = document.location.href.split('/');
				if(url[3] == 'space') {
					document.location.href = '/';
				}
				else {
					document.location.reload();
				}
			}
		})
	}
}
module.exports = loginState;
```
### index.html代码
```js
<div class="header">
	<div>
		<link rel="import" href="/widget/loginstate/loginstate.html?__inline"><h1><a href="/">主页</a></h1>
	</div>
</div>

```

由此前端模块化已经全部完成


## 所需插件
* fis-postprocessor-require-async
* fis-parser-ejs
* fis-postpackager-autoload
* fis-postpackager-simple

```js
npm install 以上插件即可
```

## fis-conf.js
结合我的项目，本人的配置文件如下。下载本配置文件并且安装好fis后，可以直接使用。（由于项目发展时间很长，配置文件已经显得啰嗦，如果大拿研究了fis，可自行优化自己的配置文件）
```js
fis.config.set('livereload.port', 8136);
//use css sprites
fis.config.set('modules.spriter', 'csssprites');
//tell fis that `.ejs` is a js file 
fis.config.set('roadmap.ext.ejs', 'js');
//tell fis that parse `.ejs` file by using `fis-parser-ejs` plugin 
fis.config.set('modules.parser.ejs', 'ejs');
//set options if you need 
//@see https://github.com/visionmedia/ejs#options 
fis.config.set('settings.parser.ejs', {
    open: '<%',
    close: '%>'
});
//使用simple插件，自动应用pack的资源引用
fis.config.set('modules.postpackager', 'simple');
//开始autoCombine可以将零散资源进行自动打包
fis.config.set('settings.postpackager.simple.autoCombine', true);
//开启autoReflow使得在关闭autoCombine的情况下，依然会优化脚本与样式资源引用位置
fis.config.set('settings.postpackager.simple.autoReflow', true);
fis.config.set('settings.postpackager.simple.output', 'pkg/mcren_${hash}');

fis.config.merge({
    pack: {
        'pkg/base.js': ['/modules/jquery/*.js', '/modules/layer/*.js', '/modules/pizzalayer/*.js', '/modules/pizzatools/*.js'],
        'pkg/kindeditor.js': ['/widget/kindeditor/kindeditor-min.js']
    }
});

fis.config.merge({
    statics: '/public',
    modules: {
        postprocessor: {
            js: "jswrapper, require-async",
            html: "require-async"
        },
        postpackager: ['autoload', 'simple'],
        lint: {
            js: 'jshint'
        }
    },
    roadmap: {
        ext: {

        },
        path: [{
            reg: /^\/modules\/laydate\/(need|skins)\/.*\.(js|css|png|gif)$/i,
            useHash: false,
            isMod: false,
            release: '${statics}/$&',
            url: '$&'

        }, {
            reg: /^\/widget\/kindeditor\/(lang|plugins|themes)\/.*\.(js|css|png|gif)$/i,
            useHash: false,
            isMod: false,
            release: '${statics}/$&',
            url: '$&'

        }, {
            reg: /^\/lib\/(.*)\.(js)$/i,
            isMod: false,
            release: '${statics}/$&',
            url: '/lib/$1'
        }, {
            reg: /^\/font\/(.*)$/i,
            isMod: false,
            release: '${statics}/$&',
            url: '/font/$1'
        }, {
            reg: /^\/views\/(.*)$/i,
            useCache: false,
            release: '/views/$1'
        }, {
            //一级同名组件，可以引用短路径，比如modules/jquery/juqery.js
            //直接引用为var $ = require('jquery');
            reg: /^\/modules\/([^\/]+)\/(.*)\.(js)$/i,
            //是组件化的，会被jswrapper包装
            isMod: true,
            //id为文件夹名
            id: '$1',
            release: '${statics}/$&',
            url: '$0'
        }, {
            //组件化的webpart的js
            reg: /^\/(widget|site)\/(.*)\.(js)$/i,
            isMod: true,
            id: '$2',
            release: '${statics}/$&',
            url: '$&'
        }, {
            //modules目录下的其他脚本文件
            reg: /^\/modules\/(.*)\.(js)$/i,
            //是组件化的，会被jswrapper包装
            isMod: true,
            //id是去掉modules和.js后缀中间的部分
            id: '$1',
            release: '${statics}/$&',
            url: '/modules/$1'
        }, {
            //组件化的webpart的html
            reg: /^\/widget\/(.*)\.(html)/i,
            release: false
        }, {
            //组件化的webpart的css
            reg: /^\/(widget|site)\/(.*)\.(css|png|jpg)/i,
            release: '${statics}/$&',
            useSprite: true,
            url: '$&'
        }, {
            //其他css文件
            reg: /^(.*)\.(css|less)$/i,
            release: '${statics}/$&',
            url: '$&'
        }, {
            //前端模板
            reg: '**.ejs',
            //当做类js文件处理，可以识别__inline, __uri等资源定位标识
            isJsLike: true,
            //只是内嵌，不用发布
            release: false
        }, {
            reg: "map.js",
            release: "${statics}/"
        }, {
            reg: /.*\.(html|jsp|tpl|vm|htm|asp|aspx|php)$/,
            useCache: false,
            release: '${statics}/$&'
        }, {
            reg: "**",
            release: '${statics}/$&',
            url: '$&'
        }]
    },
    settings: {
        postprocessor: {
            jswrapper: {
                type: 'amd'
            }
        },
        lint: {
            jshint: {
                camelcase: true,
                curly: true,
                eqeqeq: true,
                forin: true,
                immed: true,
                latedef: true,
                newcap: true,
                noarg: true,
                noempty: true,
                node: true
            }
        }
    }
});

fis.config.merge({
    roadmap: {
        //所有静态资源文件都使用 http://s1.example.com 或者 http://s2.example.com 作为域名
        domain: 'http://static1.example.com, http://s2.example.com'
    }
});


fis.config.merge({
    project: {
        exclude: /.docx|.bak$|.bat$|.md$|.rar$$|^\/less\/*|.less$|^\/modules\/pizzaui\/pizzaui\/*/i
    } //排除压缩包，文档，和bak文件
});


```
