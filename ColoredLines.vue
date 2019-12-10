<template>
		<canvas class="lines-canvas" id = "lines-canvas"></canvas>
</template>

<script>
	export default {
        name: "ColoredLines",
		props: {
			amount: {
				type: Number,
				default: 5
			},
			size: {
				type: Number,
				default: 15
			},
			speed: {
				type: Number,
				default: 25
			},
			sprinkleWidth: {
				type: Number,
				default: 15
			},
			color: {
				type: String,
				default: '#00F'
			},
			sprinkleHeight: {
				type: Number,
				default: 15
			},
			zIndex: {
				type: Number,
				default: -1
			},
			sprinkleRadius: {
				type: Number,
				default: 3
			}
		},
		mounted() {
			var self = this;
			var CANVAS = self.$el;
			var CONTEXT = CANVAS.getContext('2d');
			var canvasHeight = CANVAS.offsetHeight;
			console.log(canvasHeight)
			var canvasWidth = CANVAS.offsetWidth;
			var sprinkles = []
			CANVAS.height = canvasWidth;
			CANVAS.width = canvasHeight;
			CANVAS.style.zIndex = self.zIndex ? self.zIndex : 'auto';
	   		function init() {
				for (var i = 0; i < self.amount; i++) {
					sprinkles.push({
						x: random(0, canvasWidth),
						y: random(0, canvasHeight),
						r: random(self.size, self.size * 2) / 2,
						dx: 5,
						dy: 5,
						sh: self.sprinkleHeight,
						sw: self.sprinkleWidth,
						sr: self.sprinkleRadius
					});  
				}
				sprinkling()
			}

			function sprinkling() {
				for (var i = 0; i < self.amount; i++) {
					var sprinkle = sprinkles[i]
					CONTEXT.fillStyle = 'rgba(' + randomColor(self.color);
					makeSprinkle(CONTEXT, sprinkle.y, sprinkle.x, sprinkle.sh, sprinkle.sw, sprinkle.sr)
					CONTEXT.closePath();

					if(sprinkle.x + sprinkle.dx > canvasWidth || sprinkle.x + sprinkle.dx < 0)
					{
						sprinkle.dx = -sprinkle.dx
					}
					if(sprinkle.y + sprinkle.dy > canvasHeight || sprinkle.y + sprinkle.dy < 0)
					{
						sprinkle.dy = -sprinkle.dy
					}

					sprinkle.x += sprinkle.dx
					sprinkle.y += sprinkle.dy
					
				}
				requestAnimationFrame(sprinkling);
			}

			function reset(sprinkle) {
				var prevR = sprinkle.r;
				sprinkle.r = random(self.size, self.size * 2) / 2;
				if (sprinkle.x > canvasWidth + prevR) {
					sprinkle.x = -sprinkle.r;
					sprinkle.y = random(0, canvasHeight);
				}
				else if (sprinkle.x < -prevR) {
					sprinkle.x = canvasWidth + sprinkle.r;
					sprinkle.y = random(0, canvasHeight);
				}
				else {
					sprinkle.x = random(0, canvasWidth);
					sprinkle.y = -sprinkle.r;
				}
				sprinkle.swing = random(0, 2*Math.PI);
			}

			init();
      
   
       	 function makeSprinkle(CONTEXT, x, y, width, height, radius) {
            CONTEXT.beginPath();
            CONTEXT.moveTo(x, y + radius);
            CONTEXT.lineTo(x, y + height - radius);
            CONTEXT.arcTo(x, y + height, x + radius, y + height, radius);
            CONTEXT.lineTo(x + width - radius, y + height);
            CONTEXT.arcTo(x + width, y + height, x + width, y + height-radius, radius);
	    	CONTEXT.lineTo(x + width, y + radius);
            CONTEXT.arcTo(x + width, y, x + width - radius, y, radius);
            CONTEXT.lineTo(x + radius, y);
            CONTEXT.arcTo(x, y, x, y + radius, radius);
            CONTEXT.stroke();
            CONTEXT.fill()
        }
        
	}
	
    
}
function random(min, max) {
		return Math.floor(Math.random() * max) + min
	}
function randomColor(userColor) {
		var randomNum  = Math.floor(Math.random() * 9) + 1;
		var randomColor = [userColor, '#00F', '#F00', '#0F0','#0FF','#F0F','#FF0','#D80','#000','#FFF']
		return getRgb(randomColor[randomNum])
	}
function getRgb(str) {
		var rgb = '';
		if (str.indexOf('#') === 0) {
			rgb = str.length === 4 ? str.substr(1).split('').map(function(n) {return parseInt(n.concat(n), 16);}).join(',') :
						str.length === 7 ? [str.substr(1,2), str.substr(3,2), str.substr(5,2)].map(function(n) {return parseInt(n, 16);}).join(',') :
						'255,255,255';
		}
		else if (str.indexOf('rgb(') === 0) {
			rgb = str.substring(4, str.length - 1);
		}
		else {
			rgb = '255,255,255';
		}
		return rgb;
	}

</script>

<style scoped>
	.lines-canvas {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
	}
</style>