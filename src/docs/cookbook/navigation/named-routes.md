---
title: Navigate with named routes
description: How to implement named routes for navigating between screens.
prev:
  title: Navigate to a new screen and back
  path: /docs/cookbook/navigation/navigation-basics
next:
  title: Pass arguments to a named route
  path: /docs/cookbook/navigation/navigate-with-arguments
js:
  - defer: true
    url: https://dartpad.dev/inject_embed.dart.js
---

Trong bài viết [Navigate to a new screen and back][],
bạn đã học cách chuyển động sang màn hình mới thông qua tạo route và sử dụng  [`Navigator`][].

Tuy nhiên, bạn muốn chuyển động cùng màn hình ở nhiều phần của app, bạn phải code nhiều lần, dẫn đến trùng lập về code. Cách giải quyết là ta phải xác định _named route_, và dùng name của route này để chuyển động.

Để làm việc với name của routes,
dùng [`Navigator.pushNamed()`][] function.
Ví dụ này phỏng theo chức năng từ bài viết gốc,
các cách làm việc với name routes:

  1. Tạo hai màn hình.
  2. Xác định routes.
  3. Chuyển động sang màn hình thứ hai bằng `Navigator.pushNamed()`.
  4. Trở về màn hình thứ nhất thông qua `Navigator.pop()`.

## 1. Tạo hai màn hình

Đầu tiên, ta tạo hai màn hình để làm việc. Màn hình thứ nhất chứa 
button để chuyển sang màn hình thứ hai. Màn hình thứ hai chứa
button để chuyển về màn hình thứ nhất.

```dart
class FirstScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('First Screen'),
      ),
      body: Center(
        child: RaisedButton(
          child: Text('Launch screen'),
          onPressed: () {
            // Navigate to the second screen when tapped.
          },
        ),
      ),
    );
  }
}

class SecondScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Second Screen"),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            // Navigate back to first screen when tapped.
          },
          child: Text('Go back!'),
        ),
      ),
    );
  }
}
```

## 2. Xác định routes

Tiếp theo, xác định routes bằng cách cung cấp thuộc tính bổ sung cho hàm tạo [`MaterialApp`][] là:  `initialRoute`
và `routes`.

Thuộc tính `initialRoute` xác định route mà app sẽ bắt đầu.
Thuộc tính `routes` xác định các name routes và các widget khi có chuyển động đến routes đó.

<!-- skip -->
```dart
MaterialApp(
  // Start the app with the "/" named route. In this case, the app starts
  // on the FirstScreen widget.
  initialRoute: '/',
  routes: {
    // When navigating to the "/" route, build the FirstScreen widget.
    '/': (context) => FirstScreen(),
    // When navigating to the "/second" route, build the SecondScreen widget.
    '/second': (context) => SecondScreen(),
  },
);
```

{{site.alert.warning}}
  Khi dùng `initialRoute`, **không được** define thuộc tính của `home`.
{{site.alert.end}}

## 3. Chuyển động sang màn hình thứ hai

Với các widgets và routes tại chỗ, hãy kích hoạt điều hướng bằng cách dùng phương thức
[`Navigator.pushNamed()`][].
Điều này sẽ cho Flutter biết để xây dựng widget và defined `routes` trong danh sách đã có và chạy trong màn hình.

Trong phương thức `build()` của `FirstScreen` widget, cập nhật `onPressed()`
callback:

<!-- skip -->
```dart
// Within the `FirstScreen` widget
onPressed: () {
  // Navigate to the second screen using a named route.
  Navigator.pushNamed(context, '/second');
}
```

## 4. Trở về màn hình đầu tiên

Để trở về màn hình đầu tiên, sử dụng
[`Navigator.pop()`][] function.

<!-- skip -->
```dart
// Within the SecondScreen widget
onPressed: () {
  // Navigate back to the first screen by popping the current route
  // off the stack.
  Navigator.pop(context);
}
```

## Interactive example

```run-dartpad:theme-light:mode-flutter:run-true:width-100%:height-600px:split-60:ga_id-interactive_example
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Named Routes Demo',
    // Start the app with the "/" named route. In this case, the app starts
    // on the FirstScreen widget.
    initialRoute: '/',
    routes: {
      // When navigating to the "/" route, build the FirstScreen widget.
      '/': (context) => FirstScreen(),
      // When navigating to the "/second" route, build the SecondScreen widget.
      '/second': (context) => SecondScreen(),
    },
  ));
}

class FirstScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('First Screen'),
      ),
      body: Center(
        child: RaisedButton(
          child: Text('Launch screen'),
          onPressed: () {
            // Navigate to the second screen using a named route.
            Navigator.pushNamed(context, '/second');
          },
        ),
      ),
    );
  }
}

class SecondScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Second Screen"),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            // Navigate back to the first screen by popping the current route
            // off the stack.
            Navigator.pop(context);
          },
          child: Text('Go back!'),
        ),
      ),
    );
  }
}
```

<noscript>
  <img src="/images/cookbook/navigation-basics.gif" alt="Navigation Basics Demo" class="site-mobile-screenshot" />
</noscript>


[`MaterialApp`]: {{site.api}}/flutter/material/MaterialApp-class.html
[Navigate to a new screen and back]: /docs/cookbook/navigation/navigation-basics
[`Navigator`]: {{site.api}}/flutter/widgets/Navigator-class.html
[`Navigator.pop()`]: {{site.api}}/flutter/widgets/Navigator/pop.html
[`Navigator.pushNamed()`]: {{site.api}}/flutter/widgets/Navigator/pushNamed.html
