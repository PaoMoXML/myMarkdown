#### 隐藏手风琴：
###### html:

```html
 <div role="accordion" opened="true" id="attachs">
            <div role="head" title="提交材料"></div>
            <div role="body">
                <div class="uc-baseattachment" id="baseattachment" data-options="{'showedit':1}"></div>

                <input id="stepguid" class="mini-hidden" bind="materialParam.stepguid"/>
                <input id="baserowguid" class=" mini-hidden" bind="materialParam.baserowguid"/>
                <input id="danweiguid" class=" mini-hidden" bind="materialParam.danweiguid"/>
                <input id="currentdanweiguid" class=" mini-hidden" bind="materialParam.currentdanweiguid"/>
            </div>
        </div>
```

###### js:
```js
    $.each($("div[role=accordion]"), function (i, accordion) {
        if ($(accordion).attr("id") === "attachs") {
            Util.accordion.hideItem(i)
        }
    })
```



#### js作为一个模块应用到另一个js中：

<img src="C:\Users\xml00\AppData\Roaming\Typora\typora-user-images\image-20220224142652477.png" alt="image-20220224142652477"  />
