#变换
##仿射变换 CGAffineTransform （Core Graphics框架 2d 绘图api）
当对图层应用变换矩阵，图层矩形内的每一个点都被相应地做变换，从而形成一个新的四边形的形状。
> CGAffineTransform中的“仿射”的意思是无论变换矩阵用什么值，图层中平行的两条线在变换之后任然保持平行，CGAffineTransform可以做出任意符合上述标注的变换

##3D变换 CATransform3D
```ruby
struct CATransform3D
     {
     CGFloat m11（x缩放）, m12（y切变）, m13（旋转）, m14（）;
     CGFloat m21（x切变）, m22（y缩放）, m23（）, m24（）;
     CGFloat m31（旋转）, m32（）, m33（）, m34（透视效果，要操作的这个对象要有旋转的角度，否则没有效果。正直/负值都有意义）;
     CGFloat m41（x平移）, m42（y平移）, m43（z平移）, m44（）;
     };
     ps:整体比例变换时，也就是m11==m22时，若m33>1，图形整体缩小，若0<m33<1，图形整体放大，若s<0,发生关于原点的对称等比变换。
     首先要实现view（layer）的透视效果（就是近大远小），是通过设置m34的：
     CATransform3D rotationAndPerspectiveTransform = CATransform3DIdentity;
     rotationAndPerspectiveTransform.m34 = 1.0 / -500;
     m34负责z轴方向的translation（移动），m34= -1/D,  默认值是0，也就是说D无穷大，这意味layer in projection plane（投射面）和layer in world coordinate重合了。
     D越小透视效果越明显。
     所谓的D，是eye（观察者）到投射面的距离。
```
m34。m34用于按比例缩放X和Y的值来计算到底要离视角多远。