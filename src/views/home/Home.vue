<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>

    <tab-control
      :titles="['流行', '新款', '精选']"
      @tabClick="tabClick"
      ref="tabControl1"
      class="tab-control"
      v-show="isTabFixed"
    />

    <scroll
      class="content"
      ref="scroll"
      :probe-type="3"
      @scroll="contentScroll"
      :pull-up-load="true"
      @pullingUp="loadMore"
    >
      <home-swiper :banners="banners" @swiperImageLoad="swiperImageLoad" />
      <recommend-view :recommends="recommends" />
      <feature-view />

      <tab-control
        :titles="['流行', '新款', '精选']"
        @tabClick="tabClick"
        ref="tabControl2"
      />
      <goods-list :goods="showGoods" />
    </scroll>

    <back-top @click.native="backClick" v-show="isShowBackTop" />
  </div>
</template>

<script>
import HomeSwiper from "./childComps/HomeSwiper";
import RecommendView from "./childComps/RecommendView";
import FeatureView from "./childComps/FeatureView";

import NavBar from "components/common/navbar/NavBar";
import TabControl from "components/content/tabControl/TabControl";
import GoodsList from "components/content/goods/GoodsList";
import Scroll from "components/common/scroll/Scroll";
import BackTop from "components/content/backTop/BackTop";

import { getHomeMultidata, getHomeGoods } from "network/home";
import { itemListenerMixin } from "common/mixin";

export default {
  name: "Home",
  components: {
    HomeSwiper,
    RecommendView,
    FeatureView,

    NavBar,
    TabControl,
    GoodsList,
    Scroll,
    BackTop
  },
  data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        pop: {
          page: 0,
          list: []
        },
        new: {
          page: 0,
          list: []
        },
        sell: {
          page: 0,
          list: []
        }
      },
      currentType: "pop",
      isShowBackTop: false,
      tabOffsetTop: 0,
      isTabFixed: false,
      saveY: 0
    };
  },
  computed: {
    showGoods() {
      return this.goods[this.currentType].list;
    }
  },
  mixins: [itemListenerMixin],
  methods: {
    /**
     * 事件监听相关方法
     */
    tabClick(index) {
      switch (index) {
        case 0:
          this.currentType = "pop";
          break;
        case 1:
          this.currentType = "new";
          break;
        case 2:
          this.currentType = "sell";
          break;
      }
      this.$refs.tabControl1.currentIndex = index;
      this.$refs.tabControl2.currentIndex = index;
    },
    backClick() {
      // this.$refs.scroll.scroll.scrollTo(0, 0, 500);
      this.$refs.scroll.scrollTo(0, 0);
    },
    contentScroll(position) {
      // console.log(position);
      //1.判断BackTop是否显示
      this.isShowBackTop = -position.y > 1000;

      //2.决定tabControl是否吸顶(position: fixed)
      this.isTabFixed = -position.y > this.tabOffsetTop;
    },
    loadMore() {
      this.getHomeGoods(this.currentType);
    },
    swiperImageLoad() {
      this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop;
    },
    /**
     * 网络请求相关方法
     */
    getHomeMultidata() {
      getHomeMultidata().then(res => {
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    },
    getHomeGoods(type) {
      const page = this.goods[type].page + 1;
      getHomeGoods(type, page).then(() => {
        var res = {
          data: {
            pop: {
              list: [
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 200
                },
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 300
                },
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 400
                },
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 500
                },
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 200
                },
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 300
                },
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 400
                },
                {
                  id: 1,
                  img:
                    "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 500
                }
              ]
            },
            new: {
              list: [
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                }
              ]
            },
            sell: {
              list: [
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                },
                {
                  id: 1,
                  img:
                    "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                  title: "十点抢券",
                  price: 100
                }
              ]
            }
          }
        };
        this.goods[type].list.push(...res.data[type].list);
        this.goods[type].page += 1;

        // this.$refs.scroll.scroll.finishPullUp();
        this.$refs.scroll.finishPullUp();
      });
    }
  },
  activated() {
    this.$refs.scroll.scrollTo(0, this.saveY, 0);
    this.$refs.scroll.refresh();
  },
  deactivated() {
    //1.保存Y值
    this.saveY = this.$refs.scroll.scroll.y;

    //2.取消全局事件监听
    this.$bus.$off("itemImgListener", this.itemImgListener);
  },
  created() {
    //1.请求多个数据
    this.getHomeMultidata();

    //2.请求商品数据
    this.getHomeGoods("pop");
    this.getHomeGoods("new");
    this.getHomeGoods("sell");
  },
  mounted() {},
  destroyed() {
    console.log("home destroy");
  }
};
</script>

<style scoped>
#home {
  /* padding-top: 44px; */
  height: 100vh;
  position: relative;
}
.home-nav {
  color: #fff;
  background-color: var(--color-tint);

  /* position: fixed;
  left: 0;
  top: 0;
  right: 0;
  z-index: 1; */
}
/* .tab-control {
  position: sticky;
  top: 44px;
  z-index: 9;
} */
/* .fixed {
  position: fixed;
  left: 0;
  right: 0;
  top: 44px;
} */
.content {
  overflow: hidden;
  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
}
/* .content {
  height: calc(100% - 93px);
  overflow: hidden;
  margin-top: 44px;
} */
.tab-control {
  position: relative;
  z-index: 9;
}
</style>
