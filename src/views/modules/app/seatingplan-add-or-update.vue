<template>
  <el-dialog
    :title="新增"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="150px">
      <el-form-item label="座位号" prop="number">
        <el-input v-model="dataForm.number" placeholder="座位号"></el-input>
      </el-form-item>
      <el-form-item label="到店日期" prop="arrivaldate">
        <el-date-picker v-model="dataForm.arrivaldate" value-format="yyyy-MM-dd" type="date"
                        placeholder="选择日期"></el-date-picker>
      </el-form-item>
      <el-form-item label="到店时间" prop="arrivaltime">
        <el-time-select v-model="dataForm.arrivaltime" :picker-options="timeoptions"
                        placeholder="选择时间"></el-time-select>
      </el-form-item>
      <el-form-item label="预订人姓名" prop="name">
        <el-input v-model="dataForm.name" placeholder="预订人姓名"></el-input>
      </el-form-item>
      <el-form-item label="预定电话" prop="phone">
        <el-input v-model="dataForm.phone" placeholder="预定电话"></el-input>
      </el-form-item>
      <el-form-item label="来客人数" prop="num">
        <el-input v-model="dataForm.num" placeholder="来客人数"></el-input>
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
    data() {
      return {
        visible: false,
        dataForm: {
          uuid: 0,
          number: '',
          arrivaldate: '',
          arrivaltime: '',
          name: '',
          phone: '',
          num: ''
        },
        timeoptions: {
          start: '00:00',
          step: '00:30',
          end: '24:00'
        },
        dataRule: {
          number: [
            {required: true, message: '座位号不能为空', trigger: 'blur'}
          ],
          arrivaldate: [
            {required: true, message: '到店日期不能为空', trigger: 'blur'}
          ],
          arrivaltime: [
            {required: true, message: '到店时间不能为空', trigger: 'blur'}
          ],
          name: [
            {required: true, message: '预订人姓名不能为空', trigger: 'blur'}
          ],
          phone: [
            {required: true, message: '预定电话不能为空', trigger: 'blur'}
          ],
          num: [
            {required: true, message: '来客人数不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    methods: {
      init(id) {
        this.dataForm.uuid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          this.getOpenTime()
        })
      },
      getOpenTime() {
        this.$http({
          url: this.$http.adornUrl('/app/seat/abouttime'),
          method: 'post',
          data: this.$http.adornData({}, false)
        }).then(({data}) => {
          if (data.code == 0) {
            var openTimeArr = data.data.split(',')
            if (openTimeArr[0] !== 'null' && openTimeArr[1] !== 'null') {
              this.timeoptions.start = openTimeArr[0]
              this.timeoptions.end = openTimeArr[1]
            }
          }
        })
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/app/seating/seatPlan`, false),
              method: 'post',
              data: this.$http.adornData({
                'number': this.dataForm.number,
                'arrivaldate': this.dataForm.arrivaldate,
                'arrivaltime': this.dataForm.arrivaltime,
                'name': this.dataForm.name,
                'phone': this.dataForm.phone,
                'num': this.dataForm.num
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
