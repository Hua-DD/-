<template>
	<div class="header">
		<!--内容部分-->
		<div class="content-wrapper">
			<div class="avatar">
				<img :src="seller.avatar" width="64" height="64"/>
			</div>
			<div class="content">
				<div class="title">
					<span class="brand"></span>
					<span class="name">{{seller.name}}</span>
				</div>
				<div class="description">
					{{seller.description}}/{{seller.deliveryTime}}分钟到达
				</div>
				<div v-if="seller.supports" class="supports">
					<span class="icon" :class="classMap[seller.supports[0].type]"></span>
					<span class="text">{{seller.supports[0].description}}</span>
				</div>
			</div>
			<div v-if="seller.supports" class="count" @click="showDatail">
				<span class="num">{{seller.supports.length}}个</span>
				<span class="icon-keyboard_arrow_right"></span>
			</div>
		</div>
		<!--公告部分-->
		<div class="bulletin-wrapper" @click="showDatail">
			<span class="title"></span><span class="bulletin">{{seller.bulletin}}</span>
			<span class="icon-keyboard_arrow_right"></span>
		</div>
		<!--背景-->
		<div class="background">
			<img :src="seller.avatar" width="100%" height="100%"/>
		</div>
		<!--遮罩层-->
		<transition  name="fade">
			<div v-show="detailShow" class="detail">
				<div class="detail-wrapper clearfix">
					<div class="detail-main">
						<h1 class="name">{{seller.name}}</h1>
						<div class="star-warpper">
							<star :size="48" :score="seller.score"></star>
						</div>
						<div class="lines">
							<div class="line"></div>
							<div class="text">优惠信息</div>
							<div class="line"></div>
						</div>
						<ul v-if="seller.supports" class="supports">
							<li class="supports-item" v-for="item in seller.supports">
								<span class="icon" :class="classMap[item.type]"></span>
								<span class="text">{{item.description}}</span>
							</li>
						</ul>
						<div class="lines">
							<div class="line"></div>
							<div class="text">商家公告</div>
							<div class="line"></div>
						</div>
						<div class="bulletins">
							<p class="bul">{{seller.bulletin}}</p>
						</div>
					</div>
				</div>
				<div class="detail-close" @click="closeDetail">
					<span class="icon-close"></span>
				</div>
			</div>
		</transition>
	</div>
</template>

<script>
	import star from '../star/star.vue';
	export default{
		name:'mheader',
		props:{
			seller:{
				type:Object
			}
		},
		data(){
			return {
				detailShow:false
			}
		},
		created(){
			this.classMap=['decrease','discount','special','invoice','guarantee'];
		},
		methods:{
			showDatail(){
				this.detailShow=true;
			},
			closeDetail(){
				this.detailShow=false;
			}
		},
		components:{
			star
		}
	}
</script>

