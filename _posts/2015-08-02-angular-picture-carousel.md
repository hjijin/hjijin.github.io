---
layout: post
title: "angular 实现轮播图"
date: 2015-08-02
categories: web
---

最近的项目首页用到图片轮播效果，于是用了 angular 写了两个 demo，主要用到angular 和 AngularUI 中的 [UI Bootstrap](http://angular-ui.github.io/bootstrap/)。

主要实现如下：

####用 boostrap 中的控件
在 html 中引入 `ui-bootstrap.js` 这个文件等：

{% highlight scss %}
<script src="./bower_components/angular/angular.min.js"></script>
<script src="./bower_components/angular-bootstrap/ui-bootstrap-tpls.min.js"></script>
<script src="./assets/js/app.js"></script>
<link rel="stylesheet" href="./bower_components/bootstrap/dist/css/bootstrap.min.css">
{% endhighlight %}

####轮播图代码：

{% highlight html %}
<div ng-controller="CarouselCtrl">
  <div style="height: 305px">
    <carousel interval="myInterval" no-wrap="noWrapSlides">
      <slide ng-repeat="slide in slides" active="slide.active">
        <img ng-src="{{slide.image}}" style="margin:auto;">
        <div class="carousel-caption">
          <h4>Slide {{$index}}</h4>
          <p>{{slide.text}}</p>
        </div>
      </slide>
    </carousel>
  </div>
</div>
{% endhighlight %}

####在 angular 中的操作有：
{% highlight javascript %}
angular.module('pictureCarousel', ['ui.bootstrap']).controller('CarouselCtrl', function ($scope) {
  $scope.myInterval = 5000;
  $scope.noWrapSlides = false;
  var slides = $scope.slides = [];
  $scope.addSlide = function() {
    var newWidth = 600 + slides.length + 1;
    slides.push({
      image: 'http://placekitten.com/' + newWidth + '/300',
      text: ['More','Extra','Lots of','Surplus'][slides.length % 4] + ' ' +
        ['Cats', 'Kittys', 'Felines', 'Cutes'][slides.length % 4]
    });
  };
  for (var i=0; i<4; i++) {
    $scope.addSlide();
  }
});
{% endhighlight %}

这样就能实现如下效果：

![轮换效果](/upload/2015/0802/1.jpg)

分别写了两个 DEMO，代码托管在 [github](https://github.com/tclz/angular-picture-carousel)。