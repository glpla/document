

ES6

新方法:

注意:如果调用方法的对象不是一个String,会报错,说不是一个函数.只需要对对象做个类型转换即可:toString()

 startsWith('char')

endsWith('char')

每一个js文件都可看作是一个模块module.为了使别的js文件可以访问这个module,这个js文件必须主动暴露接口给别人使用

```js
//config.js
//暴露一个对象
export const config={
    name:"glpla",
    unit:"54414"
}
//暴露一个函数
export let fn=function(){}

//以上是分别用export暴露出每个数据.还可以在最后一次暴露出所有的数据
//推荐
export {config,fn}
```

别的js文件导入后即可使用.注意路径:不能是绝对路径哦哦哦!!!

```js
//index.js
//导入对象和函数
import { config,fn } from "path/config/js"
```



# 微信小程序

## tips

不是WEB开发,所以没有DOM哦

IDE采用VS CODE内核

没有hover高亮哦(移动端).通过为元素指定 cursor:pointer

在app.json里pages下添加结构时,可自动生成相应的文件夹和文件.

尽量采用flex布局.可以用到极致

onLoad(还没有渲染)->onShow->onReady(渲染完毕)

数据为单向绑定

函数的定义过程中,如果有默认参数,请在参数列表的最后给出.



## 结构

结构: wxml(wei xin markup language)

逻辑: js

样式: wxss(wei xin style sheet)

使用`@import`语句可以导入外联样式表，`@import`后跟需要导入的外联样式表的相对路径，用`;`表示语句结束

```css
@import "common.wxss";
```

全局样式与局部样式:

定义在`app.wxss`中的样式为全局样式，作用于每一个页面。在`[page-name].wxss`文件中定义的样式为局部样式，只作用在对应的页面，并会覆盖`app.wxss`中相同的选择器。

目前只支持如下选择器:

.class #id element ::after ::before

## wxs

wxs(weixin script)文件供wxml使用.类似ES6的module.但是ES6的很多关键字不能用哦。比如const/let,只能用var.

```js
//定义
//reg.wxs
var reg=function(){
    return 'glpla'
}
module.exports={
    reg:reg
    //可简写为 reg
}
```

```html
//在要使用的wxml中引入.注意不是import哦.且只能是相对路径哦
//引用
<wxs src='path/reg.wxs' module='util'/>
//使用module指定的reg方法
<view>{{util.reg()}}</view>
```

也可以把逻辑和wxml写在一起

```html
<wxs module="sample">
    var msg='glpla,hi.';
    module.exports.msg=msg;  
</wxs>

<text>{{sample.msg}}</text>
```

### Reg

正则表达式:和js里的不太一样哦

```js
//这个reg是用换行\n替换文本中的\\n.
//系统认为整个text是一个段落.所以换行后要追加空格.
//为了实现段落的缩进,人为的增加了2个空格&nbsp;
//为了识别&nbsp;需要为<text>指定decode属性
//而第一个段落的缩进可利用css实现:text-indent
var format=function(text){
    //为了保证使用text有数值,最好作下判断
    if(!text){
        return;
    }
    var reg=getRegExp('\\\\n','g');
    return text.replace(reg,'\n&nbsp;&nbsp;')
}
module.exports={
    format
}
```

使用

```html
//wxml
<text wx:for='{{util.format(res)}}'></text>
```



### filter

过滤器

```js
//过滤结果中的前几条数据
var limit=function(arr,len){
    return arr.slice(0,len);
}
module.exports={
    limit
}
```

使用

```html
//wxml
<text wx:for='{{util.limiet(arr,4)}}'></text>
```



## 项目配置

> app.json

backgroundTextStyle: let it be



navigationBarBackgroundColor:导航栏背景颜色

navigationBarTitleText:导航栏标题 

navigationBarTextStyle:导航栏文本颜色 (only: white/black)



backgroundColor:背景颜色

## 创建页面/文件

所有的页面在pages(名字可以任意)目录下.注意:只能以相对路径配置!!!

每个页面要在app.json中的pages项进行指定.第一个为默认页面.

Tips:文件重命名的时,按tab键可快速更改其他文件

每个页面包括(要和文件夹名字保持一致)

1. 逻辑文件.js
2. 结构文件.wxml
3. 样式文件.wxss
4. 页面配置文件.json

## 常用标签

<view>:容器,类似div

<button>:按钮.不同的open-type对应不同的功能.如getUserInfo表示要获取用户信息

<text>:文本标签.如用到了>等转义符号,应设置decode属性为true;space属性指定了文本中空格的大小.<text>内容只能是纯文本,不能嵌套其他标签.<text>碰到\n后,会换行哦.

```html
<text decode="true">&gt;</text>
```

<image>:图片,设置了宽度后,通常设置: mode=aspectFill,图片会自动铺满;aspectFit可能有留白.

<block>:抽象意义上的一个元素.多作为承载循环体使用.

<navigator>页面之间的跳转.默认是块元素哦.可在.wxss中指定hover-class模拟点击时的效果;可指定open-type为redirect为重定向.实际上就是返回不了上一页.hover-class也可用于其他视图,如view,只是体现了用户的点击效果,并没有真正实现交互.好玩而已.

