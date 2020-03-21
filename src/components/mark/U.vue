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
  <!--<h1>亲爱的{{username}},您好!</h1>-->
  <el-container style="height: 100%; border: 1px solid #eee">

  <!-- 左侧的导航栏 -->
  <el-aside width="250px" style="background-color: rgb(211, 211, 248)">
    <el-menu :default-openeds="['1', '3']">
      <el-submenu index="1">
        <template slot="title"><i class="el-icon-document"></i>数据集</template>
          <el-button type="text" size="mini" icon="el-icon-more">选择文本</el-button>
          <el-button type="text" size="mini" icon="el-icon-download">确认下载</el-button>
        </el-submenu>   
      <el-menu-item index="2" @click="usershowdata" ><i class="el-icon-error"></i>待完成本文</el-menu-item>
      <el-menu-item index="3"><i class="el-icon-success"></i>已标注文本</el-menu-item>  
      
    </el-menu>
  </el-aside>
  
  <el-container>
    <!--<el-header style="text-align: right; font-size: 12px">
    </el-header>-->
    
    <!-- 文件显示主体部分 -->
      <el-main >
        <el-table :data="tableData">
          <el-table-column prop="filename" label="文件名" width="750">
          </el-table-column>

        <el-table-column prop="todo" label="状态" >
          <template slot-scope="scope">
            <el-button @click="handleClick(scope.row)" type="text" size="small">已编辑</el-button>
          </template>
        </el-table-column>

        <el-table-column prop="todo" label="操作" >
          <template slot-scope="scope">
            <el-button @click="handleClick(scope.row)" type="text" size="small">编辑</el-button>
          </template>
        </el-table-column>

        <el-table-column prop="todo" label="导出为XML" >
          <template slot-scope="scope">
            <el-button @click="xmlClick(scope.row)" type="text" size="small">导出</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-main>
  </el-container>
</el-container>
<!-- 用来生成xml文件，寻找文件节点时使用 -->
<div v-html = 'filetxt' id="filexml" style="display:none" contenteditable="true" ></div>

</div>

</template>

<script>

export default {
  name: 'U',
  mounted :function() {
    this.usershowdata();
  },
  data () {
    return {
      tableData: [],
      userfilepath :'cc',
      username: localStorage.getItem('ms_username'),
      filetxt: '',
    }
  },
  methods:{
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
    //  打开对应的行的文本文件进行编辑
    handleClick(row) {
      const self = this;                      
      console.log(row.filename);
      this.userfilepath = row.filepath;
      let userfilepath = this.userfilepath; //  原数据保存的路径
      localStorage.setItem('name_usermark',row.filename); //  txt文件的名字
      localStorage.setItem('path_usermark',row.filepath);
      var username = localStorage.getItem('ms_username'); // 当前登录用户的名字
      var filename = row.filename;
      var data = {'userpath': userfilepath, 'filename': filename, 'username': username}
      console.log(data);
        this.$axios.post('/api/content/abc', data).then((response)=>{
            console.log(response.data);
            localStorage.setItem('userfilecontent',response.data);
            this.$router.push('/UserMark');
        }).then((error) => {
            console.log(error);
        })
    },

    //用户界面显示组长上传的数据集
    usershowdata()
    {
      const self = this;                      
        self.$axios.post('/api/user/usershowdata') //前端接口
        .then((response) => {
            console.log(response);
            console.log(response.data);
            //console.log(response.data[1].id);
            this.tableData = response.data;
            
        }).then((error) => {
            console.log(error);
        })
    },

    // 创建并下载xml文件
    xmlClick(row) {
      const self = this;                      
      console.log(row.filename);
      let filename = row.filename;
      let filepath = row.filepath;
      let username = localStorage.getItem('ms_username'); 

      var data = {'userpath': filepath, 'filename': filename, 'username': username}
      this.$axios.post('/api/content/abc', data).then((response)=>{
        console.log(response.data);// 请求到的用户标注过的文本
        localStorage.setItem('filetxt',response.data);
        this.filetxt = response.data;

        var getnode = document.getElementsByClassName("get");
        var xmldom = document.implementation.createDocument("", "root", null); // 创建xml对象
        // console.log(xmldom.documentElement.tagName); //"root"
        for(var i=0;i<getnode.length;i++)
        {
          var child = xmldom.createElement("event_argument");
          var nodetxt = document.createTextNode(getnode[i].innerHTML);
          var start = getnode[i].getAttribute("start");
          var end = getnode[i].getAttribute("end");
          xmldom.documentElement.appendChild(child);
          child.appendChild(nodetxt);
          child.setAttribute("START", start);
          child.setAttribute("END", end);
          child.setAttribute("ROLE", "参与方");
        }

        var serializer = new XMLSerializer();
        var xml = serializer.serializeToString(xmldom);// 将xml解析为dom
        console.log(xml);

        let a = document.createElement('a');
        let blob = new Blob([xml]); 
        let objectUrl = URL.createObjectURL(blob);
        a.setAttribute("href",objectUrl);
        a.setAttribute("download", '123.xml');
        a.click();

        console.log(xmldom); 
        }).then((error) => {
            console.log(error);
        })



      // console.log(1);
      // //创建一个根节点，并添加到xml
      // var Root = doc.createElement("root");
      // doc.appendChild(Root);
      // var Row = doc.createElement("row");
      // Root.appendChild(Row);
      // //向根节点添加属性，setAttribute(key , value);
      // Row.setAttribute("operation", "search");
      // alert(doc.xml)
    },
  },  
}
</script>

<style scoped>
          .el-row {
            background-color:#9b95c9;
            min-height: 50px;
            width:100%;
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

