## Camera device

After get the device list in home, you can determine whether it is a smart camera device based on the type of device. If it is a smart camera device, you can create a camera object based on the information in `TuyaSmartDeviceModel`.

### Determine if it is a smart camera

You can judge whether the device is a camera through the interface in the `TuyaSmartDeviceModel+IPCSDK` category.

**Example**

ObjC

```objc
[[TuyaSmartHomeManager new] getHomeListWithSuccess:^(NSArray<TuyaSmartHomeModel *> *homes) {
		[homes enumerateObjectsUsingBlock:^(TuyaSmartHomeModel * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
				TuyaSmartHome *home = [TuyaSmartHome homeWithHomeId:obj.homeId];
				[home getHomeDetailWithSuccess:^(TuyaSmartHomeModel *homeModel) {
						[home.deviceList enumerateObjectsUsingBlock:^(TuyaSmartDeviceModel * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            		if ([obj isIPCDevice]) {
                		NSLog(@"%@ is a smart camera", obj.name);
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
                    print(deviceModel.name!, "is a smart camera")
                }
            })
        }, failure: { error in

        })
    })
}) { error in

}
```

> Note that the sample code here is only to show the simplest process of filtering out the camera device. In actual development, the device should be displayed and managed according to the logic of UI interaction.

### Camera instance

**Class and Protocol**

|Class (Protocol) | Description |
| ----------------------- | ------------------------- ------------------------------- |
|TuyaSmartCameraFactory | Utility class for creating camera configurations and camera objects |
|TuyaSmartCameraConfig | Camera configuration class, developers do not need to care about its properties |
|TuyaSmartCameraType | Camera interface protocol, there are different specific implementations depending on the type of camera firmware |
|TuyaSmartCameraDelegate | Camera delegate, the result feedback of camera function method will be callback through delegate method |

`TuyaSmartCameraFactory` provides factory methods for creating camera control objects.

**Declaration**

Create a camera instance object.

```objc
+ (id<TuyaSmartCameraType>)cameraWithP2PType:(id)type deviceId:(NSString *)devId delegate:(id<TuyaSmartCameraDelegate>)delegate;
```

**Parameters**

| Parameter | Description        |
| --------- | ------------------ |
| type      | P2p type of camera |
| devId     | Device id          |
| delegate  | Camera delegate    |

**Return**

| Type                    | Description                                                |
| ----------------------- | ---------------------------------------------------------- |
| id<TuyaSmartCameraType> | The concrete implementation object of the camera interface |



### P2p type

The IPC SDK supports three p2p channel implementation. The SDK initializes different camera specific implementation objects according to the p2p type. Get the p2p type of the device through the interface in `TuyaSmartDeviceModel+IPCSDK`.

**Declaration**

Get p2p type

```objc
- (NSInteger)p2pType;
```



**Example**

ObjC

```objc
id<TuyaSmartCameraType> camera = [TuyaSmartCameraFactory cameraWithP2PType:@(deviceModel.p2pType) deviceId:deviceModel.devId delegate:self];
```

Swift

```swift
let camera = TuyaSmartCameraFactory.camera(withP2PType: deviceModel.p2pType(), deviceId: deviceModel.devId, delegate: self)
```

> Note that in the above code, the `self` passed in the delegate parameter needs to implement the `TuyaSmartCameraDelegate` protocol.
