<template>
  <div class='cascader_wrapper'>
    <mutiCascaderContent
      class='cascader_content'
      :title='mutiTitle'
      :datas='mutiDatas'
      @update='reciveDatas'
    ></mutiCascaderContent>

    <div class='select_wrapper' v-show='selectDatas.length'>
      <div class='header'>
       已选
      </div>
      <ul class='content'>
        <li class='item_wrapper' v-for='(item,index) in selectDatas' :key='`selected${index}`'>
          <span class='item_label'>
            {{ item.label }}
          </span>
          <span class='remove_btn' @click='handleRemove(item)'>
            <Icon type="md-close" />
          </span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>

import mutiCascaderContent from './muti-cascader-content'
import { Icon } from 'iview'

export default {
  name: 'muti_cascader',
  components: {
    mutiCascaderContent,
    Icon
  },
  props: {
    title: {
      type: String,
      default: '兴趣定向'
    },
    datas: {
      type: Array,
      default: () => ([])
    },
    defaultDatas: {
      type: Array,
      default: () => ([])
    }
  },
  data () {
    return {
      mutiDatas: [],
      selectDatas: []
    }
  },
  methods: {
    update () {
      let reciveDataArr = this.selectDatas.map(item => item.value)
      this.$emit('reciveDatas', reciveDataArr)
    },
    reciveDatas (data) {
      this.selectDatas = []
      this.handleReciveCycle(data)
      this.update()
    },
    handleReciveCycle (datas) {
      datas.map(item => {
        if (item.check) {
          this.selectDatas.push(item)
        } else {
          if (item.children && item.children.length) {
            this.handleReciveCycle(item.children)
          }
        }
        return item
      })
    },
    checkArrayLittle (datas) {
      for (let i = 0; i < datas.length; i++) {
        if (datas[i].check) {
          return true
        }
      }
      return false
    },
    checkArrayAll (datas) {
      for (let i = 0; i < datas.length; i++) {
        if (!datas[i].check) {
          return false
        }
      }
      return true
    },
    handleRemove (item) {
      this.selectDatas = this.selectDatas.filter(_item => {
        return _item.value !== item.value
      })

      this.update()

      this.handleRemoveCycle(this.mutiDatas, item.value)
    },
    handleRemoveCycle (datas, v) {
      return datas.map(item => {
        if (v === item.value) {
          item.check = false
          item.little = false
          if (item.children && item.children.length) {
            item.children = item.children.map(_item => {
              _item.check = false
              _item.little = false
              return _item
            })
          }
        } else {
          if (item.children && item.children.length) {
            item.children = this.handleRemoveCycle(item.children, v)
            if (this.checkArrayAll(item.children)) {
              item.check = true
              item.little = false
            } else if (this.checkArrayLittle(item.children)) {
              item.check = false
              item.little = true
            } else {
              item.check = false
              item.little = false
            }
          }
        }
        return item
      })
    },
    setDefaultDatas (datas) {
      this.selectDatas = []
      this.mutiDatas = this.datas
      this.handleDefaultCycle(this.mutiDatas, datas)
    },
    handleDefaultCycle (mutiDatas, datas) {
      return mutiDatas.map(item => {
        if (datas.includes(item.value)) {
          item.check = true
          item.little = false
          if (item.children && item.children.length) {
            item.children = item.children.map(_item => {
              _item.check = true
              _item.little = false
              return _item
            })
          }
          this.selectDatas.push(item)
        } else {
          item.check = false
          if (item.children && item.children.length) {
            item.children = this.handleDefaultCycle(item.children, datas)
            if (this.checkArrayLittle(item.children)) {
              item.little = true
            }
          }
        }
        return item
      })
    }
  },
  computed: {
    mutiTitle () {
      return this.title
    }
  },
  watch: {
    defaultDatas: {
      deep: true,
      immediate: true,
      handler: function (v) {
        if (v) {
          this.setDefaultDatas(v)
        }
      }
    }
  },
  created () {
    this.mutiDatas = this.datas
  },
  mounted () {
  }
}
</script>

<style lang="less" scoped>
li {
  list-style: none;
}
.cascader_wrapper {
  overflow: hidden;
  .cascader_content {
    float: left;
    margin-right: 2rem;
  }
  .select_wrapper {
    float: left;
    border: 1px solid #dee4f5;
    font-size: 14px;
    width: 140px;
    .header {
      text-align: center;
      margin-bottom: 0.5rem;
      border-bottom: 1px solid #dee4f5;
      background-color: #fafbfe;
    }
    .content {
      padding-left: 0.5rem;
      height: 200px;
      width: 100%;
      overflow-y:auto;
      .item_wrapper {
        position: relative;
        background: #f3d8d8;
        margin: 8px 12px 0;
        padding: 0 6px;
        .item_label {
          margin-right: 15px;
          height: 28px;
          line-height: 28px;
        }
        .remove_btn {
          position: absolute;
          right: 8px;
          top: 7px;
          line-height: 0;
          cursor: pointer;
        }
      }
    }
  }
}

</style>

<style>
::-webkit-scrollbar {
  -webkit-appearance: none;
  width: 7px;
}
::-webkit-scrollbar-thumb {
  border-radius: 4px;
  background-color: rgba(0, 0, 0, .5);
  -webkit-box-shadow: 0 0 1px rgba(255, 255, 255, .5);
}
</style>
