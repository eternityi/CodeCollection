# NSNotificationCenter

观察者只要向消息中心注册， 即可接受其他对象发送来的消息，消息发送者和消息接受者两者可以互相一无所知，完全解耦。这种消息通知机制可以应用于任意时间和任何对象，观察者可以有多个，所以消息具有广播的性质，只是需要注意的是，观察者向消息中心注册以后，在不需要接受消息时需要向消息中心注销，这种消息广播机制是典型的“Observer”模式。  
**使用消息机制的步骤：**

### 1. 观察者注册消息通知

```objectivec
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(getUserProfileSuccess:) name:@"Notification_GetUserProfileSuccess"object:nil];
```

* notificationObserver 观察者 : self 
* notificationSelector 处理消息的方法名: getUserProfileSuccess 
* notificationName 消息通知的名字: Notification\_GetUserProfileSuccess
* notificationSender 消息发送者 : 表示接收哪个发送者的通知，如果第四个参数为nil,接收所有发送者的通知

### 2. 发送消息通知

```objectivec
//UserProfile Is A Model
//@interface UserProfile : NSObject
[[NSNotificationCenter defaultCenter] postNotificationName:@"Notification_GetUserProfileSuccess" object:userProfile userInfo:nil];
```

* notificationName 消息通知的名字: Notification\_GetUserProfileSuccess
* notificationSender 消息发送者: userProfile

### **3. 观察者处理消息** 

```objectivec
- (void) getUserProfileSuccess: (NSNotification*) aNotification
{
    self.userProfile = [aNotification object];
    lblName.text = self.userProfile.Name; 
    lblEENO.text = self.userProfile.EENO; 
    lblNric.text = self.userProfile.NRIC; 
    lblBirthday.text = self.userProfile.Birthday; 
    lblHireDate.text = self.userProfile.Hiredate;
    txtMobilePhone.text = self.userProfile.Mobile; 
    txtEmail.text = self.userProfile.Email; 
}
```

* NSNotification 接受到的消息信息，主要含：
* Name: 消息名称 Notification\_GetUserProfileSuccess
* object: 消息发送者 userProfile
* userInfo: 消息传递的数据信息

### **4. 观察者注销，移除消息观察者**

虽然在 IOS 用上 ARC 后，不显示移除 NSNotification Observer 也不会出错，但是这是一个很不好的习惯，不利于性能和内存。

注销观察者有2个方法：

```objectivec
//a. 最优的方法，removeObserver: self，在 UIViewController.m 中：
-(void)dealloc {[[NSNotificationCenter defaultCenter] removeObserver:self];} //If you see the method you don't need to call [super dealloc]; here, only the method without super dealloc needed.
```

```objectivec
//b. 单个移除：
[[NSNotificationCenter defaultCenter] removeObserver:self name:@"Notification_GetUserProfileSuccess"object:nil];
```

