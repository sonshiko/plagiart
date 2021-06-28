<template>
  <div class="move-btn-wrapper">
    <div class="move-btn-container">
      <button class="btn move-btn" data-title-translate="up"  data-id="Up" @click="moveImage('up')"></button>
      <button class="btn move-btn" data-title-translate="right" data-id="Right" @click="moveImage('right')"></button>
      <button class="btn move-btn" data-title-translate="left" data-id="Left" @click="moveImage('left')"></button>
      <button class="btn move-btn" data-title-translate="bottom" data-id="Bottom" @click="moveImage('bottom')"></button>
    </div>
  </div>
</template>
<script>
export default {
  name: 'MoveButtons',
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
