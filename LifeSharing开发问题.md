未解决：

1、CollectionView初次滑动卡顿

2、在水平滑动的CollectionView中进行垂直滑动时，cell会产生偏移。

CollectionView继承自ScrollView，ScrollView中有个contentInsetAdjustmentBehavior属性

3、Xcode中HTTP请求失败

Xcode强制请求的协议为HTTPS，所以必须在plist的App Transport Security中将NSAllowsArbitraryLoads置为YES

![image-20230410155711881](https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20230410155711881.png)

![image-20230410155512672](https://coffeechenbucket.oss-cn-beijing.aliyuncs.com/SwiftNotePictures/image-20230410155512672.png)

已解决：

1、CollectionViewDelegate部分代理方法不被代理调用

由于代理中已经添加了Tab手势，与CollectionViewDelegate中部分方法（ShouldSelectItem、DidSelectItem）中需要的Select动作冲突

2、TapGesture与CollectionView点击事件冲突（响应链）

修改手势tap属性cancelsTouchesInView为 false。默认值为true，当用户触碰屏幕后手势识别器识别为Touch，然后会发送touchesCancelled给hit-testview以取消hit-test view对touch的响应，这个时候只有手势识别器响应touch，CollectionView也就不会响应Select事件。

3、CollectionView拖拽移动代理方法不调用

检查collectionView.dragInteractionEnabled属性，默认值为true，若设为false则不调用代理也就不能拖拽

4、当希望通过TextView的代理方法textViewDidChange()达到限制输入字数的目的时，如果是中文输入法会发生输入最后几个字过程中由于还是拼音状态而导致超出字数限制的情况

通过textView.markedTextRange属性进行判断，若为nil则当前输入的是英文或处于中文输入结束状态，在这种条件下进行字数计数就不会出现中文输入还是拼音状态时而导致字数超限。

5、使用定位步骤

1）创建高德地图APIKey并开启HTTPS。 AMapServices.shared().enableHTTPS = **true** 

2）在项目中导入第三方包并配置桥接文件

3）根据需求在项目plist中添加对应权限申请

4）在AppDelegate中设置APIKey

5）[单次定位] 在项目文件中创建AMapLocationManager实例LocationManager并配置经度和超时时间

6）调用requestLocation获取地理信息

6、App需要上架且使用SDK涉及用户隐私的情况下，需要进行隐私合规相关配置

高德定位SDK中AMapLocationManager实例化前需要对AMapLocationManager进行隐私合规配置，否则无法实例化

https://developer.amap.com/api/ios-location-sdk/guide/create-project/ios-location-privacy

