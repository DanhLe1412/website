---
title: Pass arguments to a named route
description: How to pass arguments to a named route.
prev:
  title: Navigate with named routes
  path: /docs/cookbook/navigation/named-routes
next:
  title: Return data from a screen
  path: /docs/cookbook/navigation/returning-data
js:
  - defer: true
    url: https://dartpad.dev/inject_embed.dart.js
---

[`Navigator`][] cung cấp khả năng chuyển đổi đến named route
từ bất kì phần nào trong ứng dụng bằng cách sử dụng đặc điểm nhận dạng chung  .
Trong nhiều trường hợp, bạn có thể chuyển dổi named route. Ví dụ, bạn muốn chuyển đến  `/user` route và
chuyển thông tin người dùng đến route đó.

Bạn có thể đạt được mục đích bằng `arguments` parameter của phương thức
[`Navigator.pushNamed()`][]. Trích xuất arguments bằng phương thức
[`ModalRoute.of()`][] hoặc [`onGenerateRoute()`][]
function được hỗ trợ bên trong để  cung cấp cho [`MaterialApp`][] hoặc [`CupertinoApp`][].

Bài viết này trình bày cách truyền arguments tới  named
route và đọc arguments bằng `ModalRoute.of()`
và `onGenerateRoute()` thông qua các bước sau:

  1. Xác định arguments bạn cần truyền.
  2. Tạo widget để trích xuất arguments.
  3. Đăng kí widget trong bảng `routes`.
  4. Chuyển động sang widget.

## 1. Xác định arguments bạn cần truyền

Đầu tiên, xác định arguments bạn cần truyền tới một route mới.
Trong ví dụ này, ta truyền hai mảnh của data:
`title` của màn hình và `message`.

Để truyền hai mảnh của data, tạo một class để chứa thông tin.

<!-- skip -->
```dart
// You can pass any object to the arguments parameter.
// In this example, create a class that contains a customizable
// title and message.
class ScreenArguments {
  final String title;
  final String message;

  ScreenArguments(this.title, this.message);
}
```

## 2. Tạo widget để trích xuất arguments

Tiếp theo, tạo widget để trích xuất và hiển thị
`title` và `message` từ `ScreenArguments`.
Để truy cập `ScreenArguments`,
dùng phương thức [`ModalRoute.of()`][].
Phương thức này sẽ return route hiện tại với arguments.

<!-- skip -->
```dart
// A widget that extracts the necessary arguments from the ModalRoute.
class ExtractArgumentsScreen extends StatelessWidget {
  static const routeName = '/extractArguments';

  @override
  Widget build(BuildContext context) {
    // Extract the arguments from the current ModalRoute settings and cast
    // them as ScreenArguments.
    final ScreenArguments args = ModalRoute.of(context).settings.arguments;

    return Scaffold(
      appBar: AppBar(
        title: Text(args.title),
      ),
      body: Center(
        child: Text(args.message),
      ),
    );
  }
}
```

## 3. Đăng kí widget trong bảng `routes`

Kế đến, thêm  `routes` để cung cáp cho `MaterialApp` widget. 
`routes` xác định widget nên được tạo dựa trên name của route.

<!-- skip -->
```dart
MaterialApp(
  routes: {
    ExtractArgumentsScreen.routeName: (context) => ExtractArgumentsScreen(),
  },
);
```


## 4. Chuyển động sang widget.

Cuối cùng, chuyển sang `ExtractArgumentsScreen`
khi người dùng nhấn button bằng [`Navigator.pushNamed()`][].
Cung cấp arguments cho route thông qua thuộc tính `arguments`. 
`ExtractArgumentsScreen` trích xuất `title` và `message` từ các
arguments.

<!-- skip -->
```dart
// A button that navigates to a named route. The named route
// extracts the arguments by itself.
RaisedButton(
  child: Text("Navigate to screen that extracts arguments"),
  onPressed: () {
    // When the user taps the button, navigate to a named route
    // and provide the arguments as an optional parameter.
    Navigator.pushNamed(
      context,
      ExtractArgumentsScreen.routeName,
      arguments: ScreenArguments(
        'Extract Arguments Screen',
        'This message is extracted in the build method.',
      ),
    );
  },
),
```

## Trích xuấ arguments bằng `onGenerateRoute`

Thay vì trích xuất arguments trực tiếp bên trong widget, bạn có thể trích xuất arguments bên trong [`onGenerateRoute()`][]
function và truyền nó vào widget.

`onGenerateRoute()` function tạo route chính xác dựa trên
`RouteSettings` đã cho.

<!-- skip -->
```dart
MaterialApp(
  // Provide a function to handle named routes. Use this function to
  // identify the named route being pushed, and create the correct
  // screen.
  onGenerateRoute: (settings) {
    // If you push the PassArguments route
    if (settings.name == PassArgumentsScreen.routeName) {
      // Cast the arguments to the correct type: ScreenArguments.
      final ScreenArguments args = settings.arguments;

      // Then, extract the required data from the arguments and
      // pass the data to the correct screen.
      return MaterialPageRoute(
        builder: (context) {
          return PassArgumentsScreen(
            title: args.title,
            message: args.message,
          );
        },
      );
    }
  },
);
```

