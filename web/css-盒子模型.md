# 盒子模型

盒子模型可以理解为把前端CSS理解为由多个盒子拼接而成的。盒子模型中包含内容区、内边距、边框、外边距。

举个生活中常见的例子：我买了个笔记本电脑，这个笔记本电脑包装没被拆开以前肯定是在电脑箱子里的，电脑箱子里面有笔记本，但是为了防止运输过程中造成的磕碰所以笔记本中间肯定还会放一层泡沫海绵。

内容区：即放东西的地方，可以理解为上面例子中的笔记本。

内边距：内容区到盒子边框线的部分称为内边距，可以理解为上面例子中泡沫海绵部分。

边框线：即盒子从最外层到内边距的部分。

外边距：盒子以外的所有地方都可以看做外边距。



## 边框border

**边框的设置：**

border(边框宽度 边框颜色 边框样式)：统一设置边框。

border-xxx(边框宽度 边框颜色 边框样式)：统一设置xxx的边框。xxx可选 top上边框、 bottom下边框、left左边框、right右边框。

border-color()：设置边框颜色。可选1-4个参数，1个参数为所有边框采用一个颜色；2个参数为上下、左右两组划分；3个参数为上、左右、下划分；4个参数为上、下、左、右划分。

border-style()：同上。

border-width()：同上。

## 内边距padding

设置内容区和边框线的距离，内边距不是内容区。

padding:参数同上。

padding-xxx:参数同上。

## 外边距margin

设置盒子和其他盒子之间的距离。margin参数可以为负值，表示往当前设置的方向移动，正值为反方向移动。

margin:参数同上。

margin-xxx:参数同上。