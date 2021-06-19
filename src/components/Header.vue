<template>
  <header>
    <div class="header-controls header-controls-small">
    </div>
    <div class="header-controls">
      <label class="label-radio">
        <input type="radio" id="scaleMode" class="mode input-radio" name="mode" checked><span data-translate="scale_mode">Scale mode</span>
      </label>
      <label class="label-radio radio-inverted">
        <input type="radio" id="compareMode" name="mode" class="mode input-radio"><span data-translate="compare_mode"></span>
      </label>
    </div>
    <div class="header-controls header-controls-small">
      <label class="language-radio">
        <input type="radio" id="uk" class="language input-radio" name="language" checked><span></span>
      </label>
      <label class="language-radio">
        <input type="radio" id="en" name="language" class="language input-radio"><span></span>
      </label>
    </div>
  </header>
</template>

<script>
export default {
  name: 'Header',
  props: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
