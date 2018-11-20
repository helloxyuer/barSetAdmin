<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="座位号" prop="number">
      <el-input v-model="dataForm.number" placeholder="座位号"></el-input>
    </el-form-item>
    <el-form-item label="可坐人数" prop="num">
      <el-input v-model="dataForm.num" placeholder="可坐人数"></el-input>
    </el-form-item>
    <el-form-item label="座位是否可用 1是0否" prop="status">
      <el-input v-model="dataForm.status" placeholder="座位是否可用 1是0否"></el-input>
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
          uuid: 0,
          number: '',
          num: '',
          status: ''
        },
        dataRule: {
          number: [
            { required: true, message: '座位号不能为空', trigger: 'blur' }
          ],
          num: [
            { required: true, message: '可坐人数不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '座位是否可用 1是0否不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.uuid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.uuid) {
            this.$http({
              url: this.$http.adornUrl(`/app/seating/info/${this.dataForm.uuid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.number = data.seating.number
                this.dataForm.num = data.seating.num
                this.dataForm.status = data.seating.status
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
              url: this.$http.adornUrl(`/app/seating/${!this.dataForm.uuid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'uuid': this.dataForm.uuid || undefined,
                'number': this.dataForm.number,
                'num': this.dataForm.num,
                'status': this.dataForm.status
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
