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
          <el-option label="待结算" value="1"></el-option>
          <el-option label="结算中" value="2"></el-option>
          <el-option label="已结算" value="3"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="关联工区：" prop="associatedArea">
        <el-input v-model="contractAdd.associatedArea" autocomplete="off"/>
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
import {post, tip} from "@/common";

const visible = ref(false); // 该页面的可见性
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

defineExpose({open});
</script>
<style scoped>

</style>