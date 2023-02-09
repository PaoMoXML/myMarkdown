## 房普项目用到的Smartbi内容

---



### 1. 扩展包添加

安装smartbi后需要添加一个扩展包，让smartbi支持JSON查询方式

![image-20211123092727665](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/image-20211123092727665.png)

> 扩展包添加方式详见：[新点知识门户 (JSON数据源)](https://fdoc.epoint.com.cn/onlinedoc/docshow/docshow?columnguid=031&menuguid=031001&nodeguid=5493f9f9-319c-45dd-8aaa-0858867406ab)

### 2. 完善JAVA数据连接

1. 填写请求地址，请求方式
2. 添加请求内容类型 `如果选择JSON 请求内容：{ "province":"#{province}"}`
3. 点击测试连接，显示测试结果 ，并选择数据标签`测试结果应是json格式：{key：jsonarray}，数据标签就是这个key`
4. 点击设置输出字段
5. 点击获取参数与结果集，随后保存即可

![p1](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/p1.png)

###  3. 添加数据集

1. 创建数据集
2. 选择上一步完成的数据连接
3. 进入数据集后选择左侧需要的字段拖入右侧数据显示框中

![image-20211123094915170](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/image-20211123094915170.png)

4. 点击预览可检查数据是否可以获取到`如果没有查询参数，直接保存数据集即可，如有则看下一步`

![image-20211123095210314](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/image-20211123095210314.png)

5. 创建自定义参数`以省份选择为例`
   1. 新建一个自定义参数并选择数据源，选择合适的数据类型和操作类型，点击下一步`只可以选择数据库类型`
   1. 备选值设置对应查询省份的sql，默认值用静态列表，点击下一步
   1. 选择合适的实际值和显示值，点击保存
   1. 回到数据集页面，点击下方的绑定参数，绑定创建的自定义参数

![image-20211123100012710](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/image-20211123100012710.png)

![image-20211123100952956](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/image-20211123100952956.png)

![image-20211123101110742](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/image-20211123101110742.png)

![image-20211123101206832](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/image-20211123101206832.png)

### 4. 使用excel完成报表

1. 插件安装

   > [SmartBI电子表格插件的安装](https://wiki.smartbi.com.cn/pages/viewpage.action?pageId=44501747)

2. 安装并登陆smartbi，选择到创建的数据集，将字段拖入excel中

![image-20211123102640830](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/image-20211123102640830.png)

3. 创建完成报表后点击预览就可预览报表效果，点击发布就可发布到服务器，发布后就可以获取报表url，将报表直接嵌入到需要的地方

![image-20211123102851530](https://gitee.com/nagiyoru/pic_bed/raw/master/typora/image-20211123102851530.png)

### TIPS

- 常用链接

> [新点知识门户 (Smartbi文档，常见问题处理)](https://fdoc.epoint.com.cn/onlinedoc/docshow/docshow?menuguid=031001&nodeguid=8484d051-2a57-4b73-885c-1cc5b68bc53d)
>
> [SmartbiWiki(修改Smartbi图标、title等、用户下拉内容)](https://history.wiki.smartbi.com.cn/pages/viewpage.action?pageId=48563052)
>
> [首页下方内容修改](https://wiki.smartbi.com.cn/pages/viewpage.action?pageId=76678327)

http://ip:port/epointbi/vision/openresource.jsp?resid=

