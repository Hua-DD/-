<template>
	<div class="star">
		<!--<span class="star-item on" :class="starItem"></span>
		<span class="star-item on" :class="starItem"></span>
		<span class="star-item half" :class="starItem"></span>
		<span class="star-item off" :class="starItem"></span>
		<span class="star-item off" :class="starItem"></span>-->
		<span v-for="item in starClasses"  class="star-item"  :class="[starItem,item]"></span>
	</div>
</template>

<script>
	const LENGTH=5;
	const CLASS_ON='on';
	const CLASS_HALF='half';
	const CLASS_OFF='off';
	export default {
		name: 'star',
		props: {
			size: {
				type:Number
			},
			score: {
				type:Number
			}
		},
		computed: {
			starItem () {
				return 'star-'+this.size;
			},
			starClasses () {
				let classes= [];
				//4.2 4.6
				let score = Math.floor(this.score*2)/2;
				let isHalfStar = score%1 !==0;
				let onstar = Math.floor(score);
				for(let i=0;i<onstar;i++){
					classes.push(CLASS_ON);
				}
				if(isHalfStar){
					classes.push(CLASS_HALF);
				}
				while(classes.length<LENGTH){
					classes.push(CLASS_OFF);
				}
				return classes;
			}
		}
	}
</script>

<style lang="less" scoped="scoped">
@import '../../common/less/mixin.less';
	.star{
		font-size:0;
		.star-item{
			display: inline-block;
			background-repeat:no-repeat;
			/*.bg-img('star48_on');*/
			
			&:last-child{
				margin-right:0;
			}
			&.star-48{
				width:20px;
				height:20px;
				font-size: 20px;
				background-size:20px 20px;
				margin-right:20px;
				&.on{
					.bg-img('star48_on');
				}
				&.half{
					.bg-img('star48_half');
				}
				&.off{
					.bg-img('star48_off');
				}
				
			}
			&.star-36{
				width:15px;
				height:15px;
				font-size: 15px;
				background-size:15px 15px;
				margin-right:6px;
				&.on{
					.bg-img('star36_on');
				}
				&.half{
					.bg-img('star36_half');
				}
				&.off{
					.bg-img('star36_off');
				}
			}
			&.star-24{
				width:10px;
				height:10px;
				font-size: 10px;
				background-size:10px 10px;
				margin-right:11px;
				&.on{
					.bg-img('star24_on');
				}
				&.half{
					.bg-img('star24_half');
				}
				&.off{
					.bg-img('star24_off');
				}
			}
		}
	}
</style>