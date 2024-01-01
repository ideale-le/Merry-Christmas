	<template>
		<view class="content" >
			<!-- 
				1. 背景：snow 
				2. 图： tree  three.js-tree
				3. 音乐： chirtmas
				4. 出现弹窗： 快递mag_tips 
				
			1. 引导页： 放老婆去年的图 MC
				2. 当引导页倒计时结束  会跳转到主 （MC_TREE）并且开始播放音乐 
			-->
			<!-- <view class=""> -->
				<image src="../../static/dog_tree.jpg" mode="" class="img" ></image>
				<!-- <view class="text">
					<text>2023.12.25</text>
					<text>give chen</text>
				</view> -->
			<!-- </view> -->
			<!-- 这样icon 就会出现了 -->
				<view class="warp">
					<!-- 播放按钮 -->
					<view class="music"  @click="togglePlay" >
						<image v-if="status" 
						class="icon"
						mode="widthFix" 
						src="../../static/music.jpg" 
						style="width:100rpx;height:100rpx;" 
						></image>
						<image  v-else 
						src="../../static/music_stop.jpg" 
						style="width:100rpx;height:100rpx;" 
						mode="widthFix"></image>
					</view>  
					  
				</view>
				<view class="">
					<text>2023.12.25</text>
				</view>
				<!-- 雪花 -->
			<view id="snowzone">
				<span v-for="(snowflake, index) in snowflakes" :key="index" class="snowflake" :style="snowflake.style">
				  {{ snowflake.snowletter }}
				</span>
					<!--  -->
					
					
			</view>
			
			
		</view>
