# 互动游戏网页抽签版

　　互动游戏，可以随机选择图片（图片上带有游戏内容），与所选择的字无关。
## 网页效果
![东拼西凑-before](http://img.blog.csdn.net/20170118214228835?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlubGl3ZWkxOTkw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
点击前
![东拼西凑-after](http://img.blog.csdn.net/20170118214257185?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlubGl3ZWkxOTkw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
点击后<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;屏幕左下角显示游戏图片编号，游戏内容选择与所选的文字无关，带有时钟，可以设置计时任务。网页源码见[InteractiveGame](https://github.com/jinliwei/InteractiveGame)。
## 代码实现
html部分：
```html
  <div class="main">
    <h1>东拼西凑</h1>
    <div id="content">
      <div id="littleBoxes" class="littleBoxes">
        <div id="item11">
          <a class="title" href="">欢</a>
          <div class="boxcontent"><img /></div>
        </div>
        <div id="item12">
          <a class="title" href="">迎</a>
          <div class="boxcontent"><img /></div>
        </div>
        <div id="item13">
          <a class="title" href="">来</a>
          <div class="boxcontent"><img /></div>
        </div>
        <div id="item21">
          <a class="title" href="">金</a>
          <div class="boxcontent"><img /></div>
        </div>
        <div id="item22">
          <a class="title" href="">礼</a>
          <div class="boxcontent"><img /></div>
        </div>
        <div id="item23">
          <a class="title" href="">伟</a>
          <div class="boxcontent"><img /></div>
        </div>
        <div id="item31">
          <a class="title" href="">的</a>
          <div class="boxcontent"><img /></div>
        </div>
        <div id="item32">
          <a class="title" href="">网</a>
          <div class="boxcontent"><img /></div>
        </div>
        <div id="item33">
          <a class="title" href="">页</a>
          <div class="boxcontent"><img /></div>
        </div>
      </div>
    </div>
    <div id="clock" class="clock-container">
      <div style="position: absolute; top: 270px; left: 30px;">
        <p class="clock">
          <a id="hours"></a>:
          <a id="min"></a>:
          <a id="sec"></a>
        </p>
      </div>
    </div>
    <div id="topbox"></div>
    <footer>
      <h1 style="font-size: 24px; color: #fff;">出品人：<a href="mailto:jinliwei1990@outlook.com" style="color: #fff;">金礼伟</a>&nbsp;游戏编号：<a id="gameid">1</a></h1>
    </footer>
  </div>
```
js代码，可以修改格子大小：
```JavaScript
$(function() {
  var w = 200; //littleBoxes width
  var h = 200; //littleBoxes height
  $('#item11').css({ 'top': 0, 'left': 0 });
  $('#item12').css({ 'top': 0, 'left': w + 5 });
  $('#item13').css({ 'top': 0, 'left': (w + 5) * 2 });
  $('#item21').css({ 'top': h + 5, 'left': 0 });
  $('#item22').css({ 'top': h + 5, 'left': w + 5 });
  $('#item23').css({ 'top': h + 5, 'left': (w + 5) * 2 });
  $('#item31').css({ 'top': (h + 5) * 2, 'left': 0 });
  $('#item32').css({ 'top': (h + 5) * 2, 'left': w + 5 });
  $('#item33').css({ 'top': (h + 5) * 2, 'left': (w + 5) * 2 });

  /* object to save the initial positions of each box */
  var divinfo = { "initial": [] };
  /* index of the selected / clicked box */
  var current = -1;

  /* we save the index,top and left of each one of the boxes */
  $('#littleBoxes > div').each(function() {
    var $this = $(this);
    var initial = {
      'index': $this.index(),
      'top': $this.css('top'),
      'left': $this.css('left')
    };
    divinfo.initial.push(initial);
  });

  /* clcik event for the anchors inside of the boxes */
  $('#littleBoxes a').bind('click', function(e) {
    /* top block disable the click function顶层遮挡层，在动画期间遮挡点击动作 */
    $('#topbox').css({ "width": document.documentElement.clientWidth, "height": document.documentElement.clientHeight, "background-color": "#000", "opacity": "0", "z-index": "999" });
    /* timer，execute function after interval time定时器，到时间间隔后执行函数 */
    window.setTimeout(function() { $('#topbox').css({ "width": "0px", "height": "0px" }); }, 1000);

    var $this = $(this);
    var $currentBox = $this.parent();
    /* set a z-index lower than all the other boxes,
    to see the other boxes animation on the top*/
    $currentBox.css('z-index', '1');

    /* if we are clicking on a expanded box : */
    if (current == $currentBox.index()) {
      /* put it back (decrease width,height, and set the top and left like it was before).
      the previous positions are saved in the divinfo obj*/
      $currentBox.stop().animate({
        'top': divinfo.initial[$currentBox.index()].top,
        'left': divinfo.initial[$currentBox.index()].left,
        'width': w + 'px',
        'height': h + 'px'
      }, 800, 'easeOutBack').find('.boxcontent').fadeOut();

      $('#littleBoxes > div').not($currentBox).each(function() {
        var $ele = $(this);
        var elemTop = divinfo.initial[$ele.index()].top;
        var elemLeft = divinfo.initial[$ele.index()].left;
        $ele.stop().show().animate({
          'top': elemTop,
          'left': elemLeft,
          'opacity': 1
        }, 800);
      });
      current = -1;
    }
    /* if we are clicking on a small box : */
    else {
      /* randomly animate all the other boxes.
      Math.floor(Math.random()*601) - 150 gives a random number between -150 and 450.
      This range is considering the initial lefts/tops of the elements. It's not the exact right
      range, since we would have to calculate the range based on each one of the boxes. Anyway, it
      fits our needs...
      */
      $('#littleBoxes > div').not($currentBox).each(function() {
        var $ele = $(this);
        $ele.stop().animate({
          'top': (Math.floor(Math.random() * 600) - 100) + 'px',
          'left': (Math.floor(Math.random() * 900) - 450) + 'px',
          'opacity': 0
        }, 800, function() {
          $(this).hide();
        });
      });

      /* expand the clicked one. Also, fadeIn the content (boxcontent)
      if you want it to fill the space of the littleBoxes container,
      then these are the right values */
      var newwidth = w * 3 + 12;
      var newheight = h * 3 + 12;
      $currentBox.stop().animate({
        'top': '0px', //0px
        'left': '0px', //0px
        'width': newwidth + 'px',
        'height': newheight + 'px'
      }, 800, 'easeOutBack', function() {
        current = $currentBox.index();
        $(this).find('.boxcontent').fadeIn();
      });

      /* random a number, change the image src and display the number */
      var ch = 1 + Math.floor(Math.random() * 9);
      $currentBox.find('img').attr('src', './img/' + ch + '.jpg'); //find the img in clicked currentBox and change it's src
      $('#gameid').text(ch); //display the random number
    }
    e.preventDefault();
  });
});
```
