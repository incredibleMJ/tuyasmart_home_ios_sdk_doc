## Preparation for Integration

### Register Tuya Developer Account

Go to the [Tuya Smart Development Platform](https://iot.tuya.com) to register a developer account, create products and create function points, etc. Please refer to the [Contact workflow](https://docs.tuya.com/cn/overview/dev-process.html) for details.

### Create an SDK APP

1.Log in to [IoT Platform](https://iot.tuya.com/).

**Note**: If you do not have a Tuya account, register first.

2.Go to the "**App**" panel, and select **Development** under the "**App SDK**" side menu, then click **Create**.

![Obtain App SDK](./images/getSDK_en.png)

4.Enter app related information, and then click **OK**.

- **App name**: Enter the App name.
- **iOS Bundle ID**: Enter the iOS app bundle ID. The recommended format is com.xxxxx.xxxxx.
- **Android Package Name**: Enter the Android app package name. It is not necessary to be consistent with iOS bundle ID.
- **URL Scheme**: It is not required. If it is not entered, the system will automatically generate one URL Scheme according to the package name.

![Obtain App SDK](./images/app_info_en.png)

5.You can choose the option you need according to actual needs, support multiple selections, and then integrate the SDK according to Podfile and Gradle.

![Obtain App SDK](./images/integrate_SDK_en.png)

6.Click **Obtain Key** to get SDK AppKey, AppSecret, security picture and other information.

![Obtain App SDK](./images/key_and_secret_en.png)
