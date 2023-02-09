### CIM是什么

> 城市信息模型(City Information Modeling)
>
> 城市信息模型是以[建筑信息模型](https://baike.baidu.com/item/建筑信息模型/5034795)（BIM）、地理信息系统（GIS）、物联网（IoT）等技术为基础，整合城市地上地下、室内室外、历史现状未来多维多尺度信息模型数据和城市感知数据，构建起三维数字空间的城市信息有机综合体。

![img](https://bkimg.cdn.bcebos.com/pic/1f178a82b9014a909e71e000a0773912b21beec4?x-bce-process=image/watermark,image_d2F0ZXIvYmFpa2UyMjA=,g_7,xp_5,yp_5/format,f_auto)



**`BIM`+`gis`+`物联网` 合起来是`cim`**

#### CIM不可绕开的技术：数字孪生

> 把真实的城市运行映射到数字化虚拟世界里

---

### CIM的应用场景

1. CIM+渣土车
2. CIM+智慧工地
3. CIM+城管
4. CIM+质安监

### 目前住建BU对CIM的规划

- 将中央研究院的成果根据业务进一步扩展
- 产品CIM化

### 业内主要竞争对手

- 奥格

  - 广州市住房和城乡建设局2019年广州市城市信息模型（CIM）平台项目（CZ2019-1771）
  - 南京市运用建筑信息模型系统(BIM)进行工程建设项目审查审批和城市信息模型平台(CIM)建设试点项目——CIM平台（V1.0）

- 阿里巴巴

- 腾讯

  - [CityBase](https://citybase.qq.com/)

  > [BIM风云录 步步惊心！腾讯布局CIM平台剑指阿里_gisbim的博客-CSDN博客](https://blog.csdn.net/gisbim/article/details/106044001)

  

### CIM技术路线（重点）

> 总体：对二三维数据以及BIM数据进行高效管理发布与可视化分析，实现CIM高效三维引擎，BIM模型轻量化，服务一体化管理与部署。 

- [ArcGis](https://developers.arcgis.com/javascript/latest/)
  
  1. gis
  
     > 地理信息系统
  
  2. ArcGIS是由ESRI出品的一个地理信息系统系列软件的总称
     1. 桌面版本：以功能等级而区分的套件：ArcReader、ArcView、ArcEditor和ArcInfo，而高级的套件是较低级套件加上其他进阶功能。
  
     2. 服务器版本：以功能等级(基本、标准、进阶)而区分为ArcIMS (web mapping server), ArcGIS Server 与 ArcGIS Image Server。
  
        > ArcGIS Desktop――一个专业GIS应用的完整套件
        >
        > ArcGIS Engine――为定制开发GIS应用的嵌入式开发组件
        >
        > 服务端GIS――ArcSDE，ArcIMS和ArcGIS Server
        >
        > [移动GIS](https://baike.baidu.com/item/移动GIS/2189926)――[ArcPad](https://baike.baidu.com/item/ArcPad)?以及为平板电脑使用的ArcGIS Desktop和Engine
        >
        > ArcGIS是基于一套由共享GIS组件组成的通用组件库实现的，这些组件被称为ArcObjectsTM。
  
     3. **arcgis api for javascript，让我们在web应用中嵌入地图内容，这套api可以帮我们：**
  
        > 1. 把数据放到地图上，也就是可视化
        >
        > 2. 可以在地图上找你关注的数据，也就是空间查询
        > 3. 可以帮助你分析数据的空间分布模式、规律，也就是常说的空间分析
  
  3. GeoScenePro
  
     > 对数据进行可视化、编辑、分析，可以同时在2D和3D中制作内容，并发布为要素服务、地图服务、分析服务和3D Web场景等。
  
- [Cesium](https://blog.csdn.net/u011365716/article/details/94591358?spm=1001.2014.3001.5502)
  
  1. Cesium是一个跨平台、跨浏览器的展示三维地球和地图的 javascript 库
  2. Cesium使用WebGL 来进行硬件加速图形，使用时不需要任何插件支持，但是浏览器必须支持WebGL;
  3. Cesium是基于Apache2.0 许可的开源程序。它可以免费的用于商业和非商业用途。
  
- webGL *特效渲染*

  > [Three.js](https://techbrood.com/threejs/docs/)
  >
  > WebGL可以看成是浏览器给我们提供的接口，在javascript中可以直接用这些API进行3D图形的绘制；而Three.js就是在这些接口上又帮我们封装得更好用一些。

- 

- 

### ~~我们研发侧应该做什么？~~

1. 



### 名词解释

1. 注记：地图上起说明作用的各种文字

2. 要素：是空间数据最基本、不可分割的单位，有点、线、面(多边形)等，可根据应用需要，用点状符号、线型、面状填充图案加边界线表达  （就是点、线、面、符号）

3. 底图：贴在地表上的图层（最底下的图层）

4. 瓦片：

   | 瓦片     | 优势                                                         | 劣势                                                         | 示例图                                                       |
   | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | 矢量瓦片 | 瓦片占用空间低，瓦片切图效率高，渲染地图效果快，可以随时动态调整地图样式，地图分辨率高。 | 对客户端性能要求比较高，对旧设备兼容性存在问题。             | ![矢量瓦片](https://ts1.cn.mm.bing.net/th?id=OIP-C.E1-ZXNrF20-XdSzcRa8lXwAAAA&w=182&h=170&c=8&rs=1&qlt=90&o=6&dpr=1.2&pid=3.1&rm=2) |
   | 栅格瓦片 | 瓦片提前渲染，对客户端性能要求低，性能稳定。                 | 瓦片占用空间高，瓦片切图效率低，无法随时动态调整地图样式，地图分辨率低，加载速度比较慢。 | ![栅格瓦片](https://img-blog.csdnimg.cn/20190505211052428.png) |

   

5. 图层服务：在底图上加的图层

6. 白膜服务：白膜

7. 天地图：底图服务

8. 高程：海拔

9. 纹理贴图：贴在白膜上的内容（本质是图片）

10. BIM模型：建筑信息模型（Building Information Modeling，由很多构件组成的一栋建筑

11. 构件：BIM模型中的组件

12. *切片图层：？*

13. *场景图层：是否是？类似于地球未贴图之前的那个球？*

14. 要素图层：点线面的图层（使用shp数据，白膜图层在未3d化之前就是2d的要素图层）

12. 