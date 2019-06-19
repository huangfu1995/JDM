# 移动端—JDM

## 头部滚动效果

> 监听窗口的滚动高度，根据滚动的距离 动态设置header的颜色

```javascript
    //获取banner的高度
    var h =  document.querySelector('.jd-banner img').offsetHeight;
    //监听窗口的滚动高度，根据滚动的距离 动态设置header的颜色
    window.onscroll = function () {
        var top = document.documentElement.scrollTop;
       //透明度的值最大为1
        var value = top / h;
        if(value > 1) {
            value = 1;
        }
        console.log(value);
        this.document.querySelector('.jd-header').style.background = 'rgba(222, 24, 27, '+ value +')';
        
    }
```

> 获取页面卷曲的高度：

1、各浏览器下 scrollTop的差异 
IE6/7/8： 
​	对于没有doctype声明的页面里可以使用  document.body.scrollTop 来获取 scrollTop高度 ； 
​	对于有doctype声明的页面则可以使用 document.documentElement.scrollTop； 
Safari: 
​	safari 比较特别，有自己获取scrollTop的函数 ： window.pageYOffset ； 
Firefox: 
​	火狐等等相对标准些的浏览器就省心多了，直接用 document.documentElement.scrollTop ； 
2、获取scrollTop值 
​	完美的获取scrollTop 赋值短语 ： 
​	var scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;

主流浏览器推荐使用 window.pageYOffset





 各种高:

- clientHeight：元素客户区的大小，指的是元素内容及其边框所占据的空间大小（经过实践取出来的大多是视口大小）
- scrollHeight: 滚动大小，指的是包含滚动内容的元素大小（元素内容的总高度）
- offsetHeight: 偏移量，包含元素在屏幕上所用的所有可见空间（包括所有的内边距滚动条和边框大小，不包括外边距



## 新闻模块

> 实现京东快报每2秒钟滚动一次，要求无缝

​	思路：

	//1. 使用定时器让ul进行滚动   
		//1.1 ul每次像素滚动一行的高度  1.2 设置过渡  1.3 使用translateY进行滚动
	//2. 给ul注册过渡结束事件，过渡结束后将第一个元素移动至最后，将ul位置复位（不需要过渡），
过渡结束事件：

`transitionend`事件在 CSS 完成过渡后触发。  

目前只支持 addEventListenter的方式注册事件

```javascript
ul.addEventListener("transitionend", function () {
  //过渡结束时，会触发transitionend事件
})
```

代码实现：

```javascript
 //新闻模块移动    
	var newUl = document.querySelector('.jd-news .info');    
    //向上移动  
    setInterval(function () {            
        newUl.style.transform = 'translateY(-30px)';
        newUl.style.transition = 'all 0.5s';
    }, 1000);

    newUl.addEventListener('transitionend', function () {
        //ul瞬间复位
        newUl.appendChild(newUl.querySelector('li:first-child'));
        newUl.style.transition = 'none';
        newUl.style.transform = 'translateY(0)';   
    })
```



## 秒杀模块倒计时：

> 实现时分秒的倒计时功能

```javascript
	//获取倒计时需要元素
	var spans = document.querySelectorAll('.jd-seckill .time span');
    //设置倒计时的实际
    var time = 5 * 60 * 60;
	//利用定时器进行计时
    var timer = setInterval(function () {
        time--;
        //倒计时完成后的处理方式
        if(time < 0) {
            time = 5 * 60 * 60;
        }
      
        var h = Math.floor(time/3600);    //获取小时
        var m = Math.floor(time%3600/60); //获取分钟
        var s = Math.floor(time%60);	  //获取秒
      	
		//时间格式为 00:00:00
        spans[0].innerHTML = h < 10 ? '0' +h : h;
        spans[2].innerHTML = m < 10 ? '0' +m : m;
        spans[4].innerHTML = s < 10 ? '0' +s : s;

    }, 1000);
```


## 京东轮播图

全屏轮播图的布局：

要求li的宽度铺满全屏， 且自适应

![](image/banner.jpg)

定时器滚动思路：

```javascript
//1. 页面结构：首尾都需要放一张假图片
//2. 初始化index=1
//3. 设置定时器让ul移动， 设置下标index++  设置过渡  使用translateX进行滚动
//4. 给ul注册过渡结束事件，当ul需要瞬间变回来。 让点跟着移动
//5. 方法封装

//小圆点同步显示
//1-排他  
//2-突出显示自己
//3-注意： 小圆点的索引值和 对应的li标签的索引值小于（因为li有备胎图片）
```



## touch事件

移动端新增了4个与手指触摸相关的事件。

```javascript
//touchstart:手指放到屏幕上时触发
//touchmove:手指在屏幕上滑动式触发（会触发多次）
//touchend:手指离开屏幕时触发
//touchcancel:系统取消touch事件的时候触发,比如电话
```

