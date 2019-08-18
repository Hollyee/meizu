<template>
  <div class="clearfix sort-box">
    <div class="fl sort-order">
      <a href="javascript:;" :class="{'active': activeSortKey === 'recommend'}" @click="setSortKey('recommend')">推荐</a>
      <a href="javascript:;" :class="{'active': activeSortKey === 'new'}" @click="setSortKey('new')">新品</a>
      <a href="javascript:;" :class="{'active': activeSortKey === 'low' || activeSortKey === 'high'}" @click="changePrice">
        价格 <i class="icon-font arrow" :class="sortArrow"></i>
      </a>
    </div>
    <div class="fr">
      <input v-model="haveStock" type="checkbox">
      <span>仅显示有货商品</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'sort-box',
  data () {
    return {
      activeSortKey: '',
      activePrice: 'low',
      haveStock: false
    };
  },
  computed: {
    sortArrow () {
      if (this.activePrice === 'low') {
        return 'icon-down';
      } else {
        return 'icon-up';
      }
    }
  },
  watch: {
    // 监测haveStock的值，如果它有变动，就把变动的值通过getStock传给父
    haveStock (val) {
      this.$emit('getStock', val);
    }
  },
  // 页面加载最先显示推荐页
  mounted () {
    this.setSortKey('recommend');
  },
  methods: {
    setSortKey (val) {
      this.activeSortKey = val;
      this.$emit('getKey', this.activeSortKey);
    },
    changePrice () {
      if (this.activeSortKey === this.activePrice) {
        this.activePrice = this.activePrice === 'low' ? 'high' : 'low';
      }
      this.setSortKey(this.activePrice);
    }
  }
};
</script>

<style lang="less" scoped>
  .sort-box{
    margin: 30px auto 20px;
    color: #666;

    .sort-order{

      a {
        margin-right: 50px;

        &:hover, &.active{
          color: #00c3f5;
          transition: all .2s;
        }

        &:hover .arrow{
          display: inline;
        }
      }

      .arrow{
        display: none;
      }
    }
  }
</style>
