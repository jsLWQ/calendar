<template>
  <div id="app">
    <!-- <router-view/> -->
    <calen-dar :YM="YM" />
    <!-- <button @click="update">改变</button> -->
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
    },
    methods: {
      update () {
        if(!this.YM.length) {
          this.YM = ['2019/10/1','2019/10/8','2019/10/12','2019/10/31','2019/11/12','2019/11/9']
          return
        }
        this.YM = []
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
