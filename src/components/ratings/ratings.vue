<template>
	<div class="ratings" ref="ratings">
		<div class="rating-content">
			<div class="rating-overview">
				<div class="overview-left">
					<h1 class="score">{{seller.score}}</h1>
					<h2 class="title">综合评分</h2>
					<div class="rank">
						高于周边商家{{seller.rankRate}}%
					</div>
				</div>
				<div class="overview-right">
					<div class="score-wrapper">
						<span class="title">服务态度</span>
						<star class="star" :size="36" :score="seller.serviceScore"></star>
						<span class="score">{{seller.serviceScore}}</span>
					</div>
					<div class="score-wrapper">
						<span class="title">商品评分</span>
						<star :size="36" :score="seller.foodScore"></star>
						<span class="score">{{seller.foodScore}}</span>
					</div>
					<div class="delivery-wrapper">
						<span class="title">送达时间</span>
						<span class="delivery">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
			</div>
			<!--分割线-->
			<split></split>
			<ratingselect 
					@select="select"
					@toggle="toggle"
					:ratings="ratings" 
					:select-type="selectType"
					:only-content="onlyContent"
					>
			</ratingselect>
			<div class="rating-list">
				<ul>
					<li v-show="needShow(rating.rateType,rating.text)" class="rating-item" v-for="rating in ratings">
						<div class="avatar">
							<img width="28" height="28" :src="rating.avatar"/>
						</div>
						<div class="rating-content">
							<div class="username">{{rating.username}}</div>
							<div class="desc">
								<star :size="24" :score="rating.score"></star>
								<span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
							</div>
							<p class="text">{{rating.text}}</p>
							<div v-show="rating.recommend" class="recommend-wrapper">
								<span :class="{'icon-thumb_down':rating.rateType===1,'icon-thumb_up':rating.rateType===0}"></span>
								<span class="recommend" v-for="item in rating.recommend">{{item}}</span>
							</div>
							<div class="time">
								{{rating.rateTime | formatDate}}
							</div>
						</div>
						
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
	import split from '../split/split';
	import star from '../star/star';
	import ratingselect from '../ratingselect/ratingselect';
	const ERR_OK=0;
	const POSITIVT=0;
	const NEGATIVE=1;
	const ALL=2;
	export default{
		name:'ratings',
		data(){
			return {
				ratings:[],
				selectType:ALL,
				onlyContent:false
			}
		},
		props:{
			seller:{
				type:Object
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
		components:{
			shopcart,
			star,
			split,
			ratingselect
		},
		methods:{
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
		created(){
			this.$http.get("/api/ratings").then((res)=>{
				res=res.body;
				if(res.errno==ERR_OK){
					this.ratings=res.data;
					this.$nextTick(()=>{
						if(!this.scroll){
							this.scroll=new BScroll(this.$refs.ratings,{
								click:true
							})
						}else{
							this.scroll.refresh();
						}
					});
				}
			});
		}
	}
</script>

<style lang="less" scoped="scoped">
	@import '../../common/less/mixin.less';
	.ratings{
		position: absolute;
		top: 174px;
		bottom: 46px;
		width: 100%;
		overflow: hidden;
		.rating-content{
			.rating-overview{
				display: flex;
				padding: 18px 0;
				.overview-left{
					flex: 0 0 137px;
					width: 137px;
					padding: 6px 0;
					text-align: center;
					border-right:1px solid rgba(7,17,27,0.1);
					@media only screen and (max-width:320px){
						width: 110px;
						flex: 0 0 110px;
					}
					.score{
						margin-bottom: 6px;
						line-height: 28px;
						font-size: 24px;
						color: rgb(255,153,0);
					}
					.title{
						margin-bottom: 8px;
						line-height: 12px;
						font-size: 12px;
						color: rgb(7,17,27);
					}
					.rank{
						line-height: 10px;
						font-size: 10px;
						color: rgb(147,153,159);
					}
				}
				.overview-right{
					flex: 1;
					padding: 6px 24px;
					@media only screen and (max-width:320px){
						padding: 6px 0px 6px 6px;
					}
					.score-wrapper{
						margin-bottom: 8px;
						font-size: 0;
						.title{
							margin-right: 12px;
							line-height: 18px;
							font-size: 12px;
							color: rgb(7,17,27);
						}
						.star{
							display: inline-block;
							vertical-align: top;
						}
						.score{
							line-height: 18px;
							font-size: 12px;
							color: rgb(255,153,0);
						}
					}
					.delivery-wrapper{
						font-size: 0;
						.title{
							margin-right: 12px;
							line-height: 18px;
							font-size: 12px;
							color: rgb(7,17,27);
						}
						.delivery{
							line-height: 18px;
							font-size: 12px;
							color: rgb(147,153,159);
						}
					}
				}
			}
			.rating-list{
				padding: 0 18px;
				.rating-item{
					display:flex;
					padding:18px 0;
					.border-1px(rgba(7,17,27,0.1));
					.avatar{
						flex: 0 0 28px;
						width: 28px;
						margin-right: 12px;
						img{
							border-radius: 50%;
						}
					}
					.rating-content{
						flex: 1;
						.username{
							margin-bottom: 4px;
							line-height: 12px;
							font-size: 10px;
							color: rgb(7,17,27);
						}
						.desc{
							margin-bottom: 6px;
							font-size: 0;
							.star{
								display: inline-block;
								vertical-align: top;
							}
							.delivery{
								margin-left: 6px;
								line-height: 12px;
								font-size: 10px;
								font-weight: 200;
								color: rgb(147,153,159);
							}
						}
						.text{
							margin-bottom: 8px;
							line-height: 18px;
							font-size: 12px;
							color: rgb(7,17,27);
						}
						.recommend-wrapper{
							.icon-thumb_down,.icon-thumb_up{
								margin-right: 8px;
								line-height: 16px;
								font-size: 12px;
							}
							.icon-thumb_down{
								color: rgb(147,153,159);
							}
							.icon-thumb_up{
								color: rgb(0,160,220);
							}
							.recommend{
								margin-right: 8px;
								line-height: 16px;
								border-radius: 1px;
								padding: 0 6px;
								border: 1px solid rgba(7,17,27,0.1);
								background: #FFFFFF;
								font-size: 9px;
								color: rgb(147,153,159);
							}
						}
						.time{
							position: absolute;
							right: 0;
							top: 18px;
							line-height: 12px;
							font-size: 10px;
							font-weight: 200;
							color: rgb(147,153,159);
						}
					}
				}
			}
		}
		
	}
</style>