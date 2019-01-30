# 光照总览

为了计算一个3D物体的着色，Unity需要知道照在其上的灯光的强度、方向以及颜色。  

![圖](https://docs.unity3d.com/2018.1/Documentation/uploads/Main/LightBasic.svg "icon")  


这些属性都是由场景里的 `Light` 对象提供的。不同类型的光源以不同方式发射分配好的颜色；一些光源的光照强度会随着距离减弱，而且光照角度也会有不同的规则。Unity里可用的各种光源类型列在[光源类型](Types-of-light.md "Type of light")。  

Unity可以以各种方式计算复杂的、高级的光照效果，每种方式都适配不同使用场景。  

## 选择一个光照技术
通俗来讲，Unity里的光照可以被分为“实时光”和“预计算光”，两者可以联合使用来创造逼真的场景光照。  

这个章节我们会给出一个简单的介绍来阐述不同技术提供的可能性，以及他们的相对优势和不同的性能指标。  

## 实时光照
默认情况下，Unity里的太阳光（directional）、手电筒光（spot）和灯泡光（point）都是实时光。这意味着他们直接给场景提供直射光并在每帧更新。当光源和物体（GameObjects）在场景里移动的时候，光照可以立即更新。可以在scene视图和game视图观察到效果。  

![圖](https://docs.unity3d.com/2018.1/Documentation/uploads/Main/RealtimeSpotlight.png "icon")  

<font color=#A1A8AC>单独实时光的效果。请注意阴影是完全黑色的，因为没有反射光线。只有位于手电筒光的照射范围内的表面才会受光照影响。</font>  

实时光是场景里最基本的光照方式，用来照亮角色或其他移动的几何体是很有用的。  

不幸的是，Unity里实时光的光线是不会有反射的。为了用全局光照之类的技术来创造更真实的场景，我们需要启用Unity的预计算光解决方案。  

## 烘焙光照图

Unity可以计算复杂的静态光照效果（使用一个被称为全局光照（GI）的技术），并且将数据存入被称为光照图的纹理映射中。这个计算过程就被称为烘焙。  

当烘焙一个光照图时，场景里静态物体上的光照效果会被计算，且这个结果被写入纹理中，随后纹理会覆盖在场景几何体的表面来创造光照效果。  

![圖](https://docs.unity3d.com/2018.1/Documentation/uploads/GlobalIllumination/Lightmap.png "icon")  

<font color=#A1A8AC>左: 使用了光照贴图的场景。右: Unity生成的光照图纹理。请注意阴影和光照信息是如何被捕捉的。</font>  

这些光照图可能同时包含到达表面的平光和从场景里其他物体或表面反射过来的非平行光。这个光照纹理可以由物体材质上的shader联合表面信息比如颜色、法线一起使用。  

烘焙后的关照图是不能在运行时被改变的，所以也被称为“静态的”。实时光可以被附加地覆盖在烘焙过的场景上，但是不能改变光照图本身。  

用这种方法，我们可以在游戏运行时使用光照并有一个潜在的性能提升，同时适应电量更低的硬件设备比如移动平台。  

查看[光照窗口](Lighting-Window.md "Lighting Window")和[使用预计算光照](Using-precomputed-lighting.md "Using-precomputed-lighting")来获得更多信息。  

## 预计算实时全局光

静态光照图不能反映场景里光照条件的变化，而预计算实时全局光可以提供一个技术来更新复杂的场景的光照。  


With this approach it is possible to create lit environments featuring rich global illumination with bounced light which responds, in realtime, to lighting changes. A good example of this would be a time of day system - where the position and color of the light source changes over time. With traditional baked lighting, this is not possible.

![圖](https://docs.unity3d.com/2018.1/Documentation/uploads/GlobalIllumination/timeofdaycycle.gif "icon")  

<font color=#A1A8AC>A simple example of time of day using Precomputed Realtime GI.</font>  

In order to deliver these effects at playable framerates, we need to shift some of the lengthy number-crunching from being a realtime process to one which is precomputed.

Precomputing shifts the burden of calculating complex light behaviour from something that happens during gameplay, to something which can be calculated when time is no longer so critical. We refer to this as an ‘offline’ process.

For further information, please see the lighting and rendering tutorial.

## Benefits and costs

Although it is possible to simultaneously use Baked GI lighting and Precomputed Realtime GI, be wary that the performance cost of rendering both systems simultaneously is exactly the sum of them both. Not only do we have to store both sets of lightmaps in video memory, but we also pay the processing cost of decoding both in shaders.

The cases in which you may wish to choose one lighting method over another depend on the nature of your project and the performance capabilities of your intended hardware. For example, on mobile where video memory and processing power is more limited, it is likely that a Baked GI lighting approach would be more performant. On standalone computers with dedicated graphics hardware, or recent games consoles, it is quite possible to use Precomputed Realtime GI or even to use both systems simultaneously.

The decision on which approach to take will have to be evaluated based on the nature of your particular project and desired target platform. Remember that when targeting a range of different hardware, that often it is the least performant which will determine which approach is needed.