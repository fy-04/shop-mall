<template>
  <div class="login-container">
    <el-form
      ref="loginForm"
      :model="loginForm"
      :rules="loginRules"
      class="login-form"
      auto-complete="on"
      label-position="left"
    >
      <div class="title-container">
        <h3 class="title">登录</h3>
      </div>
      <!-- 输入用户名称模块 -->
      <el-form-item
        type="flex"
        justify="space-between"
        prop="username"
        class="check"
      >
        <span class="svg-container">
          <svg-icon icon-class="user" />
        </span>

        <el-input
          ref="username"
          v-model="loginForm.username"
          placeholder="Username"
          name="username"
          type="text"
          tabindex="1"
          auto-complete="on"
        />

        <button>发送验证码</button>
      </el-form-item>

      <!-- 输入密码模块 -->
      <el-form-item prop="password">
        <span class="svg-container">
          <svg-icon icon-class="password" />
        </span>
        <el-input
          :key="passwordType"
          ref="password"
          v-model="loginForm.password"
          :type="passwordType"
          placeholder="Password"
          name="password"
          tabindex="2"
          auto-complete="on"
          @keyup.enter.native="handleLogin"
        />
        <!-- 点击该按钮可以切换是否显示密码 -->
        <span class="show-pwd" @click="showPwd">
          <!-- <svg-icon :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'" /> -->
          <i
            :class="{
              'el-icon-open': passwordType === 'password' ? true : false,
              'el-icon-turn-off': passwordType === '',
            }"
          ></i>
          <i :class="el - icon - open"> </i>
          <span
            :style="{
              'background-color':
                passwordType === 'el-icon-turn-off' ? 'red' : 'black',
              black: passwordType !== 'password',
            }"
          >
          </span>
        </span>
      </el-form-item>

      <!-- 用户名称：绑定Valid校验 -->
      <el-form-item prop="valid">
        <span class="svg-container">
          <svg-icon icon-class="user" />
        </span>
        <el-input
          ref="username"
          v-model="loginForm.valid"
          placeholder="Valid"
          name="valid"
          type="text"
          tabindex="1"
          auto-complete="on"
        />
      </el-form-item>

      <!-- @click.native.prevent="handleLogin"的作用：调用登录逻辑 -->
      <el-button
        :loading="loading"
        type="primary"
        style="width: 100%; margin-bottom: 30px"
        @click.native.prevent="handleLogin"
        >登录</el-button
      >

      <div class="tips">
        <span style="margin-right: 20px">username: admin</span>
        <span> password: any</span>
      </div>
    </el-form>
  </div>
</template>

<script>
import { validUsername } from "@/utils/validate";

export default {
  name: "Login",
  data() {
    // 校验自定义规则
    // validator: validateUsername和validator: validatePassword是自定义规则
    // const validateUsername = (rule, value, callback) => {
    //   if (!validUsername(value)) {
    //     callback(new Error("Please enter the correct user name"));
    //   } else {
    //     callback();
    //   }
    // };
    // const validatePassword = (rule, value, callback) => {
    //   if (value.length < 6) {
    //     callback(new Error("The password can not be less than 6 digits"));
    //   } else {
    //     callback();
    //   }
    // };
    return {
      // 校验自定义规则
      // loginRules: {
      // validator: validateUsername和validator: validatePassword是自定义规则
      //   username: [{ required: true, trigger: 'blur', validator: validateUsername }],
      //   password: [{ required: true, trigger: 'blur', validator: validatePassword }]
      // },
      loginForm: {
        username: "admin",
        password: "111111",
        valid: "222",
      },

      loginRules: {
        username: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          // { min: 3, max: 5, message: '长度在 4 到 5 个字符', trigger: 'blur' }
        ],
        valid: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          // { min:6, max: 8, message: '长度在 6 到 8 个字符', trigger: 'blur' }
        ],
      },
      loading: false,
      // 控制密码显示与隐藏的变量passwordType
      passwordType: "password",
      redirect: undefined,
    };
  },

  //   object.watch(prop, handle)
  // 概述：监听一个对象的某个属性是否发生变化，如果发生了变化，则立即执行回调函数
  watch: {
    $route: {
      handler: function (route) {
        // ?username=lwt&a=b
        // $route.query :{
        // a: b,
        // username : lwt
        // }
        this.redirect = route.query && route.query.redirect;
      },
      immediate: true,
    },
  },
  methods: {
    // 切换是否显示密码状态
    // 通过给passwordType赋值为password或者空来切换
    showPwd() {
      if (this.passwordType === "password") {
        this.passwordType = "";
      } else {
        this.passwordType = "password";
      }
      // Vue.nextTick()，是将回调函数延迟在下一次dom更新数据后调用。
      this.$nextTick(() => {
        this.$refs.password.focus();
      });
    },
    handleLogin() {
      // 1. 表单信息 vue 父子组件通信
      // 父组件调用子组件函数
      this.$refs.loginForm.validate((success) => {
        if (success) {
          this.loading = true;
          // 2. 调用vuex 登录逻辑
          // 3. vuex 复习一下
          // 4. then  promise  async  await
          this.$store
            .dispatch("user/login", this.loginForm)
            .then(() => {
              this.$router.push({ path: this.redirect || "/" });
              this.loading = false;
            })
            .catch(() => {
              this.loading = false;
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
  },
};
</script>

<style lang="scss">
/* 修复input 背景不协调 和光标变色 */
/* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */

$bg: #283443;
$light_gray: #fff;
$cursor: #fff;

@supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
  .login-container .el-input input {
    color: $cursor;
  }
}

/* reset element-ui css */
.login-container {
  .check {
    .el-form-item__content {
      display: flex;
      background-color: red;
    }
    button {
      width: 180px;
    }
  }

  .el-input {
    display: inline-block;
    height: 47px;
    width: 85%;

    input {
      background: transparent;
      border: 0px;
      -webkit-appearance: none;
      border-radius: 0px;
      padding: 12px 5px 12px 15px;
      color: $light_gray;
      height: 47px;
      caret-color: $cursor;

      &:-webkit-autofill {
        box-shadow: 0 0 0px 1000px $bg inset !important;
        -webkit-text-fill-color: $cursor !important;
      }
    }
  }

  .el-form-item {
    border: 1px solid rgba(255, 255, 255, 0.1);
    background: rgba(0, 0, 0, 0.1);
    border-radius: 5px;
    color: #454545;
  }
}
</style>

<style lang="scss" scoped>
$bg: #2d3a4b;
$dark_gray: #889aa4;
$light_gray: #eee;

.login-container {
  min-height: 100%;
  width: 100%;
  background-color: $bg;
  overflow: hidden;
  background: url(~@/assets/1.png);
  background-size: 100% 100%;

  .login-form {
    position: relative;
    width: 520px;
    max-width: 100%;
    padding: 160px 35px 0;
    margin: 0 auto;
    overflow: hidden;
  }

  .tips {
    font-size: 14px;
    color: #fff;
    margin-bottom: 10px;

    span {
      &:first-of-type {
        margin-right: 16px;
      }
    }
  }

  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $dark_gray;
    vertical-align: middle;
    width: 30px;
    display: inline-block;
  }

  .title-container {
    position: relative;

    .title {
      font-size: 26px;
      color: $light_gray;
      margin: 0px auto 40px auto;
      text-align: center;
      font-weight: bold;
    }
  }

  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $dark_gray;
    cursor: pointer;
    user-select: none;
  }
}
</style>
