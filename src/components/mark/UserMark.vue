<template>
<div>
  <el-container >
   <!-- 导航栏 -->
    <el-header style="margin-left=-50px"> 
    <br>
      <el-row>
      <el-col :span="12"><div><el-button type="text"  icon="el-icon-s-check" class="header">在线事件标注工具</el-button></div></el-col>
      <el-col :span="3"><div @click="goHello"><el-button type="text" icon="el-icon-s-home" class="header">主页</el-button></div></el-col>
      <el-col :span="3"><div @click="goUser"><el-button type="text" icon="el-icon-user-solid" class="header">登录</el-button></div></el-col>
      <el-col :span="3"><div @click="goRegister"><el-button type="text" icon="el-icon-user" class="header">注册</el-button></div></el-col>
      <el-col :span="3"><div @click="goHello"><el-button type="text" icon="el-icon-switch-button" class="header">注销</el-button></div></el-col> 
      </el-row>
    </el-header>
    <br>
  </el-container>
    <!-- 标注界面 -->
    
    <el-container style="height: 100%; border: 1px solid #eee">
        <el-aside width="250px">
          <!--  
          <div class="headblock"><h1>{{filename}}</h1></div> -->
    <!-- 深蓝色的标题区域 -->
      <div class="sel">
        <br>
        选择事件类型：
        <el-select id="select_1" autocomplete="off" @change="mySelection()">
          <option value="会见会谈" selected="selected">会见会谈</option>
          <option value="签署文件">签署文件</option>
          <option value="设施启用">设施启用</option>
          <option value="举行活动">举行活动</option>
        </el-select>
      </div>
      <br>
      <!-- 内容区 -->
      
      <el-button round>触发词</el-button>
      <br><br>
      <el-button id="get" @click="get" round>参与方</el-button>
      <br><br>
      <el-button  @click="time" round>时 间</el-button>
      <br><br>
      <el-button type="success"  icon="el-icon-success" @click="save" round>保存</el-button>
      </el-aside>

      <div class="mark">
        <el-main>
          <div class="user_mark_place">
            <div v-html = 'filecontent' class="text_content" id="test" contenteditable="true" ></div>
            <div id="allfile" style="display:none"></div>
          </div>
        </el-main>
    </div>
  
    </el-container>
    </div>

</template>

