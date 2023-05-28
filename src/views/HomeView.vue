<template>
<div>
<el-form :inline="true">
  <el-form-item >
    <el-button type="primary" @click="start">开始采集</el-button>
    </el-form-item>
  <el-form-item>
    <el-button type="danger" @click="stop">结束采集</el-button>
  </el-form-item>
  <el-form-item label="时刻">
    <el-date-picker
        v-model="coordinate.time"
        type="datetime"
        placeholder="选择日期时间">
    </el-date-picker>
  </el-form-item>
  <el-form-item label="纬度">
    <el-input v-model="coordinate.latitude" ></el-input>
  </el-form-item>
  <el-form-item label="经度">
    <el-input v-model="coordinate.longitude" >
    </el-input>
  </el-form-item>
  <el-form-item>
    <el-button type="primary" @click="search">查看结果</el-button>
  </el-form-item>
</el-form>
  <el-table :data="data">
    <el-table-column label="用户id" prop="user_id"></el-table-column>
    <el-table-column label="经度" >
      <template slot-scope="scope">
        <span>{{scope.row.location.coordinates[0].toFixed(2)}}</span>
      </template>
    </el-table-column>
    <el-table-column label="纬度" >
      <template slot-scope="scope">
        <span>{{scope.row.location.coordinates[1].toFixed(2)}}</span>
      </template>
    </el-table-column>
    <el-table-column label="时间" prop="timestamp"></el-table-column>
  </el-table>
</div>
</template>

<script>


import axios from "axios";

export default {
  name: 'HomeView',
  components: {
  },
  data(){
    return{
      timer:'',
      data:[],
      coordinate:{
        time:new Date(),
        latitude: '',
        longitude: ''
      }
    }
  },methods:{
    search(){
      let path='http://127.0.0.1:5000/search'
      axios.get(path,{params:this.coordinate}).then(res=>{
        if (res.data.code===200){
          this.data=res.data.data
          console.log(this.data[0].location.coordinates)
          if (this.data.length===0){
            this.$message.error('没有数据')
          }
        }
      })
    },
    start() {
      this.$message.success('采集已开始');
      this.timer = setInterval(this.collectData,  5000);
    },
    collectData() {
      let path = 'http://127.0.0.1:5000/generate_data';
      axios.get(path).then(res => {
        if (res.data.code === 200) {
          console.log('数据采集成功');
        }
      }).catch(error => {
        console.error('数据采集失败', error);
      });
    },
    stop() {
      clearInterval(this.timer);
      this.$message.success('采集已停止');
    },
  },

  beforeDestroy() {
    clearInterval(this.timer);
  }
}
</script>
