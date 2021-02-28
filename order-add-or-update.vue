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
      <el-form-item label="用户名" prop="uid">
        <!-- <el-input v-model="dataForm.uid" placeholder="用户名"></el-input> -->
        <el-select v-model="dataForm.uid" placeholder="请选择用户">
          <el-option
            v-for="item in users"
            :key="item.id"
            :label="item.name"
            :value="item.id"
          >
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="菜品" prop="fid">
        <!-- <el-input v-model="dataForm.fid" placeholder="菜品"></el-input> -->
        <el-select v-model="dataForm.fid" placeholder="请选择菜品">
          <el-option
            v-for="item in foods"
            :key="item.id"
            :label="item.name"
            :value="item.id"
          >
          </el-option>
        </el-select>
      </el-form-item>
      <!-- <el-form-item label="订单日期" prop="date">
        <el-input v-model="dataForm.date" placeholder="订单日期"></el-input>
      </el-form-item> -->
      <el-form-item label="数量" prop="num">
        <el-input
          type="number"
          v-model="dataForm.num"
          placeholder="数量"
        ></el-input>
      </el-form-item>
      <!-- <el-form-item label="金额" prop="price">
        <el-input v-model="dataForm.price" placeholder="金额"></el-input>
      </el-form-item> -->
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
import { queryById, isSave } from "@/api/order";
import { queryPage as getUsers } from "@/api/user";
import { queryPage as getFoods } from "@/api/food";
export default {
  data() {
    return {
      visible: false,
      pageSize: 10000,
      pageIndex: 1,
      dataForm: {
        id: 0,
        uid: "",
        fid: "",
        num: 1,
        date: "",
        price: "",
        address: "",
      },
      users: [],
      foods: [],
      dataRule: {
        uid: [{ required: true, message: "用户不能为空", trigger: "blur" }],
        fid: [{ required: true, message: "菜品不能为空", trigger: "blur" }],
        date: [
          { required: true, message: "订单日期不能为空", trigger: "blur" },
        ],
        num: [{required: true, message: "数量不能为空", trigger: "blur" }],
        address: [
          { required: false, message: "地址不能为空", trigger: "blur" },
        ],
      },
    };
  },
  created() {
    this.getAll();
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
              this.dataForm = data.order;
            }
          });
        }
      });
    },
    getAll() {
      getUsers({
        page: this.pageIndex,
        limit: this.pageSize,
        key: "",
      }).then((res) => {
        console.log(res);
        this.users = res.data.page.list;
      });
      getFoods({
        page: this.pageIndex,
        limit: this.pageSize,
        key: "",
      }).then((res) => {
        this.foods = res.data.page.list;
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          isSave({
            id: this.dataForm.id || undefined,
            fid: this.dataForm.fid,
            uid: this.dataForm.uid,
            num: this.dataForm.num,
            price: this.dataForm.price,
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