参数传递:同传统WEB请求一样,采用?参数,附加在url后面.

```html
<navigator url="/pages/blue/blue?id=20" hover-class='none' open-type="redirect">
    <view>link</view>
</navigator>
```

或者使用API接口:

```js
wx.redirectTo({url:"/pages/blue/blue?id=20"});
```

参数获取:在目标页面的onLoad生命周期函数内利用形参para获取.类型为json值对.

```javascript
onLoad(para){
    console.log(para);
}
//{id: "20"}
```

<loading>:加载过程中的过渡组件.可放在页面任何位置.利用一个bool值判断.数据获取完毕后,设置该bool为另外一个状态,隐藏组件.

还有一种形式是在顶部的导航栏使用loading

```html
<loading hidden="{{loading-bool}}">
    loading...
</loading>
```

<scroll-view>

scroll-x:

```vue
<scroll-view class="wrap" scroll-x="true">
    <view id="green" class="green">12</view>
    <view id="red" class="red">23</view>
    <view id="yellow" class="yellow">34</view>
    <view id="blue" class="blue">45</view>
</scroll-view>
```

```css
.wrap {
  height: 200rpx;//宽度可以由子元素撑开
  display: flex;//必须
  white-space: nowrap;//必须
}
.wrap view {
  width: 100%;
  height: 200rpx;
  display: inline-block;//必须.不要以为父是flex,子就自动inline-block啦
}
```

scroll-y

只需设置<scroll-view>高度即可.

它的触底时间也可以由page的onReachBottom实现.组件里面触发不了page的这个事件哦.

<swiper>

一般给<swiper>指定如下属性.

必须为<swiper>指定宽高(可在样式中设置);而<swiper-item>可不用指定.

借助block循环生成多个<swiper-item>.需要展示的内容放在<swiper-item>内.

另:为<swiper>指定vertical='true'可实现垂直方向的滚动

current可指定显示哪一张/从哪一张开始

```html
<swiper indicator-dots='true' indicator-color='rgba(0,255,0,0.4)' indicator-active-color='rgba(255,255,255,1)' autoplay='true' interval='1000' duration='500' circular='true'>
    <block wx:for='{{imgs}}'>
      <swiper-item>{{item}}</swiper-item>
    </block>
</swiper>
```

<icon>:<icon type='' size='' color=''>

<switch>:可指定type为checkbox或switch.checkbox通常和checkbox-group配合使用;如果只有1个的情况下,就可以采用switch的checkbox样式.

<input>

专有事件bindconfirm.按键盘上完成(回车)触发

## data

定义:在page的data属性中定义数据

使用:同vue不一样的是,vue中可在函数中直接this.var,而XCX中必须是:this.data.var

赋值:this.setData({key:value});大多情况下,需指定_this=this以指向data.



来源:

1. 直接在page的data属性中定义
2. 定义在外部js中,然后require进来
3. 定义在外部文件中(如json)
4. 外部数据库
5. 在线API(强烈推荐!!!)

```php
_this=this;//注意指向
//或者使用ES6的箭头函数.最简单
//不推荐使用promise,其过程更为烦琐.(它主要解决异步嵌套-回调地狱)
wx.request({
    url:"",
    data:"",
    header:{"content-type":"application/json"},
    //传统方法:利用外部定义的_this或that
    success:function(res){
        console.log(res.data);
        _this.setData({...})
    },
    //ES6箭头函数形式:推荐
    success:(res)=>{
        console.log(res.data);
        this.setData({...})
    },
    fail:function(e){},
    complete:function(){}
})
```

## 语句

条件语句:为真是渲染

```html
<button wx:if="bool" bindTap="start">
    立即体验
</button>
```

循环语句:可采用block做为包裹,最好指定key.

```html
<block wx:for="{{arr}}" wx:key="{{arr}}" wx:for-index='idIndex' wx:for-item='idItem'>
    //默认item是每一项,index是相应的索引.指定后就要利用新指定的值
</block>
```

数组循环

对象循环

## 事件

传递参数:利用data-xxx指定,在事件处理中利用e.currentTarget.dataset.xxx获取

```
<view bindtap="nav" data-id='blue'>tap me</view>

nav: function(e) {
    console.log(e.currentTarget.dataset.id)
}
```



bindtap:点击事件,事件会一级一级冒泡到上层

catchtap:不会冒泡

bindtouchstart:按下并保持,如录音(录音管理API))

bindtouchend

## promise

promise是一个对象,可以保存状态(函数不可以,函数执行完会立即有结果返回),所以可以解决callback的问题.

3种状态:pending:进行中;fulfilled:成功;rejected:失败

```js
const promise=new Promise((resolve,reject)=>{
    //your code here
    //箭头函数只有一个参数一个语句的简写形式
    wx.getSystemInfo({
        sucess:res=>resolve(res),
        fail:error=>reject(error)
    })
})
//then的俩个方法,一个是成功,一个是失败.顺序不能颠倒哦
promise.then(
    //sucess
    res=>console.log(res),
    //fail
    err=>console.log(err)
})
```



## page事件

