# Installation
Thêm package vào `pubspec.yaml`
```yaml
  flutter_adjust:
    git:
      url: https://tinhvv_vtn:ATBBG4h7EU5ukGEx7J5XjWfrD7Cu98123B67@bitbucket.org/innofyapp/flutter-adjust.git
```

# Usage

Khởi tạo Adjust
```dart
Future<void> initAdjust() {
    // Kiểm tra môi trường Adjust
  final adjustEnvironment = F.appFlavor == Flavor.dev
      ? AdjustEnvironment.sandbox
      : AdjustEnvironment.production;
  return AdjustUtil.instance.initialize(
    environment: adjustEnvironment,
    // Token app của Adjust
    appToken: AdjustToken(
      androidToken: 'androidToken',
      iosToken: 'iosToken',
    ),
    // Cấu hình cho ad
    adOptions: AdOptions(
      androidAdOptions: AndroidAdOptions(
        // Token event để log các sự kiện impression của ad lên Adjust
        impressionToken: 'androidImpressionToken',
        // Callback khi ad kiểm tra xem có phải là full ad không
        fullAdCallback: (isFullAd) {
          print('isFullAd: $isFullAd');
        },
      ),
      iosAdOptions: IOSAdOptions(
        impressionToken: 'iosImpressionToken',
        fullAdCallback: (isFullAd) {
          print('isFullAd: $isFullAd');
        },
      ),
    ),
    iapOptions: IapOptions(
      androidOptions: AndroidIapOptions(
        // Các event token tương ứng với các product trong app
        productRevenueTokens: {
          'com.example.weekly': 'androidWeeklyIapToken',
          'com.example.monthly': 'androidMonthlyIapToken',
        },
        // Token event để log tổng doanh thu iap của app
        totalRevenueToken: 'androidTotalIapToken',
      ),
      iosOptions: IOSIapOptions(
        productRevenueTokens: {
          'com.example.weekly': 'iosWeeklyIapToken',
          'com.example.monthly': 'iosMonthlyIapToken',
        },
        totalRevenueToken: 'iosTotalIapToken',
      ),
    ),
  );
}
```

#### 1. Log revenue của ad 
Có 2 hàm để log revenue của ad:

- `trackAdRevenue`: sử dụng để log revenue lên Adjust.

- `trackImpressionEvent`: sử dụng để log revenue lên impression event, event token là `impressionToken` được khai báo khi khởi tạo Adjust

Ví dụ khi sử dụng với package `flutter_ads`:

```dart
await EasyAds.instance.initialize(
  ...
);
EasyAds.instance.events.listen((event) {
  if (event.status.isPaid &&
      event.ad != null &&
      event.valueMicros != null &&
      event.currencyCode != null) {
    AdjustUtil.instance.trackAdRevenue(
      value: event.valueMicros! / 1000000,
      currencyCode: event.currencyCode!,
    );
    AdjustUtil.instance.trackImpressionEvent(
      value: event.valueMicros! / 1000000,
      currencyCode: event.currencyCode!,
    );
  }
});
```
#### 2. Log iap revenue
Log khi user mua thành công

Ví dụ khi sử dụng cùng package `flutter_iap`
```dart
@override
Future<void> handlePurchaseSuccess(PurchaseDetails purchaseDetails) async {
  final product = state.products.firstWhere(
    (e) => e.id == purchaseDetails.productID,
  );
  // Log revenue của các product theo event token của product đó
  // (được khai báo ở productRevenueTokens khi khởi tạo AdjustUtil)
  AdjustUtil.instance.trackSubscriptionRevenue(
    productDetails: product.productDetails,
    purchaseDetails: purchaseDetails,
  );

  // Log revenue của các product lên cùng 1 event token
  // (được khai báo ở totalRevenueToken khi khởi tạo AdjustUtil)
  AdjustUtil.instance.trackTotalIapRevenue(
    productDetails: product.productDetails,
    purchaseDetails: purchaseDetails,
  );
  ...
  // Handle purchased event
}
```

#### 3. Custom event
Sử dụng hàm `trackEvent` để log các custom event lên Adjust

```dart
AdjustEvent myAdjustEvent = AdjustEvent('eventToken');
//...
AdjustUtil.instance.trackEvent(myAdjustEvent);
```
