<template>
  <ul class="filter-box">
    <li class="box-item clearfix" v-for="(item, index) in data" :key="index">
      <div class="fl filter-title">
        {{item.name}}:
      </div>
      <ul class="fl">
        <!--根据下标来选择-->
        <li class="filter-item fl" :class="{'active': activeFilter[item.key] === info.value}" v-for="(info, ii) in item.queryList" :key="ii" @click="changeFilter(item.key, info.value)">
          {{info.name}}
        </li>
      </ul>
    </li>
  </ul>
</template>
<!--以一个对象来确定筛选的物品 {品牌：对应val，尺寸：val, 内存：val}}-->
<script>
export default {
  name: 'filter-box',
  props: {
    data: {
      type: Array,
      default () {
        return [];
      }
    }
  },
  data () {
    return {
      activeFilter: {}
    };
  },
  methods: {
    changeFilter (key, val) {
      // 进行动态改变;$set用来设置属性
      this.$set(this.activeFilter, key, val);
      //  自定义事件filter；子传父
      this.$emit('filter', this.activeFilter);
    }
  }
};
</script>

<style lang="less" scoped>
  .filter-box{
    background: white;
    padding: 15px 10px;
    border: 1px solid #efefef;

    .box-item{
      line-height: 46px;
    }

    .filter-title{
      width: 85px;
      padding-left: 10px;
      white-space: nowrap;
      color: #333;
      overflow: hidden;
    }

    .filter-item{
      cursor: pointer;
      margin-right: 62px;

      &.active{
        color: #00c3f5;
      }
    }
  }
</style>
