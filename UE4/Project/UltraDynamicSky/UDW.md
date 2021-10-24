***Ultra_Dynamic_Weather翻译及功能概述\***



***Basic Controls 基本控制\***



***Weather Type 天气类型\*** (从该列表中选择一个预设，它将应用于基本天气设置。)

Clear Skies 晴空万里

Partly Cloudy 部分多云

Cloudy 多云

Overcast 阴天

Foggy 雾

Light Rain 小雨

Rain 下雨

Thunderstorm 雷雨

Light Snow 小雪

Snow 下雪

Blizzard 暴风雪

Select Preset 选择预设

![img](https://pic3.zhimg.com/80/v2-6483b34468cf08043600a35c8bfb121e_720w.jpg)

***Weather Intensity 天气强度\*** 降水量，雪或雨。该值受云量限制。

***Cloud Coverage 云层覆盖率\*** 天空中的云量，从晴朗到阴云密布到有雾。（云量低于5将不会有雨）

***Rain/Snow 雨/雪\*** 决定是下雨还是下雪。0是雨，1是雪，0.5是两者的混合

***Wind Intensity 风力强度\***

***Wind Direction 风向\*** 风的偏航

***Cloud Speed Multiplier 云速度倍增器\*** 云速由风强度决定，然后将其乘以该变量。



***Rain And Snow Particles 雨雪粒子\***



***Enable Rain And Snow Particles 启用雨雪粒子\*** 此设置启用/禁用雨雪粒子系统

***Max Particle Spawn Rate 最大粒子繁殖率\*** 这个数字相当于天气最恶劣时每秒产生的粒子数量。当前效果质量可伸缩性设置将进一步修改此数字

***Particle Collision Enabled 启用粒子碰撞\*** 启用雨和雪粒子的碰撞检查。这样可以防止雨雪穿过屋顶或墙壁。它还用于在下雨时产生飞溅粒子

***Camera Forward Spawn Offset 摄影机正向生成偏移\*** 偏移“繁殖原点”（spawn origin）或“雨和雪”（rain and snow）使其位于摄影机前面，以便通常较少的粒子放置在播放器后面

***Celling Check Height 天花板检查高度\*** 当尝试繁殖一个新的雨或雪粒子时，这是它将在上面检查是否可以繁殖的距离 (用途不详）

***Spawn Box Height 繁殖范围高度\*** 冰雪粒子发射器的高度。这决定了雨和雪在玩家相机上方产生的高度

***Max Spawn Distance 最大繁殖距离\*** 最大距离的雨和雪粒子将从玩家相机中产生。请注意，繁殖距离分布不是线性的。与在该范围内更近的粒子相比，在该最大距离处产生的粒子很少。

***Spawn Distance Distribution 产卵距离分布\***

***Minimun Particle Distance 最小粒子距离\*** 简单地剔除比这个距离更接近相机的粒子

***Rain Drops Scale 雨滴尺寸\***

***Snow Flakes Scale 雪花尺寸\***

***Rain Drops Alpha 雨滴透明度\***

***Snow Fakes Alpha 雪花透明度\***

***Rain/Snow Particle Collision Channel 雨/雪粒子碰撞通道\*** 检查碰撞时雨雪粒子将使用的碰撞通道。雨雪碰撞对它有很大的延迟，因此作为一种优化，它在静态表面上工作得很好，但在移动表面上工作得不好。尝试选择一个被移动对象忽略的碰撞通道

***Splash Frequency 飞溅频率\*** 会产生飞溅颗粒的雨水颗粒的比例。

***Snow Flakes Stick To Surfaces 雪花粘在表面上\*** 启用此选项后，雪花将在遇到向上碰撞时停止，并保持其生命周期直至结束



***Random Weather Variation 随机天气变化\***



***Season 季节 “\***当前季节”确定系统下面的概率图将用于选择随机天气类型

***Weather Type Change Interval 天气类型变化间隔\*** 范围定义了随机天气改变其类型的频率。它选择的新类型由下面的概率值决定

***Weather Type Probabilities(Summer) 天气类型概率（夏季）\*** 这是每种天气的概率值图。每个值与其余值成比例。例如，如果一个类型设置为5，而另一个类型设置为1。第一个被选中的可能性要高出5倍。永远不会选择0值

***Weather Type Probabilities(Autumn) 天气类型概率（秋季）***同上

***Weather Type Probabilities(Winter) 天气类型概率（冬季）***同上

***Weather Type Probabilities(Spring) 天气类型概率（春季）***同上

***Avoid Extreme Weather Shifts 避免极端天气变化*** 系统将避免做过于极端的变化，如从一个晴朗的天空到一个完整的雷雨

***Avoid Repeating Weather Types 避免重复的天气类型*** 系统将避免连续多次选择天气类型

***Avoid Changing Directly From Snow To Rain (or Vice Versa) 避免直接从雪变为雨（反之亦然）***系统将避免进行从主动下雪到主动下雨或相反的更改

***Cloudiness Change Speed 云量变化速度***

***Weather Intensity Change Speed 天气强度变化速度***

***Rain/Snow Change Speed 雨/雪变化速度***

***Wind Intensity Change Speed 风力强度变化速度***

***Change Speed Scale 更改速度比例***



***Material Effects 材质影响***



***Material Wetness 材质湿度\*** 控制“潮湿天气效果”材质函数使用的共享材质参数，用于在下雨时使材质变湿 （具体可参考文章开头链接）

***Material Snow Coverage 材质积雪覆盖率\*** 控制“雪天气效果”材质函数使用的共享材质参数，用于使材质被雪覆盖

***Simulate Changing Material Effects With Weather 模拟随天气变化的材质效果\*** 启用或禁用受雨、雪、温度和阳光影响的材质湿度和饱和度的动态模拟。要使用这些效果，必须使用“潮湿天气效果”或“降雪天气效果”材质函数，或使用“超动态天气参数”中的参数。

***Time For Materials To Get Wet 材质淋湿时间\*** 如果其降雨和天气强度达到最大值，这是材质从0到1湿度所需的时间量

***Time For Materials To Dry Out 材质干燥时间\*** 这是如果太阳出来，材质从1到0湿度所需的时间

***Time For Materials To Gather Snow 材质积雪时间\*** 如果下雪和天气强度最大，这是材质从下雪到积雪所需的时间

***Time For Snow To Melt 雪融化的时间到了\*** 如果太阳出来了，这是积雪从1到0所花费的时间。

***Amount Of Snow To Turn To Wetness When Melted 融化后变湿的雪量\*** 当积雪覆盖率减少时，可以将减少的积雪覆盖率添加到湿度中，以模拟积雪融化。这就控制了有多少雪会变成湿的



***Sound Effects 音效\***



***Use Sound Effects 使用声音效果\***

***Rain Volume 降雨音量\***

***Distant Thunder Volume 远雷声量\***

***Close Thunder Volume 近雷声量\***

***Wind Volume 风音量\***

***Wind Whistling Volume 风笛声\***



***Lightning 闪电\***



***Spawn Lightning Flashes 产生闪电\*** 当天气降水量大时，会定期产生大的闪电。请注意，这些只会在游戏运行时生成。编辑器中可见的较小的模糊闪电 在本节（细节面板）的底部进行控制。

***Lightning Flash Interval Random Range 闪电间隔随机范围\*** 这定义了闪电最活跃时使用的随机范围

***Lightning Flash Light Source 闪电光源\*** 启用或禁用伴随闪电的定向光源

***Lightning Flash Light Source Color 闪电光源颜色\*** 闪电光源的颜色

***Maximun Lightning Flash Light Intensity 最大闪电强度\*** 最近的闪电的光强度

***Lightning Flashes Cast Shadows 闪电投下阴影\*** 启用或禁用闪电光源投射的阴影

***Lightning Flashes Cast Light Shaft Bloom 闪电射出光柱\*** 启用或禁用闪电光源使用的光柱开花效果。

***Lightning Flash Light Shaft Intensity 闪电光轴强度\*** 如果启用，光柱开花的强度

***Lighting Flash Distance 照明闪光距离\*** 这是闪电产生的随机距离。

***Enable Obscured Lightning 启用遮挡闪电\*** 启用或禁用较小的、模糊的闪电。这些与主要的闪电是分开的。

***Obscured Lightning Spawn Interval (Rain) 模糊闪电粒子繁殖数量间隔（雨）\*** 没有关联光或声音的远程/模糊闪电片的最大繁殖率。该值在下雨时使用

***Obscured Lightning Spawn Interval (Snow) 模糊闪电粒子繁殖数量间隔（雪）***没有关联光或声音的远程/模糊闪电片的最大繁殖率。该值在下雪时使用

***Lightning Flash Angle Range 闪电角度范围\*** 闪电与前方的最大偏差

***Lightning Flash Height 闪电高度\*** 闪电会产生的高度。

***Spawn Lightning Flashes During Snow 在下雪期间产生闪电\*** 在下雪的天气中使用闪电的开关



***Volumetric Fog Particles 体积雾粒子\***



***Enable Fog Particles 启用雾粒子\*** 启用或禁用伴随大雨和大雪的体积雾颗粒。只有当您使用体积雾时，这些才可见

***Max Fog Particle Percentage (Rain) 最大雾粒子百分比（雨）\*** 当天气强度达到最大值时，伴随雾粒子的雨粒子百分比

***Max Fog Particle Percentage (Snow) 最大雾粒子百分比（雪）\*** 当天气强度达到最大值时，伴随雾粒子的雪粒子百分比

***Fog Particle Intensity (Rain) 雾粒子强度（雨）\*** 雨雾粒子的最大强度。这将导致灭绝

***Fog Particle Intensity (Snow) 雾粒子强度（雪）雪\***雾粒子的最大强度。这将导致灭绝

***Fog Particles Draw Distance 雾粒子绘制距离\*** 绘制雾粒子的最大距离

***Fog Particles Camera Offset 雾粒子摄影机偏移\*** 当玩家在室内时，将雾粒子从相机偏移可以帮助阻止雾粒子穿过墙壁。



***Initial Settings 初始设置\***



***Use Initial Settings 使用初始设置\*** 这决定了系统在运行游戏时是否使用了来自基本控件类别的初始设置。如果没有，它将以随机变化开始

***Time To Hold Initial Settings 保持初始设置的时间\*** 保持初始设置 (来自基本控件类别) 的时间。如果也没设置，则将无限期地保持它们。

***Time to Transition From Initial Settings To Random Variation 从初始设置到随机变化的过渡时间\*** 一旦初始设置保持了完整的持续时间，这就是系统转换到随机变化模式所需的时间



***Wind Actor 风actor***

***Wind Directional Source Actor (For Speed Tree) 风向源actor（Speed Tree软件）\***



***UDS Connection UDS连接\***

***Ultra Dynamic Sky 超动态天空\***