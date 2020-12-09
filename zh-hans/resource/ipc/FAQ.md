

1. 调用 startPreview 后，没有出现视频画面，也没有任何错误反馈
   * 检查 camera 对象是否创建成功。如果获取到的 camera 对象为 nil，检查 p2pType 是否 为 “1”。p2pType 为 “1” 时，需要导入 `TuyaSmartCameraT`，这个模块由于不再维护，建议联系厂商升级摄像机固件。
   * 检查 p2p 通道是否连接成功。`startPreivew` 必须在收到 `cameraDidConnected:` 回调以后调用。
   * 检查一下 `videoView.frame`的大小，请不要将`videoView.frame`设置得太大，否则会导致 OpenGL ES 的渲染缓冲帧失效，无法渲染。可以通过`videoView.tuya_setScaled`方法来放大视频图像。
2. 调用 `- (void)destory`方法后崩溃
   * 在老的版本中，有一个 bug，p2pType 为 1 的时候，调用 destory 方法确实会崩溃。在 3.1.1 以后的版本中已经修复。
   * 请在调用 `destory` 方法之前，停止预览或者回放操作，并调用 `disConnect` 断开 p2p 通道。
3. 在使用模拟器调试的时候，画面绿屏，或者某些摄像头预览时崩溃。
   * 有些摄像头采用的硬件解码，模拟器不支持，请使用真机调试。
4. 音视频不同步
   * 可以先让设备厂商确认一下，音频帧的时间戳和视频帧的时间戳是否一样。
5. 存储卡录像回放时，播放失败
   * 请确认在开始播放前有请求获取当天的存储卡录像视频片段。
   * 请检查参数是否正确，`playTime` 需要大于等于 `startTime`，小于 `endTime`。
6. 已经接入过其他视频方案，导入 Camera  SDK 后，编译报错或者运行时崩溃
   * 这是由于 FFmpeg 库版本冲突，可以将已集成的视频播放库打包成动态库来解决冲突。
7. 视频图像两边（上下或者左右）有黑边
   * 通过 `camera.videoView` 获取到视频渲染视图后，开发者可以自行对`videoView`设置大小。视频图像的大小是摄像机设备决定的，当视频图像的宽高比和`videoView`的宽高比不一致时，为了避免图像拉伸变形，会保持视频图像的宽高比，图像两边就会留有黑边。如果需要拉伸铺满整个`videoView`，可以将 `videoView.scaleToFill`设置为`YES`。
   * 如果不想让视频图像拉伸变形，在获取视频流成功之后，可以通过`camera.getCurViewWidth`和`camera.getCurViewHeight`两个属性获取到当前视频图像的宽和高，从而计算出视频图像的宽高比，再根据视频图像的宽高比来计算出`videoView` 的大小，就可以避免图像留有黑边的情况。