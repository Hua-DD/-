<template>
	<div class="seller" ref="seller">
		<div class="seller-content">
			<div class="seller-overview">
				<h1 class="name">{{seller.name}}</h1>
				<div class="desc">
					<star class="star" :size="36" :score="seller.score"></star>
					<span class="rating-count">({{seller.ratingCount}})</span>
					<span class="sell-count">月售{{seller.sellCount}}单</span>
				</div>
				<div class="remark-wrapper">
					<div class="remark">
						<h2 class="title">起送价</h2>
						<div class="num-wrapper">
							<span class="num">{{seller.minPrice}}</span>
							<span class="unit">元</span>
						</div>
					</div>
					<div class="remark">
						<h2 class="title">商家配送</h2>
						<div class="num-wrapper">
							<span class="num">{{seller.deliveryPrice}}</span>
							<span class="unit">元</span>
						</div>
					</div>
					<div class="remark">
						<h2 class="title">平均配送时间</h2>
						<div class="num-wrapper">
							<span class="num">{{seller.deliveryTime}}</span>
							<span class="unit">分钟</span>
						</div>
					</div>
				</div>
				<div class="favorite-wrapper" @click="clickFavorite">
					<span class="icon-favorite" :class="{'on':favorite}"></span>
					<span class="favorite-text">{{favoriteText}}</span>
				</div>
			</div>
			<!--分割线-->
			<split></split>
			<!--公告与活动-->
			<div class="bulletin-wrapper">
				<h1 class="title">公告与活动</h1>
				<div class="text-wrapper">
					<p class="text">{{seller.bulletin}}</p>
				</div>
				<ul v-if="seller.supports" class="supports">
					<li class="supports-item" v-for="item in seller.supports">
						<span class="icon" :class="classMap[item.type]"></span>
						<span class="text">{{item.description}}</span>
					</li>
				</ul>
			</div>
			<!--分割线-->
			<split></split>
			<!--商家实景-->
			<div class="pics">
				<h1 class="title">商家实景</h1>
				<div class="pic-wrapper" ref="pics">
					<ul class="pic-list" ref="ulpics">
						<li class="pic-item" v-for="pic in seller.pics">
							<img width="120" height="90" :src="pic"/>
						</li>
					</ul>
				</div>
			</div>
			<!--分割线-->
			<split></split>
			<!--商家信息-->
			<div class="info-wrapper">
				<h1 class="title">商家信息</h1>
				<ul>
					<li class="info-item" v-for="info in seller.infos">
						{{info}}
					</li>
				</ul>
			</div>
		</div>
		<shopcart :min-price="seller.minPrice" :delivery-price="seller.deliveryPrice"></shopcart>
	</div>
</template>

<script>
	import BScroll from 'better-scroll';
	import shopcart from '../shopcart/shopcart';
	import star from '../star/star';
	import split from '../split/split';
	export default{
		name:'seller',
		data(){
			return {
				favorite:false
			}
		},
		props:{
			seller:{
				type:Object
			}
		},
		created(){
			this.classMap=['decrease','discount','special','invoice','guarantee'];
		},
		components:{
			shopcart,
			star,
			split
		},
		watch:{
			'seller'(){
				this.$nextTick(()=>{
					this.initScroll();
					this.initPicScroll();
				});
			}
		},
		mounted(){
			this.$nextTick(()=>{
				this.initScroll();
				this.initPicScroll();
			});
		},
		methods:{
			clickFavorite(){
				console.log("aa");
				this.favorite=!this.favorite;
				console.log(this.favorite)
			},
			initScroll(){
				if(!this.scroll){
					this.scroll=new BScroll(this.$refs.seller,{
						click:true
					});
				}else{
					this.scroll.refresh();
				}
			},
			initPicScroll(){
				if(this.seller.pics){
					let width=120;
					let margin=6;
					let ulwidth=(width+margin)*this.seller.pics.length-margin;
					this.$refs.ulpics.style.width=ulwidth+"px";
					if(!this.scrollPic){
					this.scrollPic=new BScroll(this.$refs.pics,{
						scrollX:true,
						eventPassthrough:'vertical'
					});
					}else{
						this.scrollPic.refresh();
					}
				}
			}
		},
		computed:{
			favoriteText(){
				return this.favorite?'已收藏':'收藏';
			}
		}
	}
</script>

