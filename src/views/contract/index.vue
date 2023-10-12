<template>
  <!-- <h2>合同列表</h2>
  <el-divider></el-divider> -->
  <div>
    <el-form inline class="searchForm">
      <el-form-item label="合同名:">
        <el-input v-model="params.contractName" placeholder="合同名" style="width: 120px;" clearable></el-input>
      </el-form-item>
      <el-form-item label="合同状态:" style="margin-left: 20px;">
        <el-select v-model="params.contractState" placeholder="合同状态" style="width: 120px;" clearable>
          <el-option label="未审核" value="0"></el-option>
          <el-option label="待结算" value="1"></el-option>
          <el-option label="结算中" value="2"></el-option>
          <el-option label="已结算" value="3"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item style="margin-left: 30px;">
        <el-button type="primary" @click="getContractList" style="margin-left: 10px;">
          <el-icon>
            <svg t="1646977561352" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
                 p-id="3057" width="200" height="200">
              <path
                  d="M986.304 871.424L747.328 630.4c-2.816-2.752-5.888-4.928-8.768-7.232 40.32-62.464 63.936-136.832 63.936-216.96 0-220.16-176.96-398.592-395.392-398.592C188.8 7.616 11.712 186.048 11.712 406.208s177.088 398.592 395.392 398.592a391.232 391.232 0 0 0 215.36-64.576c2.24 3.072 4.352 6.08 7.04 8.832l239.04 241.024a82.688 82.688 0 0 0 117.76 0 84.48 84.48 0 0 0 0-118.656m-579.2-192.512c-149.12 0-270.528-122.368-270.528-272.704 0-150.4 121.344-272.768 270.528-272.768 149.12 0 270.528 122.432 270.528 272.768 0 150.4-121.408 272.704-270.528 272.704"
                  p-id="3058"></path>
            </svg>
          </el-icon>
          &nbsp;查&nbsp;&nbsp;询
        </el-button>
      </el-form-item>
    </el-form>

    <div>
      <el-button type="primary" @click="openContractAdd()">
        <el-icon>
          <svg t="1646977404025" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
               p-id="2197" width="200" height="200">
            <path
                d="M925.696 384q19.456 0 37.376 7.68t30.72 20.48 20.48 30.72 7.68 37.376q0 20.48-7.68 37.888t-20.48 30.208-30.72 20.48-37.376 7.68l-287.744 0 0 287.744q0 20.48-7.68 37.888t-20.48 30.208-30.72 20.48-37.376 7.68q-20.48 0-37.888-7.68t-30.208-20.48-20.48-30.208-7.68-37.888l0-287.744-287.744 0q-20.48 0-37.888-7.68t-30.208-20.48-20.48-30.208-7.68-37.888q0-19.456 7.68-37.376t20.48-30.72 30.208-20.48 37.888-7.68l287.744 0 0-287.744q0-19.456 7.68-37.376t20.48-30.72 30.208-20.48 37.888-7.68q39.936 0 68.096 28.16t28.16 68.096l0 287.744 287.744 0z"
                p-id="2198"></path>
          </svg>
        </el-icon>
        &nbsp;添加合同
      </el-button>
      <!-- 导出数据：https://www.cnblogs.com/wangdashi/p/9269129.html -->
      <!-- https://zhuanlan.zhihu.com/p/77791428 -->
      <!-- https://www.cnblogs.com/muyangw/p/10306152.html -->
      <el-button type="warning" @click="export2Table">
        <el-icon>
          <svg t="1647313957290" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
               p-id="2289" width="200" height="200">
            <path
                d="M395.9 406H288.5c-2.8 0-5.7-0.7-8.1-2.4-18.4-13.1-18.2-34.3-6.4-46.3l217.6-220.9c11.1-11.2 29.2-11.4 40.4-0.3l0.3 0.3 217.7 221c11.1 11.3 10.9 29.4-0.4 40.4-5.3 5.2-12.5 8.2-19.9 8.2H628.1v215.7c0 7.9-6.4 14.3-14.3 14.3H410.2c-7.9 0-14.3-6.4-14.3-14.3V406zM283.3 652.4v87.5c0 7.9 6.4 14.3 14.3 14.3h428.8c7.9 0 14.3-6.4 14.3-14.3v-87.5c0-7.9 6.4-14.3 14.3-14.3h142.9c7.9 0 14.3 6.4 14.3 14.3V914c0 7.9-6.4 14.3-14.3 14.3H126.1c-7.9 0-14.3-6.4-14.3-14.3V652.4c0-7.9 6.4-14.3 14.3-14.3H269c7.9 0 14.3 6.4 14.3 14.3z"
                p-id="2290"></path>
          </svg>
        </el-icon>
        &nbsp;导出数据
      </el-button>
      <el-select placeholder="批量操作" style="width: 110px;margin-left: 12px; position: relative; top: 2px;">
        <el-option @click="deleteContractList">
          <span style="float: left;">
            <el-icon><delete/></el-icon>
          </span>
          <span style="padding-left: 6px; position: relative; top: -2px;">删除</span>
        </el-option>
      </el-select>
    </div>
  </div>
  <!-- 表格 -->
  <el-table :data="contractList" ref="multipleTableRef" @selection-change="handleSelectionChange"
            style="width: 100%; margin-top: 10px;" table-layout="auto" size="large" border stripe>
    <el-table-column type="selection" width="55"/>
    <el-table-column label="材料图片">
      <template #default="props">
        <el-image style="width: 60px; height: 60px" :src="WAREHOUSE_CONTEXT_PATH + props.row.files" fit="fill" />
      </template>
    </el-table-column>
    <el-table-column prop="contractId" label="合同ID" sortable/>
    <el-table-column prop="contractName" label="合同名" sortable/>
    <el-table-column prop="contractDesc" label="合同描述" sortable/>
    <el-table-column label="合同状态" sortable>
      <template #default="props">
        <span :class="{red:props.row.contractState=='1'}">{{
            props.row.contractState == "1" ? "待结算" : "结算中"
          }}</span>
      </template>
    </el-table-column>
    <el-table-column prop="associatedArea" label="关联工区" sortable/>
    <el-table-column prop="createTime" label="创建时间" sortable/>
    <el-table-column label="操作" fixed="right" width="240">
      <template #default="props">
        <el-link type="primary" @click.prevent="openContractUpdate(props.row)" style="margin-right: 8px">修改</el-link>
        <el-link type="primary" @click="downloadFiles(props.row)" style="margin-right: 8px">下载</el-link>
        <el-link type="primary" @click="startInstance(props.row.contractId,1)" v-if="props.row.contractState === '0'" style="margin-right: 8px">需要采购</el-link>
        <el-link type="primary" @click="startInstance(props.row.contractId,0)" v-if="props.row.contractState === '0'">无需采购</el-link>
      </template>
    </el-table-column>
  </el-table>
  <!-- 分页 -->
  <el-pagination
      background
      :total="params.totalNum"
      :page-sizes="[5, 10, 15, 20, 25, 30]"
      v-model:page-size="params.pageSize"
      v-model:currentPage="params.pageNum"
      layout="total, sizes, prev, pager, next, jumper"
      style="margin-top: 20px;"
      @size-change="changeSize"
      @current-change="changeCurrent"
  />

  <!-- 添加合同对话框 -->
  <contract-add ref="contractAddRef" @ok="getContractList"></contract-add>

  <!-- 修改合同对话框 -->
  <contract-update ref="contractUpdateRef" @ok="getContractList"></contract-update>

  <!-- 分配角色对话框 -->
  <assign-role ref="assignRoleRef"></assign-role>
