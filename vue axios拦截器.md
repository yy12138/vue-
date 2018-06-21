当我们访问某个地址页面时，有时会要求我们重新登录后再访问该页面，也就是身份认证失效了，如token丢失了，或者是token依然存在本地，但是却失效了，所以单单判断本地有没有token值不能解决问题。此时请求时服务器返回的是401错误，授权出错，也就是没有权利访问该页面。 
我们可以在发送所有请求之前和操作服务器响应数据之前对这种情况过滤。

```
// JWT 用户权限校验，判断 TOKEN 是否在 localStorage 当中
router.beforeEach(({name}, from, next) => {
  // 获取 JWT Token
  if (localStorage.getItem('JWT_TOKEN')) {
    // 如果用户在login页面
    if (name === 'login') {
      next('/');
    } else {
      next();
    }
  } else {
    if (name === 'login') {
      next();
    } else {
      next({name: 'login'});
    }
  }
});


// http request 拦截器
axios.interceptors.request.use(
  config => {
    if (localStorage.JWT_TOKEN) {  // 判断是否存在token，如果存在的话，则每个http header都加上token
      config.headers.Authorization = `token ${localStorage.JWT_TOKEN}`;
    }
    return config;
  },
  err => {
    return Promise.reject(err);
  });

// http response 拦截器
axios.interceptors.response.use(
  response => {
    return response;
  },
  error => {
    if (error.response) {
      console.log('axios:' + error.response.status);
      switch (error.response.status) {
        case 401:
          // 返回 401 清除token信息并跳转到登录页面
          store.commit('LOG_OUT');
          router.replace({
            path: 'login',
            query: {redirect: router.currentRoute.fullPath}
          });
      }
    }
    return Promise.reject(error.response.data);   // 返回接口返回的错误信息
  });

Vue.prototype.$http = axios;
```
