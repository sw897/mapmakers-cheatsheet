# Web地图制作宝典

> 原文出自 [MapBox](www.mapbox.com) 专家 [Tom MacWright](http://macwright.org/)，给非GIS专业的制图爱好者的操作便签。读过之后，获益匪浅，不敢独享，首次尝试了翻译文档。翻译过程中，发现有些内容，如获取数据的方法，对国内不太适用，于是针对数据源进行了小部分的更新。文中提到的相关小工具，非常灵活有用，但有些只能在linux上运行。为方便使用，我会参考其功能，重新做跨平台实现，发布到GitHub。

## 根据数据类型选择操作

### 点

#### 根据数据量选择
- 少量
 - 把数据转为 [GeoJSON](http://geojson.org/) 格式，使用 [Leaflet](http://leafletjs.com/) 发布地图
- 大量,  但是点位信息并不重要
 - 使用 [Leaflet.markercluster](https://github.com/Leaflet/Leaflet.markercluster) 把点集群(cluster)起来
- 大量, 并且有些值可以聚合(aggregate)起来
 - 使用 [QGIS hexbin](https://www.mapbox.com/blog/binning-alternative-point-maps/) 插件创建hexbins, 生成多边形. 参照多边形数据处理
- 大量, 表示存在状态 - 如比tweets发布位置数据
 - 使用 [Leaflet.heat](https://github.com/Leaflet/Leaflet.heat) 或 [QGIS heatmap](http://qgis.spatialthoughts.com/2012/07/tutorial-making-heatmaps-using-qgis-and.html) 插件生成热力图. 如果选择 QGIS heatmap, 参照 Raster 数据处理.
- 海量, 不需要 label , 使用 [datamaps](https://github.com/ericfischer/datamaps)绘制.

### 多边形

#### 根据数据量选择
- 少量
 - 把数据转为 [GeoJSON](http://geojson.org/) 格式，使用 [Leaflet](http://leafletjs.com/) 发布地图
- 大量, 并且多边形复杂, 有大量必需的细节信息
 - 使用 [TileMill](https://www.mapbox.com/tilemill/) 渲染，生成使用 [UTFGrid](https://www.mapbox.com/developers/utfgrid/) 的交互地图
 - 使用 [GeoServer](http://geoserver.org/) 的 WMS 与 GetFeatureInfo 接口
- 大量, 但多边形细节信息不必需
 - 使用 [TopoJSON](https://github.com/mbostock/topojson) 或 [QGIS](http://www.qgis.org/) 化简多边形后使用[Leaflet](http://leafletjs.com/)发布地图

### 属性(特征数据)

#### 根据数据类型选择
- 数值
 - 使用 [QGIS](http://www.qgis.org/) 取点集或多边形中心点，参照点数据处理
 - 归一处理成比例数据后分割多边形区域，参照 比率 类型处理
- 比率 或 分类
 - 生成等值线图，小数据量使用 [Leaflet](http://leafletjs.com/) , 大数据量使用 [TileMill](https://www.mapbox.com/tilemill/)
- 时序数据 - 与时间相关的值
 - 如果少于100个样本 - 例如 50 年间按年统计的数据, 使用 [small multiples](http://www.nytimes.com/interactive/2012/07/20/us/drought-footprint.html): 每个样本一张图.
 - 使用 [Leaflet](http://leafletjs.com/) 或 [d3.js](http://d3js.org/) 生成动画
 - 海量数据, 使用 [CartoDB  torque](http://blog.cartodb.com/post/66687861735/torque-is-live-try-it-on-your-cartodb-maps-today)。国内可以使用 [极海 GeoHey](https://geohey.com/)。
- 多变量数据, 如种群计数
 - 使用 [englewood](https://github.com/newsapps/englewood) 生成点密度图 [dot density map](http://demographics.coopercenter.org/DotMap/index.html)
- 地名数据, 如国家或行政区划名称
 - 有编码 ID:
     - ISO2 或 ISO3 编码
         - 在 [Natural Earth](http://www.naturalearthdata.com/) 下载合适等级的数据, 使用 QGIS, 参照多边形数据处理
     - 邮编
         - 在 [ZCTAs](https://www.census.gov/geo/reference/zctas.html) 下载数据
  - 无编码
     - 查找编码, 或手绘图形
- 地址数据
  - 地址不能直接上图. 使用 [OpenRefine](http://openrefine.org/) 或
    [Geo for Google Docs](https://www.mapbox.com/geo-for-google-docs/) 进行地址编码转换成点 , 然后参照点数据处理
  - 地理编码服务
     - US: [US Census](http://geocoding.geo.census.gov/geocoder/Geocoding_Services_API.pdf)
     - Canada: [Geogratis](http://geogratis.gc.ca/site/eng/geoloc)
     - OpenStreetMap: [Nominatim](http://nominatim.openstreetmap.org/)
     - [Data Science Toolkit](https://github.com/petewarden/dstk) 
  - 地理编码库
      - node - [node geocoder](http://nchaulet.github.io/node-geocoder/)
      - Perl - [Geo::Coder::Many](https://metacpan.org/pod/Geo::Coder::Many)
      - PHP - [Geocoder PHP](http://geocoder-php.org)
      - Python - [geopy](https://github.com/geopy/geopy)
      - Ruby - [Ruby Geocoder](http://www.rubygeocoder.com)

### 线

- 少量, 转为GeoJSON使用 [Leaflet](http://leafletjs.com/)发布
- 大量, 需要标注的数据 (如街道)， 使用 [TileMill](https://www.mapbox.com/tilemill/)
- 海量, 不需要标注的数据，使用 [datamaps](https://github.com/ericfischer/datamaps).

### 栅格数据

- 已经配准与处理好的数据 (卫片或处理后的数据)
  - 使用 [TileMill](https://www.mapbox.com/tilemill/) 渲染地图，在 Leaflet 中使用瓦片
  - 参考 [processing satellite imagery](https://www.mapbox.com/foundations/processing-satellite-imagery/) 使用 [GDAL](http://www.gdal.org/)/[ImageMagick](http://www.imagemagick.org/) 流程.
- 无人机采集的数据
- 扫描数据
  - 使用 [MapKnitter](http://mapknitter.org/) 进行配准与纠正

### 不能识别的格式

- 使用 [GDAL](http://www.gdal.org/) ogr2ogr 进行格式转换. 如果没有安装 GDAL，可以使用在线版本 [Ogre](http://ogre.adc4gis.com/)
- 商业产品:
  - [SAFE FME](http://www.safe.com/)
- 向数据提供源索要其他格式数据

### 获取数据

- 政府数据
  - 联系政府相关数据主管部门获取，或访问当地政务数据网站，如[北京政务数据资源网](http://www.bjdata.gov.cn/index.htm)
  - [国家数据](http://data.stats.gov.cn/)
  - [国家地球系统科学数据共享平台](http://www.geodata.cn/)
  - [中国人口地理信息系统](http://cpgis.fudan.edu.cn/cpgis/default.asp)
- 个人数据
  - 使用 [geojson.io](http://geojson.io/) 或其他GIS软件平台自行绘制
- 全球数据
  - 全球低精度基础数据，[naturalearthdata.com](http://www.naturalearthdata.com/)
  - 全球行政区划数据，[Global Administritive Areas](http://www.gadm.org/)
  - 高精度本地矢量数据,  [OpenStreetMap extracts](https://mapzen.com/metro-extracts/) 
  - [Global LUCC data (30m)](http://data.ess.tsinghua.edu.cn/)

- 国外提供的中国数据
  - 中国历史地图，[China historical geography GIS](http://www.fas.harvard.edu/~chgis/data/chgis/downloads/v4/datasets/index.html)
  - [中国25万数据](http://hcl.harvard.edu/libraries/maps/collections/series_indices/China_Index.html)
 
- 其他专题数据
  - [航空数据](http://www.openflights.org/data.html) 
  
### 可视化配置

- 投影:
  - 网络瓦片地图使用选择 [Spherical Mercator](http://epsg.io/3857) 投影
  - 如果使用 [d3](http://d3js.org/) 并且没有使用瓦片, 则选择最适合的.  [d3-geo-projection](https://github.com/d3/d3-geo-projection) 扩展了很多投影类型.
  - 如果有投影信息但又不确定是什么类型，可以使用 [epsg.io](http://epsg.io/3857).
- 配色:
  - 拿不准时用 [ColorBrewer](http://colorbrewer2.org/)
  - 可以参考  [Subtleties of Color](http://earthobservatory.nasa.gov/blogs/elegantfigures/2013/08/05/subtleties-of-color-part-1-of-6/)了解更多
- 比例尺:
  - 所有数据
     - 优先线性比例尺
     - 其次分位数
  - 比率或复合数据
     - 考虑 log 和 power 比例尺
- 点标识:
  - 首先是不区分的圆圈
  - 根据圆圈面积等比标识
- 其他地图控件:
  - 只有正北方不指向上时添加指北针
  - 尽量增加数据属性说明
  - 如果支持缩放，则让缩放控件可见，平移控件不可见
