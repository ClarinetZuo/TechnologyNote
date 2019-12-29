# GEE基础知识
## Landsat
### 每16天经过地球的同一点
### 180天大概有12张图片
---
## 构建原始Landsat图像
### 步骤
- ee.ImageCollection('图片id')
- 应用TOA校准
- 为每一个像素点进行"cloud score"
- select the lowest possible range of cloud scores at each point.
- compute per-band percentile values from the accepted pixels.
---
## NDVI:归一化植被指数的取值范围是-1~1，-1~0表示地面有云那些，0表示有岩石，大于0表示有植被覆盖
## GEE的数学函数
### subtract()
- 计算两幅图的差异
### mask():做差异性分析时很有用
- e.g. image1.mask(image2):image2中所有像素中小于等于0的，在image1中将变成透明的，所有>0的将被呈现
```JavaScript
var image1 = ndvi32Day.filterDate('2014-01-01','2014-12-31').max();
var image2 = ndvi32Day.filterDate('2000-01-01','2000-12-31').max();

var difference = image2.subtract(image1).gt(0.5);
var masked = difference.mask(difference);
Map.addLayer(masked,{'palette':'FF0000'});
```
### 导出图像的两种方式
- Export.image.toDrive
- Export.image.toAsset：存储在Asset里面，可以import进来
---
## 一些遥感英语
### spectral signature:光谱特征