<template>
  <div>
    <div class="tableAndPagination">
      <el-table :data="DeptList.list" border style="width: 100%" :stripe="true">
        <el-table-column prop="code" :label="$t('message.code')" width="180"></el-table-column>
        <el-table-column prop="cnName" :label="$t('message.cn_name')" width="180"></el-table-column>
        <el-table-column prop="enName" :label="$t('message.en_name')" width="180"></el-table-column>
        <el-table-column prop="stateName" :label="$t('message.state_name')" width="180"></el-table-column>
        <el-table-column prop="remark" :label="$t('message.remark')"></el-table-column>
        <el-table-column fixed="right" :label="$t('message.oper')" width="150">
          <template slot-scope="scope">
            <el-button
              @click.native.prevent="editRow(scope.row)"
              type="text"
              size="small"
            >{{$t('message.edit')}}</el-button>
            <el-button
              type="text"
              size="small"
              @click.native.prevent="setArea(scope.row.id)"
            >{{$t('message.auth_area_power')}}</el-button>
            <el-button
              @click.native.prevent="deleteRow(scope.row.id)"
              type="text"
              size="small"
            >{{$t('message.delete')}}</el-button>
          </template>
        </el-table-column>
      </el-table>

      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="1"
        :page-sizes="pSizes"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
        :page-size="pageSize"
      ></el-pagination>
    </div>
    <UserGpDislogForm
      Operation="edit"
      :title="$t('message.edit')"
      :dialogFormVisible="dialogFormVisible"
      :formData="formData"
      @dialogVisible="dialogVisible"
      @update="initList"
    />
    <AreaDislog @close="showAreaDialog=false" :groupId="groupId" :showAreaDialog="showAreaDialog" />
    <!-- <GppowersDislog @close="showAreaDialog=false" :groupId="groupId" :showAreaDialog="showAreaDialog" /> -->
  </div>
</template>

<script>
import UserGpDislogForm from "../../components/UserGpDislogForm";
import AreaDislog from "../../components/AuthorityDislog";
// import GppowersDislog from "../../components/GppowersDislog";
import { GetUserGps, GetGppowers, DelUserGps } from "../../api/request";
import exportExecl from "../../tool/exportExecl";
export default {
  components: {
    UserGpDislogForm,
    AreaDislog,
  },
  props: {
    keyWord: {
      type: String,
    },
    update: { type: Number },
  },
  data() {
    return {
      DeptList: {},
      total: 0,
      pageSize: 10,
      pageIndex: 1,
      pSizes: [],
      dialogFormVisible: false,
      formData: {},
      groupId: "",
      showAreaDialog: false,
    };
  },
  created() {
    this.initList();
    // console.log(this);
    let arr = [];
    for (let i = 1; i <= 5; i++) {
      arr.push(this.pageSize * i);
    }
    // console.log(arr);
    this.pSizes = arr;
  },
  computed: {
    pages() {
      const { pageSize, pageIndex, keyWord } = this;
      return { pageSize, pageIndex, keyWord };
    },
  },
  watch: {
    pages() {
      this.initList();
    },
    update() {
      this.initList();
    },
  },
  methods: {
    setArea(id) {
      this.groupId = id;
      this.showAreaDialog = true;
    },
    deleteRow(id) {
      console.log(id);
      // removeDept(id);
      this.$confirm(this.$t("message.delete_warning"), "提示", {
        confirmButtonText: this.$t("message.ok"),
        cancelButtonText: this.$t("message.cancel"),
        type: "warning",
      })
        .then(() => {
          DelUserGps(id)
            .then((res) => {
              if (res.data.status === 2000) {
                this.$message({
                  type: "success",
                  message: res.data.message,
                });
                this.initList();
              } else {
                this.$message({
                  type: "error",
                  message: "",
                });
              }
            })
            .catch((err) => {
              this.$message({
                type: "error",
                message: err.response ? err.response.data.message : err,
              });
            });
        })
        .catch((err) => {});
    },
    editRow(row) {
      // console.log(row);
      this.formData = row;
      this.dialogFormVisible = true;
      // console.log(this.dialogFormVisible)
    },
    dialogVisible(bl) {
      this.dialogFormVisible = bl;
    },
    handleSizeChange(val) {
      this.pageSize = val;
      this.pageIndex = val > this.total ? 1 : this.pageIndex;
    },
    handleCurrentChange(val) {
      this.pageIndex = val;
    },

    getDeptList(pageIndex, pageSize, keyWord) {
      let data;
      let res = GetUserGps({ pageIndex, pageSize, keyWord }).then((res) => {
        return res.data.data;
      });

      // console.log(res);
      return res;
    },
    //初始化列表数据
    initList() {
      this.getDeptList(this.pageIndex, this.pageSize, this.keyWord).then(
        (data) => {
          if (data) {
            this.DeptList = data;
            if (data.list.length === 0) this.pageIndex -= 1;
            // console.log(this.DeptList);
            this.total = data.totalCount;
          }
        }
      );
    },
    
  },
};
</script>
