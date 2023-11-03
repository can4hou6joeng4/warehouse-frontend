<template>
  <!-- 采购详情对话框 -->
  <el-dialog v-model="visible" title="采购详情" width="800px" @close="close">
    <el-form ref="contractAddForm" :model="purchaseDetail" :rules="rules" label-position="top">
      <el-form-item label="采购员：" prop="buyName" >
        <el-input v-model="purchaseDetail.buyName" autocomplete="off" disabled/>
      </el-form-item>
      <el-form-item label="生产产品：" prop="productName" >
        <el-input v-model="purchaseDetail.productName" autocomplete="off" disabled/>
      </el-form-item>
      <el-form-item label="材料比例：">
        <el-tag
            v-for="tag in ratioDetails"
            :key="tag.name"
            class="mx-1"
        >
          <div>原材料: {{ tag.materialName }}</div>
           占比: {{ tag.ratio }}
        </el-tag>
      </el-form-item>
      <el-row>
        <el-col :span="12">
          <el-form-item label="原材料：" style="width: 150px">
            <el-select v-model="purchaseDetail.materialId" clearable @change="handleSelectMaterial">
              <el-option v-for="material of purchaseList" :label="material.materialName" :value="material.materialId" :key="material.materialId"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="所选供应商：" style="width: 150px" >
            <el-input v-model="supplyNameByPurchase" autocomplete="off" disabled/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="供应商信息：">
            <el-table :data="supplyList" style="width: 100%">
              <el-table-column prop="supplyName" label="供应商名称" />
              <el-table-column prop="concat" label="联系人"  />
              <el-table-column prop="phone" label="电话" />
              <el-table-column prop="quotation" label="报价" />
            </el-table>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="close">取消</el-button>
        <el-button type="primary" @click="addUser">确定</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup>
import {ref, reactive} from 'vue'
import {post, tip, WAREHOUSE_CONTEXT_PATH, get} from "@/common";
import { Plus } from '@element-plus/icons-vue'

const visible = ref(false); // 该页面的可见性

// 图片回显路径
const imageUrl = ref('');

// 关闭
const close = () => {
  visible.value = false;
}

const rules = reactive({
  contractName: [
    {required: true, message: '请输入合同名', trigger: 'blur'}
  ],
  contractDesc: [
    {required: true, message: '请输入合同描述', trigger: 'blur'},
  ],
  contractState: [
    {required: true, message: '请选择合同状态', trigger: 'blur'},
  ]
})


const purchaseDetail = reactive({
  buyName:'',
  productName:'',
  materialId:''
});

// 配料比列表
const ratioDetails= ref({materialId:'',materialName:''});
const purchaseList = ref({})

// 该对话框打开
const open = (task) => {
  visible.value = true;
  let contractId = task
  get(`/purchase/purchase-list/${contractId}`).then(result => {
    purchaseDetail["buyName"] = result.data[0].buyName
    purchaseDetail["productName"] = result.data[0].productName
    ratioDetails.value = result.data[0].ratioList
    purchaseList.value = result.data[0].purchaseList
  });
};

const supplyNameByPurchase = ref();

// 改变材料时获取到该材料所有能提供的供应商信息以及采购员所选择的供应商信息
const handleSelectMaterial = () => {
  supplyNameByPurchase.value = purchaseList.value.find(item => item.materialId === purchaseDetail.materialId).supplyName
  getSupplyList(purchaseDetail.materialId)
}

// 获得所有产品
const supplyList = ref();
const getSupplyList= (materialId) => {
  get(`/supply/supply-list/${materialId}`).then(result => {
    supplyList.value = result.data;
    console.log(supplyList)
  });
}

const contractAddForm = ref();
// 定义
const emit = defineEmits(["ok"]);
// 添加用户提交
const addUser = () => {
  contractAddForm.value.validate(valid => {
    if (valid) {
      contractAdd.startTime = formatDate(contractAdd.date[0])
      contractAdd.endTime = formatDate(contractAdd.date[1])
      delete contractAdd.date
      post('/contract/addContract', contractAdd).then(result => {
        emit('ok');
        tip.success(result.message);
        visible.value = false; // 关闭对话框
      });
    }
  });
}

defineExpose({open});
</script>
<style scoped>
.avatar-uploader, .avatar-uploader .avatar{
  width: 100px;
  height: 100px;
  display: block;
}

.mx-1 {
  width: 90px;
  height: 52px;
  line-height: 20px;
  font-size: 14px;
  margin-left: 10px;
}
</style>