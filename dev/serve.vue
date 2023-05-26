<script>
import { defineComponent } from 'vue';
import Vue3Pagin8 from '@/vue3-pagin8.vue';

export default defineComponent({
  name: 'ServeDev',
  components: {
    Vue3Pagin8
  }
});
</script>

<template>
  <div id="app">
    <vue3-pagin8 />
  </div>
</template>
