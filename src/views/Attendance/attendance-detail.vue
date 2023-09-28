<template>
  <!-- 查看员工考勤详细信息框 -->
  <el-dialog v-model="visible" title="当前员工考勤信息" width="25%">
    <el-form ref="attendanceDetilRef" :model="attendanceDetil" :rules="rules" label-position="top">
      <el-form-item label="员工姓名：" prop="userName">
        <el-input v-model="attendanceDetil.userName" autocomplete="off" disabled/>
      </el-form-item>
      <el-form-item label="考勤总天数：" prop="attendanceDays">
        <el-input v-model="attendanceDetil.attendanceDays" autocomplete="off" disabled/>
      </el-form-item>
      <el-form-item label="缺勤天数：" prop="absenceDays">
        <el-input v-model="attendanceDetil.attendanceDays" autocomplete="off" disabled/>
      </el-form-item>
      <el-form-item label="今月缺勤日期：" prop="absenceMonthDays">
        <el-input v-model="attendanceDetil.absenceMonthDays" autocomplete="off" disabled/>
      </el-form-item>
    </el-form>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="visible=false">取消</el-button>
        <el-button type="primary" @click="updateContract">确定</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup>
import {reactive, ref} from 'vue'

const visible = ref(false); // 该页面的可见性
// 查看考勤对象
const attendanceDetil = reactive({
  userName: '',
  attendanceDays: '',
  absenceDays: 0,
  absenceMonthDays: '',
});


// 该对话框打开并初始化
const open = (attendance) => {
  for (let prop in attendance) {
    console.log(attendance[prop])
    attendanceDetil[prop] = attendance[prop];
  }
  visible.value = true;
};

const attendanceDetilRef = ref();

defineExpose({open});
</script>
<style scoped>
.avatar-uploader, .avatar-uploader .avatar {
  width: 100px;
  height: 100px;
  display: block;
}
</style>