每个触摸事件被触发后，会生成一个event对象，event对象中`changedTouches`会记录手指滑动的信息。

```javascript
e.touches;//当前屏幕上的手指
e.targetTouches;//当前dom元素上的手指。
e.changedTouches;//触摸时发生改变的手指。
```

这些列表里的每次触摸由touch对象组成，touch对象里包含着触摸信息，主要属性如下

```javascript
clientX / clientY: //触摸点相对浏览器窗口的位置
pageX / pageY:     //触摸点相对于页面的位置
```



## 通过touch滑动轮播图

```javascript
//1. 给ul注册touch相关的三个事件（注意清除浮动，不然触发不到touchmove事件）
//2. 在touchstart中
	//1. 记录开始的位置
	//2. 清除定时器
//2. 在touchmove中
	//1. 记录移动的距离
	//2. 清除过渡
	//3. 让ul跟着移动
//3. 在touchend中
	//1. 记录移动的距离
	//2. 判断移动距离是否超过1/3屏，判断上一屏还是下一屏，或者是吸附
	//3. 添加过渡
	//4. 执行动画
	//5. 开启定时器

//4. 优化：快速滑动的实现逻辑

//5. 优化：重置大小时轮播图错位。
```



# 关于tap事件与click事件

1. click事件在pc端非常用，但是在移动端会有300ms左右的延迟，比较影响用户的体验，300ms用于判断双击还是长按事件，只有当没有后续的的动作发生时，才会触发click事件
2. tap事件只要轻触了，就会触发，体验更好。


```javascript
/*
 * 由于移动端的点击事件click 有300左右的延迟， 用户体验有待提升
 *  希望 能用touch事件封装一个相应速度更快的 点击事件 tap
 *
 *   touch判断为点击事件的条件：
 *
 *   1、触屏开始 到触屏结束  手指没有移动
 *   2、接触屏幕的时间 小于指定的值 150ms *
 *
 *   满足以上两个条件 可以认为是点击事件触发了
 * */


/*
*  插件功能：
*   给指定的元素 绑定优化后的移动的点击事件--- tap事件
*   参数：
*   obj:要绑定优化后点击事件的元素
*   callback: 当点击事件触发 执行什么操作
* */

var itcast={
    tap:function(obj,callback){
        if(typeof obj=='object'){ //判断传入的obj是否为对象

            var startTime=0;//记录起始事件
            var isMove=false; //记录是否移动

            obj.addEventListener('touchstart',function(){
                startTime=Date.now(); //获取当前时间戳
            });

            obj.addEventListener('touchmove',function(){
               isMove=true; //记录移动

            });

            obj.addEventListener('touchend',function(e){
                //判断是否满足点击的条件
                if(!isMove&&Date.now()-startTime<150){
                    //tap点击事件触发
                    //if(callback){
                    //    callback();
                    //}
                    callback&&callback(e);
                }

                //数据重置
                isMove=false;
                startTime=0;
            });
        }
    }
}
```
# zepto框架介绍（了解）

> **Zepto**是一个轻量级的**针对现代高级浏览器的JavaScript库， **它与jquery**有着类似的api**。 如果你会用jquery，那么你也会用zepto。

[github地址](https://github.com/madrobby/zepto)

[中文文档](http://www.css88.com/doc/zeptojs_api/)



## zepto与jquery的区别

+ jquery针对pc端，主要用于解决浏览器兼容性问题，zepto主要针对移动端。
+ zepto比jquery轻量，文件体积更小
+ zepto封装了一些移动端的手势事件



## zepto的基本使用

zepto的使用与jquery基本一致，zepto是分模块的，需要某个功能，就需要引入某个zepto的文件。

```javascript
<script src="zepto/zepto.js"></script>
<script src="zepto/event.js"></script>
<script src="zepto/fx.js"></script>
<script>

  $(function () {
    $(".box").addClass("demo");

    $("button").on("click", function () {
      $(".box").animate({width:500}, 1000);
    });
  });


</script>
```

## zepto的定制

安装Nodejs环境

1、下载zepto.js

2、解压缩

3、cmd命令行进入解压缩后的目录

4、执行`npm install`命令

5、编辑make文件的`41行`,添加自定义模块并保存，

7、然后执行命令 `npm run-script dist`

8、查看目录dist即构建好的zepto.js



## zepto手势事件

zepto中根据`touchstart touchmove touchend`封装了一些常用的手势事件

```javascript
tap   // 轻触事件,用于替代移动端的click事件，因为click事件在老版本中会有300ms的延迟
	//穿透的问题    fastclick:
swipe //手指滑动时触发
swipeLeft  //左滑
swipeRight  //右滑
swipeUp    //上滑
swipeDown   //下滑
```
