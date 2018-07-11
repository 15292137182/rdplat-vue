<template>
  <el-dialog
    :title="!dataForm.dictId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">

      <el-form-item label="上级名称" prop="parentName">
        <el-popover
          ref="dictListPopover"
          placement="bottom-start"
          trigger="click">
          <el-tree
            :data="dictList"
            :props="dictListTreeProps"
            node-key="dictId"
            ref="dictListTree"
            @current-change="dictListTreeCurrentChangeHandle"
            :default-expand-all="true"
            :highlight-current="true"
            :expand-on-click-node="false">
          </el-tree>
        </el-popover>
        <el-input v-model="dataForm.parentName" v-popover:dictListPopover :readonly="true" placeholder="点击选择上级名称" class="dict-list__input"></el-input>
      </el-form-item>

    <el-form-item label="名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="名称"></el-input>
    </el-form-item>
    <el-form-item label="键" prop="key">
      <el-input v-model="dataForm.key" placeholder="key"></el-input>
    </el-form-item>
    <el-form-item label="值" prop="value">
      <el-input v-model="dataForm.value" placeholder="value"></el-input>
    </el-form-item>
    <el-form-item label="排序" prop="sort">
      <el-input v-model="dataForm.sort" placeholder="排序"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="remarks">
      <el-input v-model="dataForm.remarks" placeholder="备注"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import { treeDataTranslate } from '@/utils'
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          dictId: 0,
          parentId: '',
          name: '',
          parentName: '',
          key: '',
          value: '',
          sort: '',
          status: '',
          createUser: '',
          createTime: '',
          modifyUser: '',
          modifyTime: '',
          remarks: ''
        },
        dataRule: {
          name: [
            { required: true, message: '名称不能为空', trigger: 'blur' }
          ],
          key: [
            { required: true, message: 'key不能为空', trigger: 'blur' }
          ],
          value: [
            { required: true, message: 'value不能为空', trigger: 'blur' }
          ]
        },
        dictList: [],
        dictListTreeProps: {
          label: 'name',
          children: 'children'
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.dictId = id || 0
        this.visible = true
        this.$http({
          url: this.$http.adornUrl('/sys/dataDict/select'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          console.log(data.dictList)
          this.dictList = treeDataTranslate(data.dictList, 'dictId')
        }).then(() => {
          this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
            if (!this.dataForm.dictId) {
              this.dictListTreeSetCurrentNode()
            } else {
              this.$http({
                url: this.$http.adornUrl(`/sys/dataDict/info/${this.dataForm.dictId}`),
                method: 'get',
                params: this.$http.adornParams()
              }).then(({data}) => {
                if (data && data.code === 0) {
                  this.dataForm.parentId = data.sysDataDict.parentId
                  this.dataForm.name = data.sysDataDict.name
                  this.dataForm.key = data.sysDataDict.key
                  this.dataForm.value = data.sysDataDict.value
                  this.dataForm.sort = data.sysDataDict.sort
                  this.dataForm.status = data.sysDataDict.status
                  this.dataForm.createUser = data.sysDataDict.createUser
                  this.dataForm.createTime = data.sysDataDict.createTime
                  this.dataForm.modifyUser = data.sysDataDict.modifyUser
                  this.dataForm.modifyTime = data.sysDataDict.modifyTime
                  this.dataForm.remarks = data.sysDataDict.remarks
                  this.dictListTreeSetCurrentNode()
                }
              })
            }
          })
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          console.log(this.dataForm.dictId)
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/dataDict/${!this.dataForm.dictId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'dictId': this.dataForm.dictId || undefined,
                'parentId': this.dataForm.parentId,
                'name': this.dataForm.name,
                'key': this.dataForm.key,
                'value': this.dataForm.value,
                'sort': this.dataForm.sort,
                'status': this.dataForm.status,
                'createUser': this.dataForm.createUser,
                'createTime': this.dataForm.createTime,
                'modifyUser': this.dataForm.modifyUser,
                'modifyTime': this.dataForm.modifyTime,
                'remarks': this.dataForm.remarks
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
      },
      // 菜单树选中
      dictListTreeCurrentChangeHandle (data, node) {
        this.dataForm.parentId = data.dictId
        this.dataForm.parentName = data.name
      },
      // 菜单树设置当前选中节点
      dictListTreeSetCurrentNode () {
        this.$refs.dictListTree.setCurrentKey(this.dataForm.dictId)
        this.dataForm.parentName = (this.$refs.dictListTree.getCurrentNode() || {})['name']
      }
    }
  }
</script>

<style lang="scss">
  .mod-dict {
    .dict-list__input,
    .icon-list__input {
      > .el-input__inner {
        cursor: pointer;
      }
    }
    &__icon-popover {
      max-width: 370px;
    }
    &__icon-list {
      max-height: 180px;
      padding: 0;
      margin: -8px 0 0 -8px;
      > .el-button {
        padding: 8px;
        margin: 8px 0 0 8px;
        > span {
          display: inline-block;
          vertical-align: middle;
          width: 18px;
          height: 18px;
          font-size: 18px;
        }
      }
    }
    .icon-list__tips {
      font-size: 18px;
      text-align: center;
      color: #e6a23c;
      cursor: pointer;
    }
  }
</style>

