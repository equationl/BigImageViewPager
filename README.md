### BigImage + ImageView + ViewPager = BigImageViewPager

一个图片浏览器，支持超大图、超长图、支持手势放大、支持查看原图、下载、加载百分比进度显示。采用区块复用加载，优化内存占用，有效避免OOM。支持手势下拉退出。
#### 注意：本框架支持网络图片、本地图片、支持gif动图、支持Android 11。

# 框架特性
- 支持网络图片、本地图片；
- 支持https图片；
- 支持缩放比例、缩放动画时间的设置；
- 支持手势下拉关闭；
- 支持多种加载策略：（仅普清、仅原图、手动模式、网络自适应）
- 支持多种类型图片（超大图、超长图、超宽图、小图、gif动图）
- 支持查看原图，支持查看原图时百分比进度的展示；
- 看图体验优化：长图拉到屏幕宽度显示、宽图双击放大到屏幕高度显示、gif图放大到屏幕宽度显示；
- 自动根据Exif信息旋转图片进行显示；
- 支持图片的下载，可设置保存到的路径（默认在存储根目录新建文件夹）
- 支持多种界面的自定义（具体可查看Demo）
- 支持加载失败时占位图的设置；
- 针对保存图片进行优化，文件扩展名使用文件头部Mime信息进行设置，不用担心gif保存成jpeg；
- 支持自定义查看原图时的百分比View；
- 支持BMP格式的图片；
- 支持Android 11；

# 截图

# 强烈推荐推荐扫描二维码进行安装体验：
![扫码下载demo](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e50f06781ec248629b72d597183f5d2a~tplv-k3u1fbpfcp-watermark.image)

![kpFnSO.gif](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bfd23ce0042e431bb200d7e5e2fca87d~tplv-k3u1fbpfcp-zoom-1.image)

| 说明 | 截图 |
| ------------ | ------------ |
|主要功能|![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a045eaa577834b00a345b409b81826f8~tplv-k3u1fbpfcp-watermark.image)|
|预览界面|![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a0a1125f397b46619c9beea59691eaf5~tplv-k3u1fbpfcp-watermark.image)|
|下拉关闭|![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/414c415380f742c4918c4b705ffc2f4f~tplv-k3u1fbpfcp-watermark.image)|
|查看原图|![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ffb4cfcbaecf43488d7ae671c7c02d3d~tplv-k3u1fbpfcp-watermark.image)|


# 用法
### 一、添加依赖
#### Step 1. 在你project层级的build.gradle中，添加仓库地址:
```
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}
```
#### Step 2. 在你主module的build.gradle中添加依赖：

##### 此处显示的是本框架的最新版本号：
##### ⚠️注意：glide v3版本不再维护，最终版本为v3_4.0.2。建议使用glide v4、androidx版本。
```
androidx用户  :   使用  androidx-6.1.3
对于glide4.x  :   使用        v4_6.1.3

对于glide3.x  :   使用        v3_4.0.2
```

```
dependencies {

  // ① 针对androidx用户，需要添加以下依赖：

  // glide
  implementation 'com.github.bumptech.glide:glide:4.11.0'
  annotationProcessor 'com.github.bumptech.glide:compiler:4.11.0'
  implementation 'com.github.bumptech.glide:okhttp3-integration:4.11.0'
  // 查看大图
  implementation 'com.github.SherlockGougou:BigImageViewPager:androidx-6.1.3'

================================分割线==================================

  // ② 针对glide v4 版本，需要添加以下依赖：

  // glide
  implementation 'com.github.bumptech.glide:glide:4.9.0'
  annotationProcessor 'com.github.bumptech.glide:compiler:4.9.0'
  implementation 'com.github.bumptech.glide:okhttp3-integration:4.9.0'
  // 查看大图
  implementation 'com.github.SherlockGougou:BigImageViewPager:v4_6.1.3'

================================分割线==================================

  // ③ 针对glide v3 版本，需要添加以下依赖：

  // 查看大图
  implementation 'com.github.SherlockGougou:BigImageViewPager:v3_4.0.2'
}
```

