# Dart's Extension Methods: Demystified

## Introduction

As a Flutter Developer, you must have used packages/libraries and wanted to add some additional functionality to them based on your use cases. But you cannot just add methods to that class as the original class is in someone else's library.

A typical way of extending the functionality would be to create a Utility class providing static methods for your custom use cases:
```dart
class StringUtil {
  static bool isValidEmail(String email) {
    return RegExp(r"^[a-zA-Z0-9.a-zA-Z0-9.!#$%&'*+-/=?^_`{|}~]+@[a-zA-Z0-9]+\.[a-zA-Z]+").hasMatch(email);
  }
}

// Usage
void main() {
  print(StringUtil.isValidEmail('name@company.com'));  // Output: true
}
```

This is perfectly fine, but it does not fit well with the Object-Oriented programming paradigms as we are used to performing an operation on the actual instance of an object like:

```dart
String email = 'name@company.com';
email.toLowerCase();
email.isEmpty;
```

We could also create a wrapper class with the original class inside it. But it seems wasteful to wrap an object with another object just because we want to extend the original class with some methods.

So, what can help us here?

Yes, you guessed right, **Extensions**!

---

## Enable Extension Methods 
To enable extension methods, you need to go to your **pubspec.yaml** file and ensure the SDK version is set to 2.6.0 or greater:

```dart
environment:
  sdk: ">=2.6.0 <3.0.0"
```

Make sure to run `flutter pub get` after updating the **pubspec.yaml**.

---

## Implementing Extension Methods
Extensions can define not just methods, but also other members such as getters, setters, and operators. Also, extensions have names, which can be helpful if an API conflict arises. 

You define an extension method by using the following syntax:

```dart
extension <extension name> on <type> {
  (<member definition>)*
}
```

For example, here’s how you might implement an extension on the `String` class:

```dart
extension NumParse on String {
  int parseInt() {
    return int.parse(this);
  }

  double parseDouble() {
    return double.parse(this);
  }
}

// Usage
void main() {
  var num1 = '50'.parseInt();
  var num2 = '101.01'.parseDouble();

  print(num1.runtimeType);  // Output: int
  print(num2.runtimeType);  // Output: double
}
```

- To extend with getters, you can do something like:

```dart
extension StringValidation on String {
  bool get isValidEmail {
    return RegExp(r"^[a-zA-Z0-9.a-zA-Z0-9.!#$%&'*+-/=?^_`{|}~]+@[a-zA-Z0-9]+\.[a-zA-Z]+").hasMatch(this);
  }
}

// Usage
void main() {
     print('name@company.com'.isValidEmail);  // Output: true
}
```

- To extend with operators:

```dart
extension StringOps on String {
  String operator &(String prefix) => "$prefix $this";
}

// Usage
void main() {
     print('google.com'&'https://');  // Output: https://google.com
}
```

### Implementing Generic Extensions
Extensions can have generic type parameters. For example, here’s some code that extends the built-in `List<T>` type with a getter, an operator, and a method:

```dart
extension ListX<T> on List<T> {
  int get doubleLength => length * 2;
  List<T> operator -() => reversed.toList();
  List<List<T>> split(int at) => [sublist(0, at), sublist(at)];
}
```

- You can also extend only lists of some type. For example, This Extension will only show up on lists of integers:

```dart
extension PowerList<int> on List<int> {
  int get sum => fold(0, (a, b) => a + b);
}

