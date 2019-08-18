<template>
  <div>
    <v-header></v-header>
    <div class="category-wrapper">
      <div class="bread">
        <span>首页</span>
        <span class="arrow"> > </span>
        <span>全部</span>
        <span class="arrow"> > </span>
        <span class="last-bread">手机</span>
      </div>
      <filter-box :data="filterListData" @filter="getQuery"></filter-box>
      <sort-box @getKey="getSortKey" @getStock="getSortStock"></sort-box>
      <category-list @clickItem="goToDetail" :data="categoryListData"></category-list>
      <recommend-list :data="recommendListData"></recommend-list>
    </div>
    <v-footer></v-footer>
  </div>
</template>

<script>
import axios from 'axios';
import vHeader from '../components/header';
import vFooter from '../components/footer';
import categoryList from '../components/categoryList';
import filterBox from '../components/filterBox';
import sortBox from '../components/sortBox';
import recommendList from '../components/recommendList';

export default {
  name: 'category',
  components: {
    vHeader,
    vFooter,
    categoryList,
    filterBox,
    sortBox,
    recommendList
  },
  data () {
    return {
      categoryListData: [],
      filterListData: [],
      categoryListCopy: [],
      recommendListData: [],
      currentQuery: null,
      currentStock: null,
      currentKey: null
    };
  },
  mounted () {
    this.getCategoryListData();
    this.getFilterListData();
    this.getRecommendListData();
  },
  methods: {
    async getCategoryListData () {
      const { data } = await axios.get('/api/categoryList');
      this.categoryListData = data;
      this.categoryListCopy = [].concat(data);
    },
    async getFilterListData () {
      const { data } = await axios.get('/api/queryList');
      this.filterListData = data;
    },
    async getRecommendListData () {
      const { data } = await axios.get('/api/smartSale');
      this.recommendListData = data;
    },
    // 条件筛选，此时不排序
    getQuery (val) {
      this.currentQuery = val;
      this.sortGoods();
    },
    // 三个排序选项，此时需要排序
    getSortKey (key) {
      this.currentKey = key;
      this.sortGoods();
    },
    // 仅显示有货商品
    getSortStock (val) { // 监测传来的值
      this.currentStock = val;
      this.sortGoods();
    },
    // 解决条件筛选和排序不联动的问题
    // 把这三个排序选项写入一个函数中，可以一起调用
    sortGoods () { // 找到对应选项的值，以对象的形式表现，val来自子组件
      //  防止点击不同选项改变原来的值
      this.categoryListData = [].concat(this.categoryListCopy);// 数据来自aixos
      if (this.currentQuery) {
        Object.keys(this.currentQuery).forEach((key) => { // val[key] 为 18408
          if (this.currentQuery[key]) {
            //  项满足则显示筛选结果
            this.categoryListData = this.categoryListData.filter((item) => {
              // 通过feature选出满足项
              return item.features.indexOf(this.currentQuery[key]) >= 0;
            });
          }
        });
      }
      // 是否显示有货商品
      if (this.currentStock) {
        this.categoryListData = this.categoryListData.filter((item) => {
          return item.available;
        });
      }
      if (this.currentKey) {
        if (this.currentKey === 'recommend') {
          this.categoryListData.sort((a, b) => {
            return b.shelveTime - a.shelveTime;
          });
        } else if (this.currentKey === 'new') {
          this.categoryListData.sort((a, b) => {
            return b.publishedTime - a.publishedTime;
          });
        } else if (this.currentKey === 'low') {
          this.categoryListData.sort((a, b) => {
            return b.goodsPrice - a.goodsPrice;
          });
        } else if (this.currentKey === 'high') {
          this.categoryListData.sort((a, b) => {
            return a.goodsPrice - b.goodsPrice;
          });
        }
      }
    },
    goToDetail (item) {
      this.$router.push({
        name: 'Detail',
        params: {
          id: item.id
        }
      });
    }
  }
};
</script>

<style lang="less" scoped>
  .category-wrapper{
    width: 1240px;
    margin: 0 auto;
    padding-bottom: 60px;
  }

  .bread{
    height: 40px;
    line-height: 40px;

    .arrow, .last-bread{
      color: #999;
    }
  }
</style>
