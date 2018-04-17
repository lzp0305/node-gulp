# Node.js + gulp 自动化你的前端
## 1.Node.js安装
[点击下载node.js](https://nodejs.org/zh-cn/)

检查node版本`node -v`

检查npm版本`npm -v`

如果网络不稳定，可以通过淘宝的`cnpm`来完成npm的所有工作

`npm install -g cnpm --registry=https://registry.npm.taobao.org`

安装完之后，下面所有命令中的npm，都可以用cnpm代替。

## 2.全局安装gulp

`npm install -g gulp`

检查gulp版本`gulp -v`

>↑全局安装gulp的目的是为了在命令行中使用

## 3.本地安装gulp
进入项目根目录

`npm init`

按需求填写内容，可以直接回车跳过，最后输入一个`yes`

安装gulp和所需插件

`npm install --save-dev gulp`

>↑必须

`npm install --save-dev gulp.spritesmith`

>↑自动制作雪碧图插件

`npm install --save-dev gulp-sass-china`

>↑sass插件

`npm install --save-dev browser-sync`

>↑自动刷新浏览器插件

## 4.手动创建gulpfile.js文件

``` javascript
// 安装依赖
var gulp = require("gulp");//引入本地安装的 gulp模块
var spritesmith = require("gulp.spritesmith");//引入雪碧图模块
var browserSync = require("browser-sync");//引入 browser-sync 模块
var sass = require('gulp-sass-china');//引入sass模块


//创建sass任务
gulp.task('sass', function () {
	return gulp.src('./sass/**/*.scss')
		.pipe(sass().on('error', sass.logError))
		.pipe(gulp.dest('./css'));
});
//可单独用 gulp sass 来完成sass任务


//创建sprite任务
gulp.task('sprite', function(){
	gulp.src('./icon/*.png')
		.pipe(spritesmith({
			imgName: 'sprite.png',
			cssName: '../css/sprite.css',
			padding: 5,
			algorithm: 'binary-tree'
		}))
		.pipe(gulp.dest('./images'))
});
//可单独用 gulp sprite 来完成雪碧图任务


//架设静态服务器，用 asd 为任务名，用 gulp asd 来启动服务
gulp.task('asd', function () {
	browserSync.init({
		files:['**'],
		server:{
			baseDir:'./', // 设置服务器的根目录
		},
		port:8050 // 指定访问服务器的端口号
	});
	gulp.watch("icon/*.png", ['sprite']);
	gulp.watch("sass/*.scss", ['sass']);
	gulp.watch("*.html").on('change', browserSync.reload);
});
```
## 5.目录结构及逻辑
```
根目录
  ┝┉┉css
  ┋   ┝sprite.css		//雪碧图插件自动生成的雪碧图css文件
  ┋   ┝main.css			//sass插件自动生成的css文件
  ┋   └...
  ┋
  ┝┉┉icon				//需要做成雪碧图的png图片扔这里面
  ┋   ┝***.png
  ┋   ┝***.png
  ┋   └...
  ┋
  ┝┉┉images
  ┋   ┝sprite.png		//雪碧图插件自动生成的雪碧图
  ┋   └...
  ┋
  ┝┉┉node_modules		//node自动生成的模块文件夹，忽略
  ┝┉┉sass
  ┋   └main.scss		//在这个scss文件里写css
  ┝┉┉gulpfile.js		//忽略
  ┝┉┉index.html			//前端页面文件
  ┝┉┉package-lock.json	//忽略
  ┝┉┉package.json		//忽略
  └┉┉...
```


在根目录按住`shift`+右键

选择在此处打开命令窗口，或者在此处打开powershell(win10)

然后在cmd或powershell中输入`gulp asd`，ps：这个asd就是上面架设服务器的那个任务名称，可以改。

会自动打开浏览器`localhost:8050`

然后开始coding

所有需要做成雪碧图的png文件丢进icon文件夹，会自动在images文件夹内生成sprite.png，各种坐标在./css/sprite.css中

具体scss语法移步[这里](https://www.sass.hk/docs/)






## 6.其他
scss中，自动让px转rem

```scss
@function torem($px) {
  @return $px * 1.6 / 100px * 1rem;
}
// 设计图宽 640→1.875
//      740→1.66
//      750→1.6
```

需要的时候，比如`width: torem(10px)`会自动生成`width: 0.16rem;`

在sublime中，使用snippet更方便

`工具 > 插件开发 > 新建代码片段`

```
<snippet>
  <content><![CDATA[
torem(${1:}px);
]]></content>
  <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
  <tabTrigger>torem</tabTrigger>
  <!-- Optional: Set a scope to limit where the snippet will trigger -->
  <!-- <scope>source.python</scope> -->
</snippet>
```

这样输入，保存，写scss的时候直接打`to`按tab就可以了。

具体插件开发移步[这里](https://jingyan.baidu.com/article/ce436649374e0a3773afd304.html)