</template>
<script setup>
import {reactive, ref} from "vue";
import {export2excel, get, WAREHOUSE_CONTEXT_PATH, post, tip} from "@/common";

const params = reactive({
  contractName: '',
  contractDesc: '',
  contractState: '',
  associatedArea: '',
  pageSize: 5,
  pageNum: 1,
  totalNum: 0
})

// 图片回显路径
const imageUrl = ref('');

// 表格数据
const contractList = ref();

// 获取查询结果
const getContractList = () => {
  get("/contract/contract-list",params).then(result => {
    contractList.value = result.data.resultList;
    params.totalNum = result.data.totalNum;
  });
}
getContractList();

// 复选框的操作
const multipleTableRef = ref();
const multipleSelection = ref([]);
const toggleSelection = (rows) => {
  if (rows) {
    rows.forEach((row) => {
      multipleTableRef.value.toggleRowSelection(row, undefined)
    })
  } else {
    multipleTableRef.value.clearSelection()
  }
}
const handleSelectionChange = (val) => {
  multipleSelection.value = val;
}

// 添加用户
import ContractAdd from "./contract-add.vue";

const contractAddRef = ref();
// 界面添加合同
const openContractAdd = () => {
  contractAddRef.value.open();
};

import ContractUpdate from "./contract-update.vue";

const contractUpdateRef = ref();
//
const openContractUpdate = (contract) =>{
  contractUpdateRef.value.open(contract);
}

// 修改每页显示条数
const changeSize = (size) => {
  params.pageSize = size;
  // 重新查询
  getStorePageList();
}
// 修改当前页码
const changeCurrent = (num) => {
  params.pageNum = num;
  // 重新查询
  getStorePageList();
}

// 导出数据
const export2Table = () => {
  get("/contract/exportTable", params).then(result => {
    // 要导出的数据
    const storeList = result.data;
    const columns = [
      {"title": "合同ID", "key": "contractId"},
      {"title": "合同名称", "key": "contractName"},
      {"title": "合同描述", "key": "contractDesc"},
      {"title": "合同状态", "key": "contractState"},
      {"title": "关联工区", "key": "associatedArea"},
      {"title": "创建时间", "key": "createTime"},
    ];
    export2excel(columns, storeList, "合同信息表");
  });
}

import axios from 'axios';

// 下载合同照片
const downloadFiles = (contract) => {
  console.log(contract.files.substring(12,19));
  // 创建一个链接元素
  const link = document.createElement('a');
  link.href = "http://localhost:9999/warehouse/contract/download-image/"+contract.files.substring(12,19);
  link.target = '_blank';

  // 设置链接的下载属性
  link.download = 'example.jpg'; // 替换为要保存的文件名

  // 触发点击链接的操作，浏览器会自动下载
  link.click();
}

const startInstance = (contractId,state) => {
  console.log(contractId)
  console.log(state)
  let data = {
    contractId:contractId,
    state: state
  }
  console.log(data)
  post("/activiti/start-instance", data).then(result => {
    console.log(result)

    if(result.message === "启动流程成功"){
      tip.success(result.message)
    }else {
      tip.warning(result.message)
    }
    getContractList()
  })
  // if(state === 1){

  // }
}
</script>

