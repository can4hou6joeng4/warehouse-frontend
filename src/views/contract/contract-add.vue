<template>
  <!-- 添加合同对话框 -->
  <el-dialog v-model="visible" title="添加合同" width="25%" @close="close" destroy-on-close>
    <el-form ref="contractAddForm" :model="contractAdd" :rules="rules" label-position="top">
      <el-form-item label="合同名：" prop="contractName">
        <el-input v-model="contractAdd.contractName" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="合同描述：" prop="contractDesc">
        <el-input v-model="contractAdd.contractDesc" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="合同状态：" prop="contractState">
        <el-select v-model="contractAdd.contractState" autocomplete="off">
          <el-option label="未审核" value="0"></el-option>
          <el-option label="待结算" value="1"></el-option>
          <el-option label="结算中" value="2"></el-option>
          <el-option label="已结算" value="3"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="关联工区：" prop="associatedArea">
        <el-input v-model="contractAdd.associatedArea" autocomplete="off"/>
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
import {post, tip, WAREHOUSE_CONTEXT_PATH} from "@/common";
import { Plus } from '@element-plus/icons-vue'

const visible = ref(false); // 该页面的可见性

// 图片回显路径
const imageUrl = ref('');

// 添加用户对象
const contractAdd = reactive({
  contractName: '',
  contractDesc: '',
  contractState: '',
  associatedArea: ''
});

// 关闭
const close = () => {
  contractAdd.contractName = '';
  contractAdd.contractDesc = '';
  contractAdd.contractState = '';
  contractAdd.associatedArea = '';
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
      post('/contract/addContract', contractAdd).then(result => {
        emit('ok');
        tip.success(result.message);
        visible.value = false; // 关闭对话框
      });
    }
  });
}

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

defineExpose({open});
</script>
<style scoped>
.avatar-uploader, .avatar-uploader .avatar{
  width: 100px;
  height: 100px;
  display: block;
}
</style>