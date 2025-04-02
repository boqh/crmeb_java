<template>
  <div class="login-wrapper">
    <div class="shading">
      <image :src="logoUrl"/>
    </div>
    <div class="whiteBg">
      <!-- 登录表单 -->
      <div class="list" v-if="formItem === 1">
        <form @submit.prevent="submit">
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/phone_1.png" style="width: 24rpx; height: 34rpx;"></image>
              <input type="text" class="texts" placeholder="输入手机号码" v-model="account" @blur="validatePhone"/>
            </div>
            <div class="error-tip" v-if="errors.phone">{{ errors.phone }}</div>
          </div>
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
              <input type="password" class="texts" placeholder="填写登录密码" v-model="password" @blur="validatePassword"/>
            </div>
            <div class="error-tip" v-if="errors.password">{{ errors.password }}</div>
          </div>
          <!-- 图形验证码 -->
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
              <input
                  type="text"
                  placeholder="请输入验证码"
                  class="codeIput"
                  v-model="verifyCodeInput"
                  @blur="validateVerifyCode"
              />
              <div class="code verify-code" @click="generateVerifyCode">
                {{ verifyCode }}
              </div>
            </div>
            <div class="error-tip" v-if="errors.verifyCode">{{ errors.verifyCode }}</div>

          </div>
          <div class="agreement">
            <checkbox-group @change="handleAgreementChange">
              <checkbox value="1" :checked="isAgree" style="transform:scale(0.7)"/>
            </checkbox-group>
            <text class="agreement-text">我已阅读并同意</text>
            <text class="link" @click="openAgreement('user')">《用户协议》</text>
            <text class="agreement-text">和</text>
            <text class="link" @click="openAgreement('privacy')">《隐私政策》</text>
          </div>
        </form>
      </div>

      <!-- 注册表单 -->
      <div class="list" v-if="formItem === 2">
        <div class="item">
          <div class="acea-row row-middle">
            <image src="/static/images/phone_1.png" style="width: 24rpx; height: 34rpx;"></image>
            <input type="text" class="texts" placeholder="输入手机号码" v-model="registerForm.phone" @blur="validatePhone"/>
          </div>
          <div class="error-tip" v-if="errors.registerPhone">{{ errors.registerPhone }}</div>
        </div>
        <div class="item">
          <div class="acea-row row-middle">
            <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
            <input type="password" class="texts" placeholder="设置密码" v-model="registerForm.password" @blur="validatePassword"/>
          </div>
          <div class="error-tip" v-if="errors.registerPassword">{{ errors.registerPassword }}</div>
        </div>
        <div class="item">
          <div class="acea-row row-middle">
            <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
            <input type="password" class="texts" placeholder="确认密码" v-model="registerForm.confirmPassword" @blur="validatePassword"/>
          </div>
          <div class="error-tip" v-if="errors.confirmPassword">{{ errors.confirmPassword }}</div>
        </div>
        <div class="item">
          <div class="acea-row row-middle">
            <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
            <input type="text" placeholder="验证码" class="codeIput" v-model="verifyCodeInput" @blur="validateVerifyCode"/>
            <div class="code verify-code" @click="generateVerifyCode">
              {{ verifyCode }}
            </div>
          </div>
          <div class="error-tip" v-if="errors.verifyCode">{{ errors.verifyCode }}</div>
        </div>
        <div class="agreement">
          <checkbox-group @change="handleAgreementChange">
            <checkbox value="1" :checked="isAgree" style="transform:scale(0.7)"/>
          </checkbox-group>
          <text class="agreement-text">我已阅读并同意</text>
          <text class="link" @click="openAgreement('user')">《用户协议》</text>
          <text class="agreement-text">和</text>
          <text class="link" @click="openAgreement('privacy')">《隐私政策》</text>
        </div>
      </div>
      <!-- 按钮区域 -->
      <div class="logon" @click="handleSubmit">{{ formItem === 1 ? '登录' : '注册' }}</div>
      <div class="tips">
        <div @click="switchForm">{{ formItem === 1 ? '没有账号？去注册' : '已有账号？去登录' }}</div>
      </div>
    </div>
    <div class="bottom"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      formItem: 1, // 1:登录 2:注册
      account: "",
      password: "",
      captcha: "",
      captchaImage: "",
      logoUrl: "",
      registerForm: {
        phone: "",
        password: "",
        smsCode: ""
      },
      errors: {
        phone: "",
        password: "",
        captcha: "",
        registerPhone: "",
        registerPassword: "",
        smsCode: ""
      },
      smsText: "获取验证码",
      disabled: false,
      timer: null,
      verifyCode: '', // 验证码的值
      verifyCodeInput: '', // 用户输入的验证码
      isAgree: false,
    };
  },

  mounted() {
    this.generateVerifyCode();
  },

  methods: {
    // 切换表单时刷新验证码
    switchForm() {
      this.formItem = this.formItem === 1 ? 2 : 1;
      this.errors = {};
      this.generateVerifyCode();
      this.clearForm();
    },

    // 清空表单
    clearForm() {
      if (this.formItem === 1) {
        this.account = "";
        this.password = "";
      } else {
        this.registerForm = {
          phone: "",
          password: "",
          confirmPassword: "",
          smsCode: ""
        };
        this.isAgree = false;
      }
      this.verifyCodeInput = "";
    },

    // 验证确认密码
    validateConfirmPassword() {
      if (!this.registerForm.confirmPassword) {
        this.errors.confirmPassword = "请确认密码";
        return false;
      }
      if (this.registerForm.confirmPassword !== this.registerForm.password) {
        this.errors.confirmPassword = "两次输入的密码不一致";
        return false;
      }
      this.errors.confirmPassword = "";
      return true;
    },

    // 处理协议勾选
    handleAgreementChange(e) {
      this.isAgree = e.detail.value.length > 0;
    },

    // 打开协议
    openAgreement(type) {
      const url = type === 'user' ? '/pages/agreement/user' : '/pages/agreement/privacy';
      uni.navigateTo({
        url
      });
    },
    // 生成随机验证码
    generateVerifyCode() {
      const characters = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
      let code = '';
      for (let i = 0; i < 4; i++) {
        code += characters.charAt(Math.floor(Math.random() * characters.length));
      }
      this.verifyCode = code;
    },

    // 验证码验证
    validateVerifyCode() {
      if (!this.verifyCodeInput) {
        this.errors.verifyCode = "验证码不能为空";
        return false;
      }
      if (this.verifyCodeInput.toLowerCase() !== this.verifyCode.toLowerCase()) {
        this.errors.verifyCode = "验证码错误";
        this.generateVerifyCode(); // 验证失败时刷新验证码
        return false;
      }
      this.errors.verifyCode = "";
      return true;
    },
    // 表单验证方法
    validatePhone() {
      if (!this.account) {
        this.errors.phone = "手机号不能为空";
        return false;
      }
      if (!/^1[3-9]\d{9}$/.test(this.account)) {
        this.errors.phone = "请输入正确的手机号";
        return false;
      }
      this.errors.phone = "";
      return true;
    },

    validatePassword() {
      if (!this.password) {
        this.errors.password = "密码不能为空";
        return false;
      }
      if (this.password.length < 6) {
        this.errors.password = "密码长度不能少于6位";
        return false;
      }
      this.errors.password = "";
      return true;
    },

    // 提交表单
    async handleSubmit() {
      if (this.formItem === 1) {
        // 登录验证
        if (!this.validatePhone() || !this.validatePassword() || !this.validateVerifyCode() ) {
          return;
        }

        if (!this.isAgree) {
          uni.showToast({
            title: '请阅读并同意用户协议和隐私政策',
            icon: 'none'
          });
          return;
        }

        // 执行登录
        try {
          const res = await loginH5({
            account: this.account,
            password: this.password,
          });
          // 处理登录成功
          this.$store.commit("LOGIN", { token: res.data.token });
          this.getUserInfo(res.data);
        } catch (error) {
          this.refreshCaptcha();
        }
      } else {
        // 注册验证
        if (!this.validatePhone() || !this.validatePassword()  ||  !this.validateRegisterPassword() || !this.validateVerifyCode()) {
          return;
        }
        if (!this.isAgree) {
          uni.showToast({
            title: '请阅读并同意用户协议和隐私政策',
            icon: 'none'
          });
          return;
        }
        // 执行注册
        try {
          await register({
            phone: this.registerForm.phone,
            password: this.registerForm.password,
            code: this.registerForm.smsCode
          });
          // 注册成功后切换到登录
          this.formItem = 1;
          uni.showToast({
            title: '注册成功，请登录',
            icon: 'none'
          });
        } catch (error) {
          console.error(error);
        }
      }
    }
  }
};
</script>

