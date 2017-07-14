#变换
##仿射变换 CGAffineTransform （Core Graphics框架 2d 绘图api）
当对图层应用变换矩阵，图层矩形内的每一个点都被相应地做变换，从而形成一个新的四边形的形状。
> CGAffineTransform中的“仿射”的意思是无论变换矩阵用什么值，图层中平行的两条线在变换之后任然保持平行，CGAffineTransform可以做出任意符合上述标注的变换

##3D变换

m34。m34用于按比例缩放X和Y的值来计算到底要离视角多远。