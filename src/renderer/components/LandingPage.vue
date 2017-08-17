<template>
  <div id="wrapper">
    <h2> 测试windows 下载内存爆表问题 </h2>
    <input type="text" v-model="fileurl" />
    <br/>
    <div class="tl">{{fileurl}}</div>
    <button @click="doSomething">点我测试</button>
  </div>
</template>

<script>
import {remote} from 'electron'
import fs from 'fs'
import path from 'path'
import COS from 'cos-nodejs-sdk-v5'

let cos = new COS({AppId :'1253834952',SecretId : 'AKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4',SecretKey : 'qUwCGAsRq46wZ1HLCrKbhfS8e0A8tUu8'})
  export default {
    name: 'landing-page',
    data(){
      return{
        fileurl:'http://111en-1253834952.cn-southwest.myqcloud.com/1.rar?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4%26q-sign-time%3D1502971373%3B1506571373%26q-key-time%3D1502971373%3B1506571373%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3D7e71231405aa282259ed8ca3c4dddddff51dd9b3'
      }
    },
    methods: {
      doSomething(){
        // console.log('__dirname',__dirname)
        // console.log('cos',cos)
    remote.dialog.showOpenDialog({
      buttonLabel: '选择',
      filters: [{name: 'All Files', extensions: ['*']}],
      properties: ['openDirectory']
    },savepath=>{
        cos.getObject({
        Bucket: '111en',
        Region: 'cn-southwest',
        Key: "1.rar",
        Path:savepath[0],
        Prefix:this.fileurl,
        Output: fs.createWriteStream('mytest.tmp'),
        onProgress: function (progressData) {
            console.log(1,progressData);
        }
    }, function (err, data) {
      console.log(arguments)
    });
    })
    


      }
    }
  }
</script>



<style scoped>
#wrapper{
margin-left: 20%
}
input{
  width: 260px;
  padding: 8px 15px;
  margin-bottom: 14px;
}
.tl{
  padding: 10px 0;
  color: #999;
}
button{
  padding: 8px 100px;
}
</style>

