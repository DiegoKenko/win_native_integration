
# win_native_integration

This project demonstrates native Windows integration with Flutter, including communication between Dart and C++ code using platform channels.

## File: `windows/runner/flutter_window.cpp`

### Overview

This file implements the `FlutterWindow` class, which manages the native Windows window for the Flutter application. It demonstrates how to:
- Initialize and manage the Flutter engine and view controller on Windows.
- Set up a method channel for communication between Dart and native C++ code.
- Expose native Windows APIs (such as battery status) to Flutter via platform channels.

### Key Features

- **Battery Level Platform Channel**: Implements a method channel (`samples.flutter.dev/battery`) that allows Flutter code to request the current battery level from Windows using the `GetSystemPowerStatus` API.
- **Window Lifecycle Management**: Handles window creation, destruction, and message processing, integrating with the Flutter engine.
- **Plugin Registration**: Registers generated plugins with the Flutter engine.

### Main Components

- `FlutterWindow::OnCreate()`: Initializes the Flutter view controller, sets up the method channel, and registers plugins.
- `GetBatteryLevel()`: Retrieves the current battery level from the Windows system.
- `MessageHandler()`: Handles Windows messages and passes them to Flutter when appropriate.

### Example: Battery Level Channel

```cpp
flutter::MethodChannel<> channel(
		flutter_controller_->engine()->messenger(), "samples.flutter.dev/battery",
		&flutter::StandardMethodCodec::GetInstance());
channel.SetMethodCallHandler(
		[](const flutter::MethodCall<> &call,
			 std::unique_ptr<flutter::MethodResult<>> result)
		{
			if (call.method_name() == "getBatteryLevel")
			{
				int battery_level = GetBatteryLevel();
				if (battery_level != -1)
				{
					result->Success(battery_level);
				}
				else
				{
					result->Error("UNAVAILABLE", "Battery level not available.");
				}
			}
			else
			{
				result->NotImplemented();
			}
		});
```

This allows Dart code to call `getBatteryLevel` and receive the current battery percentage from Windows.

---

For more details, see the source code in `windows/runner/flutter_window.cpp`.
