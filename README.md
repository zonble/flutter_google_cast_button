![pub](https://img.shields.io/pub/v/flutter_google_cast_button.svg)
# flutter_google_cast_button

A Flutter plugin which provides the cast button and sync cast state with it.

# Use this package as a library
### 1. Depend on it
Add this to your package's pubspec.yaml file:

```yaml
dependencies:
  flutter_google_cast_button: {last_version}
```

### 2. Install it
You can install packages from the command line:

with pub:

```console
$ pub get
```
with Flutter:

```console
$ flutter packages get
```
Alternatively, your editor might support pub get or flutter packages get. Check the docs for your editor to learn more.

### 3. Import it
Now in your Dart code, you can use:

```dart
import 'package:flutter_google_cast_button/flutter_google_cast_button.dart';
```

# Setting up for each platform

## Android

### 0. Support AndroidX.
Add following properties to `gradle.properties`. 
``` properties
android.useAndroidX=true
android.enableJetifier=true
```
### 1. Add google cast dependency.
Add `play-services-cast-framework` dependency into `android/build.gradle`
``` groovy
implementation "com.google.android.gms:play-services-cast-framework:16.2.0"
``` 

### 2. Create CastOptionsProvider class for configure cast library. EX:
``` kotlin
class DefaultCastOptionsProvider : OptionsProvider {
    override fun getCastOptions(context: Context): CastOptions {
        return CastOptions.Builder()
                .setReceiverApplicationId(CastMediaControlIntent.DEFAULT_MEDIA_RECEIVER_APPLICATION_ID)
                .build()
    }

    override fun getAdditionalSessionProviders(context: Context): List<SessionProvider>? {
        return null
    }
}
```
### 3. Add options provider metadata.
Add `meta-data` to project's `AndroidManifest.xml`. EX:

``` xml
<meta-data
    android:name="com.google.android.gms.cast.framework.OPTIONS_PROVIDER_CLASS_NAME"
    android:value="github.showang.flutter_google_cast_button_example.DefaultCastOptionsProvider" />
``` 

### 4. Setup/dispose android context.
Add following code into `MainActivity`. (Avoid context leak)

``` kotlin
override fun onStart() {
    super.onStart()
    FlutterGoogleCastButtonPlugin.instance.initContext(this)
}

override fun onStop() {
    FlutterGoogleCastButtonPlugin.instance.disposeContext()
    releaseMediaController()
    super.onStop()
}
``` 

## iOS

1. Run `pod install` before open Xcode.

2. Initializing CastContext when `application didFinishLaunching`. EX:
``` swift
  let kReceiverAppID = kGCKDefaultMediaReceiverApplicationID
  let kDebugLoggingEnabled = true

  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?
  ) -> Bool {
  	let criteria = GCKDiscoveryCriteria(applicationID: kReceiverAppID)
  	let options = GCKCastOptions(discoveryCriteria: criteria)
  	GCKCastContext.setSharedInstanceWith(options)

  	// Enable logger.
  	GCKLogger.sharedInstance().delegate = self

    GeneratedPluginRegistrant.register(with: self)
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
}
```