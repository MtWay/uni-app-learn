<template>
  <view class="page">
    <view
      class="wrap"
      :style="'width:' + sw + 'px;height:' + sh + 'px'"
      @touchstart="touchstart"
      @touchmove="touchmove"
      @touchend="touchend"
    >
      <canvas
        canvas-id="canvas"
        id="canvas"
        :style="'width:' + sw + 'px;height:' + sh + 'px'"
        v-show="panFlag"
      ></canvas>
      <movable-area class="tailor_wrap" :scale-area="true" v-show="tailorFlag">
        <!-- <view class="cropper-point-wrap"  :style="
              'transform: translate(' +
              tailorObj.x +
              'px,' +
              tailorObj.y +
              'px);'
            "> -->

        <!-- </view> -->
        <movable-view
          class="tailor"
          direction="all"
          @change="tailorChange"
          @scale="tailorChange"
          :x="tailorObj.x"
          :y="tailorObj.y"
          :disabled="tailorT"
          :style="'width:' + tailorObj.w + 'px;height:' + tailorObj.h + 'px;'"
        >
          <view
            class="cropper-point cropper-point-l"
            @touchstart="tailorStart"
            id="cropper-point-l"
          >
          </view>
          <view
            class="cropper-point cropper-point-t"
            @touchstart="tailorStart"
            id="cropper-point-t"
          >
          </view>
          <view
            class="cropper-point cropper-point-r"
            @touchstart="tailorStart"
            id="cropper-point-r"
          >
          </view>
          <view
            class="cropper-point cropper-point-b"
            @touchstart="tailorStart"
            id="cropper-point-b"
          >
          </view>
          <view
            class="cropper-point cropper-point-lt"
            @touchstart="tailorStart"
            id="cropper-point-lt"
          >
          </view>
          <view
            class="cropper-point cropper-point-rt"
            @touchstart="tailorStart"
            id="cropper-point-rt"
          >
          </view>
          <view
            class="cropper-point cropper-point-lb"
            @touchstart="tailorStart"
            id="cropper-point-lb"
          >
          </view>
          <view
            class="cropper-point cropper-point-rb"
            @touchstart="tailorStart"
            id="cropper-point-rb"
          >
          </view>
          <view
            class="tailor_inner"
            :style="
              'transform: translate(-' +
              tailorObj.x +
              'px,-' +
              tailorObj.y +
              'px);'
            "
          >
            <view
              class="wrap"
              :style="'width:' + sw + 'px;height:' + sh + 'px'"
            >
              <view
                class="image_wrap"
                :style="
                  'transform: translate(' +
                  canvasObj.x +
                  'px,' +
                  canvasObj.y +
                  'px);'
                "
              >
                <image
                  :src="src"
                  mode="aspectFit"
                  class="image_show"
                  :style="
                    'transform: rotate(' +
                    canvasObj.deg +
                    'deg) scale(' +
                    canvasObj.scale +
                    '); height:' +
                    sh +
                    'px'
                  "
                  @load="imgLoad"
                ></image>
              </view>
            </view>
          </view>
        </movable-view>
      </movable-area>
      <!-- translate需要放到图片上一层不然位移会随着图片旋转角度走 -->
      <view
        class="image_wrap"
        :style="
          'transform: translate(' + canvasObj.x + 'px,' + canvasObj.y + 'px);'
        "
      >
        <image
          :src="src"
          mode="aspectFit"
          class="image_show"
          :style="
            'transform: rotate(' +
            canvasObj.deg +
            'deg) scale(' +
            canvasObj.scale +
            '); height:' +
            sh +
            'px'
          "
          @load="imgLoad"
        ></image>
        <!-- <canvas canvas-id="canvas" id="canvas" :style="'width:' + sw + 'px;height:' + sh + 'px;transform: rotate(' +
            canvasObj.deg +
            'deg) scale(' +
            canvasObj.scale +
            '); height:' +
            sh +
            'px'" ></canvas> -->
      </view>
    </view>
    <button type="default" size="mini" @click="rotate(-22.5)">左旋</button>
    <button type="default" size="mini" @click="rotate(22.5)">右旋</button>
    <button type="default" size="mini" @click="scale(0.1)">放大</button>
    <button type="default" size="mini" @click="scale(-0.1)">缩小</button>
    <button type="default" size="mini" @click="draw">绘制</button>
    <button type="default" size="mini" @click="modeImg">生成图片</button>
    <switch @change="switch1Change" color="#4CD964" />
    <text>裁剪</text>
    <button type="default" size="mini" @click="allTailor">完全覆盖</button>
    <switch @change="switch2Change" color="#4CD964" />
    <text>画笔</text>

    <radio-group @change="radioChange">
      <text>画笔大小</text>
      <radio value="5" checked="true">5</radio>
      <radio value="10">10</radio>
      <radio value="15">15</radio>
    </radio-group>
    <radio-group @change="radioChange1">
      <text>画笔颜色</text>
      <radio value="#f00" checked="true">红</radio>
      <radio value="#0f0">绿</radio>
      <radio value="#00f">蓝</radio>
    </radio-group>

    <button type="default" size="mini" @click="draw">绘制</button>
    <button type="default" size="mini" @click="draw">绘制</button>
    <canvas
      canvas-id="canva"
      id="canva"
      :style="'width:' + sw + 'px;height:' + sh + 'px'"
    ></canvas>
    <image :src="modeSrc" val mode="aspectFit"></image>
  </view>
