<template>
	<transition name="move">
		<div class="food" v-show="showFlag" ref="foodDetail">
			<div class="food-content">
				<!--头部图片-->
				<div class="food-image">
					<img :src="food.image"/>
					<div class="back" @click="back">
						<span class="icon-arrow_lift"></span>
					</div>
				</div>
				<!--商品的描述-->
				<div class="content">
					<h1 class="name">{{food.name}}</h1>
					<div class="description">
						<span class="sell-count">月售{{food.sellCount}}份</span>
						<span class="rating">好评率{{food.rating}}%</span>
					</div>
					<div class="price">
						<span class="now">￥{{food.price}}</span>
						<span v-show="food.oldPrice" class="oldprice">￥{{food.oldPrice}}</span>
					</div>
					<div @click.stop="addFrist" class="add-cart" v-show="!food.count || food.count===0">
						加入购物车
					</div>
					<div class="cartcontrol-wrapper">
						<cartcontrol :food="food"></cartcontrol>
					</div>
				</div>
				<!--分割线-->
				<split></split>
				<!--商品介绍-->
				<div v-show="food.info" class="info">
					<h1 class="title">商品介绍</h1>
					<p class="text">{{food.info}}</p>
				</div>
				<!--分割线-->
				<split></split>
				<!--商品评价-->
				<div class="rating">
					<h1 class="title">商品评价</h1>
					<ratingselect 
						@select="select"
						@toggle="toggle"
						:ratings="food.ratings" 
						:desc="desc"
						:select-type="selectType"
						:only-content="onlyContent"
						>
					</ratingselect>
					<div class="rating-list">
						<ul>
							<li v-show="needShow(rating.rateType,rating.text)" class="rating-item" v-for="rating in food.ratings">
								<div class="time">
									{{rating.rateTime | formatDate}}
								</div>
								<div class="rating-content">
									<span class="icon" :class="{'icon-thumb_down':rating.rateType===1,'icon-thumb_up':rating.rateType===0}"></span>
									{{rating.text}}
								</div>
								<div class="user">
									<span class="username">{{rating.username}}</span>
									<div class="avatar">
										<img width="12" height="12" :src="rating.avatar"/>
									</div>
								</div>
							</li>
						</ul>
					</div>
					<div class="no-rating" v-show="!food.ratings || food.ratings.length===0 ">
						暂无评价
					</div>
				</div>
			</div>
		</div>
	</transition>
</template>

<script>
	import Vue from 'vue';
	import BScroll from 'better-scroll';
	import cartcontrol from '../cartcontrol/cartcontrol';
	import split from '../split/split';
	import ratingselect from '../ratingselect/ratingselect';
	const POSITIVT=0;
	const NEGATIVE=1;
	const ALL=2;
	export default{
		name:"food",
		props:{
			food:{
				type:Object
			}
		},
		data(){
			return {
				showFlag:false,
				desc:{
					all:"全部",
					positive:'推荐',
					negative:"吐槽"
				},
				selectType:ALL,
				onlyContent:false
			}
		},
		filters:{
			formatDate(strDate){
				let date=new Date(strDate);
				let year=date.getFullYear();
				let mouth=date.getMonth()+1;
				let day=date.getDate();
				let hour=date.getHours();
				let minute=date.getMinutes();
//				let seconds=date.getSeconds();
				mouth=mouth<10?"0"+mouth:mouth;
				day=day<10?"0"+day:day;
				hour=hour<10?"0"+hour:hour;
				minute=minute<10?"0"+minute:minute;
				return year+"-"+mouth+"-"+day+" "+hour+":"+minute;
			}
		},
		methods:{
			show(){
				this.showFlag=true;
				this.selectType=ALL;
				this.onlyContent=false;
				this.$nextTick(()=>{
					if(!this.scroll){
						this.scroll=new BScroll(this.$refs.foodDetail,{
							click:true
						});
					}else{
						this.scroll.refresh();
					}
				});
			},
			back(){
				this.showFlag=false;
			},
			addFrist(){
				Vue.set(this.food,'count',1);
			},
			select(a){
				this.selectType=a;
				this.$nextTick(()=>{
					this.scroll.refresh();
				});
			},
			toggle(){
				this.onlyContent=!this.onlyContent;
				this.$nextTick(()=>{
					this.scroll.refresh();
				});
			},
			needShow(type,text){
				if(this.onlyContent && !text){
					return false;
				}
				if(this.selectType===ALL){
					return true;
				}else{
					return this.selectType===type;
				}
				return ALL;
			}
		},
		components:{
			cartcontrol,
			split,
			ratingselect
		}
	}
