IMYTools       IMYToolsFMSuspensionView   addSubviews  懒加载 相关修改

  

2024年04月11日 星期四

1. 【【iOS 技术需求】-怀孕首页 Lottie 动画替换为 PAG 动画】
   https://www.tapd.meiyou.com/21039721/prong/stories/view/1121039721001220439

```
关键词 :IMYYQHome   查找 baby_gif  pag_url新增
			IMYYunyuHome   pagUrl   将lotti相关的更换为 pag
```



```
分支 :   
IMYYQHome    origin/yjc_YQHome_0410   
IMYYunyuHome  yjc_8.72_0409_pic

```



2. 【【iOS 技术需求】iOS 启动优化-控件初始化】
   https://www.tapd.meiyou.com/36017140/prong/stories/view/1136017140001220600

```
关键词

[[IMYToolsFMSuspensionView shareInstance] showIfNeed]; 启动时 会创建UI相关,但是非必须  
修改成
[IMYToolsFMSuspensionView  showIfNeed];
```



```
分支 : 
IMYTools		origin/yjc_8.72_0408
```



3.【feature】【【技术】治理妊娠流水债务，用户可以在各端看到统一的孕期信息】
https://www.tapd.meiyou.com/21039721/prong/stories/view/1121039721001220576

```
关键词     IMYYunyuHomeBabyVC_V2 替换原 alert 样式
					新增 按钮跳转

```



```
IMYYunyuHome origin/yjc_8.72_0409
```





---

2024年04月11日14:59:05

Podfile  中需要修改的内容

```
 # 孕期
 #孕育模块首页823+
#  pod 'IMYYunyuHome', :podspec => 'https://gitlab.meiyou.com/iOS/IMYYunyuHome/raw/release-8.72.0/IMYYunyuHome.podspec'

  pod 'IMYYunyuHome', :path =>"../IMYYunyuHome"
  
  
  //图片启动不重复加载组件
  pod 'MeetYouAssets', :podspec => 'https://gitlab.meiyou.com/Youbaobao/MeetYouAssets/raw/Seeyou/MeetYouAssets.podspec', :configurations => ['Debug']
```

---



2024年04月12日09:00

```
【【设计】一级页面规范字号的使用、使其具有更整齐的视觉感知】
https://www.tapd.meiyou.com/21039721/prong/stories/view/1121039721001220462
```



```
经期
1.查看详情-->  IMYRecordScrollableBannerCell-->IMYRecordScrollableBannerInfoView-->c
(按钮高度 24,字号12,左右间距8 -->高度 28,字号13,左右间距14)

怀孕几率: IMYRecordI18nFlowerView
( ,字号20/14 -->21/13,主标题20 没看懂 黄色 )
2. 是否  --->   IMYRecord       IMYRecordToolsYesNoButton  (14-->15)
3.月经周期20天---> IMYHomeMensesToolBarView-->IMYHomeMensesToolForAnalyzeView (左边16-->左边17)

修改4处
```

```
1.IMYRecord  - (UILabel *)tipLabel 按钮修改 prepareUI  
	IMYRecord	 IMYRecordI18nFlowerView
2.IMYRecord  IMYRecordToolsYesNoButton
3.IMYRecord  IMYHomeMensesToolForAnalyzeView
```





<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240412095030313.png" alt="image-20240412095030313" style="zoom:30%;" />



   ```
   备孕
   1.身份选择--> IMYYunyuSelectBabyCellV3 (选中 17 未选中16--> 选中 17 未选中15)
   2.时间轴 ---> IMYDateSegmentedCollectionViewCell (选中 16 未选中14  --> 选中 17 未选中15)
   
   3.主标题,按钮,百分比字号-->  IMYForPregnacyPeriodHeadInfoCell 
   guideLabel
   (按钮高度 24,字号12,左右间距8 -->高度 28,字号13,左右间距14,字号20/14 -->21/13,主标题20 没看懂 黄色 )
   
   
   4.是否  --->   IMYRecord       IMYRecordToolsYesNoButton ( 14-->15) 
   5.宫格 --->  广告的->IMYYunyuToolAdView
   						正常的-->IMYYunYuToolsItemCell    (12--13)
   ```

```
1.IMYYunyuHome IMYYunyuSelectBabyCellV3  -- >16 ---15
2.IMYYunyuHome [IMYYYForPregnancyScrollableBannerCell segmentedView]
3.IMYYunyuHome  IMYForPregnacyPeriodHeadInfoCell
4.IMYRecord	IMYRecordToolsYesNoButton
5.IMYAdvertisement    
	广告  IMYYunyuToolCollectionAdManager  
	- (void)collectionView:(UICollectionView *)collectionView
 	CGFloat fontSize = 13;
 
	IMYYunyuHome 
	正常 IMYYunYuToolsModel     titleLabelFont



```