#### Step 3. 在您的主module里，添加自定义AppGlideModule
#####（注意！！！如果您用的是glide 3.x版本，不需要做这一步的操作，上一步的依赖后就完事儿了；
##### 如果您用的是androidx版本，或者glide是4.x版本，您需要继承AppGlideModule并添加以下代码到对应的重载方法中。）例如：
```
@GlideModule
public class MyAppGlideModule extends AppGlideModule {
  @Override
  public void registerComponents(@NonNull Context context, @NonNull Glide glide, @NonNull Registry registry) {
    super.registerComponents(context, glide, registry);

    // 替换底层网络框架为okhttp3，这步很重要！如果不添加会无法正常显示原图的加载百分比，或者卡在1%
    // 如果您的app中已经存在了自定义的GlideModule，您只需要把这一行代码，添加到对应的重载方法中即可。
    registry.replace(GlideUrl.class, InputStream.class, new OkHttpUrlLoader.Factory(ProgressManager.getOkHttpClient()));
  }
}
```

#### Step 4. 以上操作完成后，请点击顶部按钮：Build->Rebuild Project，等待重建完成，至此，框架添加完成。如遇到任何问题，请附带截图提issues，我会及时回复，或添加底部QQ群，进行交流。

## 二、调用方式

#### 1：生成图片源：（如果你有缩略图和原图两种路径，请使用下面的方式，进行图片List的生成；如果你是本地图片或者没有原图缩略图之分，可以跳过这一步）
```
		ImageInfo imageInfo;
		final List<ImageInfo> imageInfoList = new ArrayList<>();
		for (String image : images) {
			imageInfo = new ImageInfo();
			imageInfo.setOriginUrl(url);// 原图url
			imageInfo.setThumbnailUrl(thumbUrl);// 缩略图url
			imageInfoList.add(imageInfo);
		}
```

#### 2：最简单的调用方式：
```
        // 最简单的调用，即可实现大部分需求，如需定制，可参考下一步的自定义代码：

        ImagePreview
            .getInstance()
            // 上下文，必须是activity，不需要担心内存泄漏，本框架已经处理好；
            .setContext(MainActivity.this)

            // 设置从第几张开始看（索引从0开始）
            .setIndex(0)

            //=================================================================================================
            // 有三种设置数据集合的方式，根据自己的需求进行三选一：
            // 1：第一步生成的imageInfo List
            .setImageInfoList(imageInfoList)

            // 2：直接传url List
            //.setImageList(List<String> imageList)

            // 3：只有一张图片的情况，可以直接传入这张图片的url
            //.setImage(String image)
            //=================================================================================================

            // 开启预览
            .start();

            // 仅需一行代码,默认配置为：
            //      显示顶部进度指示器、
            //      显示右侧下载按钮、
            //      隐藏左侧关闭按钮、
            //      开启点击图片关闭、
            //      关闭下拉图片关闭、
            //      加载方式为手动模式
            //      加载原图的百分比在底部
```

##### 接口说明：

方法名 | 功能 |  说明
-|-|-
|setBigImageClickListener|设置图片点击事件|默认null|
|setBigImageLongClickListener|设置图片长按事件|默认null|
|setBigImagePageChangeListener|设置页面切换监听|默认null|
|setDownloadClickListener|设置点击下载监听|默认null，可选是否拦截下载行为|
|setCloseIconResId|设置关闭按钮的Drawable资源id|默认内置R.drawable.ic_action_close|
|setContext|设置上下文|不允许为空|
|setDownIconResId|设置下载按钮的Drawable资源id|R.drawable.icon_download_new|
|setEnableClickClose|设置是否开启点击图片退出|默认true|
|setEnableDragClose|设置是否开启下拉图片退出|默认false|
|setEnableUpDragClose|设置是否开启上拉图片退出|默认false|
|setEnableDragCloseIgnoreScale|是否忽略缩放启用拉动关闭|默认false|
|setErrorPlaceHolder|设置加载失败的占位图资源id|默认内置R.drawable.load_failed|
|setFolderName|设置下载到的文件夹名称|默认保存Picture文件夹中|
|setImage|设置单张图片地址|三选一|
|setImageInfoList|设置图片Bean集合|三选一|
|setImageList|设置图片地址集合|三选一|
|setIndex|设置开始的索引|从0开始|
|setLoadStrategy|设置加载策略|详见加载策略说明|
|setOnOriginProgressListener|设置原图加载进度回调|加载原图的百分比进度|
|setProgressLayoutId|自定义百分比布局|详细见demo|
|setShowCloseButton|设置是否显示关闭按钮|默认false，不显示|
|setShowDownButton|设置是否显示下载按钮|默认true，显示|
|setShowIndicator|设置是否显示顶部的进度指示器|默认true，显示|
|setIndicatorShapeResId|设置顶部指示器背景shape|默认自带灰色圆角shape，设置为0时不显示背景|
|setShowErrorToast|设置是否显示加载失败的Toast|默认false，不显示|
|setZoomTransitionDuration|设置图片缩放动画时长|默认200ms|
|start|开启看图|最后调用|

