<template>
  <div>
    <div style="margin-bottom: 20px;">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-pie-chart"></i> 商品类别管理
        </el-breadcrumb-item>
      </el-breadcrumb>
    </div>

    <div style="overflow: hidden; margin-bottom: 20px;">
      <el-input
        v-model="inputSearch"
        placeholder="请输入商品类别"
        style="float:left;width:320px;margin-right: 5px;"
        size="medium"
      ></el-input>
      <el-button size="medium" type="primary" icon="el-icon-search" style="float:left">搜索</el-button>
    </div>

    <div>
      <el-table :data="goodsType" style="width: 100%">
        <el-table-column label="ID" prop="id"></el-table-column>
        <el-table-column label="商品类型" prop="typeName"></el-table-column>
        <el-table-column align="right">
          <template slot="header">
            <el-button type="primary" @click="showAddGoodsType = true">添加类别</el-button>
          </template>
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
            <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <!-- 新增类型 -->
    <el-dialog title="新增商品类型" :visible.sync="showAddGoodsType" width="600px">
      <el-form :model="goodsTypeForm">
        <el-form-item label="商品类型" :label-width="formLabelWidth">
          <el-input v-model="goodsTypeForm.typeName" autocomplete="off" placeholder="请输入新的商品类型"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel()">取 消</el-button>
        <el-button type="primary" @click="addGoodsTypeSbmit()">确 定</el-button>
      </div>
    </el-dialog>

    <!-- 编辑 -->
    <el-dialog title="编辑类型" :visible.sync="showEditGoodsType" width="600px">
      <el-form :model="goodsTypeForm">
        <el-form-item  label="商品类型ID" :label-width="formLabelWidth">
          <el-input   autocomplete="off" :placeholder="itemGoodsType.id" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="商品类型" :label-width="formLabelWidth">
          <el-input v-model="goodsTypeForm.typeName"  placeholder="请输入新的商品类型" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel()">取 消</el-button>
        <el-button type="primary" @click="updateGoodsTypeSbmit()">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      inputSearch: "",
      goodsType: [],
      showAddGoodsType: false,
      showEditGoodsType: false,
      goodsTypeForm: {
        id: "",
        typeName: "",
      },
      itemGoodsType:[],
      formLabelWidth: "120px",
    };
  },
  methods: {
    getData() {
      let then = this;
      axios
        .get("http://localhost:8080/goods/findGoodsTypeAll")
        .then(function (response) {
          then.goodsType = response.data.data.goodsType;
          console.log(then.goodsType);
        })
        .catch(function (error) {
          console.log("错误");
        });
    },
    handleEdit(index, row) {
      console.log(index, row);
      this.showEditGoodsType = true;
      this.itemGoodsType = row;
    },
    handleDelete(index, row) {
      console.log(index, row);
    },
    cancel() {},
    addGoodsTypeSbmit() {
      console.log(this.goodsTypeForm);
      
      let then = this;
      axios
        .post("http://localhost:8080/goods/addGoodsType",then.goodsTypeForm)
        .then(function (response) {
          alert(response.data.data.msg)
          then.goodsTypeForm.typeName = ""
        })
        .catch(function (error) {
          console.log("错误");
        });
    },
    updateGoodsTypeSbmit(){
      console.log(this.goodsTypeForm);
      this.goodsTypeForm.id = this.itemGoodsType.id
      let then = this;
      axios
        .post("http://localhost:8080/goods/updateGoodsType",then.goodsTypeForm)
        .then(function (response) {
          alert(response.data.data.msg)
          ten.goodsTypeForm.id = ""
          then.goodsTypeForm.typeName = ""
        })
        .catch(function (error) {
          console.log("错误");
        });
    }
  },
  created() {
    this.getData();
  },
};
</script>
<style scoped>
.bgc {
  padding: 20px;
  background-color: rgb(231, 231, 231);
}
</style>