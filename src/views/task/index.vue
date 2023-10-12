<template>
  <!-- 表格 -->
  <el-table :data="flowPageList" style="width: 100%; margin-top: 10px;" table-layout="auto" size="large" border stripe>
    <el-table-column type="index" width="50" />
    <el-table-column prop="instanceId" label="流程编号" sortable />
    <el-table-column prop="task" label="当前任务" sortable />
    <el-table-column prop="assignee" label="当前负责人" sortable />
    <el-table-column prop="contractName" label="所属合同名称" sortable />
    <el-table-column label="当前流程所有任务">
      <template #default="props">
        <el-link type="primary" @click.prevent="openTaskDetail(props.row)" style="margin-right: 8px">查看详情</el-link>
      </template>
    </el-table-column>
  </el-table>
  <task-detail ref="taskDetailRef"></task-detail>
</template>

<script setup>
import {get, tip} from "@/common";
import {onMounted, ref} from "vue";
import { ElMessageBox } from 'element-plus';
import { useRouter } from "vue-router";
import TaskDetail from "@/views/task/task-detail.vue";
const router = useRouter(); // 获取路由器

const flowPageList = ref();


onMounted(() => {
  console.log("加载完成")

  get("/activiti/have-task", {}).then(result => {
    console.log(result)
    if(result.message === "有还未审核的合同"){
      ElMessageBox.confirm('是否前往处理?', '还有未审核的合同', {
        confirmButtonText: '前往处理',
        cancelButtonText: 'Cancel',
      })
          .then(() => {
            router.push({ path: "/contract/index" });
          })
          .catch(() => {
          });
    }
    if(result.data.length !== 0){
      tip.warning("您有任务待处理")
    }
  });

  init()
})

function init(){
  get("/activiti/activiti-page-list", {}).then(result => {
    let data = result.data.filter(obj => {
      // Check if the object is not empty (i.e., has at least one property)
      return Object.keys(obj).length > 0;
    });
    data.forEach(function (item){
      console.log(item)
      if(item.flag === "已结束"){
        item.task = "任务已结束"
        item.assignee = "任务已结束"
      }
    })

    flowPageList.value = data
  })
}

const taskDetailRef = ref();
const openTaskDetail = (task) =>{
  taskDetailRef.value.open(task);
}
</script>

<style scoped>

</style>