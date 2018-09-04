1. CSS选择器的特殊性分为4个等级：a,b,c,d

   1. 如果是样式是行内样式，那么a=1
   2. b等于id选择器的总数
   3. c等于类、伪类和属性选择器的数量
   4. d等于类型选择器和伪元素选择器的数量

   **PS：伪类选择器和伪元素选择器的区别**

    	1. 伪类选择器是通过增加元素效果的选择器，不改变dom结构
   		2. 伪元素的效果是需要通过添加一个实际的元素才能达到的

2. 外边距叠加

   1. 当一个元素出现在另一个元素上面的时候，第一个元素的底外边距与第二元素的顶外边距会发生叠加
   2. 当一个元素包含在另一个元素中间时（假设没有内边距或边框将外边距分隔开），它们的顶/底外边距也会发生叠加
   3. 一个外边距甚至可以与本身发生叠加。假设有一个空元素，它有外边距，但是没有边框或内边距。在这种情况下，顶外边距与底外边距碰到一起发生自身叠加

3. 浮动与清理

   ```html
   <style>
       .new{
           background-color:gray;
           border:solid 1px black;
       }
       .new img{
           float:left;
       }
       .new p{
           float:right;
       }
       .clear{
           clear:both;
       }
   </style>

   <div class="news">
       <img src="./images/123.jpg" />
       <p>
           Some Test
       </p>
       <!--方法1:  <br class="clear" />-->
   </div>
   ```

   浮动会让元素脱离文档流，所以包围img标签和p标签的div不占空间。

   1. 可以在最后加一个空元素并且清理它，设置清理。

   2. 父元素设置overflow:hidden

      overflow属性有一个副效应，即自动清除包含的任何浮动元素

   3. 结合使用:after伪类和内容声明在指定的现有内容的末尾添加新的内容。添加内容后并把height设置为0，visibility设置为hidden。因为被清理的元素在他们的顶外边距增加了空间，所以生成的内容需要将它的display值设置为block。

4. 为了尽可能提高页面的可访问性，在定义鼠标悬停状态:hover的时候最好加上:focus伪类，在通过键盘移动到链接上的时候，让链接显示的样式与鼠标相同。

5. a标签的伪类选择器使用顺序

   ```css
   a:link,a:visited{
   	text-decoration: none;
   }
   a:hover,a:focus,a:active{
   	text-decoration: underline;
   }
   ```

   如果顺序颠倒的话，是鼠标的悬停和激活样式就不起作用

   因为a标签的同一个状态可能属于多个伪类

   访问顺序：

   a:link , a:visited , a:hover , a:focus , a:active

   详细 : https://www.cnblogs.com/xiayi/p/5350423.html

6. 不要使用a标签发送post请求

   使用锚链接而不是表单元素作为删除按钮，Google加速程序会访问这些链接以便缓存，这样以便缓存它们，这样就会无意中删除大量数据。搜索引擎的spider可以造成相同的效果，递归删除大量数据。

7. 远距离翻转

   远距离翻转是一种鼠标悬停事件，他在页面的其他地方触发显示方式的改变。实现的方法是：在锚链接内嵌套一个或多个元素；然后，使用绝对定位对嵌套的元素分别定位。尽管显示在不同的地方，但是他们都包含在一个父锚中，所以可以对同一个鼠标悬停事件作出反应。因此，当鼠标悬停在一个元素上时，可以影响另一个元素样式。

   ```html
   <style>
       .fa{
           display:block;
           position:relative;
       }
       .fa span{
           position:absolute;
           top:-1000px;	/*设置负大值移出视野*/
           left:-1000px;
       }
       .fa:hover span{		/*远距离悬停*/
           top:100px;		/*使其移动到目标位置*/
           left:100px;		/*远距离悬停注意选择器写法*/
       }
   </style>
   <a class="fa">
   	<span></span>
       <p>
           嘻嘻
       </p>
   </a>
   ```
   8. lable标签的关联

      ```html
      <!--方式1-->
      <label>email <input name="email" type="text" z/></label>
      <!--方式2-->
      <lable for="email">email</lable>
      <input name="email" id="email" type="text" />
      ```

   9. 在一些可以交互的地方不要忘了把光标样式改成pointer

      ```css
      label{
          display:block;
          cursor:pointer;
      }
      ```

   10. 不需要为要应用样式的每个元素创建新的类名，而应使用层叠找到元素，这样我们可以多次使用primary和secondary类XDDD

   11. 例如假设希望创建一种新闻稿样式，在左边是窄的图像列，右边是较大的文本列。图像的宽度需要大约包含它的框的四分之一，文本占据余下的空间。为此，只需要将图像的宽度设置为25%，然后将max-width设置为图像的尺寸。

   12. 外边距叠加的大多数问题可以通过添加一点内边距或与元素背景颜色相同的小边框来修复。

   13. 拥有布局

       IE使用布局概念来控制元素的尺寸和定位。那些"拥有布局"的元素负责本身及其子元素的尺寸设置和定位。如果一个元素"没有拥有布局"，那么它的尺寸和位置由最近的拥有布局的祖先元素控制。

       默认拥有布局的元素：

       1. body
       2. html(标准模式中)
       3. table
       4. tr，td
       5. img
       6. hr
       7. input，select，textarea，button
       8. iframe，enbed，object，applet
       9. marquee

       设置一下CSS属性会自动地使元素拥有布局

       1. float：left或right
       2. display：inline-block
       3. width：任何值
       4. height：任何值
       5. zoom：任何值
       6. writing-mode:td-rl
       7. overflow:hidden，scroll或者auto（IE7)
       8. min-width：任何值
       9. max-width：除none之外的任何值

   14. 如果CSS文件很长很复杂，或者需要使用的hack比较多，那么最好将它们放在他们自己的样式表中。这不但使代码容易阅读，而且如果hack在新浏览器中造成问题，可以准确的知道他们的位置。于此相似，如果决定取消对某种浏览器的支持，那么只需要删除适当的CSS文件，就可以删除相关联的hack。

   15. 双外边距浮动bug

       将display属性设置为inline就行

   16. **IE 6**的"藏猫猫"bug

       一个浮动元素后面跟着一些非浮动元素，然后是一个清理元素，所有这些元素都包含在一个设置了背景颜色或图像的父元素中。如果清理元素碰到了浮动元素，那么中间的非浮动元素就会看起来消失了一样，隐藏在父元素的背景颜色或图像背后，只有在刷新页面的时候才重新出现。

       解决方法：~~去掉父元素的背景颜色(大雾)~~ 避免清理元素与浮动元素接触。如果容器元素应用了特定的尺寸，那么这个bug师傅就不会出现了。如果给容器指定行高，这个bug也不会出现。最后，将浮动元素和容器元素的position属性设置为relative也会减轻这个问题。

   17. reset.css

       将CSS初始化写到一个reset.css文件中~~反正有打包工具~~

   18. @font-face字体嵌入

   19. 样式表的组织和约定

       大概意思就是CSS初始化写到一个.css文件里面，IE7写一个文件，IE6写一个文件，这样易于管理

       (但是这里就有个问题了，多个文件在请求的时候比较窒息，那么打包工具就在这里发挥作用？)
