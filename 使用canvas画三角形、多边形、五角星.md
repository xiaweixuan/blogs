## 使用canvas画三角形、多边形、五角星

```javascript
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title></title>
    <style>
        canvas {
            border: 1px solid red;
        }
    </style>
</head>

<body>
    <canvas id="canvas">你的浏览器不支持canvas</canvas>

</body>
<script>
    (function () {
        CanvasRenderingContext2D.prototype.triangle = function (x1, y1, x2, y2, x3, y3) {
            this.moveTo(x1, y1);
            this.lineTo(x2, y2);
            this.lineTo(x3, y3);
            this.closePath();
            this.stroke();
            // console.log(this);
        };
        CanvasRenderingContext2D.prototype.polygon = function (x, y, r, n) {
            //补全代码实现多边形的绘制
            //其中x为中心点x坐标，y为中心点y坐标，r为半径，n为多边形边的数量

            var XCoordinate = [];//存储所有点的x坐标
            var YCoordinate = [];//存储所有点的y坐标
            var xc1,xc2;

            this.beginPath();
            for (let i = 0; i < n; i++) {
                xc = x + Math.cos((2 * i * Math.PI) / n) * r;
                // console.log(xc);
                XCoordinate.push(xc);
                yc = y + Math.sin((2 * i * Math.PI) / n) * r;
                // console.log(yc);
                YCoordinate.push(yc);
            }
            for (let i = 0; i < n; i++) {
                this.lineTo(XCoordinate[i], YCoordinate[i]);
            }
            context.strokeStyle = 'black';
            this.stroke();
            this.closePath();
            this.stroke();//封口


        };
        CanvasRenderingContext2D.prototype.star = function (x, y, r, R) {
            //补全代码实现五角星的绘制,要求填充颜色为黄色，外描边为红色
            //其中x为中心点x坐标，y为中心点y坐标，r为五角星内径，R为五角星外径
            var XCoordinate = [];//存储所有点的x坐标
            var YCoordinate = [];//存储所有点的y坐标
            var XCoordinate0 = [];//存储所有点的x坐标
            var YCoordinate0 = [];//存储所有点的y坐标
            var n = 5;//记录单个图形点数
            var xc1,xc2,yc1,yc2;


            this.beginPath();
            for (let i = 0; i < n; i++) {
                xc1 = x + Math.cos((2 * i * Math.PI) / n) * R;
                xc2 = x + Math.cos((2 * i * Math.PI) / n +2*Math.PI/5/2) * r;
                XCoordinate.push(xc1);
                XCoordinate0.push(xc2);
                yc1 = y + Math.sin((2 * i * Math.PI) / n) * R;
                yc2 = y + Math.sin((2 * i * Math.PI) / n +2*Math.PI/5/2) * r;
                YCoordinate.push(yc1);
                YCoordinate0.push(yc2);
                // console.log(xc1,yc1,xc2,yc2);
            }
            for (let i = 0; i < n; i++) {
                this.lineTo(XCoordinate[i], YCoordinate[i]);
                this.lineTo(XCoordinate0[i], YCoordinate0[i]);
            }
            context.strokeStyle = 'black';
            this.stroke();
            this.closePath();
            this.stroke();


        };
    })();

    var canvas = document.getElementById('canvas');
    var context = canvas.getContext('2d');

    context.polygon(50, 50, 25, 7);   
    context.star(95, 85, 20, 30);

</script>
```

复制请改变量~