大部分东西都是写到page里,如在生命周期函数里获取当前页:

```js
onReady:function(){
    page=getcurrentpages()[0];
}
```

也有相当一部分写在page外.如:

```html
let app=getApp(),
page;
```

onLoad(){}:页面加载时执行

onShareAppMessage:设置分享(API的wx.showActionSheet或设置了open-type为share的button也可)

```js
onShareAppMessage: function() {
  return {
    title: '54414 75716 75660'
  }
}
```

## API

动态设置导航栏

wx.showNavigationBarLoading()

wx.hideNavigationBarLoading()

wx.setNavigationBarTitle()

## IconFont

1. 用github(glpla)登陆阿里图标库
2. 选择图标并添加到某个项目
3. 选择unicode,查看在线链接.复制下方的代码(以@font-face开头)至app.wxss
4. 下载至本地,解压后复制iconfont.css中的代码至app.wxss(接上面复制的代码).
5. 删除@font-face开头的那一段,保留从.iconfont {}开始的所有的代码
6. 按照类的形式开始使用:<text class='iconfont icon-shouye'></text>

## fontawesome

必须把ttf的文件进行base64的transfer才可以

## 自定义组件

定义组件

```html
//组件header.wxml
<view title='{{innerData}}' bindtap='dis' data-idx='glplaglpla' class='com-css'>
    //code here
    <slot></slot>
</view>
```



```js
//组件header.js
//properties中的数据是暴露给使用者使用的
properties: {
    title: {
        type: String,//数据类型
        value: ''//默认值
        //监视数据的变化.只有数据变化时才会触发哦
        //方式1:直接写函数
        observer:function(){}
        //方式2:引用methods中定义的函数(推荐)
        observer:'observerHandle'
    }
},
//data中的数据是给组件本身使用的.
data: {
    innerData:null
},
//在组件的methods选项中添加自定义事件
methods: {
    dis: function(){
        let myEventDetail = {};//组件传递给使用者的数据
        let myEventOption = {};//触发事件的选项
        this.triggerEvent('myevent', myEventDetail, myEventOption)
    },
    observerHandle:function(){}
},
    
//外部样式,编写外部类时可利用!important强制覆盖组件默认样式
externalClasses:['ex-class1','ex-class2']
```



```css
//组件的样式只有很少一部分能继承到页面的样式
//同样也意味着,组件获取不到app.wxss中定义的全局样式
//所以一般要单独定义,特别是用到了字体文件时,要另外引入
@import "/styles/iconfont/iconfont";
.com-css{}
```

使用组件

按普通组件使用即可.可指定data-xxx,可指定事件,可指定样式,可指定逻辑.但是这几种操作都不推荐,如:样式可能显示有问题,还是推荐写在组件内部.

先在使用页面的json文件中指定组件的名字和来源.注意:是双""NOT单'',标准的json写法哦.

```json
//index.json 
//可以使用绝对路径
"usingComponents": {
    "Header": "/components/header/header"
  }
```

然后在使用页面的wxml中使用:其中title是组件在properties中指定的对外接口,可指定具体数据,也可绑定使用页面的数据.

```html
//index.wxml
//利用数据绑定给组件的title赋值;并指定handlemyevent事件来响应组件暴露出来的事件myevent
//bindmyevent为bind:myevent的简写
<Header title='{{title}}' bindmyevent='handlemyevent' data-idx='glpla'>
    //code here
    <view>slot content</view>
</Header>
```



```js
//index.js
handlemyevent(event){
    console.log(event.detail);//detail中的数据就是组件传递过来的数据myEventDetail
}
```

指定slot:可以更好的扩展组件

默认情况下,组件没有起用slot功能.需在组件相应的js中指定选项为:(单slot可不加,但是最好加上)

```json
options:{
    multipleSlots:true
}
```



```html
 xxxxxxxxxx11 1//slot定义:用name指定不同的slot.可指定多个slot2<view>3    <text>{{title}}</text>4    <slot name='count'></slot>5</view>67//slot使用:假设组件接受的参数为txt,引入为v-tag8//text做为slot引入,匹配名字为count的slot9<v-tag txt='{{title}}'>10    <text slot='count'></text>11</v-tag>html
```

## 云开发

体系:

1. 云函数node.js.appid,openid
2. 云数据库MongoDB.增,删,改,查
   1. 是文档型数据库,非关系型数据库
   2. 数据库database->集合collection->记录doc/record->字段field
   3. 2GB的空间
   4. 数据类型:String Number Object Array Boolean GeoPoint Date Null
   5. 控制方式:小程序控制 云函数控制 控制台控制
   6. 数据库 API 分为小程序端和服务端两部分,小程序端 API 拥有严格的调用权限控制. 权限类别:仅创建者可写,其他人可读 仅创建者可读写 仅管理端可写 仅管理端可读写
   7. 支持request和promise
3. 云存储.管理,上传,下载,分享

开通

开发者工具->工具栏左侧，点击 “云开发”

默认配额下可以创建两个环境，各个环境相互隔离，每个环境都包含独立的数据库实例、存储空间、云函数配置等资源。每个环境都有唯一的环境 ID 标识，初始创建的环境自动成为默认环境。

