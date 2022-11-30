//0.a.d.debug.1
//搞到最后是right函数写错导致的数组越界
//开始引入New()和gameover()
//加入touch事件原理的小区块上下滑动
//尚未加入gameover()的“重新开始”功能

//0.a.d.preview.0
//加入gameover()的“重新开始”，且可用
//css引入
//不同数字颜色问题，如何解决

<template>
	<view @touchstart="touchStart" @touchend="touchEnd">
		<view class="title2048">
			2048🍎
		</view>
		<!-- <view class="guide">Join the numbers and get to the 2048 tile!</view> -->
		<view class="guide">滑动来使相同格子相加，从而得到2048！！！</view>
		<view class="score">
			<view class="scoreChinese">
				score
			</view>
			<view class="scoreNum">{{this.sum}}</view>
		</view>
		
		<view class="allDiv20481" v-if="over">
			<view class="cai">菜！</view>
			<view class="finalscore">Final Score: {{this.sum}}</view>
		</view>
		<view class="allDiv2048" >
			<!-- <input @keyup.up="up()" @keyup.down="down()" @keyup.left="left()" @keyup.right="right()"> -->
			<view v-for="(item, index) in Mix" :key="index" >
				<view class="div0" v-if="item.num==0&&item.flag"> </view>
				<view class="div2" v-if="item.num==2&&item.flag">2</view>
				<view class="div4" v-if="item.num==4&&item.flag">4</view>
				<view class="div8" v-if="item.num==8&&item.flag">8</view>
				<view class="div16" v-if="item.num==16&&item.flag">16</view>
				<view class="div32" v-if="item.num==32&&item.flag">32</view>
				<view class="div64" v-if="item.num==64&&item.flag">64</view>
				<view class="div128" v-if="item.num==128&&item.flag">128</view>
				<view class="div256" v-if="item.num==256&&item.flag">256</view>
				<view class="div512" v-if="item.num==512&&item.flag">512</view>
				<view class="div1024" v-if="item.num==1024&&item.flag">1024</view>
				<view class="div2048" v-if="item.num==2048&&item.flag">2048</view>
			<view class="divMore" v-if="item.num>2048&&item.flag">item.num</view>
			</view>
		</view>
		<!-- <view v-if="over">Game Over!!!</view>
		<view v-if="over"> </view> -->
		<!-- <view v-if="over">Final Score: {{this.sum}}</view> -->
		<button class="reStart" v-if="over" @click="newInit()">重新开始</button>
		<button class="reStart" v-if="!over"@click="newInit()">新游戏</button>
	</view>
</template>

