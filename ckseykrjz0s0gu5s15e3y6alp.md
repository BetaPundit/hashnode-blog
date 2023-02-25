# The Ultimate Flutter Layout Guide

Have you ever been stuck on any of these errors while building a Flutter app?

```
> A RenderFlex overflowed…
> RenderBox was not laid out
> Viewport was given unbounded height
> An InputDecorator…cannot have an unbounded width
> Incorrect use of ParentData widget
``` 


If yes, then this blog post is for **you**!

In this blog post, I'll discuss and share some common Flutter layout scenarios and best practices. I'll try to focus more on code snippets and less on widget details. For widget details, I'll share the relevant link for the same.

---

## Contents
- [Prerequisites](#prerequisites)
- Single-child layout widgets  
  - [Align](#align)
  - [AspectRatio](#aspectratio)
  - [Center](#center)
  - [ConstrainedBox](#constrainedBox)
  - [Container](#container)
  - [Expanded](#expanded)
  - [FittedBox](#fittedbox)
  - [FractionallySizedBox](#fractionallysizedbox)
  - [SizedBox](#sizedbox)
- Multi-child layout widgets
  - [Row and Column](#row-and-column)
  - [Stack](#stack)
  - [LayoutBuilder](#layoutbuilder)
- [References](#references)

---

## Prerequisites
- Basic knowledge of Flutter widgets
- Desire to learn something new

---

## Single-child layout widgets

### Align
A widget that aligns its `child` within itself and optionally sizes itself based on the `child`'s size.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1625632361409/Zq9bGq8xA.png)

```
Center(
  child: Container(
    height: 120.0,
    width: 120.0,
    color: Colors.blue[50],
    child: const Align(
      alignment: Alignment.topRight,
      child: FlutterLogo(
        size: 60,
      ),
    ),
  ),
)
```

If you want to align your widget by a ratio from the parent's centre:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1625641654669/4WXXU707N.png)

```
Center(
  child: Container(
    height: 120.0,
    width: 120.0,
    color: Colors.blue[50],
    child: const Align(
      alignment: Alignment(0.2, 0.6),
      child: FlutterLogo(
        size: 60,
      ),
    ),
  ),
)
```

Read more about Align [here](https://api.flutter.dev/flutter/widgets/Align-class.html).

---

### AspectRatio
A widget that attempts to size the `child` to a specific aspect ratio.  
**Note: aspectRatio = width / height**

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1625634123445/aE2onzGCV.png)

```
Container(
  color: Colors.blue[100],
  alignment: Alignment.center,
  width: double.infinity,
  height: 100.0,
  child: AspectRatio(
    aspectRatio: 16 / 9,
      child: Container(
        color: Colors.green,
    ),
  ),
)
```

#### Best Practices:
1. Never put `AspectRatio()` inside `Expanded()` or similar widgets that force their child/children to stretch or take up the whole space given by the parent.
2. If needed, put the `AspectRatio()` widget inside `Align()` inside `Expanded()`.  
Example:

```
Expanded(
  child: Align(
    AspectRatio(
      aspectRatio: 16 / 9,
        child: Container(),
    ),
  ),
)
```

Read more about AspectRatio [here](https://api.flutter.dev/flutter/widgets/AspectRatio-class.html).

---

### Center
A widget that centers its `child` within itself. By default, the widget will match its `child`'s size.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1625639198984/BIUi19DjT.png)

```
Center(
  child: FlutterLogo(
    size: 60,
  ),
)
```

Read more about Center [here](https://api.flutter.dev/flutter/widgets/Center-class.html).

---

### ConstrainedBox
By default, most of the widgets will use as little space as possible.  
For Examlpe:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1625643876874/sBHWk0Q9y.png)

```
Card(
  color: Colors.blue[200],
  child: Text(
    'Widget without constraints',
  ),
)
```

`ConstrainedBox` allows its child widget to use the remaining space as desired.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1625644076727/Exg5DwIjx.png)

```
ConstrainedBox(
  constraints: BoxConstraints.expand(),
  child: Card(
    color: Colors.blue[200],
    child: Text(
      'Widget inside ConstrainedBox',
    ),
  ),
)
```

**Note:** The same behavior can be obtained using the `SizedBox.expand()` widget.

Read more about ConstrainedBox [here](https://api.flutter.dev/flutter/widgets/ConstrainedBox-class.html).

---

### Container
**Container** is one of the most frequently used Widgets!  
Container combines a number of other widgets each with their own layout behavior, thus Container's layout behavior is somewhat complicated. 

Before we move forward, it's important to understand the layout behavior of the Container.

#### Layout Behavior
- If the Container has no `child`, no `height`, no `width`, no constraints, and the parent provides unbounded constraints, then Container tries to size as **small** as possible.
- If the Container has no `child` and no `alignment`, but a `height`, `width`, or constraints are provided, then the Container tries to be as **small** as possible given the combination of those constraints and the parent's constraints.
- If the Container has no `child`, no `height`, no `width`, no constraints, and no `alignment`, but the parent provides bounded constraints, then Container **expands** to fit the constraints provided by the parent.
- If the Container has an `alignment`, and the parent provides unbounded constraints, then the Container tries to size itself to **match** the `child`.
- If the Container has an `alignment`, and the parent provides bounded constraints, then the Container tries to **expand** to fit the parent and then positions the `child` within itself as per the alignment.
- Otherwise, if the Container has a `child` but no `height`, no `width`, no constraints, and no `alignment`, the Container passes the constraints from the parent to the `child` and sizes itself to **match** the child.

I know that was quite much! But don't worry, you'll get it soon. Let's have a look at some samples below:

- When you don’t specify the `height` and the `width` of the Container, it will match its `child`’s size.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1626520322341/quKxub41I.png)

```
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      title: Text(widget.title),
    ),
    body: Container(
      color: Colors.yellow[200],
      child: Text(
        'Widget inside Container',
      ),
    ),
  );
}
```

- When you don’t specify the `height` and the `width` of the Container, but specify the `alignment`, it will match its parent’s size and align its `child` according to the specified `alignment` property.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1626520264818/s9B4ifawr.png)

```
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      title: Text(widget.title),
    ),
    body: Container(
      color: Colors.yellow[200],
      alignment: Alignment.center,
      child: Text(
        'Widget inside Container',
      ),
    ),
  );
}
```

- When you only specify the `height` of the Container, it will match its `child`’s `width`.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1626521994812/CVWsClHmn.png)

```
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      title: Text(widget.title),
    ),
    body: Container(
      color: Colors.yellow[200],
      height: 100,
      child: Text(
        'Widget inside Container',
      ),
    ),
  );
}
```

- When you only specify the `width` of the Container, it will match its `child`’s `height`.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1626522103336/AKl53ZFU6.png)

```
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      title: Text(widget.title),
    ),
    body: Container(
      color: Colors.yellow[200],
      width: 100,
      child: Text(
        'Widget inside Container',
      ),
    ),
  );
}
```

- When you only specify the `height` of the Container but also specify the `alignment`, it will match its parent’s `width` and align its `child` according to the specified `alignment` property.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1626522413853/S_WGlAQ6V.png)

```
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      title: Text(widget.title),
    ),
    body: Container(
      color: Colors.yellow[200],
      height: 100,
      alignment: Alignment.center,
      child: Text(
        'Widget inside Container',
      ),
    ),
  );
}
```

- When you only specify the `width` of the Container but also specify the `alignment`, it will match its parent’s `height` and align its `child` according to the specified `alignment` property.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1626522527498/7LiNWwHwQ.png)

```
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      title: Text(widget.title),
    ),
    body: Container(
      color: Colors.yellow[200],
      width: 100,
      alignment: Alignment.center,
      child: Text(
        'Widget inside Container',
      ),
    ),
  );
}
```

Read more about Container [here](https://api.flutter.dev/flutter/widgets/FittedBox-class.html).

---

### Expanded
A widget that expands a child of a `Row`, `Column`, or `Flex` so that the child fills the available space. It's great for distributing space between multiple items.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627322204846/Yu1APGlQN.png)

```
Column /*or Row*/ (
  children: <Widget>[
    Expanded(
      child: Card(
        color: Colors.teal,
        child: Center(child: Text('Flex: 1')),
      ),
      flex: 1,
    ),
    Expanded(
      child: Card(
        color: Colors.green,
        child: Center(child: Text('Flex: 2')),
      ),
      flex: 2,
    ),
    Expanded(
      child: Card(
        color: Colors.lightGreen,
        child: Center(child: Text('Flex: 3')),
      ),
      flex: 3,
    ),
  ],
),
```

Read more about Expanded [here](https://api.flutter.dev/flutter/widgets/Expanded-class.html).

---

### FittedBox
Scales and positions its child within itself according to `fit`. It's mainly used to fit images inside itself just like how you fit wallpapers on your desktop!

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1626524554953/VjXrjhjvd.png)

```
Container(
  height: 150,
  width: 300,
  color: Colors.yellow[200],
  child: FittedBox(
    clipBehavior: Clip.antiAlias,
    fit: BoxFit.cover,
    child: Image.network(
      'https://xyz.jpg',
    ),
  ),
),
```

Read more about FittedBox [here](https://api.flutter.dev/flutter/widgets/Container-class.html).

---

### FractionallySizedBox
A widget that sizes its `child` to a fraction of the total available space.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1626679341097/TQQ_pbmMn.png)

```
Center(
  child: FractionallySizedBox(
    widthFactor: 0.6,
    heightFactor: 0.1,
    child: Card(
      color: Colors.orange,
      child: Text('Some Widget'),
    ),
  ),
),
```

#### Best Practices:
1. Use `FractionallySizedBox()` with no `child` for fractional sized white space.
2. Wrap `FractionallySizedBox()` inside `Flexible()` widget so it plays well with `Row`/`Column`.

Read more about FractionallySizedBox [here](https://api.flutter.dev/flutter/widgets/FractionallySizedBox-class.html).

---

### SizedBox
It is one of the simplest yet useful Widgets. It enforces a specific size on its `child`.

#### Layout Behavior
- If given a `child`, this widget forces it to have a specific `width` and/or `height`.
- These values will be ignored if this widget's parent does not permit them. For example, this happens if the parent is the screen or another SizedBox.
- If either the `width` or `height` is null, SizedBox will try to size itself to match the `child`'s size in that dimension.
- If `height` or `width` is null or unspecified, it will be treated as zero.
- Use `SizedBox.expand()` to make the SizedBox match the size of its parent. It is equivalent to setting `width` and `height` to `double.infinity`.

Let's have a look at some samples below:

- SizedBox as fixed-size padding

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627320693411/vaswzpEdR.png)

```
Column(
  children: <Widget>[
    FlutterLogo(size: 50),
    const SizedBox(height: 100),
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
  ],
),
```

- If you want to match the size of the parent, use `SizedBox.expand()`

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627321041901/rdLYcDYAV.png)

```
SizedBox.expand(
  child: Card(
    color: Colors.orange[200],
    child: Text('Widget inside SizedBox'),
  ),
),
```

#### Best Practices
- When placing SizedBox as a `child` inside a parent Widget which forces its `child` to be the same size as itself (e.g. another SizedBox), then wrap the child SizedBox in a widget that does permit it to be any size up to the size of the parent, such as `Center` or `Align`.  
Example:

```
SizedBox(
  height: double.infinity,
  width: double.infinity,
  child: Align(
    child: SizedBox(
      height: 100,
      width: 100,
      child: Card(
        color: Colors.orange[200],
        child: Text('Widget inside SizedBox'),
      ),
    ),
  ),
)
```

Read more about SizedBox [here](https://api.flutter.dev/flutter/widgets/SizedBox-class.html).

---

## Multi-child layout widgets

### Row and Column
**Row**: Layout a list of child widgets in the horizontal direction.  
**Column**: Layout a list of child widgets in the vertical direction.

To align the child widgets inside Row/Column, we use `mainAxisAlignment` and `crossAxisAlignment`. Let's have a look at both of these parameters:

#### MainAxisAlignment

- `MainAxisAlignment.start`

![row_col_1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628354826434/wR-hdPz-z6.png)

```
Row /*or Column*/ (
  mainAxisAlignment: MainAxisAlignment.start,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
  ],
),
```
  
- `MainAxisAlignment.center`

![row_col_2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628355651623/7tNAhPy_sB.png)

```
Row /*or Column*/ (
  mainAxisAlignment: MainAxisAlignment.center,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
  ],
),
```

- `MainAxisAlignment.end`

![row_col_3.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628356293795/sxZOpkSWa.png)

```
Row /*or Column*/ (
  mainAxisAlignment: MainAxisAlignment.end,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
  ],
),
```

- `MainAxisAlignment.spaceBetween`

![row_col_4.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628356683784/qgeEy3nUh.png)

```
Row /*or Column*/ (
  mainAxisAlignment: MainAxisAlignment.spaceBetween,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
  ],
),
```

- `MainAxisAlignment.spaceEvenly`

![row_col_5.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628357043015/dUNBfQ-TV.png)

```
Row /*or Column*/ (
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
  ],
),
```

- `MainAxisAlignment.spaceAround`

![row_col_6.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628357205623/bOz2Vt0Ee.png)

```
Row /*or Column*/ (
  mainAxisAlignment: MainAxisAlignment.spaceAround,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
  ],
),
```

#### CrossAxisAlignment

- `CrossAxisAlignment.start`

![row_col_7.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628358455821/in9D8YT0W.png)

```
Row /*or Column*/ (
  crossAxisAlignment: CrossAxisAlignment.start,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 200),
    FlutterLogo(size: 50),
  ],
),
```

- `CrossAxisAlignment.center`

![row_col_8.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628358910254/Ms-8JfzKkV.png)

```
Row /*or Column*/ (
  crossAxisAlignment: CrossAxisAlignment.center,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 200),
    FlutterLogo(size: 50),
  ],
),
```

- `CrossAxisAlignment.end`

![row_col_9.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628359083911/aA0edoDDv.png)

```
Row /*or Column*/ (
  crossAxisAlignment: CrossAxisAlignment.end,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 200),
    FlutterLogo(size: 50),
  ],
),
```

- `CrossAxisAlignment.stretch`

![row_col_10.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628359245765/vUq5kmRDv.png)

```
Row /*or Column*/ (
  crossAxisAlignment: CrossAxisAlignment.stretch,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 200),
    FlutterLogo(size: 50),
  ],
),
```

- `CrossAxisAlignment.baseline`  
Places the children along the cross axis such that their baselines match.
Since baselines are always horizontal, this alignment is intended for `Row` Widget. If the main axis is vertical, then this value is treated like `CrossAxisAlignment.start`.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628360289312/WgANZEAJz.png)

```
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: <Widget>[
    Text(
      'Heading',
      style: Theme.of(context).textTheme.headline2,
    ),
    Text(
      'Body',
      style: Theme.of(context).textTheme.bodyText2,
    ),
  ],
),
```

Use `MainAxisSize` to size the Row/Column to either match their parent or fit their children.

#### MainAxisSize

- `MainAxisSize.max`

![row_col_1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628354826434/wR-hdPz-z6.png)

```
Row /*or Column*/ (
  mainAxisSize: MainAxisSize.max,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
  ],
),
```

- `MainAxisSize.min`

![row_col_11.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628406305811/0ZtL8ayxI.png)

```
Row /*or Column*/ (
  mainAxisSize: MainAxisSize.min,
  children: <Widget>[
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
    FlutterLogo(size: 50),
  ],
),
```

Read more about Row [here](https://api.flutter.dev/flutter/widgets/Row-class.html).  
Read more about Column [here](https://api.flutter.dev/flutter/widgets/Column-class.html).  

---

### Stack
A widget that positions its children on top of each other.  
Let's have a look at some of its interesting properties:

- By default, all the children are aligned to the top-left corner of a Stack.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628876988664/_fwm05aDe.png)

```
Stack(
  children: <Widget>[
    Container(
      width: 120,
      height: 120,
      color: Colors.green,
    ),
    Container(
      width: 100,
      height: 100,
      color: Colors.red,
    ),
    Container(
      width: 80,
      height: 80,
      color: Colors.blue,
    ),
  ],
),
```

- To align all the children, we can use the `alignment` property.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628877226725/S7y6QqQxC.png)

```
Stack(
  alignment: Alignment.center,
  children: <Widget>[
    Container(
      width: 120,
      height: 120,
      color: Colors.green,
    ),
    Container(
      width: 100,
      height: 100,
      color: Colors.red,
    ),
    Container(
      width: 80,
      height: 80,
      color: Colors.blue,
    ),
  ],
),
```

- But what if you want to position all the elements uniquely?
In that case, we can either wrap the individual child widgets with an `Align()` or `Positioned()` widget.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628877983649/mwktorG9J.png)

```
Stack(
  children: <Widget>[
    Align(
      alignment: Alignment.topLeft,
      child: Icon(
        Icons.menu,
      ),
    ),
    Align(
      alignment: Alignment.topRight,
      child: Icon(
        Icons.delete,
      ),
    ),
    Positioned(
      bottom: 0,
      right: 0,
      child: Icon(
        Icons.add_circle,
      ),
    ),
    Positioned(
      bottom: 0,
      left: 0,
      child: Icon(
        Icons.home,
      ),
    ),
  ],
),
```

- Sometimes, a child moves outside of a Stack's bounds. By default, it will be clipped.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628879594246/O44jNPb8hY.png)

To prevent clipping, use `clipBehavior: Clip.none`.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1628879629775/zCkUOpm81Y.png)

```
Stack(
  alignment: Alignment.center,
  clipBehavior: Clip.none,
  children: <Widget>[
    Container(
      width: 120,
      height: 120,
      color: Colors.black54,
    ),
    Positioned(
      bottom: -30,
      right: -30,
      child: Container(
        width: 80,
        height: 80,
        color: Colors.blue,
      ),
    ),
  ],
),
```

Read more about Stack here: [here](https://api.flutter.dev/flutter/widgets/Stack-class.html).

---

### LayoutBuilder
If you want to layout your widgets based on the parent's size, this is the widget you need. One use-case for `LayoutBuilder` could be building different layouts for different screen sizes.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1629136435896/XIot-Tz3p.png)

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1629136317704/YTN8cNrQW.png)

```
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(title: const Text('LayoutBuilder')),
    body: LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth > 600) {
          return _buildWideContainers(constraints.maxWidth);
        } else {
          return _buildNormalContainer();
        }
      },
    ),
  );
}

Widget _buildNormalContainer() {
  return Center(
    child: Container(
      height: double.infinity,
      width: double.infinity,
      color: Colors.teal,
    ),
  );
}

Widget _buildWideContainers(double width) {
  return Center(
    child: Row(
      mainAxisAlignment: MainAxisAlignment.spaceEvenly,
      children: <Widget>[
        Container(
          height: double.infinity,
          width: width / 2,
          color: Colors.teal,
        ),
        Container(
          height: double.infinity,
          width: width / 2,
          color: Colors.green,
        ),
      ],
    ),
  );
}
```

Read more about LayoutBuilder here: [here](https://api.flutter.dev/flutter/widgets/LayoutBuilder-class.html).

---

## References
- Official Flutter layout widgets page:
%[https://flutter.dev/docs/development/ui/widgets/layout#Multi-child%20layout%20widgets]

- A great article on constraints in FLutter, a must-read:
%[https://flutter.dev/docs/development/ui/layout/constraints]

- To understand existing layouts & diagnose layout issues, use Flutter Inspector:
%[https://flutter.dev/docs/development/tools/devtools/inspector]

---

That's all for now. This blog is a work in progress. I'll try and add more Widgets and samples in the coming days. Feel free to drop a line if you don't understand something.

Thanks for reading! 