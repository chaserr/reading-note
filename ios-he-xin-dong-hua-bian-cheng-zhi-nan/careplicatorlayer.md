# CAReplicatorLayer
重复图层
i.e
用来生成反射倒影
这里封装一个简单的倒影 view，

.h文件
```ruby
#import <QuartzCore/QuartzCore.h>
IB_DESIGNABLE // 动态刷新
@interface RefilpView : UIView

@property (nonatomic, assign) IBInspectable CGFloat reflectionGap;

@property (nonatomic, assign) IBInspectable CGFloat reflectionScale;
@property (nonatomic, assign) IBInspectable CGFloat reflectionAlpha;
@property (nonatomic, assign) IBInspectable BOOL dynamic;

- (void)update;

@end
```

.m文件
```ruby
@interface RefilpView ()
@property (nonatomic, strong) CAGradientLayer *gradientLayer;
@property (nonatomic, strong) UIImageView *reflectionView;
@end

@implementation RefilpView

// 重新制定 view 的寄宿图层
+ (Class)layerClass
{
    return [CAReplicatorLayer class];
}

- (void)update
{
    if (_dynamic)
    {
        //remove gradient view
        [_reflectionView removeFromSuperview];
        self.reflectionView = nil;
        
        //update instances
        CAReplicatorLayer *layer = (CAReplicatorLayer *)self.layer;
        layer.shouldRasterize = YES;
        layer.rasterizationScale = [UIScreen mainScreen].scale;
        layer.instanceCount = 2;
        CATransform3D transform = CATransform3DIdentity;
        transform = CATransform3DTranslate(transform, 0.0, layer.bounds.size.height + _reflectionGap, 0.0);
        transform = CATransform3DScale(transform, 1.0, -1.0, 0.0);
        layer.instanceTransform = transform;
        layer.instanceAlphaOffset = _reflectionAlpha - 1.0;
        
        //create gradient layer
        if (!_gradientLayer)
        {
            _gradientLayer = [[CAGradientLayer alloc] init];
            self.layer.mask = _gradientLayer;
            _gradientLayer.colors = @[(__bridge id)[UIColor blackColor].CGColor,
                                      (__bridge id)[UIColor blackColor].CGColor,
                                      (__bridge id)[UIColor clearColor].CGColor];
        }
        
        //update mask
        [CATransaction begin];
        [CATransaction setDisableActions:YES]; // don't animate
        CGFloat total = layer.bounds.size.height * 2.0 + _reflectionGap;
        CGFloat halfWay = (layer.bounds.size.height + _reflectionGap) / total - 0.01;
        _gradientLayer.frame = CGRectMake(0.0, 0.0, self.bounds.size.width, total);
        _gradientLayer.locations = @[@0.0, @(halfWay), @(halfWay + (1.0 - halfWay) * _reflectionScale)];
        [CATransaction commit];
    }
    else
    {
        //remove gradient layer
        self.layer.mask = nil;
        self.gradientLayer = nil;
        
        //update instances
        CAReplicatorLayer *layer = (CAReplicatorLayer *)self.layer;
        layer.shouldRasterize = NO;
        layer.instanceCount = 1;
        
        //create reflection view
        if (!_reflectionView)
        {
            _reflectionView = [[UIImageView alloc] initWithFrame:self.bounds];
            _reflectionView.contentMode = UIViewContentModeScaleToFill;
            _reflectionView.userInteractionEnabled = NO;
            [self addSubview:_reflectionView];
        }
        
        //get reflection bounds
        CGSize size = CGSizeMake(self.bounds.size.width, self.bounds.size.height * _reflectionScale);
        if (size.height > 0.0 && size.width > 0.0)
        {
            //create gradient mask
            UIGraphicsBeginImageContextWithOptions(size, YES, 0.0);
            CGContextRef gradientContext = UIGraphicsGetCurrentContext();
            CGFloat colors[] = {1.0, 1.0, 0.0, 1.0};
            CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceGray();
            CGGradientRef gradient = CGGradientCreateWithColorComponents(colorSpace, colors, NULL, 2);
            CGPoint gradientStartPoint = CGPointMake(0.0, 0.0);
            CGPoint gradientEndPoint = CGPointMake(0.0, size.height);
            CGContextDrawLinearGradient(gradientContext, gradient, gradientStartPoint,
                                        gradientEndPoint, kCGGradientDrawsAfterEndLocation);
            CGImageRef gradientMask = CGBitmapContextCreateImage(gradientContext);
            CGGradientRelease(gradient);
            CGColorSpaceRelease(colorSpace);
            UIGraphicsEndImageContext();
            
            //create drawing context
            UIGraphicsBeginImageContextWithOptions(size, NO, 0.0);
            CGContextRef context = UIGraphicsGetCurrentContext();
            CGContextScaleCTM(context, 1.0, -1.0);
            CGContextTranslateCTM(context, 0.0, -self.bounds.size.height);
            
            //clip to gradient
            CGContextClipToMask(context, CGRectMake(0.0, self.bounds.size.height - size.height,
                                                    size.width, size.height), gradientMask);
            CGImageRelease(gradientMask);
            
            //draw reflected layer content
            [self.layer renderInContext:context];
            
            //capture resultant image
            _reflectionView.image = UIGraphicsGetImageFromCurrentImageContext();
            UIGraphicsEndImageContext();
        }
        
        //update reflection
        _reflectionView.alpha = _reflectionAlpha;
        _reflectionView.frame = CGRectMake(0, self.bounds.size.height + _reflectionGap, size.width, size.height);
    }
}

- (void)setUp
{
    //set default properties
    _reflectionGap = 4.0;
    _reflectionScale = 0.5;
    _reflectionAlpha = 0.5;
    
    //update reflection
    [self setNeedsLayout];
}

- (id)initWithCoder:(NSCoder *)aDecoder
{
    if ((self = [super initWithCoder:aDecoder]))
    {
        [self setUp];
    }
    return self;
}

- (id)initWithFrame:(CGRect)frame
{
    if ((self = [super initWithFrame:frame]))
    {
        [self setUp];
    }
    return self;
}

- (void)setReflectionGap:(CGFloat)reflectionGap
{
    _reflectionGap = reflectionGap;
    [self setNeedsLayout];
}

- (void)setReflectionScale:(CGFloat)reflectionScale
{
    _reflectionScale = reflectionScale;
    [self setNeedsLayout];
}

- (void)setReflectionAlpha:(CGFloat)reflectionAlpha
{
    _reflectionAlpha = reflectionAlpha;
    [self setNeedsLayout];
}

- (void)setDynamic:(BOOL)dynamic
{
    _dynamic = dynamic;
    [self setNeedsLayout];
}

- (void)layoutSubviews
{
    [self update];
    [super layoutSubviews];
}

@end
```
直接在该自定义的 view上添加imageview 即可实现图像倒影