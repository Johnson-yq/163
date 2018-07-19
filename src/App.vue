<template>
  <div id="app" class="app">
    <!-- <router-view/> -->
    <view-box>
      <x-header :left-options="{showBack:false}" slot="header" class="header">
        <div slot="left">直播</div>
        <div>网易</div>
        <div slot="right">搜索</div>
      </x-header>
      <sc :lock-y="true">
        <div class="tab">
          <tab>
            <tab-item selected>要闻</tab-item>
            <tab-item>娱乐</tab-item>
            <tab-item>时事</tab-item>
            <tab-item>体育</tab-item>
            <!-- <tab-item>社会</tab-item> -->
          </tab>
        </div>
      </sc>
      <scroller class="my_scroller" :on-refresh="refresh" :on-infinite="infinite" ref="myRef">

      <swiper :list="swiperList" :loop="true" v-model="swiperIndex"></swiper>

      <marquee class="my_marquee">
        <marquee-item v-for="item in marqueeList" :key="item.id">{{item.title}}</marquee-item>
      </marquee>

      <panel :list="panelList"></panel>
      </scroller>
  

      <tabbar slot="bottom" class="my_tabbar">
        <tabbar-item>
          <img slot="icon" src=" ">
          <span slot="label">Wechat</span>
        </tabbar-item>
        <tabbar-item>
          <img slot="icon" src=" ">
          <span slot="label">Message</span>
        </tabbar-item>
        <tabbar-item>
          <img slot="icon" src=" ">
          <span slot="label">Explore</span>
        </tabbar-item>
        <tabbar-item>
          <img slot="icon" src=" ">
          <span slot="label">News</span>
        </tabbar-item>
      </tabbar>

    </view-box>
  </div>
</template>

<script>
import {
  ViewBox,
  XHeader,
  Tabbar,
  TabbarItem,
  Tab,
  TabItem,
  Scroller as sc,
  Swiper,
  Marquee,
  MarqueeItem,
  Panel
} from "vux";

export default {
  name: "App",
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
  methods: {
    refresh() {
      let _self = this;
      this.$jsonp("http://3g.163.com/touch/jsonp/sy/recommend/0-9.html",{miss:'00',refresh:'A'}).then(data =>{
          //刷新数据
        this.panelList = data.news.map(item => {
          return {
            url: item.link,
            src: item.picInfo[0].url,
            title: item.title,
            desc: item.digest,
          }
        });
        this.$refs.myRef.finishPullToRefresh();

        this.$vux.toast.text(`当前一共引入了${this.panelList.length}条信息`, 'top');     //ES6字符串拼接
      })
      
    },

    infinite(){
      this.$jsonp("http://3g.163.com/touch/jsonp/sy/recommend/10-19.html").then(data =>{
        console.log(data.news);
      })
    }
  },


  data() {
    return {
      swiperList: [],
      swiperIndex: 2,
      panelList: [],
      marqueeList: [],
    };
  },


  created() {
    this.$jsonp("http://3g.163.com/touch/jsonp/sy/recommend/0-9.html").then(
      data => {
        //轮播图的数据处理
        this.swiperList = data.focus
          .filter(item => {
            //过滤点广告，返回一组对象
            return item.addata === null;
          })
          .map(item => {
            //轮询过滤后的每一项数据，返回对应swiper组件需要的对象数据类型
            return {
              url: item.link,
              img: item.picInfo[0].url,
              title: item.title
            };
          });

        //跑马灯的数据处理
        this.marqueeList = data.live
          .filter(item => {
            //过滤点广告，返回一组对象
            return item.addata === null;
          })
          .map(item => {
            //轮询过滤后的每一项数据，返回对应swiper组件需要的对象数据类型
            return {
              title: item.title
            };
          });

        // 新闻数据的处理
        this.panelList = data.news.map(item => {
          //轮询过滤后的每一项数据，返回对应swiper组件需要的对象数据类型
          return {
            url: item.link,
            src: item.picInfo[0].url,
            title: item.title,
            desc: item.digest,
          };
        });

      }
    );
  }
};
</script>



<style <style lang="less">
@import "~vux/src/styles/reset.less";

html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  overflow-x: hidden;
}

#app {
  height: 100%;

  .header {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 10;
  }

  .tab {
    // width: 1000px;
    margin-top: 46px;
  }
  .my_scroller{
    margin-top: 90px;
  }
  .my_marquee {
    margin: 10px;
  }
  .weui-media-box__hd img {
    height: 78px;
  }
  .my_tabbar{
    position: absolute;
    bottom: 0;
    left: 0;
  }
  .loading-layer{
    padding-bottom: 90px;
  }
}
</style>
