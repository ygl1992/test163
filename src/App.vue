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

      <sc :lock-y="true">
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
      </sc>

      <scroller 
        class="my-scroller"
        :on-refresh="refresh"
        :on-infinite="infinite"
        ref="myRef"
      >
        <swiper 
          :list="swiperList" 
          v-model="swiperIndex"
          :loop="true"
        ></swiper>

        <marquee class="my-marquee">
          <marquee-item v-for="i in marqueeList">{{i.title}}</marquee-item>
        </marquee>

        <panel :list="dataList"></panel>
        <panel :list="moreDataList"></panel>
      </scroller>
      
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
import {ViewBox, XHeader, Tabbar, TabbarItem, Tab, TabItem, Scroller as sc, Swiper, Marquee, MarqueeItem, Panel } from 'vux'

var refreshKey = ['A', 'B01', 'B02', 'B03', 'B04', 'B05', 'B06', 'B07', 'B08', 'B09', 'B010'];
var key = 0;
var start = 0;
var end = start + 9;
var keyValue = 'A';
var initLoaded = false; // 初始化数据是否加载完成

function getRefreshKey(){
  key++;
  if( key==refreshKey.length ){
    key=0;
  }
  keyValue = refreshKey[key];
  return keyValue;
}

export default {
  name: 'app',
  components: {
    ViewBox,
    XHeader,
    Tabbar,
    TabbarItem,
    Tab,
    TabItem,
    sc,
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

      // 滚动新闻列表的数据
      this.marqueeList = data.live.filter(item => {
        return item.addata === null
      }).map(item => {
        return {
          title: item.title
        }
      })

      // 新闻列表的数据
      this.dataList = data.list.filter(item => {
        return item.addata === null
      }).map(item => {
        return {
          src: item.picInfo[0].url,
          title: item.title,
          desc: item.digest
        }
      })

      initLoaded = true;
    })
  },
  data () {
    return {
      swiperList: [],
      swiperIndex: 0,
      marqueeList: [],
      dataList: [],
      moreDataList: []
    }
  },
  methods: {
    refresh () {
      getRefreshKey();
      // 下拉数据加载
      this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html', {
        miss: '00',
        refresh: keyValue
      }).then(data => {
        // 下拉刷新新闻列表的数据
        this.dataList = data.list.filter(item => {
          return item.addata === null && item.picInfo[0]
        }).map(item => {
          return {
            src: item.picInfo[0].url,
            title: item.title,
            desc: item.digest
          }
        });

        this.$refs.myRef.finishPullToRefresh();
        this.$vux.toast.show(`当前一共刷新了${this.dataList.length}条数据`, 'top');
      })
    },
    infinite () {
      if(!initLoaded){
        this.$refs.myRef.finishInfinite();
        return;
      }

      this.$jsonp(`http://3g.163.com/touch/jsonp/sy/recommend/${start}-${end}.html`, {
        miss: '00',
        refresh: keyValue
      }).then(data => {
        //上拉加载更多
        setTimeout(()=>{
          var dataList = data.list.filter(item => {
            return item.addata === null && item.picInfo[0]
          }).map(item => {
            return {
              src: item.picInfo[0].url,
              title: item.title,
              desc: item.digest
            }
          });

          this.moreDataList = this.moreDataList.concat(dataList)

          start += 10;
          end = start + 9;

          this.$refs.myRef.finishInfinite();
        }, 1000);
      });
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
  .weui-media-box__hd,
  .weui-media-box__hd img{
    width: 102px;
    height: 78px;
  }
  .weui-media-box__bd{
    height: 78px;
  }
  .my-scroller {
    top: 90px;
  }
}

</style>