<style lang="scss" scoped>

.agreement {
  padding: 20rpx 40rpx;
  font-size: 26rpx;
  color: #666;
  display: flex;
  align-items: center;

  .agreement-text {
    margin: 0 4rpx;
  }

  .link {
    color: #2d8cf0;
  }
}

.verify-code {
  padding: 10rpx 30rpx;
  font-family: Arial;
  font-style: italic;
  font-weight: bold;
  font-size: 64rpx;  /* 进一步增大字体 */
  color: #333;
  letter-spacing: 12rpx;  /* 增加字间距 */
  cursor: pointer;
  user-select: none;
  text-align: center;
  min-width: 200rpx;  /* 增加容器宽度 */
  height: 100rpx;  /* 增加容器高度 */
  line-height: 100rpx;  /* 调整行高以保持垂直居中 */
  border-radius: 8rpx;  /* 添加圆角 */
  margin-left: 20rpx;  /* 增加左边距 */
}

.error-tip {
  color: #ff4d4f;
  font-size: 24rpx;
  margin-top: 8rpx;
  padding-left: 40rpx;
}

.code {
  img {
    height: 60rpx;
    width: 120rpx;
  }
}

page {
  background: #fff;
}
.appLogin {
  margin-top: 60rpx;

  .hds {
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 24rpx;
    color: #B4B4B4;

    .line {
      width: 68rpx;
      height: 1rpx;
      background: #CCCCCC;
    }

    p {
      margin: 0 20rpx;
    }
  }

  .btn-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 30rpx;

    .btn {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 68rpx;
      height: 68rpx;
      border-radius: 50%;
    }

    .apple-btn {
      display: flex;
      align-items: center;
      justify-content: center;
      margin-left: 30rpx;
      background: #000;
      border-radius: 34rpx;
      font-size: 40rpx;

      .icon-s-pingguo {
        color: #fff;
        font-size: 40rpx;
      }
    }

    .iconfont {
      font-size: 40rpx;
      color: #fff;
    }

    .wx {
      margin-right: 30rpx;
      background-color: #61C64F;
    }

    .mima {
      background-color: #28B3E9;
    }

    .yanzheng {
      background-color: #F89C23;
    }

  }
}

