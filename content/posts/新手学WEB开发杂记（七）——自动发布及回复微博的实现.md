+++
title= "新手学WEB开发杂记（七）——自动发布及回复微博的实现"
draft = false
date= "2013-04-25 21:04:51"
+++

    之前一直想写一个自动发博机，可以每隔一段时间自动发布一条微博。但是由于新浪微博API的使用需要access_token，而该token的获取需登录进行授权，而模拟登录的代码一直没想出来怎么写，计划就搁浅了。
 
    后来在sae开发者社区提出该问题后，有人说开发者自己的账号对自己创建的应用的授权是没有过期时间限制的，并向我推荐了这篇文章 http://cloudbbs.org/forum.php?mod=viewthread&tid=13391  该文章详细地讲解了如何从零开始创建一款带有回粉功能的求天气应用，即：用户发微博 城市名@求天气 ，一分钟内@求天气 微博号会自动给用户评论他所查询的城市天气。并且提供了源码的下载，推荐有兴趣创建一个自动发博机的初级开发者看一看。
    需要注意的一点是，该代码中的memcache缓存时间设置为600秒，若你设置的cron大于600秒则需要将缓存时间改大，否则会发生重复评论的问题。
 
    这是我写的自动发博机微博账号：http://weibo.com/u/3270877203 （兵库北小姐，我是香菜厨发自真心）。
 
 