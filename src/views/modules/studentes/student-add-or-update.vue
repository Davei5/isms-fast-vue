<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="学号" prop="stuid">
      <el-input v-model="dataForm.stuid" placeholder="学号"></el-input>
    </el-form-item>
    <el-form-item label="所属班级" prop="classname">
      <el-input v-model="dataForm.classname" placeholder="所属班级"></el-input>
    </el-form-item>
    <el-form-item label="所属系别" prop="depname">
      <el-input v-model="dataForm.depname" placeholder="所属系别"></el-input>
    </el-form-item>
    <el-form-item label="姓名" prop="stuname">
      <el-input v-model="dataForm.stuname" placeholder="姓名"></el-input>
    </el-form-item>
    <el-form-item label="性别" prop="sex">
      <el-input v-model="dataForm.sex" placeholder="性别"></el-input>
    </el-form-item>
    <el-form-item label="手机号" prop="telephone">
      <el-input v-model="dataForm.telephone" placeholder="手机号"></el-input>
    </el-form-item>
    <el-form-item label="邮箱" prop="email">
      <el-input v-model="dataForm.email" placeholder="邮箱"></el-input>
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
          stuid: '',
          classname: '',
          depname: '',
          stuname: '',
          sex: '',
          telephone: '',
          email: ''
        },
        dataRule: {
          stuid: [
            { required: true, message: '学号不能为空', trigger: 'blur' }
          ],
          classname: [
            { required: true, message: '所属班级不能为空', trigger: 'blur' }
          ],
          depname: [
            { required: true, message: '所属系别不能为空', trigger: 'blur' }
          ],
          stuname: [
            { required: true, message: '姓名不能为空', trigger: 'blur' }
          ],
          sex: [
            { required: true, message: '性别不能为空', trigger: 'blur' }
          ],
          telephone: [
            { required: true, message: '手机号不能为空', trigger: 'blur' }
          ],
          email: [
            { required: true, message: '邮箱不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.stuid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.stuid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/student/info/${this.dataForm.stuid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.classname = data.student.classname
                this.dataForm.depname = data.student.depname
                this.dataForm.stuname = data.student.stuname
                this.dataForm.sex = data.student.sex
                this.dataForm.telephone = data.student.telephone
                this.dataForm.email = data.student.email
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
              url: this.$http.adornUrl(`/generator/student/${!this.dataForm.stuid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'stuid': this.dataForm.stuid || undefined,
                'classname': this.dataForm.classname,
                'depname': this.dataForm.depname,
                'stuname': this.dataForm.stuname,
                'sex': this.dataForm.sex,
                'telephone': this.dataForm.telephone,
                'email': this.dataForm.email
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
