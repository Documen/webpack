# webpack

*（配置  loader plugins**）*

#####  webpack 用来做甚？

* 打包：多个js文件打包成一个，减少服务器的压力
* 转换：typeScript-->JavaScript，scss-->css
* 优化： 性能的提升


## 基础

#### 1. 安装

```
madir webpack_demo
cd webpack_demo
npm install -g webpack
npm init
npm install --save-dev webpack
webpack -v    检查版本
```



#### 2. demo

```
cd webpack_demo
mkdir src
mkdir dist
```

*dist文件下建index.html*

```html
<!doctype html>
...
<div id="title"></div>
<script src='boundle.js'></script>
```

*src文件下建立entry.js*

```
document.getElementById('titile').innerHtml="hello webpack";
```

**终端运行打包命令**

```javascript
webpack {entry file} {destination for bundled file}
//{entry file}:入口文件路径
//{destination for boundled file}:打包后路径
注意：在命令行中不需要些{}
```



#### 3. 配置文件

```javascript
//webpack.config.js
module.exporys={
  entry:{
	entry:'./src/entry.js'
  },
  output:{
  	path:path.resolve(__dirname,'dist'),
  	filename:'bundle.js'
  },
  module:{},
  plugins:[],
  devServer:{}
}
```

​	**多入口多处出口配置**:

```javascript
entry:{
  entry:'./src/entry.js',
  entry2:'./src/entry2.js'
},
output:{
  path:path.resolve(__dirname,'dist'),
  filename:'[name].js'
}
```



####4.  服务和热更新

```
//下载：
npm install webpack-dev-server -save-dev
//执行
webpack-dev-server
```

​	**热更新配置**:

```javascript
//webpack.config.js
devServer:{
  contentBase:path.resolve(__dirname,'dist'),
  host:'192.168.1.152',
  compress:ture,
  port:1717
}
```



#### 5. css文件打包

**Loaders**

*  sass转化成css
*  ES6转化ES7
*  React中JSX转化成JavaScript

<u>所有的Loader都需要在npm中单独安装，并在webpack.config.js里进行配置</u>

* test：用于匹配处理文件的扩展名的表达式，必须
* use：loader名称，就是使用模块的名称，必须
* include/exclude:手动添加必须处理的文件（文件夹）或屏蔽不需要的文件（文件夹），可选
* query：为loaders提供额外的设置选项，可选

**css打包:**

 *css建立好后需要进入到入口文件中*

```css
import css from './css/index.css'
```
**下载loader**

<u>style-loader</u>		：处理css中的url等

```
npm install style-loader --save-dev
```

<u>css-loader</u>   		：将css插入到页面中的style标签中

```
npm install css-loader --save-dev
```

**配置loader**

```javascript
//webpack.config.js
module:{
  rules:[{
  	test:/\.css$/,
  	use:['style-loader','css-loader']
  }]
},
```

####  6. 插件配置：配置js压缩

  **压缩js代码**

​	uglifyjs-webpack-plugin  

​	插件，但无需下载

**配置**

````javascript
//webpack.config.js
const uglify = require('uglifyjs-webpack-plugin');

plugins:[
  new gulify()
],
````

#### 7. HTML文件的发布

**下载** html-webpack-plugn

```javascript
npm install html-webpack-plugin --save-dev
```



**配置** html-webpack-plugin

```javascript
//webpack.config.js
const htmlPlugin = require('html-webpack-plugin');
```

**配置**

* minify：对html文件进行压缩，removeAttrbuteQuotes是去掉属性的双引号
* hash：有效避免缓存js
* template：要打包的html模板路径和文件名

```javascript
//webpack.config.js里的plugins里进行插件配置
new htmlPlugin({
  minfiy:{
  	removeAttributeQuotes:true
  },
  hash:true,
  template:'./src/index.html'
})
```

#### 8.css中的图片处理

**下载** file-loader url-loader

```javascript
npm install file-loader url-loader --save-dev
```

* file-loader：解决路径问题
* url-loade：根据图片大小选择转为DateURI或者使用file-loader进行copy

**配置**

<u>注意：loader在使用的时候不需要引用，plugins才需要使用require引入</u>

