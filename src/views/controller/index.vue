<template>
  <!-- 入库列表-->
  <el-card class="box-card">
    <template #header>
      <div class="card-header">
        <span>当前任务</span>
        <el-button class="button" @click="openTaskList" text>查看任务列表</el-button>
      </div>
    </template>
    <!-- 表格 -->
    <el-table :data="flowPageList" style="width: 100%;" table-layout="auto" size="large" border stripe>
      <el-table-column prop="contractName" label="所属合同名称" sortable />
      <el-table-column prop="task" label="当前任务" sortable />
      <el-table-column prop="assignee" label="当前负责人" sortable />
      <el-table-column label="当前流程所有任务">
        <template #default="props">
          <el-link type="primary" @click.prevent="openTaskDetail(props.row)" style="margin-right: 8px">查看详情</el-link>
        </template>
      </el-table-column>
    </el-table>
  </el-card>

  <el-drawer
      v-model="showTask"
      title="当前执行中的流程"
      direction="rtl"
      size="80%"
  >
    <task ref="taskListRef"></task>
  </el-drawer>

  <task-detail ref="taskDetailRef"></task-detail>

</template>

<script setup>
import { reactive, ref, onMounted } from 'vue';
import {get, put, tip, export2excel, post} from "@/common";
import { useRoute } from 'vue-router'
import TaskDetail from "@/views/task/task-detail.vue";
import task from "@/views/task/index.vue"

const route = useRoute(); // 获取路由信息

const flowPageList = ref(); // 当前进行中的任务列表
const hisFlowPageList = ref([]); // 历史任务列表

onMounted(() =>{
  console.log("控制中心")
  showTask.value = false

  get("/activiti/activiti-page-list", {}).then(result => {
    let data = result.data.filter(obj => {
      // Check if the object is not empty (i.e., has at least one property)
      return Object.keys(obj).length > 0;
    });
    data.forEach(function (item){
      if(item.flag === "已结束"){
        item.task = "任务已结束"
        item.assignee = "任务已结束"
        hisFlowPageList.value.push(item)
        data.splice(data.indexOf(item),1)
      }
    })
    console.log(hisFlowPageList.value)
    flowPageList.value = data
  })
})

const showTask = ref(true)

const taskDetailRef = ref();
const openTaskDetail = (task) =>{
  taskDetailRef.value.open(task);
}

const taskListRef = ref();
const openTaskList = () =>{
  showTask.value = true
  taskListRef.value.open('1');
}

</script>

<style scoped>
.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 22px;
  font-weight: bold;
}

.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.box-card {
  width: 700px;
}
</style>