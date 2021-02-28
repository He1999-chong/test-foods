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
      <el-form-item label="图片" prop="url">
        <!-- <el-input v-model="dataForm.url" placeholder="图片"></el-input> -->
        <SingleUpload v-model="dataForm.url"></SingleUpload>
      </el-form-item>
      <el-form-item label="是否展示" prop="isshow">
        <el-switch
          style="display: block"
          v-model="dataForm.isshow"
          active-color="#13ce66"
          inactive-color="#ff4949"
          active-text="是"
          inactive-text="否"
        >
        </el-switch>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import SingleUpload from "@/components/singleUpload";
import { isSave, queryById } from "@/api/slideshow";
export default {
  data() {
    return {
      visible: false,
      dataForm: {
        id: 0,
        url: "",
        isshow: true
      },
      dataRule: {
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
              this.dataForm.url = data.slideshow.url;
              this.dataForm.isshow = data.slideshow.isshow == 1 ? true : false;
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
            url: this.dataForm.url,
            isshow: this.dataForm.isshow ? 1 : 0,
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