// Usage
void main() {
  print([1, 2, 3].sum());  // Output: 6
}
```

- You can also create an Extension that operates on any type `T` that extends `num`:

```dart
extension IterableNumX<T extends num> on Iterable<T> {
  T sum() {
    // 1. initialize sum
    var sum = (T == int ? 0 : 0.0) as T;
    // 2. calculate sum
    for (var current in this) {
      if (current != null) { // only add non-null values
        sum += current;
      }
    }
    return sum;
  }
}
```
> Credit: I borrowed this example from [this blog post](https://codewithandrea.com/videos/dart-extensions-full-introduction/).

---

## How To Use Extension Methods
Like all Dart code, you usually put extension methods in their independent dart files which can be imported as libraries. To use extension methods in your code, just import the library it’s in, and use it like an ordinary method:

```dart
// Import a library that contains an extension on String.
import 'string_ext.dart';
// ···
print('50'.padLeft(5));  // String method
print('50'.parseInt());  // Extension method
```

### Using With Static and Dynamic types
You can’t invoke extension methods on variables of type `dynamic`. For example, the following code results in a runtime exception:

```dart
dynamic num = '50';
print(num.parseInt());  // Throws Runtime exception: NoSuchMethodError
```

On the other hand, extension methods do work with Dart’s type inference. The following code is fine because the variable `num` is inferred to have type `String`:

```dart
var num = '50';
print(num.parseInt());  // Output: 50
```

> The reason why `dynamic` doesn’t work is that extension methods are resolved against the static type of the receiver. Because extension methods are resolved statically, they’re as fast as calling a static function.

### Resolving API Conflicts
There are chances that extension methods with the same name are created in different Dart files and you may face API conflicts. For example:

```dart
// File: string_validation.dart
extension StringValidation on String {
  bool get isValidEmail {
    return RegExp(r"^[a-zA-Z0-9.a-zA-Z0-9.!#$%&'*+-/=?^_`{|}~]+@[a-zA-Z0-9]+\.[a-zA-Z]+").hasMatch(this);
  }
}
--------------------------------------------------------
// File: string_ext.dart
extension StringX on String {
  bool get isValidEmail {
    return RegExp(r"^[a-zA-Z0-9.a-zA-Z0-9.!#$%&'*+-/=?^_`{|}~]+@[a-zA-Z0-9]+\.[a-zA-Z]+").hasMatch(this);
  }
}
-------------------------------------------------------
main() {
  print('name@company.com'.isValidName);
}
```
This will produce a compile-time error and your code will not run.

To resolve this conflict, we have 2 different methods:
- Use `show` or `hide` to limit the exposed API:

```dart
// Defines the String extension method isValidEmail().
import 'string_validation.dart';

// Also defines isValidEmail(), but hiding StringX
// hides that extension method.
import 'string_ext.dart' hide StringX;

// ···
// Uses the isValidEmail() defined in 'string_validation.dart'.
print('name@company.com'.isValidName);
```

- Apply the extension explicitly:

```dart
// Both libraries define extensions on String that contain isValidEmail(),
// and the extensions have different names.
import 'string_validation.dart';  // Contains StringValidation extension
import 'string_ext.dart';  // Contains StringX extension

// ···
// print('name@company.com'.isEmailValid);  // Doesn't work.
print(StringValidation('name@company.com').isValidEmail);
print(StringX('name@company.com').isValidEmail);
```

---

## Flutter Advantage
You might be wondering if we can create extension methods on any class, then Flutter Widgets should also support it as they're also classes.

Well, you guessed right.

We can create extensions on existing Flutter Widgets, and reduce boilerplate code for common layout tasks. Suppose you want different alignment of children in the column. Normally you would do something like this:
```dart
Column(
  children: <Widget>[
    Align(
      alignment: AlignmentDirectional.centerStart,
      child: SomeWidget(),
    ),
    Align(
      alignment: AlignmentDirectional.centerEnd,
      child: SomeAnotherWidget(),
    ),
  ],
)
```

This can be made more elegant using extensions like this:
```dart
extension AlignUtil on Widget {
  alignAtStart() {
    return Align(
      alignment: AlignmentDirectional.centerStart,
      child: this,
    );
  }

  alignAtEnd() {
    return Align(
      alignment: AlignmentDirectional.centerEnd,
      child: this,
    );
  }
}
------------------------------------------------------------
Column(
  children: <Widget>[
    SomeWidget().alignAtStart(),
    SomeAnotherWidget().alignAtEnd()
  ],
)
```
> Credit: I borrowed this example from [this blog post](https://medium.com/aubergine-solutions/easily-understand-darts-extension-methods-for-flutter-2b3d3d3c698f).

---

## More to Explore
Do check out these popular packages that provide useful extensions:

- **Dartx** is a package which adds a lot of useful extension methods to types in the Dart Language.
%[https://pub.dev/packages/dartx]
- **Styled Widgets** is another package that started as an experiment, showing how widget styling APIs could be improved, using a syntax that is similar to the SwiftUI framework by Apple. 
%[https://pub.dev/packages/styled_widget]
- **Time** is yet another package which adds extensions to the Flutter's DateTime library.
%[https://pub.dev/packages/time]

---

## Conclusion
Extensions are a great way to add functionality to existing classes & libraries. It can also help in reducing boilerplate code to a great extent. But Just because we can use extensions, it doesn't mean that we should use them **everywhere**. 

> Remember: with power comes great responsibilities.

---

## References
%[https://dart.dev/guides/language/extension-methods#api-conflicts]
%[https://medium.com/aubergine-solutions/easily-understand-darts-extension-methods-for-flutter-2b3d3d3c698f]
%[https://codewithandrea.com/videos/dart-extensions-full-introduction/]

---

That's all folks! Do let me know what you liked and what can be improved in the comments below! Also, feel free to drop me a line if you don't understand something. ✌

Thanks for reading!