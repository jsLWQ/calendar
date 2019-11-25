<template>
  <div id="app">
    <!-- <router-view/> -->
    <calen-dar :YM="YM" />
  </div>
</template>
<script>
  export default {
    components: {
      calenDar: () => import('./components/calendar')
    },
    data () {
      return {
        YM: ['2019/10/1','2019/10/8','2019/10/12','2019/10/31','2019/11/12','2019/11/9']
      }
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
