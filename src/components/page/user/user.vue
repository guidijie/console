<template>
  <div>
    <div class="crumbs">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-pie-chart"></i> user
        </el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <el-table :data="tableData">
      <el-table-column label="用户id" prop="userId"></el-table-column>
      <el-table-column label="角色" prop="name"></el-table-column>
      <el-table-column label="账号" prop="userName"></el-table-column>
      <el-table-column label="邮箱" prop="email"></el-table-column>
      <el-table-column label="电话" prop="phone"></el-table-column>

      <el-table-column align="right" width="400px">
        <template slot="header">
          <el-input v-model="search" size="mini" placeholder="输入关键字搜索" />
        </template>
        <template slot-scope="scope">
          <el-button size="mini" @click="onUpdateImage(scope.$index, scope.row)">更换头像</el-button>
          <el-button size="mini" @click="handleEditChangeInfo(scope.$index, scope.row)">编辑资料</el-button>
          <el-button size="mini" @click="handleEditChangePW(scope.$index, scope.row)">重置密码</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <div class="page">
      <el-pagination
        background
        layout="prev, pager, next,jumper"
        
        @current-change="currentChange"
        :total="pageTotal"
        :page-size="20"
      ></el-pagination>
    </div>

    <!-- 修改用户头像 -->
    <el-dialog title="修改用户头像" :visible.sync="updateImageShow" width="700px" v-if="updateImageShow">
      <el-form ref="image" :model="updateImage" label-width="80px" label-position="top">
        <el-form-item label="上传图片">
          <el-upload
            action
            list-type="picture-card"
            :limit="1"
            :on-preview="handleImagePreview"
            :on-remove="handleImageRemove"
            :on-change="handleImageChange"
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

        <el-form-item>
          <el-button type="primary" @click="submitImage('image')">更换头像</el-button>
          <el-button @click="resetImage('image')">重置</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>

    <!-- 修改用户信息 -->
    <el-dialog
      title="修改用户信息"
      :visible.sync="dialogFormChangeInfo"
      width="700px"
      v-if="dialogFormChangeInfo"
    >
      <el-form
        ref="ruleFormChangeInfo"
        :model="ruleFormChangeInfo"
        label-width="80px"
        label-position="right"
      >
        <el-form-item label="昵称">
          <el-input
            v-model="ruleFormChangeInfo.name"
            style="width:250px;"
            :placeholder="userInfo.name"
          ></el-input>
        </el-form-item>

        <el-form-item label="角色">
          <el-select v-model="ruleFormChangeInfo.region" style="width:250px;" placeholder="请选择活动区域">
            <el-option label="超级管理员" value="shanghai"></el-option>
            <el-option label="普通用户" value="beijing"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="生日">
          <el-col :span="11">
            <el-date-picker
              type="date"
              :placeholder="userInfo.birthday"
              v-model="ruleFormChangeInfo.birthday"
              style="width: 100%;"
            ></el-date-picker>
          </el-col>
        </el-form-item>
        <el-form-item label="邮箱">
          <el-input
            v-model="ruleFormChangeInfo.email"
            style="width:250px;"
            :placeholder="userInfo.email"
          ></el-input>
        </el-form-item>
        <el-form-item label="手机">
          <el-input
            v-model="ruleFormChangeInfo.phone"
            style="width:250px;"
            :placeholder="userInfo.phone"
          ></el-input>
        </el-form-item>
        <el-form-item label="性别">
          <el-radio-group v-model="ruleFormChangeInfo.sex">
            <el-radio label="男"></el-radio>
            <el-radio label="女"></el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="个性签名">
          <el-input
            type="textarea"
            :rows="6"
            v-model="ruleFormChangeInfo.individuality_signature"
            :placeholder="userInfo.individualitySignature"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitFormChangeInfo('ruleFormChangeInfo')">立即创建</el-button>
          <el-button>取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>

    <!-- 修改密码弹窗 -->
    <el-dialog
      title="修密码"
      :visible.sync="dialogFormChangePW"
      width="700px"
      v-if="dialogFormChangePW"
      @close="closeFun()"
    >
      <el-form
        status-icon
        :rules="rulesChangePW"
        label-width="100px"
        class="demo-ruleForm"
        :model="ruleFormChangePW"
        ref="ruleFormChangePW"
      >
        <el-form-item label="账号">
          <el-input
            :placeholder="ruleFormChangePW.user"
            style="width:250px"
            v-model="ruleFormChangePW.user"
            :disabled="true"
          ></el-input>
        </el-form-item>

        <el-form-item label="密码" prop="pass">
          <el-input
            type="password"
            style="width:300px"
            v-model="ruleFormChangePW.pass"
            autocomplete="off"
          ></el-input>
        </el-form-item>
        <el-form-item label="确认密码" prop="checkPass">
          <el-input
            type="password"
            style="width:300px"
            v-model="ruleFormChangePW.checkPass"
            autocomplete="off"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitFormChangePW('ruleFormChangePW')">提交</el-button>
          <el-button @click="resetFormChangePW('ruleFormChangePW')">重置</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    let checkAge = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("年龄不能为空"));
      }
      setTimeout(() => {
        if (!Number.isInteger(value)) {
          callback(new Error("请输入数字值"));
        } else {
          if (value < 18) {
            callback(new Error("必须年满18岁"));
          } else {
            callback();
          }
        }
      }, 1000);
    };
    let validatePass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入密码"));
      } else {
        if (this.ruleFormChangePW.checkPass !== "") {
          this.$refs.ruleFormChangePW.validateField("checkPass");
        }
        callback();
      }
    };
    let validatePass2 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.ruleFormChangePW.pass) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };

    return {
      dialogVisible: false,   //预览图片
      tableData: [],  //用户数据
      search: "",
      dialogFormChangePW: false,  //显示修改密码页
      dialogFormChangeInfo: false,  //显示修改用户数据页
      ruleFormChangePW: {  //修改后密码表单
        user: "",
        pass: "",
        checkPass: "",
      },
      userInfo: {},
      ruleFormChangeInfo: {   //修改用户信息后表单
        name: "",
        birthday: "",
        email: "",
        phone: "",
        sex: "",
        individuality_signature: "",
        juese: "",
      },
      rulesChangePW: {   //密码验证
        pass: [{ validator: validatePass, trigger: "blur" }],
        checkPass: [{ validator: validatePass2, trigger: "blur" }],
      },

      //上传图片相关
      formData: new FormData(),
      updateImageShow: false,  //显示上传图片页
      previewName: "",
      dialogImageUrl: "",
      updateImage: {},


      pageTotal:0  //分页总数
    };
  },
  methods: {
    // 重置密码方法
    submitFormChangePW(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert("submit!");
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    handleEditChangePW(index, row) {
      this.ruleFormChangePW.user = row.name;
      this.dialogFormChangePW = true;
    },
    closeFun() {
      this.ruleFormChangePW.pass = "";
      this.ruleFormChangePW.checkPass = "";
    },

    
    //修改用户资料
    handleEditChangeInfo(index, row) {
      this.dialogFormChangeInfo = true;
      this.userInfo = row;
      if (row.sex === 0) {
        this.ruleFormChangeInfo.sex = "女";
      } else if (row.sex === 1) {
        this.ruleFormChangeInfo.sex = "男";
      }
    },
    submitFormChangeInfo(formName) {
      alert("submit!");
      console.log(this.$refs[formName].model);
    },
    

    //删除用户
    handleDelete(index, row) {
      console.log(index, row);
    },
    

  
    //图片上传相关
    onUpdateImage(index, row) {
      this.updateImageShow = true;
    },
    handleImageChange(file, fileList) {
      this.formData.append("file", file.raw);
      this.uploadFile = file;
      this.dialogImageUrl = file.url;
    },
    handleImageRemove(file, fileList) {
      console.log(file, fileList);
    },
    handleImagePreview(file) {
      this.dialogVisible = true;
      this.dialogImageUrl = file.url;
    },
    submitImage() {
      let then = this;
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // let formData =new FormData()
          // axios
          //   .post("http://localhost:8080/xiazai", this.formData)
          //   .then(function (response) {
          //     // goods.goods = response;
          //     // console.log("成功");
          //     resetForm("ruleForm")
          //   })
          //   .catch(function (error) {
          //     console.log("错误");
          //   });
          then.formData.delete("file");
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },

    // 分页
    currentChange(val){
      let then = this;
      axios
        .get("http://localhost:8080/console/findByUser/"+val)
        .then(function (response) {
          then.tableData = response.data.data.users.users;
          then.pageTotal = response.data.data.users.page.total
          for (let item of then.tableData) {
            item.money = "￥" + item.money + ".00";
          }
          console.log(then.tableData);
        })
        .catch(function (error) {
          console.log("错误");
        });
    },

    //获取数据
    getData(pageNum) {
      let then = this;
      axios
        .get("http://localhost:8080/console/findByUser/"+pageNum)
        .then(function (response) {
          then.tableData = response.data.data.users.users;
          then.pageTotal = response.data.data.users.page.total
          for (let item of then.tableData) {
            item.money = "￥" + item.money + ".00";
          }
          console.log(then.tableData);
        })
        .catch(function (error) {
          console.log("错误");
        });
    },
  },
  created() {
    this.getData(1);
  },
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