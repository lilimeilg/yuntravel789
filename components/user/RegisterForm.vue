<template>
  <el-form :model="form" ref="form" :rules="rules" class="form">
    <el-form-item class="form-item">
      <el-input placeholder="用户名手机" v-model="form.username"></el-input>
    </el-form-item>

    <el-form-item class="form-item">
      <el-input placeholder="验证码" v-model="form.captcha">
        <template slot="append">
          <el-button @click="handleSendCaptcha">发送验证码</el-button>
        </template>
      </el-input>
    </el-form-item>

    <el-form-item class="form-item">
      <el-input placeholder="你的名字" v-model="form.nickname"></el-input>
    </el-form-item>

    <el-form-item class="form-item">
      <el-input placeholder="密码" type="password " v-model="form.password"></el-input>
    </el-form-item>

    <el-form-item class="form-item">
      <el-input placeholder="确认密码" type="password" v-model="form.checkPassword"></el-input>
    </el-form-item>

    <el-button class="submit" type="primary" @click="handleRegSubmit">注册</el-button>
  </el-form>
</template>

<script>
import { setTimeout } from 'timers';
export default {
  data() {
    // 确认密码
    const validatePass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.form.password) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };
    return {
      // 表单数据
      form: {
        username: "", // 登录用户名/手机
        password: "", // 登录密码
        checkPassword: "", // 确认密码
        nickname: "", // 昵称
        captcha: "" // 手机验证码
      },
      // 表单规则
      rules: {
        username: [
          {
            required: true,
            message: "请输入用户名",
            trigger: "blur"
          }
        ],
        password: [
          {
            required: true,
            message: "请输入密码",
            trigger: "blur"
          }
        ],
        checkPassword: [
          {
            validator: validatePass,
            trigger: "blur"
          }
        ],
        nickname: [
          {
            required: true,
            message: "请输入昵称",
            trigger: "blur"
          }
        ],
        captcha: [
          {
            required: true,
            message: "请输入验证码",
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    // 发送验证码
    handleSendCaptcha() {
      // 获取手机号码
      const phoneNumber = this.form.username;
      //  判断手机号码是否为空
      if (!phoneNumber.trim()) {
        // 弹窗提示
        this.$confirm("手机号码不能为空", "提示", {
          confirmButtonText: "确定",
          showCancelButton: false,
          type: "warning"
        });
        return;
      }
      // 调用action的方法，请求验证码
      this.$store.dispatch("user/sendCode", phoneNumber).then(res => {
        // 这里的res就是action里sendCode返回的code
        this.$confirm(`当前的验证码是：${res}`, "提示", {
          confirmButtonText: "确定",
          showCancelButton: false,
          type: "warning"
        });
      });
    },

    // 注册
    handleRegSubmit() {
      // console.log(this.form);
      this.$refs.form.validate(valid=>{
        if(valid){
          // 结构赋值，等下传递数据的时候要去除checkPassword，只需要传递props即可
          const{checkPassword,...props}=this.form
          // 方法一：直接写业务逻辑
          // 调用注册接口
          // this.$axios({
          //   url:"/accounts/register",
          //   method:"POST",
          //   data:props
          // }).then(res=>{
          //   console.log(res);
          //   //把数据保存到vuex
          //   this.$store.commit("user/setUserInfo",res.data)
          //   // 提示用户注册成功并跳转到首页
          //   this.$message({
          //     type:"success",
          //     message:"你已经注册成功，正在跳转到首页……"
          //   })
          //   setTimeout(()=>{
          //   this.$router.push("/")
          //   },1500)
          // })
          // 第二种方式，调用action的方法
          this.$store.dispatch("user/register",props).then(res=>{
            console.log(res);
             this.$message({
              type:"success",
              message:"你已经注册成功，正在跳转到首页……"
            })
            setTimeout(()=>{
            this.$router.push("/")
            },1500)
          })
        }
      })
    }
  }
};
</script>

<style scoped lang="less">
.form {
  padding: 25px;
}

.form-item {
  margin-bottom: 20px;
}

.form-text {
  font-size: 12px;
  color: #409eff;
  text-align: right;
  line-height: 1;
}

.submit {
  width: 100%;
  margin-top: 10px;
}
</style>