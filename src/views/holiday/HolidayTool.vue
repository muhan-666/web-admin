<template>
  <div class="deptTool" ref="deptTool" v-resize="handleResize" :class="{isdf:!is500}">
    <div class="left">
      <el-button
        type="primary"
        size="small"
        icon="el-icon-plus"
        @click="dialogFormVisible = true"
      >{{$t('message.add')}}</el-button>
      <el-button
        type="primary"
        size="small"
        icon="el-icon-download"
        @click="download('export')"
      >{{$t('message.export')}}</el-button>
      <el-button
        type="primary"
        size="small"
        icon="el-icon-printer"
        @click="download('print')"
      >打印</el-button>
      <holidayDataDislog
        Operation="add"
        :title="$t('message.add')"
        :dialogFormVisible="dialogFormVisible"
        @dialogVisible="dialogVisible"
        :formData="{state:true}"
        @update="$emit('parentUpdata')"
      />
    </div>
    <div class="form">
      <el-select v-model="value" placeholder="请选择" size="small">
        <el-option v-for="item in options" :key="item.id" :label="item.name" :value="item.id"></el-option>
      </el-select>
      <el-input size="small" v-model="form.keyword" :placeholder="$t('message.p_input_key_word')"></el-input>
      <el-button
        type="primary"
        size="small"
        icon="el-icon-search"
        @click="$emit('searchData',form.keyword)"
      >{{$t('message.search')}}</el-button>
    </div>
  </div>
</template>

<script>
import holidayDataDislog from "../../components/holidayDataDislog";
import { GetHolidays, combosGet } from "../../api/request";
import exportExecl from "../../tool/exportExecl";
export default {
  components: {
    holidayDataDislog,
  },
  data() {
    return {
      form: {
        keyword: "",
      },
      dialogFormVisible: false,
      is500: true,
      options: [],
      value: "",
    };
  },
  created() {
    combosGet("holidayYears").then((res) => {
      this.options = res.data.data;
    });
  },

  methods: {
    dialogVisible(bl) {
      this.dialogFormVisible = bl;
    },
    handleResize() {
      this.is500 = this.$refs.deptTool.clientWidth > 500;
    },
    download(type) {
      GetHolidays({ isPage: true }).then((res) => {
        exportExecl(res.data.data.list, "Holidays" + +new Date(),type,"假期信息報表");
      });
    },
  },
};
</script>

<style lang="less" scoped>
</style>