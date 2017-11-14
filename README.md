# CJLabel

`CJLabel`继承自`UILabel`，在支持`UILabel`所有属性的基础上，还提供富文本展示、图文混排、自定义点击链点设置、长按（双击）唤起`UIMenuController`选择复制文本等功能。

## 特性简介
   1. 禁止使用`-init`初始化！！
   2. `enableCopy` 长按或双击可唤起`UIMenuController`进行选择、全选、复制文本操作   
   3. `attributedText` 与 `text` 均可设置富文本
   4. 不支持`NSAttachmentAttributeName``NSTextAttachment`！！<br/>显示图片请调用:<br/>
   `+ initWithImage:imageSize:imagelineAlignment:configure:`或者<br/>
   `+ insertImageAtAttrString:image:imageSize:atIndex:imagelineAlignment:configure:`方法初始化`NSAttributedString`后显示
   5. `extendsLinkTouchArea`设置是否扩大链点点击识别范围 
   6. `shadowRadius`设置文本阴影模糊半径 
   7. `textInsets` 设置文本内边距
   8. `verticalAlignment` 设置垂直方向的文本对齐方式。注意与显示图片时候的`imagelineAlignment`作区分，`self.verticalAlignment`对应的是整体文本在垂直方向的对齐方式，而`imagelineAlignment`只对图片所在行的垂直对齐方式有效
   9. `delegate` 点击链点代理
   10. `kCJBackgroundFillColorAttributeName` 背景填充颜色，属性优先级低于`NSBackgroundColorAttributeName`如果设置`NSBackgroundColorAttributeName`会忽略`kCJBackgroundFillColorAttributeName`的设置
   11. `kCJBackgroundStrokeColorAttributeName ` 背景边框线颜色
   12. `kCJBackgroundLineWidthAttributeName ` 背景边框线宽度
   13. `kCJBackgroundLineCornerRadiusAttributeName ` 背景边框线圆角弧度
   14. `kCJActiveBackgroundFillColorAttributeName ` 点击时候的背景填充颜色属性优先级同
`kCJBackgroundFillColorAttributeName`
   15. `kCJActiveBackgroundStrokeColorAttributeName ` 点击时候的背景边框线颜色
   16. 支持添加自定义样式、可点击（长按）的文本点击链点
   17. 支持 Interface Builder


