### colorful clock
#### 网页使用Markdown Preview生成
<canvas id='c' width='500' height='500'></canvas>
<script>
(function(){

var center = {x:250, y:250};
var fps = 60;
var msColor = {r:12, g:12, b:12, a1:0.0, a2:0.6};
var sColor = {r:221, g:217, b:124, a1:0.1, a2:0.4};
var mColor = {r:117, g:195, b:93, a1:0.1, a2:0.6};
var hColor = {r:81, g:179, b:245, a1:0.2, a2:0.5};

// 角度转弧度
function d2r(degree) {
  return Math.PI * 2 * degree / 360;
}

function rgba(r, g, b, a) {
	return 'rgba(' + r + ',' + g + ',' + b + ',' + a + ')';
}

function randomColor() {
	return rgba(
		Math.floor(Math.random()*100 + 100),
		Math.floor(Math.random()*140 + 100),
		Math.floor(Math.random()*100 + 100),
		0.8);
}

// 绘制扇形
// cx, cy  扇形圆心
// radius1 起始半径
// radius2 终止半径
// alpha 起始角度
// delta 跨度（正为顺时针，负为逆时针）
// color 颜色
function sector(ctx, cx, cy, radius1, radius2, alpha, delta, color) {
	var beta = alpha + delta + 0.00099;		// 0.00099为误差校正
	var cosAlpha = Math.cos(alpha);
	var sinAlpha = Math.sin(alpha);
	var p1x = cx + radius1 * cosAlpha;
	var p1y = cy + radius1 * sinAlpha;
	var p2x = cx + radius2 * cosAlpha;
	var p2y = cy + radius2 * sinAlpha;
	var p3x = cx + radius1 * Math.cos(beta);
	var p3y = cy + radius1 * Math.sin(beta);
	ctx.fillStyle = color;

	ctx.beginPath();

	ctx.moveTo(p1x, p1y);
	ctx.lineTo(p2x, p2y);
	ctx.arc(cx, cy, radius2, alpha, beta, false);	// false 顺时针
	ctx.lineTo(p3x, p3y);
	ctx.arc(cx, cy, radius1, beta, alpha, true);	// true 逆时针

	ctx.closePath();

	ctx.fill();
}

// 绘制表盘
// cx, cy 表盘中心
// r1 小刻度
// r2 大刻度
function drawDial(ctx, cx, cy, r1, r2) {
	var alpha = 0;
	var vsin;
	var vcos;
	ctx.fillStyle = rgba(50, 50, 50, 0.6);

	for(var i = 0; i < 60; ++i) {
		if(i%5 == 0)continue;
		alpha = d2r(6*i);
		vcos = Math.cos(alpha);
		vsin = Math.sin(alpha);
		ctx.beginPath();
		ctx.arc(cx + r1*vcos, cy + r1*vsin, 1, 0, 2*Math.PI, true);
		ctx.closePath();
		ctx.fill();
	}
	for(var i = 0; i < 12; ++i) {
		alpha = d2r(30*i);
		vcos = Math.cos(alpha);
		vsin = Math.sin(alpha);

		ctx.beginPath();
		ctx.arc(cx + r2*vcos, cy + r2*vsin, 3, 0, 2*Math.PI, true);
		ctx.closePath();
		ctx.fill();
	}
}

// 绘制指针
// 
function drawHand(cx, cy, r1, r2, degree, precision, color){
	for(var i = 0; i < 360/precision; ++i) {
		sector(context, cx, cy, r1, r2, d2r(i*precision + degree), d2r(precision), 
			rgba(color.r, color.g, color.b, color.a1 + color.a2*i*precision/360));
	}
}

function animate(ctx) {
	var d = new Date();
	var ms = d.getMilliseconds()/1000;
	var seconds = d.getSeconds() + ms;
	var minutes = d.getMinutes() + seconds/60;
	var hours = (d.getHours())%12 + minutes/60;

	ctx.clearRect(0, 0, 500, 500);

	drawDial(ctx, center.x, center.y, 150, 200);

	drawHand(center.x, center.y, 200, 201, ms*360 - 90, 10, msColor);
	drawHand(center.x, center.y, 150, 200, seconds*6 -90, 4, sColor);
	drawHand(center.x, center.y, 100, 150, minutes*6 -90, 4, mColor);
	drawHand(center.x, center.y, 10, 100, hours*30 -90, 4, hColor);
}

var canvas = document.getElementById('c');
var context = canvas.getContext('2d');
// context.globalCompositeOperation = "lighter";		// 叠加绘制

setInterval(function(){
	animate(context);
	}, 1000/fps);

})();
</script>