```javascript
//webpack.config.js
modole:{
  rules:[{
  	test:/\.(jpg|png|gif)/,
  	use:[{
  		loader:'url-loader',
  		options:{
  			limit:500000
  		}
	}]
  }]
}
```

#### 9. css分离与图片路径处理

1. 把css从javaScript代码中分离出来、
2. 处理分离出来后的css中的图片路径不对的问题

**安装插件**

```
npm install extract-text-webpack-plugin
```

**配置文件**

```javascript
//webpack.config.js
const extractTextPlugin=require("extract-text-plugin");
//设置Plugins
new extractTextPlugin("/css/index.css");
//这里的/css/index.css是分离后的路径位置
```

**包装代码**

修改style-loader和css-loader

```
module:{
  rules:[
  {
  	test:/\.css$/,
  	use:extractTextPlugin.extract({
  		fallback:"sytle-loader",
  		use:"css-loader"
	})
  	},{
  		test:/\.(jpg|png|gif)/,
  		use:[{
  			loader:'url-loader',
  			options:{
  				limit:500000
			}
		}]
	  }
  ]
}
```

**图片的路径问题**

~~并没有遇见这个问题~~

**把图片放在指定文件夹下**

```javascript
module:{
        rules:[{
            test:/\.css/,
            use:extractTextPlugin.extract({
                fallback:"style-loader",
                use:"css-loader"
            })
        },{
            test:/\.(jpg|png|gif)/,
            use:[{
                loader:'url-loader',
                options:{
                    limit:500000,
                    outputPath:'img/'   //把图片放在指定文件夹下
                }
            }]
        }
      ]
    },
```

#### 10.处理HTML中的图片(img标签)

**安装插件**

```
npm install html-withimg-loader --save
```

**配置**

```javascript
{
  test:/\.(htm|html)$/i,
  use:['html-withimg-loader']
}
```

## 进阶

#### 11. SASS文件打包跟分离

**安装** 

* node-sass 	


* sass-loader

```
npm install node-sass --save-dev
npm install sass-loader --save-dev
```

**编写loader配置**

```javascript
{
  test:/\.scss$/,
  use:[{
  	loader:"style-loader"
  },{
  	loader:"css-loader"
  },{
    loader:"sass-loader"
  }]
}
//loader加载要有先后的顺序
```

**把SASS文件分离**

```javascript
//分离到了index.css中了
{
  test: /\.scss$/,
  use: extractTextPlugin.extract({
    use: [{
   		 loader: "css-loader"
    }, {
   		 loader: "sass-loader"
    }],
   		 // use style-loader in development
    	 fallback: "style-loader"
    })
 }
```

#### 12. 自动处理css3属性前缀

**安装**

1. postcss-loader
2. autoprefixer

```
npm install --save-dev postcss-loader autoprefixer
```

**postcss.config.js**

```javascript
//postcss.config.js
module.exports={
  plugins:[
  	require('autoprefixer')	
  ]
}
```

**编写loader**

```javascript
{
    test: /\.css$/,
    use: extractTextPlugin.extract({
        fallback: 'style-loader',
        use: [
            { loader: 'css-loader', options: { importLoaders: 1 } },
            'postcss-loader'
        ]
    })
}
```

#### 13. 去掉冗余的css

**安装**

1. purifycss-webpack   插件并不是loader
2. purify-css   插件的依赖

```
npm install --save-dev purifycss-webpack purify-css
```

**引入glob**

```
const glob=require('glob');
```

**引入purifycss-webpack**

```
const purifyCSSplugin=require('purifycss-webpack')
```

**配置**

```javascript
plugins:[	
	new purifyCSSPlugin({
		paths:glob.sync(path.jion(__dirname,'src/*html'))
	})
]
```

#### 14.增加Babel支持

 **安装依赖**

```
npm install --save-dev babel-core babel-loader babel-preset-react
```

```
npm install --save-dev babel-preset-env
```

**配置babel**

根目录新建.babelrc文件

```
{
  "presets":["react","env"]
}
```

**webpack.config.js中loader配置**

```javascript
{
  test:/\.(jsx|js)$/,
  use:{
  	loader:'babel-loader'
  },
  exclude:/node_modules/
}
```

#### 15. 打包后如何调试

