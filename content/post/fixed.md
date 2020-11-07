---
title: "页面元素在窗口滚动到指定位置的时候fixed定位"
date: 2019-05-07T15:23:31+08:00
draft: true
tags: ['fixed', 'css', 'jquery']
categories: ["IT技术"]
---



```
 <div class="top">
 　　　　·····内容
 </div>
 <div class="scroll"></div>
 <div class="bottom">
 　　　　·······内容
 </div>

 .sfixed{
 　　position:fixed;
 　　top:0px;
 　　left:0px;
 　　z-index:10;
 }
 .scroll{
 　　······样式
 }

//Jq:
<script> 
　　　　var top1=$(".scroll").offset().top; 
　　　　$(window).scroll(function(){
　　　　　　var  win_top=$(this).scrollTop();
　　　　　　var  top=$(".scroll").offset().top; 
　　　　　　if(win_top>=top){ 
　　　　　　　　$(".scroll").addClass("sfixed"); 
　　　　　　}//如果此处用else判断来remove sfixed这个类的话是不行的，因为当加上这个类的时候，".scroll"这个元素fixed的top值是0，获取到的offset.top就相当于是当前滚动条的滚动的距离，win_top与top值就一直相等了，这样会导致抖动；只有与以前的距离做比较才能实现滑上去的时候能回到原来的位置。

　　　　　　if(win_top<top1){ 
　　　　　　　　$(".scroll").removeClass("sfixed");
　　　　　　} 
　　　　}) 
</script>
```