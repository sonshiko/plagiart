<template>
  <div class="modal visible" id="modal" v-if="showModal">
    <h3>Something is went wrong. Click "OK" to reload page.</h3>
    <button class="btn" id="reload" v-on:click="reloadPage">Ok</button>
  </div>
</template>

<script>
export default {
  name: 'Modal',
  props: {
    showModal: Boolean
  },
  methods: {
    reloadPage() {
      window.location.reload();
    }
  },
  watch: {
    showModal: function(newVal, oldVal) {
      if (newVal !== oldVal) {
        const wrapperToBlur = document.getElementById('contentWrapper');
        if (newVal) {
          wrapperToBlur.classList.add('blur');
        } else {
          wrapperToBlur.classList.remove('blur');
        }
      }
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
