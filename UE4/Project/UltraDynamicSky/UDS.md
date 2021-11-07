# 细节面板



## Basic Controls

Refresh Settings  刷新所有的设置

Time Of Day 通过设置时间（24小时制）来设置光照和太阳

Saturation 设置天空颜色的饱和度

Overall Intensity 遮罩的亮度（浓度）

Night Brightness 夜晚的亮度



## Clouds

Cloud Speed 云的速度

Cloud Density 云的密度

Cloud Wisps Opacity





## ***Ultra_Dynamic_Sky翻译及功能概述\***



***basic controls 基础控制\***

**Refresh Settings 刷新设置** 检查此布尔一次，以刷新所有设置；

**Time Of Day 一天中的时间** 一天中天空模仿的时间，从0000到2400。例如早上九点半数值为“930”，下午五点35数值为“1735”。

**Sky Mode 天空模式**

Volumetric Clouds 体积云

Static Clouds 静态云

2D Dynamic Clouds 二维动态云

No Clouds 没有云

2D Clouds using Color Curves (Legacy mode) 使用颜色曲线的二维云（传统模式）

Volumetric Aurora 体积极光（夜晚会出现极光效果）

![img](https://pic3.zhimg.com/80/v2-e10838ec454c27ea021148a4ff45936e_720w.jpg)

**Cloud Coverage 云层覆盖率**

**Overall Intensity 总强度** 天空球整体光照强度

**Saturation 饱和度** 天空球饱和度

**Contrast 对比度** 天空球对比度

**Dawn Time 黎明时分** 太阳在早晨经过地平线的确切时刻

**Dusk Time 黄昏时间** 太阳在晚上被地平线没过的确切时刻

**Night Brightness 夜间亮度**

**For Mobile Render 用于移动渲染** 调整某些设置以纠正移动设备无法做到的效果

**Directional Lights Absent Brightness 无亮度的平行光** 当两个平行光都不存在时，天空和天光会增添额外的亮度，以避免太阳和月亮都落下时出现极黑暗的不方便时刻（不影响体积云光照）

**Overcast Night Brightness 阴夜亮度** 当阴天时，此值会转化为夜间亮度。默认情况下，这将照亮多云的夜晚，使他们不那么极端黑暗。

**Use Sky Mode Scalability Map 使用天空模式可扩展性映射**

**Sky Mode Scalability Map 天空模式可扩展性映射** 如果"使用天空模式可伸缩性映射"变量，此地图将用于根据效果质量确定天空模式。整数值对应于质量级别 l）是低的，1是中等的，2是高的，3是史诗，4是电影。



***Animate Time Of Day 一天中的时间动画***

**Animate Time Of Day** 以动画形式 运行游戏中一天的时间，如果太阳光设置为静态，这将被禁用。

**Day Length 日长** 控制白天的特定长度，从日出到日落的时间（几分钟）。这些值不会因为黎明时分和黄昏时间而改变，从日出到日落的时间将永远是这个值。

**Night Length 夜长** 控制夜晚的特定长度，从日落到日出的时间（几分钟）。与日长一样，无论黎明时间和黄昏时间如何，此值都决定夜晚的长度。

**Sunrise Event Time 日出事件时间** 日出事件调度器被触发的时间。适用于切换灯光或环境声音效果。

**Sunset Event Time 日落事件时间** 日落事件调度器被触发的时间。适用于切换灯光或环境声音效果。

**Time speed 时间速度**



**Volumetric Clouds 体积云**

**Layer Height Scale 层高比例** 放大体积云层的高度，垂直拉伸。

**Bottom Altitude 底部高度** 距原点的高度，以公里为单位，即体积云层底部所在的位置。

**Base Clouds scale 基础云层比例** X轴和Y轴上基础云层的Tiling

**Sub Noise Scale 噪波比例** 用于云层侵蚀效果的3D平铺噪波纹理的比例

**High Frequency Noise Layer 高频噪波层** 3D纹理细节的云层噪波效果

**Extinction Scale Top 云层顶部消光强度** 云层顶部消失的强度，数值越低越朦胧，数值越高越清晰

**Extinction Scale Bottom 云层底部消光强度** 云层顶部消失的强度，数值越低越朦胧，数值越高越清晰，这些值可能会对性能产生相当大的影响。

**View Sample Count Scale 查看样本计数比例** 缩放用于渲染云的样本数量.

**Shadow Sample Scale 阴影采样比例** 云层内阴影的样本数量，数值低效果差些；数值高效果好，但开销大。

**Volumetric Cloud Noise Type 体积云噪声类型** 为“云噪波类型”（Cloud Noise Type）选择“自定义”（Custom）时，用于生成体积云的纹理。

classic 经典

voronoi 泰森多边形

voronoi Smooth 沃罗诺平滑

voronoi Diverse 沃罗诺多样性

Custom 自定义

![img](https://pic1.zhimg.com/80/v2-811d9d76f547e78f42d891c827957108_720w.jpg)

**Volumetric Custom Noise Texture 体积云自定义噪波纹理** 当为云噪声类型选择自定义时，用于生成体积云的纹理。（可以用来制作地震云或者有规律的有特定图案的云层效果）

**Volumetric Clouds Scale 体积云比例** 将所有体积云元素的大小缩放在一起。

**Enable Fog Inside Clouds 启用云中雾** 启用增加云层体积雾密度的效果。

**Multiscattering Intensity 多散射强度** 缩放多散射光通过云层的强度。

**Multiscattering Occlusion Factor 多散射遮挡因子** 调整光线通过云层时吸收的多散射光量。

**Shadow Tracing Distance 阴影追踪距离** 平行光通过体积云投射阴影的最大距离，公里计。

**Sub Noise Erosion 亚噪波腐蚀** 应用于云形成的侵蚀量

**Phase G** 第一相位函数分散的量。（具体用途不详）

**Phase G 2** 第二相位函数分散的量。（具体用途不详）

**Multi Scattering Eccentricity 多次散射偏心率** 0距离太阳近的云层透光效果变差，1距离太阳近的云层透光效果变好

**Distance To Sample Max Count 到样本最大计数的距离** 增加此值将减少更近的云上的样本计数。

**Tracing Max Start Distance 追踪最大开始距离** 光线从体积云量开始投射的最大距离。如果需要渲染更远的云，请尝试将其数值调大。

**Tracing Max Distance 追踪最大距离** 最大距离光线会跟踪穿过云层。如果你想看清远处云层的边缘，增大此值；特别是如果你需要进入云层内部/上方，增大此值。



**2D Dynamic Cloud Appearance 2D动态云外观(在云模式为二维动态云时有效果）**

**Soften Cloud Layer 1 柔化云层1** 增加侵蚀和软化云的边缘。

**Soften Cloud Layer 2 柔化云层2** 增加侵蚀和软化云的边缘。

**Sharpen Outer Edge 锐化外边缘**

**Cloud Tilling Layer 1 云层1平铺重复度**

**Cloud Tilling Layer 2 云层2平铺重复度**

**One Cloud Layer 只有一层云** 一种性能优化，可删除其中第二个云层

**Cloud Height Layer 1 云层1高度**

**Cloud Height Layer 2 云层2高度**

**Overcast Swirling Texture 阴天旋转纹理** 当阴天时，云层上覆盖的纹理量

**Cloud Noise Type 云噪波类型** 用于生成动态云的噪波纹理

**Custom Noise Texture 自定义噪波贴图** 当为云噪波类型选择自定义时，用于生成动态云的纹理

**Sun Lighting Intensity 太阳光照强度** 云从太阳接收的光量

**Sun Highlight Radius Curve 太阳高光半径曲线**

**Sun Highlight Intensity Curve 太阳高光强度曲线**

**Shine Intensity Curve 发光强度曲线**

**Cloud Opacity 云不透明度**



**2D Dynamic Cloud Distribution 二维动态云分布**(在云模式为二维动态云时有效果）

**Horizon Density Multiplier 层级密度乘数** 远处云层覆盖量

**Zenith Density Multiplier 天顶密度倍增** 近处云层覆盖量

**Latitude Gradient Position 纬度梯度位置** 从 “地平线” 到 “天顶” 值的梯度高度，在上面混合。

**Latitude Gradient Width 纬度梯度宽度** 从 “地平线” 到 “天顶” 值的梯度宽度

**Around Sun Density Multiplier 围绕太阳密度倍增** 太阳周围邻近区域云密度的乘数。

**Around Sun Density Exponent 太阳密度指数** 定义太阳周围区域的径向梯度的指数。越高，半径越收缩。

**Around Moon Density Multiplier 绕月密度乘数** 月球周围邻近区域云密度的乘数。

**Around Moon Density Exponent 绕月密度指数** 定义月球周围区域的径向梯度的指数



**Cloud Movement 云层运动**

**Cloud Speed 云层速度** 云在天空中移动和变化的速度。注意：如果您的场景中有一个超动态天气器，则该变量的控制将由超动态天气器执行 (也就是“Ultra_Dynamic_Weather”）

**Cloud Direction 云层方向** 云层移动的方向。注意：如果场景中有超动态天气BP，则该变量的控制将由超动态天气BP执行

**Cloud Phase 云阶段** 云层的时间偏移：当以0的速度工作时，有利于获得良好的队形

**Randomize Cloud Formation On Run 运行时随机化云形成**



**Cloud Shadows 云阴影**

**Use Cloud Shadows 启用云阴影**

**Cloud Shadows Scale 云阴影比例** 云影的世界空间缩放，以阳光的位置为中心。选择体积云时不使用此值

**Cloud Shadows Intensity when Sunny 晴天云影强度** 云量低时，云的黑暗阴影。

**Cloud Shadows Intensity when Overcast 阴天云影强度** 云量高时，云的黑暗阴影

**Cloud Shadows Softness when Sunny 晴天云影柔和度** 当云量低时，云影的边缘有多柔软。

**Cloud Shadows Softness when Overcast 阴天云影柔和度** 云量高时，云影的边缘有多柔软

**Force Light Functions On 强制灯功能开启** 绕过可缩放性设置并强制启用灯光功能，因为默认情况下，低可伸缩性配置文件禁用灯光功能，这会打破云阴影



**Cloud Wisps 云雾**

**Cloud Wisps Opacity 云雾不透明度**

**Cloud Wisps Texture 云缕纹理**



**Sun 太阳**

**Sun Angle 太阳角度** 太阳的旋转，这决定了日出/日落的方向。

**Sun Casts Shadows 太阳投下阴影**

**Sun Disk Intensity 太阳圆盘强度**

**Sun Shader Intensity 太阳着色器强度** 会影响云层

**Sun Radius 太阳半径**

**Sun Inclination 太阳倾角** 穿过天空的太阳轨道的倾斜

**Sun Light Intensity 太阳光强度**

**Sun Volumetric Scattering Intensity 太阳体散射强度** 太阳方向光源的强度

**Extend Dawn And Dusk 延长黎明和黄昏** 这将通过拉平太阳在地平线上的轨道来人为地延长黎明和黄昏

**Sun Vertical Offset 太阳垂直偏移** 迫使太阳在天空中更高或更低。可用于重建在极端纬度发现的24小时昼夜



**Moon 月亮**

**Moon Casts Shadows 月亮投下阴影**

**Moon Scale 月亮圆盘大小**

**Moon Inclination 月倾** 月亮穿过天空时的倾斜度

**Moon Phase 月盈亏** 调节月亮的阴晴圆缺

**Change Moon Phase Once A Day 一天换一次月相**

**Moon Intensity 月亮强度**

**Moon Color 月亮光颜色**

**Moon Orbit Offset 月球轨道偏移** 月亮穿过天空的时间偏移。默认情况下，月亮在夜间穿过天空，但可以使用此变量使其出现得更早或更晚

**Moonlight Intensity 月光强度**

**Moon Rotation 月球自转** 月亮纹理的手动旋转控制

**Custom Moon Texture 自定义月亮纹理**

**Use Custom Moon Texture 使用自定义月亮纹理**

**Moon Glow Intensity 月球周围辉光的强度**

**Moon Angle 月亮角度** 横向旋转

**Moon Volumetric Scattering Intensity 月球体积散射强度**

**Moon Vertical Offset 月球垂直偏移** 迫使月亮在天空中更高或更低



**Stars 星星**

**Stars Intensity 星星强度**

**Stars Color 星星颜色**

**Stars Speed 星星速度**

**Stars Tiling 星星平铺度** 星星纹理在天空中的数量，重复度。

**Stars Texture 星星纹理**

**Night Sky Glow 夜空辉光**

**Night Sky Glow Color 夜空辉光色**

**Stars Phase 星象**

**Stars Intensity Curve 星星强度曲线**



**Sky Light 天光**

**Sky Light Mode 天光模式** 选择要使用的天窗光源的类型。将鼠标悬停在每个选项上，查看每个选项的详细说明。如果对天光使用自定义actor，需要更改源类型以匹配，SLS拍摄场景“以拍摄为主”SLS为另外两个指定了“Cubemap**”**

Capture Based 基于捕获 天空将通过捕获天空来生成立方体地图。这种方法是最现实的，但是如果天空发生重大变化，那就需要重新捕获。重新捕获是一个昂贵的过程，并且可能导致故障。

Custom Cubemap 自定义立方体映射 一个简单的静态立方体贴图纹理，使用下面的自定义立方体贴图选项设置。

Cubemap with Dynamic Color Tinting 动态着色立体地图 此方法使用平坦的灰度立方体贴图，并用颜色动态着色。这是一个很好的平衡选择，因为它通常可以在一天的时间内使用正确的环境着色，并且不需要昂贵的重新捕获。

![img](https://pic3.zhimg.com/80/v2-1d8a37a861efbb60853f37fb8734fc72_720w.jpg)

**Sky Light Intensity 天光强度**

**Flat Sunny Cubemap 平面阳光立方体贴图**

**Flat Overcrast Cubemap 平顶立方体贴图**

**Sky Light Dynamic Tinting Color Color Curve 天光动态着色颜色曲线** 定义天空在一天周期中的位置的曲线

**Custom Cubemap 自定义立方体映射贴图**

**Sky Light Color 天光颜色**

**Sky Light Lower Hemisphere Color 天光下半球颜色**

**Recapture Sky Light Periodically 定期捕捉天光** 如果使用基于捕获的天光，这将定期生成新的捕获。这可以使捕获与一天中的当前时间保持一致，但请注意，捕获成本很高，并且可能会导致故障

**Sky Light Recapture Period （Seconds) 天光回收期（秒）**在天窗重新捕获之间需要等待多长时间 (以秒为单位)。重新捕获可能会很昂贵，因此应将其设置得尽可能高，否则不会有明显的捕获

**Scale Skylight Intensity At Night 夜间缩放天空强度** 应用于夜间天窗强度的乘数。仅与动态着色天空相关

**Scale Skylight Intensity When Cloudy 多云时缩放天空强度** 阴天时应用于天窗强度的乘数。仅与动态有色天空相关



**Height Fog 高度雾**

**Fog Density 雾密度**

**Fog Inscattering Color Curve 雾散射颜色曲线** 定义全天高度雾颜色的曲线

**Fog Directional Inscattering Color Curve 雾定向内散射颜色曲线** 定义整个白天周期的高度雾方向颜色的曲线

**Full Cloudiness Fog Density 满云 雾密度** 当云密度最大时，要添加到基础值 (静态或曲线) 的雾密度量。

**Fog Density Distribution 雾密度分布** 定义从雾密度到满云 雾密度的分布曲线

**Use Volumetric Fog 使用体积雾**

**Use Fog Density Curve 使用雾密度曲线** 用下面的时间控制雾密度。

**Fog Density Curve 雾密度曲线** 如果启用了上述选项，则浮标曲线将确定从0000到2400的雾密度。编辑此曲线可更改全天的雾密度。只需记住保持0000和2400处的钥匙相同，否则值会在午夜处突然改变



**Aurora 极光**

**Use Auroras 启用极光** 如果你想要晚上的极光效果，请打开这个，仅在您要使用效果时才打开。设置强度也只会使它们不可见，仍然会影响性能

**Auroras Intensity 极光强度**

**Auroras Speed 极光速度**

**Auroras Color 1 极光颜色1**

**Auroras Color 2 极光颜色2**

**Auroras Color 3 极光颜色3**

**Auroras Phase 极光形状**

**Auroras Intensity Curve 极光强度曲线**

**Volumetric Auroras Sample Count Scale 体积极光样品计数标度** 用途不详



**Component Selection And Mobility 组件选择和移动性**

**Sun Light 阳光** 如果将其设置为使用自定义Actor ，则使用在 “自定义阳光演员” 中选择的定向光 。

Use Built In Component 使用内置组件

Use Custom Actor 使用自定义参与者

Disable Completely 完全禁用

![img](https://pic1.zhimg.com/80/v2-6f6b70ed2b4ae20858763d12682103e0_720w.jpg)

**Custom Sun Light Actor 自定义日光Actor**

**Sun Mobility 太阳移动性** 如果您喜欢这种灯的静态/固定照明，请更改此设置。更改这些移动性设置之一后，可能需要在基本控件中单击 “刷新设置”。

**Moon Light 月光**

**Custom Moon Light Actor 自定义月光Actor**

**Moon Mobility 月亮移动性**

**Sky Light 天光**

**Custom Sky Light Actor 自定义天光Actor**

**Sky Light Mobility 天光移动性**

**Height Fog 高度雾**

**Custom Height Fog Actor 自定义高度雾Actor**



**Exposure 曝光**

**Use Exposure Range 使用曝光范围** 启用曝光值中应用它们的后处理组件。

**Exposure Min 最小曝光量**

**Exposure Max 最大曝光量**



**Readme 自述**

要阅读超动态天空自述文件，请在 “蓝图” 文件夹中找到自述文件资源。右键单击它，然后选择运行编辑器实用程序小部件。



**Legacy Coloring Settings 传统颜色设置**

**Manually Select Sun Color 手动选择日光颜色** 在高级设置中单独使用太阳颜色变量

**Horizon Base Color (Legacy Color Curve) 地平线基色（传统颜色曲线）**

**Cloud Dark Color (Legacy Color Curve) 云深色（传统颜色曲线）**

**Cloud Light Color (Legacy Color Curve) 云灯光颜色（传统颜色曲线）**

**Sun Color (Legacy Color Curve) 日光颜色（传统颜色曲线）**

**Sun Cloudy Color Curve 太阳云色曲线**

**Zenith Base Color (Legacy Color Curve) 天顶基色（传统颜色曲线）**

**Directional Intensity Curve 方向强度曲线**



**Advanced Settings (Legacy) 高级设置（传统）**

**Automatically Set Advanced Legacy Settings Using Time Of Day 使用一天中的时间自动设置高级传统设置**

**Stars Visibility 星星能见度**

**Moon Position 月球位置**

**Horizon Base Color 水平底色**

**Zenith Base Color 天顶基色**

**Cloud Light Color 云光颜色**

**Cloud Dark Color 云暗颜色**

**Sun Color 太阳色**

**Cloud Shine Intensity 太阳耀光强度**

**Sun Light Color 太阳光颜色**

**Sun Hightlight Radius 太阳高光半径** 云层周围锐利背光的半径

**Automatically Set Sun Light Rotation 自动设置阳光旋转**

**Sun Rotation 太阳自转** 如果没有提供定向光，则使用此旋转值。需要设置几天的时间直到改变天空的颜色

**Automatically Set Moon Light Rotation 自动设置月光旋转**



**Weather Connection 天气连接**

**Weather BP 天气蓝图**







如果想弄雾天，就在Height Fog当中来进行修改

Fog Density Clear是晴天时候的雾气浓度

Cloudy是多云天气时候的雾气浓度

Fog Start Distance when clear是晴天雾气的可见度

cloudy多云天雾气的可见度
