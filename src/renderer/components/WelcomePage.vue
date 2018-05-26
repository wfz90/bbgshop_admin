<template>
<div class="content-page">
  <div class="content-nav">
    <el-breadcrumb class="breadcrumb" separator="/">
      <el-breadcrumb-item :to="{ path: '/dashboard' }">后台主页</el-breadcrumb-item>
    </el-breadcrumb>
  </div>
  <div class="content-main">
    <el-row style="text-align:center">
      <el-col :span="6">
      </el-col>
      <el-col :span="6">
      </el-col>
      <el-col :span="6">
      </el-col>
      <el-col :span="6" >
      </el-col>
    </el-row>
  </div>
</div>
</template>

<script>
export default {
  data() {
    return {
      dialogVisible: false
    }
  },
  components: {

  },
}
</script>

<style scoped>
.content_img {
  width: 30px;
  height: 30px;
  /* border:1px solid black; */
}
</style>
