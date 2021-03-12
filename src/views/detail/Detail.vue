<template>
  <div id="detail">
    <detail-nav-bar />
    <scroll class="content1" ref="scroll" :probe-type="3">
      <detail-swiper :topImages="topImages" />
      <detail-base-info :goods="goods" />
      <detail-shop-info :shop="shop" />
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad" />
      <detail-param-info :param-info="paramInfo" />
      <detail-comment-info :comment-info="commentInfo" />
      <goods-list :goods="recommends" />
    </scroll>
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
import GoodsList from "components/content/goods/GoodsList";

import Scroll from "components/common/scroll/Scroll";
import { itemListenerMixin } from "common/mixin";

import { getDetail, Goods, Shop, GoodsParam } from "network/detail";

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
    Scroll
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
      recommends: []
    };
  },
  mixins: [itemListenerMixin],
  methods: {
    imageLoad() {
      this.$refs.scroll.refresh();
    }
  },
  created() {
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
          price: "￥38.50",
          oldPrice: "￥69.00",
          realPrice: "38.50",
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
