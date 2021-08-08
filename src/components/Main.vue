<template>
  <main>
    <!-- Main -->
    <div id="main-container" class="wrapper">
      <!-- The area to load pictures -->
      <div class="img-holder wrapper-inner">
        <div class="canvas-wrapper" id="original-wrapper"  :style="transformProperty('original')">
          <canvas id="original-image"
                  class="original-image"
                  :style="scaleOpacityProperty('original')"
                  ref="originalCanvas"
                  v-on:mousemove="showMouseCoords($event)"
                  v-on:mouseleave="clearMouseCoords()"
          ></canvas>
        </div>
        <div class="canvas-wrapper compare-mode__element" id="copy-wrapper" :style="transformProperty('copy')">
          <canvas id="copy-image" class="copy-image" :style="scaleOpacityProperty('copy')"></canvas>
        </div>
      </div>
      <!-- Right-side area with interface -->
      <div class="wrapper-inner controll-panel">
        <div class="flex-full-width">
          <button class="btn full-width-btn right" id="sidebar-position">
            <span class="inner-left" data-translate="place_sidebar_left"></span>
            <span class="inner-right" data-translate="place_sidebar_right"></span>
          </button>
        </div>
        <ul  class="panel-list">
          <li>
            <ul class="panel-list-item">
              <li class="panel-item">
                <file-upload :data-target="'original'" v-on:file="onImageUpload($event)"></file-upload>
              </li>
              <li class="panel-item scale-mode__element">
                <label for="canvasWidth">
                  <span data-translate="canvas_width"></span>
                  <input type="number"
                         id="canvasWidth"
                         data-input="height"
                         class="sizeInput input"
                         v-model="newPictureSize.width"
                         @change="calcNewImageSize($event, 'width')"
                         disabled>
                </label>
                <label for="canvasHeight">
                  <span data-translate="canvas_height"></span>
                  <input type="number"
                         id="canvasHeight"
                         data-input="width"
                         class="sizeInput input"
                         v-model="newPictureSize.height"
                         @change="calcNewImageSize($event, 'height')"
                         disabled>
                </label>
              </li>
              <li class="panel-item scale-mode__element">
                <b><p data-translate="point_coords"></p></b>
                <p><span data-translate="by_horizontal"></span> <b id="remappedX">{{mousePosition.offsetX}}</b></p>
                <p><span data-translate="by_vertical"></span> <b id="remappedY">{{mousePosition.offsetY}}</b></p>
              </li>
              <li class="panel-item compare-mode__element">
                <range-control :data-target="'original'" :max-range="200" range-category="scale" :initial-value="100" v-on:range="changeScale($event)"/>
              </li>
              <li class="panel-item compare-mode__element">
                <move-buttons :data-target="'original'" v-on:move="moveCanvas($event)"/>
              </li>
              <li class="panel-item compare-mode__element text-center">
                <rotate-buttons :data-target="'original'" v-on:move="moveCanvas($event)"/>
              </li>
            </ul>
          </li>

          <li class="compare-mode__element">
            <ul class="panel-list-item ">

              <li class="panel-item">
                <file-upload :data-target="'copy'" v-on:file="onImageUpload($event)"></file-upload>
              </li>
              <li class="panel-item compare-mode__element">
                <range-control :data-target="'copy'" :max-range="200" range-category="scale" :initial-value="100" v-on:range="changeScale($event)"/>
              </li>
              <li class="panel-item compare-mode__element">
                <move-buttons :data-target="'copy'" v-on:move="moveCanvas($event)"/>
              </li>
              <li class="panel-item compare-mode__element text-center">
                <rotate-buttons :data-target="'copy'" v-on:move="moveCanvas($event)"/>
              </li>
              <li class="panel-item">
                <range-control :data-target="'copy'" :max-range="100" :range-category="'transparency'" :initial-value="50" v-on:range="changeOpacity($event)"/>
              </li>
              <li class="panel-item compare-mode__element">
                <button class="btn full-width-btn" title="Mirror" data-translate="mirror" @click="moveCanvas({direction: 'mirror', target: 'copy'})"></button>
              </li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
    <img src="" alt="" style="display: none;" ref="newImg" @load="createAndDrawImage">
    <!-- End of main -->
  </main>
</template>

<script>