<style lang="less" scoped="scoped">
@import '../../common/less/mixin.less';
	.header{
		position:relative;
		background:rgba(7,17,27,0.5);
		color:#fff;
		overflow:hidden;
		.content-wrapper{
			position:relative;
			padding: 24px 12px 18px 24px;
			font-size:0;
			
			.avatar{
				display: inline-block;
				vertical-align: top;
				img{
					border-radius: 2px;
				}
			}
			.content{
				display: inline-block;
				margin-left: 16px;
				.title{
					padding:2px 0 8px 0;
					.brand{
						display: inline-block;
						vertical-align: top;
						width: 30px;
						height: 18px;
						/*background-image: url(brand@2x.png);*/
						.bg-img('brand');
						background-repeat: no-repeat;
						background-size: 30px 18px;
					}
					.name{
						font-size: 16px;
						font-weight: bold;
						line-height: 18px;
						margin-left: 6px;
					}
				}
				.description{
					font-size: 12px;
					font-weight: 200;
					line-height: 12px;
					padding-bottom: 10px;
				}
				.supports{
					padding-bottom: 2px;
					.icon{
						display: inline-block;
						vertical-align: top;
						width: 12px;
						height: 12px;
						/*background-image: url(decrease_1@2x.png);*/
						background-repeat: no-repeat;
						background-size: 12px 12px;
						&.decrease{
							.bg-img('decrease_1');
						}
						&.discount{
							.bg-img('discount_1');
						}
						&.guarantee{
							.bg-img('guarantee_1');
						}
						&.invoice{
							.bg-img('invoice_1');
						}
						&.special{
							.bg-img('special_1');
						}
					}
					.text{
						font-size: 10px;
						font-weight: 200;
						line-height: 12px;
						margin-left: 4px;
					}
				}
			}
			.count{
				position: absolute;
				bottom: 18px;
				right: 12px;
				height: 24px;
				line-height: 24px;
				padding: 0 8px;
				font-size: 0;
				font-weight: 200;
				border-radius: 7px;
				background-color: rgba(0,0,0,0.2);
				.num{
					font-size: 10px;
				}
				.icon-keyboard_arrow_right{
					font-size: 10px;
					margin-left: 2px;
				}
			}
		}
		.bulletin-wrapper{
			position: relative;
			height: 28px;
			line-height: 28px;
			padding: 0 21px 0 12px;
			background: rgba(7,17,27,0.2);
			white-space: nowrap;
			overflow: hidden;
			text-overflow: ellipsis;
			font-weight: 200;
			.title{
				display: inline-block;
				vertical-align: top;
				width: 22px;
				height: 12px;
				.bg-img('bulletin');
				background-repeat: no-repeat;
				background-size: 22px 12px;
				margin-top: 8px;
				font-size: 10px;
			}
			.bulletin{
				vertical-align: top;
				font-size: 10px;
				margin-left: 4px;
			}
			.icon-keyboard_arrow_right{
				position: absolute;
				top:9px;
				right: 12px;
				font-size: 10px;
			}
		}
		.background{
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			z-index: -1;
			filter: blur(10px);
		}
		.fade-enter,.fade-leave-to{
			opacity: 0;
		}
		.fade-enter-active,.fade-leave-active{
			transition: opacity 1s; 
		}
		.fade-enter-to,.fade-leave{
			opacity: 1;
		}
		.detail{
			position: fixed;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			background: rgba(7,17,27,0.8);
			z-index: 100;
			overflow: auto;
			.detail-wrapper{
				width: 100%;
				min-height:100%;
				overflow:auto;
				.detail-main{
					margin-top: 64px;
					padding-bottom: 64px;
					.name{
						text-align: center;
						font-size: 16px;
						line-height: 16px;
						font-weight: 700;
					}
					.star-warpper{
						margin-top: 16px;
						text-align: center;
					}
					.lines{
						display: flex;
						width: 80%;
						margin: 28px auto 24px auto;
						.line{
							flex: 1;
							border-bottom: 1px solid rgba(255,255,255,0.2);
							margin-top: 6px;
							height: 1px;
						}
						.text{
							font-size: 14px;
							line-height: 14px;
							font-weight: 700;
							padding: 0 12px;
						}
					}
					.supports{
						width: 80%;
						margin: 0 auto;
						padding: 0 12px;
						.supports-item{
							font-size:0;
							margin-bottom: 12px;
							&:last-chlid{
								margin-bottom: 0;
							}					
							.icon{
								display: inline-block;
								vertical-align: top;
								width: 16px;
								height: 16px;
								background-repeat: no-repeat;
								background-size: 16px 16px;
								&.decrease{
									.bg-img('decrease_2');
								}
								&.discount{
									.bg-img('discount_2');
								}
								&.guarantee{
									.bg-img('guarantee_2');
								}
								&.invoice{
									.bg-img('invoice_2');
								}
								&.special{
									.bg-img('special_2');
								}
							}
							.text{
								font-size: 12px;
								line-height: 16px;
								font-weight: 200;
								margin-left: 6px;
							}
						}
					}
					.bulletins{
						width: 80%;
						margin: 0 auto;
						padding: 0 12px;
						.bul{
							font-size: 12px;
							line-height: 24px;
							font-weight: 200;
						}
					}
				}
			}
			.detail-close{
				position: relative;
				width: 32px;
				height: 32px;
				margin: -64px auto 0 auto;
				clear: both;
				color: rgba(255,255,255,0.5);
			}
		}
	}
</style>