*没学明白调试，，，在哪调试？怎么调试？*

* source-map 单独 行 列
* cheap-module-source-map 单独 行
* eval-source-map 开发阶段 
* cheap-module-eval-source-map

**简单的配置**

```
module.exprots={
  devtool:'eval-source-map',  //开发工具
  entry:,
  output:{
  
	}
}
```

<u>注意:调试在开发中是比不可少的，但是一定要在上线前修改webpack的配置，再打包上线</u>

##  实战

**依赖问题**

* 开发依赖

  "devDependencies":{

  }

* 生产依赖

  "Dependencies":{

  }


**npm安装**

```
1.npm install jquery
安装完成后，你会发现在package.json中并不存在这个包的依赖。如果你项目拷贝给别人继续开发，或者别人和你git合作，再次下载项目npm install时就会缺少这个jquery包。项目就会无法正常运行，所以这也是我们最不赞成的安装方法。
```

```
2.npm install jquery --save
安装完成后，它存在于package.json的dependencies中，也就是说它是生产环境需要依赖的包（上线时需要的以来包）。
```

```
3.npm install jquery --save-dev
安装完成后，它存在于package.json的devDependencies中，也就是说它是开发环境中需要的，上线并不需要这个包的依赖。
```

````
4. npm install 
安装全部项目依赖包
````

```
5. npm install --production
安装生产环境依赖包
```

**配置生产和开发并行**

修改package.json

```javascript
"scripts":{
  "server":"webpack-dev-server --open",
  "dev":"set type=dev&webpack",
  "build":"set type=build&webpack"
}
```

修改webpack.config.js文件

```javascript
if(process.env.type=="build"){
  	var website={
      //本地
  		publicPath:"http://"
	}
}else{
  var website={
      //线上地址
  		publicPath:"http://"
	}
}

查看传过来的值
console.log( encodeURIComponent(process.env.type) );
```

## webpack模块化配置

**js中模块化实现**

ES6中模块化

```javascript
文件名：leo.js
//定义一个函数
fuction leo(){
  	alert('i am leo'+'webpack');
}
//暴露接口
module.exports=leo;
```

入口文件的进行引用

```
import leo from './leo.js';
leo()
```

**webpack模块**

entry_webpack.js

```javascript
const entry={};
entry.path={
  entry:'./src/entry.js'
}
module.exports=entry;
```

webpack.config.js

```javascript
const entry =require("./webpack_config/entry_webpack.js")
```

修改入口文件部分

```javascript
entry:entry.path,
```

## 优雅打包第三方类库

**安装jquery**

npm install jquery --save

**用plugin引入**

```
//webpack.config.js
const webpack=require('webpack');

plugins:[
  new webpack.ProvidePlugin({
  	$:"jquery"
  })
]
```



## watch的正确使用方法

**配置**

```
watchOptions:{
  //检测修改时间毫秒为单位
  poll:1000,
  //防止重复保存而发生种重要编译错误
  aggregateTimeout:500,
  //不监听的目录
  ignored:/mode_modules/,
}
```

* webpack --watch


* npm run dev
* npm run build
* npm run server

## 实用技巧

**BannerPlugin插件**

```
new webpack.bannerPlugin('
	*name：
	*time：
')
```

**第三方类库抽离**

*在入口配置中引入vue和jquery*

```
entry:{
    entry:'./src/entry.js',
    jquery:'jquery',
    vue:'vue'
},
```

*修改配置*

```
new webpack.optimize.CommonsChunkPlugin({
    //name对应入口文件中的名字，我们起的是jQuery
    name:['jquery','vue'],
    //把文件打包到哪里，是一个路径
    filename:"assets/js/[name].js",
    //最小打包的文件模块数，这里直接写2就好
    minChunks:2
}),
```

**静态资源集中输出**

*使用copy-webpack-plugin插件*

```
//下载
npm i --save-dev copy-webpack-plugin
//引入
const copyWebpackPlugin=require("copy-webpack-plugin")
//配置
new copyWebpackPlugin([{
  from:__dirname+'/src/plugin',
  to:'./pulblic'
}])
```

**热更新插件**

```
new webpack.HotModuleReplacementPlugin()
```

