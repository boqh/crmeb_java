<template>
  <div class="login-wrapper">
    <div class="shading">
      <image :src="logoUrl" />
    </div>
    <div class="whiteBg">
      <div class="list" v-if="formItem === 1">
        <form @submit.prevent="handleSubmit">
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/phone_1.png" style="width: 24rpx; height: 34rpx;"></image>
              <input type="text" class="texts" placeholder="输入手机号码" v-model="account" @blur="validateAccount" />
            </div>
            <div class="error-tip" v-if="accountError">{{ accountError }}</div>
          </div>
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
              <input type="password" class="texts" placeholder="填写登录密码" v-model="password" @blur="validatePassword" />
            </div>
            <div class="error-tip" v-if="passwordError">{{ passwordError }}</div>
          </div>
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
              <input type="text" placeholder="请输入验证码" class="codeIput" v-model="verifyCodeInput" @blur="validateVerifyCode" />
              <div class="code verify-code" @click="generateVerifyCode">
                {{ verifyCode }}
              </div>
            </div>
            <div class="error-tip" v-if="verifyCodeError">{{ verifyCodeError }}</div>
          </div>
          <div class="agreement">
            <checkbox-group @change="handleAgreementChange">
              <checkbox value="1" :checked="isAgree" style="transform:scale(0.7)" />
            </checkbox-group>
            <text class="agreement-text">我已阅读并同意</text>
            <text class="link" @click="openAgreement('user')">《用户协议》</text>
            <text class="agreement-text">和</text>
            <text class="link" @click="openAgreement('privacy')">《隐私政策》</text>
          </div>
          <div class="error-tip" v-if="agreementError">{{ agreementError }}</div>
          <div class="logon" @click="handleSubmit">登录</div>
        </form>
      </div>

      <div class="list" v-if="formItem === 2">
        <form @submit.prevent="handleSubmit">
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/phone_1.png" style="width: 24rpx; height: 34rpx;"></image>
              <input type="text" class="texts" placeholder="输入手机号码" v-model="registerForm.phone" @blur="validateRegisterPhone" />
            </div>
            <div class="error-tip" v-if="registerPhoneError">{{ registerPhoneError }}</div>
          </div>
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
              <input type="password" class="texts" placeholder="设置密码" v-model="registerForm.password" @blur="validateRegisterPassword" />
            </div>
            <div class="error-tip" v-if="registerPasswordError">{{ registerPasswordError }}</div>
          </div>
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
              <input type="password" class="texts" placeholder="确认密码" v-model="registerForm.confirmPassword" @blur="validateConfirmPassword" />
            </div>
            <div class="error-tip" v-if="confirmPasswordError">{{ confirmPasswordError }}</div>
          </div>
          <div class="item">
            <div class="acea-row row-middle">
              <image src="/static/images/code_2.png" style="width: 28rpx; height: 32rpx;"></image>
              <input type="text" placeholder="请输入验证码" class="codeIput" v-model="verifyCodeInput" @blur="validateVerifyCode" />
              <div class="code verify-code" @click="generateVerifyCode">
                {{ verifyCode }}
              </div>
            </div>
            <div class="error-tip" v-if="verifyCodeError">{{ verifyCodeError }}</div>
          </div>
          <div class="agreement">
            <checkbox-group @change="handleAgreementChange">
              <checkbox value="1" :checked="isAgree" style="transform:scale(0.7)" />
            </checkbox-group>
            <text class="agreement-text">我已阅读并同意</text>
            <text class="link" @click="openAgreement('user')">《用户协议》</text>
            <text class="agreement-text">和</text>
            <text class="link" @click="openAgreement('privacy')">《隐私政策》</text>
          </div>
          <div class="error-tip" v-if="agreementError">{{ agreementError }}</div>
          <div class="logon" @click="handleSubmit">注册</div>
        </form>
      </div>
      <div class="tips">
        <div @click="switchForm">{{ formItem === 1 ? '没有账号？去注册' : '已有账号？去登录' }}</div>
      </div>
    </div>
    <div class="bottom"></div>
  </div>
</template>

<script>
// 导入相关 API loginH5 登录 loginMobile 注册
import { loginH5, loginMobile, getUserInfo } from '@/api/user';


const BACK_URL = "login_back_url";


