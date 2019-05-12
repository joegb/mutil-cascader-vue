<template>
  <div id="app">
    <mutiCascader
      title='兴趣定向'
      :datas='adsTagDatas'
      :defaultDatas='defaultTagDatas'
      @reciveDatas='reciveTagDatas'
      style='height: 400px;'
    ></mutiCascader>
  </div>
</template>

<script>
import mutiCascader from './components/muti-cascader'
import adsTagData from './assets/ad_tag.json'

export default {
  name: 'app',
  components: {
    mutiCascader
  },
  data () {
    return {
      adsTagDatas: adsTagData.data,
      defaultTagDatas: []
    }
  },
  methods: {
    reciveTagDatas (data) {
      console.log(data)
    }
  }
}
</script>

<style>
#app {
  height: 100vh;;
}
</style>