</template>
<script>
	export default {
		data() {
			return {
				ctx: null,
				sw: 375,
				sh: 240,
				defSrc: '../../static/img/picture.jpg',
				src: "../../static/img/picture.jpg",
				modeSrc: "",
				tailorFlag: 0,
				panFlag: false,
				panObj: {
					r: 5,
					color: '#f00',
					data: []
				},
				tailorT: false, //裁剪区域大小变化
				canvasObj: {
					deg: 0,
					w: 0,
					h: 0,
					scale: 1,
					x: 0,
					y: 0,
				},
				touchObj: {},
				tailorObj: {
					scale: 1,
					x: 0,
					y: 0,
					w: 100,
					h: 100,
					start: "",
				},
			};
		},
		onReady() {
			console.log("ready");
			uni.getSystemInfo({
				success: (res) => {
					this.sw = res.screenWidth;
				},
			});
			let ctx = uni.createCanvasContext("canvas");
			this.ctx = ctx;

		},
		methods: {
			imgLoad(e) {
				console.log(e);
				let {
					ctx,
					sw
				} = this;
				this.sh = (e.detail.height / e.detail.width) * sw;
				// this.panFlag = false
				// this.initBg()
				// ctx.draw();
			},
			initBg() {
				let {
					ctx,
					sw,
					sh,
					src
				} = this;
				// let src = "../../static/img/picture.jpg";
				ctx.drawImage(src, 0, 0, sw, sh)
			},
			//触发图片变化
			loop(now, rotation, scale) {
				// console.log(now,rotation,scale)
				let touchObj = this.touchObj;
				touchObj.start = now;
				this.rotate(rotation);
				this.scale(scale);
			},

			//移动
			translate(now, x, y) {
				console.log(now, x, y)
				let {
					touchObj,
					canvasObj
				} = this;
				touchObj.start = now;
				canvasObj.x += x;
				canvasObj.y += y;
			},
			//缩放
			scale(s, flag) {
				let canvasObj = this.canvasObj;
				canvasObj.scale += s;
				// if(flag)
				// canvasObj.scale = s;
			},
			//旋转
			async rotate(deg, flag) {
				// console.log('rotate',deg,)
				//flag为true时直接赋值
				let ctx = this.ctx;
				let canvasObj = this.canvasObj;
				canvasObj.deg += deg;
				if (flag) {
					canvasObj.deg = deg;
				}
			},
			touchstart(e) {
				console.log(e);
				let touchObj = this.touchObj;
				let {
					panObj
				} = this;
				touchObj.start = e.touches; //得到第一组两个点
				if (e.touches.length == 1 && this.panFlag) {
					//加一条新的线
					panObj.data.push({
						r: panObj.r,
						color: panObj.color,
						data: []
					})
				}
				if (e.touches.length >= 2) {
					//判断是否有两个点在屏幕上
					touchObj.istouch = true;
					// obj.gesturestart&&obj.gesturestart.call(el); //执行gesturestart方法
				}
			},
			touchmove(e) {
				let {
					touchObj,
					ctx,
					panObj
				} = this;
				let {
					start
				} = touchObj;

				function getDistance(p1, p2) {
					// console.log(p1,p2)
					var x = p2.pageX - p1.pageX,
						y = p2.pageY - p1.pageY;
					return Math.sqrt(x * x + y * y);
				}

				function getAngle(p1, p2) {
					var x = p1.pageX - p2.pageX,
						y = p1.pageY - p2.pageY;
					return (Math.atan2(y, x) * 180) / Math.PI;
				}
				var now = e.touches; //得到第二组两个点

				//裁剪	
				if (this.tailorT) {
					this.modeTailor(start[0], now[0])
				}

				//一根手指 可拖动
				if (e.touches.length == 1 && !touchObj.istouch) {
					if (this.panFlag) {
						//画笔工具
						panObj.data[panObj.data.length - 1].data.push(now[0])
						// this.initBg()
						this.drawPan(panObj)
						ctx.draw()
						return
					}
					if (this.tailorFlag) {
						// 拖动裁剪框
						return
					}
					//拖动图片
					this.translate(
						now,
						now[0].pageX - start[0].pageX,
						now[0].pageY - start[0].pageY
					);
				}

				if (e.touches.length >= 2 && touchObj.istouch) {
					// console.log(now)
					var scale =
						getDistance(now[0], now[1]) / getDistance(start[0], start[1]); //得到缩放比例，getDistance是勾股定理的一个方法
					var rotation = getAngle(now[0], now[1]) - getAngle(start[0], start[1]); //得到旋转角度，getAngle是得到夹角的一个方法
					// console.log(scale,rotation)
					this.throttle(this.loop(now, rotation, scale - 1));
					// e.scale=scale.toFixed(2);
					// e.rotation=rotation.toFixed(2);
					// obj.gesturemove&&obj.gesturemove.call(el,e); //执行gesturemove方法
				}
			},

			touchend(e) {
				let touchObj = this.touchObj;
				console.log(e.touches.length);
				this.tailorT = false;
				//如果之前是双指 则双指都离开才清除双指权限，防止此时拖动图片出现错位，同时防止双指交换点击图片位移。
				if (touchObj.istouch && e.touches.length == 0) {
					touchObj.istouch = false;
					// obj.gestureend&&obj.gestureend.call(el); //执行gestureend方法
				}
			},
			//画线
			drawPan(panObj) {

				let ctx = this.ctx;
				panObj = panObj || this.panObj;
				panObj.data.map((n, i) => {
					ctx.beginPath()
					ctx.setStrokeStyle(n.color)
					ctx.setLineWidth(n.r)
					n.data.map((son, idx) => {
						if (idx == 0) {
							ctx.moveTo(son.pageX, son.pageY)
						}
						ctx.lineTo(son.pageX, son.pageY)
					})
					ctx.stroke()
				})
			},
			//将画笔内容和图片合并起来
			joinPan(fn) {
				let {
					sw,
					sh,
					ctx
				} = this;
				let _this = this;
				this.draw(ctx).then((res) => {
					this.drawPan();
					ctx.draw(0, res => {
						uni.canvasToTempFilePath({
							x: 0,
							y: 0,
							width: sw,
							height: sh,
							canvasId: "canvas",
							success: function(res) {

								// 在H5平台下，tempFilePath 为 base64
								console.log(res.tempFilePath);
								_this.src = res.tempFilePath;
								if(typeof fn == "function"){
									fn()
								}
							},
						});
					})
				})
			},
			//节流
			throttle(fn) {
				let canRun = true; // 通过闭包保存一个标记
				return function() {
					console.log(canRun);
					if (!canRun) return; // 在函数开头判断标记是否为true，不为true则return
					canRun = false; // 立即设置为false
					setTimeout(() => {
						console.log(canRun);
						// 将外部传入的函数的执行放在setTimeout中
						fn.apply(this, arguments);
						// 最后在setTimeout执行完毕后再把标记设置为true(关键)表示可以执行下一次循环了。当定时器没有执行的时候标记永远是false，在开头被return掉
						canRun = true;
					}, 1000 / 24);
				};
			},
			GetImageData() {
				let sw = this.sw;
				let _this = this;
				if (this.canvasObj.data) {
					return this.canvasObj.data;
				} else {
					return new Promise((r, q) => {
						uni.canvasGetImageData({
							canvasId: "canvas",
							x: 0,
							y: 0,
							width: sw,
							height: sw,
							success(res) {
								_this.canvasObj.data = res;
								r(res);
							},
						});
					});
				}
			},
			draw(ctx) {
				// let ctx = uni.createCanvasContext("canva");
				let {
					sw,
					sh,
					src
				} = this;
				let _this = this;
				return new Promise((r, q) => {
					uni.getImageInfo({
						src: src,
						success: function(res) {
							console.log(res, "图片");
							_this.tranform(_this.canvasObj, ctx);
							let hw = res.height / res.width;
							//白色背景
							ctx.setFillStyle('#fff')
							ctx.fillRect(-10000, -10000, 20000, 20000)
							ctx.drawImage(src, 0, 0, sw, sw * hw);
							// ctx.drawImage(image, sw * 0.1, sw * 0.1 * hw, sw * 0.8, sw * hw * 0.8);
							r(res);
							// ctx.draw(0, function () {
							//   console.log("绘制完成");
							//   r(res);
							// });
						},
					});
				});
			},
			//生成图片
			modeImg() {
				let ctx = uni.createCanvasContext("canva");
				this.draw(ctx).then((res) => {
							let {
								tailorObj,
								sw,
								sh
							} = this;
							let _this = this;
							// _this.tranform(_this.canvasObj, ctx,1)
							// uni.canvasGetImageData({
							//   canvasId: 'canvas',
							//   x: 0,
							//   y: 0,
							//   width: sw,
							//   height: sh,
							//   success(res) {
							//    uni.canvasPutImageData({
							//      canvasId: 'canva',
							//      x: 0,
							//      y: 0,
							//      width: sw,
							// 				   height:sh,
							//      data: res.data,
							//      success(res) {

							ctx.draw(0, function() {
									uni.canvasToTempFilePath({
										x: tailorObj.x,
										y: tailorObj.y,
										width: tailorObj.w,
										height: tailorObj.h,
										canvasId: "canva",
										success: function(res) {

											// 在H5平台下，tempFilePath 为 base64
											console.log(res.tempFilePath);
											_this.modeSrc = res.tempFilePath;
										},
									});
								// }
								//    })
								//   }
								// })
							});
							})
					},
					//变化canvas转换
					tranform(obj, ctx, flag) {
						obj = { ...obj
						};

						if (flag) {
							Object.keys(obj).map(n => {
								obj[n] = -obj[n];
							})
						}
						let {
							sw,
							sh
						} = this;
						ctx.translate(sw / 2, sh / 2);
						//固定顺序，不可变
						ctx.scale(obj.scale, obj.scale);
						ctx.translate(obj.x, obj.y);
						ctx.rotate((obj.deg * Math.PI) / 180);
						ctx.translate(-sw / 2, -sh / 2);
					},
					tailorChange(e) {
						// console.log(e.detail)
						this.tailorObj = { ...this.tailorObj,
							...e.detail
						};

						// this.throttle(
						// 	() => {
						// 		this.tailorObj = e.detail;
						// 	}
						// )()
						// let {x,y,scale} = e.detail
						// if(scale){
						// this.tailorObj.scale =scale;
						// }
						// scale = this.tailorObj.scale
						// this.tailorObj.x = x*scale;
						// this.tailorObj.y = y*scale;

						// console.log(this.tailorObj)
						// this.$forceUpdate()
					},
					tailorFn(e) {
						// this.tailorObj = e.detail;
						this.tailorObj.x = e.detail.x;
						this.tailorObj.y = e.detail.y;
					},
					switch1Change(e) {
						// this.joinPan()
						this.tailorFlag = e.detail.value;

					},
					switch2Change(e) {
						if(e.detail.value){
						this.panFlag = e.detail.value;
						}else{
							this.joinPan(()=>{
								//需要延迟改变不然canvas消失之后将不能生成图片
								setTimeout(() => {
						this.panFlag = e.detail.value;
									
								}, 10);
							})
						}
						

					},
					//点击缩放裁剪框的点
					tailorStart(e) {
						console.log(e);
						this.tailorT = true;
						let {
							tailorObj
						} = this;
						let start = e.target.id.split("-")[2];
						console.log(start);
						tailorObj.start = start;
					},
					//计算裁剪框变动，位置和大小
					modeTailor(start, end) {
						let {
							tailorObj,
							sw,
							sh
						} = this;
						let {
							x,
							y,
							w,
							h
						} = tailorObj;
						let type = tailorObj.start;
						console.log(type, start, end)
						if (type.indexOf('l') >= 0) {
							x += (end.pageX - start.pageX)
							if (x > 0 && x < sw) {
								w -= (end.pageX - start.pageX)
							}
						}
						if (type.indexOf('t') >= 0) {
							y += (end.pageY - start.pageY)
							if (y > 0 && y < sh) {
								h -= (end.pageY - start.pageY)
							}
						}
						if (type.indexOf('r') >= 0) {
							w += (end.pageX - start.pageX)
						}
						if (type.indexOf('b') >= 0) {
							h += (end.pageY - start.pageY)
						}
						this.touchObj.start = [end]

						x = this.restrict(x, 0, sw)
						y = this.restrict(y, 0, sh)
						w = this.restrict(w, 10, sw - x)
						h = this.restrict(h, 10, sh - y)

						tailorObj.x = x;
						tailorObj.y = y;
						tailorObj.w = w;
						tailorObj.h = h;
						console.log(tailorObj)
						this.$forceUpdate()
					},
					//限制最大最小值
					restrict(k, min, max) {
						if (k > max) k = max;
						if (k < min) k = min;
						return k
					},
					allTailor() {
						let {
							tailorObj,
							sw,
							sh
						} = this;
						tailorObj.w = sw;
						tailorObj.h = sh;
						setTimeout(()=>{
tailorObj.x = 0;
						tailorObj.y = 0;
						})
						
						console.log(this.tailorObj)
					},
					radioChange(e) {
						console.log(e)
						this.panObj.r = e.detail.value
					},
					radioChange1(e) {
						this.panObj.color = e.detail.value
					}
			},
		};
