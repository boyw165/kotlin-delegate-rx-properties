[![CircleCI](https://circleci.com/gh/boyw165/kotlin-delegate-rx-properties.svg?style=svg)](https://circleci.com/gh/boyw165/kotlin-delegate-rx-properties)
[ ![Download](https://api.bintray.com/packages/boyw165/android/kotlin-delegate-rx-properties/images/download.svg) ](https://bintray.com/boyw165/android/kotlin-delegate-rx-properties/_latestVersion)

Kotlin Delegate ReactiveX Properties
===

A Kotlin delegate turning properties to ReactiveX observable easily.

Setup
---

Add this into your dependencies block.

```
implementation 'io.useful:kotlin-delegate-rx-properties:x.x.x'
```

If you cannot find the package, add this to your gradle repository

```
maven {
    url 'https://dl.bintray.com/boyw165/android'
}
```

Usage
---

### RxValue

It works similarly as [Delegates.observable()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/observable.html), moreover it's with ReactiveX power!

```kotlin
private var prop by RxValue(0)

// Use "::" to reflect the Delegate
this::prop
    .changed()
    .subscribe { newValue ->
        println(newValue)
    }
    .addTo(disposableBag)

prop = 1 // See assignment to 1
prop = 2 // See assignment to 2
prop = 3 // See assignment to 3
```

### RxMutableSet

To observe the item addition and removal from a `MutableSet`, which are `itemAdded()` and `itemRemoved` accordingly.

```kotlin
private val mutableSet by RxMutableSet(mutableSetOf<Int>())

// Use "::" to reflect the Delegate
this::mutableSet
    .itemAdded()
    .subscribe { item ->
        println("$item added")
    }
    .addTo(disposableBag)

this::mutableSet
    .itemRemoved()
    .subscribe { item ->
        println("$item removed")
    }
    .addTo(disposableBag)

mutableSet.add(0) // See 0 added
mutableSet.add(1) // See 1 added
mutableSet.add(2) // See 2 added
```

### RxMutableMap

To observe the tuple addition and removal from a `MutableMap`, which are `tupleAdded` and `tupleRemoved` accordingly.

```kotlin
private val mutableMap by RxMutableMap(mutableMapOf<Int, String>())

// Use "::" to reflect the Delegate
this::mutableMap
    .tupleAdded()
    .subscribe { (k, v) ->
        println("tuple=(key=$k, value=$v) just added")
    }
    .addTo(disposableBag)

this::mutableMap
    .tupleRemoved()
    .subscribe { (k, v) ->
        println("tuple=(key=$k, value=$v) just removed")
    }
    .addTo(disposableBag)

mutableMap[0] = "000" // See (0, "000") added
mutableMap[1] = "111" // See (1, "111") added
mutableMap[2] = "222" // See (2, "222") added
```

### Or checkout the unit test suit

- [RxValue](lib-rx-delegate/src/test/java/co/sodalabs/delegate/rx/RxValueTest.kt) test
- [RxMutableSet](lib-rx-delegate/src/test/java/co/sodalabs/delegate/rx/RxMutableSetTest.kt) test
- [RxMutableMap](lib-rx-delegate/src/test/java/co/sodalabs/delegate/rx/RxMutableMapTest.kt) test

How it works
---

Kotlin makes it easy by using the [Delegate](https://kotlinlang.org/docs/reference/delegated-properties.html). The Kotlin compiler will generate code for you, for example:

```
class C {
    var prop: Type by MyDelegate()
}

// this code is generated by the compiler 
// when the 'provideDelegate' function is available:
class C {
    // calling "provideDelegate" to create the additional "delegate" property
    private val prop$delegate = MyDelegate().provideDelegate(this, this::prop)
    var prop: Type
        get() = prop$delegate.getValue(this, this::prop)
        set(value: Type) = prop$delegate.setValue(this, this::prop, value)
}
```

We create the magic Delegate with Observable capability and yet, it's still the problem to get the real Delegate reference. One way to do get the Delegate is through [Reflection](https://kotlinlang.org/docs/reference/reflection.html). Hence, the library depends on Kotlin Reflection and you have to use `::` to reflect the Delegate to get the Observable functionality.

Dependency
---

- Jake Wharton, [RxRelay](https://github.com/JakeWharton/RxRelay).
- Kotlin [Reflection](https://mvnrepository.com/artifact/org.jetbrains.kotlin/kotlin-reflect).
