
---
layout: post
title: Glide使用自定义Transformations 必须实现的方法
key: 100011
tags: glide Transformation
category: 开源项目
date: 2017-12-25 01:15:00 +08:00
desc: 为了让内存缓存正常地工作你是否必须实现 equals() 和 hashCode() 方法。很不幸，即使你没有复写这两个方法，BitmapTransformation 和 Transformation 也能通过编译，但这并不意味着它们能正常工作
---
* content
{:toc}
## 前言
我们使用Glide加载图片，使用自定义对Transformations 转换器的时候，都**忽略了定制变换必须实现**的方法：

      updateDiskCacheKey
      equals() / hashCode()!
官网这么说明的：

**为了让内存缓存正常地工作你是否必须实现 equals() 和 hashCode() 方法。很不幸，即使你没有复写这两个方法，BitmapTransformation 和 Transformation 也能通过编译，但这并不意味着它们能正常工作。**

当我们重写这几个方法，刷新加载Glide 的时候图片会闪动一下，网上提供的各种解决Glide 加载图片出现闪动，什么设置tag，取消默认动画，没设置缓存等等解决方案都不可靠，可能是以前Glide 4.0 一下的版本解决方案吧，并且网上提供的大多数转换器是没有重写  updateDiskCacheKey、 equals() / hashCode()! 这几个方法的，导致我们Copy 过去使用的时候不能正常工作。

下面说说Transformations：
引用官网原文解决方案：http://bumptech.github.io/glide/doc/transformations.html
### 内置类型
Glide 提供了很多内置的变换，包括：

    CenterCrop
    FitCenter
    CircleCrop

## 应用
通过 RequestOptions 类可以应用变换：

## 默认变换

    RequestOptions options = new RequestOptions();
    options.centerCrop();
    
    Glide.with(fragment)
        .load(url)
        .apply(options)
        .into(imageView);

大多数内置的变换都有静态的 import ，这是为 API 的流畅性考虑的。例如，你可以通过静态方法应用一个 FitCenter 变换：

    import static com.bumptech.glide.request.RequestOptions.fitCenterTransform;
    
    Glide.with(fragment)
        .load(url)
        .apply(fitCenterTransform())
        .into(imageView);

如果你正在使用 Generated API ，那么这些变换方法已经被内联了，所以使用起来甚至更为轻松：

    GlideApp.with(fragment)
      .load(url)
      .fitCenter()
      .into(imageView);

可以查阅 Options 页来获得更多 RequestOption 的相关信息。

多重变换
默认情况下，每个 transform() 调用，或任何特定转换方法(fitCenter(), centerCrop(), bitmapTransform() etc)的调用都会替换掉之前的变换。

如果你想在单次加载中应用多个变换，请使用 MultiTransformation 类。