### 增删改查

请确保有合适的权限哦

使用 API 操作数据库只需三步：获取数据库引用、构造查询/更新条件、发出请求.

获取数据库引用:

获取对默认环境的数据库的引用.

```js
const db = wx.cloud.database();
```

获取其他环境的数据库引用，传入env 对象参数即可.

```js
const testDB = wx.cloud.database({
  env: 'test'
})
```

插入Insert.成功后系统会自动增加用户ID: _openid 和记录ID:  _id. 开发者可以自定义 _id，但不可自定义和修改 _openid.

_openid:标志记录的创建者，即小程序的用户. 在管理端（控制台和云函数）中创建的不会有 _openid 字段，因为这是属于管理员创建的记录.

_id:用以唯一标志一条记录.可指定也可以由系统自动分配

```js
db.collection('userInfo')//需手动在云开发中筹建这个集合
      //add往集合中插入一条记录
      .add({        
        data: {
          //_id:'这里_id由系统分配',
          done: false,//key可以不用""
          'name': "glpla",//JSON的值对格式
          "date": new Date("2019-08-11"),
          "tags":['reading','swiming','book'],
          "sum": 20,
          "location": new db.Geo.Point(113, 23)
        }
      })
      .then(res => {
        console.log(res)
      })
      .catch(err => {
        console.log(err)
      })
```

删除remove.

```js
db.collection("userInfo")
      //doc 用来获取集合中一个指定 ID 的记录的引用
      .doc("25c59b425d4e49fc104c8f4f7993c0a6")
      .remove()
      .then(res => {
        console.log(res)
      })
      .catch(err => {
        console.log(err)
      })
```

更新update.

```js
db.collection('userInfo').doc("25c59b425d4e49fc104c8f4f7993c0a6")
      .update({
        data: {
          'name': "cnplaman"
        }
      })
      .then(res => {
        console.log(res)
      })
      .catch(err => {
        console.log(err)
      })
```

查找search.

获取一个记录:利用doc方法

获取多个记录:利用where方法

获取一个集合的数据,即获取 集合的所有记录.在集合上调用 get 方法获取.但不建议这么使用.需尽量避免一次性获取过量数据,只获取必要数据.为了防止误操作以及保护小程序体验,服务器一次默认并且最多返回 20 条记录.云函数端这个数字则是 100.当然开发者可通过 `limit` 方法指定获取记录的数量,但小程序端不能超过 20 条,云函数端不能超过 100 条.

```js
db.collection("userInfo")
      .where({//用where构造查询条件
        "name": 'glpla'
      })
      .get()//get方法会触发网络请求,往数据库取数据/获取结果
      .then(res => {
        console.log(res)
      })
      .catch(err => {
        console.log(err)
      })
```

### 导入

云开发支持从文件导入已有的数据。目前仅支持导入 CSV、JSON 格式的文件数据。有云开发控制台和HTTP API两种导入方式.



## vant

安装node.js的前提下

1. miniprogram->终端(cmd)打开

2. npm init(一路回车下去,OK后,在miniprogram下面出现package.json文件.里面是上面各选项相关的配置)

3. npm i vant-weapp -S --production(安装组件库)

4. 开发者工具->工具->构建npm

5. 开发者工具->详情->使用npm模块

6. 在使用组件页面的json中引入相应的组件:

   ```json
   {
     "usingComponents": {
       "van-button": "vant-weapp/button"
     }
   }
   ```

   

7. 在wxml中使用:

   ```html
   <van-button type="info" size="large">大号按钮</van-button>
   ```

   

8. 创建云函数,如:movieLists,然后在终端(cmd)中打开

9. 先安装request:

   ```
   npm install --save request
   ```

   

10. 再安装request-promise:

    ```
    npm install --save request-promise
    ```

    

11. 俩个安装完毕后,在该云函数的目录下多了一个package.json文件.里面有相应的依赖

    ```json
    "dependencies": {
        "request": "^2.88.0",
        "request-promise": "^4.2.4",
        "wx-server-sdk": "latest"
    }
    ```

    

12. 在云函数的js文件引入,如index.js

    ```js
    let rp=require('request-promise')
    ```

    

13. 

14. 引入

    

## 腾讯云

COS:cloun object storage

## 尺寸单位

规定整个屏幕为750rpx,相对于分成了750份

rpx（responsive pixel）: 可以根据屏幕宽度进行自适应。规定屏幕宽为750rpx。如在 iPhone6 上，屏幕宽度为375px，共有750个物理像素，则750rpx = 375px = 750物理像素，1rpx = 0.5px = 1物理像素

rem（root em）: 规定屏幕宽度为20rem；1rem = (750/20)rpx 

## 插件

### three.js

基本组成

scene:就是一个容器,用于放置所有的元素.

camera:透视/正交

renderer:

这三要素都是为mesh服务

每个mesh都是若干个几何体和材质的结合体



步骤:

1. 创建scene
2. 创建camera:角度,纵横比,近场,远场,位置,视角
3. 创建renderer:颜色,大小,添加到body.实际上renderer的结果是一个画布canvas.
4. 创建几何体
5. 创建材质
6. 创建mesh
7. 把mesh放置到场景中
8. 创建光源
9. 渲染

