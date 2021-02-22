<template>
  <div id="app">
    <slider />
  </div>
</template>

<script>
// 引入 iconfont
import './assets/js/iconfont.js';
import slider from '@/components/content/slider';
export default {
  name: 'App',
  components: {
    slider
  }
};
</script>

<style>
@import './assets/css/base.css';
</style>
