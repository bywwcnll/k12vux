<template>
  <div class="demo-list-box" id="demo_list_box" :style="{height: `${height}px`}">
      <flexbox :gutter="0" v-for="(list, index) in components" :key="index">
        <flexbox-item :span="1/3" v-for="(component, index) in list" :key="index" class="cbox vux-1px-t vux-tap-active" @click.native="go(component.name.toLowerCase(), component.hasHome)">
          <div class="vux-1px-r cbox-inner">
            <span class="demo-icon demo-icon-big" v-html="component.icon" :style="{color: component.color}"></span>
            <br>
            <span :style="{fontSize: component.name.length > 12 ? '12px' : ''}">{{component.name | camelCase}}</span>
          </div>
        </flexbox-item>
      </flexbox>
    </div>
</template>

<script>
import { Flexbox, FlexboxItem, VuxComponentListData as components } from 'k12vux'
import { mapState } from 'vuex'
import DemoList from './DemoList'

function camelCase (input) {
  let str = input.toLowerCase().replace(/-(.)/g, function (match, group1) {
    return group1.toUpperCase()
  })

  str = str.replace(/_(.)/g, function (match, group1) {
    return group1.toUpperCase()
  })
  return str.slice(0, 1).toUpperCase() + str.slice(1)
}

export default {
  name: 'demoList',
  components: {
    DemoList,
    Flexbox,
    FlexboxItem
  },
  filters: {
    camelCase
  },
  activated () {
    // console.log(this.components)
    document.querySelector('#demo_list_box').scrollTop = this.demoTop
  },
  mounted () {
    this.height = window.innerHeight - 46 - 53
  },
  methods: {
    go (name, hasHome) {
      if (!hasHome) {
        this.$router.push(`/component/${name}`)
      } else {
        this.$router.push(`/components/${name}/home`)
      }
    },
    split (array) {
      let chunks = []
      let count = Math.ceil(array.length / 3)
      while (count > 0) {
        chunks.push(array.slice((count - 1) * 3, count * 3))
        count--
      }
      chunks = chunks.reverse()
      const lastList = chunks[chunks.length - 1]
      const lastLength = lastList.length
      if (lastLength < 3) {
        for (let i = 0; i < 3 - lastLength; i++) {
          lastList.push({
            name: '----',
            icon: ''
          })
        }
      }
      return chunks
    }
  },
  data () {
    return {
      height: 0,
      components: this.split(components)
    }
  },
  computed: {
    ...mapState({
      demoTop: state => state.k12vux.demoScrollTop
    })
  }
}
</script>

<style lang="less" scoped>
.cbox {
  text-align: center;
}
.cbox-inner {
  padding: 15px 0;
  width: 100%;
  height: 100%;
}
.demo-list-box {
  margin-bottom: 10px;
  background-color: #fff;
  width: 100%;
  overflow: scroll;
  -webkit-overflow-scrolling: touch;
}
</style>
