<template>
  <div>
    <el-table :data="contractList" ref="multipleTableRef" @selection-change="handleSelectionChange"
              style="width: 100%; margin-top: 10px;" border stripe id="elTable" v-if="!showEngineering">
      <el-table-column prop="contractId" label="合同ID"/>
      <el-table-column prop="contractName" label="合同名" width="120"/>
      <el-table-column prop="productName" label="产品名称" width="120"/>
      <el-table-column prop="productNum" label="生产数量" width="120"/>
      <el-table-column prop="startTime" label="工期开始时间" width="120"/>
      <el-table-column prop="endTime" label="工期结束时间" width="120"/>
      <el-table-column prop="workRegion" label="关联工区" width="120"/>
      <el-table-column prop="custom" label="合同客户" width="120"/>
      <el-table-column label="合同状态" width="120">
        <template #default="props"> 
        <span :class="{red:props.row.contractState ==='0' || props.row.contractState ==='1'}">
          {{
            props.row.contractState === '0' ? '未审核'
                : props.row.contractState === '1' ? '被驳回'
                    : props.row.contractState === '2' ? '待结算'
                        : props.row.contractState === '3' ? '结算中'
                            : props.row.contractState === '4' ? '已结算'
                                : '其他'
          }}
        </span>
        </template>
      </el-table-column>
      <el-table-column label="是否需要采购" width="120">
        <template #default="props">
        <span :class="{red:props.row.ifPurchase=='1'}">{{
            props.row.ifPurchase == "1" ? "需要采购" : "无需采购"
          }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="createTime" label="创建时间" width="100"/>
      <el-table-column label="操作" fixed="right" width="240">
        <template #default="props">
<!--                  v-if="props.row.contractState ===   '0' || props.row.contractState === '1'"-->
          <el-link type="primary"
                   v-if="props.row.contractState === '0' && props.row.contractId != '' && showExamine==true"
                   @click.prevent="openContractUpdate(props.row)" style="margin-right: 8px">修改
          </el-link>
          <el-link type="primary" @click="openContractDetail(props.row)" style="margin-right: 8px">查看合同详情</el-link>
          <el-link type="primary" @click="downloadFiles(props.row)" style="margin-right: 8px">下载附件</el-link>
          <el-link type="primary" @click="agree(props.row)"
                   v-if="props.row.contractState === '0' && props.row.contractId != '' && showExamine==true"
                   style="margin-right: 8px">通过
          </el-link>
          <el-link type="primary" @click="reject(props.row.contractId,props.row.ifPurchase)"
                   v-if="props.row.contractState === '0' && props.row.contractId != ''  && showExamine==true"
                   style="margin-right: 8px">退回
          </el-link>
          <el-link type="primary" v-if="props.row.contractState === '1'" @click.prevent="completeTask(props.row)"
                   style="margin-right: 8px">
            再次提交审核
          </el-link>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script setup>
import {onMounted} from "vue";
import {get} from "@/common";

const props = defineProps({
  paramsData: {
    type: Object,
    required: true,
  },
});

onMounted(hook => {
  console.log(123)
  console.log(props.paramsData)
  
})
</script>

<style scoped>

</style>