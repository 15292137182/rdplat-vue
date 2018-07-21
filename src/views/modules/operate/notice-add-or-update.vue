<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="语言类型" prop="langType">
      <el-input v-model="dataForm.langType" placeholder="语言类型"></el-input>
    </el-form-item>
    <el-form-item label="主题" prop="subject">
      <el-input v-model="dataForm.subject" placeholder="主题"></el-input>
    </el-form-item>
    <el-form-item label="内容" prop="content">
      <el-input v-model="dataForm.content" placeholder="内容"></el-input>
    </el-form-item>
    <el-form-item label="发布时间" prop="publicTime">
      <el-input v-model="dataForm.publicTime" placeholder="发布时间"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态"></el-input>
    </el-form-item>
    <el-form-item label="消息类型" prop="msgType">
      <el-input v-model="dataForm.msgType" placeholder="消息类型"></el-input>
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
          pId: 0,
          langType: '',
          subject: '',
          content: '',
          publicTime: '',
          status: '',
          createTime: '',
          createUser: '',
          modifyTime: '',
          modifyUser: '',
          msgType: ''
        },
        dataRule: {
          langType: [
            { required: true, message: '语言类型不能为空', trigger: 'blur' }
          ],
          subject: [
            { required: true, message: '主题不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '内容不能为空', trigger: 'blur' }
          ],
          publicTime: [
            { required: true, message: '发布时间不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
          ],
          msgType: [
            { required: true, message: '消息类型不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.pId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.pId) {
            this.$http({
              url: this.$http.adornUrl(`/operate/notice/info/${this.dataForm.pId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.langType = data.notice.langType
                this.dataForm.subject = data.notice.subject
                this.dataForm.content = data.notice.content
                this.dataForm.publicTime = data.notice.publicTime
                this.dataForm.status = data.notice.status
                this.dataForm.createTime = data.notice.createTime
                this.dataForm.createUser = data.notice.createUser
                this.dataForm.modifyTime = data.notice.modifyTime
                this.dataForm.modifyUser = data.notice.modifyUser
                this.dataForm.msgType = data.notice.msgType
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
              url: this.$http.adornUrl(`/operate/notice/${!this.dataForm.pId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'pId': this.dataForm.pId || undefined,
                'langType': this.dataForm.langType,
                'subject': this.dataForm.subject,
                'content': this.dataForm.content,
                'publicTime': this.dataForm.publicTime,
                'status': this.dataForm.status,
                'createTime': this.dataForm.createTime,
                'createUser': this.dataForm.createUser,
                'modifyTime': this.dataForm.modifyTime,
                'modifyUser': this.dataForm.modifyUser,
                'msgType': this.dataForm.msgType
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
