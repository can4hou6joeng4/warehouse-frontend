<template>
  <div>
    <el-form ref="contractAddForm" :model="contractAdd" label-position="top">
      <el-form-item label="合同照片：" prop="imgs">
        <el-upload
            class="avatar-uploader"
            :action="WAREHOUSE_CONTEXT_PATH + '/file/upload-contract-annex'"
            :multiple="true"
            :on-change="handleAvatarChange"
            :before-upload="beforeAvatarUpload"
        >
          <el-icon class="avatar-uploader-icon"><Plus /></el-icon>
        </el-upload>
      </el-form-item>
    </el-form>
  </div>
</template>

<script setup>
import {onMounted, ref} from "vue";
import {WAREHOUSE_CONTEXT_PATH} from "@/common";
import {Plus} from "@element-plus/icons-vue";

const contractAdd = ref('');

onMounted(hook => {
  console.log("工程合同")
})

// 图片回显路径
const imageUrls = ref([]);
let imageList = ref([])
// 添加文件时的回显
const handleAvatarChange = (uploadFile) => {
  const reader = new FileReader();
  // 从上传组件中获取数据
  const image = uploadFile.raw;
  reader.readAsDataURL(image);
  // 读取文件的回调函数
  reader.onload = () => {
    // 将转化的url赋值给文件
    imageUrls.value.push(reader.result);
  };
  if(uploadFile.response != null){
    if (contractAdd.files == null || contractAdd.files == ""){
      contractAdd.files += uploadFile.response.message
    }else{
      contractAdd.files += "," + uploadFile.response.message
    }
  }
}

// 上传之前做简单验证
const beforeAvatarUpload = (rawFile) => {
  // if (rawFile.type !== 'image/jpg'
  //     && rawFile.type !== 'image/jpeg'
  //     && rawFile.type !== 'image/png'
  //     && rawFile.type !== 'image/gif'
  //     && rawFile.type !== 'image/svg'
  //     && rawFile.type !== 'image/webp'
  // ) {
  //   tip.error('只能上传图片格式!');
  //   return false
  // } else if (rawFile.size / 1024 / 1024 > 5) {
  //   tip.error('上传文件不能大于5MB!');
  //   return false
  // }
  return true;
}
</script>

<style scoped>

</style>