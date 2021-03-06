# 前端canvas整理汇总

## canvas标签的常用样式与属性

要想在网页中引用canvas可以直接在HTML中添加canvas标签即可，此标签默认有如下样式：

- width:300px
- height:150px
- display:inline

通常为了方便使用,会给canvas添加一个`display:block`样式

canvas的width样式与height样式确定了元素的宽和高,他的width属性和height属性确定了画布的宽和高如果画布大小和元素大小不一样,按照如下规则进行处理：

- 画布尺寸大于元素尺寸 - 此时,元素会被画布撑开
- 画布尺寸小于元素尺寸 - 绘制是在画布尺寸内绘制的,但绘制完成后,会缩放到元素尺寸大小

所以通常canvas元素只添加宽高属性,而不添加样式

canvas为双标签,标签中间的内容在浏览器不支持canvas时会显示出来,在支持canvas的浏览器会忽略内部的内容

## 绘制图形

### 获取元素可绘制区域

```html
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <canvas width="500" height="500"></canvas>
        <script>
        	let canvas = document.getElementsByTagName('canvas')[0]; // 获得元素
            let canCon = canvas.getContext('2d'); // 获得canvas元素渲染的上下文(画布)
        </script>
    </body>
</html>
```

*后续的代码中,直接JS的部分,HTML中默认有与上面代码相同的元素,且已获取完上下文(canCon)*

### 绘制矩形

以下内容中参数x,y为矩形左上角的坐标,width和height为矩形的宽与高

`window.CanvasRenderingContext2D.prototype.fillRect(x, y, width, height)`绘制实心矩形

`window.CanvasRenderingContext2D.prototype.strokeRect(x, y, width, height)`绘制空心矩形

`window.CanvasRenderingContext2D.prototype.clearRect(x, y, width, height)`清除矩形区域

### 绘制文字

`window.CanvasRenderingContext2D.prototype.fillText(text,x,y,maxWidth)`在坐标为x,y的位置绘制对应文字,最后一个参数为绘制的最大宽度,最后一个参数可以省略

`window.CanvasRenderingContext2D.prototype.strokeText(text,x,y,maxWidth)`在坐标为x,y的位置绘制文字边框(空心文字),最后一个参数为绘制的最大宽度,最后一个参数可以省略

**绘制文字中的x,y坐标为第一个字符的基线的最左侧的坐标**

### 绘制图片

`window.CanvasRenderingContext2D.prototype.drawImage(image[, sx, sy, sWidth, sHight], dx, dy[, dWidth][, dHeight])`绘制一个图片，参数如下：

- image为图片对象，可以是HTMLImageElement(通过new Image()创建的对象)，也可以是另一个canvas元素
- sx与sy为从图片上对应位置开始截取图片
- sWidth与sHight为截取对应宽高的图片
- dx与dy为从画布的对应位置开始绘制图片
- dWidth与dHeight为绘制图库的宽和高

### 路径

绘制矩形的方法可以直接将矩形绘制出来，但有的图形无法直接绘制，只能定义出来，这样就需要在定义后通过绘制定义图形方法来将其绘制出来

`window.CanvasRenderingContext2D.prototype.beginPath()`新建一条路径,生成之后,图形绘制命令被指向到路径上生成路径

`window.CanvasRenderingContext2D.prototype.closePath()`闭合路径之后图形绘制命令又重新指向到上下文中

`window.CanvasRenderingContext2D.prototype.clip()`执行此函数后，只有之前绘制的路径中的区域才能被绘制内容

`window.CanvasRenderingContext2D.prototype.stroke()`通过线条来绘制图形轮廓

`window.CanvasRenderingContext2D.prototype.fill()`或`window.CanvasRenderingContext2D.prototype.fill(fillRule)`或`window.CanvasRenderingContext2D.prototype.fill(path, fillRule)`通过填充路径的内容区域生成实心的图形(会自动闭合图形),path为创建的Path2D对象，fillRule可选如下值

- `nonzero` - 全部填充,默认值
- `evenodd` - 填充基数区域,偶数区域不填充(从内向外)

**定义矩形**

`window.CanvasRenderingContext2D.prototype.rect(x,y,width,height)`定义矩形，x,y为矩形左上角的坐标，widht与height分别为矩形的宽与高

**定义圆弧**

