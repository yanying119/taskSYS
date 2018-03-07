<template>
    <div id="content">
        <div class="container">
            <div class="task_list_box">
                <ul class="task_list list-group">
                    <li class="list-group-item clearfix" v-for="(task,i) in taskList" @click="editTash(i)" :key="'task'+i">
                        <span>{{i+1}}、{{task.name}}</span><br>
                        <span>开始时间：{{task.starTime}}</span><br>
                        <span>结束时间：{{task.endTime}}</span><br>
                        <span v-if="task.isImportant==1">重要程度：
                            <b class="red">加急</b>
                        </span>
                        <span v-else>重要程度：不急</span><br>
                        <span v-if="task.isClose">
                            完成与否：<b class="green">完成了</b>
                        </span>
                        <span v-else>完成与否：努力中</span><br>
                        <b>任务详情：</b><br>
                        <span v-html="task.content"></span>
                        <span class="glyphicon glyphicon-remove-sign remove" @click="removeTask($event,i)"></span>
                    </li>
                    <li class="list-group-item">
                        <button class="btn btn-default" @click="newTask">新建任务</button>
                    </li>
                </ul>
            </div>
            <div id="dialog" v-show="showDialog">
                <div class="box">
                    <ul class="task_list list-group">
                        <li class="list-group-item">
                            <div class="form-group">
                                <label class="control-label">任务名称：</label>
                                <input type="text" class="form-control" name="taskName" v-model="taskItem.name">
                            </div>
                        </li>
                        <li class="list-group-item">
                            <div class="form-group">
                                <label class="control-label">
                                    开始时间：
                                </label>
                                <vue2datepicker v-model="taskItem.starTime" format="YYYY-MM-DD HH:mm:ss" type="datetime" />
                            </div>
                        </li>
                        <li class="list-group-item">
                            <div class="form-group">
                                <label class="control-label">
                                    结束时间：
                                </label>
                                <vue2datepicker v-model="taskItem.endTime" format="YYYY-MM-DD HH:mm:ss" type="datetime" />
                            </div>
                        </li>

                        <li class="list-group-item">
                            <div class="form-group">
                                <label class="control-label">重要程度：</label>
                                <div class="radio">
                                    <label>
                                        <input type="radio" name="import" v-model="taskItem.isImportant"  :value="1"/>加急
                                    </label>
                                    <label>
                                        <input type="radio" name="import" v-model="taskItem.isImportant"  :value="0"/>不急
                                    </label>
                                </div>
                            </div>
                        </li>
                        <li class="list-group-item">
                            <div class="form-group">
                                <label class="control-label">是否完成：</label>
                                <div class="radio">
                                    <label>
                                        <input type="radio" name="finsh" :value="0" v-model="taskItem.isClose">努力中
                                    </label>
                                    <label>
                                        <input type="radio" name="finsh" :value="1" v-model="taskItem.isClose">完成了
                                    </label>
                                </div>
                            </div>
                        </li>
                        <li class="list-group-item">
                            <div class="form-group">
                                <label class="control-label">任务内容：</label>
                                <textarea name="content" class="form-control" v-model="taskItem.content"></textarea>
                            </div>
                        </li>
                        <li class="list-group-item">
                            <button class="btn btn-default" @click="saveTask">保存</button>
                            <button class="btn btn-default" @click="closeDialog">取消</button>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
        <div class="wrap" v-show="showDialog"></div>
    </div>
</template>
<script>
import vue2datepicker from 'vue2-datepicker';
import moment from 'moment';
export default {
    data: function () {
        return {
            taskList: [],
            taskItem: {},
            currentCount: 0,
            showDialog:false,
        }
    },
    components: { vue2datepicker },
    methods: {
        newTask: function () {
            var _this = this;
            this.showDialog = true;
            this.taskItem = {};
            this.currentCount = this.taskList.length;
            setTimeout(function () {
                _this.dialogPosition()
            })
        },
        editTash: function (i) {
            var _this = this;
            this.showDialog = true;
            this.taskItem = this.taskList[i]
            this.currentCount = i;
            setTimeout(function () {
                _this.dialogPosition()
            })
        },
        saveTask: function () {
            var dialog = document.getElementById("dialog"),
                i = this.currentCount;
            this.taskList[i] = Object.assign({},this.taskItem,{
                starTime:moment(this.taskItem.starTime).format("YYYY-MM-DD HH:mm:ss"),
                endTime:moment(this.taskItem.endTime).format("YYYY-MM-DD HH:mm:ss")
            });
            this.taskItem = {};
            this.showDialog = false;
            this.saveData("taskList", this.taskList);
        },
        saveData: function (name, content) {
            if (window.localStorage) {
                if (typeof content == "string") {
                    localStorage[name] = content;
                } else if (typeof content == "object") {
                    localStorage[name] = JSON.stringify(this.taskList);
                } else {
                    localStorage[name] = String(this.taskList);
                }
            }
        },
        removeTask: function (e, i) {
            if (confirm("您真的要删掉吗，删掉就没了")) {
                e.stopPropagation();
                this.taskList.splice(i, 1);
                this.saveData("taskList", this.taskList);
            } else {
                e.stopPropagation();
            }
        },
        dialogPosition: function () {
            var dialog = document.getElementById("dialog"),
                height = document.documentElement.clientHeight;
            var dialogHeight = dialog.clientHeight,
                top = (height - dialogHeight) / 2;

            if (top > 0) {
                dialog.style.top = top + "px";
            } else {
                dialog.style.height = (height - 136) + "px";
                dialog.style.top = 78 + "px";
            }
        },
        closeDialog: function () {
            this.showDialog = false;
        },
        getData: function (name) {
            if (window.localStorage) {
                return localStorage.getItem(name);
            }
        },
        init: function () {
            if (this.getData("taskList")) {
                this.taskList = JSON.parse(this.getData("taskList"));
            }
        }
    },
    mounted: function () {
        this.init();
    }
}
</script>

<style lang="scss">
#content {
  height: 100%;
  overflow-y: auto;
  padding: 66px 0 50px;
}
.task_list_box {
  padding: 10px 0;
}
.fl {
  float: left;
}
.fr {
  float: right;
}
.rili {
  color: #4399d8;
}
.red {
  color: #f00;
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
    right: -5px;
    top: -4px;
    line-height: 15px;
  }
}
.green {
  color: #1f8214;
  font-weight: normal;
}
#dialog {
  position: fixed;
  left: 5%;
  width: 90%;
  z-index: 10;
  overflow-y: auto;
}
.box {
  overflow: hidden;
  max-width: 600px;
  background: #fff;
  margin: 0 auto;
  border-radius: 4px;
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
.task_list {
  margin-bottom: 0;
}
</style>
