<template>
  <div class="index">
    <el-container>
      <el-header>
        <p>测试</p>
      </el-header>
      <el-container>
        <el-container>
          <el-main>
            <div id="res"></div>
            <el-input id="songName" v-model="inp" placeholder="输入歌曲名称点歌"></el-input>
            <el-button plain @click="open(addSo)">点歌成功</el-button>
          </el-main>
          <el-main>
            我是评论区<br>
            <div id="diss"></div>
            <el-input v-model="disInp" placeholder="输入消息来评论咯">

            </el-input>
            <el-button plain @click="open(dis)">评论成功</el-button>
          </el-main>

        </el-container>
      </el-container>
    </el-container>

  </div>
</template>

<script>
  export default {
    name: 'Index',
    data() {
      return {
        reqDto:{
          controlType:1,
          content:'菊花台'
        },
        inp: "",
        disInp: "",
        addSo: "addSo",
        dis: "dis",
      }
    },
    methods: {
      open(type) {
        const h = this.$createElement;
        if (type == "addSo"){
          this.$notify({
            title: '点歌成功咯',
            message: h('i', { style: 'color: teal'}, "刚刚您点歌"+ this.inp + "成功咯"),
          });
          send(this.inp,1)
        }else {
          this.$notify({
            title: '评论成功咯',
            message: h('i', { style: 'color: teal'}, "刚刚您评论"+ this.disInp + "已经被所有小伙伴看到咧"),
          });
          send(this.disInp,2)
        }
      },
      open2() {
        this.$notify({
          title: '提示',
          message: '这是一条不会自动关闭的消息内容',
          duration: 0
        });
      },
      open3() {
        this.$notify({
          title: '成功',
          message: '这是一条成功的提示消息',
          type: 'success'
        });
      },
    }
  }

  var websocket = null;

  function RndId(n){
    var rnd="";
    for(var i=0;i<n;i++)
      rnd+=Math.floor(Math.random()*10);
    return rnd;
  }
  //判断当前浏览器是否支持WebSocket
  if ('WebSocket' in window) {
    websocket = new WebSocket("ws://192.168.1.134:8086/websocket/" + RndId(5));
  }
  else {
    alert('Not support websocket')
  }

  //连接发生错误的回调方法
  websocket.onerror = function () {
    setMessageInnerHTML("哎呀出现异常了1");
  };

  //连接成功建立的回调方法
  websocket.onopen = function (event) {
    setMessageInnerHTML("连接服务器成功1");
  }

  //接收到消息的回调方法
  websocket.onmessage = function (event) {
    console.log(event);
    setMessageInnerHTML(event.data);
  }

  //连接关闭的回调方法
  websocket.onclose = function () {
    setMessageInnerHTML("服务器已经关闭了1");
  }

  //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
  window.onbeforeunload = function () {
    websocket.close();
  }

  //将消息显示在网页上
  function setMessageInnerHTML(innerHTML) {
    if (innerHTML.substring(innerHTML.length-1,innerHTML.length) === '1') {
      document.getElementById('res').innerHTML += innerHTML.substring(0,innerHTML.length-1) + '<br/>';
    }else {
      var aa = document.getElementById('diss').innerHTML.toString();
      var newStr = aa;
      var lastStr = innerHTML.substring(0,innerHTML.length-1) + "<br/>" + newStr;
      document.getElementById('diss').innerHTML = lastStr;
  }
  }

  //关闭连接
  function closeWebSocket() {
    websocket.close();
  }

  //发送消息
  function send(var1,var2) {
    var req = JSON.stringify(
      {controlType:var2,
        content:var1});
    websocket.send(req);
  }
</script>
<style scoped>
  .el-input{
    width: 200px;
  }
  .el-header, .el-footer {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }

  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 100px;
  }

  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 50px;
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
</style>