export default {
  data() {
    return {
      formItem: 1,
      account: "",
      password: "",
      logoUrl: "",
      registerForm: {
        phone: "",
        password: "",
        confirmPassword: ""
      },
      verifyCode: '',
      verifyCodeInput: '',
      isAgree: false,
      accountError: '',
      passwordError: '',
      verifyCodeError: '',
      agreementError: '',
      registerPhoneError: '',
      registerPasswordError: '',
      confirmPasswordError: '',
    };
  },

  mounted() {
    this.generateVerifyCode();
  },

  methods: {
    switchForm() {
      this.formItem = this.formItem === 1 ? 2 : 1;
      this.clearForm();
      this.generateVerifyCode();
    },

    clearForm() {
      this.account = "";
      this.password = "";
      this.registerForm = {
        phone: "",
        password: "",
        confirmPassword: ""
      };
      this.verifyCodeInput = "";
      this.isAgree = false;
      this.accountError = '';
      this.passwordError = '';
      this.verifyCodeError = '';
      this.agreementError = '';
      this.registerPhoneError = '';
      this.registerPasswordError = '';
      this.confirmPasswordError = '';
    },

    handleAgreementChange(e) {
      this.isAgree = e.detail.value.length > 0;
      this.agreementError = this.isAgree ? '' : '请阅读并同意用户协议和隐私政策';
    },

    openAgreement(type) {
      const url = type === 'user' ? '/pages/agreement/user' : '/pages/agreement/privacy';
      uni.navigateTo({
        url
      });
    },

    generateVerifyCode() {
      const characters = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
      let code = '';
      for (let i = 0; i < 4; i++) {
        code += characters.charAt(Math.floor(Math.random() * characters.length));
      }
      this.verifyCode = code;
    },

    validateVerifyCode() {
      if (!this.verifyCodeInput) {
        this.verifyCodeError = "验证码不能为空";
      } else if (this.verifyCodeInput.toLowerCase() !== this.verifyCode.toLowerCase()) {
        this.verifyCodeError = "验证码错误";
        this.generateVerifyCode();
      } else {
        this.verifyCodeError = "";
      }
    },

    validateAccount() {
      if (!this.account) {
        this.accountError = "手机号不能为空";
      } else if (!/^1[3-9]\d{9}$/.test(this.account)) {
        this.accountError = "请输入正确的手机号";
      } else {
        this.accountError = "";
      }
    },

    validatePassword() {
      if (!this.password) {
        this.passwordError = "密码不能为空";
      } else if (this.password.length < 6) {
        this.passwordError = "密码长度不能少于6位";
      } else {
        this.passwordError = "";
      }
    },

    validateRegisterPhone() {
      if (!this.registerForm.phone) {
        this.registerPhoneError = "手机号不能为空";
      } else if (!/^1[3-9]\d{9}$/.test(this.registerForm.phone)) {
        this.registerPhoneError = "请输入正确的手机号";
      } else {
        this.registerPhoneError = "";
      }
    },

    validateRegisterPassword() {
      if (!this.registerForm.password) {
        this.registerPasswordError = "密码不能为空";
      } else if (this.registerForm.password.length < 6) {
        this.registerPasswordError = "密码长度不能少于6位";
      } else {
        this.registerPasswordError = "";
      }
    },

    validateConfirmPassword() {
      if (!this.registerForm.confirmPassword) {
        this.confirmPasswordError = "请确认密码";
      } else if (this.registerForm.confirmPassword !== this.registerForm.password) {
        this.confirmPasswordError = "两次输入的密码不一致";
      } else {
        this.confirmPasswordError = "";
      }
    },

    async handleSubmit() {
      this.validateAccount();
      this.validatePassword();
      this.validateVerifyCode();
	  if(!this.isAgree){
		  this.$util.Tips({title: '请阅读并同意用户协议和隐私政策'})
		  return;
	  }
      // this.agreementError = this.isAgree ? '' : '请阅读并同意用户协议和隐私政策';

      if (this.formItem === 2) {
        this.validateRegisterPhone();
        this.validateRegisterPassword();
        this.validateConfirmPassword();
      }

      // if (this.accountError || this.passwordError || this.verifyCodeError || this.agreementError || this.registerPhoneError || this.registerPasswordError || this.confirmPasswordError) {
      //   return;
      // }

      if (this.formItem === 1) {
        // 登录逻辑
        uni.showLoading({ title: '登录中...' });
        try {
          const res = await loginH5({
            account: this.account,
            password: this.password,
          });
		  let data = res.data;
		  let newTime = Math.round(new Date() / 1000);
		  this.$store.commit("LOGIN", {
				'token': res.data.token
		  });
		  this.getUserInfo(data);
        } catch (error) {
          this.generateVerifyCode();
          uni.showToast({ title: error, icon: 'none' });
        } finally {
          uni.hideLoading();
        }
      } else {
        // 注册逻辑
        uni.showLoading({ title: '注册中...' });
        try {
          await loginMobile({
            phone: this.registerForm.phone,
            password: this.registerForm.password,
          });
		  uni.showToast({ title: '注册成功，请登录', icon: 'none' });
          this.formItem = 1;
		  this.clearForm();
        } catch (error) {
          console.error(error);
		  this.generateVerifyCode();
          uni.showToast({ title: error, icon: 'none' });
        } finally {
          uni.hideLoading();
        }
      }
    },
	
	getUserInfo(data){
		alert("11")
		alert(JSON.stringify(data))
		this.$store.commit("SETUID", data.uid);
		getUserInfo().then(res => {
			this.$store.commit("UPDATE_USERINFO", res.data);
			let backUrl = this.$Cache.get(BACK_URL) || "/pages/index/index";
			if (backUrl.indexOf('/pages/users/login/index') !== -1) {
				backUrl = '/pages/index/index';
			}
			// #ifdef APP  
				uni.reLaunch({
					url: "/pages/index/index"
				});
				return
			// #endif
			console.log(69999);
			console.log(backUrl);
			uni.reLaunch({
				url: backUrl
			});
		})
	}
  
  }
};
</script>
<style lang="scss" scoped>
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
	  font-size: 104rpx;  /* 进一步增大字体 */
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