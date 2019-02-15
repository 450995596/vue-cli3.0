<template>
  <div>
    <lg-header></lg-header>
    <lc-conent></lc-conent>

    <div class="test">
      这是一个div
    </div>
  </div>
</template>

<script>
import LgHeader from "./components/LoginHeader";
import LcConent from "./components/LoginContent";
export default {
  components: {
    LgHeader,
    LcConent
  },
  created() {
      
  },
  mounted(){
    
     this.http.get('http://mobile.ximalaya.com/mobile/v1/album/track?').then(res=>{
                console.log(res)
       })
    
  }
};
</script>

<style lang='scss' scoped>
    
    
</style>