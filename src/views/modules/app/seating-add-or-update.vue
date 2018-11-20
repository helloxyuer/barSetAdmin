<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="80px">
      <el-form-item label="营业时间" prop="begaintime">
        <el-time-select placeholder="起始时间"
                        v-model="dataForm.begaintime"
                        :picker-options="timeoptions"></el-time-select>
      </el-form-item>
      <el-form-item label="打烊时间" prop="endtime">
        <el-time-select
          placeholder="结束时间"
          v-model="dataForm.endtime"
          :picker-options="{
      start: '00:00',
      step: '00:30',
      end: '24:00',
      minTime: dataForm.begaintime
    }"></el-time-select>
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
          begaintime: '',
          endtime: ''
        },
        timeoptions: {
          start: '00:00',
          step: '00:30',
          end: '24:00'
        },
        dataRule: {
          begaintime: [
            {required: true, message: '开始时间不能为空', trigger: 'blur'}
          ],
          endtime: [
            {required: true, message: '结束时间不能为空', trigger: 'blur'}
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
              this.dataForm.begaintime = openTimeArr[0]
              this.dataForm.endtime = openTimeArr[1]
            }
          }
        })
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/app/seating/abouttime`),
              method: 'post',
              data: this.$http.adornData({
                'begaintime': this.dataForm.begaintime,
                'endtime': this.dataForm.endtime
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