##### CJLabel 已知 Bug
 
   `numberOfLines`大于0且小于实际`label.numberOfLines`，同时`verticalAlignment`不等于`CJContentVerticalAlignmentTop`时，文本显示位置有偏差。如下图所示:<br/>
   ![](http://oz3eqyeso.bkt.clouddn.com/CJLabelBug.jpg)

## CJLabel引用
##### 1. 直接导入
下载demo，将CJLabel文件夹导入项目，引用头文件 `#import "CJLabel.h"`
##### 2. CocoaPods安装
```ruby
pod 'CJLabel', '~> 3.0.0'

```

## 用法
* 根据NSAttributedString计算CJLabel的size大小
  
```objective-c
CGSize size = [CJLabel sizeWithAttributedString:str withConstraints:CGSizeMake(320, CGFLOAT_MAX) limitedToNumberOfLines:0];
```
* 指定内边距以及限定行数计算CJLabel的size大小
```objective-c
CGSize size = [CJLabel sizeWithAttributedString:str withConstraints:CGSizeMake(320, CGFLOAT_MAX) limitedToNumberOfLines:0 textInsets:3];
  ```
  
* 设置富文本展示
<center>
 <img src="http://oz3eqyeso.bkt.clouddn.com/example0.png" width="50%"/>
 </center>
 
```objective-c
//初始化配置
CJLabelConfigure *configure = [CJLabel configureAttributes:nil isLink:NO activeLinkAttributes:nil parameter:nil clickLinkBlock:nil longPressBlock:nil];
//设置配置属性
configure.attributes = @{NSFontAttributeName:[UIFont boldSystemFontOfSize:18]};
//设置指定字符属性
attStr = [CJLabel configureAttrString:attStr withString:@"不同字体" sameStringEnable:NO configure:configure];
NSRange imgRange = [attStr.string rangeOfString:@"插入图片"];
//移除指定属性
[configure removeAttributesForKey:kCJBackgroundStrokeColorAttributeName];
//指定位置插入图片
attStr = [CJLabel insertImageAtAttrString:attStr image:@"CJLabel.png" imageSize:CGSizeMake(55, 45) atIndex:(imgRange.location+imgRange.length) imagelineAlignment:CJVerticalAlignmentBottom configure:configure];
//设置内边距
self.label.textInsets = UIEdgeInsetsMake(10, 10, 10, 0);
self.label.attributedText = attStr;
```
* 垂直对齐
 <center>
 <img src="http://oz3eqyeso.bkt.clouddn.com/example1.gif" width="35%"/>
 </center>
 
```objective-c
//设置垂直对齐方式
self.label.verticalAlignment = CJVerticalAlignmentCenter;
self.label.text = self.attStr;
//支持选择复制
self.label.enableCopy = YES;
```
* 设置文字、图片点击链点
 <center>
 <img src="http://oz3eqyeso.bkt.clouddn.com/example4.gif" width="35%"/>
 </center>
 
```objective-c
//设置点击链点属性
configure.attributes = @{NSForegroundColorAttributeName:[UIColor blueColor]};
//设置点击高亮属性
configure.activeLinkAttributes = @{NSForegroundColorAttributeName:[UIColor redColor]};
//链点自定义参数
configure.parameter = @"参数为字符串";
//点击回调（也可通过设置self.label.delegate = self代理，返回点击回调事件）
configure.clickLinkBlock = ^(CJLabelLinkModel *linkModel) {
   //do something
};
//长按回调
configure.longPressBlock = ^(CJLabelLinkModel *linkModel) {
   //do something
};
//设置为可点击链点
configure.isLink = YES;
//设置点击链点
attStr = [CJLabel configureAttrString:attStr
                           withString:@"CJLabel"
                     sameStringEnable:YES
                            configure:configure];
//设置图片点击链点属性
NSRange imageRange = [attStr.string rangeOfString:@"图片"];
CJLabelConfigure *imgConfigure =
[CJLabel configureAttributes:@{kCJBackgroundStrokeColorAttributeName:[UIColor redColor]}
                      isLink:YES
         activeLinkAttributes:@{kCJActiveBackgroundStrokeColorAttributeName:[UIColor lightGrayColor]}
                    parameter:@"图片参数"
               clickLinkBlock:^(CJLabelLinkModel *linkModel){
                   [self clickLink:linkModel isImage:YES];
               }
               longPressBlock:^(CJLabelLinkModel *linkModel){
                   [self clicklongPressLink:linkModel isImage:YES];
               }];
attStr = [CJLabel insertImageAtAttrString:attStr image:@"CJLabel.png" imageSize:CGSizeMake(45, 35) atIndex:(imageRange.location+imageRange.length) imagelineAlignment:verticalAlignment configure:imgConfigure];
self.label.attributedText = attStr;
//支持选择复制
self.label.enableCopy = YES;
```

## 版本说明
* ***V3.0.0***<br/>
 优化富文本配置方法，新增CJLabelConfigure类，简化方法调用，增加对NSAttributedString点击链点的判断（比如对于两个重名用户：@lele 和 @lele，可以分别设置不同的点击响应事件）

* ***V2.1.2***<br/>
 新增方法，可修改插入图片所在行图文在垂直方向的对齐方式（只针对当前行），有居上、居中、居下选项，默认居下

* ***V2.1.1***<br/>
 修复单行文字时候点击链点的判断，增加delegate
 
* ***V2.0.0***<br/>
 重构了底层对点击链点响应的判断，增加插入图片、插入图片链点、点击链点背景色填充、点击链点边框线描边等功能
 v2.0.0之后版本与v1.x.x版本差别较大，基本上重写了增加以及移除点击链点的API

* ***V1.0.2***<br/>
 点击链点增加扩展属性parameter
 增加方法`- addLinkString: linkAddAttribute: linkParameter: block:`
 
* ***V1.0.1***<br/>
  增加文本中内容相同的链点能够响应点击属性sameLinkEnable，必须在设置self.attributedText前赋值，默认值为NO，只取文本中首次出现的链点。<br/>
  CJLinkLabelModel的linkString改为NSString类型
  
* ***V1.0.0***<br/>
  注意：文本内存在相同链点时只有首次出现的链点能够响应点击
  
  ***注意***

**V3.0.0** 版本引入CJLabelConfigure类，优化了NSAttributedString的设置，旧的配置API不再支持。相关调用请参照以下相关方法<br/>
`+ initWithImageName:imageSize:imagelineAlignment:configure:`<br/>
`+ initWithString:configure:`<br/>
`+ initWithAttributedString:strIdentifier:configure:`<br/>

## 相关介绍
[CJLabel图文混排二 —— UILabel插入图片以及精确链点点击](http://www.jianshu.com/p/9a70533d217e)
