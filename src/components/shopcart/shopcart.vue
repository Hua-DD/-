<template>
	<div>
		<div class="shopcart">
			<div class="content" @click="toggleList">
				<div class="content-left">
					<div class="logo-wrapper">
						<div class="logo" :class="{'hightLight':totalCount>0}">
							<span class="icon-shopping_cart" :class="{'hightLight':totalCount>0}"></span>
						</div>
						<div v-show="totalCount>0" class="num">
							{{totalCount}}
						</div>
					</div>
					<div class="total-price" :class="{'hightLight':totalPrice>0}">
						￥{{totalPrice}}
					</div>
					<div class="description">
						另需配送费￥{{deliveryPrice}}元
					</div>
				</div>
				<div class="content-right" :class="{'hightLight':totalPrice>=minPrice}" @click.stop="goToPay">
					{{pay}}
				</div>
			</div>
			<!--购物车详情列表-->
			<transition name='slid'>
				<div class="cart-list" v-show="listShow">
				<div class="list-header">
					<h1 class="title">购物车</h1>
					<span class="clear-empty" @click="clearEmpty">清空</span>
				</div>
				<div class="cart-content" ref="cartContentList">
					<ul >
						<li class="food" v-for="food in selectedFoods">
							<span class="name">{{food.name}}</span>
							<div class="price">
								<span>￥{{food.price * food.count}}</span>
							</div>
							<div class="cartcontrol-wrapper">
								<cartcontrol :food="food"></cartcontrol>
							</div>
						</li>
					</ul>
				</div>
			</div>
			</transition>
		</div>
		<transition name="fade">
			<div class="mask" v-show="listShow" @click="hiddenMask"></div>
		</transition>
	</div>
</template>

<script>
	import BScroll from 'better-scroll';
	import cartcontrol from '../cartcontrol/cartcontrol';
	export default{
		name:'shopcart',
		data(){
			return {
				fold:true
			}
		},
		props:{
			deliveryPrice:{
				type:Number
			},
			minPrice:{
				type:Number
			},
			selectedFoods:{
				type:Array,
				default(){
					return [];
				}
			}
		},
		computed:{
			totalPrice(){
				let total=0;
				this.selectedFoods.forEach((food)=>{
					total+=food.price*food.count;
				})
				return total;
			},
			totalCount(){
				let total=0;
				this.selectedFoods.forEach((food)=>{
					total+=food.count;
				})
				return total;
			},
			pay(){
				if(this.totalPrice===0){
					return `￥${this.minPrice}起送`;
				}else if(this.totalPrice<this.minPrice){
					let diff=this.minPrice-this.totalPrice;
					return `还差￥${diff}起送`;
				}else{
					return '去结算';
				}
			},
			listShow(){
				if(this.totalCount<=0){
					this.fold=true;
					return false;
				}
				let show=!this.fold;
				if(show){
					this.$nextTick(()=>{
						if(!this.scroll){
							this.scroll=new BScroll(this.$refs.cartContentList,{
								click:true
							});
						}else{
							this.scroll.refresh();
						}
					});
					
					
				}
				return show;
				
			}
		},
		components:{
			cartcontrol
		},
		methods:{
			toggleList(){
				this.fold=!this.fold;
			},
			clearEmpty(){
				this.selectedFoods.forEach((food)=>{
					food.count=0;
				})
			},
			goToPay(){
				if(this.totalPrice<this.minPrice){
					return ;
				}
				alert(`需支付${this.totalPrice}元`);
			},
			hiddenMask(){
				this.fold=true;
			}
		}
	}
</script>

<style lang="less" scoped="scoped">
	@import '../../common/less/mixin.less';
	.shopcart{
		position: fixed;
		left: 0;
		bottom: 0;
		width: 100%;
		height: 46px;
		z-index: 50;
		.content{
			display: flex;
			background: #141d27;
			.content-left{
				flex: 1;
				.logo-wrapper{
					display: inline-block;
					position: relative;
					top: -12px;
					margin: 0 12px;
					padding: 6px;
					border-radius: 50%;
					background: #141d27;
					.logo{
						width: 45px;
						height: 45px;
						text-align: center;
						line-height: 54px;
						background: #2b343c;
						border-radius: 50%;
						&.hightLight{
							background: rgb(0,160,220);
						}
						.icon-shopping_cart{
							font-size: 24px;
							color: rgba(255,255,255,0.4);
							&.hightLight{
								color: #fff;
							}
						}
					}
					.num{
						position: absolute;
						top: 0;
						right: 0;
						width: 24px;
						background: rgb(240,20,20);
						box-shadow: 0px 4px 8px 0px rgba(0,0,0,0.4);
						font-size: 9px;
						line-height: 16px;
						font-weight: 700;
						color: #fff;
						text-align: center;
						border-radius: 6px;
					}
				}
				.total-price{
					display: inline-block;
					vertical-align: top;
					margin:8px 0;
					font-size: 16px;
					color: rgba(255,255,255,0.4);
					font-weight: 700;
					border-right: 1px solid rgba(255,255,255,0.1);
					line-height: 30px;
					padding-right: 12px;
					&.hightLight{
						color: #fff;
					}
					
				}
				.description{
					display: inline-block;
					vertical-align: top;
					line-height: 46px;
					padding: 0 12px;
					font-size: 10px;
					color: rgba(255,255,255,0.4);
				}
			}
			.content-right{
				flex: 0 0 105px;
				width: 105px;
				padding: 0 8px;
				line-height: 46px;
				font-size: 12px;
				color: rgba(255,255,255,0.4);
				font-weight: 700;
				background: #2b333b;
				text-align: center;
				&.hightLight{
					background: #00b43c;
					color: #fff;
				}
			}
		}
		.cart-list{
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			overflow: hidden;
			z-index: -1;
			transform: translate3d(0,-100%,0);
			&.slid-enter,&.slid-leave-to{
				transform: translate3d(0,0,0);
			}
			&.slid-enter-active,&.slid-leave-active{
				transition: all 1s;
			}
			&.slid-enter-to,&.slid-leave{
				transform: translate3d(0,-100%,0);
			}
			.list-header{
				height: 40px;
				padding: 0 18px;
				background: #F3F5F7;
				line-height:40px;
				border-bottom:1px solid rgba(7,17,27,0.1);
				.title{
					float: left;
					font-size: 14px;
					font-weight: 200;
					color: rgb(7,17,27);
				}
				.clear-empty{
					float: right;
					font-size: 12px;
					color: rgb(0,160,220);
				}
			}
			.cart-content{
				background: #FFFFFF;
				padding: 0 18px;
				max-height: 219px;
				overflow: hidden;
				.food{
					position: relative;
					padding: 12px 0;
					.border-1px(rgba(7,17,27,0.1));
					.name{
						font-size: 14px;
						color: rgb(7,17,27);
						line-height: 24px;
					}
					.price{
						position: absolute;
						right: 112px;
						bottom: 12px;
						font-size: 14px;
						font-weight: 700;
						color: rgb(240,20,20);
						line-height: 24px;
					}
					.cartcontrol-wrapper{
						position: absolute;
						right: 0px;
						bottom: 6px;
					}
				}
			}
		}
		
	}
	.mask{
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 30;
		background: rgba(7,17,27,0.6);
		&.fade-enter,&.fade-leave-to{
			opacity: 0;
		}
		&.fade-enter-active,&.fade-leave-active{
			transition: opacity 1s;
		}
		&.fade-enter-to,&.fade-leave{
			opacity: 1;
		}
	}
</style>