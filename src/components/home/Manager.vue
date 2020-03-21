<template>
<body>
  <el-container>
    <el-header> 
      <br><br>
      <el-row>
      <el-col :span="15"><div @click="goHello"><el-button type="text"  icon="el-icon-s-check" class="header">在线事件标注工具</el-button></div></el-col>
      <el-col :span="3"><div @click="goHello"><el-button type="text" icon="el-icon-s-home" class="header">主页</el-button></div></el-col>
      <el-col :span="3"><div @click="goUser"><el-button type="text" icon="el-icon-user-solid" class="header">登录</el-button></div></el-col>
      <el-col :span="3"><div @click="goRegister"><el-button type="text" icon="el-icon-user" class="header">注册</el-button></div></el-col>
      <el-col :span="24"><div class="bg-purple"></div></el-col>
      </el-row>
    </el-header>
    <br>
    <el-container>
      <el-main>
        <div id="div_login">
          <div id="div_title"><h1>管理员登录</h1></div>
          <el-divider></el-divider>
          <div id="midden">
             <el-col :span="8"><div @click="goManager"><el-button type="text" class="el-menu-demo">管理员</el-button></div></el-col>
              <el-col :span="8"><div @click="goLeader"><el-button type="text" class="el-menu-demo">用户组长</el-button></div></el-col>
              <el-col :span="8"><div @click="goUser"><el-button type="text" class="el-menu-demo">普通用户</el-button></div></el-col>
            <br>
            <input placeholder="请输入管理员账号" type="text" name="userName" class="inputinfo"/>
            <br>
            <input placeholder="请输入密码" type="text" name="password" v-model="ruleForm.password" class="inputinfo"/>
            <br>
            <el-button  value="登录" @click="goM" class="submitbutton_login" type="warning">登录</el-button>
            <el-button  value="注册" @click="open2" class="submitbutton_registered" type="warning">注册</el-button>
          </div>
        </div>
    </el-main>
  </el-container>
</el-container>
</body>
</template>

<script>

export default {
  name: 'Manager',
  data () {
    return {
        errorInfo : false,
        ruleForm: {
            username: '',
            password: '',                   
        },
        rules: {
            username: [
                { required: true, message: '请输入用户名', trigger: 'blur' }
            ],
            password: [
                { required: true, message: '请输入密码', trigger: 'blur' }
            ],
        }
    }
  },
  methods: {
    goHello(){
      this.$router.push('/Hello')
    },
  	goUser() {
  		this.$router.push('/User')
    },
    goLeader() {
  		this.$router.push('/Leader')
    },
  	goManager() {
  		this.$router.push('/Manager')
    },
    goRegister() {
      this.$router.push('/Register')
    },
    goM(){
      this.$router.push('/M')
    },
        open2() {
        this.$alert('普通用户才可以注册!', '注意⚠️', {
          confirmButtonText: '确定',
          }
        )},
    submitForm(ruleForm) {
      const self = this;
      localStorage.setItem('ms_username',self.ruleForm.username);
      localStorage.setItem('ms_user',JSON.stringify(self.ruleForm));
      console.log(JSON.stringify(self.ruleForm));                        
      self.$axios.post('/api/user/findUser',self.ruleForm) //前端接口
      .then((response) => {
          console.log(response);
          if (response.data == -1) {
              self.errorInfo = true;
              self.errInfo = '该用户不存在';
              console.log('该用户不存在')
          } else if (response.data == 0) {
              console.log('密码错误')
              self.errorInfo = true;
              self.errInfo = '密码错误';
              this.$alert('密码错误', '注意⚠️', {
          confirmButtonText: '确定',})
          }
           else {
              this.$router.push('/L');
          }                          
      }).then((error) => {
          console.log(error);
      })
    }

  }
}
</script>

<style scoped>
          .el-row {
            margin-bottom: 20px;
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
          .el-main {
            background-color:#E6E6FA;
            color: #333;
            text-align: center;
            height: 100%;
            line-height: 100%;
          }
        #div_login{
            width:500px;
            height:400px;
            background-color:rgba(97, 89, 89, 0.5);
            float:center;
            border-top-left-radius:10px;
            border-top-right-radius:10px;
            border-bottom-right-radius:10px;
            border-bottom-left-radius:10px;
            position: absolute;
            top: 50%;
            left: 50%;
            -webkit-transform: translate(-50%, -50%);
            -moz-transform: translate(-50%, -50%);
            -ms-transform: translate(-50%, -50%);
            -o-transform: translate(-50%, -50%);
            transform: translate(-50%, -50%);
                   }
        #div_title{
            font-family:"华文黑体";
            font-size: 25px;
            width: 100%;
            height: 60px;
            text-align: center;
            line-height:60px;
            color:white;
        }
        *{
            margin:0px;
            padding: 0px;
        }
        #midden{
            font-family: "华文黑体";
            border-top-left-radius:10px;
            border-top-right-radius:10px;
            border-bottom-right-radius:10px;
            border-bottom-left-radius:10px;
            float:center;
            color:white;
        }
        .el-menu-demo{
            color:white;
            font-weight:normal;
            font-size: 20px;
            min-height: 40px;
          }
        .inputinfo{
            text-indent: 10px;
            width: 350px;
            height:35px;
            margin:40px 50px 10px 50px;
            font-size: 15px;
            border-top-left-radius:5px;
            border-top-right-radius:5px;
            border-bottom-right-radius:5px;
            border-bottom-left-radius:5px;
        }
        .submitbutton_login{
            font-family: "华文黑体";
            font-size: 20px;
            width: 260px;
            height:35px;
            margin:10px;
            background-color:#BC8F8F;
            border-top-left-radius:5px;
            border-top-right-radius:5px;
            border-bottom-right-radius:5px;
            border-bottom-left-radius:5px;
        }
        .submitbutton_registered{
            font-family: "华文黑体";
            font-size: 20px;
            width: 260px;
            height:35px;
            margin:20px 20px 20px 20px;
            background-color:#BC8F8F;
            border-top-left-radius:5px;
            border-top-right-radius:5px;
            border-bottom-right-radius:5px;
            border-bottom-left-radius:5px;
        }
</style>