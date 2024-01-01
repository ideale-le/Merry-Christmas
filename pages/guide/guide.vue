<template>
	<view class="content">
		<image src="../../static/kai.jpg" mode="" class="img" @click="startCountdown()"></image>
		<view class="space" >
			
		</view>
		
	</view>
</template>
 
<script>
	export default {
		data() {
			return {
				// agree: ""
				countdown: 1, 
			}
		},
		onLoad() {
			// uni.setStorageSync('agree', "")
			// this.agree = uni.getStorageSync('agree')
			this.startCountdown();
		},
		methods: {
			startCountdown() {
			 let seconds = 3; // 初始倒计时秒数
				 const timer = setInterval(() => {
				   if (seconds > 0) {
					 console.log(seconds);
					 seconds--;
				   } else {
					 // 倒计时为零时执行跳转操作，这里假设跳转到首页
					 clearInterval(timer);
					 uni.redirectTo({
					   url: '/pages/index/index', // 跳转到首页的路径
					
					 });
				   }
				 }, 1000); // 每秒执行一次
			},
			// 是否同意政策
			// agreePolicy(act) {
			// 	if (act == 0) {
			// 		plus.runtime.quit()
			// 	} else if (act == 1) {
			// 		uni.setStorageSync('agree', true)
			// 		this.agree = true
			// 	}
			// }
		}
	}
</script>
 
<style lang="scss">
	page {
		.content {
			position: relative;
			display: flex;
			justify-content: center;
			width: 100%;
			height: 100%;
			.img {
				position: absolute;
				padding:50%;
				width: 50%;
				z-index: -1;
				height:50vw;
				border-radius: 50%;
			}
 
		}
	}
</style>