<template>
  <div class="contain_wrap">
    <div class="contain">
      <div class="login">
        <div class="login_title">
          <span>注册或登录</span>
        </div>
        <div class="login_fields">
          <div class="login_fields__user">
            <div class="icon">
              <img mode="scaleToFill" src="/static/images/img/user_icon_copy.png">
            </div>
            <input placeholder="邮箱" type="text" v-model="email">
            <div class="validation"></div>
          </div>
          <div class="login_fields__password">
            <div class="icon">
              <img src="/static/images/img/lock_icon_copy.png">
            </div>
            <input placeholder="至少6位的密码" type="password" v-model="password">
            <div class="validation"></div>
          </div>
          <div class="login_fields__submit">
            <button @click="handleLogin">登录</button>
            <div class="forgot"></div>
          </div>
        </div>
        <div class="disclaimer"></div>
      </div>
    </div>
  </div>
</template>

<script>
import card from "@/components/card";
import Flyio from "flyio/dist/npm/wx";
const flyio = new Flyio();
export default {
  data() {
    return {
      email: "",
      password: ""
    };
  },

  components: {
    card
  },

  methods: {
    handleLogin() {
      let email = this.email;
      let password = this.password;
      let email_pattern = /^([A-Za-z0-9_\-\.])+\@([A-Za-z0-9_\-\.])+\.([A-Za-z]{2,4})$/;
      if (email_pattern.test(email) && /\w{6,}/.test(password)) {
        wx.showLoading({
          title: "登录中...",
          mask: true
        });
        flyio
          .post(`${HOST}/api/v1/signup`, {
            email,
            password
          })
          .then(res => {
            wx.hideLoading();
            let { data: result } = res;
            if (result.code < 400) {
              result.data.login_time = new Date().getTime();
              wx.setStorageSync("user_info", result.data);
              wx.showToast({
                title: "登录成功",
                icon: "none",
                duration: 2000,
                mask: true
              });
              wx.redirectTo({
                url: "/pages/wordlist/main"
              });
            } else {
              wx.showToast({
                title: result.message || "登陆失败",
                icon: "none",
                duration: 2000,
                mask: true
              });
            }
          });
      } else {
        wx.showToast({
          title: "请输入正确的邮箱或输入6位以上密码",
          icon: "none",
          duration: 2000,
          mask: true
        });
      }
    }
  },
  onLoad() {
    let user_info = wx.getStorageSync("user_info");
    console.log(user_info);
    if (user_info && user_info.token) {
      let now = new Date().getTime();
      if (now - user_info.login_time < 1000 * 60 * 60 * 24 * 7) {
        wx.redirectTo({
          url: "/pages/wordlist/main"
        });
      }
    }
  },
  created() {}
};
</script>

<style scoped>
input {
  font-size: 16px;
}
.contain_wrap {
  width: 100vw;
  border: 1px solid #ccc;
}