<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240412101837435.png" alt="image-20240412101837435" style="zoom:20%;" />

```
怀孕
1.身份选择--> IMYYunyuSelectBabyCellV3 (选中 17 未选中16--> 选中 17 未选中15)
2.时间轴 ---> IMYDateSegmentedCollectionViewCell (选中 16 未选中14  --> 选中 17 未选中15)
	辅助时间轴--> IMYYunyuPregnancyBabyInfoView (12.13--->13)
3.主标题,按钮,百分比字号-->  IMYYunyuPregnancyBabyInfoView 
(按钮高度 24,字号12,左右间距8 -->高度 28,字号13,左右间距14 )
4.卡片文案--->IMYBabyChangeItemTagView ( 14-->黄色)
5.左边-->IMYYunyuInviteView(14-->黄色)
	按钮(  高度 24,字号12,左右间距12 -->高度 28,字号13,左右间距14 )
6.宫格 --->  广告的->IMYYunyuToolAdView
						正常的-->IMYYunYuToolsItemCell    (12--13)
```

```
1.IMYYunyuHome IMYYunyuSelectBabyCellV3  -- >16 ---15   (已提交)
2.IMYYunyuHome IMYYunyuPregnancyContainerView   dateControl - (IMYDateSegmentedControlView *)dateControl
	IMYYunyuHome			IMYYunyuPregnancyBabyInfoView(辅助时间线)  pregnancyLabel (NSDictionary *pregnancAllText = @{NSFontAttributeName:[UIFont systemFontOfSize:13], NSForegroundColorAttributeName:[UIColor whiteColor]}; 这个是addAttributes的属性)
3.IMYYunyuHome IMYYunyuPregnancyBabyInfoView   self.guideLabel.frame
4.
5.IMYYunyuHome IMYYunyuPregnancyBabyBornView
6.

```



<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240412111804533.png" alt="image-20240412111804533" style="zoom:20%;" />

```
育儿 1-2岁
1.身份选择--> IMYYunyuSelectBabyCellV3 (选中 17 未选中16--> 选中 17 未选中15)
2.
	正文+图--> (左右间距 16 --> 图左20,右边 16)
3.宫格 --->  广告的->IMYYunyuToolAdView
						正常的-->IMYYunYuToolsItemCell    (12--13)
4.注意文案 --> IMYYunyuBRGuidanceHeaderView (主副文案间距修改为 8)
5.今天--> IMYYunyuBRDayCell(16-->17)
	今天/年纪---> (文字下方对其-->居中对齐)
```

```
1.已提交
2.IMYYunyuHome IMYBabyChangeItemCell  make.left.mas_equalTo(20);
3.
4.IMYYunyuHome IMYYunyuBRGuidanceHeaderView  make.left.mas_equalTo(self.titleLabel.mas_right).offset(8);
5.IMYYunyuHome IMYYunyuBRDayCell        
				make.centerY.equalTo(self.dateLabel); 
        label.font = [UIFont imy_MediumFontWith:17];
 

```





<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240412112530156.png" alt="image-20240412112530156" style="zoom:20%;" />

```
育儿 2-3岁
1.身份选择--> IMYYunyuSelectBabyCellV3 (选中 17 未选中16--> 选中 17 未选中15)
2.
	正文+图--> (左右间距 16 --> 图左20,右边 16)
3.宫格 --->  广告的->IMYYunyuToolAdView
						正常的-->IMYYunYuToolsItemCell    (12--13)
4.今天--> (16-->17)
	今天/年纪---> (文字下方对其-->居中对齐)
```

<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240412112712051.png" alt="image-20240412112712051" style="zoom:20%;" />

```
记录
(经期)
1.身份选择--> IMYCalendarModelTypeV2View (选中 17 未选中16--> 选中 17 未选中15)
2.星期 --> IMYCalendarModelTypeV2View 的同级label(10-->11)
3.注释 --> record_shiyi_jingqi 这是图片名字  (12-->13)
4.大姨妈来了 是否  --->   IMYRecord       IMYYESNOButton ( 14-->15)
5.爱爱--> IMYRecordMessageCell_v2 (列表左边16 -->17)
(怀孕)
6.农历-->(10-->11)  IMYRecordPregnancyMsgBar
  注释--> (10-->11)
  孕周--> (14-->13)
  预产期--> (14-->13)
7. 全身份,你目前的记录均已自动备份成功了!IMYCalendarModelTypeV2View --> (12-->13)
```

