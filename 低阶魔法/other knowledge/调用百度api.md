 https://blog.csdn.net/u010251278/article/details/52877370

以下内容转自上述网站，为了以后的学习方便，为此才特地将该网站内容转到自己的博客，多谢博主，见谅！

## step1：获取密钥

为了统一平台服务的配额管理，JavaScript API在新版本引入ak机制。JavaScript API v1.4及以前版本无须申请密钥（ak），自v1.5版本开始需要先申请密钥（ak），才可使用。申请密钥的链接：点击打开链接

打开链接后点击创建应用，填入相关的信息，填完后是这个样子
![img](https://img2018.cnblogs.com/blog/1541173/201812/1541173-20181204093349579-1814168850.png)

点击提交后就知道自己的密钥啦

![img](https://img2018.cnblogs.com/blog/1541173/201812/1541173-20181204093414982-1347121497.png)

## step2:用百度提供的地图生成器工具，链接：[点击打开链接](http://api.map.baidu.com/lbsapi/createmap/index.html)

## step3:生成一个地图，并进行相关的配置

### 1.定位中心点，输入城市（重庆），地点（解放碑）

![img](https://img2018.cnblogs.com/blog/1541173/201812/1541173-20181204093432418-1308271671.png)

### 2.设置地图，设置地图的一些配置，参数

![img](https://img2018.cnblogs.com/blog/1541173/201812/1541173-20181204093527985-1177883145.png)

 

### 3.添加标注，在右边会有根据你的配置生成的地图，选择一种标记，在地图上找到你想要标注的位置，添加标注信息就好

![img](https://img2018.cnblogs.com/blog/1541173/201812/1541173-20181204093539604-1958693112.png)

### step4:点击获取代码，会跳出来一个框里显示你创建的地图的HTML代码，copy最核心的代码到你的页面中，然后把第一个script标签下的你的密钥换成step1中获得的密钥就可以啦。下面的这几句是最核心的

 

```html
 <!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <meta name="keywords" content="百度地图,百度地图API，百度地图自定义工具，百度地图所见即所得工具" />
    <meta name="description" content="百度地图API自定义地图，帮助用户在可视化操作下生成百度地图" />
    <title>百度地图API自定义地图</title>
    <!--引用百度地图API-->
    <script type="text/javascript" src="http://api.map.baidu.com/api?v=2.0&ak=您的密匙"></script>
  </head>
  
  <body>
    <!--百度地图容器-->
    <div style="width:700px;height:550px;border:#ccc solid 1px;font-size:12px" id="map"></div>
    <p style="color:red;font-weight:600">地图生成工具基于百度地图JS api v2.0版本开发，使用请申请密匙。
      <a href="http://developer.baidu.com/map/index.php?title=jspopular/guide/introduction" style="color:#2f83c7" target="_blank">了解如何申请密匙</a>
      <a href="http://lbsyun.baidu.com/apiconsole/key?application=key" style="color:#2f83c7" target="_blank">申请密匙</a>
    </p>
  </body>
  <script type="text/javascript">
    //创建和初始化地图函数：
    function initMap(){
      createMap();//创建地图
      setMapEvent();//设置地图事件
      addMapControl();//向地图添加控件
      addMapOverlay();//向地图添加覆盖物
    }
    function createMap(){ 
      map = new BMap.Map("map"); 
      map.centerAndZoom(new BMap.Point(116.820034,36.568717),15);
    }
    function setMapEvent(){
      map.enableScrollWheelZoom();
      map.enableKeyboard();
      map.enableDragging();
      map.enableDoubleClickZoom()
    }
    function addClickHandler(target,window){
      target.addEventListener("click",function(){
        target.openInfoWindow(window);
      });
    }
    function addMapOverlay(){
      var markers = [
        {content:"我的备注",title:"齐鲁工业大学（校医院）",imageOffset: {width:-46,height:-21},position:{lat:36.566282,lng:116.818237}}
      ];
      for(var index = 0; index < markers.length; index++ ){
        var point = new BMap.Point(markers[index].position.lng,markers[index].position.lat);
        var marker = new BMap.Marker(point,{icon:new BMap.Icon("http://api.map.baidu.com/lbsapi/createmap/images/icon.png",new BMap.Size(20,25),{
          imageOffset: new BMap.Size(markers[index].imageOffset.width,markers[index].imageOffset.height)
        })});
        var label = new BMap.Label(markers[index].title,{offset: new BMap.Size(25,5)});
        var opts = {
          width: 200,
          title: markers[index].title,
          enableMessage: false
        };
        var infoWindow = new BMap.InfoWindow(markers[index].content,opts);
        marker.setLabel(label);
        addClickHandler(marker,infoWindow);
        map.addOverlay(marker);
      };
    }
    //向地图添加控件
    function addMapControl(){
      var scaleControl = new BMap.ScaleControl({anchor:BMAP_ANCHOR_BOTTOM_LEFT});
      scaleControl.setUnit(BMAP_UNIT_IMPERIAL);
      map.addControl(scaleControl);
      var navControl = new BMap.NavigationControl({anchor:BMAP_ANCHOR_TOP_LEFT,type:BMAP_NAVIGATION_CONTROL_LARGE});
      map.addControl(navControl);
      var overviewControl = new BMap.OverviewMapControl({anchor:BMAP_ANCHOR_BOTTOM_RIGHT,isOpen:true});
      map.addControl(overviewControl);
    }
    var map;
      initMap();
  </script>
</html>
```

 