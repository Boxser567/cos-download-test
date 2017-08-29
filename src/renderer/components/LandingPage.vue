<template>
  <div id="wrapper">
    <h2> node fs 文件下载续传 Demo </h2>
    <!-- <input type="text" v-model="fileurl" /> -->
    <br/>
    <!-- <div class="tl">{{fileurl}}</div> -->
    <button @click="readStream">点我上传</button>
    <br />

    <!-- <button @click="beginSomething">开始</button> -->
    <button @click="pauseSomething">点我暂停</button>
    <button @click="readStream"> 读取流信息</button>
    <!-- <button @click="readDir">读取目录</button> -->
    <!-- <button @click="rename">重命名</button> -->
    <button @click="pipeFile">流读写</button>
  </div>
</template>

<script>
import { remote } from 'electron'
import fs from 'fs'
import path from 'path'
import COS from 'cos-nodejs-sdk-v5'
let list = [{ path: 'http://111en-1253834952.cn-southwest.myqcloud.com/pancy.zip?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4%26q-sign-time%3D1503387470%3B5103387470%26q-key-time%3D1503387470%3B5103387470%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3D2dc2b8f15bb090885dce684a581d90073530848f', key: 'pancy.zip' },
{ path: 'http://111en-1253834952.cn-southwest.myqcloud.com/12.rar?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4%26q-sign-time%3D1503040864%3B1506640864%26q-key-time%3D1503040864%3B1506640864%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3Dbad82859f02316e4695eb776a4e57b5dc363fd27', key: '12.rar' },
{ path: 'http://111en-1253834952.cn-southwest.myqcloud.com/2.rar?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4%26q-sign-time%3D1503040906%3B1506640906%26q-key-time%3D1503040906%3B1506640906%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3D2108ec72e5b15a0e77620686071ab3020ce669f5', key: '2.rar' }
]
let cos = new COS({ AppId: '1253834952', SecretId: 'AKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4', SecretKey: 'qUwCGAsRq46wZ1HLCrKbhfS8e0A8tUu8' })
export default {
  name: 'landing-page',
  data() {
    return {
      fileurl: 'http://111en-1253834952.cn-southwest.myqcloud.com/1.rar?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDa4NkxzaV0Ut7Yr4sa6ScbNwMdibHb4A4%26q-sign-time%3D1503036804%3B1506636804%26q-key-time%3D1503036804%3B1506636804%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3D215c5a3c270a423d22f083b758c3d4d1fcf54682',
      params: {
        Bucket: '111en',
        Region: 'cn-southwest',
        // Range: 'bytes=8280467-1838731238',
        Key: list[0].key,
        Path: list[0].path,
        Output: fs.createWriteStream(`/Users/gokuai/Downloads/${list[0].key}.tmp`, { flags: 'a' }),
        total: '1838731238',
        onProgress: (progressData) => {
          console.log(12, progressData);
          if (!this.params.total)
            this.params.total = Number(progressData.total)
        }
      },
      count: 0
    }
  },
  methods: {
    readStream() {
      // this.params.Range = 'bytes=87743832-1838731238'
      // console.log('begin', this.params)
      // if (this.params.total)
      //   delete this.params.total
      let stream, currentfile = '/Users/gokuai/Downloads/pancy.zip.tmp';
      stream = fs.statSync(currentfile)
      if (stream && stream.size) {
        this.params.Range = `bytes=${stream.size}-${this.params.total}`
      }
      console.log(stream,this.params)
      // cos.getObject(this.params, (err, data) => {
      //   console.log('getObject', err, data, this.params)
      // });
    },
    doSomething() {
      list.splice(1, 2)
      // for (let obj in list) {
      // let rs = fs.createReadStream(`/Users/gokuai/Downloads/${list[obj].key}.tmp`);
      // let ws = fs.createWriteStream(`/Users/gokuai/Downloads/${list[obj].key}.tmp`)
      // console.log(ws, rs)
      // rs.on('data', function (data) {
      //   console.log('数据可读', data)
      // });
      // rs.on('error', function (data) {
      //   console.log(new Error(data))
      // });
      // rs.on('end', function (mydata) {
      //   console.log('文件读取完成', mydata);
      //   //ws.end('再见')
      // });
      cos.getObject(this.params, (err, data) => {
        console.log(err, data)
        if (data) {
          this.params.localRange = data.headers['content-range']
        }
      });



      // }





      // console.log('__dirname',__dirname)
      // console.log('cos',cos)
      // remote.dialog.showOpenDialog({
      //   buttonLabel: '选择',
      //   filters: [{ name: 'All Files', extensions: ['*'] }],
      //   properties: ['openDirectory']
      // }, savepath => {
      //   console.log(savepath[0])
      // })
    },
    pauseSomething() {
      this.params.Output.on('drain', () => {
        this.params.Output.close()
        this.params.Output.emit('error', new Error('cancel'))
      })

      // if (!this.params.Range) {
      //   // this.params.total = Number(this.params.total) - Number(this.params.Output.bytesWritten)
      //   this.params.Range = `bytes=${this.params.Output.bytesWritten}-${this.params.total}`
      // } else {
      //   let arr = (this.params.Range.split('=')[1]).split('-')
      //   let Range = Number(arr[0]) + Number(this.params.Output.bytesWritten)
      //   // this.params.total = Number(this.params.total) - Number(Range)
      //   this.params.Range = `bytes=${Range}-${this.params.total}`
      // }
      console.log('暂停', this.params)
      // let params = {
      //   Bucket: '111en',
      //   Region: 'cn-southwest',
      //   Key: list[obj].key,
      //   Path: list[obj].path,
      //   dasd: 1
      // }
      // params.Body.emit('error', new Error('cancel'))
      // let rs = fs.createReadStream('/Users/gokuai/Downloads/2.rar.tmp');
      // let ws = fs.createWriteStream('/Users/gokuai/Downloads/2.rar.tmp')

      // rs.pipe(ws);
      // console.log(rs)
      // rs.on('data', function (data) {
      //   console.log('数据可读', arguments)
      // });
      // rs.on('end', function (data) {
      //   console.log('文件读取完成', data);
      // });
    },
    beginSomething() {
      fs.readFile('/Users/gokuai/Downloads/1.rar.tmp', function (err, data) {
        if (err) {
          console.error(err);
        } else {
          console.log(data);
        }
      });
    },
    readDir() {  // 读取某路径下所有文件及文件夹信息  返回Array
      let join = require('path').join;
      function findSync(startPath) {
        let result = [];
        function finder(path) {
          let files = fs.readdirSync(path);
          files.forEach((val, index) => {
            let fPath = join(path, val);
            let stats = fs.statSync(fPath);
            if (stats.isDirectory()) finder(fPath);
            if (stats.isFile()) result.push(fPath);
          });

        }
        finder(startPath);
        return result;
      }
      let fileNames = findSync('/Users/gokuai/Downloads/demo');
      console.log('files', fileNames)
    },
    rename() {
      fs.rename('/Users/gokuai/Downloads/1.rar.tmp', '/Users/gokuai/Downloads/test.rar', (err) => {
        if (err)
          console.log(new Error(err))
        console.log('重命名成功!');
      })
    },
    pipeFile() {        //实现流读写
      // var rs = fs.createReadStream('/Users/gokuai/Downloads/1.rar.tmp');
      // var ws = fs.createWriteStream('/Users/gokuai/Downloads/2.rar.tmp');

      let stream,
        currentfile = '/Users/gokuai/Downloads/1.rar.tmp',
        dhh = fs.createWriteStream('/Users/gokuai/Downloads/moment.zip');
      stream = fs.statSync(currentfile)
      console.log('stream', stream)

      // stream = fs.createReadStream(currentfile);
      // stream.pipe(dhh, { end: false });
      // stream.on("end", function () {
      //   console.log(currentfile + ' appended');
      //   // main();
      // });
    },
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