使用 generated API:

    Glide.with(fragment)
      .load(url)
      .transform(new MultiTransformation(new FitCenter(), new YourCustomTransformation())
      .into(imageView);

或结合使用快捷方法和 generated API：

    GlideApp.with(fragment)
      .load(url)
      .transforms(new FitCenter(), new YourCustomTransformation())
      .into(imageView);

请注意，你向 MultiTransformation 的构造器传入变换参数的顺序，决定了这些变换的应用顺序。
## 定制变换
尽管 Glide 提供了各种各样的内置 Transformation 实现，如果你需要额外的功能，你也可以实现你自己的 Transformation。

BitmapTransformation
如果你只需要变换 Bitmap，最好是从继承 BitmapTransformation 开始。BitmapTransformation 为我们处理了一些基础的东西，例如，如果你的变换返回了一个新修改的 Bitmap ，BitmapTransformation将负责提取和回收原始的 Bitmap。

## 一个简单的实现看起来可能像这样：

public class FillSpace extends BitmapTransformation {
    private static final String ID = "com.bumptech.glide.transformations.FillSpace";
    private static final String ID_BYTES = ID.getBytes(STRING_CHARSET_NAME);

    @Override
    public Bitmap transform(BitmapPool pool, Bitmap toTransform, int outWidth, int outHeight) {
        if (toTransform.getWidth() == outWidth && toTransform.getHeight() == outHeight) {
            return toTransform;
        }

        return Bitmap.createScaledBitmap(toTransform, outWidth, outHeight, /*filter=*/ true);
    }

    @Override
    public void equals(Object o) {
      return o instanceof FillSpace;
    }

    @Override
    public int hashCode() {
      return ID.hashCode();
    }

    @Override
    public void updateDiskCacheKey(MessageDigest messageDigest)
        throws UnsupportedEncodingException {
      messageDigest.update(ID_BYTES);
    }
}

尽管你的 Transformation 将几乎确定比这个示例更复杂，但它应该包含了相同的基本元素和复写方法。

## 必需的方法
请特别注意，对于任何 Transformation 子类，包括 BitmapTransformation，你都有三个方法你 必须 实现它们，以使得磁盘和内存缓存正确地工作：

    equals()
    hashCode()
    updateDiskCacheKey

如果你的 Transformation 没有参数，通常使用一个包含完整包限定名的 static final String 来作为一个 ID，它可以构成 hashCode() 的基础，并可用于更新 updateDiskCacheKey() 传入的 MessageDigest。如果你的 Transformation 需要参数而且它会影响到 Bitmap 被变换的方式，它们也必须被包含到这三个方法中。

例如，Glide 的 RoundedCorners 变换接受一个 int，它决定了圆角的弧度。它的equals(), hashCode() 和 updateDiskCacheKey 实现看起来像这样：

      @Override
      public boolean equals(Object o) {
        if (o instanceof RoundedCorners) {
          RoundedCorners other = (RoundedCorners) o;
          return roundingRadius == other.roundingRadius;
        }
        return false;
      }
    
      @Override
      public int hashCode() {
        return Util.hashCode(ID.hashCode(),
            Util.hashCode(roundingRadius));
      }
    
      @Override
      public void updateDiskCacheKey(MessageDigest messageDigest) {
        messageDigest.update(ID_BYTES);
    
        byte[] radiusData = ByteBuffer.allocate(4).putInt(roundingRadius).array();
        messageDigest.update(radiusData);
      }

原来的 String 仍然保留，但 `roundingRadius` 被包含到了三个方法中。这里，`updateDiskCacheKey` 方法还演示了你可以如何使用 ByteBuffer 来包含基本参数到你的 `updateDiskCacheKey` 实现中。

不要忘记 `equals() / hashCode()!`
值得重申的一点是，为了让内存缓存正常地工作你是否必须实现 `equals()` 和 `hashCode()` 方法。很不幸，即使你没有复写这两个方法，`BitmapTransformation` 和 `Transformation` 也能通过编译，但这并不意味着它们能正常工作。我们正在探索一些方案，以使在 Glide 的未来版本中，使用默认的 equals() 和 hashCode 方法将抛出一个编译时错误。

## Glide中的特殊行为
### 重用变换
`Transformation` 的设计初衷是无状态的。因此，在多个加载中复用 `Transformation` 应当总是安全的。创建一次 `Transformation` 并在多个加载中使用它，通常是很好的实践。

### ImageView的自动变换
在Glide中，当你为一个 ImageView 开始加载时，Glide可能会自动应用 FitCenter 或 CenterCrop ，这取决于view的 ScaleType 。如果 scaleType 是 CENTER_CROP , Glide 将会自动应用 CenterCrop 变换。如果 scaleType 为 FIT_CENTER 或 CENTER_INSIDE ，Glide会自动使用 FitCenter 变换。

当然，你总有权利覆写默认的变换，只需要一个带有 Transformation 集合的 RequestOptions 即可。另外，你也可以通过使用 dontTransform() 确保不会自动应用任何变换。

### 自定义资源
因为 Glide 4.0 允许你指定你将解码的资源的父类型，你可能无法确切地知道将会应用何种变换。例如，当你使用 asDrawable() (或就是普通的 with() ，因为 asDrawable() 是默认情形)来加载 Drawable 资源时，你可能会得到 BitmapDrawable 子类，也有可能得到 GifDrawable 子类。

为了确保你添加到 RequestOptions 中的任何变换都会被使用，Glide将 Transformation 添加到一个Map中保存，其Key为你提供变换的资源类型。当资源被成功解码时，Glide使用这个Map来取回对应的 Transformation 。

Glide可以将 Bitmap Transformation应用到 BitmapDrawable , GifDrawable , 以及 Bitmap 资源上，因此通常你只需要编写和应用 Bitmap Transformation 。然而，如果你添加了额外的资源类型，你可能需要考虑派生 RequestOptions 类，并且，在内置的这些 Bitmap Transformations 之外，你还需要为你的自定义资源类型提供一个 Transformation 。
