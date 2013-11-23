---
title: 完美解决markdown+mathjax冲突
layout: post
guid: urn:uuid:b87da13a-a4dd-402f-b06a-cef7eeee2d82
tags:
  - 写作
---


### 高效建站，玩转github+markdown+mathjax

今天效率超高，Github自带Jekyll后台解析markdown，等着就行了，但是和mathjax各种冲突，终于找到解决方案！

我用的模板是别人Jekyll Boostrap的，暂时没mac没linux没钱怎么办？抄啊！

github上面建立一个同名地址，yourname.github.io格式，开抄，还不懂的参考[github.com/catalystfrank](http://github.com/catalystfrank)

抄完记得注册一个disqus授权博客评论哦。.name域名设置MyDNS 2个小时开启，开A级Url跳转，当然github也要加CNAME。两边联通测试，博客就能用啦。

### \_config.yml 需要改：

> markdown: kramdown

不改就坑爹，rdiscount比他老一点，其他更老，仔细看，欢迎纠正。

### post.html 需要改：

    <script type="text/x-mathjax-config">
    MathJax.Hub.Config({
    tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    processEscapes: true,
    skipTags: ['script', 'noscript', 'style', 'textarea', 'pre', 'code']
    },
 	  TeX: {
    equationNumbers: {
    autoNumber: ["AMS"],
    useLabelIds: true
    }
    },
    "HTML-CSS": {
    linebreaks: {
    automatic: true
    },
    scale: 85
    },
    SVG: {
    linebreaks: {
    automatic: true
    }
    }
    });
    MathJax.Hub.Queue(function() {
    var all = MathJax.Hub.getAllJax(), i;
    for(i = 0; i < all.length; i += 1) {
    all[i].SourceElement().parentNode.className += ' has-jax';
    }
    });
    </script>
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

### style.css需要改：

    code.has-jax {
    font: inherit; 
    font-size: 100%; 
    background: inherit; 
    border: inherit;}


### 其他注意事项

\*号和成对的\_在markdown里面会转义的！一定要加反下划线\\。Over
