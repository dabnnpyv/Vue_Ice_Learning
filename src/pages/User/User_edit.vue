<template>

  <el-dialog title="修改" width="50%"   center   :visible.sync="edit_show" :before-close="beforeClose">
    <el-form  :model="edit_model"  ref="ruleForm" :rules="rules" class="demo-form-inline" >
      <el-row>
        <el-col :span="12">
          <el-form-item prop="username" label="账号" :label-width="formLabelWidth">
              <el-input v-model="edit_model.username"  placeholder="请输入 账号"></el-input>
          </el-form-item>
        </el-col>
          <el-col :span="12">
              <el-form-item prop="name" label="姓名" :label-width="formLabelWidth">
                  <el-input v-model="edit_model.name"  placeholder="请输入 姓名"></el-input>
              </el-form-item>
          </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item prop="sid" label="工号" :label-width="formLabelWidth">
            <el-input v-model.number="edit_model.sid"   placeholder="请输入 工号"></el-input>
          </el-form-item>
        </el-col>
          <el-col :span="12">
              <el-form-item prop="sex"  label=" 性别" :label-width="formLabelWidth">
                  <el-select  align="center"  v-model="edit_model.sex"  placeholder="请选择 性别"> <!--style="width: 185px;" -->
                      <el-option align="center"  label="男"  :value="1"></el-option>
                      <el-option align="center"  label="女"  :value="0"></el-option>
                  </el-select>
              </el-form-item>
          </el-col>
      </el-row>

      <el-row>
          <el-col :span="12"> <!-- fuck value-format="yyyy-MM-dd"  日期选择器 需要 这样进行时间格式化！-->
              <el-form-item  prop="dob" label="出生日期" :label-width="formLabelWidth" >
                  <el-date-picker v-model = "edit_model.dob"  type="date"  format="yyyy 年 MM 月 dd 日" value-format="yyyy-MM-dd"  placeholder="选择日期" :editable=false  > </el-date-picker>

              </el-form-item>
          </el-col>
        <el-col :span="12">
          <el-form-item label="是否启用" :label-width="formLabelWidth" align="center" >
            <el-switch v-model="edit_model.isenable"  :active-value="1" :inactive-value="0" inactive-text="禁用" active-text="启用" active-color="#13ce66" >  </el-switch>
          </el-form-item>
        </el-col>
      </el-row>

      <el-form-item prop="remark" label="备注信息" :label-width="formLabelWidth">
        <el-input v-model="edit_model.remark"  type="textarea" :autosize="{ minRows: 2, maxRows: 4}" placeholder="最多输入10个字哦~" maxlength="10">    </el-input>
      </el-form-item>
    </el-form>

    <div slot="footer" class="dialog-footer">
      <el-button type="primary" icon="el-icon-check" @click="dialog_ok('ruleForm')" >确 定</el-button>
      <el-button type="danger" icon="el-icon-close" @click="$store.state.dialog_store.edit_show=false">取 消</el-button>
    </div>
  </el-dialog>
</template>

<script>
import { mapGetters} from 'vuex'
import {StrToGMT,GMTToStr} from '../../utils/times.js';
  export default {
      computed: {
          ...mapGetters([ 'edit_show' ]),
          ...mapGetters([ 'edit_model' ]),
      },

      data() {
          return {
              formLabelWidth: '80px',
              rules: {
                  username: [
                      { required: true, message: '请输入 账号', trigger: 'blur' }, //为空时 提示信息
                      {  min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' } ],  // 验证不通过时 提示信息
                  name: [
                      { required: true, message: '请输入 姓名', trigger: 'blur' }, //为空时 提示信息
                      {  min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' } ],  // 验证不通过时 提示信息
                  sid: [
                      { required: true, message: '请输入 工号',trigger: 'blur'},
                      { type: 'number', message: '工号必须为数字值'}],
                  dob: [
                      { type: 'string', required: true, message: '请选择日期', trigger: 'blur' }],
                  sex: [
                      { required: true, message: '请选择 性别', trigger: 'change' }       ],
                  remark: [
                      { required: true, message: '请输入 备注', trigger: 'blur' },
                      {  min: 3, max: 50, message: '长度在 3 到 50 个字符', trigger: 'blur' } ],  //
              },
          }
      },

    watch: {
        edit_show: function (newQuestion, oldQuestion) {  console.log(newQuestion,oldQuestion); },
    },

    methods: {
        beforeClose() { this.$store.state.dialog_store.edit_show = false },
        cancelFieldValidate (formName) { this.$refs[formName].resetFields();}, // 清除校检信息 防止记录上次的校检信息
        dialog_ok(formName){
            var _this = this;
            this.$refs[formName].validate((valid) => {
                if (valid){
                    console.log(this.edit_model,'PATCH')
//                    console.log(this.edit_model.dob,'11111111111111dob')
//                    this.edit_model.dob =  GMTToStr(this.edit_model.dob); // 1. sos 转换
                    this.$axios.post('api/sys_user/PATCH', this.edit_model).then(function (res) {  // fuck  为什么 这里使用 .patch 后台配合使用 method= RequestMethod.PATCH 就提示禁止跨域请求呢？
                        _this.$message.success('修改成功！');
                        _this.$store.state.dialog_store.edit_show=false
                        _this.$emit('addSaveTodo'); // 添加成功后 隐藏自身对话框
                        console.log(res);
                    }).catch(function (error) { console.log(error);});
                }
            });
        }
    }
  }
</script>

<style scoped>

</style>
