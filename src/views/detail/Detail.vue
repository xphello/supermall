<template>
  <div id="detail">
    <detail-nav-bar ref="nav" @titleClick="titleClick" />
    <scroll
      class="content1"
      ref="scroll"
      :probe-type="3"
      @scroll="contentScroll"
    >
      <detail-swiper :topImages="topImages" />
      <detail-base-info :goods="goods" />
      <detail-shop-info :shop="shop" />
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad" />
      <detail-param-info ref="params" :param-info="paramInfo" />
      <detail-comment-info ref="comment" :comment-info="commentInfo" />
      <goods-list ref="recommend" :goods="recommends" />
    </scroll>
    <detail-bottom-bar @addToCart="addToCart" />
    <back-top @click.native="backTop" v-show="isShowBackTop" />
    <toast :message="message" :is-show="show" />
  </div>
</template>

<script>
import DetailNavBar from "./childComps/DetailNavBar";
import DetailSwiper from "./childComps/DetailSwiper";
import DetailBaseInfo from "./childComps/DetailBaseInfo";
import DetailShopInfo from "./childComps/DetailShopInfo";
import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
import DetailParamInfo from "./childComps/DetailParamInfo";
import DetailCommentInfo from "./childComps/DetailCommentInfo";
import DetailBottomBar from "./childComps/DetailBottomBar";
import GoodsList from "components/content/goods/GoodsList";

import Scroll from "components/common/scroll/Scroll";
import { itemListenerMixin, backTopMixin } from "common/mixin";
import { debounce } from "common/utils";

import { getDetail, Goods, Shop, GoodsParam } from "network/detail";

import { mapActions } from "vuex";

import Toast from "components/common/toast/Toast";

