<template>
  <div id="wrapper">
    <h2> 测试windows 下载内存爆表问题 </h2>
    <input type="text" v-model="fileurl" />
    <br/>
    <div class="tl">{{fileurl}}</div>
    <button @click="doSomething">点我上传</button>
    <br />
  
    <button @click="beginSomething">开始</button>
    <button @click="pauseSomething">点我暂停</button>
  </div>
</template>

<script>
import { remote } from 'electron'
import fs from 'fs'
import path from 'path'
import COS from 'cos-nodejs-sdk-v5'

let cos = new COS({ AppId: '1253834952', SecretId: 'AKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4', SecretKey: 'qUwCGAsRq46wZ1HLCrKbhfS8e0A8tUu8' })
export default {
  name: 'landing-page',
  data() {
    return {
      fileurl: 'http://111en-1253834952.cn-southwest.myqcloud.com/1.rar?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4%26q-sign-time%3D1503036804%3B1506636804%26q-key-time%3D1503036804%3B1506636804%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3D215c5a3c270a423d22f083b758c3d4d1fcf54682'
    }
  },
  methods: {
    doSomething() {
      // console.log('__dirname',__dirname)
      // console.log('cos',cos)
      remote.dialog.showOpenDialog({
        buttonLabel: '选择',
        filters: [{ name: 'All Files', extensions: ['*'] }],
        properties: ['openDirectory']
      }, savepath => {
        console.log(savepath[0])
        let list = [{ path: 'http://111en-1253834952.cn-southwest.myqcloud.com/1.rar?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4%26q-sign-time%3D1503036804%3B1506636804%26q-key-time%3D1503036804%3B1506636804%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3D215c5a3c270a423d22f083b758c3d4d1fcf54682', key: '1.rar' },
        { path: 'http://111en-1253834952.cn-southwest.myqcloud.com/12.rar?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4%26q-sign-time%3D1503040864%3B1506640864%26q-key-time%3D1503040864%3B1506640864%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3Dbad82859f02316e4695eb776a4e57b5dc363fd27', key: '12.rar' },
        { path: 'http://111en-1253834952.cn-southwest.myqcloud.com/2.rar?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4%26q-sign-time%3D1503040906%3B1506640906%26q-key-time%3D1503040906%3B1506640906%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3D2108ec72e5b15a0e77620686071ab3020ce669f5', key: '2.rar' }
        ]
        list.splice(1, 1)
        for (let obj in list) {
          
          let rs = fs.createReadStream(`${savepath[0]}/${list[obj].key}.tmp`);
          let ws = fs.createWriteStream(`${savepath[0]}/${list[obj].key}.tmp`)
          rs.pipe(ws);
          rs.on('data', function (data) {
            console.log('数据可读', arguments)
          });
          rs.on('end', function () {
            console.log('文件读取完成', arguments);
          });
          cos.getObject({
            Bucket: '111en',
            Region: 'cn-southwest',
            Key: list[obj].key,
            Path: list[obj].path,
            Output: ws,//savepath[0]+'/xxyy.tmp'
            onProgress: function (progressData) {
              console.log(1, progressData);
            }
          }, function (err, data) {
            console.log(arguments)
          });
        }
      })
    },
    pauseSomething() {
      let params = {
        Bucket: '111en',
        Region: 'cn-southwest',
        Key: list[obj].key,
        Path: list[obj].path,
        dasd: 1
      }
      params.Body.emit('error', new Error('cancel'))
      let rs = fs.createReadStream('/Users/gokuai/Downloads/2.rar.tmp');
      let ws = fs.createWriteStream('/Users/gokuai/Downloads/2.rar.tmp')


      // rs.pipe(ws);
      // console.log(rs)
      // rs.on('data', function (data) {
      //   console.log('数据可读',arguments)
      // });
      // rs.on('end', function (data) {
      //   console.log('文件读取完成',data);
      // });
    },
    beginSomething() {

    }
  }
}
</script>



<style scoped>
#wrapper {
  margin-left: 20%
}

input {
  width: 260px;
  padding: 8px 15px;
  margin-bottom: 14px;
}

.tl {
  padding: 10px 0;
  color: #999;
}

button {
  padding: 8px 100px;
}
</style>

