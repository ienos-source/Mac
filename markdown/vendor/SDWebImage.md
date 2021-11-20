# SDWebImage

```
|- UIKit
    |- UIImageView + WebCache
    |- SDWebImageManager
    |- SDImageCache
    |- SDWebImageDownloader
```

### 加载图片过程大致流程


```

  |- setImageWithURL:placeholderImage:options: 会先显示 placeholderImage - SDWebImageManager
  |- downloadURL:delegate:options:userInfo:(SDWebImageManager) -> queryDiskCacheForKey:delegate:userInfo(SDImageCache) 缓存查找

      |- 缓存找到 imageCache:didFindImage(SDImageCacheDelegate) -> SDWebImageManager
      |- SDWebImageManager 回调 webImageManager:didFinishWithImage: 到 UIImageView+WebCache 等前端展示图片

      |- 内存未找到，生成 NSInvocationOperation 添加到队列中开始从硬盘查找图片是否已经缓存
      |- 根据 URLKey 在硬盘缓存目录下尝试读取图片文件。这一步是在 NSOperation 中进行的操作，所以回主线程进行结果回调 notifyDelegate:
      |- 如果从硬盘读取到了图片，将图片添加到内存缓存中（如果空闲内存过小，会先清空内存缓存）。SDImageCacheDelegate 回调 imageCache:didFindImage:forKey:userInfo:，进而回调展示图片

      |- 如果从硬盘缓存都读取不到图片，则回调 imageCache:didNotFindImageForKey:userInfo:

  |- 共享或重新生成一个下载器 SDWebImageDownloader 开始下载
  |- 图片下载由 NSURLConnection 来做，实现相关 Delegate 来判断图片下载中、下载完成和下载失败
  |- connection:didReceiveData: 中利用 ImageIO 做了按图片下载进度加载效果
  |- connectionDidFinishLoading: 数据瞎子啊完成后交给 SDWebImageDecoder 做图片解码处理
  |- 图片解码处理在一个 NSOperationQueue 完成，不会拖慢主线程 UI，如果有需要对下载的图片进行二次处理，也可以在此处进行处理
  |- 在主线程 notifyDelegateOnMainThreadWithInfo: 宣告解码完成 imageDecoder:didFinishDecodingImage:userInfo: 回调给 SDWebImageDownloader
  |- imageDownloader:didFinishWithImage: 回调给 SDWebImageManager 告知图片下载完成
  |- 通知所有 downloadDelegates 下载完成，回调给需要的地方展示图片
  |- 将图片保存到 SDImageCache 中，内存缓存和硬盘缓存同时保存。写文件到硬盘也在以单独 NSInvocationOperation 完成，避免拖慢主线程
  |- SDImageCache 在初始化的时候会注册一些消息通知，在内存警告或退到后台的时候清理内存图片缓存，应用程序结束的时候清理过期图片
  |- SDWebImage 也提供 UIButton+WebCache 和 MKAnnotation+WebCache，方便使用
  |- SDWebImagePrefetcher 可以预先下载图片，方便后续使用

```

内部做 Decoder 的原因

由于 UIImage 的 imageWithData 函数是每次画图的时候才将 Data 解压成 ARGB 的图像，所以在每次画图的时候，会有一个解压缩操作，这样的效率很低，但是只要瞬时的内存需求。为了提高效率通过 SDWebImageDecoder 将包装在 Data 下的资源解压，然后画在另外一张图片上，这样这张新的图片就不需要重复解压了
