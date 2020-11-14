# Detection alarm

Tuya smart camera usually has the ability of detecting alarm. There are two main types of alarm detection, sound detection and motion detection. When the device detects a sound or object moving, it sends a warning message, and if your App is integrated with push, it will also receive a push notification.  Integrate push refer to[ Integrate push](https://tuyainc.github.io/tuyasmart_home_ios_sdk_doc/en/resource/Push.html).

The alarm message usually comes with a screenshot of the current video.

Direct-powered doorbell equipment, has ability to provide video messages. When someone rings the doorbell, the device can upload a video message, which is also picked up by an alarm message with a six-second encrypted video.

## Message list

**Class**

| Class                  | Description                                     |
| ---------------------- | ----------------------------------------------- |
| TuyaSmartCameraMessage | Camera detection alarm event message management |



### Initialize

Before getting the list of messages, you need to initialize the message manager with the device id and time zone.

**Declaration**

Initilize message manager.

```objc
- (instancetype)initWithDeviceId:(NSString *)devId timeZone:(NSTimeZone *)timeZone;
```

**Parameters**

| Parameter | Description                                         |
| --------- | --------------------------------------------------- |
| devId     | Deivce id of camera                                 |
| timeZone  | Time zone, default is `[NSTimeZone systemTimeZone]` |



### Message calendar

You can use the SDK to look up the date of the alarm message in a certain year and a certain month, so that it can be displayed visually on the calendar.

**Declaration**

Gets the date on which the message was logged.

```objc
- (void)messageDaysForYear:(NSInteger)year
                     month:(NSInteger)month
                   success:(void (^)(NSArray<NSString *> *result))success
                   failure:(void (^)(NSError *error))failure;
```

**Parameters**

| Parameter | Description                                                  |
| --------- | ------------------------------------------------------------ |
| year      | Year，ex: 2020                                               |
| month     | Month, ex: 2                                                 |
| success   | Success callback, return the days of the month which has messages |
| failure   | Failure callback, error indicates an error message           |

>  The `result` is an array, elements is string. ex: ["01", "11", "30"].



### Message type

There are many types of detection alarm messages according to the trigger mode, some of which can be divided into a large category. The SDK provides a list to get the default categories for sorting the query alarm messages.

**Declaration**

Get the list of message categories.

```objc
- (void)getMessageSchemes:(void (^)(NSArray<TuyaSmartCameraMessageSchemeModel *> *result))success
                  failure:(void (^)(NSError *error))failure;
```

**Parameters**

| Parameter | Description                                        |
| --------- | -------------------------------------------------- |
| success   | Success callback, return message category models   |
| failure   | Failure callback, error indicates an error message |



**TuyaSmartCameraMessageSchemeModel**

| Field    | Type     | Description                                      |
| -------- | -------- | ------------------------------------------------ |
| describe | NSString | Message category description                     |
| msgCodes | NSArray  | Message types that the message category contains |

> When getting a list of messages, the value of the `msgCodes` attribute of the message category can be passed in to get all types of alarm messages that this category contains.

The message type represents the trigger form of the alarm message, which is reflected in the code as the `msgCode` attribute of the alarm message data model.

**Message Type**

| Type            | Description                |
| --------------- | -------------------------- |
| ipc_motion      | Motion detecting           |
| ipc_doorbell    | Doorbell ring              |
| ipc_dev_link    | Devices linkage            |
| ipc_passby      | Someone passby             |
| ipc_linger      | Someone linger             |
| ipc_leave_msg   | Doorbell message           |
| ipc_connected   | Doorbell ring has answered |
| ipc_unconnected | Doorbell not answered      |
| ipc_refuse      | Doorbell resisted          |
| ipc_human       | Human detection            |
| ipc_cat         | Pet detection              |
| ipc_car         | Car detection              |
| ipc_baby_cry    | Baby cry                   |
| ipc_bang        | Abnormal sound             |
| ipc_face        | Face detection             |
| ipc_antibreak   | Forced demolition alarm    |
| ipc_low_battery | Low power alarm            |

> Depending on the device's capabilities, the types of messages that can be triggered can vary. 

### Message list

**Declaration**

Get message list.

```objc
- (void)messagesWithMessageCodes:(NSArray *)msgCodes
                          Offset:(NSInteger)offset
                           limit:(NSInteger)limit
                       startTime:(NSInteger)startTime
                         endTime:(NSInteger)endTime
                         success:(void (^)(NSArray<TuyaSmartCameraMessageModel *> *result))success
                         failure:(void (^)(NSError *error))failure;
```

**Parameters**

| Parameter | Description                                                  |
| --------- | ------------------------------------------------------------ |
| msgCodes  | Array of message types, pass nil to get all types of messages |
| offset    | Offset, 0 means starting with the first alarm message        |
| limit     | Page size with a maximum of 200                              |
| startTime | Gets messages that is reported no earlier than startTime     |
| endTime   | Gets messages that is reported no later than endTime         |
| success   | Success callback, return message data models array           |
| failure   | Failure callback, error indicates an error message           |

**Declaration**

Batch delete alarm message.

```objc
- (void)removeMessagesWithMessageIds:(NSArray *)msgIds
                             success:(void (^)(void))success
                             failure:(void (^)(NSError *))failure;
```

**Parameters**

| Parameter | Description                                        |
| --------- | -------------------------------------------------- |
| msgIds    | Message ids to be deleted                          |
| success   | Success callback                                   |
| failure   | Failure callback, error indicates an error message |



**TuyaSmartCameraMessageModel**

| Field          | Type      | Description                            |
| -------------- | --------- | -------------------------------------- |
| dateTime       | NSString  | Formatted string of date               |
| msgTypeContent | NSString  | Message type description               |
| attachPic      | NSString  | Url of picture attachment              |
| attachVideos   | NSArray   | Urls of video attachments              |
| msgSrcId       | NSString  | Deivice id of which report the message |
| msgContent     | NSString  | Message content                        |
| msgTitle       | NSString  | Message title                          |
| msgId          | NSString  | Message id                             |
| msgCode        | NSString  | Message type                           |
| time           | NSInteger | Unix timestamp of message report time  |

Depending on the message type, there may be different attachments. The `attachPic` property gets the url of the image attachment, and the `attachVideos` property gets the urls of the video attachments. Normally, this property has only one element.

### Encrypted image

In order to ensure the privacy and security of data, you can choose to use encrypted picture messages.

**Declaration**

Enable image encryption, the default is off. After opening, the pictures carried in the message will be encrypted, you need to use the `TYEncryptImage` component to display the pictures, refer to [Encrypt Image](./encryptImage.md).

```objc
@property (nonatomic, assign) BOOL enableEncryptedImage;
```

## Video message

The video attachment in the video message is the encrypted video, which needs to be played through the interface provided by `TuyaSmartCameraKit/TuyaSmartCameraMessageMediaPlayer`.

**Declaration**

Get video view

```objc
- (UIView<TuyaSmartVideoViewType> *)videoView;
```

**Declaration**

Play attachments in alarm messages

```objc
- (void)playMessage:(TuyaSmartCameraMessageModel *)messageModel attachmentType:(TuyaCameraMessageAttachmentType)attachmentType success:(void(^)(void))success failure:(void(^)(int errCode))failure finished:(void(^)(int errCode))onFinish;
```

**Parameters**

| Parameter      | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| messageModel   | Detect message mode                                          |
| attachmentType | The type of attachment that needs to be played. The warning message may include video messages and audio messages |
| success        | Success callback                                             |
| failure        | Failure callback                                             |
| onFinish       | Finished callback, errCode indicates the error code, 0 means the end of normal playback |

**Declaration**

Play attachment in alarm message

```objc
- (void)playMessageAttachment:(NSString *)attachmentPath type:(TuyaCameraMessageAttachmentType)attachmentType success:(void(^)(void))success failure:(void(^)(int errCode))failure finished:(void(^)(int errCode))onFinish;
```

**Parameters**

| Parameter      | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| attachmentPath | The url of the attachment                                    |
| attachmentType | The type of the attachment                                   |
| success        | Success callback                                             |
| failure        | Failure callback                                             |
| onFinish       | Finished callback, errCode indicates the error code, 0 means the end of normal playback |

**Declaration**

Pause playing

```objc
- (int)pausePlay:(TuyaCameraMessageAttachmentType)attachmentType;
```

**Declaration**

Resume playing.

```objc
- (int)resumePlay:(TuyaCameraMessageAttachmentType)attachmentType;
```

**Declaration**

Stop playing.

```objc
- (void)stopPlay:(TuyaCameraMessageAttachmentType)attachmentType;
```

**Declaration**

Mute enable

```objc
- (void)enableMute:(BOOL)mute success:(void(^)(void))success failure:(void (^)(NSError * error))failure;
```

**Parameters**

| Parameter | Description      |
| --------- | ---------------- |
| mute      | Whether to mute  |
| success   | Success callback |
| failure   | Failure callback |



**Return**

| Type | Description                                                  |
| ---- | ------------------------------------------------------------ |
| int  | Error code, indicating the reason for the failure, 0 indicates succeed |



**TuyaSmartCameraMessageMediaPlayerDelegate**

Message media player delegate protocol.

**Declaration**

Video frame data callback

```objc
- (void)mediaPlayer:(TuyaSmartCameraMessageMediaPlayer *)player didReceivedFrame:(CMSampleBufferRef)frameBuffer videoFrameInfo:(TuyaSmartVideoFrameInfo)frameInfo;
```

**Parameters**

| Parameter           | Description          |
| ------------------- | -------------------- |
| player              | Media player object  |
| frameBuffer         | Video frame YUV data |
| frameInfo           | Video frame info     |
| frameInfo.nDuration | Video total time     |
| frameInfo.nProgress | Video play progress  |

**Declaration**

Audio frame data callback

```objc
- (void)mediaPlayer:(TuyaSmartCameraMessageMediaPlayer *)player  didReceivedAudioFrameInfo:(TuyaSmartAudioFrameInfo)frameInfo;
```

**Parameters**

| Parameter           | Description         |
| ------------------- | ------------------- |
| player              | Media player object |
| frameInfo           | Audio frame info    |
| frameInfo.nDuration | Audio total time    |
| frameInfo.nProgress | Audio play progress |

> Before version 3.20.0, use the interface in `TuyaSmartCloudManager` to play the attachments in the warning message. Version 3.20.0 has been deprecated, please modify it as soon as possible.

### Alarm message and playback

There is no direct correlation between the alarm message and the record videos in memory card, the only correlation is that in the memory card event recording mode, the trigger reason and time point of the alarm message and record video are the same.

The alarm message is saved in the Tuya cloud, and the video recording of the memory card is saved in the camera's memory card, and the video in the memory card may be overwritten when the capacity is insufficient. The switch recorded by the memory card is not related to the switch that detects the alarm, so even in the mode of recording the memory card event, the alarm message and the video recording in the memory card are not one-to-one.

However, if there is a video recording at the time when the alarm message occurs, the SDK does not provide an interface for such correlation search. The developer can establish the correlation by searching whether there is a corresponding video recording in the video clip of the memory card on the same day by the trigger time of the alarm message.

**Example**

ObjC

```objc
- (void)enableDetect {
		if ([self.dpManager isSupportDP:TuyaSmartCameraMotionDetectDPName]) {
        bool motionDetectOn = [[self.dpManager valueForDP:TuyaSmartCameraMotionDetectDPName] tysdk_toBool];
        if (!motionDetectOn) {
            [self.dpManager setValue:@(YES) 
             									 forDP:TuyaSmartCameraMotionDetectDPName 
             								 success:^(id result) {
                // Enable motion detection successfully
            } failure:^(NSError *error) {
								// Network error
            }];
        }
      	[self.dpManager setValue:TuyaSmartCameraMotionHigh
                         	 forDP:TuyaSmartCameraMotionSensitivityDPName
                         success:^(id result) {
            // Successfully set the motion detection sensitivity to high sensitivity
        } failure:^(NSError *error) {
            // Network error
        }];
    }
}

- (void)viewDidLoad {
    [super viewDidLoad];
    self.cameraMessage = [[TuyaSmartCameraMessage alloc] initWithDeviceId:self.devId timeZone:[NSTimeZone defaultTimeZone]];
    [self.cameraMessage getMessageSchemes:^(NSArray<TuyaSmartCameraMessageSchemeModel *> *result) {
	      // Message category models
        self.schemeModels = result;
				// Get messages of the first category
        [self reloadMessageListWithScheme:result.firstObject];
    } failure:^(NSError *error) {
        // Network error
    }];
}

- (void)reloadMessageListWithScheme:(TuyaSmartCameraMessageSchemeModel *)schemeModel {
    NSDateFormatter *formatter = [NSDateFormatter new];
    formatter.dateFormat = @"yyyy-MM-dd";
    NSDate *date = [formatter dateFromString:@"2020-02-17"];
	  // Get the top 20 alarm messages from zero on February 17, 2020 to the present
    [self.cameraMessage messagesWithMessageCodes:schemeModel.msgCodes Offset:0 limit:20 startTime:[date timeIntervalSince1970] endTime:[[NSDate new] timeIntervalSince1970] success:^(NSArray<TuyaSmartCameraMessageModel *> *result) {
        self.messageModelList = result;
    } failure:^(NSError *error) {
        // Network error
    }];
}

```

Swift

```swift
func enableDectect() {
    guard self.dpManager.isSupportDP(.motionDetectDPName) else {
        return;
    }
    if let isMontionDetectOn = self.dpManager.value(forDP: .motionDetectDPName) as? Bool, !isMontionDetectOn {
        self.dpManager.setValue(true, forDP: .motionDetectDPName, success: { _ in
            // Enable motion detection successfully
        }) { _ in
            // Network error
        }
    }
    self.dpManager.setValue(TuyaSmartCameraMotion.high, forDP: .motionSensitivityDPName, success: { _ in
        // Successfully set the motion detection sensitivity to high sensitivity
    }) { _ in
        // Network error
    }
}

override func viewDidLoad() {
    super.viewDidLoad()
    self.cameraMessage = TuyaSmartCameraMessage(deviceId: self.devId, timeZone: NSTimeZone.default)
    self.cameraMessage.getSchemes({ result in
				// Get messages of the first category
        self.schemeModels = result
        if let schemeModel = result?.first {
            reloadMessage(schemeModel: schemeModel)
        }
    }) { _ in
        // Network error
    }
}
    
func reloadMessage(schemeModel: TuyaSmartCameraMessageSchemeModel) {
    let formatter = DateFormatter()
    formatter.dateFormat = "yyyy-MM-dd"
    let date = formatter.date(from: "2020-02-17")
	  // Get the top 20 alarm messages from zero on February 17, 2020 to the present
    self.cameraMessage.messages(withMessageCodes: schemeModel.msgCodes, offset: 0, limit: 20, startTime: Int(date!.timeIntervalSince1970), endTime: Int(Date().timeIntervalSince1970), success: { result in
        self.messageModelList = result;
    }) { _ in
        // Network error
    }
}
```