</script>
<style>
.page {
  overflow: hidden;
  width: 100vw;
  /* background: #007aff; */
}

.wrap {
  background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQAQMAAAAlPW0iAAAAA3NCSVQICAjb4U/gAAAABlBMVEXMzMz////TjRV2AAAACXBIWXMAAArrAAAK6wGCiw1aAAAAHHRFWHRTb2Z0d2FyZQBBZG9iZSBGaXJld29ya3MgQ1M26LyyjAAAABFJREFUCJlj+M/AgBVhF/0PAH6/D/HkDxOGAAAAAElFTkSuQmCC");
  width: 100vw;
  overflow: hidden;
  position: relative;
  /* height: 240px; */
  /* height: 100vh; */
}

.image_wrap {
  position: absolute;
  width: 100vw;
  top: 0;
  left: 0;
}

.image_show {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  overflow: hidden;
  transform: translate();
}

.tailor_wrap {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 2;
}

.tailor {
  width: 100px;
  height: 100px;
  overflow: hidden;
  position: absolute;
  /* border: 2px solid rgba(51,153,255,0.5); */
  /* padding: 2px;
		left: -2px;
		top: -2px; */
}

.tailor::after {
  content: "";
  display: block;
  position: absolute;
  width: calc(100% - 8px);
  height: calc(100% - 8px);
  top: 4px;
  left: 4px;
  border: 2px solid rgba(51, 153, 255, 0.5);
  box-sizing: border-box;
  box-shadow: 0 0 5px 50px rgba(0, 0, 0, 0.5);
}

