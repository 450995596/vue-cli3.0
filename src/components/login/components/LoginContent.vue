<template>
  <div class="content">
    <input type="text" placeholder="请输入账号" v-model="phone">
    <input type="password" placeholder="请输入密码" v-model="password">
    <div class="login" @click="login">登录</div>  
  </div>
</template>

<script>
import router from "@/router";
export default {
  data() {
    return {
      phone: "",
      password: ""
    };
  },
  methods: {
    async login() {
      if (!this.phone || !this.password) {
        this.$toasted.error("请输入完善信息", { icon: "error" }).goAway(2000);
        return;
      }
      try {
        // await等待一个异步返回的结果 如果没有await 会报user is undefined 获取不到
        let res = await this.http.post("/api/login", {
          username: this.phone,
          password: this.password
        });
        if (res.code == 200) {
          this.$store.commit("loginbanner", res.data.banners);
          this.$store.dispatch("login", res.data.user);
          this.$toasted.success("登录成功").goAway(1500);
          this.$router.replace({ name: "home" });
        } else {
          this.$toasted.error(res.message, { icon: "error" }).goAway(2000);
        }
      } catch (error) {
        this.$toasted.error(error.message, { icon: "error" }).goAway(2000);
      }
    }
  }
};
</script>

<style scoped>


.problem {
  width: 100%;
  height: 0.44rem;
  display: flex;
  justify-content: space-between;
}
.problem span {
  width: 1.2rem;
  height: 0.44rem;
  line-height: 0.44rem;
  text-align: center;
  font-size: 0.24rem;
}
</style>