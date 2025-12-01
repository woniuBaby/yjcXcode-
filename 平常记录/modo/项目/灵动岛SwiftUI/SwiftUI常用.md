1. 倒计时时间显示

   ```swift
   let showTime = context.state.totalTime -  context.state.curTime
   let deliveryTimer: ClosedRange<Date> = Date()...Date().addingTimeInterval(showTime)
   Text(timerInterval: deliveryTimer, countsDown: true)
                                   .bold()
                                   .font(.caption)
                                   .foregroundColor(.white.opacity(0.8))
                                   .multilineTextAlignment(.center)
                                   .frame(width: 40)
   ```

   

2. Apple自带倒计时器

   ```swift
   //圆圈
   let value:Double = context.state.curTime / context.state.totalTime
                   ProgressView(value: value, total: 1) {
                       let healthLevel = Int(value * 100)
                       Text("\(healthLevel)")
                           .accessibilityLabel("Health level at \(healthLevel) percent.")
                   }
                   .progressViewStyle(.circular)
                   .tint(Color.green)
   
   //线性
    ProgressView(timerInterval: deliveryTimer, countsDown: false){
                       //上边文本
                   } currentValueLabel: {
                       //下边文本
                   }
                   .progressViewStyle(LinearProgressViewStyle(tint: .orange))
                   //                        .frame(height: height)
                   //                        .tint(.orange)
                   //                        .frame(width: geometry.size.width * (1-progress),height: height)
                   //                        .offset(x: geometry.size.width * progress - height / 2)
                   .scaleEffect(y: 4, anchor: .center)//扩大倍数
   ```

   

3. 铺满

   ```swift
   frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .center) // ✅ 居中铺满
   ```

   

4. 空view + 宽高

   ```
   Spacer().frame(height: 15)
   Spacer().frame(width: 15)
   ```

   

5. image

   ```swift
    Image(context.attributes.gameImageName)
                           .resizable()
                           .scaledToFit()
                           .frame(width: 50, height: 50)
    //下载连接图片                      
    let imageUrl = "https://easylink.cc/2cinmn"
                       AsyncImage(url: URL(string: imageUrl))
   //                        .resizable()
                           .scaledToFit()
                           .frame(width: 50, height: 50)
   ```

   

6. 点击

   ```swift
    HStack {
                           // 点击事件响应在SecneDelegate中
                           Link(destination: URL(string: "livetest://TIM")!) {
                               Text("前往花园")
                                   .font(.system(size: 16))
                                   .foregroundColor(.black)
                                   .bold()
                                   .frame(width: 80,height: 35,alignment: .center)
                               
                           }.background(Color.orange)
                               .clipShape(RoundedRectangle(cornerRadius: 15))
                       }
   ```

   

7. 文本text

   ```swift
   Text("\(context.state.itemSubName)")
                                       .lineLimit(1)
                                       .font(.system(size: 15))
                                       .foregroundColor(.white)
                                       .foregroundColor(.secondary)
                                       .multilineTextAlignment(.leading)
   ```

   

8. 1

9. 1

10. 1

11. 1

12. 1

13. 1

14. 1