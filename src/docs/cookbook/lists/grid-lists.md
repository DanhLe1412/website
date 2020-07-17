---
title: Create a grid list
description: How to implement a grid list.
prev:
  title: Create a horizontal list
  path: /docs/cookbook/lists/horizontal-list
next:
  title: Create lists with different types of items
  path: /docs/cookbook/lists/mixed-list
js:
  - defer: true
    url: https://dartpad.dev/inject_embed.dart.js
---

Trong vài trường hợp, bạn muốn hiển thị items dưới dạng lứoi hơn 
là hiển thị dưới dạng danh sách liên tiếp nhau.
Với nhiệm vụ này, ta sẽ dùng [`GridView`][] widget.

Cách đơn giản để bắt đầu dùng hiển thị dạng lưới là dùng
[`GridView.count()`][] constructor,
nó sẽ cho phép bạn tùy chỉnh số dòng và cột tùy thích.

Để hình dung cách `GridView` hoạt động,
khởi tạo danh sách danh sách 100 widgets hiển thị trong một danh sách.

<!-- skip -->
```dart
GridView.count(
  // Create a grid with 2 columns. If you change the scrollDirection to
  // horizontal, this produces 2 rows.
  crossAxisCount: 2,
  // Generate 100 widgets that display their index in the List.
  children: List.generate(100, (index) {
    return Center(
      child: Text(
        'Item $index',
        style: Theme.of(context).textTheme.headline,
      ),
    );
  }),
);
```

## Interactive example

```run-dartpad:theme-light:mode-flutter:run-true:width-100%:height-600px:split-60:ga_id-interactive_example
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final title = 'Grid List';

    return MaterialApp(
      title: title,
      home: Scaffold(
        appBar: AppBar(
          title: Text(title),
        ),
        body: GridView.count(
          // Create a grid with 2 columns. If you change the scrollDirection to
          // horizontal, this produces 2 rows.
          crossAxisCount: 2,
          // Generate 100 widgets that display their index in the List.
          children: List.generate(100, (index) {
            return Center(
              child: Text(
                'Item $index',
                style: Theme.of(context).textTheme.headline,
              ),
            );
          }),
        ),
      ),
    );
  }
}
```

<noscript>
  <img src="/images/cookbook/grid-list.gif" alt="Grid List Demo" class="site-mobile-screenshot" />
</noscript>

[`GridView`]: {{site.api}}/flutter/widgets/GridView-class.html
[`GridView.count()`]: {{site.api}}/flutter/widgets/GridView/GridView.count.html
