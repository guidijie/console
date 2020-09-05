<template>
  <div>
    <div class="crumbs">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-pie-chart"></i> user
        </el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column label="用户id" prop="userId"></el-table-column>
      <el-table-column label="角色" prop="name"></el-table-column>
      <el-table-column label="账号" prop="userName"></el-table-column>
      <el-table-column label="用户名" prop="name"></el-table-column>
      <el-table-column label="余额" prop="money"></el-table-column>
      <el-table-column label="邮箱" prop="email"></el-table-column>
      <el-table-column label="电话" prop="phone"></el-table-column>

      <el-table-column align="right">
        <template slot="header">
          <el-input v-model="search" size="mini" placeholder="输入关键字搜索" />
        </template>
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-dialog title="修改用户信息" :visible.sync="dialogVisible" width="30%" :before-close="handleClose">
      <el-form ref="form" label-width="80px" size="mini">
        <el-form-item label="角色：">
          <el-select v-model="tableData.name" placeholder="角色">
            <el-option label="普通角色" value="shanghai"></el-option>
            <el-option label="超级管理员" value="beijing"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="账号：">
          <el-input v-model="tableData.name"></el-input>
        </el-form-item>
        <el-form-item label="密码：">
          <el-input v-model="tableData.name"></el-input>
        </el-form-item>
        <el-form-item label="余额：">
          <el-input v-model="tableData.name"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>

    <div class="page">
      <el-pagination background layout="prev, pager, next,jumper" :total="10" :page-size="2"></el-pagination>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      dialogVisible: false,
      title: ["时间", "姓名", "地址"],
      sizeForm:"",
      tableData: [],
      search: ""
    };
  },
  methods: {
    handleEdit(index, row) {
      // console.log(index, row);
      this.dialogVisible = true;
    },
    handleDelete(index, row) {
      console.log(index, row);
    },
    handleClose(done) {
      this.$confirm("确认关闭？")
        .then(_ => {
          done();
        })
        .catch(_ => {});
    },
    getData(){
      let then = this
      axios
            .get("http://localhost:8080/console/findByUser")
            .then(function (response) {
             then.tableData = response.data.data.users
             console.log(then.tableData);
            })
            .catch(function (error) {
              console.log("错误");
            });
    }
  },
  created(){
    this.getData()
  }
  
};
</script>
<style scoped>
.page {
  padding-left: 33%;
  padding-top: 8px;
  margin-top: 30px;
  height: 40px;
  line-height: 40px;
  background-color: #fff;
}
</style>