<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240412114655023.png" alt="image-20240412114655023" style="zoom:50%;" />

```
1.IMYRecord   IMYCalendarModelTypeV2View  [UIFont systemFontOfSize:15];
2.IMYRecord   IMYCalendarViewController_v2 lb.font = [UIFont systemFontOfSize:11];
3.IMYRecord 	IMYCalendarViewController_v2  storyboard 故事版调节
4.IMYRecord		IMYYESNOButton 	_rightButton.titleLabel.font = [UIFont systemFontOfSize:15];
```



```
经期:
IMYRecord

备孕:
IMYYunyuHome
IMYAdvertisement

怀孕:
IMYYunyuHome


```



---





2024年04月15日09:25

```
isBoldTitleStyle1MaxFont

```

<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240415093033699.png" alt="image-20240415093033699" style="zoom:50%;" />



选择日期中...当天的

<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240415094024060.png" alt="image-20240415094024060" style="zoom:30%;" />







titleStyle1MaxFont

titleStyle1MinFont



<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240415105727827.png" alt="image-20240415105727827" style="zoom:50%;" />



---





2024年04月17日14:18:59





IMYVIPAnalysisMensesHealthCell  雷达区



UI地址
https://pixso.cn/app/editor/NNTnaRKzCV81y4uBroLKkw?page-id=67%3A13290

----



0x116364d20



![image-20240424184815247](/Users/meetyou/Library/Application Support/typora-user-images/image-20240424184815247.png)



----

 //背景图片

  UIImageView *backgroundView = [[UIImageView alloc] init];

  [backgroundView imy_setImage:@"fx_top_bg"];

  backgroundView.contentMode = UIViewContentModeScaleToFill;

  [**self**.view addSubview:backgroundView];

  [backgroundView mas_makeConstraints:^(MASConstraintMaker *make) {

​    make.top.equalTo(**self**.view).mas_offset(-SCREEN_STATUSBAR_HEIGHT);

​    make.leading.trailing.equalTo(**self**.view);

​    make.height.mas_equalTo(SCREEN_By375(300));

  }];

   

  //导航栏

  [**self**.view addSubview:**self**.navigationBar];

  [**self**.navigationBar mas_makeConstraints:^(MASConstraintMaker *make) {

​    make.top.equalTo(**self**.view).mas_offset(-SCREEN_STATUSBAR_HEIGHT);

​    make.leading.trailing.equalTo(**self**.view);

​    make.height.mas_equalTo(SCREEN_STATUSBAR_NAVIGATIONBAR_HEIGHT);

  }];

<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240425180416908.png" alt="image-20240425180416908" style="zoom:50%;" />

---

<img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240425200011948.png" alt="image-20240425200011948" style="zoom:100%;" />

https://test-diaries.seeyouyima.com/v4/diary_analysis/mens_day_stat?birthday=1989-12-31&mens_day=5&mens_end_date=2024-04-23&period_day=27&period_end_date=1970-01-01&period_start_date=2024-04-17&pre_mens_end_date=2024-03-23&pre_period_end_date=2024-04-16&pre_period_start_date=2024-03-20

---

2024年04月26日14:12:11



修改UI

<img src="/Users/meetyou/Downloads/tapd_21039721_base64_1713929378_8.png.jpeg" alt="tapd_21039721_base64_1713929378_8.png" style="zoom:25%;" />



1~4  OK

6.**self**.heightLabel.imy_top = 34;   顶臀长  向上偏移 3个像素点 (37 ---14)  IMYYunyuPregnancyBabyInfoView

7.IMYYunyuInviteView

按钮修改  

.**self**.button.frame = CGRectMake(0, 0, ceil(sizeToFitFrame.size.width)+ 7 + 6, 28);  (    宽 + 7,高度 28 )

**self**.button.titleLabel.font = [UIFont systemFontOfSize:13];

​	邀请宝爸一起记录孕期、了解孕期知识: 字体修改14

**self**.titleLabel.font = [UIFont systemFontOfSize:14];

8.按钮 字体/宽高

IMYYYLamaPeriodHeadInfoCell

[**self**.guideView mas_makeConstraints:^(MASConstraintMaker *make) {

​    make.leading.equalTo(containerView);

​    make.bottom.equalTo(containerView);

​    make.top.equalTo(**self**.subtitleLabel.mas_bottom).offset(12);

​    make.size.mas_equalTo(CGSizeMake(80, 28));

  }];

9.花朵字体

