<template>
	<div class="cartcontrol">
		<div v-show="food.count>0" class="des icon-remove_circle_outline" @click.stop="desFood">
			
		</div>
		<div v-show="food.count>0" class="count">
			{{food.count}}
		</div>
		<div class="add icon-add_circle" @click.stop="addFood">
			
		</div>
	</div>
</template>

<script>
	import Vue from 'vue';
	export default{
		name:'cartcontrol',
		props:{
			food:{
				type:Object
			}
		},
		methods:{
			addFood(){
				console.log("aaa");
				if(!this.food.count){
//					this.food.count=1;
					Vue.set(this.food,'count',1);
				}else{
					this.food.count++;
				}
			},
			desFood(){
				if(this.food.count){
					this.food.count--;
				}
				
			}
		}
	}
</script>

<style lang="less" scoped="scoped">
	.cartcontrol{
		font-size: 0;
		.des,.add{
			display: inline-block;
			vertical-align: top;
			font-size: 24px;
			line-height: 24px;
			padding: 6px;
			color: rgb(0,160,220);
		}
		.count{
			display: inline-block;
			font-size: 10px;
			line-height: 24px;
			margin-top: 6px;
			color: rgb(147,153,159);
			padding: 0 12px;
		}
		.add{
		}
	}
</style>