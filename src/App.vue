<template>
  <div id="app">
    <view-box>
      <x-header 
        class="header"
        :left-options="{showBack: false}"
      >
        <div slot="left">直播</div>
        <div>网易</div>
        <div slot="right">搜索</div>
      </x-header>

      <scroller :lock-y="true">
        <div class="tab">
          <tab>
            <tab-item selected>推荐</tab-item>
            <tab-item>要闻</tab-item>
            <tab-item>游戏</tab-item>
            <tab-item>科技</tab-item>
            <tab-item>娱乐</tab-item>
            <tab-item>体育</tab-item>
            <tab-item>黑客</tab-item>
          </tab>
        </div>
      </scroller>

      <swiper 
        :list="swiperList" 
        v-model="swiperIndex"
        :loop="true"
      ></swiper>

      <marquee class="my-marquee">
        <marquee-item v-for="i in 5" :key="i">Hello world {{i}}</marquee-item>
      </marquee>

      <panel
        :list="dataList"
      ></panel>

      <tabbar>
        <tabbar-item>
          <img slot="icon" src="./assets/icon_nav_button.png">
          <span slot="label">Wechat</span>
        </tabbar-item>
        <tabbar-item>
          <img slot="icon" src="./assets/icon_nav_article.png">
          <span slot="label">推荐</span>
        </tabbar-item>
        <tabbar-item>
          <img slot="icon" src="./assets/icon_nav_cell.png">
          <span slot="label">我的</span>
        </tabbar-item>
      </tabbar>
    </view-box>
  </div>
</template>

<script>
import 'vux/src/styles/reset.less'
import {ViewBox, XHeader, Tabbar, TabbarItem, Tab, TabItem, Scroller, Swiper, Marquee, MarqueeItem, Panel } from 'vux'

export default {
  name: 'app',
  components: {
    ViewBox,
    XHeader,
    Tabbar,
    TabbarItem,
    Tab,
    TabItem,
    Scroller,
    Swiper,
    Marquee,
    MarqueeItem,
    Panel
  },
  created () {
    this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html').then(data=>{
      
      // 轮播图的数据
      this.swiperList = data.focus.filter(item => {
        return item.addata === null
      }).map(item => {
        return {
          url: item.link,
          img: item.picInfo[0].url,
          title: item.title
        }
      })

      // 新闻列表的数据
      // this.dataList = data.list.filter(item => {
      //   return item.addata === null
      // }).map(item => {
      //   return {
      //     src: '',
      //     title: '',
      //     desc: ''

      //     title: item.link,
      //     src: item.picInfo[0].url,
      //     desc: item.title
      //   }
      // })
      // src: 'http://placeholder.qiniudn.com/60x60/3cc51f/ffffff',
      // title: '标题一',
      // desc: '由各种物质组成的巨型球状天体，叫做星球。星球有一定的形状，有自己的运行轨道。'
    })
  },
  data () {
    return {
      swiperList: [],
      swiperIndex: 0,
      dataList: []
    }
  }
}
</script>


<style lang="less">

html,body{
  margin: 0;
  height: 100%;
  width: 100%;
  overflow-x: hidden;
}
#app{
  height: 100%;
  .header{
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    z-index: 9;
  }
  .tab{
    width: 600px;
    margin-top: 46px;
  }
  .my-marquee{
    margin: 10px;
  }
}

</style>