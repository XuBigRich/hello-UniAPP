<template>
	<!-- .vue文件是什么
		1.是一个自定义的文件类型，类似于html语法 用于描述的vue组建
		2.三部分组成
			-template 视图层  组件
			-scripte 逻辑层
			-style  样式层
		3.常用的基础组建
			-view  -> div  容器组件
			-text ->span   文字组件
			-image -> img  图片组件
		 -->
	<view class="container">
		<view class="sele">
			<view class="title">请输入你想做的事情：</view>
			<p>{{ awardsConfig.awards }}</p>
			<input class="uni-input" v-model="mes" focus placeholder="事情清单" />
			<button type="primary" plain="true" @tap="meessageAdd">添加事情</button>
		</view>
		<view class="header">
			<view class="header-title"> 专治各种选择困难症 </view>
		</view>

		<view class="main">
			<view class="canvas-container">
				<view :animation="animationData" class="canvas-content">
					<view class="canvas-line">
						<view class="canvas-litem" v-for="(item,index1) in awardsList" :key="index1" :style="[{transform:'rotate('+item.lineTurn+')'}]"></view>
					</view>

					<view class="canvas-list">
						<view class="canvas-item" v-for="(iteml,index2) in awardsList" :key="index2">
							<view class="canvas-item-text" :style="[{transform:'rotate('+iteml.turn+')'}]">
								<text>{{iteml.award}}</text>
								<image class="canvas-item-text-img" src="../../static/icon/huodong.png"></image>
							</view>
						</view>
					</view>
				</view>

				<view @tap="playReward" class="canvas-btn" v-bind:class="btnDisabled">抉择</view>
			</view>
		</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				awardsConfig: {
					chance: true,
					awards: []
				},
				awardsList: {},
				animationData: {},
				btnDisabled: '',
				mes: '',
			};
		},
		onLoad: function() {
			uni.setNavigationBarColor({
				frontColor: '#ffffff',
				backgroundColor: '#A83FDB',
				animation: {
					duration: 400,
					timingFunc: 'easeIn'
				}
			})
		},
		onReady: function(e) {

			//分享  
			uni.showShareMenu({
				withShareTicket: true
			});
		},
		methods: {
			meessageAdd: function() {
				this.awardsConfig.awards.push(this.mes);
				this.mes='';
				this.drawAwardRoundel();
			},
			//画抽奖圆盘  
			drawAwardRoundel: function() {
				var awards = this.awardsConfig.awards;
				var awardsList = [];
				var turnNum = 1 / awards.length * 360; // 文字旋转 turn 值  

				// 奖项列表  
				for (var i = 0; i < awards.length; i++) {
					awardsList.push({
						turn: i * turnNum + 'deg',
						lineTurn: i * turnNum + turnNum / 2 + 'deg',
						award: awards[i].name
					});
				}

				this.btnDisabled = this.awardsConfig.chance ? '' : 'disabled';
				console.log(awardsList);
				this.awardsList = awardsList;
			},

			//发起抽奖  
			playReward: function() {
				//中奖index  
				var awardsNum = this.awardsConfig.awards;
				var awardIndex = Math.round(Math.random() * (awardsNum.length - 1)); //随机数  
				var runNum = 8; //旋转8周  
				var duration = 4000; //时长  
				console.log(awardIndex);
				// 旋转角度  
				this.runDeg = this.runDeg || 0;
				this.runDeg = this.runDeg + (360 - this.runDeg % 360) + (360 * runNum - awardIndex * (360 / awardsNum.length))
				//创建动画  
				var animationRun = uni.createAnimation({
					duration: duration,
					timingFunction: 'ease'
				})
				animationRun.rotate(this.runDeg).step();
				this.animationData = animationRun.export();
				this.btnDisabled = 'disabled';
				
				// 中奖提示  
				var awardsConfig = this.awardsConfig;
				setTimeout(function() {
					uni.showModal({
						title: '你应该去',
						content: awardsConfig.awards[awardIndex],
						showCancel: false
					});
					this.btnDisabled = '';
				}.bind(this), duration);
			}
		}

	}
</script>

<style>
	page {
		background: #fff;
	}

	.header {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		height: 100upx;
	}

	/* 转盘 */
	.canvas-container {
		margin: 0 auto;
		position: relative;
		width: 600upx;
		height: 600upx;
		border-radius: 50%;
		box-shadow: 0 10upx 30upx #333, 0 0 10upx #000;
		border: 10rpx solid #A83FDB;
	}

	.canvas-content {
		position: absolute;
		left: 0;
		top: 0;
		z-index: 1;
		display: block;
		width: 600upx;
		height: 600upx;
		border-radius: inherit;
		background-clip: padding-box;
		/* background-color: #ffcb3f; */
	}

	.canvas-element {
		position: relative;
		z-index: 1;
		width: inherit;
		height: inherit;
		border-radius: 50%;
	}

	.canvas-list {
		position: absolute;
		left: 0;
		top: 0;
		width: inherit;
		height: inherit;
		z-index: 9999;
	}

	.canvas-item {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		color: #e4370e;
		font-weight: bold;
		text-shadow: 0 1upx 1upx rgba(255, 255, 255, 0.6);
	}

	.canvas-item-text {
		position: relative;
		display: block;
		padding-top: 20upx;
		margin: 0 auto;
		text-align: center;
		-webkit-transform-origin: 50% 300upx;
		transform-origin: 50% 300upx;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.canvas-item-text text {
		font-size: 30upx;
	}

	.canvas-item-text-img {
		width: 60upx;
		height: 60upx;
		padding-top: 10upx;
	}

	/* 分隔线 */
	.canvas-line {
		position: absolute;
		left: 0;
		top: 0;
		width: inherit;
		height: inherit;
		z-index: 99;
	}

	.canvas-litem {
		position: absolute;
		left: 300upx;
		top: 0;
		width: 3upx;
		height: 300upx;
		background-color: rgba(228, 55, 14, 0.4);
		overflow: hidden;
		-webkit-transform-origin: 50% 300upx;
		transform-origin: 50% 300upx;
	}

	/**  
* 抽奖按钮  
*/
	.canvas-btn {
		position: absolute;
		left: 260upx;
		top: 260upx;
		z-index: 400;
		width: 80upx;
		height: 80upx;
		border-radius: 50%;
		color: #f4e9cc;
		background-color: #e44025;
		line-height: 80upx;
		text-align: center;
		font-size: 26upx;
		text-shadow: 0 -1px 1px rgba(0, 0, 0, 0.6);
		box-shadow: 0 3px 5px rgba(0, 0, 0, 0.6);
		text-decoration: none;
	}

	.canvas-btn::after {
		position: absolute;
		display: block;
		content: ' ';
		left: 12upx;
		top: -44upx;
		width: 0;
		height: 0;
		overflow: hidden;
		border-width: 30upx;
		border-style: solid;
		border-color: transparent;
		border-bottom-color: #e44025;
	}

	.canvas-btn.disabled {
		pointer-events: none;
		background: #b07a7b;
		color: #ccc;
	}

	.canvas-btn.disabled::after {
		border-bottom-color: #b07a7b;
	}

	.canvas-btn-table {
		color: #A83FDB;
		width: 120upx;
		text-align: center;
		position: absolute;
		left: 240upx;
		top: 360upx;
		font-size: 26upx;
		background-color: #FFFFFF;
		opacity: 0.9;
	}
</style>