##### 3：自定义多种配置：
```
        // 完全自定义调用：
        findViewById(R.id.buttonPreview).setOnClickListener(new View.OnClickListener() {
            @Override public void onClick(View v) {
                ImagePreview.getInstance()
                    // 上下文，必须是activity，不需要担心内存泄漏，本框架已经处理好
                    .setContext(MainActivity.this)
                    // 从第几张图片开始，索引从0开始哦~
                    .setIndex(0)

                    //=================================================================================================
                    // 有三种设置数据集合的方式，根据自己的需求进行三选一：
                    // 1：第一步生成的imageInfo List
                    .setImageInfoList(imageInfoList)

                    // 2：直接传url List
                    //.setImageList(List<String> imageList)

                    // 3：只有一张图片的情况，可以直接传入这张图片的url
                    //.setImage(String image)
                    //=================================================================================================

                    // 加载策略，默认为手动模式（具体可看下面加载策略的详细说明）
                    .setLoadStrategy(loadStrategy)

                    // 保存的文件夹名称，会在Picture目录进行文件夹的新建。比如："BigImageView"，会在Picture目录新建BigImageView文件夹)
                    .setFolderName("BigImageView")

                    // 缩放动画时长，单位ms
                    .setZoomTransitionDuration(300)

                    // 是否显示加载失败的Toast
                    .setShowErrorToast(showErrorToast)

                    // 是否启用点击图片关闭。默认启用
                    .setEnableClickClose(enableClickClose)
                    // 是否启用上拉/下拉关闭。默认不启用
                    .setEnableDragClose(enableDragClose)
                    // 是否忽略缩放启用拉动关闭。默认不忽略
                    .setEnableDragCloseIgnoreScale(true)

                    // 是否显示关闭页面按钮，在页面左下角。默认不显示
                    .setShowCloseButton(showCloseButton)
                    // 设置关闭按钮图片资源，可不填，默认为库中自带：R.drawable.ic_action_close
                    .setCloseIconResId(R.drawable.ic_action_close)

                    // 是否显示下载按钮，在页面右下角。默认显示
                    .setShowDownButton(showDownButton)
                    // 设置下载按钮图片资源，可不填，默认为库中自带：R.drawable.icon_download_new
                    .setDownIconResId(R.drawable.icon_download_new)

                    // 设置是否显示顶部的指示器（1/9）默认显示
                    .setShowIndicator(showIndicator)

                    // 设置失败时的占位图，默认为库中自带R.drawable.load_failed，设置为 0 时不显示
                    .setErrorPlaceHolder(R.drawable.load_failed)

                    // 点击回调
                    .setBigImageClickListener(new OnBigImageClickListener() {
                        @Override public void onClick(View view, int position) {
                            // ...
                            Log.d(TAG, "onClick: ");
                        }
                    })
                    // 长按回调
                    .setBigImageLongClickListener(new OnBigImageLongClickListener() {
                        @Override public boolean onLongClick(View view, int position) {
                            // ...
                            Log.d(TAG, "onLongClick: ");
                            return false;
                        }
                    })
                    // 页面切换回调
                    .setBigImagePageChangeListener(new OnBigImagePageChangeListener() {
                        @Override
                        public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
                            Log.d(TAG, "onPageScrolled: ");
                        }

                        @Override public void onPageSelected(int position) {
                            Log.d(TAG, "onPageSelected: ");
                        }

                        @Override public void onPageScrollStateChanged(int state) {
                            Log.d(TAG, "onPageScrollStateChanged: ");
                        }
                    })
                    // 下载按钮点击回调，可以拦截下载逻辑，从而实现自己下载或埋点统计
                    .setDownloadClickListener(new OnDownloadClickListener() {
                        @Override
                        public void onClick(Activity activity, View view, int position) {
                            // 可以在此处执行您自己的下载逻辑、埋点统计等信息
                            Log.d(TAG, "onClick: position = " + position);
                        }

                        @Override
                        public boolean isInterceptDownload() {
                            // return true 时, 需要自己实现下载
                            // return false 时, 使用内置下载
                            return false;
                        }
                    })

                    //=================================================================================================
                    // 设置查看原图时的百分比样式：库中带有一个样式：ImagePreview.PROGRESS_THEME_CIRCLE_TEXT，使用如下：
                    .setProgressLayoutId(ImagePreview.PROGRESS_THEME_CIRCLE_TEXT, new OnOriginProgressListener() {
                        @Override public void progress(View parentView, int progress) {
                            Log.d(TAG, "progress: " + progress);

                            // 需要找到进度控件并设置百分比，回调中的parentView即是传入的布局的根View，可通过parentView找到进度控件：
                            ProgressBar progressBar = parentView.findViewById(R.id.sh_progress_view);
                            TextView textView = parentView.findViewById(R.id.sh_progress_text);
                            progressBar.setProgress(progress);
                            String progressText = progress + "%";
                            textView.setText(progressText);
                        }

                        @Override public void finish(View parentView) {
                            Log.d(TAG, "finish: ");
                        }
                    })

                    // 使用自定义百分比样式，传入自己的布局，并设置回调，再根据parentView找到进度控件进行百分比的设置：
                    //.setProgressLayoutId(R.layout.image_progress_layout_theme_1, new OnOriginProgressListener() {
                    //    @Override public void progress(View parentView, int progress) {
                    //        Log.d(TAG, "progress: " + progress);
                    //
                    //        ProgressBar progressBar = parentView.findViewById(R.id.progress_horizontal);
                    //        progressBar.setProgress(progress);
                    //    }
                    //
                    //    @Override public void finish(View parentView) {
                    //        Log.d(TAG, "finish: ");
                    //    }
                    //})
                    //=================================================================================================

                    // 开启预览
                    .start();
            }
        });

```