`window.CanvasRenderingContext2D.prototype.arc(x, y, radius, startAngle, endAngle, anticlockwise)`参数中x,y表示圆心坐标,radius表示圆弧半径,startAngle为起始角度,endAngle为终止角度,anticlockwise为绘制方向,true表示逆时针绘制,false表示顺时针绘制,默认为false

绘制圆弧时,角度应使用**弧度值**,0为右侧,按顺时针方向计算角度大小

**注意:计算角度大小的方向固定为顺时针,而绘制的方向由最后一个参数决定**

```javascript
canCon.arc(250,250,80,0,Math.PI / 2,true); // 定义圆弧
canCon.stroke(); // 绘制图案
```

`window.CanvasRenderingContext2D.prototype.moveTo(x,y)`将笔触移动到指定的x,y坐标上

`window.CanvasRenderingContext2D.prototype.lineTo(x,y)`定义一条从当前位置到x,y坐标的直线

```javascript
canCon.beginPath(); // 开始绘制路径
canCon.moveTo(0,50); // 画笔移动到
canCon.lineTo(50,300); // 定义直线
canCon.lineTo(100,50); // 定义直线
canCon.lineTo(150,300); // 定义直线
canCon.lineTo(200,50); // 定义直线
canCon.closePath(); // 闭合图形
canCon.fill(); // 绘制图形
```

`window.CanvasRenderingContext2D.prototype.arcTo(x1,y1,x2,y2,r)`定义圆角,(用于路径中需要有起始位置坐标)参数中x1,y1为两条边的延长线的交点坐标,x2,y2为终点坐标,r为圆角半径

此命令会定义一个圆角,并将圆角起点与起始位置相连接,与结束位置不会连接

```javascript
canCon.beginPath(); // 开始绘制路径
canCon.moveTo(100,200); // 确定起始位置
canCon.arcTo(250,200,250,210,50); // 定义圆角
canCon.stroke(); // 绘制图形
```

`window.CanvasRenderingContext2D.prototype.quadraticCurveTo(cp1x,cp1y,x,y)`定义具有一个控制点的贝塞尔曲线,前两个参数为控制点坐标,后两个参数为结束点坐标

`window.CanvasRenderingContext2D.prototype.bezierCurveTo(cp1x,cp1y,cp2x,cp2y,x,y)`定义具有两个控制点的贝塞尔曲线,前两个参数为控制点一的坐标,中间两个为控制点二的坐标,最后两个为结束点的坐标

## 坐标系变换

。。。

`window.CanvasRenderingContext2D.prototype.save()`保存当前状态

`window.CanvasRenderingContext2D.prototype.restore()`恢复到之前保存的状态

## 相关参数

### 调整绘制的颜色

`window.CanvasRenderingContext2D.prototype.fillstyle = colorValue`设置实心图形的颜色

`window.CanvasRenderingContext2D.prototype.strokestyle = colorValue`设置空心图形的颜色

### 透明度

`window.CanvasRenderingContext2D.prototype.globalAlpha`透明度,从此代码向后的按照此透明度绘制,对之前的代码无影响，但更常使用的是rgba颜色来设置透明度

### 线型

`window.CanvasRenderingContext2D.prototype.lineWidth = value`设置线条宽度(单位为px,但设置时不写单位),**推荐使用偶数宽度**

`lwindow.CanvasRenderingContext2D.prototype.ineCap = type`设置线条末端样式,有如下值可选:

- butt - 不超出设置长度(默认)
- round - 超出部分用半圆形
- square - 超出部分用矩形

*后两个值会造成不精确*

`window.CanvasRenderingContext2D.prototype.lineJoin = type`设定线条与线条接合处样式,有如下值可选:

- round - 接合处为圆角
- bevel - 接合处垂直于法线效果
- miter -  接合处为尖锐的效果(默认)

