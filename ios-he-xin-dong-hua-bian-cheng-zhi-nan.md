#1. 图层类
![
](/assets/4DE570CB-5433-4244-B419-5F6724DD3327.png)
##1.1CALayer的功能
阴影，圆角，带颜色的边框
3D变换
非矩形范围
透明遮罩
多级非线性动画

#2. 动画和计时类
核心动画的动画类使用基本的动画和关键帧动画把图层的内容和选取的属性动 画的显示出来。所有核心动画的动画类都是从 CAAnimation 类继承而来。CAAnimation 实现了 CAMediaTiming 协议，提供了动画的持续时间，速度，和重复计数。 CAAnimation 也实现了 CAAction 协议。该协议为图层触发一个动画动作提供了提供 标准化响应。动画类同时定义了一个使用贝塞尔曲线来描述动画改变的时间函数。
##2.1 CATransion类
CATransition 提供了一个图层变化的过渡效果，它能影响图层的整个内容。 动画进行的时候淡入淡出(fade)、推(push)、显露(reveal)图层的内容。这些过渡效果可以扩展到你自己定制的 Core Image 滤镜。

- CAAnimationGroup 允许一系列动画效果组合在一起，并行显示动画。  
- CAPropertyAnimation 是一个抽象的子类，它支持动画的显示图层的关键路 径中指定的属性。
- CABasicAnimation 简单的为图层的属性提供修改。
- CAKeyframeAnimation 支持关键帧动画，你可以指定的图层属性的关键路径动画，包括动画的每个阶段的价值，以及关键帧时间和计时功能的一系列值。在 动画运行是，每个值被特定的插入值替代。

#3. 图层的几何变换
##3.1 图层的属性
- 图层的 position 属性是一个 CGPoint 的值，它指定图层相当于它父图层的位置， 该值基于父图层的坐标系。
- 图层的 bounds 属性是一个 CGRect 的值，指定图层的大小(bounds.size)和图层的 原点(bounds.origin)。当你重写图层的重画方法的时候，bounds 的原点可以作为图形 上下文的原点。
- 图层拥有一个隐式的 frame,它是 position，bounds，anchorPoint 和 transform 属性 的一部分。设置新的 frame 将会相应的改变图层的 position 和 bounds 属性，但是 frame 本身并没有被保存。但是设置新的 frame 时候，bounds 的原点不受干扰，bounds 的大 小变为 frame 的大小，即 bounds.size=frame.size。图层的位置被设置为相对于锚点 (anchor point)的适合位置。当你设置 frame 的值的时候，它的计算方式和 position、 bounds、和 anchorPoint 的属性相关。
- 图层的 anchorPoint 属性是一个 CGPoint 值，它指定了一个基于图层 bounds 的符 合位置坐标系的位置。锚点(anchor point)指定了 bounds 相对于 position 的值，同 时也作为变换时候的支点。锚点使用单元空间坐标系表示，(0.0，0.0)点接近图层 的原点，而(1.0，1.0)是原点的对角点。改变图层的父图层的变换属性(如果存在 的话)将会影响到 anchorPoint 的方向，具体变化取决于父图层坐标系的 Y 轴。
- 当你设置图层的 frame 属性的时候，position 会根据锚点(anchorPoint)相应的改 变，而当你设置图层的 position 属性的时候，bounds 会根据锚点(anchorPoint)做相应 的改变。
##3.2 图层的几何变换
图层一旦创建，你就可以通过矩阵变换来改变一个图层的几何形状。 CATransform3D 的数据结构定义一个同质的三维变换(4x4 CGFloat 值的矩阵)，用于 图层的旋转，缩放，偏移，歪斜和应用的透视。
图层的两个属性指定了变换矩阵:transform 和 sublayerTransform 属性。图层的 transform 属性指定的矩阵结合图层的 anchorPoint 属性作用于图层和图层的子图层上 面。
你可以通过以下的任何一个方法改变 CATransform3D 的数据结构:   
- 使用CATransform3D函数  
- 直接修改数据结构的成员  
- 使用键-值编码改变键路径

