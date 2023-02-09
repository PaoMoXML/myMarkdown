## 组件变换单位（或其他配置）
![img](https://docimg10.docs.qq.com/image/qgxXix9ynhld0vqyiX9kdg.png?w=582&h=354) 

> 像这样的万用图组件无法自动改变单位，只能配置一个固定的Y轴单位
> 但是可以通过自定义事件，根据条件改变组件的配置

处理步骤如下：
1. 在组件中添加自定义事件，事件类型根据自己的需求配置
![img](https://docimg1.docs.qq.com/image/CIbVTC4KxFm3CyB7MH9hBw.png?w=398&h=499)

2. 添加条件，在满足条件后就会执行配置操作，条件字段需要在data中
![img](https://docimg7.docs.qq.com/image/OmmqkG2YaWPszz8uzCiUyA.png?w=476&h=389)

3. 配置改变后的样式（组件配置中设置新的单位，或者其他样式配置...）
![img](https://docimg4.docs.qq.com/image/G4L6491NlzHZeiu2l-FY6Q.png?w=405&h=346) 

---

## 页面排序切换
实现多个列的排序需要由三部分组成
1. ![img](https://docimg9.docs.qq.com/image/LSAb09tH71E7KtE8jEJjJg.png?w=1071&h=281) 
  > 两个排序的按钮，第三个选项卡用来判断点击的是哪个列的排序

2. ![img](https://docimg5.docs.qq.com/image/-lxNjiZMCRrPhuuA_cCy4w.png?w=1179&h=628)
  > 给排序按钮添加回调参数，参数用来区分排序是否被启用

3. ![img](https://docimg7.docs.qq.com/image/Rxpaw-HmwdNUXXHX2nsaPw.png?w=1280&h=411.3993610223642) 
  > 给选项卡也添加参数以及回调，由于选项卡有两个列，分别对应了两个按钮，当排序按钮被点击时选项卡也会传出回调参数，就可以用来判断点击的列

4. <img src="https://docimg4.docs.qq.com/image/IupT4FN0MIvJfagMtmaIdA.png?w=465&h=118" alt="img" style="zoom:150%;" />
  > 最后在你需要排序的表中添加过滤器来接收参数并排序

```js
if(callbackArgs.s === "1" && callbackArgs.xzqh.length == 0){
  data.sort(function(a, b){
    return parseInt(b.areacode) - parseInt(a.areacode)
  })
}
else if(callbackArgs.s === "1" && callbackArgs.xzqh.length != 0 && callbackArgs.xzqh[0] === "xzqh"){
  data.sort(function(a, b){
    return parseInt(a.areacode) - parseInt(b.areacode)
    })
}
else if(callbackArgs.s === "2"&& callbackArgs.sz.length == 0){
   data.sort(function(a, b){
    return parseInt(b.value) - parseInt(a.value)
  })
}
else if(callbackArgs.s === "2"&& callbackArgs.sz.length != 0 && callbackArgs.sz[0] === "sz"){
  data.sort(function(a, b){
    return parseInt(a.value) - parseInt(b.value)
    })
}
return data;
```

---

## 页面组件控制显隐
> 显隐主要是通过自定义事件来实现，可以通过传递参数或者点击按钮的方式控制，动作除了显示隐藏还可选择其他的

![img](https://docimg9.docs.qq.com/image/VtOkI7xJYd9oJoXLG0VlTA.png?w=329&h=522)   ![img](https://docimg4.docs.qq.com/image/McuwyFVA27Xp5ZlgHT4AGA.png?w=308&h=246)        

> 当选择事件类型为`当请求完成会数据变化时`需要填入对应条件 

![img](https://docimg1.docs.qq.com/image/U7KCorCvNSyF6Z41t2t8Eg.png?w=381&h=133) 

> 条件可以从别的组件或者外部传入，通过过滤器接收字段后组装进`data`中，在自定义事件中设置对应满足条件，满足条件后就会执行隐藏或者显示



如果该组件没有自定义事件，那就用另一个有自定义事件的组件来控制这个组件显隐



##  袋鼠云与传统页面通信（iframe中的页面）

![image-20221031144736696](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20221031144736696.png)

回调：接收别的组件传来的值并传给iframe，iframe中页面的js中添加如下代码接收

```javascript
 window.addEventListener(
        'message',
        (e) => {
            const {data} = e;
            //输出效果为json {text:xxx}
            console.log(data);
        })
```

传出回调参数：iframe中添加如下代码，来传输值给袋鼠云

```javascript
 var search = {
            areacode: areacode,
            buildtime: buildtime,
            close: isClose,
            closesjzc: closeSJZC,
            closeywyy: closeYWYY
        };
        window.parent.postMessage(JSON.stringify(search), '*');
```







## TIPS



> 名词解释
>
> 1. 隐藏卸载-->相当于js中将DOM remove
> 2. 

> 袋鼠云好处
>
> 1. 相对于传统页面，配置袋鼠云接口时不需要理解`js`，只需要返回固定格式的数据即可。
> 2. 页面bug少，问题基本就是后台数据问题或者配置不正确`前提是交互不复杂`



> 袋鼠云问题
>
> 1. 如果需要和传统页面交互，而且交互复杂，简直就是灾难 `非常容易出现问题且十分难定位问题！`，配置很多bug难改。
>
>    1. ==袋鼠云页面多个地方的`点击事件`需要传给`iframe（让iframe确定袋鼠云点击了哪里）`，可以将这几个点击事件的回调字段名全部设置为相同==
> - 如果回调字段名设置为不同，则无法判断具体点击了哪个按钮
>       
> - 如果设置为相同，一旦出现问题就很难改
>       
> - 问题可能是：各种按钮胡乱触发。==可以尝试拆分问题组件来解决问题（不推荐）==
> 2. 如果过滤器中接收的回调参数值和原来的值一样，组件配置的自定义事件就是满足条件也不会触发 （回调也不触发）
>
>      1. 假设从**传统页面**传值到**袋鼠云页面**，*Close*传值为true：隐藏`袋鼠云页面1`，false：显示。此时展示的为`袋鼠云页面1`
>      2. **传统页面**传值*Close*=true给**袋鼠云页面**,`袋鼠云页面1`隐藏
>      3. 袋鼠云中切换页面，从`袋鼠云页面1`切换到`袋鼠云页面2`，这中间是由袋鼠云自己控制了显隐，所以Close值不变，`页面1`隐藏，`页面2`显示
>      4. 再次切换回到`袋鼠云页面1`，袋鼠云控制`袋鼠云页面1`显示`袋鼠云页面2`隐藏，这时*Close*的值仍然不变为true，但`页面1`却为显示状态的
>      5. **传统页面**再次想要`袋鼠云页面1`隐藏，传值*Close*=true给**袋鼠云页面**
>      6. ==虽然传值了，但是Close的值没有变，袋鼠云不会触发自定义事件，即使值满足条件 --> 也就是说页面不会隐藏（想办法让值变得不同=>如果是选项卡可以设置一个默认值，当选择其他内容的时候让该选项卡变为默认值）==
> 3. 如果用到引用面板每次修改都得重新配置
