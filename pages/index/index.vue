<template>
	<view class="content">
		<navigator url="./canvas">
			<button type="default">canvas</button>
		</navigator>
		<navigator url="../webView/webView">
			<button type="default">webview</button>
		</navigator>
		<!-- <button type="default"  @click="">canvas</button> -->
		<image class="logo" src="/static/logo.png"></image>ssdfasda da
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
		<text class="title">临时{{tempFilePath}}</text>
		<!-- <image :src="tempFilePath" mode=""></image> -->
		<video :src="tempFilePath"></video>
		<text class="title">本地{{file}}</text>
		<image :src="file" mode=""></image>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				title: 'Hello',
				file: '_doc/uniapp_save/16136335546250.jpg',
				tempFilePath: ''
			}
		},
		onLoad(a) {
			console.log("onLoad", a)
			this.download()
		},
		methods: {
			download() {
				let downloadTask = uni.downloadFile({
					url: 'http://182.150.44.163:10000/deliver_file_test/qmsht.jpg', //仅为示例，并非真实的资源
					// url: 'https://avatars.githubusercontent.com/u/19549754?s=60&v=4', //仅为示例，并非真实的资源
					success: (res) => {
						if (res.statusCode === 200) {
							console.log('下载成功', res);
							var tempFilePaths = res.tempFilePaths;
							// this.tempFilePath = res.tempFilePath
							this.tempFilePath = 'http://baishan.oversketch.com/2019/11/05/d07f2f1440e51b9680f4bcfe63b0ab67.MP4'
							uni.saveFile({
								// tempFilePath: tempFilePaths[0],
								tempFilePath: res.tempFilePath,
								success: (res) => {
									var savedFilePath = res.savedFilePath;
									console.log('保存成功', res);
									this.file = savedFilePath
								}
							});
						}
					}
				});

				// downloadTask.onProgressUpdate((res) => {
				//     console.log('下载进度' + res.progress);
				//     console.log('已经下载的数据长度' + res.totalBytesWritten);
				//     console.log('预期需要下载的数据总长度' + res.totalBytesExpectedToWrite);

				//     // 测试条件，取消下载任务。
				//     // if (res.progress > 50) {
				//     //     downloadTask.abort();
				//     // }
				// });
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;

	}

	image /deep/div {
		background-size: 100% !important;
		background-position: center !important;
	}
</style>