### orbitControl

# vue

MVC: Module(后端:数据模型) View(前端:视图) Controler(中间件)

MVP:Presenter

MVVM: ViewModule

易上手,适合移动端

el最好是#选择器,且不要挂在body

## 指令

v-model

双向绑定,表单元素,多用于input

v-for

循环数组或对象.如有2个,默认第1个是值,第2个是索引/键;如只有1个,那就是值.通常用item表示值,用index表示数组对象的索引,用key表示json对象的键.当然你也可使用任意合法的变量名.vue是根据位置自动处理的.

```vue
v-for='(item,index) in arr'
v-for='(item,key) in json'
```

v-show

v-bind

绑定属性,可简写为:.

绑定class:需要在样式中定义相应的类,且在data中定义相应的变量

数组格式:

:class='class0'//指定1个类(名)

:class='[class0,class1]'//指定多个类(名)

对象格式:

:class='{class0:bool}'

:class="{class0:bool, class1:boo}"

JSON格式(推荐):

:class="json"//json中指定相应的class和bool

三目运算选择类:

:class=' !isOk? show: hide '



绑定style:

:style='styleObj'

:style="{top:abreTop+'px'}"

:style='{backgroundImage: "url(img/rou"+picIndex+".jpg)"}'

:style='{backgroundImage:"url("+link+")"}'

:style="{'background-image':'url(img/rou0.jpg)'}"

:style="{backgroundImage:'url(img/rou0.jpg)'}"



 :src="item" 



v-on

绑定事件,可简写为@.

因为事件中指定的函数默认就是方法,所以可不带().如要有参数,那就要带上().其他地方对函数的绑定一般都要带上(),否则vue会以为是data中的数据,而不是methods中的函数.

事件类型:

```vue
v-on:click='fn'
v-on:mouseover='fn'
v-on:mouseout='fn'
v-on:moustdown='fn(para)'
v-on:mouseup='fn'
v-on:dblclick='fn' ( NOT:dbclick)
//input 事件
v-on:keyup.enter='fn'//还可以是up/down/left/right/delet/backspace/space
v-on:keydown.13='fn'
v-on:keyup.alt.enter='fn'
@keyup.enter='fn'
v-on:keyup.alt.enter='fn'
//右键事件
v-on:contextmenu='fn'
```

事件

默认是冒泡:点s会由内到外(到f)依次触发(c->b).

```html
<div class="f" v-on:click='bClick'>
    <div class="s" v-on:click='cClick'></div>
</div>
```

```vue
bClick(e) {
  console.log('b');
},
cClick(e) {
  console.log('c');
}
```

取消冒泡:

指定事件时同时指定stop关键字:

```vue
<div class="wrap" v-on:click.stop='cClick'></div>
```

或:在事件处理函数中指定:e.cancelBubble = true;

```
cClick(e) {
  e.cancelBubble = true;
  console.log('c');
}
```

取消默认行为

指定事件时同时指定prevent关键字

```vue
<div class="wrap" v-on:contextmenu.prevent='cClick'></div>
```

或:在事件定义中加入

```vue
cClick(e) {
  e.preventDault = true;
  console.log('c');
}
```

## 过滤器

常见过滤器如下,多个过滤器用|分隔,可在过滤器后面指定参数.

确保当前vue版本支持过滤器

uppercase/lowercase/capitalize/currency

{{tips|uppercase}}

{{tips|lowercase|capitalize}}

{{tips|currency}}

{{tips|currency 'rmb'}}



# html

DTD->xml->xhtml->html->js

网景公司

livescript->javascript



# php

## project

目的:利用PHP读取doc

请放弃IE,谢谢

### 配置:

1. 打开php.ini
2. 去掉注释:extension=php_com_dotnet.dll
3. 去掉注释:com.allow_dcom = true
4. restart all services

request and save word

请求资源(doc):弹出对话框,询问下载,保存,取消

```js
window.open('http://localhost/word.doc');
```

display word

说明:显示.doc文档. gb2312预防乱码

```php
<?php
header("content-type:text/html;charset=gb2312");
$word=new COM("Word.Application") or die("can't start word");
$word->Visible=1;
echo "{$word} v.{$word->Version}";
echo "<br>";
$word->Documents->Open(dirname(__FILE__).'/test.doc');
$cont=$word->ActiveDocument->content->Text;
echo $cont;
$word->Quit();
$word=null;
flush();
?>
```

### .word->.html

说明:将本地的.doc转化成.html

```php
<?php
header("content-type:text/html;charset=gb2312");
$word=new COM("Word.Application") or die("can't start word");
$word->Visible=1;
$tar='c:/wamp/www/test.html';
$word->Documents->open('c:/wamp/test.doc');
$word->Documents[1]->SaveAs($tar,8);
$word->Quit();
$word=null;
flush();
?>
```



# css

## Tips

1. 静态页面布局可大胆使用flex.但是...如果页面存在交互或动画等导致部分元素hidden, transform的效果,整个页面就会乱掉.这种情况下,请使用传统的position布局...
2. line-height:1;按照字体大小进行比例调整
3. fixed布局,一般要设置宽高,并指定z-index:99(尽可能的大,确保位于顶部)

