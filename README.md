# 什么数据类型?

## 点

- 多大数据量?
  - 少量
    - 把数据转为 [GeoJSON](http://geojson.org/) 格式，使用 [Leaflet](http://leafletjs.com/) 发布地图
  - 大量, 但是点并不是很重要
    - 使用 [Leaflet.markercluster](https://github.com/Leaflet/Leaflet.markercluster) 把点集群(cluster)起来
  - 大量，并且有些值可以聚合(aggregate)起来
    - Create hexbins of your points with the 把数据使用 [QGIS hexbin](https://www.mapbox.com/blog/binning-alternative-point-maps/) 插件创建hexbins, 生成多边形. 参照多边形数据处理
  - 大量表示存在状态的点 - 像tweets数据
    - 使用 [Leaflet.heat](https://github.com/Leaflet/Leaflet.heat) 或 [QGIS heatmap](http://qgis.spatialthoughts.com/2012/07/tutorial-making-heatmaps-using-qgis-and.html) 插件生成热力图. 如果选择 QGIS heatmap, 参照 Raster 数据处理.
  - 海量数据, 并且不需要 label , 使用 [datamaps](https://github.com/ericfischer/datamaps).

## 多边形

- 多大数据量?
  - 少量
    - 把数据转为 [GeoJSON](http://geojson.org/) 格式，使用 [Leaflet](http://leafletjs.com/) 发布地图
  - 大量, 并且多边形复杂，有大量必需的细节信息
    - 使用 [TileMill](https://www.mapbox.com/tilemill/) 渲染，生成使用 [UTFGrid](https://www.mapbox.com/developers/utfgrid/) 的交互地图
    - 使用 [GeoServer](http://geoserver.org/) 的 WMS 与 GetFeatureInfo 接口
  - 大量, 但多边形细节信息不必需
    - 使用 [TopoJSON](https://github.com/mbostock/topojson) 或 [QGIS](http://www.qgis.org/) 化简多边形

## 属性(特征数据)

- 什么类型?
  - 数值
    - 使用 [QGIS](http://www.qgis.org/) 取点集中心点，参照点数据处理
    - 归一处理成比例数据后分割多边形区域，参照 比率 类型处理
  - 比率 或 分类
    - 生成等值线图，小数据量使用 [Leaflet](http://leafletjs.com/) , 大数据量使用 [TileMill](https://www.mapbox.com/tilemill/)
  - 时序数据 - 与时间相关的值
    - 如果少于100个样本 - 例如 50 年间按年统计的数据, 使用 [small multiples](http://www.nytimes.com/interactive/2012/07/20/us/drought-footprint.html): 每个样本一张图.
    - 使用 [Leaflet](http://leafletjs.com/) 或 [d3.js](http://d3js.org/) 生成动画
    - 海量数据, 使用 [CartoDB and torque](http://blog.cartodb.com/post/66687861735/torque-is-live-try-it-on-your-cartodb-maps-today)
  - 多变量数据: 例如种群计数
    - 使用 [englewood](https://github.com/newsapps/englewood) 生成点密度图 [dot density map](http://demographics.coopercenter.org/DotMap/index.html)
  - 地名数据, 例如国家
    - 有编码 ID:
      - ISO2 或 ISO3 编码
        - 在 [Natural Earth](http://www.naturalearthdata.com/) 下载合适等级的数据, 使用 QGIS,
          参照多边形数据处理
      - 邮编
        - 在 [ZCTAs](https://www.census.gov/geo/reference/zctas.html) 下载数据
    - 无编码
      - 查找编码, 或手绘图形
  - 地址
    - You can't map addresses directly. Geocode them with [OpenRefine](http://openrefine.org/) or
      [Geo for Google Docs](https://www.mapbox.com/geo-for-google-docs/), and then start at Points
    - Other Geocoding options:
      - US: [US Census](http://geocoding.geo.census.gov/geocoder/Geocoding_Services_API.pdf)
      - Canada: [Geogratis](http://geogratis.gc.ca/site/eng/geoloc)
      - OpenStreetMap: [Nominatim](http://nominatim.openstreetmap.org/)
      - [Data Science Toolkit](https://github.com/petewarden/dstk) can be useful for local bulk geocoding that would be too much for a hosted service.

## 线

- 少量数据: 使用 [Leaflet](http://leafletjs.com/)
- 大量数据, 或需要标注的数据 (如街道)， 使用 [TileMill](https://www.mapbox.com/tilemill/)
- 海量数据, 或不需要标注的数据，使用 [datamaps](https://github.com/ericfischer/datamaps).

## 栅格数据

- 已经配准与处理好的数据 (卫片或处理后的数据)
  - 使用 [TileMill](https://www.mapbox.com/tilemill/) 渲染地图，在 Leaflet 中使用瓦片
  - 参考 [processing satellite imagery](https://www.mapbox.com/foundations/processing-satellite-imagery/) 使用 [GDAL](http://www.gdal.org/)/[ImageMagick](http://www.imagemagick.org/) 流程.
- 无人机采集的数据
- 扫描数据
  - 使用 [MapKnitter](http://mapknitter.org/) 进行配准与纠正

## 不能识别的格式

- 使用 [GDAL](http://www.gdal.org/) ogr2ogr 进行格式转换. 如果没有安装 GDAL，可以使用在线版本 [Ogre](http://ogre.adc4gis.com/)
- 商业产品:
  - [SAFE FME](http://www.safe.com/)
- 向数据提供源索要其他格式数据

## 没有数据

- 政府数据
  - Contact the town or federal GIS dept you need
  - Use [FOIAMachine.org](https://www.foiamachine.org/) to request data via FOIA
- 个人数据
  - If you want to create data, use [geojson.io](http://geojson.io/) and draw it.
- 全球数据
  - For basic data like countries, cities, use [naturalearthdata.com](http://www.naturalearthdata.com/)
  - Use [OpenStreetMap extracts](https://mapzen.com/metro-extracts/) for higher-detail local data
- 历史数据
  - [NYPL MapWarper](http://maps.nypl.org/) for historical, scanned (raster) maps
  - Greek & Roman: [Pleiades](http://pleiades.stoa.org/home)

## 可视化配置

- 投影:
  - If it's a web map with tiles, use [Spherical Mercator](http://epsg.io/3857)
  - If using [d3](http://d3js.org/) and not using tiles anywhere, use whatever fits best. Bonus projections are in [d3-geo-projection](https://github.com/d3/d3-geo-projection).
  - Have a project and not sure what it is? Use [epsg.io](http://epsg.io/3857).
- 配色:
  - When in doubt, use [ColorBrewer](http://colorbrewer2.org/)
  - Want to know more? Read [Subtleties of Color](http://earthobservatory.nasa.gov/blogs/elegantfigures/2013/08/05/subtleties-of-color-part-1-of-6/)
- 比例尺:
  - For any data
    - Try linear first
    - Then quantile
  - For data of rates or compounding values
    - Try log and power scales
- 画点:
  - Start with normal circles with no strokes
  - Scale points by area, not diameter
- 整饰:
  - Only add a north arrow if north isn't up
  - Always attribute your data, especially [OpenStreetMap](http://www.openstreetmap.org/), to avoid the nerd wrath
  - If it zooms, add visible zoom controls. Pan isn't necessary, but not everyone has a scroll wheel / multitouch
