<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="uid">
      <el-input v-model="dataForm.uid" placeholder="用户id"></el-input>
    </el-form-item>
    <el-form-item label="菜品id" prop="fid">
      <el-input v-model="dataForm.fid" placeholder="菜品id"></el-input>
    </el-form-item>
    <el-form-item label="数量" prop="totalnum">
      <el-input v-model="dataForm.totalnum" placeholder="数量"></el-input>
    </el-form-item>
    <el-form-item label="总价" prop="totalprice">
      <el-input v-model="dataForm.totalprice" placeholder="总价"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          uid: '',
          fid: '',
          totalnum: '',
          totalprice: ''
        },
        dataRule: {
          uid: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          fid: [
            { required: true, message: '菜品id不能为空', trigger: 'blur' }
          ],
          totalnum: [
            { required: true, message: '数量不能为空', trigger: 'blur' }
          ],
          totalprice: [
            { required: true, message: '总价不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/foods/cart/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.uid = data.cart.uid
                this.dataForm.fid = data.cart.fid
                this.dataForm.totalnum = data.cart.totalnum
                this.dataForm.totalprice = data.cart.totalprice
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/foods/cart/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'uid': this.dataForm.uid,
                'fid': this.dataForm.fid,
                'totalnum': this.dataForm.totalnum,
                'totalprice': this.dataForm.totalprice
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
