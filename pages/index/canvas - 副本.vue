<template>
    <view class="body">
        <!-- <canvas style="width: 300px; height: 200px;" canvas-id="firstCanvas" id="firstCanvas"></canvas> -->
        <!-- <img id="image" src="../../static/img/picture-3.jpg" alt="Picture"> -->
        <h1>Crop a round image</h1>
    <h3>Image</h3>
    <div>
        <img id="image" src="https://fengyuanchen.github.io/cropperjs/images/picture.jpg" alt="Picture" ref="image">
        <!-- <img id="image" src="../../static/img/picture.jpg" alt="Picture"> -->
      <!-- <img id="image" src="../images/picture.jpg" alt="Picture"> -->
    </div>
    <h3>Result</h3>
    <p>
      <button type="button" id="button" @click="run">Crop</button>
    </p>
    <div id="result"></div>
    </view>
</template>

<script>
import Cropper from "../../static/js/cropper.js";
export default {
  mounted: function (e) {
    //   this.getRoundedCanvas()
    var context = uni.createCanvasContext("firstCanvas");
    // console.log(cropperjs)
    // cropperjs()
    console.log(Cropper);

    // var image = document.querySelector("#image");                                                               
	setTimeout(()=>{
		console.log(uni.createSelectorQuery())
		var image = uni.createSelectorQuery().selectAll('img')
		// var image = this.$refs.image;
		// console.log(this.$refs)
		console.log(image)
	},1000)
	
	return
	// console.log(image.src)
    var minAspectRatio = 0.5;
    var maxAspectRatio = 1.5;
    var cropper = new Cropper(image, {
      ready: function () {
      // var image = document.getElementById('image');
    //   var button = document.getElementById('button');
      var result = document.getElementById('result');
      var croppable = false;
      var cropper = new Cropper(image, {
        aspectRatio: 1,
        viewMode: 1,
        ready: function () {
          croppable = true;
        },
      });
    //   button.onclick = function () {
      
    //   };
      },

      cropmove: function () {
        var cropper = this.cropper;
        var cropBoxData = cropper.getCropBoxData();
        var aspectRatio = cropBoxData.width / cropBoxData.height;
    //   debugger

        if (aspectRatio < minAspectRatio) {
          cropper.setCropBoxData({
            width: cropBoxData.height * minAspectRatio,
          });
        } else if (aspectRatio > maxAspectRatio) {
          cropper.setCropBoxData({
            width: cropBoxData.height * maxAspectRatio,
          });
        }
      },
    });
  },
  onReady(){
	  const query = uni.createSelectorQuery().in(this);
	  console.log(query.select('#image'))
	  query.select('#image').context(data => {
	    console.log("得到布局位置信息" + JSON.stringify(data));
	    console.log("节点离页面顶部的距离为" + data.top);
	  }).exec();
	  console.log("onReady")
  },
  methods: {
      getRoundedCanvas(sourceCanvas) {
      var canvas = document.createElement('canvas');
      var context = canvas.getContext('2d');
      var width = sourceCanvas.width;
      var height = sourceCanvas.height;

      canvas.width = width;
      canvas.height = height;
      context.imageSmoothingEnabled = true;
      context.drawImage(sourceCanvas, 0, 0, width, height);
      context.globalCompositeOperation = 'destination-in';
      context.beginPath();
      context.arc(width / 2, height / 2, Math.min(width, height) / 2, 0, 2 * Math.PI, true);
      context.fill();
      return canvas;
    },
    run(){
          var croppedCanvas;
        var roundedCanvas;
        var roundedImage;

        if (!croppable) {
          return;
        }

        // Crop
        croppedCanvas = cropper.getCroppedCanvas();

        // Round
        roundedCanvas = this.getRoundedCanvas(croppedCanvas);

        // Show
        roundedImage = document.createElement('img');
        roundedImage.src = roundedCanvas.toDataURL()
        result.innerHTML = '';
        result.appendChild(roundedImage);
    }
    
  },
};
</script>

<style lang="less">
.body{
    // background: #0f0;
}
#image{
    width: 100%;
}

</style>

