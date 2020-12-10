<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="学生id" prop="stuid">
      <el-input v-model="dataForm.stuid" placeholder="学生id"></el-input>
    </el-form-item>
    <el-form-item label="智育成绩分" prop="intellectually">
      <el-input v-model="dataForm.intellectually" placeholder="智育成绩分"></el-input>
    </el-form-item>
    <el-form-item label="思想道德素质分" prop="sxddsz">
      <el-input v-model="dataForm.sxddsz" placeholder="思想道德素质分"></el-input>
    </el-form-item>
    <el-form-item label="身体素质分" prop="physically">
      <el-input v-model="dataForm.physically" placeholder="身体素质分"></el-input>
    </el-form-item>
    <el-form-item label="日常行为规范分" prop="rcxwgf">
      <el-input v-model="dataForm.rcxwgf" placeholder="日常行为规范分"></el-input>
    </el-form-item>
    <el-form-item label="发展性素质" prop="developmental">
      <el-input v-model="dataForm.developmental" placeholder="发展性素质"></el-input>
    </el-form-item>
    <el-form-item label="综合测评成绩" prop="zhcp">
      <el-input v-model="dataForm.zhcp" placeholder="综合测评成绩"></el-input>
    </el-form-item>
    <el-form-item label="该成绩所属学期" prop="time">
      <el-input v-model="dataForm.time" placeholder="该成绩所属学期"></el-input>
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
          graid: 0,
          stuid: '',
          intellectually: '',
          sxddsz: '',
          physically: '',
          rcxwgf: '',
          developmental: '',
          zhcp: '',
          time: ''
        },
        dataRule: {
          stuid: [
            { required: true, message: '学生id不能为空', trigger: 'blur' }
          ],
          intellectually: [
            { required: true, message: '智育成绩分不能为空', trigger: 'blur' }
          ],
          sxddsz: [
            { required: true, message: '思想道德素质分不能为空', trigger: 'blur' }
          ],
          physically: [
            { required: true, message: '身体素质分不能为空', trigger: 'blur' }
          ],
          rcxwgf: [
            { required: true, message: '日常行为规范分不能为空', trigger: 'blur' }
          ],
          developmental: [
            { required: true, message: '发展性素质不能为空', trigger: 'blur' }
          ],
          zhcp: [
            { required: true, message: '综合测评成绩不能为空', trigger: 'blur' }
          ],
          time: [
            { required: true, message: '该成绩所属学期不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.graid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.graid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/grades/info/${this.dataForm.graid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.stuid = data.grades.stuid
                this.dataForm.intellectually = data.grades.intellectually
                this.dataForm.sxddsz = data.grades.sxddsz
                this.dataForm.physically = data.grades.physically
                this.dataForm.rcxwgf = data.grades.rcxwgf
                this.dataForm.developmental = data.grades.developmental
                this.dataForm.zhcp = data.grades.zhcp
                this.dataForm.time = data.grades.time
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
              url: this.$http.adornUrl(`/generator/grades/${!this.dataForm.graid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'graid': this.dataForm.graid || undefined,
                'stuid': this.dataForm.stuid,
                'intellectually': this.dataForm.intellectually,
                'sxddsz': this.dataForm.sxddsz,
                'physically': this.dataForm.physically,
                'rcxwgf': this.dataForm.rcxwgf,
                'developmental': this.dataForm.developmental,
                'zhcp': this.dataForm.zhcp,
                'time': this.dataForm.time
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
