#CAShapeLayer
CAShapeLayer可以用来绘制所有能够通过CGPath来表示的形状。这个形状不一定要闭合，图层路径也不一定要不可破，事实上你可以在一个图层上绘制好几个不同的形状。你可以控制一些属性比如lineWith（线宽，用点表示单位），lineCap（线条结尾的样子），和lineJoin（线条之间的结合点的样子）。
使用UIBezierPath帮助类创建图层路径，这样我们就不用考虑人工释放CGPath了。
使用UIBezierPath创建圆：
```ruby
- (void)addArcWithCenter:(CGPoint)center radius:(CGFloat)radius startAngle:(CGFloat)startAngle endAngle:(CGFloat)endAngle clockwise:(BOOL)clockwise
可以画出一段弧线。
看下各个参数的意义：
center：圆心的坐标
radius：半径
startAngle：起始的弧度
endAngle：圆弧结束的弧度
clockwise：YES为顺时针，No为逆时针
```
方法里面主要是理解startAngle与endAngle，，比如弧度为0的话，是从上下左右哪个点开始算
看了下面这张图就明了了
![](/assets/1361069-9396924d2f620514.png)
看出0Pi就是指圆最右边开始计算的，顺时针依次为M_PI/2,M_PI,M_PI*1.5
明白这个，用BezierPath画圆弧就简单了
比如要画上图加粗的那段就是：
```ruby
UIBezierPath *path = [[UIBezierPath alloc] init];    
[path addArcWithCenter:center    
                radius:radius    
            startAngle:M_PI*1.1    
              endAngle:M_PI*1.9    
             clockwise:YES];
             ```