<script>
export default {
  name: 'UserMark',
  data(){
    return{
      filecontent:localStorage.getItem('userfilecontent'),
      filename: localStorage.getItem('name_usermark'),

    }
  },
  methods:{
    //页面跳转
     goHello(){
      this.$router.push('/Hello')
    },
  	goUser() {
  		this.$router.push('/User')
    },
    goRegister() {
      this.$router.push('/Register')
    },
    //  标注参与方
    get(){
        var myField = document.getElementById("test");
        var myrange = window.getSelection().getRangeAt(0);//  找到选区
        console.log(window.getSelection().getRangeAt(0));
        var startoffset = myrange.startOffset;
        var endoffset = myrange.endOffset;
        var selectedText = window.getSelection().toString();//  将选区内容转化为字符串存在selectedText变量中
        myrange.deleteContents();// 删除原有的文本
        var para=document.createElement("div");//  用新建的节点代替
        var sub=document.createElement("span");//  用新建的节点代替
        var btn=document.createElement("button");//  用新建的节点代替
        var node=document.createTextNode(selectedText);
        var val=document.createTextNode("删除");
        sub.appendChild(node);
        btn.appendChild(val);
        para.style.color = "blue";
        para.setAttribute("class","get"); // 节点的属性是参与方
        myrange.insertNode(para); 
        myrange.setStartAfter(para);

        //  获取选区内容的起止位置
        var ele = para.previousElementSibling;
        if(ele){
          startoffset += parseInt(ele.getAttribute("END"));
          endoffset += parseInt(ele.getAttribute("END"));
          para.setAttribute("START",startoffset);
          para.setAttribute("END",endoffset);
          console.log(para);
        }else{
          para.setAttribute("START",startoffset);
          console.log(para);
          para.setAttribute("END",endoffset);  
        }
    },
    time(){
      var myField = document.getElementById("test");
      var myrange = window.getSelection().getRangeAt(0);//  找到选区
      console.log(window.getSelection().getRangeAt(0));
      var startoffset = myrange.startOffset;
      var endoffset = myrange.endOffset;
      var selectedText = window.getSelection().toString();//  将选区内容转化为字符串存在selectedText变量中
      myrange.deleteContents();// 删除原有的文本
      var para=document.createElement("span");//  用新建的节点代替
      var node=document.createTextNode(selectedText);
      para.appendChild(node);
      para.style.color = "green";
      para.setAttribute("class","time"); //节点属性是参与时间
      myrange.insertNode(para); 
      myrange.setStartAfter(para);

      //  获取选区内容的起止位置
      var ele = para.previousElementSibling;
      if(ele){
        startoffset += parseInt(ele.getAttribute("END"));
        endoffset += parseInt(ele.getAttribute("END"));
        para.setAttribute("START",startoffset);
        para.setAttribute("END",endoffset);
        console.log(para);
      }else{
        para.setAttribute("START",startoffset);
        console.log(para);
        para.setAttribute("END",endoffset);  
      }
    },

    //  保存用户当前的编辑，以及计算编辑之后文件的一致性
    save(){
      const self = this;                      
      var filename = localStorage.getItem('name_usermark');
      var username = localStorage.getItem('ms_username');
      var myField = document.getElementById("test");
      var filecontent = myField.innerHTML;
      var data = {'filename': filename, 'username':username, 'filecontent':filecontent}
      // console.log(data);
        this.$axios.post('/api/content/usersave', data).then((response)=>{
            console.log('用于检测一致性的文件个数' + response.data);
            var k = 0;  //和当前节点有交集的节点数
            var m = 0;  //有交集计算一致性
            var n = 0;  //全文的一致性
            if(response.data == 1)
            {
              n = 1;
            }
            else
            {
              var allfile = response.data;
              document.getElementById("allfile").innerHTML = allfile;
              // 一致性检测
              var getnode = document.getElementById("allfile").getElementsByClassName("get");
              var temp = new Array();
              for(var i=0;i<getnode.length;i++)
              {
                var mynode1 = getnode[i];
                var start1 = mynode1.getAttribute("start");
                var end1 = mynode1.getAttribute("end");
                for(var j=0;j<getnode.length;j++)
                {
                  if(j!=i)
                  {
                    var mynode2 = getnode[j];
                    var start2 = mynode2.getAttribute("start");
                    var end2 = mynode2.getAttribute("end");
                    if(start2<end1)
                    {
                      m = m+((end1-start2)/(end2-start1));
                      k++;
                    }
                    else if(start1<end2)
                    {
                      m = m+((end2-start1)/(end1-start2));
                      k++;
                    }
                    else if((start1>=start2)&&(end1<=end2))
                    {
                      m = m+((end1-start1)/(end2-start2));
                      k++;
                    }
                    else if((start1<start2)&&(end2<end1))
                    {
                      m = m+((end2-start2)/(end1-start1));
                      k++;
                    }
                  }
                }
                if(k==0)
                {
                  temp[i]=0;
                }
                else
                {
                  temp[i]=m/k;
                }
                n+=temp[i];
              }
              n = n/getnode.length;
            }

            console.log(n);
            this.$options.methods.storeK(n,this);
        }).then((error) => {
            console.log(error);
        })
    },
    //  将计算出来的k值传到后端，保存到数据库中
    storeK(n,that){
      console.log(n)
      var filename = localStorage.getItem('name_usermark');
      that.$axios.post('/api/content/setk', {'k_value': n, 'filename': filename}).then((response)=>{
        console.log(response);
        }).then((error) => {
            console.log(error);
        })
    }
  }
}
</script>

<style scoped>
          .el-row {
            background-color:#9b95c9;
            min-height: 50px;
            width: 100%;
          } 
          .bg-purple {
            background: #9b95c9;
            width: 100%;
          }
          .el-header {
            background-color:#9b95c9;
            height:300px;
            width:100%;
            font-family:'Courier New', Courier, monospace;
            font-weight:normal;
            text-align: left;
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
          *{
            margin:0px;
            padding:0px;
        }

          .el-aside{
            height: 400px;
            margin-top: 7px;
          }
          
          .el-main {
            background-color:#E6E6FA;
            color: #333;
            text-align: center;
            height: 400px;
            line-height: 100%;
            margin-top: 7px;
          }
          .mark{
             width: 100%;
        }
        .text_content{
          width: 100%;
          height: 400px;
          text-align: left;
          display: inline-block;
          background-color: aliceblue;
        }
        
        .headblock{
          height: 80px;
        }

       .el-footer {
          background-color: #B3C0D1;
          color: #333;
          text-align: center;
          line-height: 60px;
        }
        body > .el-container {
          margin-bottom: 40px;
        }
        
        .el-container:nth-child(5) .el-aside,
        .el-container:nth-child(6) .el-aside {
          line-height: 260px;
        }
        
        .el-container:nth-child(7) .el-aside {
          line-height: 320px;
        }

        .el-tag + .el-tag {
          margin-left: 10px;
        }
        .button-new-tag {
          margin-left: 10px;
          height: 32px;
          line-height: 30px;
          padding-top: 0;
          padding-bottom: 0;
        }
        .input-new-tag {
          width: 90px;
          margin-left: 10px;
          vertical-align: bottom;
        }
</style>
