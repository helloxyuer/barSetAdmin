<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="锁定座位号" prop="number">
      <el-input v-model="dataForm.number" placeholder="锁定座位号"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createtime">
      <el-input v-model="dataForm.createtime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="到达时间，默认小时分钟" prop="arrivaltime">
      <el-input v-model="dataForm.arrivaltime" placeholder="到达时间，默认小时分钟"></el-input>
    </el-form-item>
    <el-form-item label="预订人" prop="name">
      <el-input v-model="dataForm.name" placeholder="预订人"></el-input>
    </el-form-item>
    <el-form-item label="" prop="phone">
      <el-input v-model="dataForm.phone" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="来客人数" prop="num">
      <el-input v-model="dataForm.num" placeholder="来客人数"></el-input>
    </el-form-item>
    <el-form-item label="微信唯一id" prop="openid">
      <el-input v-model="dataForm.openid" placeholder="微信唯一id"></el-input>
    </el-form-item>
    <el-form-item label="是否本店人员锁定" prop="isours">
      <el-input v-model="dataForm.isours" placeholder="是否本店人员锁定"></el-input>
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
          createtime: '',
          arrivaltime: '',
          name: '',
          phone: '',
          num: '',
          openid: '',
          isours: ''
        },
        dataRule: {
          number: [
            { required: true, message: '锁定座位号不能为空', trigger: 'blur' }
          ],
          createtime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          arrivaltime: [
            { required: true, message: '到达时间，默认小时分钟不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '预订人不能为空', trigger: 'blur' }
          ],
          phone: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          num: [
            { required: true, message: '来客人数不能为空', trigger: 'blur' }
          ],
          openid: [
            { required: true, message: '微信唯一id不能为空', trigger: 'blur' }
          ],
          isours: [
            { required: true, message: '是否本店人员锁定不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/app/seatingplan/info/${this.dataForm.uuid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.number = data.seatingplan.number
                this.dataForm.createtime = data.seatingplan.createtime
                this.dataForm.arrivaltime = data.seatingplan.arrivaltime
                this.dataForm.name = data.seatingplan.name
                this.dataForm.phone = data.seatingplan.phone
                this.dataForm.num = data.seatingplan.num
                this.dataForm.openid = data.seatingplan.openid
                this.dataForm.isours = data.seatingplan.isours
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
              url: this.$http.adornUrl(`/app/seatingplan/${!this.dataForm.uuid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'uuid': this.dataForm.uuid || undefined,
                'number': this.dataForm.number,
                'createtime': this.dataForm.createtime,
                'arrivaltime': this.dataForm.arrivaltime,
                'name': this.dataForm.name,
                'phone': this.dataForm.phone,
                'num': this.dataForm.num,
                'openid': this.dataForm.openid,
                'isours': this.dataForm.isours
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
