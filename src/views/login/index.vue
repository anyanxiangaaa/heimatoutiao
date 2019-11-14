<template>
  <div class="login">
    <!-- elementui card卡片插件 -->
    <el-card class="login-card">
    <!-- 卡片内容 -->
    <div class="title">
      <img src="../../assets/img/logo_index.png" alt="">
    </div>
    <!-- 表单 -->
    <!-- model属性要绑定表单数据对象 rules标识绑定校验规则对象 ref属性 -->
    <el-form  ref="formObj" style="margin-top:30px" :model="loginform" :rules="loginRules">
<!-- 一个表单域就是一个form-item -->
       <el-form-item prop="mobile">
          <el-input v-model="loginform.mobile" placeholder="请输入您的手机号"></el-input>
       </el-form-item>
       <!-- 验证码和发送验证码 -->
       <el-form-item prop="code">
           <el-input v-model="loginform.code" style="width:280px" placeholder="请输入验证码"></el-input>
           <el-button style="float:right" plain>获取验证码</el-button>
        </el-form-item>
<!-- 勾选同意框 -->
      <el-form-item prop="checked">
            <el-checkbox v-model="loginform.checked">我已阅读并同意用户协议及条款</el-checkbox>
      </el-form-item>
       <el-form-item>
            <el-button style="width:100%" type="primary" @click="login">登录</el-button>
      </el-form-item>
    </el-form>
    </el-card>
  </div>
</template>

<script>
export default {
  data () {
  // 要校验的整个表单数据
    return {
      loginform: {
        model: '', // 手机号
        code: '', // 验证码
        checked: false// 是否勾选协议

      },
      // 校验规则对象
      loginRules: {
        // key要校验的字段名 ：value（数组=》多条或者1条或者没有规则）
        mobile: [{ required: true, message: '请输入您的手机号' }, {
          pattern: /^1[3456789]\d{9}$/, message: '请输入正确的手机号'
        }],
        code: [{ required: true, message: '请输入您的验证码' },
          { pattern: /^\d{6}$/, message: '请输入六位验证码'
          }],
        checked: [{ validator: function (rule, value, callback) {
          // rule代表当前的规则
          // value代表当前的值 表单字段check的值
          // callback回调函数
          if (value) {
            callback()// 直接执行callback表示直接通过
          } else {
            callback(new Error('您需要勾选用户协议'))
          }
        } }]
      }
    }
  },
  methods: {
    login () {
      //  this.$refs.formObj  获取 el-form 的对象实例
      this.$refs.formObj.validate((isOK) => {
        if (isOK) {
          // 如果为true 继续下一步 调用接口 登录
          this.$axios({
            url: '/authorizations',
            data: this.loginForm,
            method: 'post'
          }).then(result => {
            // 存储到本地存储
            window.localStorage.setItem('user-token', result.data.data.token)
            // 跳转到主页
            this.$router.push('/home')
          }).catch(() => {
            // 提示消息
            this.$message({
              type: 'warning',
              message: '手机号或者验证码错误'
            })
          })
        }
      })
    }
  }

}
</script>

<style lang="less" scoped>
 .login{
   background-image:url(../../assets/img/login_bg.jpg);
   background-size: cover;
   height: 100vh;
   display: flex;
   justify-content:center;
   align-items: center;
   .login-card{
     width:440px;
     height:360px;
     .title{
       text-align: center;
       img{
         height:45px;
       }
     }
   }
 }
</style>
