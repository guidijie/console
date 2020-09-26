<template>
  <div>
    <div style="margin-bottom: 20px">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-pie-chart"></i> 商品类别管理
        </el-breadcrumb-item>
      </el-breadcrumb>
    </div>

    <div style="overflow: hidden; margin-bottom: 20px; margin-top= 20px">
      <div style="float: left">
        <!-- <el-button size="medium" type="primary" style="float: left"
          >删除</el-button
        > -->
        <el-popconfirm title="你确定要删除这些商品吗？" @onConfirm="deleteGoodsList">
          <el-button size="medium" type="danger" slot="reference" style="margin-left: 10px;">删除</el-button>
        </el-popconfirm>
      </div>
      <div style="float: right">
        <el-input
          v-model="inputSearch"
          placeholder="请输入商品类别"
          style="float: left; width: 320px; margin-right: 5px"
          size="medium"
        ></el-input>
        <el-button
          size="medium"
          type="primary"
          icon="el-icon-search"
          style="float: left"
          >搜索</el-button
        >
      </div>
    </div>

    <div>
      <el-table
        :data="goods"
        style="width: 100%"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection" width="60"></el-table-column>
        <el-table-column label="ID" prop="id"></el-table-column>
        <el-table-column label="商品名称" prop="name"></el-table-column>
        <el-table-column label="商品图片">
          <template slot-scope="scope">
            <img
              :src="'http://localhost:8080' + scope.row.imagePath"
              width="80"
              height="80"
            />
          </template>
        </el-table-column>
        <el-table-column label="价格" prop="price"></el-table-column>
        <el-table-column label="优惠" prop="preferential"></el-table-column>
        <el-table-column
          prop="grade"
          label="等级"
          width="100"
          :filters="goodsGrade"
          :filter-method="filterGrade"
          filter-placement="bottom-end"
        >
          <template slot-scope="scope">
            <el-tag disable-transitions>{{ scope.row.grade }}</el-tag>
          </template>
        </el-table-column>

        <el-table-column
          prop="typeName"
          label="类型"
          width="100"
          :filters="goodsTypeFilter"
          :filter-method="filterType"
          filter-placement="bottom-end"
        >
          <template slot-scope="scope">
            <el-tag disable-transitions>{{ scope.row.typeName }}</el-tag>
          </template>
        </el-table-column>

        <el-table-column align="right">
          <template slot="header">
            <span>操作</span>
          </template>
          <template slot-scope="scope">
            <el-button size="mini" @click="goodsEdit(scope.index, scope.row)"
              >编辑</el-button
            >
            <el-popconfirm title="这是一段内容确定删除吗？" @onConfirm="handleDelete(scope.$index, scope.row)">
              <el-button size="mini" type="danger" slot="reference" style="margin-left: 10px;">删除</el-button>
            </el-popconfirm>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <!-- 分页 -->
    <div class="page">
      <el-pagination
        background
        layout="prev, pager, next,jumper"
        @current-change="currentChange"
        :total="pageTotal"
        :page-size="20"
        style="text-align: center"
      ></el-pagination>
    </div>

    <!-- 修改商品信息 -->
    <div>
      <el-dialog title="修改商品信息" :visible.sync="updataGoodsVisible">
        <el-form :model="updataGoodsForm">
          <el-form-item label="商品ID" :label-width="formLabelWidth" >
            <el-input
              autocomplete="off"
              :placeholder="itemGoods.id"
              style="width: 350px"
              :disabled="true"
            ></el-input>
          </el-form-item>
          <el-form-item label="商品名称" :label-width="formLabelWidth">
            <el-input
              v-model="updataGoodsForm.name"
              :placeholder="itemGoods.name"
              autocomplete="off"
              style="width: 350px"
            ></el-input>
          </el-form-item>
          <el-form-item label="商品价格" :label-width="formLabelWidth">
            <el-input
              v-model="updataGoodsForm.price"
              :placeholder="itemGoods.price"
              autocomplete="off"
              style="width: 350px"
            ></el-input>
          </el-form-item>
          <el-form-item label="商品优惠价格" :label-width="formLabelWidth">
            <el-input
              v-model="updataGoodsForm.preferential"
              :placeholder="itemGoods.preferential"
              autocomplete="off"
              style="width: 350px"
            ></el-input>
          </el-form-item>
          <el-form-item label="商品等级" :label-width="formLabelWidth">
            <el-select v-model="updataGoodsForm.grade" :placeholder="itemGoods.grade" >
              <el-option label="入门" value="入门"></el-option>
              <el-option label="进阶" value="进阶"></el-option>
              <el-option label="大师" value="大师"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="商品类别" :label-width="formLabelWidth">
            <el-select v-model="updataGoodsForm.typeId" :placeholder="itemGoods.typeName">
              <el-option :key="item.typeName" v-for="item in goodsType" :label="item.typeName" :value="item.id"></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="课程描述" :label-width="formLabelWidth">
            <el-input type="textarea" v-model="updataGoodsForm.introduction" :rows="6" :placeholder="itemGoods.introduction" style="width: 550px"></el-input>
          </el-form-item>
          <el-form-item label="课程详细描述" :label-width="formLabelWidth">
            <el-input type="textarea" v-model="updataGoodsForm.details" :rows="9" :placeholder="itemGoods.details" style="width: 600px"></el-input>
          </el-form-item>
          <el-form-item label="课程目录" :label-width="formLabelWidth">
            <el-input type="textarea" v-model="updataGoodsForm.directory" :rows="9" :placeholder="itemGoods.directory" style="width: 600px"></el-input>
          </el-form-item>

        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="updataGoodsVisible = false">取 消</el-button>
          <el-button type="primary" @click="updataGoods">确 定</el-button
          >
        </div>
      </el-dialog>
    </div>

  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      inputSearch: "",
      goods: [],
      goodsType: [],
      goodsTypeFilter: [],
      goodsGrade: [
        { text: "入门", value: "入门" },
        { text: "进阶", value: "进阶" },
        { text: "大师", value: "大师" },
      ],
      itemGoods:{},
      updataGoodsForm: {
        id: "",
        name: "",
        price: "",
        preferential: "",
        grade: "",
        typeId: "",
        introduction: "",
        details: "",
        directory: "",
      },
      updataGoodsVisible: false,
      checked: false,
      multipleSelection: [], //单选框选中的商品
      pageTotal: 0, //分页总数
      formLabelWidth: "120px",
    };
  },
  methods: {
    getData() {
      let then = this;
      let num = 1;
      axios
        .get("http://localhost:8080/goods/findGoodsAll/" + num)
        .then(function (response) {
          then.goods = response.data.data.goods.goods;
          console.log(response.data.data.goods);
          then.pageTotal = response.data.data.goods.page.total;
          console.log(then.goods);
        })
        .catch(function (error) {
          console.log("错误");
        });

      axios
        .get("http://localhost:8080/goods/findGoodsTypeAll")
        .then(function (response) {
          let type = response.data.data.goodsType;
          then.goodsType = response.data.data.goodsType;
          console.log(then.goodsType);
          then.goodsTypeFilter = type.map((item) => ({
            text: item.typeName,
            value: item.typeName,
          }));
        })
        .catch(function (error) {
          console.log("错误");
        });
    },

    //更新goods
    updataGoods(){
      this.updataGoodsForm.id = this.itemGoods.id
      let then = this;
      axios
        .post("http://localhost:8080/goods/updateGoods/",then.updataGoodsForm)
        .then(function (response) {
          alert(response.data.data.msg);

          then.updataGoodsForm.id= ""
          then.updataGoodsForm.name= ""
          then.updataGoodsForm.price= ""
          then.updataGoodsForm.preferential= ""
          then.updataGoodsForm.grade= ""
          then.updataGoodsForm.typeId= ""
          then.updataGoodsForm.introduction=""
          then.updataGoodsForm.details=""
          then.updataGoodsForm.directory= ""

        })
        .catch(function (error) {
          console.log("错误");
        });
    
    },

  
      
    // 分页
    currentChange(val) {
      let then = this;
      axios
        .get("http://localhost:8080/goods/findGoodsAll/" + val)
        .then(function (response) {
          then.goods = response.data.data.goods.goods;
          then.pageTotal = response.data.data.goods.page.total;

          console.log(then.goods);
        })
        .catch(function (error) {
          console.log("错误");
        });
    },

    handleSelectionChange(val) {
      this.multipleSelection = val;
      console.log(this.multipleSelection);
    },

    goodsEdit(index, row) {
      this.updataGoodsVisible = true;
      this.itemGoods = row
      console.log(row);
    },

    //删除
    handleDelete(index, row) {
      let then = this;
      axios
        .get("http://localhost:8080/goods/deleteGoods/" + row.id)
        .then(function (response) {
          alert(response.data.data.msg)
        })
        .catch(function (error) {
          console.log("错误");
        });
    },

    //多选删除
    deleteGoodsList(){
      let then = this;
      axios
        .post("http://localhost:8080/goods/deleteGoodsList/" ,then.multipleSelection)
        .then(function (response) {
          alert(response.data.data.msg)
        })
        .catch(function (error) {
          console.log("错误");
        });
    },

    filterType(value, row) {
      return row.typeName === value;
      console.log(column);
    },
    filterGrade(value, row) {
      return row.grade === value;
    },
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

.page {
  /* padding-left: 33%; */
  padding-top: 8px;
  margin-top: 30px;
  height: 40px;
  line-height: 40px;
  background-color: #fff;
}
</style>



