---
layout: post
title: Android中签名、证书、公钥密钥的概念及使用
permalink: sign
date: 2016-09-02 17:08:06
categories: 技术
tags: Android
---

资料来源于Android 官方文档：
[https://developer.android.com/studio/publish/app-signing.html](https://developer.android.com/studio/publish/app-signing.html)
还有些资料来源于网络。加以整理！
**公钥和私钥的概念**
----

在现代密码体制中加密和解密是采用不同的密钥（公开密钥），也就是公开密钥算法（也叫非对称算法、双钥算法）”，每个通信方均需要两个密钥，即公钥和私钥，这两把密钥可以互为加解密。公钥是公开的，不需要保密，而私钥是由个人自己持有，并且必须妥善保管和注意保密。

#### **证书的概念**

数字证书则是由证书认证机构（CA）对证书申请者真实身份验证之后，用CA的根证书对申请人的一些基本信息以及申请人的公钥进行签名（相当于加盖发证书机 构的公章）后形成的一个数字文件。CA完成签发证书后，会将证书发布在CA的证书库（目录服务器）中，任何人都可以查询和下载，因此数字证书和公钥一样是公开的。实际上，数字证书就是经过CA认证过的公钥。

 <!-- more -->
**原则：**

    1、一个公钥对应一个私钥。

    2、密钥对中，让大家都知道的是公钥，不告诉大家，只有自己知道的，是私钥。

    3、如果用其中一个密钥加密数据，则只有对应的那个密钥才可以解密。

    4、如果用其中一个密钥可以进行解密数据，则该数据必然是对应的那个密钥进行的加密。

    5、非对称密钥密码的主要应用就是公钥加密和公钥认证，而公钥加密的过程和公钥认证的过程是不一样的

 

**基于公开密钥的加密过程**

比如有两个用户Alice和Bob，Alice想把一段明文通过双钥加密的技术发送给Bob，Bob有一对公钥和私钥，那么加密解密的过程如下：

    1、Bob将他的公开密钥传送给Alice。

    2、Alice用Bob的公开密钥加密她的消息，然后传送给Bob。

    3、Bob用他的私人密钥解密Alice的消息。

**Android中的签名：**
----------

#### **是什么签名？**

------
Android要求所有已安装的应用程序都使用数字证书做数字签名，数字证书的私钥由应用开发者持有，
<font color=red> Android使用证书作为标示应用程序作者的一种方式，并在应用程序之间建立信任的关系。</font> 证书并不用来控制用户能否安装哪个应用。证书不需要由证书认证中心签名；完全可以使用自制签名证书。

没有正确签名的应用，Android系统不会安装或运行。此规则适用于在任何地方运行的Android系统，不管是在模拟器还是真实设备上。因为这个原因。在真机或模拟器上运行或者调试应用前，必须为其设置好签名。


#### **为什么要有签名？**

-----
开发Android的人这么多，完全有可能把类名，包名命名成同样的名字，这个时候该如何区分？所以，这时候就需要签名来区分了，由于开发商可能通过使用相同的Package Name来混淆替换已经安装的程序，签名可以保证相当名字，但是签名不同的包不被替换。 
发布过Android应用的朋友们应该都知道，Android APK的发布是需要签名的。签名机制在Android应用和框架中有着十分重要的作用。例如，Android系统禁止更新安装签名不一致的APK；如果应用需要使用system权限，必须保证APK签名与Framework签名一致。

#### **签名策略**

------
应用程序签名的一些方面可能会影响应用程序的开发过程, 尤其是当你计划发布多个应用时. 通常情况下, 对于所有开发者而言,推荐的策略是:在应用程序的整个生命周期,所有的应用程序使用相同的证书签名.

**为什么这么做的原因:**

* 应用程序升级 – 当发布应用的更新时, 如果想让用户无缝地升级到新版本, 需要继续使用相同的某个或者某一套证书来签名更新包.当系统安装应用的更新时, 它会比较现有版本和新版本的证书. 如果证书吻合, 包括证书数据和顺序都吻合, 那么系统允许更新.如果新版本所做的签名不是匹配的, 那么将需要给应用起一个不同的包名 — 在这种情况下, 用户相当于安装了一个完全的新程序.
* 应用程序模块化 – Android允许由相同证书签名的应用程序运行在相同的进程中, 此时系统会将它们作为单个应用程序对待.在这种方式中, 可以按模块化的方式部署应用, 用户可以根据需要独立地更新每一个模块.
* 代码/数据 的授权共享 – Android 提供模式匹配的权限控制机制,因此一个应用可以暴露功能给另一个用指定证书签名的应用. 通过用相同证书签名多个应用,以及使用模式匹配的权限检查,应用程序可以以安全的方式共享代码和数据.


**另一个影响签名策略的重要考量是, 如何设置签名应用的key的有效期.**

* 如果计划为某个单独的应用程序提供更新支持, 那么应该确认key的有效期要比应用的寿命长. 推荐25年或者更长的有效期.当key的有效期过期, 用户将再也不能无缝地更新到应用程序的新版本.
* 如果要使用相同的key为多个不同的应用签名, 应当确认key的有效期比所有这些应用的所有版本的生命周期还长,包括要比将来加到这个套件中的额外的关联应用的生命周期更长.
*  如果计划将应用程序发布到Android Market, 为应用签名的key的有效期必须在2033年10月22日以后.Market服务器强制执行这个规则, 来保证当新版本可用时, 用户可以无缝地更新Market应用.

**当设计的时候, 须牢记这些要点, 以确保使用合适的证书来签名应用程序。**

#### **Debug模式下的签名**

------

运行或从IDE调试项目时，Android的Studio会自动由Android SDK工具生成的调试证书签名的APK。您运行或调试Android Studio中的项目第一次，IDE会自动在调试密钥库和证书 
$HOME/.android/debug.keystore，并设置密钥库和密钥的密码。

因为Debug模式下的证书由构建工具创建，这样是不安全的，大部分应用程序商店（包括谷歌Play商店）不会接受的APK有出版调试证书签名。

所以你不用每次调试时都输入的Android Studio自动存储在签约配置调试签约信息。签名的配置是由所有必要的信息，以签署APK，包括密钥存储位置，存储密码，密钥名称，密钥密码的对象。您不能直接编辑调试签约配置，但可以配置你如何签上你的发布版本。

有关如何构建和调试运行的应用程序，看到更多的信息， 生成并运行您的应用程序。


#### **调试证书到期**

-----
用来签署APK调试的自签名证书有365天，从它的创建日期的截止日期。证书过期后，你在构建的时候会报错的。

为了解决这个问题，只需删除该debug.keystore文件。该文件存储在以下位置：

```
~/.android/ 在OS X和Linux
C:\Documents and Settings\<user>\.android\ 在Windows XP上
C:\Users\<user>\.android\ 在Windows Vista和Windows 7，8，和10
```

接下来，你构建和运行调试版本的时候，构建工具将重新生成一个新的秘钥库和Debug key，请注意！你必须运行你的程序，不然就不会重新生成秘钥库和Debug key。

#### **发布版的签名**
你可以使用AndroidStudio 来手动生成签名的apk，但是这样每次发布不同的版本的时候就都手动生成一次，比较麻烦的，所以，我们还可以配置Gradle文件，在构建的过程中会自动签名。

在AndroidStudio中要手动生成签名的apk，按照以下步骤：
1.  在菜单栏中，Build > Generate Signed APK
![这里写图片描述](https://img-blog.csdn.net/20160902150733659)
2.  选择您想从下拉释放下来，然后单击模块 下一步。
3. 如果你已经有一个密钥库，请转到步骤5.如果你想创建一个新的密钥库，单击 新建。
4. 在新的密钥库窗口，为您提供密钥库和密钥以下信息，如图1所示。
![这里写图片描述](https://img-blog.csdn.net/20160902151403849)
图1。创建Android Studio中一个新的密钥库。

**Keystore**

 *  Key store path:：选择您的密钥存储应该创建的位置。
 *  Password：创建并确认您的密钥库安全的密码。

**Key**

 * Alias：你的Enter键的标识名称。
 * Password: 创建并确认你的密钥的安全密码。这应该是从你选择你的密钥库的密码不同
 * Validity (years)：设置的时间长度在几年，你的key将是有效的。你的key应该是有效期至少为25年来，这样你就可以通过你的应用程序的寿命相同的密钥签名的应用程序更新。
 * Certificate：输入有关自己的一些信息，为您的证书。这个信息是不是在你的应用程序中显示，但是属于apk的一部分。
填完上面信息，然后单击确定。

在生成签名APK窗口中，选择一个密钥库，私钥，并输入密码两种。（如果在上一步中创建您的密钥库，这些字段已经为您填充。）然后单击 下一步。

![这里写图片描述](https://img-blog.csdn.net/20160902152553403)
图2。选择Android Studio中的私钥。


在下一个窗口中，选择签署的apk的输出目录，和签名的环境，然后单击 Finish（完成）。

![这里写图片描述](https://img-blog.csdn.net/20160902152859138)
图3。生成所选Flavors的APK。

该过程完成后，你会发现在你上面选择的目标文件夹已经有签名的APK。您现在可以通过一个应用市场，如谷歌Play商店，或使用你选择的机制分发签署的APK。

#### **配置build.gradle文件自动签名你的apk**

------

在Android Studio中，你可以通过build.gradle 配置来构建你的项目，在构建的过程中会自动生成你的apk。步骤如下：

1.  在当前你的项目，右键点击你的程序，然后打开Projec Structure，
2. 再打开的窗口中，在模块的左侧面板中选择你想签署的Module。
3. 选择你的密码存储文件，然后输入相关的信息；如图：
![这里写图片描述](https://img-blog.csdn.net/20160902164510994)
图4。用于创建新签名构造的窗口。
4.  点击Ok。
5. 然后在build types配置中 选择刚刚签名的配置。如图：
![这里写图片描述](https://img-blog.csdn.net/20160902154255284)

最后点击ok。

然后AndroidStudio 自动构建编译，成功后可以在build/outputs/apk/为您构建模块项目目录内的文件夹中找到我们输出的apk。

#### **签名注意事项**

-----

你应该签名所有的APK与整个应用程序的寿命预期相同的证书。有几个原因，你应该这样做：

* 应用程序升级：当系统安装更新到一个应用程序，它在新版本中与那些在现有版本的证书（S）比较。该系统允许更新，如果证书相匹配。如果你用不同的证书签名的新版本，你必须在不同的包名称分配给应用程序，在这种情况下，用户安装新版本作为一个全新的应用程序。
* 应用模块：如果是app，Android 允许同一证书签名在同一个进程中运行的APK，因此，系统会将其视为一个单一的应用程序。通过这种方式，你可以部署模块你的应用程序。
* 代码/数据，通过共享的权限：Android提供了基于签名的权限执行，这样应用程序可以公开的功能到与指定的证书签名的另一个应用程序。通过签署多个APK使用相同的证书，并使用基于签名的权限检查，您的应用程序可以在一个安全的方式共享代码和数据。


#### **保护您的私钥**

----

保护你的私钥的安全性是至关重要的，无论是你还是用户，如果允许别人使用你的秘钥，或者你泄露你的秘钥文件，使得第三方找到他们并使用它们，那么你的app安全性将会受到损失~

作为一个开发者，在任何时候，都要保护好你的私钥，直到密钥已过期。以下是一些技巧让你的密钥的安全：

*  选择不容易破解的密码，和keystore；
* 不要给外人你的私钥和keystore文件；
* 把他们放置在一个安全的地方。


#### **删除签名信息**

-----

当你创建一个签名的配置的时候，Android的Studio将在纯文本模块的您的签名信息build.gradle的文件。如果你是一个团队或用你的代码工作，你应该让它变成一个不容易给别人访问的移动的文件。要做到这一点，你应该创建一个单独的属性文件来存储安全的信息，

创建一个文件名 ​​为keystore.properties在项目的根目录。这个文件应该包含您的签名信息，如下所示：

```
storePassword = myStorePassword 
keyPassword = mykeyPassword 
keyAlias ​​= myKeyAlias 
​​storeFile = myStoreFileLocation
```
在你的模块的build.gradle文件中，添加的代码加载你keystore.properties的文件之前android 块。

```
...

// Create a variable called keystorePropertiesFile, and initialize it to your
// keystore.properties file, in the rootProject folder.
def keystorePropertiesFile = rootProject.file("keystore.properties")

// Initialize a new Properties() object called keystoreProperties.
def keystoreProperties = new Properties()

// Load your keystore.properties file into the keystoreProperties object.
keystoreProperties.load(new FileInputStream(keystorePropertiesFile))

android {
    ...
}
```
你可以使用语法，来引用存储的签名信息：

```
android {
    signingConfigs {
        config {
            keyAlias keystoreProperties['keyAlias']
            keyPassword keystoreProperties['keyPassword']
            storeFile file(keystoreProperties['storeFile'])
            storePassword keystoreProperties['storePassword']
        }
    }
    ...
  }
```

#### **使用工具签名**

----

如果你不用AndroidStudio来签名你的apk，你还可以使用Android SDK 和 JDK 自带的工具，步骤如下：

1.  **使用keytool 生成私钥，例如：**

```
$ keytool -genkey -v -keystore my-release-key.keystore
-alias alias_name -keyalg RSA -keysize 2048 -validity 10000
```

提示您输入密钥库和密钥的密码，并为你的key设置别名。然后生成密钥库作为一个名为my-release-key.keystore。密钥库包含单个键，有效10000天。别名是签署您的应用程序时，您将在以后使用的名称。
2.  **编译发布模式下您的应用程序，以获得一个无符号的APK。**
3. **使用jarsigner来签名你的app：**
```
$ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1
-keystore my-release-key.keystore my_application.apk alias_name
```
上面提示您输入密钥库和密钥的密码。然后签名apk。
4.  **验证你的apk签名：**
```
$ jarsigner -verify -verbose -certs my_application.apk
```
...
5.  **使用zipalign来对齐apk包：**

```
$ zipalign -v 4 your_project_name-unaligned.apk your_project_name.apk
```
zipalign 确保所有的未压缩数据与特定字节对齐相对于文件，从而降低apk文件的大小。



> 学习理解并整理下来的笔记。  
> 希望大家能够指点或提出宝贵意见，谢谢！一起学习。 
> 转载请注明出处：
[http://blog.csdn.net/u011974987/article/details/52415037](http://blog.csdn.net/u011974987/article/details/52415037)
> 个人主页：[xuhaoblog.com](http://xuhaoblog.com)

