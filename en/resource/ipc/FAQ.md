

1. After calling startPreview, there is no video picture and no error feedback.

   * Check if the camera object was created successfully. If the obtained camera object is nil, check if p2pType is "1". When p2pType is "1", `TuyaSmartCameraT` needs to be imported. Since this module is no longer maintained, it is recommended to contact the manufacturer to upgrade the camera firmware.
   * Check if the p2p channel is connected successfully. `startPreivew` must be called after receiving the` cameraDidConnected: `callback.
   * Check the size of `videoView.frame`, please do not set `videoView.frame` too large, otherwise it will cause the rendering buffer frame of OpenGL ES to fail and render it impossible. The video image can be enlarged by the `videoView.tuya_setScaled` method.

2. Crash after calling `-(void) destory` method.

   * In the old version, there was a bug. When p2pType is 1, calling destory method would indeed crash. This has been fixed in versions after 3.1.1.
   * Before calling the `destory` method, stop the preview or playback operation and call` disConnect` to disconnect the p2p channel.

3. When using the simulator to debug, the screen is green, or some cameras crash when previewing.

   * Some cameras use hardware decoding, which is not supported by the simulator, please use real machine debugging.

4. Audio and video are out of sync.

   * Please let the equipment manufacturer confirm whether the timestamp of the audio frame is the same as the timestamp of the video frame.

5. Playback fails during video playback of memory card.

   * Please confirm that you have requested to obtain the video clip of the memory card recorded on the day before starting the playback.
   * Please check whether the parameters are correct. `PlayTime` must be greater than or equal to` startTime` and less than `endTime`.

6. Already integrated other video solutions, after importing Tuya Smart Camera iOS SDK, compilation error or crash at runtime.

   * This is due to the conflict of FFmpeg library version. You can package the integrated library into a dynamic library to resolve the conflict.

7. There are black borders on both sides of the video image (up and down or left and right).

   * After get the video rendering view through `camera.videoView`, developers can set the size of `videoView` by themselves. The size of the video image is determined by the camera device. When the aspect ratio of the video image is inconsistent with the aspect ratio of `videoView`, in order to avoid image stretching and distortion, the aspect ratio of the video image will be maintained, and there will be left on both sides of the image black border. If you need to stretch and cover the entire `videoView`, you can set `videoView.scaleToFill` to `YES`.
   * If you don’t want the video image to be stretched or deformed, after receiving the video stream successfully, you can get the width and height of the current video image through the two properties of `camera.getCurViewWidth` and `camera.getCurViewHeight`, thereby calculating the aspect ratiot of the video image, and then calculate the size of `videoView` according to the aspect ratio of the video image, which can avoid the situation that the image has black borders.

8. After the App enters the background for a period of time, it crashes when re-entering the App, and the console log outputs the error reason: `Terminated due to signal 13`.

   * When entering the background, you need to stop the video stream and disconnect the p2p connection.

   * In the `main.m` file, the `SIGPIPE` signal processing need added to the implementation of the `int main(int argc, char * argv[])` method.

     ```objective-c
     int main(int argc, char * argv[]) {
         NSString * appDelegateClassName;
         @autoreleasepool {
             // Setup code that might create autoreleased objects goes here.
             appDelegateClassName = NSStringFromClass([AppDelegate class]);
             
             // handle SIGPIPE
             struct sigaction sa;
             sa.sa_handler = SIG_IGN;
             sigemptyset(&sa.sa_mask);
             sa.sa_flags = 0;
             if (sigaction(SIGPIPE, &sa, NULL) < 0) {
                 perror("cannot ignore SIGPIPE");
                 return -1;
             }
         }
         return UIApplicationMain(argc, argv, nil, appDelegateClassName);
     }
     ```

     