##### 4：加载策略介绍
```
  public enum LoadStrategy {
    /**
     * 仅加载原图；会强制隐藏查看原图按钮
     */
    AlwaysOrigin,

    /**
     * 仅加载普清；会强制隐藏查看原图按钮
     */
    AlwaysThumb,

    /**
     * 根据网络自适应加载，WiFi原图，流量普清；会强制隐藏查看原图按钮
     */
    NetworkAuto,

    /**
     * 手动模式：默认普清，点击按钮再加载原图；会根据原图、缩略图url是否一样来判断是否显示查看原图按钮
     */
    Default
  }

  注：以上所有方式，如果原图缓存存在的情况，会默认加载原图缓存保证清晰度；且原图缓存只要存在，就不会显示查看原图按钮。
```

##### 5：自定义百分比View
详细操作请参考Demo：https://github.com/SherlockGougou/BigImageViewPager/blob/master/sample/src/main/java/cc/shinichi/bigimageviewpager/MainActivity.java#L291

# DEMO体验
![扫码下载demo](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0bc6607b402b4d0d9837fa44291cae43~tplv-k3u1fbpfcp-zoom-1.image)

# GitHub源码
https://github.com/SherlockGougou/BigImageViewPager

# 致谢
- 本框架核心是开源作者 [davemorrissey](https://github.com/davemorrissey) 的 [subsampling-scale-image-view](https://github.com/davemorrissey/subsampling-scale-image-view)，在此感谢他的付出！
对原作感兴趣的，可以去研究学习 ---> [传送门点我](https://github.com/davemorrissey/subsampling-scale-image-view)
- okhttp 进度监听部分代码，借鉴使用了[GlideImageView](https://github.com/sunfusheng/GlideImageView)，在此对其表示感谢，喜欢其作品的可以移步去查看学习

# Bug反馈、增加需求，加 QQ 交流群：271127803（大话安卓）
### 欢迎加入“大话安卓”技术交流群，一起分享，共同进步##

![欢迎加入“大话安卓”技术交流群，互相学习提升](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/516d83214bcd4db48e9b40798a945758~tplv-k3u1fbpfcp-zoom-1.image)

# LICENSE
```
Copyright (C) 2018 SherlockGougou qinglingou@gmail.com

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
