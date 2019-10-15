@[toc](基于superslide的文字动画全屏焦点图)

# 一、前言

对于焦点图，我们平时用superslide已经能实现大部分效果，但有时候想实现些更绚丽的效果，例如现在一些流行的焦点图都有切换图片后几段文字（图片）独立动画出现，这样有视觉差异，动画效果更生动，其实如果你熟悉js，那么在superslide的基础上也可以做出一样的效果。

思路就是利用superslide的endFun回调函数，每次切换图片后执行endFun，然后结合css和html，用js控制文字的动画效果。

# 二、在线地址及GitHub源码
效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191015135630642.gif)
[-->在线演示地址](https://lujingtao.github.io/full-screen-text-animation-useing-superslide/)
[-->GitHub地址](https://github.com/lujingtao/full-screen-text-animation-useing-superslide)


# 三、核心代码讲解
代码比较长，就不全部贴出来了，这里解释下比较重要的代码：

css：

```css
.slideBox .bd dd{ position:absolute; display:none }
/* 这里的定位需与dd的playOut属性一致 */
.slideBox .bd .item1 .dd1{ top:50px; right:340px; font-size:50px; line-height:150%;   }
.slideBox .bd .item1 .dd2{ top:140px; right:340px; }
.slideBox .bd .item1 .dd3{ top:190px; right:340px; }
```

html：
```html
<div class="bd">
    <ul>
        <li class="item1" style="background:#1E90FF">
            <dl>
                <!-- palyIn属性为文字出现效果，写入jquery的animate函数，可按需修改。 palyOut属性要与css定义一致，用于返回到默认状态 -->
                <dd class="dd1" palyIn="{opacity: 'show',right:'140px'}" palyOut="{opacity: 'hide',right:'340px'}">《变4》全球3亿夺冠</dd>
                <dd class="dd2" palyIn="{opacity: 'show',right:'140px'}" palyOut="{opacity: 'hide',right:'340px'}">中国内地票房持平北美</dd>
                <dd class="dd3" palyIn="{opacity: 'show',right:'140px'}" palyOut="{opacity: 'hide',right:'340px'}">《变形金刚4》上周五中美同步上映</dd>
            </dl>
        </li>
        .............
    </ul>
</div>
```
以 .dd1 为例子，上面的代码意思是，首先css定义文字绝对定位在 top:50px； right:340px;，然后 .dd1加上属性playIn（展现方式），控制.dd1最终位置在 right:140px，从340px --> 140px，这就是动画过程了，playIn的值会写入jquery的animate函数，可以根据自身需要修改。

playOut值是 right:340px，这和css一致的，目的是让.dd1返回默认状态，为下次切换时做相同的动画效果。

这里可以看看js是怎么写的，通常下不用修改。

js：

```js
jQuery(".slideBox").slide({mainCell:".bd ul",titCell:".hd ul",effect:"fold", autoPage:"<li></li>",autoPlay:true,interTime:5000,
    endFun:function(i,c,s){
        var curItem = $(".bd li",s).eq(i);
        curItem.find("dd").each(function(j){
            // j*250，每行文字延迟执行时间，按需修改；同时这里会把playIn的值写入animate动画函数
            $(this).stop(1,1).delay( j*250).animate( eval( '(' +$(this).attr("palyIn") +')' )); 
        });
        $(".bd li",s).not(curItem).find("dd").each(function(j){
            $(this).stop(1,1).animate( eval( '(' + $(this).attr("palyOut")  +')' ) )
        });
    }
});
```
