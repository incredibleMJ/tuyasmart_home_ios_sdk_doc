## 摄像机

通过家庭获取到设备列表后，就可以根据设备的类型来判断是否是智能摄像机设备，如果是智能摄像机设备，则可以根据`TuyaSmartDeviceModel`中的信息来创建摄像机对象。IPC SDK 通过分类`TuyaSmartDeviceModel+IPCSDK`提供了几个针对摄像机设备的工具接口。

### 判断是否是智能摄像机

**接口说明**

判断设备是否是智能摄像机设备

```objc
- (BOOL)isIPCDevice;
```

**示例代码**

ObjC

```objc
[[TuyaSmartHomeManager new] getHomeListWithSuccess:^(NSArray<TuyaSmartHomeModel *> *homes) {
		[homes enumerateObjectsUsingBlock:^(TuyaSmartHomeModel * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
				TuyaSmartHome *home = [TuyaSmartHome homeWithHomeId:obj.homeId];
				[home getHomeDetailWithSuccess:^(TuyaSmartHomeModel *homeModel) {
						[home.deviceList enumerateObjectsUsingBlock:^(TuyaSmartDeviceModel * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            		if ([obj isIPCDevice]) {
                		NSLog(@"%@ 是一个智能摄像机设备", obj.name);
            		}
        }];
        } failure:^(NSError *error) {

        }];
    }];
} failure:^(NSError *error) {

}];
```

Swift

```swift
let homeManager = TuyaSmartHomeManager()
homeManager.getHomeList(success: { homeList in
    homeList?.forEach({ homeModel in
        let home = TuyaSmartHome(homeId: homeModel.homeId)
        home?.getDetailWithSuccess({ _ in
            home?.deviceList.forEach({ deviceModel in
                if deviceModel.isIPCDevice() {
                    print(deviceModel.name!, "是一个智能摄像机设备")
                }
            })
        }, failure: { error in

        })
    })
}) { error in

}
```

> 注意，这里的示例代码只是展示筛选出摄像头设备的最简单流程，实际开发中，应该根据 UI 交互的逻辑来展示和管理设备。

### 摄像机实例

**类（协议）说明**

| 类名（协议名）          | 说明                                                     |
| ----------------------- | -------------------------------------------------------- |
| TuyaSmartCameraFactory  | 创建摄像机配置和摄像机对象的工具类                       |
| TuyaSmartCameraConfig   | 摄像机配置类，开发者不用关心它的属性                     |
| TuyaSmartCameraType     | 摄像机接口协议，根据摄像机固件类型不同，有不同的具体实现 |
| TuyaSmartCameraDelegate | 摄像机代理，摄像机功能方法的结果反馈都将通过代理方法回调 |

`TuyaSmartCameraFactory` 提供创建摄像机控制对象的工厂方法。

**接口说明**

创建摄像机实例对象

```objc
+ (id<TuyaSmartCameraType>)cameraWithP2PType:(id)type deviceId:(NSString *)devId delegate:(id<TuyaSmartCameraDelegate>)delegate;
```

**参数说明**

| 参数     | 说明                            |
| -------- | ------------------------------- |
| type     | p2p 类型，参数使用 `Number`类型 |
| devId    | 设备 id                         |
| delegate | 代理对象                        |

**返回值**

| 类型                    | 说明                     |
| ----------------------- | ------------------------ |
| id<TuyaSmartCameraType> | 摄像机接口的具体实现对象 |



### P2P 类型

涂鸦智能摄像机支持三种 p2p 通道实现方案，IPC SDK 会根据 p2p 类型来初始化不同的摄像机具体实现的对象，通过下面的方式获取设备的 p2p 类型。3.22.0 版本开始，`TuyaSmartDeviceModel+IPCSDK` 增加获取 p2p 类型的接口。

**接口说明**

获取 p2p 类型，3.22.0 版本开始支持

```objc
- (NSInteger)p2pType;
```



**示例代码**

ObjC

```objc
// deviceModel 为设备列表中的摄像机设备的数据模型
id<TuyaSmartCameraType> camera = [TuyaSmartCameraFactory cameraWithP2PType:@(deviceModel.p2pType) deviceId:deviceModel.devId delegate:self];
```

Swift

```swift
let camera = TuyaSmartCameraFactory.camera(withP2PType: deviceModel.p2pType(), deviceId: deviceModel.devId, delegate: self)
```

> 注意，上面代码中，`delegate` 参数传入的 `self` 需要实现 `TuyaSmartCameraDelegate` 协议。