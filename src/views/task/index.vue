<template>

<!--  <el-link type="primary" @click.prevent="toPurchaseDetail()" style="margin-right: 8px">-->
<!--    采购审核</el-link>-->
  
  <!-- 表格 -->
  <el-table :data="flowPageList" style="width: 100%; margin-top: 10px;" table-layout="auto" size="large" border stripe>
    <el-table-column type="index" width="50" />
    <el-table-column prop="instanceId" label="流程编号" sortable />
    <el-table-column prop="task" label="当前任务" sortable />
    <el-table-column prop="assignee" label="当前负责人" sortable />
    <el-table-column prop="contractName" label="所属合同名称" sortable />
    <el-table-column prop="reason" label="驳回原因" sortable />
    <el-table-column label="当前流程所有任务">
      <template #default="props">
        <el-link type="primary" @click.prevent="openTaskDetail(props.row)" style="margin-right: 8px">查看详情</el-link>
        <el-link type="primary" @click.prevent="completeTask(props.row)" style="margin-right: 8px" v-if="props.row.task !== '任务已结束'">
          完成任务</el-link>
        <el-link type="primary" @click.prevent="openContractDetail(props.row)" style="margin-right: 8px">查看合同</el-link>
        <el-link type="primary" @click.prevent="toPurchaseDetail(props.row)" style="margin-right: 8px">
          采购审核</el-link>
        <el-link type="primary" @click.prevent="toCommodity(props.row)" style="margin-right: 8px">
          前往采购</el-link>
        <el-link type="primary" @click.prevent="toPurchase(props.row)" style="margin-right: 8px">
          检查采购</el-link>
        <el-link type="primary" @click.prevent="toInStore(props.row)" style="margin-right: 8px">
          前往入库</el-link>
        <el-link type="primary" @click.prevent="toOutStore(props.row)" style="margin-right: 8px">
          前往出库</el-link>
        <el-link type="primary" @click.prevent="toSettlement(props.row)" style="margin-right: 8px">
          前往结算</el-link>
      </template>
    </el-table-column>
  </el-table>
  <task-detail ref="taskDetailRef"></task-detail>
  
  <purchase-detail ref="purchaseDetailRef" @ok="init"></purchase-detail>
</template>

<script setup>
import {get, post, tip} from "@/common";
import {onMounted, ref} from "vue";
import { useRouter } from "vue-router";
import TaskDetail from "@/views/task/task-detail.vue";
const router = useRouter(); // 获取路由器

const flowPageList = ref();

const taskStatus = ref(false);

onMounted(() => {
  get("/activiti/have-task", {}).then(result => {
    console.log(result)
    if(result.message === "有还未审核的合同"){
      tip.warning("有还未审核的合同")
    }
    else if(result.message === "有审核未通过的合同，请前往修改"){
      tip.warning("有审核未通过的合同，请前往修改")
    }
    else if(result.data.length !== 0){
      tip.warning("您有任务待处理")
    }else{
      tip.success("当前无任务需要处理")
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

const openContractDetail = (task) =>{
  console.log(task.contractId)
  // contractRef.value.open(task.value.contractId)
  router.push({path: "/contract/index", query: {"contractId": task.contractId}})

}

// 完成任务
const completeTask = (task) =>{
  post("/activiti/complete-task", task).then(result => {
    console.log(result)
    if(result.message === "完成任务"){
      tip.success(result.message)
    }else {
      tip.warning(result.message)
    }
    init()
  })
}

// 跳转去物料界面采购
const toPurchase = (task) =>{
  router.push({path: "/purchase/index", query: {"contractId": task.contractId}})
}

// 跳转去采购页面
const toCommodity = (task) =>{
  router.push({path: "/commodity/index", query: {"contractId": task.contractId}})
}


import PurchaseDetail from "@/views/purchase/purchase-detail.vue";
// 跳转去采购详情，用于审核采购
const purchaseDetailRef = ref()
const toPurchaseDetail = (task) =>{
  // router.push({path: "/commodity/index", query: {"contractId": "109"}})
  purchaseDetailRef.value.open(task.contractId)
}

// 前往入库
const toInStore = (task) =>{
  router.push({path: "/instore/index", query: {"contractId": task.contractId}})
}

// 前往出库
const toOutStore = (task) =>{
  router.push({path: "/outstore/index", query: {"contractId": task.contractId}})
}


// 前往合同结算
const toSettlement = (task) =>{
  router.push({path: "/settlement/index", query: {"contractId": task.contractId}})
}

</script>

<style scoped>

</style>