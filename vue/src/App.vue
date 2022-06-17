<template>
  <div class="container">
    <template v-if="token===null">
      <div style="height: 80px">
        <h2>登录</h2>
        <p v-if="ErrMsg!==null" style="color: orangered">{{ErrMsg}}</p>
      </div>

      <a-input v-model:value="username" placeholder="input username">
        <template #prefix>
          <user-outlined type="user" />
        </template>
      </a-input>
      <a-input-password v-model:value="password" placeholder="input password" />
      <a-button @click="submit"> submit  </a-button>
    </template>
    <template v-else>
        <h2 style="color: #42b983">登录成功!</h2>
        <a-button @click="logout"> Log out  </a-button>
    </template>

  </div>
</template>

<script>
import { Button, Input ,InputPassword} from 'ant-design-vue'
import { ref } from 'vue';


export default {
  name: 'App',
  components: {
    AButton: Button,
    AInput: Input,
    AInputPassword: InputPassword
  },
  setup(){
    const username = ref('')
    const password = ref('')
    const ErrMsg = ref(null) // 错误信息
    const token = ref(localStorage.getItem('token')||null) // token

    // 提交登录表单
    function submit (){

      fetch('http://127.0.0.1:7001/api/user/login',{
        method: 'POST', // or 'PUT'
        body: JSON.stringify({
          username: username.value,
          password: password.value
        }), // data can be `string` or {object}!
        headers: new Headers({
          'Content-Type': 'application/json'
        })
      }).then(ref=>ref.json()).then(ref=>{
        switch (ref.code) {
          case 200:
            ErrMsg.value = null
            token.value = ref.data.token
            localStorage.setItem('token',ref.data.token)
            break;
          case 400:
            ErrMsg.value = ref.message
            break;
        }
      })
    }

    function logout (){
      token.value = null
      localStorage.clear('token')
    }

    return { username,password,submit, ErrMsg ,token, logout}
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.container{
  width: 500px;
  height: 450px;
  display: flex;
  flex-direction: column;
  align-content: space-between;
  justify-content: space-between;
  border: 1px #2c3e50 solid;
  padding: 100px 60px;
  border-radius: 10px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
}
</style>