</script>

<style lang="less" scoped="scoped">
	@import '../../common/less/mixin.less';
	.food{
		position: fixed;
		top: 0;
		bottom: 46px;
		width: 100%;
		height: 100%;
		background: #FFFFFF;
		&.move-enter,&.move-leave-to{
			transform: translate3d(100%,0,0);
		}
		&.move-enter-active,&.move-leave-active{
			transition: all 0.2s linear;
		}
		&.move-enter-to,&.move-leave{
			transform: translate3d(0,0,0);
		}
		.food-content{
			.food-image{
				position: relative;
				width: 100%;
				height: 0;
				padding-top: 100%;
				img{
					position: absolute;
					top: 0;
					left: 0;
					width: 100%;
					height: 100%;
				}
				.back{
					position: absolute;
					top: 10px;
					left: 0;
					padding: 10px;
					color: #FFFFFF;
					.icon-arrow_lift{
						font-size: 20px;
					}
				}
			}
			.content{
				position: relative;
				padding: 18px;
				.name{
					line-height: 14px;
					font-size: 14px;
					font-weight: 700;
					color: rgb(7,17,27);
				}
				.description{
					margin-top: 8px;
					font-size: 0;
					.sell-count,.rating{
						line-height: 10px;
						font-size: 10px;
						color: rgb(147,153,159);
					}
					.sell-count{
						margin-right: 12px;
					}
				}
				.price{
					margin-top: 18px;
					font-size: 0;
					.now{
						line-height: 24px;
						font-size: 14px;
						font-weight: 700;
						color: rgb(240,20,20);
					}
					.oldprice{
						line-height: 24px;
						font-size: 10px;
						font-weight: normal;
						color: rgb(147,153,159);
						margin-left: 8px;
					}
				}
				.cartcontrol-wrapper{
					position: absolute;
					right: 12px;
					bottom: 12px;
				}
				.add-cart{
					position: absolute;
					right: 18px;
					bottom: 18px;
					height: 24px;
					line-height: 24px;
					padding: 0 12px;
					border-radius: 12px;
					z-index: 10;
					font-size: 10px;
					background: rgb(0,160,220);
					color: #FFFFFF;
					
				}
			}
			.info{
				padding: 18px;
				.title{
					line-height: 14px;
					font-size: 14px;
					font-weight: 700;
					color: rgb(7,17,27);
				}
				.text{
					padding: 6px 8px 0 8px;
					line-height: 24px;
					font-size: 12px;
					font-weight: 200;
					color: rgb(77,85,93);
				}
			}
			.rating{
				padding-top: 18px;
				.title{
					line-height: 14px;
					margin-left: 18px;
					font-size: 14px;
					font-weight: 700;
					color: rgb(7,17,27);
					
				}
				.rating-list{
					position: relative;
					padding: 0 18px;
					.rating-item{
						padding: 16px 0;
						.border-1px(rgba(7,17,27,0.1));
						.time{
							margin-bottom: 6px;
							line-height: 12px;
							font-size: 10px;
							color: rgb(147,153,159);
						}
						.rating-content{
							line-height: 16px;
							font-size: 12px;
							color: rgb(7,17,27);
							.icon{
								line-height: 24px;
								font-size: 12px;
							}
							.icon-thumb_down{
								color: rgb(147,153,159);
							}
							.icon-thumb_up{
								color: rgb(0,160,220);
							}
						}
						.user{
							position: absolute;
							top: 16px;
							right: 0;
							font-size: 0;
							.username{
								line-height: 12px;
								font-size: 10px;
								color: rgb(147,153,159);
							}
							.avatar{
								display: inline-block;
								vertical-align: top;
								width: 12px;
								height: 12px;
								border-radius: 50%;
								img{
									border-radius: 50%;
									margin-left: 6px;
								}
							}
						}
						
					}
				}
				.no-rating{
					padding: 18px;
					font-size: 12px;
					color: rgb(147,153,159);
				}
			}
		}
	}
</style>