> CATransform3DIdentity 是单位矩阵，该矩阵没有缩放、旋转、歪斜、透视。把该 矩阵应用到图层上面，会把图层几何属性修改为默认值。

> 旋转的单位采用弧度(radians),而不是角度(degress)。以下两个函数，你可以在 弧度和角度之间切换。

核心动画 提供了用于转换矩阵的变换函数 CATransform3DInvert。一般是用反转 点内转化对象提供反向转换。当你需要恢复一个已经被变换了的矩阵的时候，反转将 会非常有帮助。反转矩阵乘以逆矩阵值，结果是原始值。

变换函数同时允许你把 CATransform3D 矩阵转化为 CGAffineTransform(仿射) 矩阵，前提是 CATransform3D 矩阵采用如下表示方法。
1. CATransform3DMakeAffineTransform
2. CATransform3DIsAffine
3. CATransform3DGetAffineTransform

核心动画扩展了键-值编码协议，允许通过关键路径获取和设置一个图层的 CATransform3D 矩阵的值。表 4 描述了图层的 transform 和 sublayerTransform 属性的 相应关键路径。![](/assets/EB06291B-A0C8-412D-A36A-7B962A145604.png)
##3.3 图层的位置调整和大小改变
图层创建以后，你可以通过改变图层的几何属性:frame、bounds、position、 anchorPoint 和 zPosition 来编程式移动和改变图层大小。
如果一个图层的属性 needsDisplayOnBoundsChange 被设置为 YES 的时候，当图层 的 bounds 属性改变的时候，图层的内容将会被重新缓存起来。默认情况下图层的 needsDisplayOnBoundsChange 属性值为 NO。
默认情况下，设置图层的属性 frame、bounds、position、anchorPoint 和 zPosition 属性将会导致图层动画显示新值。

#4. 图层内容
##4.1 contents属性
图层的图片内容可以通过指定 contents 属性的值为 CGImageRef。当图层被创建 的时候或者在任何其他时候，这个操作可以在其他实体上面完成
##4.2 通过委托提供内容
你可以绘制图层的内容，或更好的封装图层的内容图片，通过创建一个委托类实现下列方法之一:

```ruby
displayLayer:
```
或 
```
drawLayer:inContext:
```
实现委托重绘的方法并不意味会自动的触发图层使用实现的方法来重绘内容。而 是你要显式的告诉一个图层实例来重新缓存内容，通过发送以下任何一个方法 `setNeedsDisplay` 或 者 `setNeedsDisplayInRect: `的 消 息 ， 或 者 把 图 层 的 `needsDisplayOnBoundsChange `属性值设置为 YES。通过委托实现方法 displayLayer:可以根据特定的图层决定显示什么图片，还可以设置图层的 contents 属性值。

#5. 动画
##5.1 隐式动画
核心动画的隐式动画模型假定所有动画图层属性的变化应该是渐进的和异步的。 动态的动画场景可以在没有显式的动画图层时候实现。改变可动画显示的图层的属性 将会导致图层隐式把图层从旧的值动画显示为新的值。虽然动画是持续的，但是设置 新的目标值时会导致图层从当前状态动画过渡到新的目标值。
i.e:

```ruby
 // animate theLayer's opacity to 0 while moving it // further away in the layer theLayer.opacity=0.0; theLayer.zPosition=-100;  // animate anotherLayer's opacity to 1 // while moving it closer in the layer anotherLayer.opacity=1.0;  anotherLayer.zPosition=100.0;
 
```
##5.2 显示动画
核心动画同时提供了一个显式的动画模型。该显式动画模型需要你创建一个动画 对象，并设置开始和结束的值。显式动画不会开始执行，直到你把该动画应用到某个 图层上面。

下面代码片段创建了一个显式动画，它实现一个层的不透明度从完 全不透明过渡到完全透明的，3 秒后返回重新执行。动画没有开始，直到它被添加到 某一图层层。
``` ruby
CABasicAnimation *theAnimation;
  theAnimation=[CABasicAnimation animationWithKeyPath:@"opacity"]; theAnimation.duration=3.0; theAnimation.repeatCount=2; theAnimation.autoreverses=YES; theAnimation.fromValue=[NSNumber numberWithFloat:1.0]; theAnimation.toValue=[NSNumber numberWithFloat:0.0]; 

```