.code img {
  width: 100%;
  height: 100%;
}

.acea-row.row-middle {
  input {
    margin-left: 20rpx;
    display: block;
  }
}

.login-wrapper {
  padding: 30rpx;

  .shading {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;

    /* #ifdef APP-VUE */
    margin-top: 50rpx;
    /* #endif */
    /* #ifndef APP-VUE */

    margin-top: 200rpx;
    /* #endif */


    image {
      width: 180rpx;
      height: 180rpx;
    }
  }

  .whiteBg {
    margin-top: 100rpx;

    .list {
      border-radius: 16rpx;
      overflow: hidden;

      .item {
        border-bottom: 1px solid #F0F0F0;
        background: #fff;

        .row-middle {
          position: relative;
          padding: 16rpx 45rpx;

          .texts{
            flex: 1;
            font-size: 28rpx;
            height: 80rpx;
            line-height: 80rpx;
            display: flex;
            justify-content: center;
            align-items: center;
          }

          input {
            flex: 1;
            font-size: 28rpx;
            height: 80rpx;
            line-height: 80rpx;
            display: flex;
            justify-content: center;
            align-items: center;
          }

          .code {
            position: absolute;
            right: 30rpx;
            top: 50%;
            color: $theme-color;
            font-size: 26rpx;
            transform: translateY(-50%);
          }
        }
      }
    }

    .logon {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 100%;
      height: 86rpx;
      margin-top: 80rpx;
      background-color: $theme-color;
      border-radius: 120rpx;
      color: #FFFFFF;
      font-size: 30rpx;
    }

    .tips {
      margin: 30rpx;
      text-align: center;
      color: #999;
    }
  }
}
</style>