</template>

	<script>
		export default {
		  data() {
			return {
				
				isPlay: false,	//  false 音乐停止
				status: false,  // false stop icon 出来
				innerAudioContext: null,
				lastRecord:"https://rd-sycdn.kuwo.cn/eda0cd266c7997124d1aab88fde11629/6589749d/resource/n1/24/16/2529067112.mp3?from=vip",
				
			  snowmax: 120,
			  snowcolor: ["#FFDA65", "#00AADD", "#aaaacc", "#a0c4ff", "#ccccdd", "#caffbf", "#bbf7f9", "#ffc6ff", "#ffd6a5"],
			  snowtype: ["Times", "Arial", "Verdana"],
			  snowletter: ["❄", "❅", "❆"],
			  sinkspeed: 0.3,
			  sink: 0.01,
			  snowmaxsize: 32,
			  snowminsize: 15,
			  snowingzone: 1,
			  snowflakes: [],  // 
			  marginbottom: 0,
			  marginright: 0,
			  timer: null,
			  i_snow: 0,
			  x_mv: [],
			  crds: [],
			  lftrght: [],
			  browserinfos: navigator.userAgent,
			  ie5: document.all && document.getElementById && !navigator.userAgent.match(/Opera/),
			  ns6: document.getElementById && !document.all,
			  opera: navigator.userAgent.match(/Opera/),
			  browserok: true,
			  snowsizerange :5,
			};
		  },
		  //开启音乐
			onShow(){
				setTimeout(() => {
				  this.open();
				}, 100);
			},
			//非本页关闭音乐
			onHide(){
				// this.innerAudioContext.pause(); // 停止
				this.pauseAudio();
			},
			//非本页关闭音乐
			onUnload(){
				// this.innerAudioContext.pause(); // 停止
				this.pauseAudio();
			},
		  mounted() {
			this.browserok = this.ie5 || this.ns6 || this.opera;
			if (this.browserok) {
			  this.initsnow();
			  // this.movesnow();
			}
		  },
		  
		  methods: {
			// 播放/暂停音乐
				togglePlay() {
					
				if (this.isPlay) {
					this.pauseAudio();  //stop
				} else {
					this.playAudioOnUserInteraction();
				}
				
				// this.isPlay = !this.isPlay;
				// this.status = !this.status;
			},
			// 用户点击时播放音频
			playAudioOnUserInteraction(){
				this.playVoice();
				// document.removeEventListener('click', this.playAudioOnUserInteraction);
			},
			
			//播放音乐
			open(){
				//播放音乐
				this.innerAudioContext = uni.createInnerAudioContext();
				this.playVoice();
			},
			//播放音乐
			playVoice() { // url即为音频路径
				if (this.lastRecord) {
					this.innerAudioContext.src = this.lastRecord;
					this.innerAudioContext.play();
					this.innerAudioContext.loop = true;
						this.isPlay = true;
						this.status = true;
				}else{
					this.innerAudioContext.loop = false;
					this.isPlay = false;
					this.status = false;
				}
			},
			// 暂停音乐
			pauseAudio() {
				if (this.innerAudioContext) {
					this.innerAudioContext.pause();
					this.isPlay = false;
					this.status = false;
				}
			},
			  
			// 雪背景
			randommaker(range) {
			  return Math.floor(range * Math.random());
			},
			initsnow() {
				this.marginright = window.innerWidth /10;
				this.marginbottom = window.innerHeight /10;

			 this.snowflakes = [];
			   this.crds = new Array(this.snowmax + 1).fill(0);
			   this.x_mv = new Array(this.snowmax + 1).fill(0).map(() => Math.random() * 0.6); // 添加这一行，为 x_mv 数组添加一些随机值
			   this.lftrght = new Array(this.snowmax + 1).fill(0).map(() => Math.random() * 15); 
			  if (this.ie5 || this.opera) {
				console.log("ie5");
				this.marginbottom = document.body.scrollHeight - 80;
				this.marginright = document.body.clientWidth - 15;
			  } else if (this.ns6) {
				this.marginbottom = document.body.scrollHeight - 80;
				this.marginright = window.innerWidth - 15;
			  }
			
			  const snowsizerange = this.snowmaxsize - this.snowminsize;
			
			  console.log("snowsizerange ");
			  console.log("nadoa");
			
			  for (let i = 0; i <= this.snowmax; i++) {
				  // console.log('Snowflakes:', this.snowflakes);  // 数组
				const size = this.randommaker(snowsizerange) + this.snowminsize;
				
				this.snowflakes.push({
				  id: i,
				  fontFamily: this.snowtype[this.randommaker(this.snowtype.length)],
				  size: size,
				  fontSize: size + 'px',
				  color: this.snowcolor[this.randommaker(this.snowcolor.length)],
				  zIndex: 1000,
				  sink: this.sinkspeed * size / 30,   // 调节速度 snow
				  posx:Math.random() * (this.marginright - size),
				  posy: Math.random() *  (this.marginbottom - 2 * size),
					snowletter: this.snowletter[this.randommaker(this.snowletter.length)],
				  style: {  },
				});
			  }
			
			  this.movesnow();
			},
			movesnow() {
				for (let i = 0; i <= this.snowmax; i++) {
				   this.crds[i] += this.x_mv[i];
				   const snowflake = this.snowflakes[i];
				   const snowflakeSize = snowflake?.size;
				   snowflake.style.left = "-9999px";
				   snowflake.style.top = "-9999px";
			   if (snowflake && snowflakeSize) {
				 snowflake.posy += snowflake.sink;
				 // snowflake.posx += this.lftrght[i] * Math.sin(this.crds[i]) ;
				 snowflake.posy += this.sink /10;
				 snowflake.posx += Math.random() * 0.1 - 0.02;
				snowflake.snowletter = this.snowletter[this.randommaker(this.snowletter.length)];

				// snowflake.snowletter = String.fromCharCode(9731 + this.randommaker(3));  // yingx	
				 // 设置元素位置
				 // console.log(`i: ${i}, snowletter: ${snowflake.snowletter}, posx: ${snowflake.posx}, posy: ${snowflake.posy},crds[i]: ${this.crds[i]}, sin(crds[i]): ${Math.sin(this.crds[i])}`);
				 snowflake.style.left = snowflake.posx + 'px';
				 snowflake.style.top = snowflake.posy + 'px';
				 
		   
				 if (snowflake.posy >= this.marginbottom - 2 * snowflakeSize || snowflake.posx > (this.marginright - 3 * this.lftrght[i] || snowflake.posx < this.snowflakeSize)) {
					 snowflake.posx = Math.random() * this.marginright;
					 snowflake.posy = 0;
					 if (this.snowingzone === 1) {
						 snowflake.posx = Math.random() * this.marginright;
					 } else if (this.snowingzone === 2) {
						 snowflake.posx = Math.random() * (this.marginright / 2);
					 } else if (this.snowingzone === 3) {
						 snowflake.posx = Math.random() * (this.marginright / 4) + this.marginright / 2;
					 } else if (this.snowingzone === 4) {
						 snowflake.posx = Math.random() * (this.marginright / 2) + this.marginright / 4;
					 }
				 }
			 }
		   
			 setTimeout(() => {
			   this.movesnow();
			 }, 50);
		   }
		}
	}
}
	</script>

	<style lang="scss" scoped>
		 
		#snowzone {
		  position: relative;
		  width: 100%;
		  height: 100vw; /* 设置为视窗高度 */
		}
		 .snowflake {
			 // background-color: skyblue;
			 // color:#fff;
			 // overflow: hidden;
		   position: absolute;
		   top: 0;
		 }
		 .warp{
			float:right;
		}
		.img{
			display: flex;
			justify-content: center;
			width: 100%;
			height: 100vw;
			// padding:-50%;
			position: absolute;
		}
		.text{
			font-weight: 400;
			display: flex;
			justify-content: center;
			flex-direction: column;
		}
	</style>
