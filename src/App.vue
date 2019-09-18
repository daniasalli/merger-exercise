<template>
  <div id="app">
    <years-data msg="Merger Spot challenge"/>
  </div>
</template>

<script>
import YearsData from './components/YearsData.vue';

export default {
  name: 'app',
  components: {
    YearsData,
  },
};
</script>

<style lang="scss">
@import '../node_modules/font-awesome/css/font-awesome.min.css';

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
