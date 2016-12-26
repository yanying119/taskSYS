<template>
	<div id="footer">
		<div class="container" v-html="footer"></div>
	</div>
</template>
<style>
	#footer {
		background: #428bca;
		text-align: center;
		width: 100%;
		padding:15px 0;
		color:#fff;
		position: fixed;
		left: 0;
		bottom:0;
		z-index: 10
	}
</style>
<script>
	export default{
		data:function(){
			return {
				footer:"版权所有：转载请说明出处"
			}
		}
	}
</script>