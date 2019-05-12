<template>
  <div class='wrapper_box'>
    <div class='wrapper'>
      <div class='header'>
        {{ title }}
      </div>
      <ul class='content'>
        <li class='title'>
          <Checkbox :value='isCheckAll' @on-change='handleCheckAll'>全选</Checkbox>
        </li>
        <li class='item_wrapper' v-for='(item,index) in entites' :key='item.id'>
          <span class='check_box'>
            <Checkbox v-model='item.check'
                      @on-change='handleSelect($event,item,index)'
                      :indeterminate='item.little'></Checkbox>
            <span class='check_label' @click='handleChildrenDatas(item,index,item.check)'>{{ item.label }}</span>
          </span>
          <span class='icon'>
            <Icon v-show='item.children' type="ios-arrow-forward" />
          </span>
        </li>
      </ul>
    </div>
    <multiCascader
      v-if='childrenDatas.length'
      :title='childrenTitle'
      :datas='childrenDatas'
      @update='childUpdate'></multiCascader>
  </div>
</template>

<script>
import { Checkbox, Icon } from 'iview'

export default {
  name: 'multiCascader',
  components: {
    Checkbox,
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
    }
  },
  data () {
    return {
      entites: [],
      childrenDatas: [],
      selectedDatas: [],
      selectedIndex: -1,
      childrenCheck: false
    }
  },
  computed: {
    isCheckAll () {
      return this.checkArrayAll(this.entites)
    }
  },
  methods: {
    checkArrayAll (datas) {
      for (let i = 0; i < datas.length; i++) {
        if (!datas[i].check) {
          return false
        }
      }
      return true
    },
    checkArrayLittle (datas) {
      for (let i = 0; i < datas.length; i++) {
        if (datas[i].check) {
          return true
        }
      }
      return false
    },
    handleCheckAll (checked) {
      this.entites = this.entites.map(item => {
        item.check = checked
        item.little = false
        if (item.children && item.children.length) {
          this.childrenCheck = checked
          item.children.map(_item => {
            _item.check = checked
            _item.little = false
            return _item
          })
        }
        return item
      })
      this.updateToParent()
    },
    handleChildrenDatas (item, index, checked) {
      this.selectedIndex = index
      if (item.children && item.children.length) {
        this.childrenDatas = item.children
        this.childrenTitle = item.label
      }
    },
    handleSelect (checked, item, index) {
      if (checked)item.little = false
      this.selectedIndex = index
      if (item.children && item.children.length) {
        item.children = item.children.map(_item => {
          _item.check = checked
          return _item
        })
        this.childrenDatas = item.children
        this.childrenTitle = item.label
      }
      this.updateToParent()
    },
    childUpdate (childDatas) {
      this.entites[this.selectedIndex].children = Object.assign([], childDatas)
      this.entites[this.selectedIndex].check = this.checkArrayAll(childDatas)
      if (this.entites[this.selectedIndex].check) {
        this.entites[this.selectedIndex].little = false
      } else {
        this.entites[this.selectedIndex].little = this.checkArrayLittle(childDatas)
      }
      this.updateToParent()
    },
    updateToParent () {
      this.$emit('update', this.entites)
    }
  },
  watch: {
    datas: {
      deep: true,
      immediate: true,
      handler: function (v) {
        if (v && v.length) {
          this.entites = Object.assign([], v)
        }
      }
    }
  },
  created () {
  },
  mounted () {
  }
}
</script>

<style lang="less" scoped>
li {
  list-style: none;
}
.wrapper_box {
  overflow: hidden;
  .wrapper {
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
      width: 100%;
      height: 200px;
      overflow-y:auto;
      .item_wrapper {
        display: flex;
        align-items: center;
        justify-content: space-between;
        .check_box {
          .check_label {
            display: inblock;
            width: 80;
          }
        }
        // .icon {
        //   padding-right: 0.5rem;
        // }
      }
    }
  }
}
</style>