<style lang="less" scoped="scoped">
	@import '../../common/less/mixin.less';
	.seller{
		position: absolute;
		top: 174px;
		bottom: 46px;
		width: 100%;
		overflow: hidden;
		.seller-content{
			.seller-overview{
				position:relative;
				padding: 18px;
				.name{
					margin-bottom: 8px;
					line-height: 14px;
					font-size: 14px;
					color: rgb(7,17,27);
				}
				.desc{
					padding-bottom: 18px;
					font-size:0;
					.border-1px(rgba(7,17,27,0.1));
					.star{
						display: inline-block;
						vertical-align: top;
						margin-right: 8px;
					}
					.rating-count,.sell-count{
						line-height: 18px;
						font-size: 10px;
						color: rgb(77,85,93);
					}
					.rating-count{
						margin-right: 12px;
					}
				}
				.remark-wrapper{
					display: flex;
					padding-top: 18px;
					text-align: center;
					.remark{
						flex: 1;
						border-right:1px solid rgba(7,17,27,0.1);
						&:last-child{
							border: none;
						}
						.title{
							margin-bottom: 4px;
							line-height: 10px;
							font-size: 10px;
							color: rgb(147,153,159);
						}
						.num-wrapper{
							line-height: 24px;
							font-weight: 200;
							font-size: 0;
							color: rgb(7,17,27);
							.num{
								font-size: 24px;
							}
							.unit{
								font-size: 10px;
							}
						}
					}
					
				}
				.favorite-wrapper{
					position: absolute;
					top: 18px;
					right: 8px;
					text-align: center;
					width: 50px;
					.icon-favorite{
						display: block;
						margin-bottom: 4px;
						line-height: 24px;
						font-size: 24px;
						color: #d4d6d9;
						&.on{
							color: rgb(240,20,20);
						}
					}
					.favorite-text{
						display: block;
						line-height: 10px;
						font-size: 10px;
						color: rgb(77,85,93);
					}
				}
			}
			.bulletin-wrapper{
				padding: 18px 18px 0 18px;
				.title{
					margin-bottom: 8px;
					line-height: 14px;
					font-size: 14px;
					color: rgb(7,17,27);
				}
				.text-wrapper{
					padding: 0 12px 16px 12px;
					.border-1px(rgba(7,17,27,0.1));
					.text{
						line-height: 24px;
						font-size: 12px;
						font-weight: 200;
						color: rgb(240,20,20);
					}
				}
				.supports{
					.supports-item{
						padding: 16px 12px;
						font-size: 0;
						.border-1px(rgba(7,17,27,0.1));
						&:last-child{
							.border-none();
						}
						.icon{
							display: inline-block;
							vertical-align: top;
							margin-right: 6px;
							width: 16px;
							height: 16px;
							background-repeat: no-repeat;
							background-size: 16px 16px;
							&.decrease{
								.bg-img('decrease_4');
							}
							&.discount{
								.bg-img('discount_4');
							}
							&.guarantee{
								.bg-img('guarantee_4');
							}
							&.invoice{
								.bg-img('invoice_4');
							}
							&.special{
								.bg-img('special_4');
							}
						}
						.text{
							line-height: 16px;
							font-size: 12px;
							font-weight: 200;
							color: rgb(7,17,27);
						}
					}
				}
			}
			.pics{
				padding: 18px 0 18px 18px;
				.title{
					margin-bottom: 12px;
					line-height: 14px;
					font-size: 14px;
					color: rgb(7,17,27);
				}
				.pic-wrapper{
					width: 100%;
					overflow: hidden;
					white-space: nowrap;
					.pic-list{
						font-size: 0;
						.pic-item{
							display: inline-block;
							width: 120px;
							height: 90px;
							margin-right: 6px;
							&:last-child{
								margin: 0;
							}
						}
					}
				}
			}
			.info-wrapper{
				padding: 18px 0 18px 18px;
				.title{
					padding-bottom: 12px;
					line-height: 14px;
					font-size: 14px;
					color: rgb(7,17,27);
					.border-1px(rgba(7,17,27,0.1));
				}
				.info-item{
					padding: 16px 12px;
					line-height: 16px;
					font-size: 12px;
					font-weight: 200;
					color: rgb(7,17,27);
					.border-1px(rgba(7,17,27,0.1));
					&:last-child{
						.border-none();
					}
				}
			}
		}
	}
</style>