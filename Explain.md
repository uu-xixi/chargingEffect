 HTML

```
      <div class="chargeBody" style="height: 100%;">
        <canvas id="waterCanvas" width="200" height="300"
          ><p>你的浏览器不支持Canvas</p></canvas
        >
        <canvas id="wave"></canvas>
```

// 创建水珠函数

```

    var waterObj = document.getElementById("waterCanvas");
    var ctx = waterObj.getContext("2d");
    var Color = [
      "#99cccc",
      "#FFCC99",
      "#FFCCCC",
      "#0099CC",
      "#FF9999",
      "#FFFF66",
      "#FF6666",
      "#FFFF00",
      "#99CCFF"
    ];

    waterObj.width = 200;
    waterObj.height = 100;
    function Random(min, max) {
      return Math.random() * (max - min) + min;
    }
    function Water() {}
    Water.prototype = {
      init: function() {
        this.x = Random(0, waterObj.width); //新创建的小球的 x 坐标  范围：0-w
        this.y = Random(0, waterObj.height); //新创建的小球的 y 坐标  范围：0-h
        this.r = Random(2, 6); //新创建的小球的 半径
        this.l = "1"; //圆的线框大小
        this.color = Color[Math.floor(Random(0, 6))]; //新创建的小球的 颜色   floor向上取整
        this.vX = Random(-1, 1); //该小球在x轴方向的速度
        this.vY = Random(-1, 1); //该小球在y轴方向的速度
      },
      draw: function() {
        ctx.beginPath(); //开始路径
        // cx.clearRect(0,0,waterObj.width,waterObj.height);
        ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI); //绘制图形
        ctx.strokeStyle = this.color;
        ctx.fillStyle = this.color; //填充颜色
        ctx.fill(); //应用于图形上
        ctx.stroke();
      },
      move: function() {
        this.x += this.vX;
        this.y += this.vY;
        if (this.x - this.r < 0 || this.x + this.r > waterObj.width) {
          //挡球碰到屏幕x轴运动边缘就反弹
          this.vX = -this.vX;
        }
        if (this.y - this.r < 0 || this.y + this.r > waterObj.height) {
          //挡球碰到屏幕y轴运动边缘就反弹
          this.vY = -this.vY;
        }
        this.draw(); //每次运动之后就要进入画的的下一帧
      }
    };

    var arr = new Array();
    function createWater(num) {
      for (var i = 0; i < num; i++) {
        var bubble = new Water(); //创建一个新的小球
        arr.push(bubble); //将每次创建的小球丢进数组里
        bubble.init();
        bubble.draw();
      }
    }
    createWater(7); //创建6个小球

    setInterval(function() {
      //实现画中帧的播放
      ctx.clearRect(0, 0, waterObj.width, waterObj.height); //每次播放新的下一帧之前要对上一帧进行清除工作
      for (var item of arr) {
        item.move(); //移动
      }
    }, 5000 / 100); //1000/100代表是1000ms 100张   ->   1s 100张
```



//创建波浪函数

```
var canvas = document.getElementById("wave");
    var ctx1 = canvas.getContext("2d");

    var nowRange = 60; //控制水位的百分比

    //画布属性
    var mW = (canvas.width = 200);
    var mH = (canvas.height = 300);
    var lineWidth = 1;

    var r = mH / 2; //中心

    //Sin 曲线属性
    var sX = 0;
    var axisLength = mW; //轴长
    var waveWidth = 0.01; //波浪宽度,数越小越宽
    var waveHeight = 20; //波浪高度,数越大越高
    var speed = 0.04; //波浪速度，数越大速度越快
    var xOffset = 0; //波浪x偏移量

    ctx1.lineWidth = lineWidth;

    //画矩形函数
    var IsdrawCircled = false;
    var drawCircle = function() {
      ctx1.strokeStyle = "#eb4d4c";
      ctx1.stroke();
      IsdrawCircled = true;
    };

    //画sin 曲线函数
    var drawSin = function(xOffset, color, waveHeight) {
      ctx1.save();

      var points = []; //用于存放绘制Sin曲线的点

      ctx1.beginPath();
      //在整个轴长上取点
      for (var x = sX; x < sX + axisLength; x += 20 / axisLength) {
        //此处坐标(x,y)的取点，依靠公式 “振幅高*sin(x*振幅宽 + 振幅偏移量)”
        var y = Math.sin((-sX - x) * waveWidth + xOffset) * 0.8 + 0.1;

        var dY = mH * (1 - nowRange / 100);

        points.push([x, dY + y * waveHeight]);
        ctx1.lineTo(x, dY + y * waveHeight);
      }

      //封闭路径
      ctx1.lineTo(axisLength, mH);
      ctx1.lineTo(sX, mH);
      ctx1.lineTo(points[0][0], points[0][1]);
      ctx1.fillStyle = color;
      ctx1.fill();

      ctx1.restore();
    };

    var render = function() {
      ctx1.clearRect(0, 0, mW, mH);

      if (IsdrawCircled == false) {
        drawCircle();
      }

      drawSin(xOffset + Math.PI * 0.7, "rgba(235, 77, 76, 0.5)", 18);
      drawSin(xOffset, "#eb4d4c", 18);
      drawText();

      xOffset += speed;
      requestAnimationFrame(render);
    };
    // 写百分比文本函数
    var drawText = function() {
      ctx1.save();

      var size = 36;
      ctx1.font = size + "px Microsoft Yahei";
      ctx1.textAlign = "center";
      ctx1.fillStyle = "rgba(255, 255, 255)";
      ctx1.fillText(~~nowRange + "%", r / 1.5, (mH / 4) * 3 + size / 2);

      ctx1.restore();
    };

    render();
```

