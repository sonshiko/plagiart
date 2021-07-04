<template>
  <div class="move-btn-wrapper">
    <div class="move-btn-container">
      <button class="btn move-btn" data-title-translate="up"  data-id="Up" @click="emitMoveImage('up')"></button>
      <button class="btn move-btn" data-title-translate="right" data-id="Right" @click="emitMoveImage('right')"></button>
      <button class="btn move-btn" data-title-translate="left" data-id="Left" @click="emitMoveImage('left')"></button>
      <button class="btn move-btn" data-title-translate="bottom" data-id="Bottom" @click="emitMoveImage('bottom')"></button>
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
    emitMoveImage(direction) {
      this.$emit('move', {direction: direction, target: this.dataTarget})
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