## Interactive example

```run-dartpad:theme-light:mode-flutter:run-true:width-100%:height-600px:split-60:ga_id-interactive_example
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        // Provide a function to handle named routes. Use this function to
        // identify the named route being pushed, and create the correct
        // Screen.
        onGenerateRoute: (settings) {
          // If you push the PassArguments route
          if (settings.name == PassArgumentsScreen.routeName) {
            // Cast the arguments to the correct type: ScreenArguments.
            final ScreenArguments args = settings.arguments;

            // Then, extract the required data from the arguments and
            // pass the data to the correct screen.
            return MaterialPageRoute(
              builder: (context) {
                return PassArgumentsScreen(
                  title: args.title,
                  message: args.message,
                );
              },
            );
          }
          // The code only supports PassArgumentsScreen.routeName right now.
          // Other values need to be implemented if we add them. The assertion
          // here will help remind us of that higher up in the call stack, since
          // this assertion would otherwise fire somewhere in the framework.
          assert(false, 'Need to implement ${settings.name}');
          return null;
        },
        title: 'Navigation with Arguments',
        home: HomeScreen(),
        routes: {
          ExtractArgumentsScreen.routeName: (context) =>
              ExtractArgumentsScreen(),
        });
  }
}

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Home Screen'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            // A button that navigates to a named route that. The named route
            // extracts the arguments by itself.
            RaisedButton(
              child: Text("Navigate to screen that extracts arguments"),
              onPressed: () {
                // When the user taps the button, navigate to a named route
                // and provide the arguments as an optional parameter.
                Navigator.pushNamed(
                  context,
                  ExtractArgumentsScreen.routeName,
                  arguments: ScreenArguments(
                    'Extract Arguments Screen',
                    'This message is extracted in the build method.',
                  ),
                );
              },
            ),
            // A button that navigates to a named route. For this route, extract
            // the arguments in the onGenerateRoute function and pass them
            // to the screen.
            RaisedButton(
              child: Text("Navigate to a named that accepts arguments"),
              onPressed: () {
                // When the user taps the button, navigate to a named route
                // and provide the arguments as an optional parameter.
                Navigator.pushNamed(
                  context,
                  PassArgumentsScreen.routeName,
                  arguments: ScreenArguments(
                    'Accept Arguments Screen',
                    'This message is extracted in the onGenerateRoute function.',
                  ),
                );
              },
            ),
          ],
        ),
      ),
    );
  }
}

// A Widget that extracts the necessary arguments from the ModalRoute.
class ExtractArgumentsScreen extends StatelessWidget {
  static const routeName = '/extractArguments';

  @override
  Widget build(BuildContext context) {
    // Extract the arguments from the current ModalRoute settings and cast
    // them as ScreenArguments.
    final ScreenArguments args = ModalRoute.of(context).settings.arguments;

    return Scaffold(
      appBar: AppBar(
        title: Text(args.title),
      ),
      body: Center(
        child: Text(args.message),
      ),
    );
  }
}

// A Widget that accepts the necessary arguments via the constructor.
class PassArgumentsScreen extends StatelessWidget {
  static const routeName = '/passArguments';

  final String title;
  final String message;

  // This Widget accepts the arguments as constructor parameters. It does not
  // extract the arguments from the ModalRoute.
  //
  // The arguments are extracted by the onGenerateRoute function provided to the
  // MaterialApp widget.
  const PassArgumentsScreen({
    Key key,
    @required this.title,
    @required this.message,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(title),
      ),
      body: Center(
        child: Text(message),
      ),
    );
  }
}

// You can pass any object to the arguments parameter. In this example,
// create a class that contains both a customizable title and message.
class ScreenArguments {
  final String title;
  final String message;

  ScreenArguments(this.title, this.message);
}
```

<noscript>
  <img src="/images/cookbook/navigate-with-arguments.gif" alt="Demonstrates navigating to different routes with arguments" class="site-mobile-screenshot" />
</noscript>


[`CupertinoApp`]: {{site.api}}/flutter/cupertino/CupertinoApp-class.html
[`MaterialApp`]: {{site.api}}/flutter/material/MaterialApp-class.html
[`ModalRoute.of()`]: {{site.api}}/flutter/widgets/ModalRoute/of.html
[`Navigator`]: {{site.api}}/flutter/widgets/Navigator-class.html
[`Navigator.pushNamed()`]: {{site.api}}/flutter/widgets/Navigator/pushNamed.html
[`onGenerateRoute()`]: {{site.api}}/flutter/widgets/WidgetsApp/onGenerateRoute.html
