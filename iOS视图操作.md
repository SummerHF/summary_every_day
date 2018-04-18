#iOS视图操作
##寄宿图
###contentsScale
定义了寄宿图的像素尺寸和视图大小的比例,默认情况下是一个值为1.0的浮点数.该属性其实属于支持高分辨率屏幕机制的一部分.它用来判断在绘制图层的时候应该为寄宿图创建的空间大小,和需要显示的图片的拉伸度(假设并没有设置`contentsGravity`).
如果`contentsScale`设置为1.0,将会以每个点1个像素绘制图片,如果设置为2.0，则会以每个点2个像素绘制图片`2*2`.

###CALayerDelegate
不同于`UIView`，当图层显示在屏幕上时,`CALayer`不会自动重绘它的内容.它把决定权交给了开发者.
尽管没有使用`maskToBounds`属性,绘制的那个圆仍然沿边界被裁减了.这是因为当你使用`CALayerDelegate`绘制寄宿图的时候,并没有对超出边界外的内容提供支持.

注意,当`UIView`创建了它的宿主图层时,它就会自动的把图层的代理设置为自己,并提供了一个`displayLayer`的实现.

```
 	func draw(_ layer: CALayer, in ctx: CGContext) {
        ctx.setLineWidth(1)
        ctx.setStrokeColor(UIColor.red.cgColor)
        ctx.strokeEllipse(in: layer.bounds)
    }
```

##图层几何学
`UIView`有三个比较重要的布局属性:`frame`,`bounds`,`center`，`CALayer`对应的叫`frame`,`bounds`和`position`.为了能够区分,图层用了`position`，视图用了`center`,但他们都代表同样的值.

`center`和`position`都代表了相对于父图层`anchorPoint`所在的位置.

对于视图或者图层来说,`frame`并不是一个非常清晰的属性.它其实是一个虚拟属性,是根据`bounds`，`position`和`transform`计算而来.

###锚点
改变`anchorPoint`,`position`属性保持固定的值并没有发生改变,但是`frame`却移动了.

###Z坐标轴
`CALayer`存在于一个三维空间当中,除了我们已经讨论过的`position`和`anchorPoint`属性之外,`CALayer`还有另外两个属性,`zPosition`和`anchorPointZ`,二者都是在`Z`轴上描述位置的浮点类型.`zPosition`可以决定视图的层级结构.`anchorPointZ`是用来移动图层的把柄

###Hit Testing 
####containsPoint
`图层树`证实了最好使用图层相关视图,而不是创建独立的图层关系.其中一个原因就是要处理额外复杂的触摸事件或者手势.但是它有一系列的方法帮你处理事件: `-containsPoint`和`hitTest`.
`-containsPoint`: 接受一个在本图层坐标系下的`CGPoint`,如果这个点在图层`frame`范围内就返回`YES`.注意,是在对应图层的坐标系下,需要进行坐标系的转换.
####hitTest
它返回图层本身,或者包含这个坐标点的叶子节点图层.
当调用图层的`-hitTest`方法时,测算的顺序严格依赖于图层树当中的图层顺序.这意味着如果改变了图层的`z`轴顺序,你会发现将不能检测到最前方的视图点击事件,这是因为被另一个图层遮盖住了.

##自动布局
`UIViewAutoresizingMask`类型的一些常量, 应用于当父视图改变尺寸的时候,相应`UIView`的`frame`也跟着更新的场景.(通常用于横竖屏切换)

`自动排版机制`不同于`自动调整`,并且更加复杂.

当使用视图的时候,可以充分利用`UIView`类接口暴露出来的`UIViewAutoresizingMask`和`NSLayoutConstraint API`,但如果想随意控制`CALayer`的布局,就需要手工操作. 最简单的方法就是用`CALayerDelegate`如下函数:

```

    func layoutSublayers(of layer: CALayer) {

    }
```

当图层的`bounds`发生改变,或者图层的-`setNeedsLayout`方法被调用的时候,这个函数将会被执行.

##mask属性
它类似于一个子图层,相对于父图层(即拥有该属性的图层),但是它却不是一个普通的子图层.不同于那些绘制在父图层中的子图层,`mask`图层定义了父图层的部分可见区域.`mask`图层的`Color`属性是无关紧要的,真正重要的是图层的轮廓,其他的则会被抛弃.

如果`mask`图层比父图层要小,只有在`mask`图层里面的内容才是它关心的,除此以外的一切都会被隐藏起来.


```
content.layer.minificationFilter /// 缩小图片
content.layer.magnificationFilter /// 放大图片


kCAFilterTrilinear     /// 三线性滤波算法
kCAFilterNearest       /// 双线性滤波算法
kCAFilterLinear       ///  最近过滤算法 
```

##专用图层
###CAShapeLayer
###CATextLayer
它以图层的形式包含了`UILable`几乎所有的绘制特性,并且额外提供了一些新的特性.同时,`CATextLayer`也要比`UILable`渲染的快的多.
###CATransformLayer
###CAEmitterLayer
高性能的粒子引擎,`CAEmitterLayer`被用来创建实时例子动画.如烟雾,火,雨...`CAEmitterLayer`看上去像是许多`CAEmitterCell`的容器,这些`CAEmitterCell`定义了一个例子效果.
###CAEAGLLayer
它是`CALayer`的一个字类,用来显示任意的`OpenGL`图形
###AVPlayerLayer

##隐式动画
###事务
但当你改变一个属性,`Core Animation`是如何判断动画类型和持续时间的咧?实际上动画执行的时间
取决于当前事物的设置,动画类型取决于图层行为.

事务是通过`CATransaction`类来做管理.
使用`begin`和`commit`分别来入栈或者出栈

`Core Animation`在每一个`run loop`周期中自动开始一次新的事务,`run loop`是`iOS`负责收集用户输入,处理定时器或者网络事件并去重新绘制屏幕的东西.

###图层行为


##显示动画
###CABasicAnimation
###CAKeyframeAnimation
`关键帧动画`: 你提供了显著的帧,然后`Core Animation`在每帧之间进行插入.
###虚拟属性
不是针对关键路径
`transform.rotation`
###动画组
`CAAnimationGroup`可以将动画组合起来.
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
dsdsdsdsd





### 组透明
`shouldRasterize`: 栅格化

##视觉效果
###变换
我们将要研究可以用来对图层旋转，摆放或者扭曲的`CGAffineTransform`
以及可以将扁平物体转换为三维空间对象的`CATransform3D`(而不是仅仅对圆角矩形添加下沉阴影)


###仿射变换

`CGAffineTransform` 是一个可以和二维空间向量做乘法的3*3矩阵

一个`pi`代表`180度`,`M_PI_4`于是就是`pi`的四分之一就是45度.

###混合变换
`CGAffineTransform`类型属于`Core Graphics`框架,`Core Graphics`实际上是一个严格意义上的`2D绘图API`,并且`CGAffineTransform`仅仅对`2D`变换有效.

###3D变换
`CATransform3D`也是一个矩阵,但是和`2*3`的矩阵不同,`CATransform3D`是一个在3维空间内做变换的`4*4`矩阵.

###透视投影

`m34`:用于按照比例缩放`X`和`Y`的值来计算到底要距离视角多远

通过设置`m34`为`-1.0/d`来应用透视效果,`d`代表了想象中视角相机和屏幕之间的距离,以像素为单位.

###灭点
`sublayerTransform`影响所有的子图层.

###背面
`CALayer`有一个叫做`doubleSided`的属性来控制图层的背面是否要被绘制.

##扁平化图层