<script>
	export default {
		
		data() {
			return {
				// Array[6][6]
				Mix: [{num:0,flag:false},{num:0,flag:false},{num:0,flag:false},{num:0,flag:false},{num:0,flag:false},{num:0,flag:false},
						{num:0,flag:false},{num:0,flag:true},{num:0,flag:true},{num:0,flag:true},{num:0,flag:true},{num:0,flag:false},
						{num:0,flag:false},{num:0,flag:true},{num:0,flag:true},{num:0,flag:true},{num:0,flag:true},{num:0,flag:false},
						{num:0,flag:false},{num:0,flag:true},{num:0,flag:true},{num:0,flag:true},{num:0,flag:true},{num:0,flag:false},
						{num:0,flag:false},{num:0,flag:true},{num:0,flag:true},{num:0,flag:true},{num:0,flag:true},{num:0,flag:false},
						{num:0,flag:false},{num:0,flag:false},{num:0,flag:false},{num:0,flag:false},{num:0,flag:false},{num:0,flag:false},],
				sum: 0,
				temp: -1,
				flag: false,
				ai: 0,
				aj: 0,
				bi: 0,
				bj: 0,
				over: false,
				touchStartX: 0,  // 触屏起始点x  
				touchStartY: 0, // 触屏起始点y 
				interval_id: "",
				interval:"",
				counter: 0
			}
		},
		onLoad:function(){
			this.stopSmooth();
			this.newInit();
		},
		// computed:{
		// 	reMix(){
		// 		let list = JSON.parse(JSON.stringify(this.Mix)); //拷贝对象
		// 		return list;
		
		// 	}
		// },
		methods: {
			stopSmooth:function(){
				var mo = function(e){
					e.preventDefault();
				}
				document.body.style.overflow='hidden';
				document.addEventListener("touchmove",mo,false);
			},	
			// addColor(){
			// 	// var i,j;
			// 	// for(i=1;i<=4;i++){
			// 	// 	for(j=0;j<=4;j++){
						
			// 	// 		this.Mix[6*i+j] = 0;
			// 	// 	}
			// 	// }
			// 	if(this.Mix[6*1+1].num==2) return 'div2';
			// 	else if(e==4) return 'div4';
			// 	else if(e==8) return 'div8';
			// 	else if(e==16) return 'div16';
			// 	else if(e==32) return 'div32';
			// 	else if(e==64) return 'div64';
			// 	else if(e==128) return 'div128';
			// 	else if(e==256) return 'div256';
			// 	else if(e==512) return 'div512';
			// 	else if(e==1024) return 'div1024';
			// 	else if(e==2048) return 'div2048';
			// 	else return 'divnull';
			// },
			/**  
			* 触摸开始  
			**/  
			touchStart(e) {  
				console.log("触摸开始");  
			    this.touchStartX = e.touches[0].clientX;  
			    this.touchStartY = e.touches[0].clientY;  
			},  
			/**  
			* 触摸结束  
			**/  
			touchEnd(e) {  
				console.log("触摸结束"); 
			    let deltaX = e.changedTouches[0].clientX - this.touchStartX;  
			    let deltaY = e.changedTouches[0].clientY - this.touchStartY;  
			    if (Math.abs(deltaX) > 50 && Math.abs(deltaX) > Math.abs(deltaY)) {  
			        if (deltaX >= 0) {  
			            console.log("左滑");
						setTimeout(this.right(),0);
			        } else {  
			            console.log("右滑");
						setTimeout(this.left(),0);
			        }  
			    } else if(Math.abs(deltaY) > 50&& Math.abs(deltaX) < Math.abs(deltaY)) {  
			        if (deltaY < 0) {  
			            console.log("上滑");
						setTimeout(this.up(),0);
			        } else {  
			            console.log("下滑");
						setTimeout(this.down(),0);
			        }  
			    } else {  
			        console.log("可能是误触！");
					// alert("可能是误触！");
			    }  
			}, 
			// 初始化
			newInit:function(){
				this.counter = 0
				this.interval_id = setInterval(this.Init,250);
				// 不能写成this.Init()，因为this.Init()无返回值，会被识别为undefined;
			},
				
			newInit2:function(){
				this.interval = setInterval(this.Init,500);
			},
			Init:function(){
				this.counter++;
				console.log('Init');
				console.log(this.counter);
				if(this.counter == 15){
					clearInterval(this.interval_id);
					this.newInit2();
				}
				if(this.counter == 19){
					clearInterval(this.interval);
				}
				// console.log('Init');
				else{
					this.over = false;
					this.temp = 0;
					this.sum = 0;
					var ai,aj,bi,bj,i,j;
					for(i=1;i<=4;i++){
						for(j=0;j<=4;j++){
							this.Mix[6*i+j].num = 0;
						}
					}
					ai = Math.random()*4+1;
					ai = parseInt(ai, 10);
					aj = Math.random()*4+1;
					aj = parseInt(aj, 10);
					bi = Math.random()*4+1;
					bi = parseInt(bi, 10);
					bj = Math.random()*4+1;
					bj = parseInt(bj, 10);
					while(ai == bi && aj == bj){
						bi = Math.random()*4+1;
						bi = parseInt(bi, 10);
						bj = Math.random()*4+1;
						bj = parseInt(bj, 10);
					}
					this.Mix[ai*6+aj].num=2;
					this.Mix[bi*6+bj].num=2;
				}
				// this.over = false;
				// this.temp = 0;
				// this.sum = 0;
				// var ai,aj,bi,bj,i,j;
				// for(i=1;i<=4;i++){
				// 	for(j=0;j<=4;j++){
				// 		this.Mix[6*i+j].num = 0;
				// 	}
				// }
				// ai = Math.random()*4+1;
				// ai = parseInt(ai, 10);
				// aj = Math.random()*4+1;
				// aj = parseInt(aj, 10);
				// bi = Math.random()*4+1;
				// bi = parseInt(bi, 10);
				// bj = Math.random()*4+1;
				// bj = parseInt(bj, 10);
				// while(ai == bi && aj == bj){
				// 	bi = Math.random()*4+1;
				// 	bi = parseInt(bi, 10);
				// 	bj = Math.random()*4+1;
				// 	bj = parseInt(bj, 10);
				// }
				// this.Mix[ai*6+aj].num=2;
				// this.Mix[bi*6+bj].num=2;
				// Vue 不能检测以下数组的变动：
				
				// 当你利用索引直接设置一个数组项时，例如：vm.items[indexOfItem] = newValue
				// 当你修改数组的长度时，例如：vm.items.length = newLength
				// this.Mix[ai][aj] = 2;
				// this.Mix[bi][bj] = 2;
				// this.ai = ai;
				// this.aj = aj;
				// this.bi = bi;
				// this.bj = bj;
			},
			New:function(flag){
				var temp,i0,j0;
				if(flag){
					// var temp,i0,j0;
					temp = Math.random()*10;
					temp = parseInt(temp,10);
					// i0 = Math.random()*4+1;
					// i0 = parseInt(i0,10);
					// j0 = Math.random()*4+1;
					// j0 = parseInt(j0,10);
					do{
						i0 = Math.random()*4+1;
						i0 = parseInt(i0,10);
						j0 = Math.random()*4+1;
						j0 = parseInt(j0,10);
					}while(this.Mix[i0*6+j0].num);
					if(temp == 5){
						this.Mix[i0*6+j0].num=4;
						
					}
					else{
						this.Mix[i0*6+j0].num=2;
					}
				}	
			},
			GameOver:function(){
				var flag=true,filled=true;
				var i,j;
				for(i=1;i<=4;i++) for(j=1;j<=4;j++) if(!this.Mix[i*6+j].num) filled=false;
					if(!filled){
						console.log("not filled!");
						return filled;
					}
					else
					{
						for(i=1;i<=4;i++) for(j=1;j<=4;j++)
						{
							if(this.Mix[i*6+j].num==this.Mix[(i-1)*6+j].num) flag=false;
							if(this.Mix[i*6+j].num==this.Mix[(i+1)*6+j].num) flag=false;
							if(this.Mix[i*6+j].num==this.Mix[i*6+j+1].num) flag=false;
							if(this.Mix[i*6+j].num==this.Mix[i*6+j-1].num) flag=false;
						}
						console.log("Sum avaiable:" );
						console.log(flag);
						return flag;
					}
			},
			// 操作
			up:function(){
				setTimeout(this.Move_up(),0);
				if(this.GameOver()){
					this.over = true;
				}
			},
			left:function(){
				setTimeout(this.Move_Left(),0);
				if(this.GameOver()){
					this.over = true;
				}
			},
			right:function(){
				setTimeout(this.Move_Right(),0);
				if(this.GameOver()){
					this.over = true;
				}
			},
			down:function(){
				setTimeout(this.Move_Down(),0);
				if(this.GameOver()){
					this.over = true;
				}
			},
			Move_up:function(){
				var flag = false;
				var t, i, j;
				for(t=3;t;t--) for(i=4;i>1;i--) for(j=1;j<=4;j++) if(this.Mix[i*6+j].num) if(!this.Mix[(i-1)*6+j].num)
					{
						this.Mix[(i-1)*6+j].num=this.Mix[i*6+j].num;
						this.Mix[i*6+j].num=0;
						
						flag=true;
					}
					for(i=1;i<4;i++) for(j=1;j<=4;j++) if(this.Mix[i*6+j].num)
					{
						if(this.Mix[(i+1)*6+j].num==this.Mix[i*6+j].num)
						{
							this.Mix[(i)*6+j].num=2*this.Mix[(i)*6+j].num;
							
							this.Mix[(i+1)*6+j].num=0;
							this.sum+=this.Mix[i*6+j].num; 
							flag=true;
						}
					}
					for(t=3;t;t--) for(i=4;i>1;i--) for(j=1;j<=4;j++) if(this.Mix[i*6+j].num) if(!this.Mix[(i-1)*6+j].num)
					{
						this.Mix[(i-1)*6+j].num=this.Mix[i*6+j].num;
						this.Mix[i*6+j].num=0;
						
						flag=true;
					}
					this.New(flag);
					console.log("up success!");
					i=null;
					j=null;
					t=null;
					flag=null;
			},
			Move_Left:function(){
				var flag = false;
				var t, i, j;
				for(t=3;t;t--) for(i=1;i<=4;i++) for(j=4;j>1;j--) if(this.Mix[i*6+j].num) if(!this.Mix[(i)*6+j-1].num)
					{
						this.Mix[(i)*6+j-1].num=this.Mix[i*6+j].num;
						this.Mix[i*6+j].num=0;
						
						flag=true;
					}
					for(i=1;i<=4;i++) for(j=1;j<4;j++) if(this.Mix[i*6+j].num)
					{
						if(this.Mix[(i)*6+j+1].num==this.Mix[i*6+j].num)
						{
							this.Mix[(i)*6+j].num=2*this.Mix[(i)*6+j].num;
							this.Mix[(i)*6+j+1].num=0;
							this.sum+=this.Mix[i*6+j].num; 
							flag=true;
						}
					}
					for(t=3;t;t--) for(i=1;i<=4;i++) for(j=4;j>1;j--) if(this.Mix[i*6+j].num) if(!this.Mix[(i)*6+j-1].num)
					{
						this.Mix[(i)*6+j-1].num=this.Mix[i*6+j].num;
						this.Mix[i*6+j].num=0;
						
						flag=true;
					}
					this.New(flag);
					i=null;
					j=null;
					t=null;
					flag=null;
					console.log("left success!");
					
			},
			Move_Right:function(){
				var flag = false;
				var t, i, j;
				for(t=3;t;t--) for(i=1;i<=4;i++) for(j=1;j<4;j++) if(this.Mix[i*6+j].num) if(!this.Mix[(i)*6+j+1].num)
					{
						this.Mix[(i)*6+j+1].num=this.Mix[i*6+j].num;
						this.Mix[i*6+j].num=0;
					
						flag=true;
					}
					for(i=1;i<=4;i++) for(j=4;j>1;j--) if(this.Mix[i*6+j].num)
					{
						if(this.Mix[(i)*6+j-1].num==this.Mix[i*6+j].num)
						{
							this.Mix[(i)*6+j].num=2*this.Mix[(i)*6+j].num;
							this.Mix[(i)*6+j-1].num=0;
							this.sum+=this.Mix[i*6+j].num; 
							flag=true;
						}
					}
					for(t=3;t;t--) for(i=1;i<=4;i++) for(j=1;j<4;j++) if(this.Mix[i*6+j].num) if(!this.Mix[(i)*6+j+1].num)
					{
						this.Mix[(i)*6+j+1].num=this.Mix[i*6+j].num;
						this.Mix[i*6+j].num=0;
						
						flag=true;
					}
					this.New(flag);
					i=null;
					j=null;
					t=null;
					flag=null;
					console.log("right success!");
			},
			Move_Down:function(){
				var flag = false;
				var t, i, j;
				for(t=3;t;t--) for(i=1;i<4;i++) for(j=1;j<=4;j++) if(this.Mix[i*6+j].num) if(!this.Mix[(i+1)*6+j].num)
					{
						this.Mix[(i+1)*6+j].num=this.Mix[i*6+j].num;
						this.Mix[i*6+j].num=0;
						
						flag=true;
					}
					for(i=4;i>1;i--) for(j=1;j<=4;j++) if(this.Mix[i*6+j].num)
					{
						if(this.Mix[(i-1)*6+j].num==this.Mix[i*6+j].num)
						{
							this.Mix[(i)*6+j].num=2*this.Mix[(i)*6+j].num;
							this.Mix[(i-1)*6+j].num=0;
							
							this.sum+=this.Mix[i*6+j].num; 
							flag=true;
						}
					}
					for(t=3;t;t--) for(i=1;i<4;i++) for(j=1;j<=4;j++) if(this.Mix[i*6+j].num) if(!this.Mix[(i+1)*6+j].num)
					{
						this.Mix[(i+1)*6+j].num=this.Mix[i*6+j].num;
						this.Mix[i*6+j].num=0;
						
						flag=true;
					}
					this.New(flag);
					i=null;
					j=null;
					t=null;
					flag=null;
					console.log("down success!");
			}
		}
	}
