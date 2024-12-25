# Implementing Deep Linking in Flutter

<p align="center">
  <img src="https://github.com/user-attachments/assets/51941b19-b867-45ca-aaf0-65e8b1ac94c1" style="max-width: 100%; height: auto;">
</p>

## Overview

This guide demonstrates how to implement deep linking in a Flutter application using the `go_router` package. It covers the setup process, route configuration, and testing deep links. 

Deep linking enables your app to respond to external URLs and navigate users directly to specific pages within the app. It improves the user experience by allowing users to access content quickly and seamlessly.

## Table of Contents

- [Implementing Deep Linking in Flutter](#implementing-deep-linking-in-flutter)
  - [Overview](#overview)
  - [Table of Contents](#table-of-contents)
  - [Why Use go_router?](#why-use-go_router)
  - [Setup Deep Linking in Flutter](#setup-deep-linking-in-flutter)
    - [Adding Dependencies](#adding-dependencies)
    - [Route Configuration](#route-configuration)
    - [AndroidManifest Configuration](#androidmanifest-configuration)
    - [Asset Links File for Release](#asset-links-file-for-release)
  - [Testing Deep Links](#testing-deep-links)
  - [Conclusion](#conclusion)
  - [References](#references)

## Why Use go_router?

The `go_router` package simplifies navigation and deep linking in Flutter applications. Unlike manually parsing URLs, `go_router` provides a structured approach to handle routing and state management efficiently. Key benefits include:

- Simplified route definitions.
- Dynamic URL handling.
- Integrated error handling.
- Declarative approach, making code more maintainable.

This guide is based on the [official Flutter deep linking documentation](https://docs.flutter.dev/ui/navigation/deep-linking).

## Setup Deep Linking in Flutter

### Adding Dependencies

Add the following packages to your `pubspec.yaml` file:

```yaml
dependencies:
  go_router: ^6.0.0
  app_links: ^6.3.3
```

Run the following command to install them:

```bash
flutter pub get
```

### Route Configuration

Create a `GoRouter` object in your `main.dart` to define the routes and handle navigation:

```dart
final GoRouter _router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => const HomePage(),
    ),
    GoRoute(
      path: '/landing',
      builder: (context, state) => const LandingPage(),
    ),
    GoRoute(
      path: '/products/:id',
      builder: (context, state) {
        final productId = state.params['id'];
        return ProductPage(productId: productId);
      },
    ),
    GoRoute(
      path: '/error',
      builder: (context, state) => ErrorPage(error: state.error.toString()),
    ),
  ],
);
```

### AndroidManifest Configuration

Update the `AndroidManifest.xml` file to handle deep links. Add an `<intent-filter>` inside the `<activity>` tag:

```xml
<intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data
        android:scheme="https"
        android:host="dummyjson.com"
        android:pathPrefix="/products" />
</intent-filter>
```

This configuration allows your app to respond to URLs like `https://dummyjson.com/products/14`.

### Asset Links File for Release

For production builds, create an `assetlinks.json` file to verify your app’s ownership of the domain. Place it in the `.well-known` directory on your server. Example:

```json
[{
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
        "namespace": "android_app",
        "package_name": "<YOUR_PACKAGE_NAME>",
        "sha256_cert_fingerprints": [
            "<YOUR_SHA256_CERT_FINGERPRINT>"
        ]
    }
}]
```

Replace `<YOUR_PACKAGE_NAME>` with your app’s package name and `<YOUR_SHA256_CERT_FINGERPRINT>` with your app’s SHA-256 certificate fingerprint.

To obtain the SHA-256 fingerprint, navigate to the Android directory in your Flutter project by running the following command in your terminal:

```bash
cd android
```

Then, execute the following command:

```bash
gradlew signingReport
```

This will generate all the SHA certificates, including the SHA-256 fingerprint required for the `assetlinks.json` file.

For testing, you can skip this step in debug mode.

## Testing Deep Links

To test deep linking in debug mode, use the following ADB command:

```bash
adb shell am start -W -a android.intent.action.VIEW -d "<your-url-here>" <your-package-name>
```

This opens the app and navigates to the specified product page.

## Conclusion

Deep linking enhances user experience by enabling seamless navigation to specific content within your app. Using `go_router` and the setup described above, you can implement deep linking efficiently in your Flutter projects.

Happy Linking! 🚀

## References

- [Flutter Deep Linking Documentation](https://docs.flutter.dev/ui/navigation/deep-linking)
- [go_router Package](https://pub.dev/packages/go_router)