export default {
  name: "Detail",
  components: {
    DetailNavBar,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    DetailGoodsInfo,
    DetailParamInfo,
    DetailCommentInfo,
    GoodsList,
    Scroll,
    DetailBottomBar,
    Toast
  },
  data() {
    return {
      id: null,
      topImages: [],
      goods: {},
      shop: {},
      detailImages: [],
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommends: [],
      themeTopYs: [],
      getThemeTopY: null,
      currentIndex: 0,
      isShowBackTop: false,
      message: "",
      show: false
    };
  },
  mixins: [itemListenerMixin, backTopMixin],
  methods: {
    ...mapActions(["addCart"]),
    imageLoad() {
      this.$refs.scroll.refresh();
      this.getThemeTopY();
    },
    titleClick(index) {
      this.$refs.scroll.scrollTo(0, -this.themeTopYs[index], 100);
    },
    contentScroll(position) {
      //1.获取y值
      const positionY = -position.y;
      //2.positionY和主题中值进行对比
      //[0, 3182, 3689, 3884
      //positionY 在0 和 3182 之间, index = 0
      //positionY 在3182 和 3689 之间, index = 1
      //positionY 在3689 和 3884 之间, index = 2
      //positionY 超过3884, index = 3
      let length = this.themeTopYs.length;
      // for (let i = 0; i < length; i++) {
      //   if (
      //     this.currentIndex !== i &&
      //     ((i < length - 1 &&
      //       positionY >= this.themeTopYs[i] &&
      //       positionY < this.themeTopYs[i + 1]) ||
      //       (i === length - 1 && positionY >= this.themeTopYs[i]))
      //   ) {
      //     this.currentIndex = i;
      //     this.$refs.nav.currentIndex = this.currentIndex;
      //   }
      // }
      for (let i = 0; i < length - 1; i++) {
        if (
          this.currentIndex !== i &&
          positionY >= this.themeTopYs[i] &&
          positionY < this.themeTopYs[i + 1]
        ) {
          this.currentIndex = i;
          this.$refs.nav.currentIndex = this.currentIndex;
        }
      }

      this.isShowBackTop = -position.y > 1000;
    },
    addToCart() {
      //1.获取购物车需要展示的数据
      const product = {};
      product.image = this.topImages[0];
      product.title = this.goods.title;
      product.desc = this.goods.desc;
      product.price = this.goods.price;
      product.id = this.id;
      //2.将商品添加到购物车里
      this.addCart(product).then(res => {
        // this.show = true;
        // this.message = res;
        // setTimeout(() => {
        //   this.show = false;
        //   this.message = "";
        // }, 1500);
        this.$toast.show(res, 2000);
      });
    }
  },
  created() {
    this.getThemeTopY = debounce(() => {
      this.themeTopYs = [];
      this.themeTopYs.push(0);
      this.themeTopYs.push(this.$refs.params.$el.offsetTop);
      this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
      this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);
      this.themeTopYs.push(Number.MAX_VALUE);
    });

    //1.保存传入的id
    this.id = this.$route.params.id;

    //2.根据id请求详情数据
    getDetail(this.id).then(() => {
      var res = {
        topImages: [
          "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
          "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
          "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg"
        ],
        itemInfo: {
          desc: "新款上市",
          discountDesc: "活动价",
          count: 0,
          price: "￥38.50",
          oldPrice: "￥69.00",
          newPrice: "38.50",
          title:
            "2021春秋季新款原宿风长袖T恤女潮学生韩版拼接白色复古港味宽松百搭上衣打底"
        },
        columns: ["销量1580", "收藏33人", "默认快递"],
        services: [
          {
            icon: "",
            name: "7天无理由退货"
          },
          {
            icon: "",
            name: "退货补运费"
          },
          {
            icon: "",
            name: "全国包邮"
          },
          {
            icon: "",
            name: "72小时发货"
          }
        ],
        shopInfo: {
          cFans: 17145,
          cGoods: 99,
          shopLogo:
            "https://s10.mogucdn.com/mlcdn/c45406/180913_387kgl3j21ff29lh04181iek48a6h_225x225.png",
          name: "仲陌美",
          cSells: 57876,
          score: [
            {
              isBetter: false,
              name: "描述相符",
              score: 4.64
            },
            {
              isBetter: true,
              name: "价格合理",
              score: 5
            },
            {
              isBetter: false,
              name: "质量满意",
              score: 4.62
            }
          ]
        },
        detailInfo: {
          desc: "新款上市",
          detailImage: [
            {
              key: "穿看效果",
              list: [
                "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg",
                "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
                "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
                "https://img12.360buyimg.com/n2/s308x308_jfs/t1/148700/37/7522/79662/5f50f211E8aeab425/56a8706b72a9d297.jpg!q70.dpg"
              ]
            }
          ]
        },
        itemParamsInfo: {
          set: [
            {
              key: "厚薄",
              value: "普通"
            },
            {
              key: "厂名",
              value: "美衣梦服饰有限公司"
            },
            {
              key: "尺码",
              value: "S,XL,L,M"
            },
            {
              key: "衣长",
              value: "常规款"
            },
            {
              key: "季节",
              value: "春秋"
            },
            {
              key: "材质",
              value: "聚酯纤维"
            }
          ]
        },
        itemParamsRule: {
          tables: [
            [
              ["尺码", "M", "L", "XL", "XXL"],
              ["胸围", "100", "104", "108", "112"],
              ["衣长", "54", "55", "56", "57"],
              ["袖长", "61", "62", "63", "64"]
            ]
          ]
        },
        rate: {
          cRate: 121,
          list: [
            {
              content: "物美价廉，非常棒！",
              created: 1535694719,
              images: [],
              style: "颜色：上衣+裤子 ,尺码：L",
              user: {
                avatar:
                  "https://s10.mogucdn.com/mlcdn/c45406/180913_387kgl3j21ff29lh04181iek48a6h_225x225.png",
                uname: "半梦半醒"
              }
            }
          ]
        },
        recommendsList: [
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
              "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
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
              "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
            title: "十点抢券",
            price: 100
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
              "https://img12.360buyimg.com/n2/s308x308_jfs/t1/151685/14/10874/501165/5fe03b00Eaf1b2e2c/8afa0fda957c0fea.jpg!q70.dpg",
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
              "https://img14.360buyimg.com/n2/s308x308_jfs/t1/156412/16/8274/126712/60163f02Ea0bf16ed/fd9a38c9a0864ceb.jpg!q70.dpg",
            title: "十点抢券",
            price: 100
          }
        ]
      };
      //1.获取顶部图片轮播数据
      this.topImages = res.topImages;

      //2.获取商品信息
      this.goods = new Goods(res.itemInfo, res.columns, res.services);

      //3.店铺信息
      this.shop = new Shop(res.shopInfo);

      //4.商品详情数据
      this.detailInfo = res.detailInfo;

      //5.获取参数信息
      this.paramInfo = new GoodsParam(res.itemParamsInfo, res.itemParamsRule);

      //6.评论信息
      this.commentInfo = res.rate.list[0];

      //7.推荐数据
      this.recommends = res.recommendsList;

      // this.themeTopYs = [];
      // this.$nextTick(() => {
      //   //根据最新数据，对应的DOM是已经被渲染出来
      //   //但是图片是依然没有加载完（目前获取到offsetTop不包含其中的图片）
      //   //offsetTop值不对的时候，一般都是因为图片问题
      //   this.themeTopYs.push(0);
      //   this.themeTopYs.push(this.$refs.params.$el.offsetTop);
      //   this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
      //   this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);
      //   console.log(this.themeTopYs);
      // });
    });
  },
  mounted() {},
  destroyed() {
    //取消全局事件监听
    this.$bus.$off("itemImgListener", this.itemImgListener);
  }
};
</script>

<style>
#detail {
  height: 100vh;
  position: relative;
  z-index: 9;
  background-color: #fff;
}
.content1 {
  height: calc(100% - 44px);
  background-color: #fff;
}
</style>
