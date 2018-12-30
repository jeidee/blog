---
author: jeidee
categories:
- html
date: '2015-03-15'
guid: https://erlnote.wordpress.com/?p=391
id: 391
permalink: /2015/03/15/ionic%ec%97%90%ec%84%9c-css%eb%a5%bc-%ec%82%ac%ec%9a%a9%ed%95%9c-flip-card-%ea%b5%ac%ed%98%84%ed%95%98%ea%b8%b0/
tags:
- css
- flip card
- ionic
title: ionic에서 css를 사용한 flip card 구현하기
url: /2015/03/15/ionicec9790ec849c-csseba5bc-ec82acec9aa9ed959c-flip-card-eab5aced9884ed9598eab8b0
---

다음과 같이 간략하게 과정을 정리해 본다.

1) html

```html
  
toggle
  
<section class="container">
      
<div id="card">
          
<figure class="front">1</figure>
          
<figure class="back">2</figure>
      
</div>
  
</section>
  
```

2) css

```css
  
.container {
      
width: 200px;
      
height: 260px;
      
position: relative;
      
perspective: 800px;
  
}

#card {
      
width: 100%;
      
height: 100%;
      
position: absolute;
      
transform-style: preserve-3d;
      
transition: transform 1s;
  
}

#card figure {
      
display: block;
      
position: absolute;
      
width: 100%;
      
height: 100%;
      
backface-visibility: hidden;
  
}

#card .front {
      
background: red;
  
}
  
#card .back {
      
background: blue;
      
transform: rotateY( 180deg );
  
}

#card.flipped {
      
transform: rotateY( 180deg );
  
}
  
```

## 참고

  * [Intro to CSS 3D transform &#8211; Card Flip](http://desandro.github.io/3dtransforms/docs/card-flip.html)
  * [Create css flipping animation](http://davidwalsh.name/css-flip)