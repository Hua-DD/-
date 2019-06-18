<template>
	<div class="ratingselect">
		<div class="rating-type">
			<span @click="clickType(2)" class="block positive" :class="{'active':selectType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
			<span @click="clickType(0)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span class="count">{{positiveArray.length}}</span></span>
			<span @click="clickType(1)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span class="count">{{negativeArray.length}}</span></span>
		</div>
		<div @click="onlySeeContent" class="only-see-content">
			<span class="icon-check_circle" :class="{'on':onlyContent}"></span>
			<span class="text">只看有内容的评价</span>
		</div>
	</div>
</template>

<script>
	const POSITIVT=0;
	const NEGATIVE=1;
	const ALL=2;
	export default{
		name:'ratingselect',
		props:{
			ratings:{
				type:Array,
				default(){
					return [];
				}
			},
			selectType:{
				type:Number,
				default:ALL
			},
			onlyContent:{
				type:Boolean,
				default:false
			},
			desc:{
				type:Object,
				default(){
					return {
						all:'全部',
						positive:"满意",
						negative:"不满意"
					}
				}
			}
		},
		methods:{
			clickType(type){
//				this.selectType=type;
				//父子组件通信
				this.$emit('select',type);
			},
			onlySeeContent(){
//				this.onlyContent=!this.onlyContent;
				this.$emit("toggle");
			}
		},
		computed:{
			positiveArray(){
			 	return	this.ratings.filter((rating)=>{
					return rating.rateType===POSITIVT;
				});
			},
			negativeArray(){
				return	this.ratings.filter((rating)=>{
					return rating.rateType===NEGATIVE;
				});
			}
		}
	}
</script>

<style lang="less" scoped="scoped">
	@import '../../common/less/mixin.less';
	.ratingselect{
		.rating-type{
			margin: 18px;
			margin-bottom:0;
			padding-bottom:18px;
			font-size:0;
			.border-1px(rgba(7,17,27,0.1));
			.block{
				padding: 8px 12px;
				line-height: 16px;
				border-radius: 1px;
				font-size: 12px;
				margin-right: 8px;
				.count{
					font-size:8px;
					margin-left:4px;
				}
				&.positive{
					background: rgba(0,160,220,0.2);
					&.active{
						background: rgb(0,160,220);
						color: #FFFFFF;
					}
				}
				&.negative{
					background: rgba(77,85,93,0.2);
					&.active{
						background: rgb(77,85,93);
						color: #FFFFFF;
					}
				}
			}
		}
		.only-see-content{
			padding: 12px 18px;
			font-size: 0;
			border-bottom: 1px solid rgba(7,17,27,0.1);
			.icon-check_circle{
				display: inline-block;
				vertical-align: top;
				line-height: 24px;
				font-size: 24px;
				color: rgb(147,153,159);
				&.on{
					color: #00c850;
				}
			}
			.text{
				display: inline-block;
				vertical-align: top;
				margin-left: 4px;
				line-height: 24px;
				font-size: 12px;
				color: rgb(147,153,159);
				
			}
		}
	}
</style>