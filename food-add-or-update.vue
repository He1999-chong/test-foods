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
      <el-form-item label="菜名" prop="name">
        <el-input v-model="dataForm.name" placeholder="菜名"></el-input>
      </el-form-item>
      <el-form-item label="价钱" prop="price">
        <el-input v-model="dataForm.price" placeholder="价钱"></el-input>
      </el-form-item>
      <el-form-item label="图片" prop="url">
        <SingleUpload v-model="dataForm.url"></SingleUpload>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import { isSave, queryById } from "@/api/food.js";
import SingleUpload from "@/components/singleUpload";
export default {
  data() {
    return {
      visible: false,
      dataForm: {
        id: 0,
        name: "",
        price: "",
        url: "",
      },
      dataRule: {
        name: [{ required: true, message: "菜名不能为空", trigger: "blur" }],
        price: [{ required: true, message: "价钱不能为空", trigger: "blur" }],
        url: [{ required: true, message: "图片不能为空", trigger: "blur" }],
      },
    };
  },
  components:{
    SingleUpload
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
              this.dataForm.name = data.food.name;
              this.dataForm.price = data.food.price;
              this.dataForm.url = data.food.url;
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
            name: this.dataForm.name,
            price: this.dataForm.price,
            url: this.dataForm.url,
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
