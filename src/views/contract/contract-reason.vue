<template>
  <!-- 添加合同对话框 -->
  <el-dialog v-model="visible" title="添加合同" width="800px" @close="close" destroy-on-close>
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

// 关闭
const close = () => {
  visible.value = false;
}

const rules = reactive({
  contractName: [
    {required: true, message: '请输入合同名', trigger: 'blur'}
  ],
})

// 该对话框打开
const open = (data) => {
  visible.value = true;
  console.log(data)
};

defineExpose({open});
</script>
<style scoped>
.avatar-uploader, .avatar-uploader .avatar{
  width: 100px;
  height: 100px;
  display: block;
}
</style>