.tailor_inner {
  position: absolute;
  width: 100px;
  height: 100px;
}

.cropper-point-wrap {
  position: absolute;
  display: flex;
  width: 100px;
  height: 100px;
  top: 0;
  left: 0;
  justify-content: space-between;
  flex-direction: column;
  /* z-index: 2; */
}

.cloumn {
  width: 100%;
  display: flex;
  justify-content: space-between;
}

.cropper-point-lt {
  /* transform: translate(-50%,-50%); */
  left: 0;
  top: 0;
}

.cropper-point-rt {
  right: 0;
  top: 0;
  /* transform: translate(50%,-50%); */
}

.cropper-point-lb {
  left: 0;
  bottom: 0;
  /* transform: translate(-50%,50%); */
}

.cropper-point-rb {
  right: 0;
  bottom: 0;
  /* transform: translate(50%,50%); */
}

.cropper-point-l {
  left: 0;
  top: 50%;
  margin-top: -5px;
}

.cropper-point-t {
  left: 50%;
  top: 0;
  margin-left: -5px;
}

.cropper-point-r {
  right: 0;
  top: 50%;
  margin-top: -5px;
}

.cropper-point-b {
  left: 50%;
  bottom: 0;
  margin-left: -5px;
}

.cropper-point {
  width: 10px;
  height: 10px;
  background: rgba(51, 153, 255, 1);
  position: absolute;
  z-index: 2;
  border-radius: 50%;
  /* border: 2px solid rgba(51, 153, 255, 1); */
  /* box-sizing: border-box; */
}

/* 扩大点击区域 */
.cropper-point::after {
  position: absolute;
  content: "";
  width: 200%;
  height: 200%;
  /* background: #f00; */
  top: -50%;
  left: -50%;
}

canvas {
  /* background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQAQMAAAAlPW0iAAAAA3NCSVQICAjb4U/gAAAABlBMVEXMzMz////TjRV2AAAACXBIWXMAAArrAAAK6wGCiw1aAAAAHHRFWHRTb2Z0d2FyZQBBZG9iZSBGaXJld29ya3MgQ1M26LyyjAAAABFJREFUCJlj+M/AgBVhF/0PAH6/D/HkDxOGAAAAAElFTkSuQmCC"); */
}

#canva {
  position: absolute;
  top: -200%;
}

#canvas {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 9;
}
</style>
