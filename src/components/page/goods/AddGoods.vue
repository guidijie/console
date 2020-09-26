<template>
  <div>
    <div class="crumbs">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-pie-chart"></i> 新增商品
        </el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <el-form
      :model="ruleForm"
      :rules="rules"
      ref="ruleForm"
      drag="ture"
      label-width="100px"
      class="demo-ruleForm bgc"
    >
      <el-form-item label="课程名称" prop="name">
        <el-input v-model="ruleForm.name" style="width: 350px"></el-input>
      </el-form-item>

      <el-form-item label="课程价格" prop="price">
        <el-input
          type="age"
          v-model.number="ruleForm.price"
          autocomplete="off"
          style="width: 350px"
        ></el-input>
      </el-form-item>
      <el-form-item label="课程优惠价格" prop="preferential">
        <el-input
          type="age"
          v-model.number="ruleForm.preferential"
          autocomplete="off"
          style="width: 350px"
        ></el-input>
      </el-form-item>

      <el-form-item label="商品图片" prop="goodsImg">
        <el-upload
          action
          list-type="picture-card"
          :limit="1"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :on-change="handleChange"
          :auto-upload="false"
        >
          <i class="el-icon-plus"></i>
        </el-upload>
        <el-dialog width="50%" :visible.sync="dialogVisible" :title="previewName">
          <iframe
            :src="dialogImageUrl"
            width="100%"
            height="100%"
            frameborder="1"
            style="height: 540px;"
          ></iframe>
        </el-dialog>
      </el-form-item>

      <el-form-item label="课程级别" prop="grade">
        <el-select v-model="ruleForm.grade" placeholder="请选择课程的等级">
          <el-option label="入门" value="入门"></el-option>
          <el-option label="进阶" value="进阶"></el-option>
          <el-option label="大师" value="大师"></el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="课程类别" prop="goodsTypeId">
        <el-select v-model="ruleForm.goodsTypeId" placeholder="请选择课程的类别">
          <el-option :label="item.typeName" :value="item.id" :key="item.id" v-for="item of goodsType"></el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="课程描述" prop="introduction">
        <el-input type="textarea" v-model="ruleForm.introduction" :rows="6" style="width: 550px"></el-input>
      </el-form-item>
      <el-form-item label="课程详细描述" prop="details">
        <el-input type="textarea" v-model="ruleForm.details" :rows="8" style="width: 550px"></el-input>
      </el-form-item>
      <el-form-item label="课程目录" prop="directory">
        <el-input type="textarea" v-model="ruleForm.directory" :rows="9" style="width: 550px"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submit('ruleForm')">立即创建</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      goodsType:[],

      formData: new FormData(),
      previewName: "",
      dialogImageUrl: "",
      uploadFile: {},
      dialogVisible: false,
      disabled: false,
      fileList: [],
      ruleForm: {
        name: "",
        price: "",
        preferential: "",
        grade: "",
        goodsTypeId:'',
        introduction: "",
        details: "",
        directory: "",
        
      },

      rules: {
        name: [
          { required: true, message: "请输入商品课程名称", trigger: "change" },
          {
            min: 1,
            max: 30,
            message: "长度在 3 到 30 个字符",
            trigger: "change",
          },
        ],
        price: [
          { required: true, message: "价格不能为空" },
          { type: "number", message: "价格必须为数字值" },
        ],
        preferential: [
          { required: true, message: "优惠价格不能为空，不打折写0" },
          { type: "number", message: "价格必须为数字值" },
        ],
        grade: [
          { required: true, message: "请选择商品的等级", trigger: "change" },
        ],
        goodsTypeId: [
          {
            required: true,
            message: "请选择商品课程的类别",
            trigger: "change",
          },
        ],
        introduction: [
          { required: true, message: "商品描述不能为空", trigger: "change" },
        ],
        details: [
          {
            required: true,
            message: "商品详细信息不能为空",
            trigger: "change",
          },
        ],
        directory: [
          {
            required: true,
            message: "请填写商品课程的目录",
            trigger: "change",
          },
        ],
      },
    };
  },
  methods: {

    

    submit(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          console.log(this.uploadFile);
          
          this.formData.append("name", this.ruleForm.name);
          this.formData.append("price", this.ruleForm.price);
          this.formData.append("preferential", this.ruleForm.preferential);
          this.formData.append("grade", this.ruleForm.grade);
          this.formData.append("goodsTypeId", this.ruleForm.goodsTypeId );
          this.formData.append("introduction", this.ruleForm.introduction);
          this.formData.append("details", this.ruleForm.details);
          this.formData.append("directory", this.ruleForm.directory);
        
          console.log();
          axios
            .post("http://localhost:8080/goods/addGoods", this.formData)
            .then(function (response) {
              alert(response.data.data.msg)
              this.$options.methods.resetForm("ruleForm")
              this.formData = new FormData();
            })
            .catch(function (error) {
              console.log("错误");
            });

            // this.ruleForm.name = ""
            // this.ruleForm.price = ""
            // this.ruleForm.preferential = ""
            // this.ruleForm.grade = ""
            // this.ruleForm.typeName = ""
            // this.ruleForm.introduction = ""
            // this.ruleForm.details = ""
            // this.ruleForm.directory = ""

        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    handleChange(file, fileList) {
      this.formData.append("file", file.raw);
      this.uploadFile = file
      this.dialogImageUrl = file.url
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePreview(file) {
      this.dialogVisible = true;
      this.dialogImageUrl = file.url;
    },

    getData(){
      let then = this
      axios
            .get("http://localhost:8080/goods/findGoodsTypeAll")
            .then(function (response) {
              then.goodsType = response.data.data.goodsType
              console.log(then.goodsType);
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