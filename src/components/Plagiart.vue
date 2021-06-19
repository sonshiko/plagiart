<template>
  <Header/>
  <Main/>
  <Footer/>
  <Modal/>
</template>

<script>
import Header from "./Header";
import Main from "./Main";
import Footer from "./Footer";
import Modal from "./Modal";
import json from "../assets/transliterate.json"
export default {
  name: 'Plagiart',
  components: {Modal, Footer, Main, Header},
  props: {},
  beforeCreate() {
    document.body.classList.add('scale-mode');
  },
  mounted() {
    this.main();
  },
  methods: {

    main() {
      const plagiart = this;
      this.initMoveButtons();
      this.switchSidebar();
      this.initTranslate();
      this.canvasOrig = document.getElementById("original-image");
      this.canvasCopy = document.getElementById("copy-image");
      this.canvasHeight = this.canvasOrig.parentElement.clientHeight;
      this.canvasWidth = this.canvasOrig.parentElement.clientWidth;
      this.canvasOrig.width = this.canvasWidth;
      this.canvasOrig.height = this.canvasHeight;
      this.canvasCopy.width = this.canvasWidth;
      this.canvasCopy.height = this.canvasHeight;

      this.inputHeight = document.getElementById('canvasHeight');
      this.inputWidth = document.getElementById('canvasWidth');
      let ctxOrig = this.canvasOrig.getContext("2d");
      let ctxCopy = this.canvasCopy.getContext("2d");

      let imageOrig = new Image();
      imageOrig.onload = function () {
        plagiart.resizeCanvasToImg(plagiart.canvasOrig, imageOrig);
        plagiart.origImgHeight = imageOrig.height;
        plagiart.origImgWidth = imageOrig.width;
        plagiart.inputHeight.removeAttribute('disabled');
        plagiart.inputHeight.value = 0;
        plagiart.inputWidth.removeAttribute('disabled');
        plagiart.inputWidth.value = 0;
        ctxOrig.drawImage(imageOrig, 0, 0);
      };
      let imageCopy = new Image();
      imageCopy.onload = function () {
        plagiart.resizeCanvasToImg(plagiart.canvasCopy, imageCopy);
        ctxCopy.drawImage(imageCopy, 0, 0);
      };

      this.uploadPicture('original', imageOrig);
      this.uploadPicture('clone', imageCopy);

      this.changeOpacity(this.canvasCopy);
      this.changeScale(this.canvasCopy, document.getElementById('scaleCopy'));
      this.changeScale(this.canvasOrig, document.getElementById('scaleOrig'));

      this.inputHeight.addEventListener('keyup', function () {
        plagiart.inputWidth.value = this.value * plagiart.origImgWidth / plagiart.origImgHeight;
      })

      this.inputWidth.addEventListener('keyup', function () {
        plagiart.inputHeight.value = this.value * plagiart.origImgHeight / plagiart.origImgWidth;
      })

      this.canvasOrig.addEventListener('mousemove', function (event) {
        const canvas = event.target;
        const rect = canvas.getBoundingClientRect();
        const coords = {
          x: event.clientX - rect.x,
          y: event.clientY - rect.y,
        };
        const oldCanvas = {
          width: rect.width,
          heigth: rect.height
        };
        const newCanvas = {
          width: plagiart.inputWidth.value === 0 ? rect.width : plagiart.inputWidth.value,
          height: plagiart.inputHeight.value === 0 ? rect.height : plagiart.inputHeight.value
        };
        const newCoords = {
          x: Math.round(newCanvas.width * coords.x / oldCanvas.width),
          y: Math.round(newCanvas.height * coords.y / oldCanvas.heigth)
        };

        document.getElementById('remappedX').innerText = newCoords.x;
        document.getElementById('remappedY').innerText = newCoords.y;
      })
      this.mode = 'scale';

      this.modeInputs = document.getElementsByClassName('mode');
      for (let i = 0; i < this.modeInputs.length; i++) {
        this.modeInputs[i].addEventListener('change', function () {
          if (plagiart.mode === 'scale') {
            plagiart.mode = 'compare';
            document.body.classList.remove('scale-mode');
            document.body.classList.add('compare-mode');
          } else {
            plagiart.mode = 'scale';
            document.body.classList.remove('compare-mode');
            document.body.classList.add('scale-mode');
          }
        })
      }
    },


    changeOpacity(canvasCopy) {
      const opacityInput = document.getElementById('transparency');
      opacityInput.value = 50;
      canvasCopy.style.opacity = 1 - opacityInput.value / 100;
      opacityInput.addEventListener('input', function (e) {
        const newOpacity = e.target.value;
        canvasCopy.style.opacity = 1 - newOpacity / 100;
      });

    },

    changeScale(canvas, scaleInput) {
      scaleInput.value = 100;
      canvas.style.transform = `scale(${scaleInput.value / 100})`;
      scaleInput.addEventListener('input', function (e) {
        const newScale = e.target.value;
        canvas.style.transform = `scale(${newScale / 100})`;
      });

    },

    resizeCanvasToImg(canvas, image) {
      const ctx = canvas.getContext("2d");
      ctx.fillStyle = 'white';
      ctx.fillRect(0, 0, canvas.height, canvas.width);
      canvas.width = image.width;
      canvas.height = image.height;

    },

    initMoveButtons() {
      const plagiart = this;
      const buttons = [
        {dataId: 'Up', direction: 'up'},
        {dataId: 'Left', direction: 'left'},
        {dataId: 'Bottom', direction: 'bottom'},
        {dataId: 'Right', direction: 'right'},
        {dataId: 'Mirror', direction: 'mirror'},
        {dataId: 'Rotate90', direction: 'rotate90'},
        {dataId: 'Rotate-90', direction: 'rotate-90'},
        {dataId: 'Rotate1', direction: 'rotate1'},
        {dataId: 'Rotate-1', direction: 'rotate-1'},
      ];

      buttons.forEach(item => {
        const currentButtons = document.querySelectorAll('[data-id=' + item.dataId + ']');
        currentButtons.forEach(function (button) {
          button.addEventListener('click', function () {
            plagiart.moveCanvas(item.direction, button.getAttribute('data-target'));
          });
        })
      })
    },

    moveCanvas(direction, target) {
      let wrapper = document.getElementById('original-wrapper');
      if (target === 'copy') {
        wrapper = document.getElementById('copy-wrapper');
      }
      const step = 2;
      switch (direction) {
        case 'up':
          moveCopy(-step, 'data-vertical');
          break;
        case 'left':
          moveCopy(-step, 'data-horizontal');
          break;
        case 'bottom':
          moveCopy(step, 'data-vertical');
          break;
        case 'right':
          moveCopy(step, 'data-horizontal');
          break;
        case 'mirror':
          moveCopy(step, 'mirror');
          break;
        case 'rotate90':
          moveCopy(step, 'rotate90');
          break;
        case 'rotate1':
          moveCopy(step, 'rotate1');
          break;
        case 'rotate-90':
          moveCopy(step, 'rotate-90');
          break;
        case 'rotate-1':
          moveCopy(step, 'rotate-1');
          break;
      }
      function moveCopy(step, dataDirection) {
      let transformVProperty = (wrapper.getAttribute('data-vertical') === null) ? 0 : parseInt(wrapper.getAttribute('data-vertical'));
      let transformHProperty = (wrapper.getAttribute('data-horizontal') === null) ? 0 : parseInt(wrapper.getAttribute('data-horizontal'));
      let transform3DProperty = (wrapper.getAttribute('data-mirror') === null) ? 0 : parseInt(wrapper.getAttribute('data-mirror'));
      let transform2DProperty = (wrapper.getAttribute('data-rotate') === null) ? 0 : parseInt(wrapper.getAttribute('data-rotate'));
      //
      const imgRotationState = transform3DProperty === 0 ? 1 : -1;
      switch (dataDirection) {
        case 'data-horizontal':
          transformHProperty += step;
          wrapper.setAttribute('data-horizontal', transformHProperty);
          break;
        case 'data-vertical':
          transformVProperty += step;
          wrapper.setAttribute('data-vertical', transformVProperty);
          break;
        case 'mirror':
          transform3DProperty = (transform3DProperty === 180 ? 0 : 180);
          wrapper.setAttribute('data-mirror', transform3DProperty);
          break;
        case 'rotate90':
          transform2DProperty = transform2DProperty + 90 * imgRotationState;
          wrapper.setAttribute('data-rotate', transform2DProperty);
          break;
        case 'rotate1':
          transform2DProperty = transform2DProperty + 1 * imgRotationState;
          wrapper.setAttribute('data-rotate', transform2DProperty);
          break;
        case 'rotate-90':
          transform2DProperty = transform2DProperty - 90 * imgRotationState;
          wrapper.setAttribute('data-rotate', transform2DProperty);
          break;
        case 'rotate-1':
          transform2DProperty = transform2DProperty - 1 * imgRotationState;
          wrapper.setAttribute('data-rotate', transform2DProperty);
          break;
      }

      const shiftY = 'translateY(' + transformVProperty + 'px)';
      const shiftX = 'translateX(' + transformHProperty + 'px)';
      const shift3D = 'rotateY(' + transform3DProperty + 'deg)';
      const shift2D = 'rotate(' + transform2DProperty + 'deg)';
      wrapper.style.transform = shiftX + ' ' + shiftY + ' ' + shift3D + ' ' + shift2D;
    }
    },

    switchSidebar() {
      let switchSidebarBtn = document.getElementById('sidebar-position');
      let sidebarPosition = 'right';
      let documentWrapper = document.getElementById('main-container');
      switchSidebarBtn.addEventListener('click', function () {
        if (sidebarPosition === 'right') {
          sidebarPosition = 'left';
          document.getElementById('sidebar-position').classList.remove('right');
          document.getElementById('sidebar-position').classList.add('left');
        } else {
          sidebarPosition = 'right';
          document.getElementById('sidebar-position').classList.remove('left');
          document.getElementById('sidebar-position').classList.add('right');
        }
        documentWrapper.classList.toggle('wrapper-reverse');
      })
    },

    initTranslate() {
      setLanguagesAndStart(json);

      function setLanguagesAndStart(response) {
        let languagesSet = {};
        let language = 'uk';
        languagesSet = response;
        initTranslateButtons();
        translate();

        function initTranslateButtons() {
          const switchLanguageButtons = document.getElementsByClassName('language');
          for (let i = 0; i < switchLanguageButtons.length; i++) {
            switchLanguageButtons[i].addEventListener('change', function () {
              if (language === 'uk') {
                language = 'en';
              } else {
                language = 'uk';
              }
              translate();
            })
          }
        }

        function translate() {
          const langSet = languagesSet;
          // inner text translation
          const elementsToTranslate = document.querySelectorAll('[data-translate]');
          elementsToTranslate.forEach(element => {
            const translationKey = element.getAttribute('data-translate');
            element.innerHTML = langSet[translationKey][language];
          });
          // title attribute translation
          const titlesToTranslate = document.querySelectorAll('[data-title-translate]');
          titlesToTranslate.forEach(element => {
            const translationKey = element.getAttribute('data-title-translate');
            element.setAttribute('title', langSet[translationKey][language]);
          });
        }
      }
    },



    uploadPicture(elementId, imgSource) {
      const plagiart = this;
      let currentInput = document.getElementById(elementId);
      currentInput.addEventListener('change', function () {
        if (this.files && this.files[0]) {
          plagiart.readFile(elementId, imgSource, this.files[0])
        }
      });
    },

    readFile(imgType, imgSource, targetFile) {
      var FR = new FileReader();

      FR.addEventListener("load", function (e) {
        if (imgType === 'original') {
          imgSource.src = e.target.result;
        } else {
          imgSource.src = e.target.result;
        }
      });

      FR.readAsDataURL(targetFile);

    },

    showModal(modalId) {
      const currentModal = document.getElementById(modalId);
      currentModal.classList.add('visible');
      const wrapperToBlur = document.getElementById('app');
      wrapperToBlur.classList.add('blur');
      const reloadButton = document.getElementById('reload');
      reloadButton.addEventListener('click', function () {
        window.location.reload();
      })
    }
  },
}


</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
