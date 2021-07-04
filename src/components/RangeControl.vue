<template>
  <form name="scaleForm">
    <label :for="rangeCategory" :data-translate="rangeCategory"></label>
    {{value}}%
    <input type="range" :id="rangeCategory" :name="rangeCategory" :data-id="rangeCategory"
           class="input"
           v-model="value"
           @input="emitRange()"
           min="0" :max="maxRange" step="1">

  </form>
</template>
<script>
export default {
  name: 'RangeControl',
  data() {
    return {
      value: 0
    }
  },
  props: {
    dataTarget: String,
    maxRange: Number,
    rangeCategory: String,
    initialValue: Number,
  },
  mounted() {
    this.value = this.initialValue;
  },
  methods: {
    emitRange() {
      this.$emit('range', {target: this.dataTarget, value: this.value})
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
