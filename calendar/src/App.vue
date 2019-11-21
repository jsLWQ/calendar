<template>
  <div id="app">
    <!-- <router-view/> -->
    <calen-dar />
  </div>
</template>
<script>
  export default {
    components: {
      calenDar: () => import('./components/calendar')
    }
  }
</script>
<style lang="scss" scoped>
#app {
  display: flex;
  justify-content: center;
  margin: 100px auto;
}
</style>
