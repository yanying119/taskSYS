<template>
	<div id="content">
		<div class="container">
			<div class="task_list_box">
				<ul class="task_list list-group">
					<li class="list-group-item clearfix" v-for="(task,i) in taskList" @click="editor(i)">
						<span>{{i+1}}、{{task.name}}</span>
						<span class="glyphicon glyphicon-calendar rili fr" style="top:0">{{task.starTime}}至{{task.endTime}}</span><br>
						<span v-if="task.isImportant">重要程度：<b class="red">加急</b></span>
						<span v-else="task.isImportant">重要程度：不急</span><br>
						<span v-if="task.isClose"><b class="green">完成与否：完成了</b></span></span>
						<span v-else="task.isClose">完成与否：努力中</span><br>
						<span v-html="task.content"></span>
						<span  class="glyphicon glyphicon-remove-sign remove" @click="removeTask($event,i)"></span>
					</li>
					<li class="list-group-item">
						<button class="btn btn-default" @click="newTask">新建任务</button>
					</li>
				</ul>
			</div>
			<div id="dialog" v-if="taskItem">
				<div class="box">
					<ul class="task_list list-group">
						<li class="list-group-item">
							任务名称：<input type="text" name="taskName" :value="taskItem.name">
						</li>
						<li class="list-group-item">
							开始时间：<input type="text" name="taskStarTime" :value="taskItem.starTime"><span class="glyphicon glyphicon-calendar rili" style="top:0"></span>
						</li>
						<li class="list-group-item">
							结束时间：<input type="text" name="taskEndTime"  :value="taskItem.endTime" ><span class="glyphicon glyphicon-calendar rili" style="top:0"></span>
						</li>

						<li class="list-group-item" v-if="taskItem.isImportant">
							重要程度：
							<label for=""><input type="radio" name="import" checked="checked" value="1">加急</label>
							<label for=""><input type="radio" name="import" value="0">不急</label>
						</li>
						<li class="list-group-item" v-else="taskItem.isImportant">
							重要程度：
							<label for=""><input type="radio" name="import"  value="1">加急</label>
							<label for=""><input type="radio" name="import"  checked="checked" value="0">不急</label>
						</li>

						<li class="list-group-item" v-if="taskItem.isClose">
							是否完成：
							<label for=""><input type="radio" name="finsh"  value="0">努力中</label>
							<label for=""><input type="radio" name="finsh" checked="checked" value="1">完成了</label>
						</li>
						<li class="list-group-item" v-else="taskItem.isClose">
							是否完成：
							<label for=""><input type="radio" name="finsh" checked="checked"  value="0">努力中</label>
							<label for=""><input type="radio" name="finsh" value="1">完成了</label>
						</li>

						<li class="list-group-item">
							<span class="fl">任务内容：</span><textarea name="content"  :value="taskItem.content"></textarea>
						</li>
						<li class="list-group-item">
							<button class="btn btn-default" @click="saveTask">保存</button>
							<button class="btn btn-default" @click="closeWindow">取消</button>
						</li>
					</ul>
				</div>
			</div>
		</div>
		<div class="wrap" v-show="taskItem"></div>
	</div>
</template>

<style lang="scss">
	#content {
		height: 100%;
		overflow-y: auto;
		padding:66px 0 50px;
	}
	.task_list_box {
		padding:10px 0;
	}
	.fl {
		float: left;
	}
	.fr {
		float: right;
	}
	.rili {
		color:#4399d8;
	}
	.red {
		color:#f00;
	}
	.list-group-item {
		position: relative;
		span {
			line-height: 30px;
		}
		label {
			font-weight: normal;
		}
		.remove {
			position: absolute;
			right:-5px;
			top: -4px;
			line-height: 15px;
		}
	}
	.green {
		color:#1f8214;
		font-weight: normal;
	} 
	#dialog {
		position: fixed;
		left: 25%;
		top: 25%;
		width: 50%;
		z-index: 10;
		max-height: 300px;
		overflow-y:auto;
		border-radius:4px;
	}
	.wrap {
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		z-index: 9;
		background: #000;
		opacity: 0.5;
	}
	input,textarea {
		width: 100%;
		max-width: 154px;
	}
</style>

<script>
	export default{
		data:function(){
	        return {
	            taskList:[],
	            taskItem:null,
	            currentCount:0
	        }
	    },
	    methods:{
	    	editor:function(i){
	    		this.taskItem = this.taskList[i]
	    		this.currentCount = i
	    	},
	    	removeTask:function(e,i){
	    		if(confirm("您真的要删掉吗，删掉就没了")){
	    			e.stopPropagation();
	    			this.taskList.splice(i,1);
	    			this.saveData("taskList",this.taskList);
	    		}else {
	    			e.stopPropagation();
	    		}
	    	},
	    	saveTask:function(){
	    		var dialog = document.getElementById("dialog"),
	    			name = document.getElementsByName("taskName")[0].value,
                    starTime = document.getElementsByName("taskStarTime")[0].value,
                    endTime = document.getElementsByName("taskEndTime")[0].value,
                    content = document.getElementsByName("content")[0].value,
                    isClose = this.getRadio(document.getElementsByName("finsh")),
                    isImportant = this.getRadio(document.getElementsByName("import")),
                    i = this.currentCount;
                var newTask = {
                	name:name,
                    starTime:starTime,
                    endTime:endTime,
                    content:content,
                    isClose:isClose,
                    isImportant:isImportant
                }

                this.taskList[i] = newTask;
                this.taskItem = null;
                this.saveData("taskList",this.taskList);
	    	},
	    	getRadio:function(obj){

	    		for(var i = 0; i<obj.length;i++){
	    			if(obj[i].checked){
	    				if(obj[i].value == 1){
	    					return true;
	    				}else{
	    					return false;
	    				}
	    			}
	    		}
	    	},
	    	saveData:function(name,content){

	    		if(window.localStorage){
	    			if(typeof content == "string"){
	    				localStorage[name] = content;
	    			}else if(typeof content == "object"){
	    				localStorage[name] = JSON.stringify(this.taskList);
	    			}else{
	    				localStorage[name] = String(this.taskList);
	    			}
	    		}
	    	},
	    	getData:function(name){
	    		if(window.localStorage){
	    			return localStorage.getItem(name);
	    		}
	    	},
	    	newTask:function(){
	    		this.taskItem = true;
	    		this.currentCount = this.taskList.length
	    	},
	    	closeWindow:function(){
	    		this.taskItem = false;
	    	},
	    	init:function(){
	    		if(this.getData("taskList")){

	    			this.taskList = JSON.parse(this.getData("taskList"));
	    		}
	    	}
	    },
	    mounted:function(){
	    	this.init()
	    }
	}
</script>