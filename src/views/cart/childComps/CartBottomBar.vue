<template>
  <div class="bottom-bar">
    <div class="check-content">
      <check-button
        :is-checked="isSelectAll"
        class="check-button"
        @click.native="checkClick"
      />
      <span>全选</span>
    </div>
    <div class="price">合计：{{ totalPrice }}</div>
    <div class="calculate">去计算({{ checkLength }})</div>
  </div>
</template>

<script>
import CheckButton from "components/content/checkButton/CheckButton";
import { mapGetters } from "vuex";

export default {
  name: "CartBottomBar",
  components: {
    CheckButton
  },
  computed: {
    ...mapGetters(["cartList"]),
    totalPrice() {
      return (
        "￥" +
        this.cartList
          .filter(item => {
            return item.checked;
          })
          .reduce((preValue, item) => {
            return preValue + item.price * item.count;
          }, 0)
          .toFixed(2)
      );
    },
    checkLength() {
      return this.cartList.filter(item => item.checked).length;
    },
    isSelectAll() {
      if (this.cartList.length === 0) return false;
      for (let item of this.cartList) {
        if (!item.checked) {
          return false;
        }
      }
      return true;
    }
  },
  methods: {
    checkClick() {
      if (this.isSelectAll) {
        this.cartList.forEach(item => (item.checked = false));
      } else {
        this.cartList.forEach(item => (item.checked = true));
      }
    }
  }
};
</script>

<style>
.bottom-bar {
  height: 40px;
  background-color: #eee;
  position: relative;
  bottom: 40px;
  display: flex;
  align-items: center;
  font-size: 14px;
}
.check-content {
  width: 60px;
  display: flex;
  /* align-items: center; */
  margin-left: 10px;
}
.check-button {
  width: 20px;
  height: 20px;
  margin-right: 5px;
}
.price {
  flex: 1;
  margin-left: 30px;
}
.calculate {
  width: 90px;
  line-height: 40px;
  color: #fff;
  background-color: red;
  text-align: center;
}
</style>