`window.CanvasRenderingContext2D.prototype.miterLimit = value`限制当两条线相交时交界处最大长度(交界处长度值线条交界处内角顶掉到外角定点的长度

`window.CanvasRenderingContext2D.prototype.getLineDash()`返回一个包含当前虚线样式,查给你读为非负偶数的数组

`window.CanvasRenderingContext2D.prototype.setLineDash(segments)`设置当前虚线样式,segments为一个数组,第一个元素为线段的长度,第二个为空隙的长度

`window.CanvasRenderingContext2D.prototype.lineDashOffset = value`设置虚线样式的起始偏移量

### 渐变

**镜像渐变**

`window.CanvasRenderingContext2D.prototype.createLinearGradient(x1,y1,x2,y2)`返回一个`CanvasGradient`对象，x1,y1为渐变方向线起点坐标,x2,y2为渐变方向线终点坐标

*渐变的颜色设置请参考创建渐变的颜色部分*

线性渐变颜色举例如下:

```javascript
let lin = canCon.createLinearGradient(0,0,0,150);
lin.addColorStop(0, `red`);
lin.addColorStop(1, `blue`);
canCon.fillStyle = lin;
canCon.fillRect(10,10,260,160);
```

**径向渐变**

`window.CanvasRenderingContext2D.prototype.createRadialGradient(x1,y1,r1,x2,y2,r2)`返回一个`CanvasGradient`对象，x1,y1,r1为渐变开始的圆心坐标与半径，x2,y2,r2为渐变结束的圆心坐标与半径

*渐变的颜色设置请参考创建渐变的颜色部分*

**创建渐变的颜色**

创建的线性渐变颜色可以使用`window.CanvasGradient.prototype.addColorStop(x,y)`方法来规定渐变颜色,x为位置(0-1之间的一个值),y为颜色

### 图片背景

`window.CanvasRenderingContext2D.prototype.crearePattern(image,type)`返回一个具有背景图片的样式。image为图片对象，type有如下几个值：

- repeat
- repeat-x
- repeat-y
- no-repeat

举例如下:

```javascript
let img = new Image();
img.src = `test.png`;
img.addEventListener(`load`,() => {
    let ptrn = canCon.createPattern(img,`repeat`);
    canCon.fillStyle = ptrn;
    canCon.fillRect(100,100,200,200);
});
```

### 阴影

从此代码向后的按照此阴影绘制

`window.CanvasRenderingContext2D.prototype.shadowOffsetX = value`与`window.CanvasRenderingContext2D.prototype.shadowOffsetY = value`阴影在X或Y轴上的延伸距离(正数为正方向,负数为负方向,默认为0)

`window.CanvasRenderingContext2D.prototype.shadowBlur = value`阴影的模糊程度,默认为0

`window.CanvasRenderingContext2D.prototype.shadowColor = color`阴影颜色效果,默认全透明黑色

### 文字相关

`window.CanvasRenderingContext2D.prototype.font = value`绘制文本的字体,与CSS font语法相同,默认为10px san-serif,从此代码向后的照此绘制

`window.CanvasRenderingContext2D.prototype.textAlign = value`设置文字的对齐方式,但不常用

`window.CanvasRenderingContext2D.prototype.textBaseline = value`设置基线对齐选项,但不常用

`window.CanvasRenderingContext2D.prototype.direction = value`设置文本方向,有如下值可用:

- ltr
- rtl
- inherit(默认值)

`window.CanvasRenderingContext2D.prototype.measureText(x)`返回x会占用多少宽度

## Path2D对象

Path2D相当于把部分绘图记录下来,方便后续直接调用

`new window.Path2D()`创建一个空的Path2D对象

`new window.Path2D(path)`复制一个Path2D对象

举例如下:

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <style>
        canvas {
            display: block;
            background-color: #ccc;
        }
    </style>
</head>
<body>
    <canvas height="500" width="500"></canvas>
    <button>红色</button>
    <button>蓝色</button>
    <button>绿色</button>
    <script>
        'use strict'
        let canCon = document.getElementsByTagName(`canvas`)[0].getContext(`2d`);
        let path = new Path2D();
        let aButton = document.getElementsByTagName(`button`);
        path.arc(250, 250, 100, 0, 2 * Math.PI);
        [...aButton].forEach((v, i) => {
            console.dir(v);
            v.addEventListener(`click`, () => {
                switch (i) {
                    case 0:
                        canCon.fillStyle = `red`;
                        break;
                    case 1:
                        canCon.fillStyle = `blue`;
                        break;
                    case 2:
                        canCon.fillStyle = `green`;
                }
                canCon.clearRect(0, 0, 500, 500);
                canCon.fill(path);
            });
        });
    </script>
</body>
</html>
```