## background

容器要需显式设置高度.如不显示图片,多半是没指定高度,或继承不到高度

1. background-color
2. background-image
3. background-repeat
4. background-attachment
5. background-position
6. background-size
7. background-origin

## transform

transform-origin

transform: none;可取消所有的效果,简单粗暴直接,哈哈!

## animation

动画属性

1. animation-name: 动画名称
2. animation-duration: 完成动画时长,单位秒(s)
3. animation-timing-function: 动画速度曲线( ease/linear/ease-in/ease-out/ease-in-out )
4. animation-delay: 动画延迟时长,单位秒(s).通常指定负数!!!
5. animation-iteration-count:动画播放次数(n / infinite)
6. animation-direction: 轮流反向播放(normal / alternate)
7. animation-fill-mode: 动画结束时停留位置( forwards / backwards )
8. animation-play-state: paused/running/unset
9. animationend: 事件,动画结束后触发.动作callback函数使用

tips

先做页面元素的静态布局,然后设置页面元素的初始状态(同一个元素,后面定义的样式会覆盖前面的样式),最后才是定义动画.

定义动画:可利用关键字 from和to,也可使用百分比.如从默认状态开始, from 或 0%可略.关键字也可配合百分比使用,但不推荐.

对同一个元素而言,transition和animation这二种方式应区分使用.混着使用,可能导致效果失败.请注意.部分插件也是基于transition的,如 zepto.

创作连续动画时,为了保持动画前后的一致,通常要指定animation-fill-mode:  backwards,即:使动画停留在最后,以便于继续向前操作.

关键字形式:只有2个状态.

```css
@keyframes animation-name{
  from{
  }
  to{
  }
}

```

百分比形式:可设置多个状态.

```css
@keyframes animation-name{
  0%{
  }
  50%{
  }
  100%{
  }
}
```

动画结束事件

```javascript
el.addEventListener('animationend', () => {
    console.log('done');
})
```

例:一个元素先淡入,然后循环上下跳动.

思路1:用2个元素.单纯的给元素外加1个容器,什么属性都不用设置.先让整个容器fadeIn,然后单独让元素上下跳动.

```html
<div class="bear-box">
    <div class="bear"></div>
</div>
```

思路2:指定2个动画,采用连续动画的形式.前一个动画必须指定forwards.

```css
 animation: fadeIn 0.5s linear forwards, updown 1s 0.5s linear infinite;
```

例:旋转进来->停留->旋转出去.略微有点抖动.animation也需要指定变形起点: transform-origin.为了方便动画的复用,通常将动画写成类,哪个需要,哪个就添加这个类.

```css
.test {
    width: 100px;
    height: 400px;
    background: #f60;
    margin: 200px auto;
    transform-origin: center bottom;
    opacity: 0;
}
.test animate{
    animation: rotate 1s linear forwards;
}

@keyframes rotate {
    0% {
      transform: rotateZ(90deg);
    }

    25% {
      transform: rotateZ(-8deg);
      opacity: 1;
    }

    30% {
      transform: rotateZ(0deg);
      opacity: 1;
    }

    60% {
      transform: rotateZ(0deg);
      opacity: 1;
    }

    75% {
      transform: rotateZ(8deg);
      opacity: 1;
    }

    100% {
      transform: rotateZ(-90deg);
      opacity: 0;
    }
}
```

例:上下左右晃动.指定4个点即可.

```css
@keyframes scale {
    25% {
      transform: translateX(10%) translateY(0%);
    }

    50% {
      transform: translateX(-10%) translateY(0%);
    }

    75% {
      transform: translateX(0%) translateY(10%);
    }

    10% {
      transform: translateX(0%) translateY(-10%);
    }
}
```

## viewport

视口

移动端: 320px-640px

## rem

匹配移动终端尺寸:移动端设计稿中的尺寸一般是640&750;

1. 把设计稿的尺寸分为若干份,以容易运算为原则,如:20份.每一份就是对应的1rem.假设设计稿是640px,则1rem=640px/20=32px;
2. 测量设计稿中元素的宽度(假设64px),则元素对应的宽度比/份:64/32=2;
3. 同理,移动终端屏幕的尺寸W,分为20份,那么每份就是1rem=W/20,设置html的font-size=W/20 px即可;
4. 确保在页面获取的第一时间设置.通常放在<head>区,且要在加载其他库文件之前.当用户调整屏幕尺寸时(多用于测试),也应该相应的重新设置大小.
5. 元素最终大小为:2*1rem;

```javascript
<script>
    document.querySelector('html').style.fontSize = window.screen.availWidth / 20 + 'px';
    window.onresize = function () {
      document.querySelector('html').style.fontSize = window.screen.availWidth / 20 + 'px';
    }
  </script>
```

## scss

scss可以帮我们完成重复的工作,比如计算/转换

文件后缀名为.scss

下面例子将元素原始大小转化成相应的rem大小:

```scss
@function pix2rem($size){
    @return ($size/32)*1rem;
}
body{
    width:pix2rem(20);
}
```

