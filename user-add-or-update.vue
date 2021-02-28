<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="80px"
    >
      <el-form-item label="昵称" prop="nickname">
        <el-input v-model="dataForm.nickname" placeholder="昵称"></el-input>
      </el-form-item>
      <el-form-item label="账号" prop="name">
        <el-input v-model="dataForm.name" placeholder="账号"></el-input>
      </el-form-item>
      <el-form-item label="年龄" prop="age">
        <el-input type="number" v-model="dataForm.age" placeholder="年龄"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input v-model="dataForm.password" placeholder="密码"></el-input>
      </el-form-item>
      <el-form-item label="手机号" prop="phone">
        <el-input v-model="dataForm.phone" placeholder="手机号"></el-input>
      </el-form-item>
      <el-form-item label="地址" prop="address">
        <el-input v-model="dataForm.address" placeholder="地址"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import { isSave, queryById } from "@/api/user.js";
export default {
  data() {
    return {
      visible: false,
      dataForm: {
        id: 0,
        nickname: "",
        name: "",
        age: 0,
        password: "",
        address: "",
        phone:""
      },
      dataRule: {
        nickname: [
          { required: true, message: "昵称不能为空", trigger: "blur" },
        ],
        name: [{ required: true, message: "账号不能为空", trigger: "blur" }],
        age: [{ required: true, message: "年龄不能为空", trigger: "blur" }],
        password: [
          { required: true, message: "密码不能为空", trigger: "blur" },
        ],
        address: [{ required: true, message: "地址不能为空", trigger: "blur" }],
      },
    };
  },
  methods: {
    init(id) {
      this.dataForm.id = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.id) {
          queryById(this.dataForm.id).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.nickname = data.user.nickname;
              this.dataForm.name = data.user.name;
              this.dataForm.phone = data.user.phone;
              this.dataForm.age = parseInt(data.user.age);
              this.dataForm.password = data.user.password;
              this.dataForm.address = data.user.address;
            }
          });
        }
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          isSave({
            id: this.dataForm.id || undefined,
            nickname: this.dataForm.nickname,
            name: this.dataForm.name,
            phone: this.dataForm.phone,
            age: parseInt(this.dataForm.age),
            password: this.dataForm.password,
            address: this.dataForm.address,
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false;
                  this.$emit("refreshDataList");
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      });
    },
  },
};
</script>
