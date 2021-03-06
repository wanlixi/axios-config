# axios-config
axios的一些配置

## axios 的初始化配置
### src/api/axios-config.js

```
import axios from 'axios'
import qs from 'qs'

axios.defaults.timeout = 5000;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded;charset=UTF-8';
// axios.defaults.baseURL = 'http://192.168.1.1:8088/dev'; // dev
// axios.defaults.baseURL = 'http://192.168.1.1:8088/test'; // test
axios.defaults.baseURL = 'http://192.168.1.1:8088/product'; // product

//POST传参序列化
axios.interceptors.request.use((config) => {
    if(config.method  === 'post'){
        config.data = qs.stringify(config.data);
    }
    return config;
},(error) =>{
    return Promise.reject(error);
});

//返回状态判断
axios.interceptors.response.use((res) =>{
    if(res.status !== 200){
        return Promise.reject(res);
    }
    return res;
}, (error) => {
    return Promise.reject(error);
});

// 对axios二次封装
const fetch = (url, method, data) => {
    return new Promise((resolve, reject) => {
        axios({url, method: method, data})
            .then(response => {
                resolve(response.data);
            }, err => {
                reject(err);    
            })
            .catch((error) => {
               reject(error)
            })
    })
};

export default fetch;
```
## 接口文件
### src/api/index.js

```
immport fetch from './axios-config'

export default {
  getUserInfo (userId) {
    return fetch('/user?userId=${userId}')
  },
  ...
}
```

## 全局注入
### src/main.js

```
import api from './api'
import Vue from 'vue'
Vue.prototype.$api = api
```

## 使用实例：
### src/module/index.vue

```
<template>

</template>
<script type="text/babel">
export default {
  data () {
    return {
        userId: 10,
        userInfo: {}
    }
  },
  mounted () {
    this.$api.getUserInfo(this.userId)
        .then( res => {
            this.userInfo = res.data
        })
        .catch( err => {
            //
        })
  }
}
</script>
```
