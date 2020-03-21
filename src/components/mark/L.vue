<template>
<div>
  <el-container>
    <!-- 导航栏 -->
    <el-header> 
      <br>
      <el-row>
      <el-col :span="12"><div><el-button type="text"  icon="el-icon-s-check" class="header">在线事件标注工具</el-button></div></el-col>
      <el-col :span="3"><div @click="goHello"><el-button type="text" icon="el-icon-s-home" class="header">主页</el-button></div></el-col>
      <el-col :span="3"><div @click="goUser"><el-button type="text" icon="el-icon-user-solid" class="header">登录</el-button></div></el-col>
      <el-col :span="3"><div @click="goRegister"><el-button type="text" icon="el-icon-user" class="header">注册</el-button></div></el-col>
      <el-col :span="3"><div @click="goHello"><el-button type="text" icon="el-icon-switch-button" class="header">注销</el-button></div></el-col>
      <el-col :span="24"><div class="bg-purple"></div></el-col>
      </el-row>
    </el-header>
    <br>
  </el-container>
  <el-container style="height: 100%; border: 1px solid #eee">
    <!-- 侧边栏 -->
    <el-aside width="250px" style="background-color: rgb(211, 211, 248) margin-top=“7px">
      <el-menu :default-openeds="['1', '4']">
          <el-submenu index="4">
              <template slot="title"><i class="el-icon-upload"></i>上传文件</template>        
              <input type="file" @change="getFile($event)">
              <el-button @click="submitForm($event)" type="text" size="mini" icon="el-icon-success">确认上传</el-button>           
          </el-submenu> 
          <el-submenu index="1">
              <template slot="title"><i class="el-icon-s-custom"></i>新闻小组</template>
              <el-menu-item-group>
                 <template slot="title">小组信息</template>
                <el-menu-item index="1-1" @click="goMarked"><i class="el-icon-document-checked"></i>已标注数据集</el-menu-item>
                <el-menu-item index="1-2" @click="goOringin"><i class="el-icon-document"></i>原始数据集</el-menu-item>
              </el-menu-item-group>
          </el-submenu>
          <el-submenu index="2">
              <template slot="title"><i class="el-icon-s-custom"></i>机器学习小组</template>
              <el-menu-item-group>
                <template slot="title">小组信息</template>
                <el-menu-item index="2-1" @click="goMarked"><i class="el-icon-document-checked"></i>已标注数据集</el-menu-item>
                <el-menu-item index="2-2" @click="goOringin"><i class="el-icon-document"></i>原始数据集</el-menu-item>
              </el-menu-item-group>
          </el-submenu>
              <el-submenu index="3">
                <template slot="title"><i class="el-icon-s-custom"></i>图像识别</template>
                <el-menu-item-group>
                <template slot="title">小组信息</template> 
                <el-menu-item index="3-1" @click="goMarked"><i class="el-icon-document-checked"></i>已标注数据集</el-menu-item>
                <el-menu-item index="3-2" @click="goOringin"><i class="el-icon-document"></i>原始数据集</el-menu-item>
                </el-menu-item-group>
          </el-submenu>
      </el-menu>
    </el-aside>
   
    <el-main>
    <!-- 展示数据集部分 -->

    <router-view></router-view>
    </el-main>
  </el-container>
</div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'L',
    data () {
      const item = {
        date: '2016-05-02',
        filename: '王小虎',
        type: '上海市普陀区金沙江路 1518 弄'
      };
      return {
          form:{
              title: 'title',
              content: 'content',
              username: localStorage.getItem('ms_username'),
              file:'',
          },
          tableData: []
      }
    },
    methods: {
        //导航栏跳转
        goHello(){
          this.$router.push('/Hello')
        },
        goUser() {
          this.$router.push('/User')
        },
        goRegister() {
          this.$router.push('/Register')
        },
        // post文件上传
        getFile(event){
            this.file = event.target.files[0];
            console.log(this.file);
        },
        submitForm(event){
            event.preventDefault();
            let formData = new FormData();
            formData.append('filename', this.file.name);
            formData.append('name', this.form.username);
            formData.append('file', this.file);
            let config = {
              headers: {
                'Content-Type': 'multipart/form-data'
              }
            }
            axios.post('http://127.0.0.1:3000/api/user/postFile', formData, config)
            .then(res=>{
                console.log(res)
            }).catch(err=>{
                console.log(err)
            })
        },
        // 展示原始数据集
        goOringin(){
          this.$router.push('/L/database');
        },
        // 展示标注的数据集
        goMarked(){
          this.$router.push('/L/datamarked');
        },
    },
}
</script>





<style scoped>
         .el-row {
            background-color:#9b95c9;
            min-height: 50px;
          }
          .bg-purple {
            background: #9b95c9;
          }
          .header{
            text-indent: 20px;
            color:white;
            font-weight:normal;
            font-size: 20px;
            background-color:#9b95c9;
            min-height: 50px;
            margin-bottom: 20px;
          }
        .el-header {
            background-color:#9b95c9;
            height:300px;
            font-family:'Courier New', Courier, monospace;
            font-weight:normal;
            text-align: left;
          }
          .el-aside{
            background-color: rgb(211, 211, 248);
            margin-top: 7px;
          }
          .el-main {
            background-color:#E6E6FA;
            color: #333;
            text-align: center;
            height: 100%;
            line-height: 100%;
            margin-top: 7px;
          }
        *{
            margin:0px;
            padding: 0px;
            }
</style>

