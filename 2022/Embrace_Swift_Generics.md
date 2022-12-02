# Embrace_Swift_Generics

### [WWDC](https://developer.apple.com/videos/play/wwdc2022/110352/)

```swift
// 동물

struct Cow
  func eat(_ food: Hay) {}
}

// 음식
struct Hay {}

struct Farm {
  func feed(_ animal: Cow) {
    animal.eat(Hay())
  }
}
```

농장에 동물을 추가해 봅시다!

```swift
struct Cow {
  func eat(_ food: Hay) {}
}

struct Horse {
  func eat(_ food: Carrot) {}
}

struct Chicken {
  func eat(_ food: Grain) {}
}
```

#### Different forms of polymorphism

- Overloading: ad-hoc polymorphism
- Subtypes: subtype polymorphism
- Generics: parametric polymorphism


### Subtypes를 이용한 추상화

```swift
class Animal {
  func eat(_ food: Any) { fatalError("ERROR")}
}

class Cow: Animal {
  override func eat(_ food: Any) {
    guard let food = food as? Hay else { fatalError() }
  }
}

class Horse: Animal {
  override func eat(_ food: Any) {
    guard let food = food as? Carrot else { fatalError() }
  }
}

class Chicken: Animal {
  override func eat(_ food: Any) {
    guard let food = food as? Grain else { fatalError() }
  }
}
```

이런식으로도 쓸 수 있지만? 어색하다!

```swift
class Animal<Food> {
  func eat(_ food: Food) { fatalError("ERROR")}
}

class Cow: Animal<Hay> {
  override func eat(_ food: Hay) {
    
  }
}

class Horse: Animal<Carrot> {
  override func eat(_ food: Carrot) {
    
  }
}

class Chicken: Animal<Grain> {
  override func eat(_ food: Grain) {
    
  }
}
```

추후 확장됬을때도, 문제가 될 수 있다

```swift
class Animal<Food, Habitat, Commodity> {
  func eat(_ food: Food) { fatalError("ERROR")}
}

```

### Protocol을 이용한 추상화


```swift
protocol Animal {
  associatedtype Feed
  
  func eat(_ food: Feed)
}

struct Cow: Animal {
  func eat(_ food: Hay) {}
}

struct Horse: Animal {
  func eat(_ food: Carrot) {}
}

struct Chicken: Animal {
  func eat(_ food: Grain) {}
}
```

```swift
// ERROR: AssociatedType은 타입으로 쓸 수 없다
struct Farm {
  func feed(_ animal: Animal) { ... }
}

// OK
struct Farm {
  func feed<A: Animal>(_ animal: A) { ... }
}

// OK
struct Farm {
  func feed(_ animal: some Animal) { ... }
}
```

### Some

- Opaque Type
- 범위에서 값이 고정되어 있어야 한다

```swift
var animal: Animal = Cow() // Complie Error

var animal: some Animal = Cow() // OK
animal = Horse() // Complie Error

var animals: [some Animal] = [Cow(), Cow(), Cow()] // OK

var animals: [some Animal] = [Cow(), Horse()] // Complie Error
```

```swift
// Compile Error
func makeAnimal(_ condition: Bool) -> Animal {
  if condition {
    return Cow()
  } else {
    return Horse()
  }
}

// Complie Error
func makeAnimal(_ condition: Bool) -> some Animal {
  if condition {
    return Cow()
  } else {
    return Horse()
  }
}

// OK
func makeAnimal(_ condition: Bool) -> any Animal {
  if condition {
    return Cow()
  } else {
    return Horse()
  }
}
```

### Self를 포함한 프로토콜

#### ex) Equatable

```swift
public protocol Equatable {

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    static func == (lhs: Self, rhs: Self) -> Bool
}
```

ex1)
```swift
// Compile Error
func makeRandomNumber() -> Equatable {
  return Int.random(in: 1...10)
}
```

ex2)
```swift
func makeRandomNumber() -> some Equatable {
  return Int.random(in: 1...10)
}

let n1 = makeRandomNumber()
let n2 = makeRandomNumber()

print(n1 == n2)
```

ex3)
```swift
// Complie Error
func makeRandomNumber() -> some Equatable {
  if Bool.random() {
    return Double.random(in: 1...10)
  } else {
    return Int.random(in: 1...10)
  }
}
```

ex4)
```swift
func makeRandomNumber() -> any Equatable {
  if Bool.random() {
    return Double.random(in: 1...10)
  } else {
    return Int.random(in: 1...10)
  }
}

let n1 = makeRandomNumber()
let n2 = makeRandomNumber()

 // Compile Error
print(n1 == n2)
```
