<template>
  <figure>
    <img class="buttons" src="@/assets/button-orig.jpg" alt="Press this button to load original picture">
    <figcaption data-translate="original_picture"></figcaption>
  </figure>
  <label :for="dataTarget" class="input-label btn full-width-btn">
    <span data-translate="upload_image"></span>
    <input type="file"
           @change="uploadPicture()"
           :id="dataTarget" :name="dataTarget"
           placeholder="Original file"
           ref="uploadImageFile"
           accept="image/png, image/jpeg">
  </label>
</template>

<script>
export default {
  name: 'FileUpload',
  props: {
    dataTarget: String,
  },
  methods: {
    uploadPicture() {
      this.files = this.$refs.uploadImageFile.files;
      if (this.files && this.files[0]) {
        this.readFile(this.dataTarget, this.files[0])
      }
    },

    readFile(imgType, targetFile) {
      var FR = new FileReader();
      const fileUpload = this;
      FR.addEventListener("load", function (e) {
        fileUpload.$emit('file', {target: fileUpload.dataTarget, value: e.target.result})
      });
      FR.readAsDataURL(targetFile);

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
