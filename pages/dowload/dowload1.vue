<template>
  <view>
    <view v-for="(item, index) in list" class="list" :key="item.name">
      <image :src="item.src" mode="aspectFill" class="image"></image>
      <view class="right">
        <view class="line_box flex_box">
          <view class="line">
            <view class="progress" :style="'width: ' + item.progress + '%'">
            </view>
          </view>
          <text>{{ item.progress }}%</text>
        </view>
        <view class="btn_box flex_box">
          <button type="default" size="mini" @click="dowload(item)">下载</button>
          <button type="default"  size="mini" @click="abort(item)">暂停</button>
          <button type="default" size="mini"  @click="constructor(item)">继续</button>
        </view>
        <view class="btn_box flex_box">
          <button type="default" size="mini"  @click="start(item)">开始</button>
          <button type="default"  size="mini" @click="pauseById(item)">暂停</button>
          <button type="default"  size="mini" @click="resumeById(item)">继续</button>
            <button type="primary" size="mini"  @click="deleteById(item)">删除</button>
        </view>
        <text
          >{{ item.jd.current_size }}/{{
            item.jd.total_size
          }}</text
        >
      </view>
    </view>
	 <view>
            <button @click="listener">开始监听</button>
        </view>
        <view>
            <button @click="stopListener">停止监听</button>
        </view>
		 <view>
            <button @click="pauseAll">暂停所有下载</button>
        </view>
        <view>
            <button @click="deleteAll">删除全部</button>
        </view>
  </view>
</template>

