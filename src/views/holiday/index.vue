<template>
  <div>
    <HolidayTool @searchData="handleSearch" @parentUpdata="num++" />
    <router-view :num="num" :keyWord="keyword"></router-view>
  </div>
</template>
<script>
import HolidayTool from "./HolidayTool";
export default {
  components: {
    HolidayTool,
  },
  data() {
    return {
      num: 0,
      keyword: "",
    };
  },
  methods: {
    handleSearch(keyword) {
      this.keyword = keyword;
    },
  },
};
</script>