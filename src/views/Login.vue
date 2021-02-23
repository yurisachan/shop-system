<template>
  <div class="body">
    <div class="login_container">
      <div class="login_border">
        <div class="login_box">
          <!-- 头像区域 -->
          <div class="avatar">
            <img src="~/assets/images/cart.svg" alt="" />
          </div>
          <!-- 登录表单区域 -->
          <el-form label-width="0px" :model="loginForm" :rules="loginRules" class="login_form" ref="loginFormRef">
            <!-- 用户名 -->
            <el-form-item prop="username">
              <!-- 字体文件在入口文件引入后，可以在 prefix-icon 写class名 -->
              <el-input v-model="loginForm.username" prefix-icon="iconfont icon-user"></el-input>
            </el-form-item>

            <!-- 密码 -->
            <el-form-item prop="password">
              <el-input type="password" v-model="loginForm.password" prefix-icon="iconfont icon-3702mima" show-password></el-input>
            </el-form-item>

            <!-- 按钮 -->
            <el-form-item class="btns">
              <el-button type="primary" @click="login">登录</el-button>
              <el-button type="info" @click="resetLoginForm" title="重置表单数据、清除验证规则">重置</el-button>
            </el-form-item>
          </el-form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data() {
    return {
      // 这是登录表单的数据绑定对象
      loginForm: {
        username: 'admin',
        password: '123456',
      },
      // 验证规则
      loginRules: {
        // 验证用户名是否合法
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          {
            min: 3,
            max: 16,
            message: '长度在 3 到 16 个字符',
            trigger: 'blur',
          },
        ],
        // 验证密码是否合法
        password: [
          { required: true, message: '请输入登录密码', trigger: 'blur' },
          {
            min: 6,
            max: 16,
            message: '长度在 6 到 16 个字符',
            trigger: 'blur',
          },
        ],
      },
      // 消息框
      notify: null,
    }
  },
  created() {
    this.notify = this.$notify({
      title: '欢迎您',
      message: '初始用户名：admin </br> 初始密码：123456',
      type: 'success',
      dangerouslyUseHTMLString: true,
      offset: 100,
      duration: 5000,
    })
  },
  destroyed() {
    // 组件销毁时关闭提示框
    this.notify.close()
  },
  methods: {
    // 点击重置按钮，重置表单数据和验证规则
    resetLoginForm() {
      console.log(this)
      this.$refs.loginFormRef.resetFields()
    },
    login() {
      // 判断是否校验通过 => valid是否通过，field未通过的字段
      this.$refs.loginFormRef.validate(async (valid, field) => {
        // console.log(valid, field);

        // 未通过验证
        if (!valid) {
          let arr = Object.keys(field)
          // 拿到未通过验证的验证规则提示
          return this.$message.error({
            message: field[arr[0]][0].message,
            duration: 1000,
          })
        }

        let { data: result } = await this.$http.post('login', this.loginForm)
        // 登录失败
        if (result.meta.status !== 200) {
          return this.$message.error({
            message: result.meta.msg,
            duration: 1000,
          })
        }
        console.log(result)
        // 登录成功
        this.$message.success({
          message: '登录成功！',
          duration: 1000,
        })
        // 保存token
        window.sessionStorage.setItem('token', result.data.token)
        // 跳转到首页
        this.$router.push('/home')
      })
    },
  },
}
</script>

<style lang="less" scoped>

.body {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}
.body::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(45deg, #e91e63, #e91e63 50%, #ffc107 50%, #ffc107);
}
.body::after {
  content: '';
  position: absolute;
  top: -20px;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(160deg, #03a9f4, #03a9f4 50%, transparent 50%, transparent);
  animation: animate 5s ease-in-out infinite;
}
@keyframes animate {
  0%,
  100% {
    transform: translateY(10px);
  }
  50% {
    transform: translateY(-10px);
  }
}
.login_container {
  position: relative;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.login_container::before {
  content: '';
  position: absolute;
  bottom: -80px;
  width: 100%;
  height: 60px;
  background: radial-gradient(rgba(0, 0, 0, 0.2), transparent, transparent);
  border-radius: 50%;
}
.login_border {
  position: relative;
  z-index: 1;
  width: 470px;
  height: 320px;
  backdrop-filter: blur(25px);
  border: 1px solid rgba(255, 255, 255, 0.5);
  animation: animate 5s ease-in-out infinite;
  animation-delay: -2.5s;
  border-radius: 30px;
}
.login_box {
  position: absolute;
  top: 10px;
  left: 10px;
  right: 10px;
  bottom: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  background: radial-gradient(transparent, rgba(255, 255, 255, 0.2));
  background-size: cover;
  backdrop-filter: blur(25px);
  border: 1px solid rgba(255, 255, 255, 0.5);
  border-bottom: none;
  border-right: none;
  box-shadow: -10px -10px 20px rgba(255, 255, 255, 0.1), 10px 10px 20px rgba(0, 0, 0, 0.1), 0px 40px 50px rgba(0, 0, 0, 0.2);

  width: 450px;
  height: 300px;
  border-radius: 30px;


  .avatar {
    width: 130px;
    height: 130px;
    //border: 1px solid #eee;
    border-radius: 50%;
    padding: 5px;
    position: absolute;
    left: 50%;
    transform: translate(-50%, -65%);
    //background-color: #fff;
    img {
      width: 100px;
      height: 100px;
      border-radius: 50%;
    }
  }
}

.login_form {
  position: absolute;
  bottom: 0;
  width: 100%;
  padding: 0 20px;
  box-sizing: border-box;
}
.btns {
  display: flex;
  justify-content: flex-end;
}
</style>