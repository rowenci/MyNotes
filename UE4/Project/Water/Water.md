# water



## 没有水波纹

在Water内容当中搜索M_Fluid_Sim_01材质

## 参数

Selected Points	选择样条点，就是对地形、湖泊的形状进行设置

Water	水

Islands	水体当中的岛屿元素，通过添加“水体岛屿”来添加元素。

Terrain

​	Curve Settings

​		Channel Depth	设置水深

Wave attenuation Water Depth 波浪开始衰减时候的水的深度

Max Wave Height Offset	添加到自动计算的最大波高边界的偏移量。如果自动计算的最大高度边界不匹配，请使用此选项。如果通过世界位置偏移或其他方式手动改变水面，则可能发生这种情况。

Wave Source	改变波浪的来源

​	Waves

​		Gerstner Wave

​			Default

​				Num Waves	波浪的数量

​				Seed	不知道

​			Wavelengths	波浪的长度，也就是每个波浪的大小，调整下面两个参数后可以看出区别。

​				MinWavelength	波浪长度的最小值

​				MaxWavelength	波浪长度的最大值

​			Amplitude	振幅，波浪上下跳跃的高度

​				MinAmplitude

​				MaxAmplitude

​			Directions	波浪的方向

​				Dominant Wind Angle（1）	主风向角度

​				Direction Angular Spread（2）	方向角扩展，将这个调整成0°然后再改变这上面一个参数就能清楚地看见效果。

​				参数1为180°，参数2为40左右，可以模拟海滩的波浪。

​			Steepness	陡度

## 无法保存资产

是多开引擎的原因造成的

## 无属性正在尝试读取sim

把BPSIM蓝图放地图里面

## 重量

通过调整staticmesh的物理质量

