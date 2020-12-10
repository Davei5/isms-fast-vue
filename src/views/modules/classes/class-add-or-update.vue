<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="班级名称" prop="classname">
      <el-input v-model="dataForm.classname" placeholder="班级名称"></el-input>
    </el-form-item>
    <el-form-item label="班主任" prop="hrteacher">
      <el-input v-model="dataForm.hrteacher" placeholder="班主任"></el-input>
    </el-form-item>
    <el-form-item label="所属部门" prop="depid">
      <el-input v-model="dataForm.depid" placeholder="所属部门"></el-input>
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
          classid: 0,
          classname: '',
          hrteacher: '',
          depid: ''
        },
        dataRule: {
          classname: [
            { required: true, message: '班级名称不能为空', trigger: 'blur' }
          ],
          hrteacher: [
            { required: true, message: '班主任不能为空', trigger: 'blur' }
          ],
          depid: [
            { required: true, message: '所属部门不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.classid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.classid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/class/info/${this.dataForm.classid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.classname = data.class.classname
                this.dataForm.hrteacher = data.class.hrteacher
                this.dataForm.depid = data.class.depid
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
              url: this.$http.adornUrl(`/generator/class/${!this.dataForm.classid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'classid': this.dataForm.classid || undefined,
                'classname': this.dataForm.classname,
                'hrteacher': this.dataForm.hrteacher,
                'depid': this.dataForm.depid
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
