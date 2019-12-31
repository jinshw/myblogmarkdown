# Python 气象数据渲染成图片

## 需求

已有气象接口，可以请求放回气象数据，例如降水数据。由于气象接口返回的是格点数据（1KM*1KM），而我请求的经纬度之间面积很大，返回的数据格点数据很大，导致直接使用前端动态渲染到地图（leaflet）中很慢，想后端执行请求数据渲染为图片，地图在请求渲染后的图片。

## 分析

python对数据处理，图像生成非常友好，并且可以把格点数据（矩阵）直接渲染成图片，并且把相关数据保存在数据库中。渲染后的图片直接可以存放在网址的指定目录下，前端就可以直接访问了。具体步骤如下：

1. 后端定时执行请求接口
2. 把接口格点数据渲染成图片
3. 前端地图（leaflet）可以请求加载图片



## Python生成图片

本文就重点讲一下python根据气象生成图片，其他2个步骤不描述了。

* 气象数据格式（事例）

  ```json
  {   
      "code": 200,    
      "msg": "success",   
      "rows": 4,   
      "cols": 10,   
      "values": [        
          [0, 0, 0.1, 0.5, 0.9, 0.7, 0, 0.6, 0, 1],        
          [1, 2, 3, 4, 5, 6, 0, 0, 0, 0],        
          [1.1, 2.1, 3.1, 4.1, 5.1, 6.1, 0, 0, 0, 0],       
          [3.7, 5.7, 6.4, 5.2, 7.3, 0, 0, 0, 0, 0]    
      ]
  }
  ```

* 详细代码

  ```python
  import matplotlib as mpl
  import matplotlib.pyplot as plt
  # 气象数据
  datas = {
      "code": 200,
      "msg": "success",
      "rows": 4,
      "cols": 10,
      "values": [
          [0, 0, 0.1, 0.5, 0.9, 0.7, 0, 0.6, 0, 1],
          [1, 2, 3, 4, 5, 6, 0, 0, 0, 0],
          [1.1, 2.1, 3.1, 4.1, 5.1, 6.1, 0, 0, 0, 0],
          [3.7, 5.7, 6.4, 5.2, 7.3, 0, 0, 0, 0, 0]
      ]
  }
  # 颜色
  colors = ['none', '#39AA00', '#62BAFF', '#0001FB']
  # 边界0-1：none(没有颜色)、1-2：'#39AA00'、2-3：'#62BAFF'、3-...:'#0001FB'
  bounds = [0, 1, 2, 3]
  #行数
  rows = datas["rows"]
  #列数
  cols = datas["cols"]
  # 格点矩阵数据
  values = datas["values"]
  
  cmap = mpl.colors.ListedColormap(colors)
  norm = mpl.colors.BoundaryNorm(bounds, cmap.N)
  
  im = plt.imshow(values, interpolation='none', cmap=cmap, norm=norm, alpha=0.8)
  
  
  ax = plt.gca()
  # x轴方向调整：
  ax.xaxis.set_ticks_position('top')  # 将x轴的位置设置在顶部
  # ax.invert_xaxis()  # x轴反向
  # y轴方向调整：
  ax.yaxis.set_ticks_position('left')  # 将y轴的位置设置在右边
  # ax.invert_yaxis()  # y轴反向
  
  plt.axis('off')  # 去掉坐标轴
  
  fig = plt.gcf()
  # 设置图片大小
  fig.set_size_inches(rows, cols)
  # dpi分辨率、transparent透明、alpha 透明度
  plt.savefig("test7.png", dpi=100, transparent=True, alpha=0.8, pad_inches=0, bbox_inches='tight')
  plt.show()
  
  ```

  

## 注意点

* Python版本为3.7

* 连接`MySQL`时报错：`RuntimeError: implement_array_function method already has a docstring `

  原因：`import pymysql` 安装的影响

  ```python
  pip uninstall scikit-learn
  pip uninstall matplotlib
  pip uninstall pandas
  pip uninstall scipy
  pip uninstall numpy
  
  pip install numpy
  pip install scipy
  pip install pandas
  pip install matplotlib
  pip install scikit-learn
  
  Pycharm的问题！！！
  ```

  