下面例子将重复代码混入/整合:

```scss
@mixin hcenter($url,$width){
    background:url($url) no-repeat;
    position:absolute;
    left:50%;
    margin-left:(-$width/32/2)*1rem;
}
.box{
    @include hcenter('img/bg.png',400);
}
```

嵌套使用:

```scss
@mixin size($w,$h){
    width:pix2rem($w);
    height:pix2rem($h);
}
.wrap{
    @include hcenter('img/rou.jpg',200);
    @include size(200,300);
}
```

# bootstrap3

警告:需要把整个BS文件夹放在目录下,然后引入css,否则字体可能显示不完整.如要使用BS3的组件,还需要先导入JQ库(依赖JQ).

## table

<thead>中的各项要水平居中,需写到每个<th>上.而tbody中只需要写到<tr>,所以可用css进行统一定义.

```html
<th class='text-center'>...</th>

<tr class='text-center'>...</tr>
```

responsive table without width

```html
<div class="table-responsive">
    <table class="table table-striped table-hover table-condensed table-bordered">
    </table>
</div>
```

table的单元格默认是顶部对齐,单独设td/th都不行.权重不够.一定要尽可能的增加权重

```css
table.table thead tr th,
table.table tbody tr td {
    text-align: center;
    vertical-align: middle;
}
```

## modal

点击模态框的取消或确认,模态框消失.有2种方法:

1. data-dismiss="modal"
2. $('#smallModal').modal('toggle');

# swiper

三级结构:

.swiper-container->.swiper-wrap->.swiper-slider

```javascript
let swiper = new Swiper('.swiper-container', {
    direction: 'vertical',
    loop:true,
    pagination:'.swiper-page',
    nextButton:'next_btn',
    prevButton:'prev_btn',
    scrollbar:'.swiper_scrollbar'
});
```



# web server

分类: apache / iis / tomcat / ngix

集成包: wamp / appServ / PHPStudy 

架构: Browser/Server  Client/Server



## art-template

目的:快速渲染HTML页面

需求:IE8+

> 步骤:
>
> 1. 引入到文件中.先引入后使用;
> 2. 准备一个模板
> 3. 准备一个数据
> 4. 渲染页面
>

```php+HTML
<?php
	echo "glpla";
?>
```

# JQuery

## 简介

jQuery是一个基于javascript的插件库;通过jQuery,您可以选取(查询,query)HTML 元素,并对它们执行“操作”(actions).

jQuery基本没有属性,都是以方法的形式存在.

1.  引入

确保库文件在合适的位置.

```html
<script src="./jquery-3.3.1.min.js"></script>
```

 

使用:所有jQuery 函数位于一个document ready 函数中.防止文档在完全加载(就绪)之前运行jQuery 代码.如在文档没有完全加载之前就运行函数,操作可能失败.

window.onload=function(){}//原生js

 

$(document).ready(function(){… …})//jQuery标准模式:确保有一个独立的作用域. 

$(function(){… …})//jQuery简写模式

 

语法:jQuery的变量通常以$开始,如:$name.

$(selector).action();

 

Js vs Jquery:

可把js对象丢到$()里,将其变成一个jq对象:

let oBox=document.getElementById('box');

$(oBox).html('…');

也可把jq对象转换成js对象:利用id获取元素时,通常是唯一的,可用[0]直接获取到;如果是类,就不一定是唯一,要根据DOM结构决定是第几个.

Let $box=$('#box');

$box.get(**0**).innerHTML='…';

更为简单的是:$box[0].

# Git/github

## 常见命令

1.安装git:百度git,下载相应的版本即可

注册github:使用email注册github帐号并登陆github.

2.创建仓库:点头像旁边的+号创建仓库Repository .这个仓库用来存放你的code.输入仓库名(必填),其他选项根据需要填写.点下面的创建按钮即可.仓库页面中的地址有HTTPS和SSH俩种.通常使用HTTPS.记住这个地址.仓库所有的操作都是在这个地址上进行的.如微信小程序的github地址为:https://github.com/glpla/weixin.git

3.检查git版本:

$git --version 

4.配置git:

Open terminal: 右键->git bash here.在cmd窗口中输入了部分指令后,可按tab键自动补全;如果没有补全,那就是该指令输的还不够多.再输几个字母就可以.

$git config --global user.name 'your name'

$git config --global user.email 'your email'

5.检查git用户信息:

$git config  user.name

$git config  user.email

或者使用:$ git config --list,会列出所有相关的信息/所有 Git 当时能找到的配置

 6.初始化仓库

$git init:

7.克隆仓库clone

$git clone url newName

url就是上面要你记住的仓库地址.你可以采用项目原有的名字,也可以指定一个新名字(通常不这么做).

在cmd窗口中不能直接粘贴,可右键点击cmd窗口->paste.如果是空仓库的话,系统会提示你下了一个空仓库.

可使用DOS命令cd 到下来的仓库文件夹: cd weixin

8.$git status

检查文件当前状态.工作目录下每一个文件都不外乎这两种状态：已跟踪或未跟踪.查看当前项目各文件的状态.如有没有工作区文件未添加到暂存区(Untracked),工作区文件修改未提交到暂存区(Unstaged),暂存区的文件未提交到仓库(Changes to be committed)等等.有问题解决问题。

