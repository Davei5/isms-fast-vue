<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="部门名" prop="depname">
      <el-input v-model="dataForm.depname" placeholder="部门名"></el-input>
    </el-form-item>
    <el-form-item label="部门管理员" prop="depadmin">
      <el-input v-model="dataForm.depadmin" placeholder="部门管理员"></el-input>
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
          depid: 0,
          depname: '',
          depadmin: ''
        },
        dataRule: {
          depname: [
            { required: true, message: '部门名不能为空', trigger: 'blur' }
          ],
          depadmin: [
            { required: true, message: '部门管理员不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.depid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.depid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/department/info/${this.dataForm.depid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.depname = data.department.depname
                this.dataForm.depadmin = data.department.depadmin
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
              url: this.$http.adornUrl(`/generator/department/${!this.dataForm.depid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'depid': this.dataForm.depid || undefined,
                'depname': this.dataForm.depname,
                'depadmin': this.dataForm.depadmin
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
