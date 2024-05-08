字符串

```objective-c
[NSString stringWithFormat:@"%d",strength] 
```



打印引用计数

```
   NSLog(@"打印returnCount = %ld",CFGetRetainCount((__bridge CFTypeRef)(_api)));
```





//毫秒级别的 时间戳 差距
 ```objective-c
 double bef = [[NSDate date] timeIntervalSince1970] * 1000;
 double nextTime = [[NSDate date] timeIntervalSince1970] * 1000;
 double hca = nextTime - bef;
         NSLog(@"%f",hca);
 ```

//隔 几秒打印数据

```objective-c
@property (nonatomic,assign) double lastTime;


double cur_time = [[NSDate date] timeIntervalSince1970];
double time_past = cur_time - self.lastTime;
    if (time_past >= 2) {//两秒打印一次

        self.lastTime = cur_time;
    }
```

分类添加 属性: runtime

```objc
- (void)setMiguLivePublisherDelegate:(id<MGLivePublisherDelegate>)miguLivePublisherDelegate {
    objc_setAssociatedObject(self, "miguLivePublisherDelegate", miguLivePublisherDelegate, OBJC_ASSOCIATION_ASSIGN);
}

- (id<MGLivePublisherDelegate>)miguLivePublisherDelegate {
    return objc_getAssociatedObject(self, "miguLivePublisherDelegate");
}


-(void)setPublishStatus:(MGAPIPublishFlag)publishStatus
{
    objc_setAssociatedObject(self, @"mgl_publishStatus",@(publishStatus), OBJC_ASSOCIATION_ASSIGN);

}

-(MGAPIPublishFlag)publishStatus
{
    return [objc_getAssociatedObject(self, @"mgl_publishStatus") intValue];
}
```

