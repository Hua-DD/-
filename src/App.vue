<template>
  <div id="app">
  	<mheader :seller='seller'></mheader>
  	<div class="nav">
  		<div class="nav-item border-1px">
  			<router-link to='/goods'>商品</router-link>
  		</div>
  		<div class="nav-item">
  			<router-link to="/ratings">评价</router-link>
  		</div>
  		<div class="nav-item">
  			<router-link to="/seller">商家</router-link>
  		</div>
  		
  	</div>
  	<keep-alive>
    	<router-view :seller="seller"/>
    </keep-alive>
  </div>
</template>

<script>
	import mheader from './components/header/header';
	const ERR_OK=0;
export default {
  name: 'App',
  components:{
  	mheader
  },
  data(){
  	return {
  		seller:{}
  	}
  },
  created(){
  	this.$http.get('/api/seller').then((response)=>{
  		console.log(response);
  		response=response.body;
  		if(response.errno==ERR_OK){
  			this.seller=response.data;
  		}
  		
  	},(error)=>{
  		console.log("访问数据出错");
  	})
  }
  
}
</script>

<style lang="less" scoped="scoped">
@import 'common/less/mixin.less';
@import 'common/less/base.less';
#app {
	.nav{
		display: flex;
		width: 100%;
		height: 40px;
		line-height: 40px;
		/*border-bottom: 1px solid rgba(7,17,27,0.1);*/
		.border-1px(rgba(7,17,27,0.1));
	}
	.nav-item{
		flex: 1;
		text-align: center;
		a{
			font-size: 14px;
			color: rgb(77,85,93);
		}
		a.router-link-active{
			color: rgb(240,20,20);
		}
	}
}
</style>
