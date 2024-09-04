---
layout: default
title: Js Browser
parent: JavaScript
grand_parent: Front-end Dev
nav_order: 10
---


 Here are commonly used JavaScript examples .
{: .fs-6 .fw-300 }



1. TOC
{:toc}


# demo 

```javascript
// ==UserScript==
// @name         small-rose
// @namespace    http://tampermonkey.net/
// @version      2024-08-31
// @description  try to take over the world!
// @author       small-rose
// @match        https://roum18.xyz/*
// @match        http://roum18.xyz/*
// @icon         data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==
// @require      https://code.jquery.com/jquery-2.1.4.min.js
// @grant        none
// ==/UserScript==

(function() {
    'use strict';
    let domainList = ['roum18.xyz',''];
    let balckDomainList = ['ra7.xyz',''];

    let myFunctionList = {

        roum18xyz: function(){
            let max = 3 ;
            let tips=setInterval(() => {

                console.log("启动完成");
                let ga = $("ins a");
                console.log(ga);
                if (ga.attr("href") && contains(ga.attr("href"), "ra7.xyz")){
                    ga.parent().parent().remove();
                }

                $('div').filter(function() {
                    var $this = $(this);
                    return ( $this.css('display') === 'flex' &&
                            $this.css('position') === 'fixed' &&
                            $this.find('img[src*="ra7.xyz"]').length > 0 );
                }).each(function() {
                    // 对符合条件的元素进行操作
                    console.log($(this).text()); // 示例操作：打印元素的文本内容
                    $(this).remove();

                });

                $("div[class*='headline'], div[class*='bookid_imgSquare']").each(function() {
                    // 对每个匹配的元素执行操作
                    console.log(this.id); // 输出元素的ID
                    $(this).remove();
                });

                $("a[class*='headline'], div[class*='bookid_imgSquare']").each(function() {
                    // 对每个匹配的元素执行操作
                    console.log(this.id); // 输出元素的ID
                    $(this).remove();
                });

                if(max == 0){
                    clearInterval(tips);
                    console.log("关闭提示");
                }
                max--;
            }, 3000);
        }

    };

    function contains(str, substr){
        return str && substr && str.indexOf(substr)>=0 ;
    }

    function dispatchMethodBasedOnDomain(host) {
        switch (window.location.hostname) {
            case 'roum18.xyz':
                myFunctionList.roum18xyz();
                break;
            case 'domainb.com':
                roum18Xyz();
                break;
                // 你可以根据需要添加更多的域名和方法
            default:
                console.log('未匹配的域名');
                break;
        }
    }

    /* $.each(domainList, function(index, value){
        dispatchMethodBasedOnDomain(value);
    });*/

    //http://roum18.xyz/
    var domain = window.location.hostname;
    console.log(domain); // 输出当前URL的域名
    // Your code here...
    domainList.forEach(value => {
        if(domain ==value){
            dispatchMethodBasedOnDomain(value);
        }
    });



})();
```