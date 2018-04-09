#Image I/O

[图片渲染性能优化](https://www.jianshu.com/p/b0b7c3d140fa)
[imageIO](http://blog.mrriddler.com/2016/07/20/iOS%E4%B8%AD%E7%9A%84imageIO%E4%B8%8Eimage%E8%A7%A3%E7%A0%81/)
[加载动画](https://github.com/NghiaTranUIT/FeSpinner)
[持续化集成方案](https://www.jianshu.com/p/3b4a131653e0)

##图片加载流畅

* 使用 +imageWithContentsOfFile: 方法从磁盘中加载一张图片，这个时候的图片并没有解压缩
* 将生成的 UIImage 赋值给 UIImageView
* 一个隐式的 CATransaction 捕获到了 UIImageView 图层树的变化
* 在主线程的下一个 run loop 到来时，Core Animation 提交了这个隐式的 transaction ，这个过程可能会对图片进行 copy 操作，而受图片是否字节对齐等因素的影响，这个 copy 操作可能会涉及以下部分或全部步骤
	* 分配内存缓冲区用于管理文件 IO 和解压缩操作
	* 将文件数据从磁盘读到内存中
	* 将压缩的图片数据解码成未压缩的位图形式，这是一个非常耗时的 CPU 操作
	* 最后 Core Animation 使用未压缩的位图数据渲染 UIImageView 的图层
* 常用的解码就是对图片进行重新绘制，得到一张新的解压缩后的位图。其中，用到的最核心的函数是 CGBitmapContextCreate 

```
 /**
     data: 如果不为 NULL ，那么它应该指向一块大小至少为 bytesPerRow * height 字节的内存；如果 为 NULL ，那么系统就会为我们自动分配和释放所需的内存，所以一般指定 NULL 即可
     width: 位图的宽度和高度，分别赋值为图片的像素宽度和像素高度即可
     height: 像素的每个颜色分量使用的 bit 数，在 RGB 颜色空间下指定 8 即可
     bitsPerComponent: 像素的每个颜色分量使用的 bit 数，在 RGB 颜色空间下指定 8 即可
     bytesPerRow: 位图的每一行使用的字节数，大小至少为 width * bytes per pixel 字节。有意思的是，当我们指定 0 时，系统不仅会为我们自动计算，而且还会进行 cache line alignment 的优化
     space: 就是我们前面提到的颜色空间，一般使用 RGB 即可
     bitmapInfo: 就是我们前面提到的位图的布局信息
     */
    CGBitmapContextCreate(<#void * _Nullable data#>, <#size_t width#>, <#size_t height#>, <#size_t bitsPerComponent#>, <#size_t bytesPerRow#>, <#CGColorSpaceRef  _Nullable space#>, <#uint32_t bitmapInfo#>)
```
--------

* 图片编码和解码
* 递增加载图片
* 支持图片元数据
* 高效缓存


``` c
    /// CGImageSourceCopyTypeIdentifiers: 返回一个数组.数据里面还有“唯一类型标识”来表示imageIO支持的图片文件类型
    CFArrayRef mySourceTypes = CGImageSourceCopyTypeIdentifiers();
    CFShow(mySourceTypes);
    /**
     (
     "public.jpeg",
     "public.png",
     "com.compuserve.gif",
     "com.canon.tif-raw-image",
     "com.adobe.raw-image",
     "com.dxo.raw-image",
     "com.canon.cr2-raw-image",
     "com.leafamerica.raw-image",
     "com.hasselblad.fff-raw-image",
     "com.hasselblad.3fr-raw-image",
     "com.nikon.raw-image",
     "com.nikon.nrw-raw-image",
     "com.pentax.raw-image",
     "com.samsung.raw-image",
     "com.sony.raw-image",
     "com.sony.sr2-raw-image",
     "com.sony.arw-raw-image",
     "com.epson.raw-image",
     "com.kodak.raw-image",
     "public.tiff",
     "public.jpeg-2000",
     "com.apple.atx",
     "org.khronos.astc",
     "org.khronos.ktx",
     "com.canon.crw-raw-image",
     "com.fuji.raw-image",
     "com.panasonic.raw-image",
     "com.panasonic.rw2-raw-image",
     "com.leica.raw-image",
     "com.leica.rwl-raw-image",
     "com.konicaminolta.raw-image",
     "com.olympus.sr-raw-image",
     "com.olympus.or-raw-image",
     "com.olympus.raw-image",
     "com.phaseone.raw-image",
     "com.microsoft.ico",
     "com.microsoft.bmp",
     "com.apple.icns",
     "com.adobe.photoshop-image",
     "com.microsoft.cur",
     "com.truevision.tga-image",
     "com.ilm.openexr-image",
     "public.radiance",
     "public.pbm",
     "public.mpo-image",
     "public.pvr"
     )
     */

    /// CGImageDestinationCopyTypeIdentifiers: 返回一个数组，数组中包含着一系列“位置类型标识”用来标识可以左右image destination的图片文件类型
    CFArrayRef myDestinationTypes = CGImageDestinationCopyTypeIdentifiers();
    CFShow(myDestinationTypes);

    /**
     (
     "public.jpeg",
     "public.png",
     "com.compuserve.gif",
     "public.tiff",
     "public.jpeg-2000",
     "com.apple.atx",
     "org.khronos.ktx",
     "org.khronos.astc",
     "com.microsoft.ico",
     "com.microsoft.bmp",
     "com.apple.icns",
     "com.adobe.photoshop-image",
     "com.adobe.pdf",
     "com.truevision.tga-image",
     "com.ilm.openexr-image",
     "public.pbm",
     "public.pvr"
     )
     */
```


# CoreImage