**self**.periodLabel.attributedText = [**self** attributedStringWithPregnancyRates:odds integerFontSize:21 decimalFontSize:13];



10.正文修改  13/15  统一修改 14

IMYBabyChangeItemTagView

\- (CGFloat)setTagTextWithModel:(IMYLamaBabyGrowupHeaderModel*)model isTagClearBg:(**BOOL**)isTagClearBg {

  **self**.curModel = model;

  **if** (**self**.isBabyHomeAB){

​    **if** (imy_isBlankString(model.image_url)) { //无动图

​      **self**.tagLabel.font = [UIFont imy_MediumFontWith:14];

​      **self**.contentLabel.font = [UIFont imy_FontWith:14];

​    } **else** {

​      **self**.tagLabel.font = [UIFont imy_MediumFontWith:14];

​      **self**.contentLabel.font = [UIFont imy_FontWith:14];

​    }

  }

11.居中处理 IMYYunyuBRGuidanceHeaderView

[**self**.subLabel mas_makeConstraints:^(MASConstraintMaker *make) {

​    make.left.mas_equalTo(**self**.titleLabel.mas_right).offset(8);

​    make.centerY.equalTo(**self**.titleLabel);

  }];



12/13.

IMYYunyuBREmptyStateCell

居中处理

[**self**.ageLabel mas_makeConstraints:^(MASConstraintMaker *make) {

​    make.left.equalTo(**self**.dateLabel.mas_right).mas_offset(8);

​    make.centerY.equalTo(**self**.dateLabel);

  }];



label.font = [UIFont imy_MediumFontWith:17];   (16 换17)







---

记录 修改UI



1.IMYCalendarViewController_v2

padding 间距 3--->2    17 --- >16

```
- (void)initToastBarPosition {
    CGFloat offset = 13;
    CGFloat padding = 2;
    
    _img_physiology.imy_left = offset;
    _lb_physiology.imy_left = _img_physiology.imy_right + padding;

    _img_yuche.imy_left = _lb_physiology.imy_right + 16;
    _lb_yuche.imy_left = _img_yuche.imy_right + padding;

    _img_risk.imy_left = _lb_yuche.imy_right + 16;
    _lb_risk.imy_left = _img_risk.imy_right + padding;
    _lb_safety.hidden = YES;
    _img_safety.hidden = YES;

    _img_pailuan.imy_left = _lb_risk.imy_right + 16;
    _lb_pailuan.imy_left = _img_pailuan.imy_right + padding;

    _img_info_bar_arrow.imy_right = SCREEN_WIDTH - 16;
    if (SCREEN_WIDTH == 320) {
        _img_info_bar_arrow.imy_right = SCREEN_WIDTH - 8;
    }
}
```

2. IMYCalendarToastBar  感叹号 左边距10 -->12

   ```
   - (UIImageView *)image {
       if (!_image) {
           _image = [[UIImageView alloc] initWithFrame:CGRectMake(12, (self.imy_height - 12) / 2, 12, 12)];
       }
       return _image;
   }
   ```

3. 计划 详情内仍然字体 ;      IMYRecordActionCell_v2

   ```
   self.detailLabel.font = [UIFont systemFontOfSize:17] 好几处
   ```

   

4.方块到文字间距 IMYRecordPregnancyMsgBar    3-->2

```
 [cycleView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.left.mas_equalTo(view.mas_left);
        make.right.mas_equalTo(label.mas_left).offset(-2);
        make.centerY.mas_equalTo(label.mas_centerY);
        make.size.mas_equalTo(CGSizeMake(10, 10));
    }];
```

5. 是正确的  不需要改

   <img src="/Users/meetyou/Library/Application Support/typora-user-images/image-20240427114414815.png" alt="image-20240427114414815" style="zoom:20%;" />





<img src="/Users/meetyou/Downloads/tapd_21039721_base64_1713947816_43.png" alt="tapd_21039721_base64_1713947816_43" style="zoom:50%;" />

---



2024年04月28日16:09:44



[bugfix]【【iOS】【经期健康度分析】无上周期时雷达图各维度分数应不显示上升/下降icon】
https://www.tapd.meiyou.com/21039721/bugtrace/bugs/view/1121039721001275903





---

2024年04月30日16:01:33



待办 : 长图更新









---

2024年05月06日11:09:56

//胎动小圆圈未显示,原因是 发送通知时  view 都没有初始化 ,所以无法接受通知

**BOOL** isAbNormalExit = [[[IMYUserDefaults standardUserDefaults] objectForKey:kIsExitAbNormal] boolValue];







