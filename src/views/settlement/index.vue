<template>
  <!-- 出库单表格 -->
  <el-table :data="outstorePageList" style="width: 100%;margin-top: 10px;" height="250px" table-layout="auto" border stripe>
    <el-table-column prop="outsId" label="出库单ID" width="130"/>
    <el-table-column prop="contractName" label="所属合同"/>
    <el-table-column prop="workRegion" label="工区名称"/>
    <el-table-column prop="productName" label="产品名称"/>
    <el-table-column prop="outNum" label="出库数量"/>
    <el-table-column prop="salePrice" label="单价"/>
    <el-table-column prop="salePriceSum" label="金额"/>
    <el-table-column prop="userCode" label="创建人"/>
    <el-table-column prop="createTime" label="创建时间"/>
  </el-table>
  <div>
    <span>出库总金额：{{outStoreSum}}</span>
  </div>

  <el-divider></el-divider>


  <!-- 入库单表格 -->
  <el-table :data="instorePageList" style="width: 100%;margin-top: 10px;" height="250px" border stripe>
    <el-table-column prop="insId" label="入库单ID" width="130"/>
    <el-table-column prop="contractName" label="所属合同" />
    <el-table-column prop="supplyName" label="供应商" />
    <el-table-column prop="materialName" label="材料名称" />
    <el-table-column prop="inNum" label="公司数量" />
    <el-table-column prop="price" label="单价" />
    <el-table-column prop="priceSum" label="金额" />
    <el-table-column prop="remarks" label="备注" />
  </el-table>
  <div>
    <span>出库总金额：{{inStoreSum}}</span>
  </div>

  <!--  &lt;!&ndash; 分页 &ndash;&gt;-->
<!--  <el-pagination-->
<!--      background-->
<!--      :total="params.totalNum"-->
<!--      :page-sizes="[5, 10, 15, 20, 25, 30]"-->
<!--      v-model:page-size="params.pageSize"-->
<!--      v-model:currentPage="params.pageNum"-->
<!--      layout="total, sizes, prev, pager, next, jumper"-->
<!--      style="margin-top: 20px;"-->
<!--      @size-change="changeSize"-->
<!--      @current-change="changeCurrent"-->
<!--  />-->


</template>

<script setup>
import {nextTick, onMounted, reactive, ref} from 'vue';
import {get, put, tip, export2excel, post} from "@/common";
import { useRoute } from 'vue-router'
import { Search, Edit, Check, Message, Star, Delete } from '@element-plus/icons-vue'

const route = useRoute(); // 获取路由信息

// 分页模糊查询数据
const params = reactive({
  contractId: '',
  productName: '',
  startTime: '',
  endTime: '',
  isOut: '',
  pageSize: 5,
  pageNum: 1,
  totalNum: 0
})


// 页面初始化
onMounted(() => {
  console.log("初始化")
  if (route.query.contractId){
    console.log(route.query.contractId)
    params.contractId = route.query.contractId
    getOutstorePageList()
    getInstorePageList()
  }
});

const outStoreSum = ref(0);
// 出库单的表格数据
const outstorePageList = ref();
const getOutstorePageList = () => {
  // 后台获取查询结果
  get("/outstore/outstore-page-list", params).then(result => {
    outstorePageList.value = result.data.resultList;
    outstorePageList.value.forEach(function (item, index){
      item.salePriceSum = item.salePrice * item.outNum
      outStoreSum.value += item.salePriceSum
    })
  });
}

const inStoreSum = ref(0);
// 入库单据
const instorePageList = ref()
// 入库单的表格数据
const getInstorePageList = () => {
  // 后台获取查询结果
  get("/instore/instore-page-list", params).then(result => {
    instorePageList.value = result.data.resultList;
    instorePageList.value.forEach(function (item, index){
      console.log(item)
      item.priceSum = item.price * item.inNum
      inStoreSum.value += item.priceSum
    })
  });
}
</script>

<style scoped>
.searchForm{
  margin-top: 20px;
  display: flex;
}
.red{
  color: red;
}
.green{
  color: green;
}
</style>