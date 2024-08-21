# Branch: version/1.2.0

- Bỏ hàm `setOnSplashScreen` trong AppLifecycleReactor, sử dụng hàm `setShouldShow` 
Ví dụ 
```dart
EasyAds.instance.appLifecycleReactor.setOnSplashScreen(true) 
// được viết lại thành
EasyAds.instance.appLifecycleReactor.setShouldShow(false) 
```

- Đổi tên hàm 
```dart
EasyAds.instance.initAdmob(
  ...
) 
// được viết lại thành
EasyAds.instance.initAppOpenAd(
  ...
) 
```

- Bỏ hàm `showSplashInterstitialAd`, chuyển sang sử dụng `showInterstitialAd` với 2 tham số `forceShow: true` và `showLoading: false`

```dart
EasyAds.instance.showInterstitialAd(
      context,
      adId: '...',
      forceShow: true, // bỏ qua qua điều kiện sau x giây (default 20s) hiển thị inter 1 lần
      showLoading: false, // không hiển thị loading
);

```

- Bây giờ có thể chuyển sang màn khác khi vào sự kiện `onShowed` của inter ad, không cần đợi `adDismiss`

- Xóa bỏ **Adjust** trong flutter_ads
