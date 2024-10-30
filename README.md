<html><head>
<meta http-equiv="content-type" content="text/html; charset=windows-1252">
<title>Wallpaper Engine</title>
<style>
canvas{
	position:fixed;
	top:0px;
	left:0px;
	background:url("img.png") no-repeat center center fixed;
	-webkit-background-size:cover;
	-moz-background-size:cover;
	-o-background-size:cover;
	background-size:cover;
}
</style>
<script>
//SETTINGS
var colors = ["#893EFF", "#771DFF", "#FF3CFF"];

//Pixels
var pixelSpawn = 6;
var pixelsLimit = 1000;
var pixelGravity = 1;
var pixelGravityForce = 1.02;
var pixelSizeDecay = .6;

//Triangles
var triangleSpawn = 3;
var triangleLimit = 1000;
var triangleGravity = 1;
var triangleSizeDecay = .6;
var triangleGravityForce = 1.02;
</script>
</head>
<body>
<script>
var canvas, ctx;
document.body.onload = function(){
	loadCanvas();
}

function loadCanvas(){
	canvas = document.createElement("canvas");
	canvas.width = window.innerWidth;
	canvas.height = window.innerHeight;
	document.body.appendChild(canvas);
	ctx = canvas.getContext("2d");
	ctx.clearRect(0, 0, 600, 600);
	animateCanvas();
}

function drawTriangle(s){
	ctx.beginPath();
	ctx.moveTo(0, 0);
	ctx.lineTo(-1*s, 1.6*s);
	ctx.lineTo(1*s, 1.6*s);
	ctx.fill();
}

function AddPixel(){
	var c = colors[Math.floor(Math.random()*(colors.length))];
	var s = (Math.floor(Math.random()*10)+4) * 4;
	
	var x = Math.floor(Math.random()*canvas.width);
	var y = -s;//Math.floor(Math.random()*canvas.height);
	var p = new Vector2(x, y);
	
	var r = Math.random() * 90;
	pixels[pixels.length] = new pixel(s, p, r, c);
}

function AddTriangle(){
	var c = colors[Math.floor(Math.random()*(colors.length))];
	var s = (Math.floor(Math.random()*10)+4) * 4;
		
	var x = Math.floor(Math.random()*canvas.width);
	var y = canvas.height;//canvas.height - Math.floor(Math.random()*canvas.height);
	var p = new Vector2(x, y);
		
	var r = Math.random() * 90;
	triangles[triangles.length] = new triangle(s, p, r, c);
}

var pixels = [];
function pixel(s, p, r, c){
	this.size = s;
	this.pos = p;
	this.rot = r;
	this.color = c;
	this.speed = 0;
	this.angularVelocity = 4-(Math.random()*8);
	
	this.Update = function(){
		this.pos.y += this.speed;
		this.speed += pixelGravity / 10;
		this.speed *= pixelGravityForce;
		
		this.rot += this.angularVelocity;
		
		this.size -= pixelSizeDecay;
		
		if(this.pos.y-this.size >= canvas.height || this.size <= 0){
			var i = pixels.indexOf(this);
			pixels.splice(i, 1);
		}
	}
	
	this.Draw = function(){
		ctx.save();
		ctx.translate(this.pos.x, this.pos.y);
		ctx.rotate(this.rot * Math.PI/180);
		ctx.fillStyle = this.color;
		ctx.fillRect(
			-(this.size / 2),
			-(this.size / 2),
			this.size,
			this.size
		);
		ctx.restore();
	}
}

var triangles = [];
function triangle(s, p, r, c){
	this.size = s;
	this.pos = p;
	this.rot = r;
	this.color = c;
	this.speed = 0;
	
	this.Update = function(){
		this.pos.y -= this.speed;
		this.speed += triangleGravity / 10;
		this.speed *= triangleGravityForce;
		
		this.size -= triangleSizeDecay;
		
		if(this.pos.y+this.size <= 0 || this.size <= 0){
			var i = triangles.indexOf(this);
			triangles.splice(i, 1);
		}
	}
	
	this.Draw = function(){
		ctx.save();
		ctx.translate(this.pos.x, this.pos.y);
		ctx.fillStyle = this.color;
		drawTriangle(this.size);
		//ctx.drawImage(tri,0,0,this.size,this.size);
		ctx.restore();
	}
}

function Vector2(x = 0, y = 0){
	this.x = x;
	this.y = y;
}

function animateCanvas(){
	requestAnimationFrame(animateCanvas);
	ctx.clearRect(0, 0, canvas.width, canvas.height);
	
	//UPDATE & DRAW
	for(var i = 0; i < pixels.length; i++){
			pixels[i].Update();
			pixels[i].Draw();
	}
	
	for(var i = 0; i < triangles.length; i++){
			triangles[i].Update();
			triangles[i].Draw();
	}
	
	//ADDS
	if(pixels.length <= pixelsLimit)
		for(var i = 0; i < pixelSpawn; i++)
			AddPixel();
	
	if(triangles.length <= triangleLimit)
		for(var i = 0; i < triangleSpawn; i++)
			AddTriangle();
}
</script>

<canvas width="1920" height="937"></canvas></body></html>