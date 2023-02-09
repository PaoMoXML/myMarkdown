### 输入`vue create model-proj`创建Vue项目

<img src="C:\Users\xml00\AppData\Roaming\Typora\typora-user-images\image-20220315141201347.png" alt="image-20220315141201347"  />

### element-ui安装

> npm i element-ui -S

```typescript
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
```



### axios安装

> npm install axios

```typescript
import axios from 'axios';
// 启用网络请求插件
Vue.prototype.$axios = axios;
```

