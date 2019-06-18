<template>
	<div class="goods">
		<!--左边的菜单栏-->
		<div class="menu_wrapper" ref='menuWrapper'>
			<ul>
				<li class="menu-list" v-for='(item,index) in goods' :class="{'current':index==currentIndex}" @click="clickMenu(index)">
					
					<span class="meun" :class="{'current':index==currentIndex}">
						<span v-if="item.type>0" class="icon" :class="classMap[item.type]"></span>
						{{item.name}}
					</span>
				</li>
			</ul>
		</div>
		<!--右边的商品列表-->
		<div class="goods-wrapper" ref='goodsWrapper'>
			<ul>
				<li class="goods-list" v-for='item in goods' ref="goodsList">
					<h1 class="name">{{item.name}}</h1>
					<ul>
						<li @click="showFoodDetail(food)" class="foods-list" v-for="food in item.foods">
							<div class="foodimg">
								<img width="57" height="57" :src="food.icon"/>
							</div>
							<div class="content">
								<h2 class="food-name">{{food.name}}</h2>
								<p class="description">{{food.description}}</p>
								<div class="extra">
									<span class="sell-count">月售{{food.sellCount}}份</span>
									<span class="rating">好评率{{food.rating}}%</span>
								</div>
								<div class="price">
									<span class="now">￥{{food.price}}</span>
									<span v-if='food.oldPrice' class="oldprice">￥{{food.oldPrice}}</span>
								</div>
								<!--加减号-->
								<div class="cartcontrol-wrapper">
									<cartcontrol :food="food"></cartcontrol>
								</div>
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</div>
		<!--购物车组件-->
		<shopcart :selected-foods="selectFood" :min-price="seller.minPrice" :delivery-price="seller.deliveryPrice"></shopcart>
		<!--商品详情页-->
		<food ref="foodDetail" :food="food"></food>
	</div>
</template>

<script>
	import BScroll from 'better-scroll';
	import shopcart from '../shopcart/shopcart';
	import cartcontrol from '../cartcontrol/cartcontrol';
	import food from '../food/food';
	const ERR_OK=0;
	export default {
		name:'goods',
		props:{
			seller:{
				type:Object
			}
		},
		data(){
			return {
				goods:[],
				heightList:[],
				scrollY:0,
				food:{}
			}
		},
		created(){
			this.classMap=['decrease','discount','special','invoice','guarantee'];
			this.$http.get('/api/goods').then((res)=>{
				res=res.body;
				if(res.errno==ERR_OK){
					this.goods=res.data;
					this.$nextTick(()=>{
						this.initBScroll();
						this.calcHeight();
					});
					
				}
			})
		},
		methods:{
			initBScroll(){
				this.menuScroll=new BScroll(this.$refs.menuWrapper,{
					click:true
				});
				this.goodsScroll=new BScroll(this.$refs.goodsWrapper,{
					click:true,
					probeType:3
				});
				this.goodsScroll.on('scroll',(position)=>{
//					console.log(position);
					this.scrollY=Math.abs(Math.round(position.y));
//					console.log(this.scrollY);
				});
			},
			calcHeight(){
				let goodsList=this.$refs.goodsList;
				let height=0;
				this.heightList.push(height);
				for(let i=0;i<goodsList.length;i++){
					let temp=goodsList[i];
					height+=temp.clientHeight;
					this.heightList.push(height);
				}
//				console.log(this.heightList);
			},
			clickMenu(index){
//				this.goodsScroll.scrollToElement(dom元素,滚动时长)
				let goodsList=this.$refs.goodsList;
				this.goodsScroll.scrollToElement(goodsList[index],300);
			},
			showFoodDetail(food){
				this.$refs.foodDetail.show();
				this.food=food;
			}
			
		},
		computed:{
			currentIndex(){
				for(let i=0;i<this.heightList.length;i++){
					let height1=this.heightList[i];
					let height2=this.heightList[i+1];
					if(!height2 ||(this.scrollY>=height1 && this.scrollY<height2)){
						return i;
					}
				}
				return 0;
			},
			selectFood(){
				let result=[];
				this.goods.forEach((good)=>{
					good.foods.forEach((food)=>{
						if(food.count){
							result.push(food);
						}
					});
				});
				return result;
			}
		},
		components:{
			shopcart,
			cartcontrol,
			food
		}
	}
</script>

<style lang="less" scoped="scoped">
@import '../../common/less/mixin.less';
	.goods{
		display: flex;
		position: absolute;
		top: 174px;
		bottom: 46px;
		width: 100%;
		overflow: hidden;
		.menu_wrapper{
			flex: 0 0 80px;
			width: 80px;
			background: #f3f5f7;
			.menu-list{
				display: table;
				width: 56px;
				height: 54px;
				padding: 0 12px;
				/*border-bottom:1px solid rgba(7,17,27,0.1);*/
				&.current{
					position:relative;
					top:-1px;
					font-weight: 700;
					background: #fff;
					.border-none();
				}
				&:last-child{
					.border-none();
				}
				.icon{
					display: inline-block;
					vertical-align: middle;
						width: 12px;
						height: 12px;
						/*background-image: url(decrease_1@2x.png);*/
						background-repeat: no-repeat;
						background-size: 12px 12px;
						&.decrease{
							.bg-img('decrease_3');
						}
						&.discount{
							.bg-img('discount_3');
						}
						&.guarantee{
							.bg-img('guarantee_3');
						}
						&.invoice{
							.bg-img('invoice_3');
						}
						&.special{
							.bg-img('special_3');
						}
				}
				.meun{
					display: table-cell;
					vertical-align: middle;
					font-size: 12px;
					font-weight: 200;
					line-height: 14px;
					.border-1px(rgba(7,17,27,0.1));
					&.current{
						.border-none();
					}
				}
			}
		}
		.goods-wrapper{
			flex: 1;
			.goods-list{
				.name{
					height: 26px;
					line-height: 26px;
					border-left: 2px solid #d9dde1;
					font-size: 12px;
					color: rgb(147,153,159);
					background: #f3f5f7;
					padding-left: 14px;
				}
				.foods-list{
					display: flex;
					margin: 18px;
					padding-bottom: 18px;
					.border-1px(rgba(7,17,27,0.1));
					&:last-child{
						.border-none();
						margin-bottom: 0;
					}
					.foodimg{
						flex: 0 0 57px;
						width: 57px;
						height: 57px;
						margin-right: 10px;
					}
					.content{
						flex: 1;
						.food-name{
							padding-top: 2px;
							font-size: 14px;
							line-height: 14px;
							color: rgb(7,17,27);
						}
						.description{
							padding: 8px 0;
							font-size: 10px;
							line-height: 12px;
							color: rgb(147,153,159);
						}
						.extra{
							font-size: 0;
							.sell-count,.rating{
								font-size: 10px;
								line-height: 10px;
								color: rgb(147,153,159);
							}
							.rating{
								margin-left: 12px;
							}
						}
						.price{
							font-size: 0;
							.now{
								font-size: 14px;
								line-height: 24px;
								font-weight: 700;
								color: rgb(240,20,20);
							}
							.oldprice{
								font-size: 10px;
								line-height: 24px;
								font-weight: normal;
								color: rgb(147,153,159);
								margin-left: 8px;
								text-decoration: line-through;
							}
						}
						.cartcontrol-wrapper{
							position: absolute;
							right: 0;
							bottom: 8px;
						}
					}
				}
			}
		}
	}
</style>