<script>
const DownloaderManager = uni.requireNativePlugin("Karma617-DownloaderManager");
export default {
  data() {
    return {
      list: [
        {
          name: "图片1",
          src: "http://182.150.44.163:10000/deliver_file_test/qmsht.jpg",
                        saveName: "a.jpg",
          totalBytesWritten: 0,
          totalBytesExpectedToWrite: 0,
          progress: 0,
          downloadTask: null,
		  jd:{}
        },
		{
          name: "图片2",
          src: "http://dldir1.qq.com/weixin/android/weixin708android1540.apk",
                        saveName: "微信.apk",
          totalBytesWritten: 0,
          totalBytesExpectedToWrite: 0,
          progress: 0,
          downloadTask: null,
		  jd:{}
        },
      ],
	   taskList:[],
		  downloadList:[],
		  taskStart:false,
		  isListener:false,
		  now:{}
    };
  },
  stopListener () {
                this.isListener = false;
                DownloaderManager.stopListener();
            },
			 pauseAll () {
                DownloaderManager.pauseAll();
            },
            deleteAll() {
                DownloaderManager.pauseAll();
                // 删除之前若有任务在进行中，请先暂停全部任务再进行删除操作
                DownloaderManager.deleteAll(true);
                this.queryAll();
                this.taskList = [];
                this.taskIdList= [];
                this.taskStart= false;
            },
  onReady() {
            var _this = this;
            DownloaderManager.init({
                // maxDownloadTasks: 3,        // 最大同时下载任务数
                // downloadDir: '/storage/emulated/0/Android/data/com.qdapi.downloader/cache/myDownloader', // 下载文件路径
                // maxDownloadThreads: 3,      // 最大下载线程数
                // autoRecovery: false,        // 是否自动恢复下载
                // openRetry: true,            // 下载失败是否打开重试
                // maxRetryCount: 2,           // 重试次数
                // retryIntervalMillis: 1000   // 每次重试时间间隔（毫秒）
            }, function(res){
                if(0 == res.code){
                    uni.showToast({
                        title: res.msg,
                        icon: "none"
                    })
                    // 显示下载列表
                    _this.queryAll();
                }
            });
        },
  methods: {
	   queryAll () {
                var _this = this;
                DownloaderManager.queryAll(function(res){
                    _this.downloadList = JSON.parse(res.data);
					console.log(res)
                });
            },
    dowload(item) {
      let downloadTask = uni.downloadFile({
        url: item.src, //仅为示例，并非真实的资源
        success: (res) => {
          if (res.statusCode === 200) {
            console.log("下载成功", res);
            var tempFilePaths = res.tempFilePaths;
            //  this.tempFilePath = res.tempFilePath
            //  this.tempFilePath ='http://baishan.oversketch.com/2019/11/05/d07f2f1440e51b9680f4bcfe63b0ab67.MP4'
            uni.saveFile({
              // tempFilePath: tempFilePaths[0],
              tempFilePath: res.tempFilePath,
              success: (res) => {
                var savedFilePath = res.savedFilePath;
                console.log("保存成功", res);
                this.file = savedFilePath;
              },
            });
          }
        },
      });
      item.downloadTask = downloadTask;
      downloadTask.onProgressUpdate((res) => {
        // console.log('下载进度' + res.progress);
        // console.log('已经下载的数据长度' + res.totalBytesWritten);
        // console.log('预期需要下载的数据总长度' + res.totalBytesExpectedToWrite);
        item.progress = res.progress;
        item.totalBytesWritten = res.totalBytesWritten;
        item.totalBytesExpectedToWrite = res.totalBytesExpectedToWrite;
        // 测试条件，取消下载任务。
        if (res.progress > 50) {
          downloadTask.abort();
        }
      });
    },
    abort(item) {
      console.log(item.downloadTask);
      item.downloadTask.abort();
      console.log(item.downloadTask);
    },
    constructor(item) {
      item.downloadTask.constructor();
    },
    start(item) {
		console.log(DownloaderManager)
      var _this = this;
	  this.now = item
      // 文件保存目录，应用目录下的cache/quietDownload/this.saveName
      DownloaderManager.createDownloadTask(
        {
          downUrl: item.src,
          saveName: "123", // 此处可改成根据下载地址自动获取文件名及文件格式
        },
        function (res) {
			console.log(res)
        //   if (0 == res.code) {
            _this.listener();
        //   }
        }
      );
    },
	 deleteById(item) {
		 let {id} = item
                // 删除之前若任务在进行中，请先暂停任务再进行删除操作
                // deleteById 参数1：任务id 参数2：是否同时删除文件 参数3：要删除的文件名
                DownloaderManager.deleteById(id, true, item.saveName);
                this.queryAll();
            },
    resumeById(item) {
      DownloaderManager.resumeById(item.id);
    },
	 pauseById(item) {
                DownloaderManager.pauseById(item.id);
            },
	listener(){
		 var _this = this;
		 console.log(_this.isListener)
                if(!_this.isListener){
                    // 如果有正在进行中的下载任务，此方法将返回下载中的任务信息
                    // 回调频率为 1秒
                    DownloaderManager.listener(function(res){
                        _this.isListener = true;
                        // 注意 res.data 这个是字符串，需要手动转为object
                        // 观察者模式监听下载状态，需手动赋值，
                        // 小弟才疏学浅~写的很混乱，希望大佬能优化一下这个方法，感激不尽
                        let _res = JSON.parse(res.data);
						console.log(_res)
						_this.now.id = _res.id
						_this.now.jd = _res
                        if (_res instanceof Object) {
                            let obj = {
                                id: _res.id,
                                name: _res.save_name,
                                status: _res.status,
                                currentLength: _res.current_size,
                                totalLength: _res.total_size
                            };
                            // 添加进下载队列
                            _this.taskList.push(obj);
                            // 查询队列id里是否已有此任务id，没有则在下载列表里追加一条任务
                            if (_this.taskIdList.indexOf(obj.id) == -1) {
                                _this.taskIdList.push(_res.id);
                                _this.downloadList.push(obj);
                            }
                            // 渲染列表
                            if(!_this.taskStart){
                                _this.taskStart = true;
                                _this.doTask();
                            }
                        }
                    });
                }
	}
  },
};
</script>

<style>
.list {
  width: 100%;
  padding: 0 20px;
  margin: 20px 0;
  /* height: 60px; */
  display: flex;
  align-items: center;
  box-sizing: border-box;
}

.image {
  width: 100px;
  height: 60px;
}

.right {
  flex: 1;
}
.flex_box {
  display: flex;
  align-items: center;
}
.line {
  flex: 1;
  margin: 0 10px;
  height: 10px;
  background: #ccc;
  border-radius: 30px;
  overflow: hidden;
}

.progress {
  background: #4cd964;
  height: 100%;
  width: 0;
  min-width: 10px;
  border-radius: 30px;
}
</style>