##5.3 开始和显示动画
你可以发送 `addAnimation:forKey:`消息给目标图层来开始一个显式动画，把动画 和标识符作为参数。一旦把动画添加到目标图层，动画将会一直执行直到动画完成， 或者动画被从图层上面移除。把动画添加到图层时添加的标识符，同样也可以在停止 动画的时候使用，通过调用 `removeAnimationForKey:`。你可以通过给图层发送一个 `removeAllAnimations `消息来停止图层所有的动画。

#6. 事务
## 6.1 隐式事务
当图层树被没有获得事务的线程修改的时候将会自动创建隐式事务，当线程的运 行循环(run-loop)执行下次迭代的时候将会自动提交事务。

> 当在一个没有运行循环(runloop)的线程修改图层的属性的时候，你必须使用显式的事务。

## 6.2 显示事务
在你修改图层树之前，可以通过给 CATransaction 类发送一个 begin 消息来创建一 个显式事务，修改完成之后发送 comit 消息。显式事务在同时设置多个图层的属性的 时候(例如当布局多个图层的时候)，暂时的禁用图层的行为，或者暂时修改动画的 时间的时候非常有用。
###6.3 暂时禁用图层行为
你可以在修改图层属性值的时候通过设置事务的 `kCATransactionDisableActions` 值为 YES 来暂时禁用图层的行为。在事务范围所作的任何更改也不会因此而发生的动 画。下面显示了一个示例，当把 aLayer 从可视化图层树移除的时候禁用淡出动画。

``` ruby
 [CATransaction begin]; [CATransaction setValue:(id)kCFBooleanTrue forKey:kCATransactionDisableActions]; [aLayer removeFromSuperlayer];  [CATransaction commit];
```
###6.4 重载隐式动画时间
你可以暂时改变响应改变图层属性的动画的时间，通过设置事务的 `kCATransactionAnimationDuration` 键的值为新的时间。事务范围内所产生的任何动画 都会使用该新设置的时间值而不是他们原有的值。下面显示了一个示例，把动画的 发生时间改为 10 秒而不是`zPosition` 和 `opacity` 所指定的动画的默认时间。

``` ruby
[CATransaction begin]; [CATransaction setValue:[NSNumber numberWithFloat:10.0f] forKey:kCATransactionAnimationDuration]; theLayer.zPosition=200.0; theLayer.opacity=0.0;  [CATransaction commit];
```

###6.5 事务的嵌套
显式事务可以被嵌套，允许你禁用部分动画的行为或者在属性被修改的时候产生 的动画使用不同的时间。仅当最外层的事务被提交的时候，动画才会发生。下面显示了一个嵌套两个事务的例子。最外层的事务设置隐式动画的时间为 2 秒，并设置图层的 position 属性值。内层的事务设置隐式动画的时间为 5 秒，并修 改图层的 opacity 和 zPosition 属性值。
```ruby
[CATransaction begin]; // outer transaction
 // change the animation duration to 2 seconds [CATransaction setValue:[NSNumber numberWithFloat:2.0f] forKey:kCATransactionAnimationDuration]; // move the layer to a new position theLayer.position = CGPointMake(0.0,0.0);  [CATransaction begin]; // inner transaction // change the animation duration to 5 seconds [CATransaction setValue:[NSNumber numberWithFloat:5.0f] forKey:kCATransactionAnimationDuration];  // change the zPosition and opacity theLayer.zPosition=200.0; theLayer.opacity=0.0;  [CATransaction commit]; // inner transaction   [CATransaction commit]; // outer transaction
```

#7. 核心动画的键-值编码扩展
CALayer 和 CAAnimation 都是键-值编码兼容的容器类，允许你修改属性键对应的值。即使键为“someKey”对应的属性没有被定义，你也可以给“someKey”的键设置一个值，如下:
`[theLayer setValue:[NSNumber numberWithInteger:50] forKey:@"someKey"];`
你可以通过下面的代码检索“someKey”对应的值:          `someKeyValue=[theLayer valueForKey:@"someKey"];`


