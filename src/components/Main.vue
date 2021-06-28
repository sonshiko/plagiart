<template>
  <main>
    <!-- Main -->
    <!-- -->
    <div id="main-container" class="wrapper">
      <!-- The area to load pictures -->
      <div class="img-holder wrapper-inner">
        <div class="canvas-wrapper" id="original-wrapper"  v-bind:style="styleObject['original']">
          <canvas id="original-image" class="original-image"></canvas>
        </div>
        <div class="canvas-wrapper compare-mode__element" id="copy-wrapper" v-bind:style="styleObject['copy']">
          <canvas id="copy-image"
                  class="copy-image"></canvas>
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
                <figure>
                  <img id="originalPicture" class="buttons" src="@/assets/button-orig.jpg" alt="Press this button to load original picture">
                  <figcaption data-translate="original_picture"></figcaption>
                </figure>
                <label for="original" class="input-label btn full-width-btn">
                  <span data-translate="upload_image"></span>
                  <input type="file"
                         id="original" name="original"
                         placeholder="Original file"
                         accept="image/png, image/jpeg">
                </label>
              </li>
              <li class="panel-item scale-mode__element">
                <label for="canvasWidth">
                  <span data-translate="canvas_width"></span>
                  <input type="number"
                         id="canvasWidth"
                         data-input="height"
                         class="sizeInput input" value="0"
                         disabled>
                </label>
                <label for="canvasHeight">
                  <span data-translate="canvas_height"></span>
                  <input type="number"
                         id="canvasHeight"
                         data-input="width"
                         class="sizeInput input"
                         value="0"
                         disabled>
                </label>
              </li>
              <li class="panel-item scale-mode__element">
                <b><p data-translate="point_coords"></p></b>
                <p><span data-translate="by_horizontal"></span> <b id="remappedX"></b></p>
                <p><span data-translate="by_vertical"></span> <b id="remappedY"></b></p>
              </li>
              <li class="panel-item compare-mode__element">
                <form name="scaleForm" oninput="rangevalue.value = scale.valueAsNumber">
                  <label for="scaleOrig" data-translate="scale"></label>
                  <output name="rangevalue" for="range">100</output>%
                  <input type="range" id="scaleOrig" name="scale" data-id="scale"
                         class="input"
                         value="100"
                         min="0" max="200" step="1">

                </form>

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
                <figure>
                  <!-- <button id="artistsPicture" class="buttons" type="button">Load the your own picture</button> -->
                  <img id="artistsPicture" class="buttons" src="@/assets/button-custom.jpg" alt="Press this button to load your picture">
                  <figcaption data-translate="your_picture"></figcaption>
                </figure>
                <label for="clone" class="input-label btn full-width-btn">
                  <span data-translate="upload_image"></span>
                  <input type="file"
                         id="clone" name="clone"
                         placeholder="Your file"
                         accept="image/png, image/jpeg">
                </label>
              </li>
              <li class="panel-item compare-mode__element">
                <form name="scaleForm" oninput="rangevalue.value = scale.valueAsNumber">
                  <label for="scaleOrig" data-translate="scale"></label>
                  <output name="rangevalue" for="range">100</output>%
                  <input type="range" id="scaleCopy" name="scale" data-id="scale"
                         class="input"
                         value="100"
                         min="0" max="200" step="1">

                </form>
              </li>
              <li class="panel-item compare-mode__element">
                <move-buttons :data-target="'copy'" v-on:move="moveCanvas($event)"/>
              </li>
              <li class="panel-item compare-mode__element text-center">
                <rotate-buttons :data-target="'copy'" v-on:move="moveCanvas($event)"/>
              </li>
              <li class="panel-item">
                <form name="scaleForm" oninput="opacityvalue.value = transparency.valueAsNumber">
                  <label for="transparency" data-translate="transparency"></label>
                  <output name="opacityvalue" for="transparency">50</output>%
                  <input type="range" id="transparency" name="transparency"
                         class="input"

                         value="50"
                         min="0" max="100" step="5">
                </form>
              </li>
              <li class="panel-item compare-mode__element">
                <button class="btn full-width-btn" title="Mirror" data-target="copy" data-id="Mirror" data-translate="mirror"></button>
              </li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
    <!-- End of main -->
  </main>
</template>

<script>

import MoveButtons from "@/components/MoveButtons";
import RotateButtons from "@/components/RotateButtons";
export default {
  name: 'Main',
  components: {
    RotateButtons,
    MoveButtons
  },
  props: {
  },
  data() {
    return {
      canvasPosition: {
        original: {
          top: 0,
          left: 0,
          mirror: false,
          rotate: 0
        },
        copy: {
          top: 0,
          left: 0,
          mirror: false,
          rotate: 0
        }
      },
      styleObject:{
        original: {
          transform: 'translateY(0) translateX(0)'
        },
        copy: {
          transform: 'translateY(0) translateX(0)'
        }
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
      // const imgRotationState = transform3DProperty === 0 ? 1 : -1;
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
          // todo add rotation and mirroring
          // case 'mirror':
          //   transform3DProperty = (transform3DProperty === 180 ? 0 : 180);
          //   wrapper.setAttribute('data-mirror', transform3DProperty);
          //   break;
          // case 'rotate90':
          //   transform2DProperty = transform2DProperty + 90 * imgRotationState;
          //   wrapper.setAttribute('data-rotate', transform2DProperty);
          //   break;
          // case 'rotate1':
          //   transform2DProperty = transform2DProperty + 1 * imgRotationState;
          //   wrapper.setAttribute('data-rotate', transform2DProperty);
          //   break;
          // case 'rotate-90':
          //   transform2DProperty = transform2DProperty - 90 * imgRotationState;
          //   wrapper.setAttribute('data-rotate', transform2DProperty);
          //   break;
          // case 'rotate-1':
          //   transform2DProperty = transform2DProperty - 1 * imgRotationState;
          //   wrapper.setAttribute('data-rotate', transform2DProperty);
          //   break;
      }
      const shiftY = 'translateY(' + this.canvasPosition[target].top + 'px)';
      const shiftX = 'translateX(' + this.canvasPosition[target].left + 'px)';
      // todo
      // const shift3D = 'rotateY(' + transform3DProperty + 'deg)';
      // const shift2D = 'rotate(' + transform2DProperty + 'deg)';
      // wrapper.style.transform = shiftX + ' ' + shiftY + ' ' + shift3D + ' ' + shift2D;
      this.styleObject[target]  = {
        transform: `${shiftX} ${shiftY}`
      }
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