.contain {
  height: 100vh;
  margin: 0;
  overflow: hidden;
  font-family: "Gudea", sans-serif;
  background: linear-gradient(135deg, #ea5c54 0%, #bb6dec 100%);
}

.contain ::-webkit-input-placeholder {
  color: #4e546d;
}

.contain p {
  color: #5b5e6f;
  font-size: 10px;
  text-align: left;
}

.contain .testtwo {
  left: -320px !important;
}

.contain .test {
  box-shadow: 0px 20px 30px 3px rgba(0, 0, 0, 0.55);
  pointer-events: none;
  top: -100px !important;
  -webkit-transform: rotateX(70deg) scale(0.8) !important;
  transform: rotateX(70deg) scale(0.8) !important;
  opacity: 0.6 !important;
  -webkit-filter: blur(1px);
  filter: blur(1px);
}

.contain .login {
  opacity: 1;
  top: 20px;
  -webkit-transition-timing-function: cubic-bezier(0.68, -0.25, 0.265, 0.85);
  -webkit-transition-property: -webkit-transform, opacity, box-shadow, top, left;
  transition-property: transform, opacity, box-shadow, top, left;
  -webkit-transition-duration: 0.5s;
  transition-duration: 0.5s;
  -webkit-transform-origin: 161px 100%;
  -ms-transform-origin: 161px 100%;
  transform-origin: 161px 100%;
  -webkit-transform: rotateX(0deg);
  transform: rotateX(0deg);
  width: 240px;
  border-top: 2px solid #d8312a;
  height: 300px;
  position: absolute;
  left: 0;
  right: 0;
  margin: 0 auto;
  top: 60px;
  bottom: 0;
  padding: 100px 40px 40px 40px;
  background: linear-gradient(45deg, #1f222e 0%, #35394a 100%);
}

.contain .login .validation {
  position: absolute;
  z-index: 1;
  right: 10px;
  top: 6px;
  opacity: 0;
}

.contain .login .disclaimer {
  position: absolute;
  bottom: 40px;
  left: 35px;
  width: 250px;
}

.contain .login_title {
  color: #afb1be;
  height: 60px;
  text-align: left;
  font-size: 18px;
}

.contain .login_fields {
  height: 208px;
  position: absolute;
  left: 0;
}

.contain .login_fields .icon {
  position: absolute;
  z-index: 1;
  left: 36px;
  top: 10px;
  opacity: 0.5;
}
.icon > img {
  width: 14px;
  height: 14px;
}

.contain .login_fields input[type="password"] {
  color: #dc6180 !important;
}

.contain .login_fields input[type="text"],
.contain .login_fields input[type="password"] {
  color: #afb1be;
  width: 190px;
  margin-top: -2px;
  background: #32364a;
  left: 0;
  padding: 10px 65px;
  border-top: 2px solid #393d52;
  border-bottom: 2px solid #393d52;
  border-right: none;
  border-left: none;
  outline: none;
  font-family: "Gudea", sans-serif;
  box-shadow: none;
}

.contain .login_fields__user,
.contain .login_fields__password {
  position: relative;
}

.contain .login_fields__submit {
  position: relative;
  top: 80px;
  left: 0;
  width: 80%;
  right: 0;
  margin: auto;
}

.contain .login_fields__submit .forgot {
  float: right;
  font-size: 10px;
  margin-top: 11px;
}

.contain .login_fields__submit .forgot a {
  color: #606479;
}

.contain .login_fields__submit button {
  border-radius: 50px;
  background: transparent;
  padding: 5px 10px;
  border: 2px solid #dc6180;
  color: #dc6180;
  text-transform: uppercase;
  font-size: 16px;
  -webkit-transition-property: background, color;
  transition-property: background, color;
  -webkit-transition-duration: 0.2s;
  transition-duration: 0.2s;
}

.contain .login_fields__submit input:focus {
  box-shadow: none;
  outline: none;
}

.contain .login_fields__submit input:hover {
  color: white;
  background: #dc6180;
  cursor: pointer;
  -webkit-transition-property: background, color;
  transition-property: background, color;
  -webkit-transition-duration: 0.2s;
  transition-duration: 0.2s;
}

/* Color Schemes */
.love {
  position: absolute;
  right: 20px;
  bottom: 0px;
  font-size: 11px;
  font-weight: normal;
}

.love p {
  color: white;
  font-weight: normal;
  font-family: "Open Sans", sans-serif;
}

.love a {
  color: white;
  font-weight: 700;
  text-decoration: none;
}

.love img {
  position: relative;
  top: 3px;
  margin: 0px 4px;
  width: 10px;
}

.brand {
  position: absolute;
  left: 20px;
  bottom: 14px;
}

.brand img {
  width: 30px;
}

.disclaimer a {
  color: #5b5e6f;
}

.qrcode_wrap {
  width: 120px;
  height: 120px;
  overflow: hidden;
  display: none;
  opacity: 0.8;
}
.qrcode_wrap > img {
  display: block;
  width: 100%;
}
</style>
