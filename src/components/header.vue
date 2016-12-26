<template>
	<div id="header">
		<div class="container"><h3><p v-html="logo"></p></h3></div>
	</div>
</template>
<style>
	#header {
		background: #428bca;
		width: 100%;
		position: fixed;
		left: 0;
		top:0;
		z-index: 10;
	}
	#header h3 {
		overflow: hidden;
		color:#fff;
	}
</style>
<script>
	export default{
		data:function(){
			return {
				logo:"日常任务管理系统"
			}
		}
	}
</script>