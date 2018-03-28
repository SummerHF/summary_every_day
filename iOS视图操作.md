#iOS视图操作
##寄宿图
###contentsScale
定义了寄宿图的像素尺寸和视图大小的比例,默认情况下是一个值为1.0的浮点数.该属性其实属于支持高分辨率屏幕机制的一部分.它用来判断在绘制图层的时候应该为寄宿图创建的空间大小,和需要显示的图片的拉伸度(假设并没有设置`contentsScale`).
如果`contentsScale`设置为1.0,将会以每个点1个像素绘制图片,如果设置为2.0，则会以每个点2个像素绘制图片.

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
`CALayer`存在于一个三维空间当中,除了我们已经讨论过的`position`和`anchorPoint`属性之外,`CALayer`还有另外两个属性,`zPosition`和`anchorPointZ`,二者都是在`Z`轴上描述位置的浮点类型.

