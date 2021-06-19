<template>
  <main>
    <!-- Main -->
    <!-- -->
    <div id="main-container" class="wrapper">
      <!-- The area to load pictures -->
      <div class="img-holder wrapper-inner">
        <div class="canvas-wrapper" id="original-wrapper">
          <canvas id="original-image" class="original-image"></canvas>
        </div>
        <div class="canvas-wrapper compare-mode__element" id="copy-wrapper">
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
                <div class="move-btn-wrapper">
                  <div class="move-btn-container">
                    <button class="btn move-btn" data-title-translate="up" 	data-target="original"  data-id="Up"></button>
                    <button class="btn move-btn" data-title-translate="right" data-target="original"  data-id="Right"></button>
                    <button class="btn move-btn" data-title-translate="left" data-target="original"  data-id="Left"></button>
                    <button class="btn move-btn" data-title-translate="bottom"	data-target="original"  data-id="Bottom"></button>
                  </div>
                </div>
              </li>
              <li class="panel-item compare-mode__element text-center">
                <button class="btn icon-btn" data-title-translate="clockwise_90" data-target="original" data-id="Rotate90">
                  <svg class="icon">
                    <use xlink:href="#rotate-right-90"></use>
                  </svg>
                </button>
                <button class="btn icon-btn" data-title-translate="counterclockwise_90" data-target="original" data-id="Rotate-90">
                  <svg class="icon">
                    <use xlink:href="#rotate-left-90"></use>
                  </svg>
                </button>
                <button class="btn icon-btn" data-title-translate="clockwise" data-target="original"  data-id="Rotate1">
                  <svg class="icon">
                    <use xlink:href="#rotate-right"></use>
                  </svg>
                </button>
                <button class="btn icon-btn" data-title-translate="counterclockwise" data-target="original"  data-id="Rotate-1">
                  <svg class="icon">
                    <use xlink:href="#rotate-left"></use>
                  </svg>
                </button>
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
                <div class="move-btn-wrapper">
                  <div class="move-btn-container">
                    <button class="btn move-btn" data-title-translate="up" 	data-target="copy" data-id="Up"></button>
                    <button class="btn move-btn" data-title-translate="right" data-target="copy" data-id="Right"></button>
                    <button class="btn move-btn" data-title-translate="left" data-target="copy" data-id="Left"></button>
                    <button class="btn move-btn" data-title-translate="bottom" data-target="copy" data-id="Bottom"></button>
                  </div>
                </div>
              </li>
              <li class="panel-item compare-mode__element text-center">
                <button class="btn icon-btn" data-title-translate="clockwise_90" data-target="copy" data-id="Rotate90">
                  <svg class="icon">
                    <use xlink:href="#rotate-right-90"></use>
                  </svg>
                </button>
                <button class="btn icon-btn" data-title-translate="counterclockwise_90" data-target="copy" data-id="Rotate-90">
                  <svg class="icon">
                    <use xlink:href="#rotate-left-90"></use>
                  </svg>
                </button>
                <button class="btn icon-btn" data-title-translate="clockwise" data-target="copy"  data-id="Rotate1">
                  <svg class="icon">
                    <use xlink:href="#rotate-right"></use>
                  </svg>
                </button>
                <button class="btn icon-btn" data-title-translate="counterclockwise" data-target="copy"  data-id="Rotate-1">
                  <svg class="icon">
                    <use xlink:href="#rotate-left"></use>
                  </svg>
                </button>
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
export default {
  name: 'Main',
  props: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
