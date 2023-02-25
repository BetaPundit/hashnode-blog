# Understanding null safety in Dart

Ever wondered what is all this fuss about null safety? In this article, I'll provide a brief introduction to null safety in Dart and share code examples for a better understanding.

---

## Introduction
Null safety helps you solve one of the most common errors in software development: **NullPointerException**.   

The main issue with the `NullPointerException` is that it can happen in almost any part of the app if not well handled. Checking for nulls every now and then throughout your app isn’t the best solution either. As your app grows, it can become even harder to handle the null values. For these reasons and more, handling nulls at the language level is probably the best way to avoid the infamous `NullPointerEception`.  

To build on this idea, the Dart team introduced **sound null safety**. This means that variables are considered non-nullable by default. If you don’t give an object null support when you create it, it will never store a `null`. As a result, you avoid null reference errors in your code.

---

## Activate Null Safety 
To activate null safety, you need to go to your **pubspec.yaml** file and ensure the SDK version is set to:

```
environment:
  sdk: ">=2.12.0 <3.0.0"
```

Make sure to run `flutter pub get` after updating the **pubspec.yaml**.

---

## The Dart Type System
Before we move on to creating null safe apps, let's discuss the type system implemented by Dart.  
Dart divides types into two groups:

- Nullable types - can contain either a value or null
- Non-Nullable types - can only contain values. Nulls are not permitted

Let's have a look at these types in the next section.

---

## Understanding Sound Null Safety
Now that we have null-safety activated, all our types are by default **non-nullable**, which means they all require some value.

```
String name = 'Aditya Sharma';   // This works ✔
String name = null;   // Will throw error ❌
```

Adding a `?` makes the Type nullable and allows us to pass a null value to our `name` field.

```
String? name = 'Aditya Sharma';   // This works ✔
String? name = null;   // This works too ✔
```

Let's now have a look at some real-world scenarios:

### 1. Null Checks
Null check is a Dart feature that helps you to avoid null related errors.

Let's take an example:

```dart
void setName(String name) {  
  name.toUpperCase();
  print(name);
}

void main() {
  setName(null);   // Throws error ❌
}
```

In this case, type String is not nullable and thus we cannot store a null inside it.

If we want to pass a null value to `setName()`, we change the parameter type to `String?`:

```dart
void setName(String? name) {
  name.toUpperCase();   // Warning here ❌
  print(name);
}

void main() {
  setName(null);
}
```

Now, the compiler warns us that we cannot call the `upperCase()` on `name` as it can also store a null value.  
We have 2 ways to fix it:

1. The null aware accessor `?.`:  
```dart
void setName(String? name) {
     name?.toUpperCase();
     print(name);
}
//
void main() {
     setName(null);
}
```

2. Check for null before calling any method on `name`:
```dart
void setName(String? name) {
     if (name == null) return;
     name.toUpperCase();
     print(name);
}
//
void main() {
     setName(null);
}
```

### 2. Non-nullable Type to Nullable Type Conversion
When converting from `Type` to `Type?`, there is no problem as `Type?` accepts both real value and null value.

```
void main() {
  String name = 'Aditya';
  String? newName = name;   // Works fine ✔
}
```

### 3. Nullable Type to Non-nullable Type Conversion
When converting from `Type?` to `Type`, the compiler gives us a warning.

```dart
void main() {
  String? name = null;
  String newName = name;   // Warning ❌
}
```

We have 3 ways to fix it:

1. Check for null before conversion:
```
void main() {
     String? name = null;
 
     if (name != null) {
       String newName = name;  
     }
}
```

2. Using the if-null `??` operator for providing a default value:
```
void main() {
     String? name = null;
     String newName = name ?? 'Aditya';  
}
```

3. Using the Bang operator `!` (also called the null assertion operator):
```
void main() {
     String? name = null;
     String newName = name!;  
}
```
> PS: If you’re sure that an expression with a nullable type isn’t null, you can use a null assertion operator `!` to make Dart treat it as non-nullable. By adding `!` just after the expression, you tell Dart that the value won’t be `null`, and that it’s safe to assign it to a non-nullable variable.  
⚠️: If you’re wrong, Dart throws an exception at run-time. This makes the `!` operator unsafe, so don’t use it unless you’re very sure that the expression isn’t null.

### 4. Late Init
Sometimes, you can’t initialize properties in the constructor, but you’ll define them in other methods of your class. In that case, you mark those properties with `late`.

Another advantage of late is lazy initialization. Dart will not initialize late properties until they’re used for the first time. This can be useful during app initialization when an expression is costly or might not be needed.

```dart
late String name;

@Override
void initState() {
  super.initState();
  
  name = 'Aditya Sharma';
}
```

---

## Sound Null Safety In a Nutshell
Dart has three main operators to work with null:

- Null-aware operators: The null aware accessor `?.`, which accesses properties of its operand, is an example. If the operand is `null`, then it will **not throw an exception**. Instead, it shows null text.
- Bang operator: Use `!` to cast away nullability. It tries to cast a nullable type to a non-nullable type, **throwing an exception** if the operand is `null`.
- If-null operator: `??` is shorthand for an if-else condition. If the left side of the `??` operator is null, then it will use its right side.
- Late init: Use `late` on variables when you’re sure you’ll initialize them before using them. Use `late` with class properties.

---

## References
- %[https://www.raywenderlich.com/21955673-non-nullable-dart-understanding-null-safety#toc-anchor-018]
- %[https://www.youtube.com/watch?v=PnSpQkOKwv0&t=52s]

---

That's all folks! Do let me know what you liked and what can be improved in the comments below! Also, feel free to drop me a line if you don't understand something. ✌

Thanks for reading!