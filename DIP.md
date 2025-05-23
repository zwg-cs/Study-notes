## Digital Image Processing

### Chapter 3 灰度变换和空间滤波

#### 3.1 背景
空间域：图像平面本身
点处理技术：结果进取决于点的灰度技术    
邻域处理技术     
**对比度拉伸：** $k$以下的灰度值变暗，$k$以上的灰度值变亮， 比原图的对比度更高。    
阈值处理函数：产生一幅二级（二值）图像。    
图像增强：对图像进行加工，使其结果对某些特定应用而言比原图更适合的一种应用。

$dst(x,y)=alpha⋅src(x,y)+beta$

#### 3.2 一些基本的灰度变换函数
* 反转变换
* 对数变换
* 恒定变换
* 反对数变换
* 幂律（伽马）变换

图像反转，底片效果，用于增强图像暗色区域中的白色或灰色细节。    
       
对数变换： $s = c \times log(1+r)$  范围较窄的低灰度值映射到输出范围中范围较宽的高灰度值(显示器显示傅里叶频谱)     
    
幂律(伽马)变换：$s=c\times (r+\epsilon)^\gamma$ ；$\epsilon$是偏移    
获取、打印、显示图像中用到伽马校正。增强对比度。压缩灰度级。    
     
分段线性变换函数
* 对比度拉伸
* 灰度级分层
* 比特平面分层

#### 3.3 直方图处理   
暗图像：大多数直方图容器集中在灰度级的低（暗）端    
亮图像：大多数直方图容器集中在灰度级的高（亮）端    
低对比度图像：直方图容器基本位于灰度图的中间  
高对比度图像：直方图容器覆盖较宽的灰度级，占据整个灰度级范围并且是均匀分布的（细节多，高动态范围）  

##### 直方图均衡化


##### 直方图匹配（规定化）


##### 局部直方图处理


#### 3.4 空间滤波基础


#### 3.5 平滑（低通）空间滤波器
盒式核：系数的值相同

镜像填充和复制填充


#### 3.6 锐化（高通）空间滤波器


##### 基础
一阶导数：    
* 恒定灰度区域的一阶导数必须为0
* 灰度台阶或斜坡开始处的一阶导数必须为非0
* 灰度斜坡上的一阶导数必须为非0
二阶导数
* 恒定灰度区域的二阶导数必须为0
* 灰度台阶或斜坡开始处和结束处的二阶导数必须为非0
* 灰度斜坡上的二阶导数必须为0

##### 使用二阶导数锐化图像--拉普拉斯