当仓库与工作区，暂存区文件一致时，提示应该是:nothing to commit, working tree clean.

9.$git add filename.extension

追踪新文件:如果$git status提示你:Untracked files:xxx,说明有文件没有被系统追踪.只需要使用add命令即可添加到系统的追踪列表中

10.$git commit

提交更新:每次准备提交前,先用 $git status 看下,是不是都已暂存起来了, 然后再运行提交命令.

11.$git log

打印操作的相关日志/查看版本提交记录 

12.$git remote -v

查看本次clone仓库的url(好象没有什么卵用.你都知道了才可以clone.都clone了还不知道是什么吗?)

13.$git remote add <shortname> <url>

添加远程仓库.

14.$git remote show origin

查看远程仓库信息

完整命令更新仓库:

流程:working area(工作区)->stage(暂存区)->repository(仓库)

先添加文件add:这个过程没有什么提示.然后再推送文件commit.一定要添加备注信息-m.成功会提示你几个文件变化bla bla;最后再push到仓库去.这个过程会提示你输入,即使你在浏览器已经登陆了github,还是会提示你输入github帐号和密码.成功后,把文件推送上去并给出相应的提示.如果文件没有更新过再次push时,会提示你:Everything up-to-date.是不是很好玩?可以编辑下文件再次add->commit->push.(为什么不能直接push过去呢.真是麻烦)

$git add index.html//添加指定文件.要完整文件名和后缀哦.

$git add *//添加所有内容.NOT: * . *哦.

$git commit -m 'some msg here'

$git push

create:

$ssh-keygen -t rsa -C [1942194789@qq.com](mailto:1942194789@qq.com)

get:

$cat ~/.ssh/id_rsa.pub

## 其他操作:

shift+右键: 在此处打开cmd

cls: 清窗

directory:

$mkdir css

$mkdir html

$mkdir js

$mkdir lib

$cd html



file operation:

$touch index.html

$rm index.html

rm file

rm -r directory

## github

删除仓库:当前仓库->setting->danger zone(最下方红色区域块)->delet this repository->输入仓库名确认->删除



# vs code

## git

操作1:下载仓库项目并保持同步

1. 下载clone:把远程仓库的项目下载到本地.要记得自己项目仓库的地址哦
2. 打开:用vs code打开项目,正常编辑文件.git图标栏会有相应的提示:changes 数字.表示有几个改变.
   1. D:文件不存在,可能被移走或者删除.
   2. M:文件被编辑/被修改
   3. U没有被添加到暂存区Untracked.大多是创建了新文件.系统没有追踪到
3. 缓存add:选择+号(stage all changes)或依次点击提示项目后面的+号.添加到staged(缓存起来)直到提示变为:changes 0.
4. 提交commit:在上方的消息框中输入提交信息(-m).按上面的对号/或ctrl+enter,开始提交.
5. 推送push:点击...下拉菜单,选择push,把项目推送到仓库.

操作2:把现有项目推送到仓库

1. 先在github上建立一个仓库用来保存你的项目,记好这个地址哦
2. 在当前项目目录下,右键->git bash here,打开git控制台.vs code的cmd 窗口不可以哦.

```cmd
$git init
$git add *
$git commit -m "some msg"
$git remote add origin url//你仓库的url
$git push -u origin master//提交到仓库
```



## 操作

 Font Size:12->18

 Tab Size:4->2

 Word Wrap:off->on

## 插件

必用

vscode-icons:美化了vscode资源管理器中文件夹和文件的图标样式.需要在

auto rename tag:修改一对标签中的一个时,自动同步修改另外一个.修改前或后都可触发

html css suport:好象会自动安装.智能提示样式类

Live server:项目需以文件夹的形式在vs中打开

view in browser

open in browser

Bracket Pair Colorizer

VS Code JavaScript (ES6) snippets

vetur

Vue-color

HTML snappers

选用

icon Fonts:对使用fontawesome等字体图标的项目.奇怪的是你打fa-会出现fab相关的类.

css peek:样式关联.ctrl+html中的某个class或id可以自动跳转到样式文件中的定义处.适用于查看样式,新写的样式需要重新打开项目才可以.坑爹吧?

# typora

标题:#+空格+内容+回车.没有空格不出效果哦.一个#代表一级标题,2个#号代表二级标题,依次类推.共6级标题

引用:>.与标题不同的是,>和内容之间可以不加空格.多级引用可多次使用>.引用结束,以回车结束.多级引用要多次回车哦

代码:```+语言+回车,中间不用空格哦.如三撇+js就生成一个js代码块.

水平分割线:三个星号或者三个减号+回车

有序列表:1.+空格,会自动缩进,然后输入你的内容.

- 增加缩进:tab或ctrl+]

- 减少缩进:ctrl+[

无序列表:*号+空格/+号+空格/-号+空格都可以

多级无序列表:tab增加缩进

# E-tall

- ETA:Estimated Time of Arrival,预计到达时间,ETA 20 min
- sandal 凉鞋

