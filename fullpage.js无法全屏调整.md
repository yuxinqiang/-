因为之前项目中需要实现滚屏的效果，所以采用fullpage.js来实现。在使用过程中遇到一些坑点所以特此记录一下，防备以后用到。

github地址：

[fullpage.js](https://github.com/alvarotrigo/fullPage.js)

官网地址：

[fullpage](https://alvarotrigo.com/fullPage/#)

关于如何使用fullpage，github和官网上已经说的很详细了再次就不多说了。

整体来说fullpage是个很牛的插件，兼容pc和移动端。

说说我遇到的问题吧，设计中存在内容不满一屏的情况，所以找了很多帖子说用js控制重新计算最后一屏的方式，但是不行。

后来找到一个解决方法——改源码

原文：[Fullpage.js底部高度自定义fp-auto-height样式无效的解决方案](https://blog.csdn.net/sloafer/article/details/79135916)

HTML部分:

```html
<div class="section section5">
  <!--这里是倒数第2屏-->		
</div>
<!--最后一屏开始-->
<div class="section" id="section-footer">
  <div id="footer-text">
    <!--这里写底部版权信息等文字-->	
  </div>
</div>
<!--最后一屏结束-->
```

CSS部分：

```css
#section-footer{
	background:#212020;
}
#section-footer #footer-text{
	height: 150px!important;/*设置尾屏高度*/
}
```

修改源码fullpage.js,找到function performMovement(v)这个方法：

```js
if (options.css3 && options.autoScrolling && !options.scrollBar) {
 
				//var translate3d = 'translate3d(0px, -' + v.dtop + 'px, 0px)';
				if (v.anchorLink == 'footer')//判断当滚动到尾屏如何做
				{
					footer_a = $('#section-footer').height();
					footer_h = $('#footer-text').height();
					var translate3d = 'translate3d(0px, -' + (v.dtop - footer_a + footer_h) + 'px, 0px)';
				}
				else
				{
					var translate3d = 'translate3d(0px, -' + v.dtop + 'px, 0px)';
				}
				transformContainer(translate3d, true);
 
				setTimeout(function () {
					afterSectionLoads(v);
				}, options.scrollingSpeed);
			}
 
			// using jQuery animate
```

大概意思就是if一下当滚动到底部时再重新计算一下要滚动的高度．

**因为判断的是anchors．所以要在fullpage的参数配置里面最后加上footer，否则会判断不到．**

根据上面的帖子操作虽然解决了我的问题，但是我感觉不应该是这样，这个插件的作者不可能会想不到存在不满一屏的情况，所以我又仔细查看了一次文档，果然不负所望

官网提供的example中有这种情况的实例

[Auto-height sections](https://alvarotrigo.com/fullPage/examples/autoHeight.html)

对比Github的源码确实找到了相关的属性

[fp-auto-height](https://github.com/alvarotrigo/fullPage.js/blob/master/src/fullpage.js)

只需要在相应的section上添加fp-auto-height的class即可，这样section的高度就会根据内容自动适应，不需要进行额外的调整

**注意：之前我找过的帖子中也有提到的fp-auto-height，但是无效，是因为版本的问题，目前来说3.0版本是有这个属性的，这也是GIthub上现在最新的版本**

