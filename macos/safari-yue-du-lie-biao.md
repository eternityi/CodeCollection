# Safari 阅读列表



阅读列表的使用非常简单，没有什么需要特别说明的地方，不熟悉的用户可以阅读苹果的官方支持文章（[iOS 版](https://support.apple.com/zh-cn/guide/iphone/iph1a4721132/ios)、[Mac 版](https://support.apple.com/zh-cn/guide/safari/sfri35905/mac)）来比较全面地了解。不过，阅读列表功能也有一些比较隐蔽的技巧，记忆之后，可以进一步提高将其作为链接暂存工具的效率。

**添加阶段：**

* 在 iOS 版 Safari 中，添加到阅读列表的最快方式是长按工具栏中的书签图标，然后选择相应选项；在 Mac 上，最快的方法则是使用快捷键 ⇧⌘D。
* 在 iPad 上，阅读列表界面支持拖放，并且可以批量操作：既可以从其他应用中选中多个链接拖放添加到阅读列表中，也可以从阅读列表中选择多个项目一并拖出。
* 在 Mac 版 Safari 中，可以通过「书签」-「将这 x 个标签页添加到阅读列表」菜单项将当前打开的所有标签页批量添加到阅读列表；该功能尤其适用于针对特定话题检索资料的使用场景，可以一定程度上弥补 Safari 没有 [OneTab](https://www.one-tab.com/) 一类插件的不足。

**管理和阅读阶段：**

* Safari 阅读列表支持离线缓存，但默认没有开启。
  * 在 Mac 上，这一选项位于 Safari 偏好设置中的「高级」选项卡下，通过勾选「自动存储文章以便离线阅读」开启。
  * 在 iOS 上，这一选项位于「设置 - Safari 浏览器」，然后打开「阅读列表」下方的「自动离线存储」。
  * iOS 似乎不会很及时地清理阅读列表缓存。手动清理的方法是进入「设置 - 通用 - iPhone \(iPad\) 存储 - Safari 浏览器」，然后向左划动「离线阅读列表」项目。
* 从阅读列表界面的顶部向下划动可以看到搜索框，其搜索范围包括网页标题、链接和摘要。
* 在 Mac 版 Safari 中，右键单击阅读列表的空白位置可以找到清空阅读列表的选项。
* 读到一个网页的底部后，继续向下滚动可以直接跳转到阅读列表中的下一个网页；从网页顶部向上滚动则会跳转到上一个网页。在 Mac 上，还可以通过 ⌥⌘↑/↓ 快捷键切换阅读列表中的项目。



#### 阅读列表的工作原理 <a id="ss-3-1555403196715"></a>

在介绍具体的方法之前，首先铺垫一下阅读列表的工作原理。在实现层面，Safari 阅读列表是与 Safari 书签一起存放和同步的。实际上，阅读列表不过是收藏夹中一个特殊的文件夹。

在 macOS 上，阅读列表的路径位于 `~/Library/Safari/Bookmarks.plist`。原始状态下，该 plist 文件是以二进制格式存储的；我们可以通过在终端应用程序中运行以下命令转换出一份可读的 XML 格式副本以观察其内部结构：

```bash
cd ~/Library/Safari/Bookmarks.plist && plutil -convert xml1 -o Bookmarks.xml Bookmarks.plist
```

![&#x8F6C;&#x6362;&#x4E3A; XML &#x540E;&#x7684;&#x9605;&#x8BFB;&#x5217;&#x8868;&#x6587;&#x4EF6;&#x7ED3;&#x6784;](../.gitbook/assets/image.png)



可以看到，针对每条阅读列表中的链接，Safari 存储的信息包括其标题、URL、摘要、缩略图链接、添加日期和最近打开日期等。从 `ReadingListNonSync` 这一子键名称及其包含的内容还可以看出，阅读列表中网页的缓存是不会被同步的，每台设备都会自己重新抓取一次网页信息及其缓存。（网页缓存位于 `~/Library/Safari/ReadingListArchives` 目录下，以 [Web Archive](https://en.wikipedia.org/wiki/Webarchive) 格式存储。）

iOS 上，阅读列表的存储位置稍有差别，位于 `/private/var/mobile/Library/Safari/Bookmarks.db` 这一数据库文件中，但仍然和普通书签一起存储。根据数据库各列的名称可以看出，其存储的信息和 macOS 上的 Safari 基本是一一对应的。

![ iOS &#x4E0A;&#x7684; Safari &#x4E66;&#x7B7E;&#x6570;&#x636E;&#x5E93;](../.gitbook/assets/image%20%281%29.png)

可见，要实现将阅读列表和第三方服务打通，只需要完成以下操作：

* 解析 `Bookmarks.plist` 文件，将其中的阅读列表链接通过第三方服务的 API 保存到后者中；
* 监控 `Bookmarks.plist` 文件的变动，在新链接被添加到阅读列表时同步地将其保存到其他服务。