</script>

<style>
	.guide{
		height: 120rpx;
		width: 300rpx;
		float:left;
		margin-top: 50rpx;
		margin-left: 110rpx;
		margin-right: ;
		font-weight: bold;
		color: #786F66;
		/* margin-right: 300rpx; */
	}
	.cai{
		font-size: 350rpx;
		font-weight: bold;
		margin-left: 30rpx;
		margin-top: 25rpx;
		float: v-bind();
		color: #EEE4DA;
		/* position: absolute;
		z-index:1314; */
		/* background-color: #EEE4DA; */
	}
	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 20rpx;
		margin-left: 110rpx;
		margin-right: 87rpx;
		margin-bottom: 20rpx;
	},
	.num{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #EEE4DA;
		border: 2px ;
		border-radius: 5px;
		font-size: 66rpx;
		font-weight: 600;
		color:#786F66;
		text-align: center;
		line-height: 50px;
		/* vertical-align: top; */
	},
	.score{
		height: 120rpx;
		width: 220rpx;
		background-color: #BAADA0;
		border: 2px ;
		/* float: left; */
		border-radius: 5px;
		margin-top: 50rpx;
		margin-left: 420rpx;
	},
	.scoreChinese{
		color: #E6DCD1;
		font-size: 35rpx;
		font-weight: 300rpx;
		text-align: center;
		line-height: 30px;
	},
	.scoreNum{
		color: #FFFFFF;
		font-size: 50rpx;
		font-weight: 800rpx;
		text-align: center;
		line-height: 20px;
	}
	.allDiv2048{
		height: 530rpx;
		width: 530rpx;
		margin-top: 50rpx;
		margin-left: 110rpx;
		margin-right: 110rpx;
		margin-bottom: 30rpx;
		float: left;
		background-color: #BAAD9F;
		border: 2px ;
		border-radius: 5px;
		position: absolute;
		z-index:-1;
	},
	.allDiv20481{
		height: 530rpx;
		width: 530rpx;
		margin-top: 50rpx;
		margin-left: 110rpx;
		margin-right: 110rpx;
		margin-bottom: 30rpx;
		float: left;
		/* background-color: #BAAD9F; */
		background-color: rgba(186,173,159,0.5);
		border: 2px ;
		border-radius: 5px;
		position: absolute;
		z-index:1314;
	},
	.test2{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #EEE4DA;
		border: 2px ;
		border-radius: 5px;
		font-size: 66rpx;
		font-weight: 600;
		color:#786F66;
		text-align: center;
		line-height: 50px;
		/* vertical-align: top; */
	},
	.div0{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #CDBFB4;
		border: 2px ;
		border-radius: 5px;
		font-size: 66rpx;
		font-weight: 600;
		color:#786F66;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div2{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #EEE4DA;
		border: 2px ;
		border-radius: 5px;
		font-size: 66rpx;
		font-weight: 600;
		color:#786F66;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div4{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #EDDFC7;
		border: 2px ;
		border-radius: 5px;
		font-size: 66rpx;
		font-weight: 600;
		color:#776E65;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div8{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #F2B079;
		border: 2px ;
		border-radius: 5px;
		font-size: 66rpx;
		font-weight: 600;
		color:#F8F6F2;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div16{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #F59463;
		border: 2px ;
		border-radius: 5px;
		font-size: 66rpx;
		font-weight: 600;
		color:#FFFFFF;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div32{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #F67B5E;
		border: 2px ;
		border-radius: 5px;
		font-size: 66rpx;
		font-weight: 600;
		color:#FFFFFF;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div64{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #F67D4A;
		border: 2px ;
		border-radius: 5px;
		font-size: 66rpx;
		font-weight: 600;
		color:#FFFFFF;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div128{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #EDCF72;
		border: 2px ;
		border-radius: 5px;
		font-size: 55rpx;
		font-weight: 600;
		color:#FFFFFF;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div256{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #F2CB67;
		border: 2px ;
		border-radius: 5px;
		font-size: 55rpx;
		font-weight: 600;
		color:#FFFFFF;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div512{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #F3C657;
		border: 2px ;
		border-radius: 5px;
		font-size: 55rpx;
		font-weight: 600;
		color:#FFFFFF;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div1024{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #F3C350;
		border: 2px ;
		border-radius: 5px;
		font-size: 40rpx;
		font-weight: 600;
		color:#FFFFFF;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.div2048{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #F3BF39;
		border: 2px ;
		border-radius: 5px;
		font-size: 40rpx;
		font-weight: 600;
		color:#FFFFFF;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.divMore{
		height: 110rpx;
		width: 110rpx;
		margin-top: 18rpx;
		/* margin-bottom: 18rpx; */
		margin-left: 18rpx;
		/* margin-right: 18rpx; */
		float:left;
		background-color: #12122F;
		border: 2px ;
		border-radius: 5px;
		font-size: 40rpx;
		font-weight: 600;
		color:#FFFFFF;
		text-align: center;
		line-height: 55px;
		/* vertical-align: top; */
	},
	.reStart{
		 background-color: #BAAD9F; /* Green */
		  border: none;
		  color: white;
		  padding: 15rpx 32rpx;
		  border-radius: 6rpx;
		  text-align: center;
		  text-decoration: none;
		  display: inline-block;
		  font-size: 30rpx;
		  font-weight: 500rpx;
		  margin-left: 280rpx;
		  margin-top: 600rpx;
		  
	},
	.showALLdiv{
		margin-left: 110rpx;
		margin-right: 110rpx;
	},
		
	.title2048{
		font-size: 135rpx;
		font-weight: bold;
		margin-left: 110rpx;
		margin-top: 25rpx;
		float: v-bind();
		color: #776E65;
	},
	.finalscore{
		color: #776E65;
		font-weight:550;
		margin-left: 150rpx;
	}
</style>
