<template>
  <!-- 查看员工考勤详细信息框 -->
  <el-dialog v-model="visible" title="当前员工考勤信息" width="35%">
    <el-form ref="attendanceDetilRef" :model="attendanceDetil" :rules="rules" label-position="top">
      <el-form-item label="员工姓名：" prop="userName">
        <el-input v-model="attendanceDetil.userName" autocomplete="off" disabled/>
      </el-form-item>
      <el-form-item label="考勤总天数：" prop="attendanceDays">
        <el-input v-model="attendanceDetil.attendanceDays" autocomplete="off" disabled/>
      </el-form-item>
      <el-form-item label="缺勤天数：" prop="absenceDays">
        <el-input v-model="attendanceDetil.absenceDays" autocomplete="off" disabled/>
      </el-form-item>
      
      <el-form-item>
        <el-calendar>
          <template #dateCell="{ data }">
            <div :class="data.isSelected ? 'is-selected' : ''">
              {{ data.day.split('-').slice(1).join('-') }}
              
            </div>
            <div v-for="item in currentDate">
              <p v-if="data.day == item.date">{{item.status}}</p>
              <p v-if="data.day == item.date">{{item.address}}</p>
            </div>
          </template>
        </el-calendar>
      </el-form-item>
      <el-form-item label="今月缺勤日期：" prop="absenceMonthDays">
        <el-input v-model="attendanceDetil.absenceMonthDays" autocomplete="off" type="textarea"
                  disabled
                  :rows="3" />
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
import {get} from "@/common";

const visible = ref(false); // 该页面的可见性
// 查看考勤对象
const attendanceDetil = reactive({
  userId:0,
  userName: '',
  attendanceDays: '',
  absenceDays: 0,
  absenceMonthDays: '',
});

const currentDate = ref([])

// 该对话框打开并初始化
const open = (attendance) => {
  attendanceDetil.absenceDays = 0
  for (let prop in attendance) {
    console.log(attendance[prop])
    attendanceDetil[prop] = attendance[prop];
  }
  let data = {"userId":attendance.userId}
  get("/checkin/check-date", data).then(result => {
    console.log(result)
    let dateList = ""
    currentDate.value = result.data
    for (let p in result.data){
      if(result.data[p].status=="缺勤"){
        console.log(result.data[p].date)
        dateList+=result.data[p].date+"，"
        attendanceDetil.absenceDays+=1
      }
    }
    console.log(dateList.slice(0,-1))
    attendanceDetil["absenceMonthDays"] = dateList.slice(0,-1)
  });

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
.is-selected {
  color: #1989fa;
}

::v-deep .el-calendar-table:not(.is-range) td.next {
  display: none;
}

::v-deep .el-calendar-table:not(.is-range) td.prev {
  visibility: hidden;
}

::v-deep .el-calendar__button-group {
  display: none;
}
</style>