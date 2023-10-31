<template>
  <!-- 添加合同对话框 -->
  <el-dialog v-model="visible" title="添加合同" width="500px" @close="close" destroy-on-close>
    <el-form ref="contractAddForm" :model="contractAdd" :rules="rules" label-position="top">
      <el-form-item label="合同名：" prop="contractName">
        <el-input v-model="contractAdd.contractName" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="关联工区：" prop="associatedArea">
        <el-input v-model="contractAdd.associatedArea" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="合同工期">
        <el-date-picker
            v-model="contractAdd.date"
            type="datetimerange"
            unlink-panels
            range-separator="To"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            size="large"
        />
      </el-form-item>
      <el-form-item label="合同照片：" prop="imgs">
        <el-upload
            class="avatar-uploader"
            :action="WAREHOUSE_CONTEXT_PATH + '/contract/img-upload'"
            :show-file-list="false"
            :on-change="handleAvatarChange"
            :before-upload="beforeAvatarUpload"
        >
          <img v-if="imageUrl" :src="imageUrl" class="avatar" />
          <el-icon v-else class="avatar-uploader-icon"><Plus /></el-icon>
        </el-upload>
      </el-form-item>
      <el-form-item label="生产数量：">
        <el-input v-model="contractAdd.productNum" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="生产产品：">
        <el-select v-model="contractAdd.productId" style="width: 120px;" clearable @change="handleSelectChange">
          <el-option v-for="product of productList" :label="product.productName" :value="product.productId" :key="product.productId"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="材料比例：" v-if="showRatio">
        <el-tag
            v-for="tag in ratioDetails"
            :key="tag.name"
            class="mx-1"
        >
          {{ tag.materialName }} : {{ tag.ratio }}
        </el-tag>
      </el-form-item>
      <el-form-item label="原材料：" v-if="showRatio">
        <el-select v-model="contractAdd.materialId" style="width: 120px;" clearable @change="handleSelectMaterial">
          <el-option v-for="material of ratioDetails" :label="material.materialName" :value="material.materialId" :key="material.materialId"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="仓库数量：" v-if="showRatio">
        <el-input v-model="materialNum" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="需要数量：" v-if="showRatio">
        <el-input v-model="needNum" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="是否需要采购：" v-if="showRatio">
        <el-select v-model="contractAdd.ifPurchase" autocomplete="off">
          <el-option label="无需采购" value="0"></el-option>
          <el-option label="需要采购" value="1"></el-option>
        </el-select>
      </el-form-item>
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

// 添加用户对象
const contractAdd = reactive({
  contractName: '',
  contractDesc: '',
  contractState: '',
  associatedArea: '',
  productId:'',
  materialId: '',
  productNum: '',
  ifPurchase:''
});

// 关闭
const close = () => {
  contractAdd.contractName = '';
  contractAdd.contractDesc = '';
  contractAdd.contractState = '';
  contractAdd.associatedArea = '';
  visible.value = false;
  showRatio.value = false;
  contractAdd.productId = "";
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

// 该对话框打开
const open = () => {
  visible.value = true;
};

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
      post('/activiti/start-instance', contractAdd).then(result => {
        emit('ok');
        tip.success(result.message);
        visible.value = false; // 关闭对话框
      });
    }
  });
}

// 日期格式化函数
function formatDate(dateStr) {
  const date = new Date(dateStr);
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, '0');
  const day = String(date.getDate()).padStart(2, '0');
  const hours = String(date.getHours()).padStart(2, '0');
  const minutes = String(date.getMinutes()).padStart(2, '0');
  const seconds = String(date.getSeconds()).padStart(2, '0');
  return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
}


// 获得所有产品
const productList = ref();
const getProductList= () => {
  get("/product-material/product-list").then(result => {
    productList.value = result.data;
  });
}
getProductList();

// 添加文件时的回显
const handleAvatarChange = (uploadFile) => {
  const reader = new FileReader();
  // 从上传组件中获取数据
  const image = uploadFile.raw;
  reader.readAsDataURL(image);
  // 读取文件的回调函数
  reader.onload = () => {
    // 将转化的url赋值给文件
    imageUrl.value = reader.result;
    contractAdd.files = image.name;
  };
}

// 上传之前做简单验证
const beforeAvatarUpload = (rawFile) => {
  if (rawFile.type !== 'image/jpg'
      && rawFile.type !== 'image/jpeg'
      && rawFile.type !== 'image/png'
      && rawFile.type !== 'image/gif'
      && rawFile.type !== 'image/svg'
      && rawFile.type !== 'image/webp'
  ) {
    tip.error('只能上传图片格式!');
    return false
  } else if (rawFile.size / 1024 / 1024 > 5) {
    tip.error('上传文件不能大于5MB!');
    return false
  }
  return true;
}

// 配料比列表
const ratioDetails= ref({materialId:'',materialName:''});

// 材料比例可见性
const showRatio = ref(false)

// 选择合同所需的产品并查询到相应的配料比
const handleSelectChange = () => {
  let productId = contractAdd.productId
  get(`/product-material/ratio/${productId}`).then(result => {
    ratioDetails.value = result.data;
    showRatio.value = true
  });
}

// 改变材料时，算出生产所需要的材料用量以及仓库剩余的材料数量
const needNum = ref(0);
const materialNum = ref();
const handleSelectMaterial = () => {
  materialNum.value = ratioDetails.value.find(item => item.materialId === contractAdd.materialId).materialNum
  needNum.value = ratioDetails.value.find(item => item.materialId === contractAdd.materialId).ratio * contractAdd.productNum
  console.log(contractAdd.productNum)
}

defineExpose({open});
</script>
<style scoped>
.avatar-uploader, .avatar-uploader .avatar{
  width: 100px;
  height: 100px;
  display: block;
}
</style>