<template>
    <div class="box">
        <van-nav-bar
            title="Mc聊天室"
            left-text="返回"
            right-text=""
            left-arrow
            />
        <span class="iconfont icon-tips-add-friend"></span>
         <ul>
            <li v-for="(item,index) of list" :key="index">
                <div :class="item.flag">
                  <!-- {{item.username}}  -->
                   {{item.msg}}
                </div>
            </li>
        </ul>
     <van-cell-group>

    <van-field
        v-model="msg"
        center
        clearable 
        placeholder="请输入内容"
        @keyup.enter="websocketsend"
    >

    <van-button slot="button" size="small" type="primary" @click="websocketsend">发送</van-button>
  </van-field>
</van-cell-group>

    <van-dialog
  v-model="show"
  show-cancel-button
  :before-close='beforeClose'
  @cancel='close'
  @confirm='confirm'
>
  <van-field
    v-model="username"
    label="用户名"
    placeholder="请输入用户名"
  />  
</van-dialog>



    </div>
</template>


<script>
import { Toast } from 'vant'
export default {
    name: "test",
    data() {
        return {
            data: "",
            msg:'',
            websock: null,
            list:[],
            flag:2,
            username:'',
            show:true,
            id:2,
        };
    },
    　　　methods: { 
            confirm(){
                this.id=Math.floor(Math.random()*(1,1000)+1000)
                this.initWebpack();
            },
            close(){
                
            },     
             beforeClose(action, done) {
                    if (action === 'confirm') {
                        if(!this.username){
                            done(false);
                        }else{
                            done();
                        }                    
                    } else {                     
                        done(false);
                    }
            },          
            initWebpack(){//初始化websocket
                const wsuri = "ws://192.168.0.188:8282";
                this.websock = new WebSocket(wsuri);//这里面的this都指向vue
                this.websock.onopen = this.websocketopen;
                this.websock.onmessage = this.websocketonmessage;
                this.websock.onclose = this.websocketclose;
                this.websock.onerror = this.websocketerror;
            },
            websocketopen(){//打开
                 console.log("WebSocket连接成功")              
                // this.websock.send(`login:${this.username}`)    
                this.websock.send(JSON.stringify({username:this.username,id:this.id,type:'init'}))       
            },
            websocketonmessage(e){ //数据接收 
            let res;   
                try{
                    res=JSON.parse(e.data)
                } catch(err){
                    console.log(e.data)                 
                }                  
              
                if(res!='undefined'){
                      console.log(res)
                     if(res.type=='chatMessage'){
                    if(res.data.id!=this.id){
                        this.list.push({msg:res.data.content,flag:'left',username:res.data.username})
                    }else{
                        // this.list.push({msg:res.data.content,flag:'right'})
                    }
                }
                }
               

            },
            websocketsend(agentData){//数据发送 
                if(!this.msg){
                    Toast('不输内容你发个球');
                    return
                }
            // this.http.get('http://www.tuling123.com/openapi/api',{
            //     params:{
            //         key: "fa7f4d06b0a24b479d29ea0a01672350",
            //         info:this.msg
            //     }
            // }).then(res=>{
            //     this.list.push({msg:res.text,flag:'left'})
            // })
                let data={
                    mine:{
                        id:this.id,
                        username:this.username,
                        content:this.msg,
                        mine:true
                    },
                    to:{
                        type:'group',
                        id:this.id,
                        username:this.username
                    }
                }
　　　　　　　　  this.websock.send(JSON.stringify({type:'chatMessage',data:data})); 
                this.list.push({msg:this.msg,flag:'right'})
                this.msg='';
　　　　　　}, 
            websocketclose(){  //关闭
                console.log("WebSocket关闭");
            },
            websocketerror(){  //失败
                console.log("WebSocket连接失败");
            },　　　　　
　　　},
    mounted() {
        
    }
};
</script>

<style lang="scss" scoped>
.box {
    .iconfont {
        color: white;
    }
}
ul{
    margin-bottom: 6.25rem;
}
li{
    color: red;
    overflow: hidden;
    margin-bottom: .2rem;  
    &>div{
      max-width: 50%;   
    }
   
}
.van-cell-group{
    position: fixed;
    bottom: 0;
    width: 100%;
}
 .left{
        float: left;       
    }
.right{
    float: right;

    text-align: right;   
}
</style>
