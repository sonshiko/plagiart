<template>
  <button class="btn icon-btn" data-title-translate="clockwise_90" data-id="Rotate90" @click="moveImage('Rotate90')">
    <svg class="icon">
      <use xlink:href="#rotate-right-90"></use>
    </svg>
  </button>
  <button class="btn icon-btn" data-title-translate="counterclockwise_90" data-id="Rotate-90"  @click="moveImage('Rotate-90')">
    <svg class="icon">
      <use xlink:href="#rotate-left-90"></use>
    </svg>
  </button>
  <button class="btn icon-btn" data-title-translate="clockwise" data-id="Rotate1"  @click="moveImage('Rotate1')">
    <svg class="icon">
      <use xlink:href="#rotate-right"></use>
    </svg>
  </button>
  <button class="btn icon-btn" data-title-translate="counterclockwise" data-id="Rotate-1"  @click="moveImage('Rotate-1')">
    <svg class="icon">
      <use xlink:href="#rotate-left"></use>
    </svg>
  </button>
</template>
<script>
export default {
  name: 'RotateButtons',
  props: {
    dataTarget: String
  },
  methods: {
    moveImage(direction) {
      this.$emit('move', {direction: direction, target: this.dataTarget})
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
