<template>
	<view>
		<web-view :src="pdfUrl"></web-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				pdfUrl:'',
				from:''
			}
		},
		onLoad() {
			/**
			 * 浏览情景
			 */
			
			// 浏览情景1:浏览本地的pdf文件
			this.pdfUrl = '/static/liuyichen-h5-show-pdf_1.1.0/hybrid/html/web/viewer.html?file=./compressed.tracemonkey-pldi-09.pdf'
			// this.pdfUrl = '/static/liuyichen-h5-show-pdf_1.1.0/hybrid/html/web/viewer.html?file=/static/liuyichen-h5-show-pdf_1.1.0/hybrid/html/web/compressed.tracemonkey-pldi-09.pdf'
			
			let pdfUrl = 'http://127.0.0.1:5500/hybrid/html/web/compressed.tracemonkey-pldi-09.pdf'
			// let pdfUrl = '/static/liuyichen-h5-show-pdf_1.1.0/hybrid/html/web/compressed.tracemonkey-pldi-09.pdf'
			// 浏览情景2:浏览在线的pdf文件
			// this.pdfUrl = '/static/liuyichen-h5-show-pdf_1.1.0/hybrid/html/web/viewer.html?file='+encodeURIComponent(pdfUrl)
		},
		methods: {
			/**
			 * 下载情景
			 */
			// downloadPdfClick(){
			// 	// 下载情景1:h5内嵌app,通过分享给朋友的方式进行下载
			// 	if(this.from == 'app'){
			// 		let item = 'http://自己的域名/hybrid/html/web/pdf.html?shareUrl='+encodeURIComponent(this.bgUrl)
			// 					+'&shareTitle=PDF文件';
			// 		window.open(item);
			// 		return;
					
			// 	}else if(this.from == 'wx wq'){
			// 	// 下载情景2:h5内嵌微信小程序,从h5页面跳转到小程序页面后,然后通过调用小程序原生API进行下载
			// 	// 注意: 先要引入微信jssdk  [命令: npm install jweixin-module]
			// 		// this.$wx.miniProgram.navigateTo({
			// 		// 	url: '/pages/pdf/pdf_download?pdf='+ encodeURIComponent(this.bgUrl) //小程序页面链接
			// 		// });
			// 	}
				
			// }
		}
	}
</script>

<style lang="less">

</style>
