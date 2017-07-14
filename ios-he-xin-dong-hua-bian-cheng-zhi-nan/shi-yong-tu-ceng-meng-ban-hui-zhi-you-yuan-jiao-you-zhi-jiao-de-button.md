#使用图层蒙版绘制有圆角有直角的 button
##1. UIBezierPath和CAShapeLayer类
###1.1 CAShapeLayer
`CAShapeLayer`是一个通过矢量图形而不是bitmap来绘制的图层子类。你指定诸如颜色和线宽等属性，用CGPath来定义想要绘制的图形，最后`CAShapeLayer`就自动渲染出来了。

`CAShapeLayer`可以用来绘制所有能够通过CGPath来表示的形状。这个形状不一定要闭合，图层路径也不一定要不可破，事实上你可以在一个图层上绘制好几个不同的形状。你可以控制一些属性比如lineWith（线宽，用点表示单位），lineCap（线条结尾的样子），和lineJoin（线条之间的结合点的样子）。
###1.2 UIBezierPath类
`UIBezierPath`对象是`CGPathRef`数据类型的封装。path如果是基于矢量形状的，都用直线和曲线段去创建。我们使用直线段去创建矩形和多边形，使用曲线段去创建弧（arc），圆或者其他复杂的曲线形状。每一段都包括一个或者多个点，绘图命令定义如何去诠释这些点。**每一个直线段或者曲线段的结束的地方是下一个的开始的地方。**每一个连接的直线或者曲线段的集合成为subpath。一个UIBezierPath对象定义一个完整的路径包括一个或者多个subpaths。
   创建和使用一个path对象的过程是分开的。创建path是第一步，包含一下步骤：
（1）创建一个Bezier path对象。
（2）使用方法moveToPoint:去设置初始线段的起点。
（3）添加line或者curve去定义一个或者多个subpaths。
（4）改变UIBezierPath对象跟绘图相关的属性。
例如，我们可以设置stroked path的属性lineWidth和lineJoinStyle。也可以设置filled path的属性usesEvenOddFillRule。

   当创建path，我们应该管理path上面的点相对于原点（0，0），这样我们在随后就可以很容易的移动path了。为了绘制path对象，我们要用到stroke和fill方法。这些方法在current graphic context下渲染path的line和curve段。
   
> 推荐一个绘图工具`PaintCode`可以将你手动绘制的线段或者图形转化为swift 或者 OC 的代码，功能很强大
>![](/assets/PaintCode.png)

##2. 图层蒙版
###2.1 CALayer 的 mask 图层
mask图层真正重要的是图层的轮廓。mask属性就像是一个饼干切割机，mask图层实心的部分会被保留下来，其他的则会被抛弃。
如果mask图层比父图层要小，只有在mask图层里面的内容才是它关心的，除此以外的一切都会被隐藏起来。如下图：
![](/assets/2015-12-24_567bc1f47e2dd.png)

所以利用这个特性可以很容易的做出看似很难的UI需求

###3. 使用图层蒙版绘制有圆角有直角的 button
核心代码：
```ruby
UIButton *button1 = [UIButton buttonWithType:(UIButtonTypeSystem)];
    button1.frame = (CGRect){CGPointMake(50, 50),CGSizeMake(100, 40)};
    button1.backgroundColor = [UIColor redColor];
    
    [self.view addSubview:button1];
    CGSize radii = CGSizeMake(5, 5);
    // 控制哪个角为圆角
    UIRectCorner corners = UIRectCornerTopRight | UIRectCornerTopLeft;
    //create path
    UIBezierPath *path = [UIBezierPath bezierPathWithRoundedRect:button1.bounds byRoundingCorners:corners cornerRadii:radii];
    
    //create shape layer
    CAShapeLayer *shapeLayer = [CAShapeLayer layer];
    shapeLayer.strokeColor = [UIColor redColor].CGColor;
    shapeLayer.fillColor = [UIColor redColor].CGColor;
    shapeLayer.lineWidth = 1;
    shapeLayer.lineJoin = kCALineJoinRound;
    shapeLayer.lineCap = kCALineCapRound;
    shapeLayer.path = path.CGPath;
    [self.view.layer addSublayer:shapeLayer];
    // 设置图层蒙版
    button1.layer.mask = shapeLayer;
    
```


