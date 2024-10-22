---
id: interview-webgis

title: Interview WebGIS
---

# 瓦片行列号

```
col = floor((x0 - x) / (tileSize * resolution))

row = floor((y0 - y) / (tileSize * resolution))
```

# 分辨率

分辨率表示 1 个像素代表多少地图单位。

```
resolution = extentSize / tileSize
```

# 比例尺

比例尺表示地图上的 1 米与其代表的实际距离之间的比例。

```
scale = metersInMap / metersInReality
```
