制作图标字体：
进入https://icomoon.io/app/#/select
点击import Icons上传svg
点击生成的图标，并点击右下方Generate Font按钮
就会生成所需要的图标，hover在图标上，点击 Get Code就会知道图标的使用方法
点击download可以配置一些参数，然后确认下载，将fonts文件和style.css引入就可以使用字体了

vue-cli配置时没有dev-server.js文件因为2.0以后需要在webpack.dev.conf.js中配置了

//首先
const express = require('express')
const app = express()
var appData = require('../data.json')
var seller = appData.seller
var goods = appData.goods
var ratings = appData.ratings
var apiRoutes = express.Router()
app.use('/api', apiRoutes)

//找到devServer,添加
before(app) {
  app.get('/api/seller', (req, res) => {
    res.json({
      // 这里是你的json内容
      errno: 0,
      data: seller
    })
  }),
  app.get('/api/goods', (req, res) => {
    res.json({
      // 这里是你的json内容
      errno: 0,
      data: goods
    })
  }),
  app.get('/api/ratings', (req, res) => {
    res.json({
      // 这里是你的json内容
      errno: 0,
      data: ratings
    })
  })
}

在static中建立css文件夹，放入reset.css样式
css默认样式清除：http://cssreset.com

在src中删除asset文件夹，新建common文件夹，在common中放入之后常用的js,fonts,stylus文件夹
在webpack.base.conf.js中可以配置默认文件的路径，找到resolve -> alias在这里可以配置默认路径


border的宽度: 在手机端浏览的话物理像素是设备像素的2倍(iphone6)，1px的宽度在手机上会显示2px,那如何解决的，利用伪类:after/:before来进行border的设置，然后进行缩放，那么在手机端就不会有倍数的变化


Sticky footers
当页面不够长的时候页脚会浮在视窗底部，而当页面够长时，则会被内容向下推送

安装better-scroll