import MoveButtons from "@/components/MoveButtons";
import RotateButtons from "@/components/RotateButtons";
import RangeControl from "@/components/RangeControl";
import FileUpload from "@/components/FileUpload";
export default {
  name: 'Main',
  components: {
    RotateButtons,
    MoveButtons,
    RangeControl,
    FileUpload,
  },
  props: {
  },
  data() {
    return {
      canvasPosition: {
        original: {
          top: 0,
          left: 0,
          mirror: 0,
          rotate: 0,
          transparency: 0,
          scale: 100
        },
        copy: {
          top: 0,
          left: 0,
          mirror: 0,
          rotate: 0,
          transparency: 50,
          scale: 100
        }
      },
      mousePosition: {
        offsetX: null,
        offsetY: null
      },
      originalPictureSize: {
        height: 0,
        width: 0
      },
      newPictureSize: {
        height: 0,
        width: 0
      }
    }
  },
  methods: {
    moveCanvas($event) {
      const {direction, target} = $event
      const step = 2;
      this.moveCopy(step, direction, target);
    },

    moveCopy(step, dataDirection, target) {
      switch (dataDirection) {
        case 'up':
          this.canvasPosition[target].top -= step;
          break;
        case 'bottom':
          this.canvasPosition[target].top += step;
          break;
        case 'left':
          this.canvasPosition[target].left -= step;
          break;
        case 'right':
          this.canvasPosition[target].left += step;
          break;
        case 'mirror':
          this.canvasPosition[target].mirror = this.canvasPosition[target].mirror === 0 ? 180 : 0;
          break;
        case 'rotate90':
          this.canvasPosition[target].rotate += 90;
          break;
        case 'rotate-90':
          this.canvasPosition[target].rotate -= 90;
          break;
        case 'rotate1':
          this.canvasPosition[target].rotate++;
          break;
        case 'rotate-1':
          this.canvasPosition[target].rotate--;
          break;
      }
    },

    transformProperty(target) {
      const shiftY = 'translateY(' + this.canvasPosition[target].top + 'px)';
      const shiftX = 'translateX(' + this.canvasPosition[target].left + 'px)';
      const mirror = 'rotateY(' + this.canvasPosition[target].mirror + 'deg)';
      const rotate = 'rotate(' + this.canvasPosition[target].rotate + 'deg)';
      return `transform: ${shiftX} ${shiftY} ${mirror} ${rotate}`;
    },

    scaleOpacityProperty(target) {
      const scale = `scale(${this.canvasPosition[target].scale/100})`;
      const opacity = `opacity: ${1 - this.canvasPosition[target].transparency/100}`;
      return `transform: ${scale}; ${opacity}`;
    },

    changeOpacity($event) {
      const {target, value} = $event;
      this.canvasPosition[target].transparency = value;
    },

    changeScale($event) {
      const {target, value} = $event;
      this.canvasPosition[target].scale = value;
    },

    onImageUpload($event) {
      const {target, value} = $event;
      this.inputHeight = document.getElementById('canvasHeight');
      this.inputWidth = document.getElementById('canvasWidth');
      this.inputHeight.removeAttribute('disabled');
      this.inputWidth.removeAttribute('disabled');
      this.$refs.newImg.src = value;
      this.$refs.newImg.id = target;
    },

    createAndDrawImage() {
      const newImg = this.$refs.newImg;
      const target = newImg.id;
      const currentCanvas = document.getElementById(target +"-image");
      this.resizeCanvasToImg(currentCanvas);
      let ctx = currentCanvas.getContext("2d");
      ctx.drawImage(this.$refs.newImg, 0, 0);
    },
    
    resizeCanvasToImg(canvas) {
      const ctx = canvas.getContext("2d");
      ctx.fillStyle = 'white';
      ctx.fillRect(0, 0, canvas.height, canvas.width);
      canvas.width = this.$refs.newImg.width;
      canvas.height = this.$refs.newImg.height;
      this.originalPictureSize.height = canvas.height;
      this.originalPictureSize.width = canvas.width;
    },

    showMouseCoords($event) {
      if (!this.originalPictureSize.height) return;
      const {offsetX, offsetY} = $event;
      const coefficient = this.newPictureSize.height / this.originalPictureSize.height;
      this.mousePosition.offsetX = parseInt(offsetX * coefficient);
      this.mousePosition.offsetY = parseInt(offsetY * coefficient);
    },

    clearMouseCoords() {
      this.mousePosition.offsetX = null;
      this.mousePosition.offsetY = null;
    },

    calcNewImageSize($event, dimension) {
      const newDimension = parseInt($event.target.value);
      if (dimension === 'height') {
        this.newPictureSize.width = parseInt(newDimension * this.originalPictureSize.width / this.originalPictureSize.height);
      } else {
        this.newPictureSize.height = parseInt(newDimension * this.originalPictureSize.height / this.originalPictureSize.width);
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
