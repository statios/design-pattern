# Design Pattern
## Creational Design Pattern
### Factory Pattern
#### #1
```swift 
protocol Animal {
    func speak()
}

class Cat: Animal {
    func speak() { print("meow") }
}

class Dog: Animal {
    func speak() { print("bark") }
}

enum AnimalName {
    case cat, dog
}

func animalFactory(animalName: AnimalName) -> Animal {
    switch animalName {
    case .cat: return Cat()
    case .dog: return Dog()
    }
}

var cat = animalFactory(animalName: .cat)
cat.speak() // meow

var dog = animalFactory(animalName: .dog)
dog.speak() // bark
```
#### #2
```swift
protocol Animal {
    func speak()
}

class Cat: Animal {
    func speak() { print("meow") }
}

class Dog: Animal {
    func speak() { print("bark") }
}

enum AnimalName {
    case cat, dog
}

class AnimalFactory {
    func createAnimal(animalName: AnimalName) -> Animal {
        switch animalName {
        case .cat: return Cat()
        case .dog: return Dog()
        }
    }
}

var factory = AnimalFactory()
var cat = factory.createAnimal(animalName: .cat)
var dog = factory.createAnimal(animalName: .dog)

cat.speak() // meow
dog.speak() // bark
```
### Factory Method Pattern
팩토리에서 단순 생성만 하는것이 아니라 여러가지 기능을 추가하고 싶을 때 사용 합니다. 객체를 생성하는 메서드를 여러 Factory class에서 상속을 통해서 구현하였으면 Factory Method Pattern이 사용되었다고 할 수 있습니다. 예시에서는 class 상속이 아닌 protocol을 사용하였습니다.
```swift
protocol Animal {
    func speak()
}

class Cat: Animal {
    func speak() { print("meow") }
}

class Dog: Animal {
    func speak() { print("bark") }
}

protocol AnimalFactory {
    associatedtype AnimalType: Animal
    func createAnimal() -> AnimalType
}

class CatFactory: AnimalFactory {

    typealias AnimalType = Cat

    var catCount: Int = .zero

    func createAnimal() -> Cat {
        catCount += 1
        return Cat()
    }
}

class DogFactory: AnimalFactory {

    typealias AnimalType = Dog

    var dog: Dog?

    func createAnimal() -> Dog {
        return Dog()
    }

    func haveDog() {
        self.dog = createAnimal()
    }

    func makeWings(dog: Dog) -> Dog {
        print("dog wings added")
        return dog
    }
}

// Usage

var catFactory = CatFactory()
var cat = catFactory.createAnimal()
print("\(catFactory.catCount) cats are created") // 1 cats are created

var dogFactory = DogFactory()
var dog = dogFactory.createAnimal()
var wingFactory = dogFactory.makeWings(dog: dog) // dog wings added
```
### Abstract Factory Pattern
matrix 관계를 가진 class object들을 생성할 때 유용합니다. 하나의 팩토리 인터페이스를 가지고 matrix 관계로 표현되는 각 조합에 대해서 팩토리를 정의 합니다. 예시에서는 다크모드를 지원하는 UI 객체들의 조합을 생성하는 팩토리를 표현하였습니다.

```swift
/*
          | DarkMode         | LightMode
 ---------------------------------------------
 Button   | DarkModeButton   | LightModeButton
 CheckBox | DarkCheckBox     | LightCheckBox
 ScrollBar| DarkScrollBar    | LightScrollBar
 ---------------------------------------------
 */
class Button {
    var backgroundColor: UIColor { return .white }
}

class LightButton: Button {
    override var backgroundColor: UIColor { return .white }
}

class DarkButton: Button {
    override var backgroundColor: UIColor { return .black }
}

class CheckBox {
    var backgroundColor: UIColor { return .white }
}

class LightCheckBox: CheckBox {
    override var backgroundColor: UIColor { return .white }
}

class DarkCheckBox: CheckBox {
    override var backgroundColor: UIColor { return .black }
}

class ScrollBar {
    var backgroundColor: UIColor { return .white }
}

class LightScrollBar: ScrollBar {
    override var backgroundColor: UIColor { return .white }
}

class DarkScrollBar: ScrollBar {
    override var backgroundColor: UIColor { return .black }
}

protocol UIFactory {
    func getButton() -> Button
    func getCheckBox() -> CheckBox
    func getScrollBar() -> ScrollBar
}

class LightUIFactory: UIFactory {
    func getButton() -> Button { return LightButton() }
    func getCheckBox() -> CheckBox { return LightCheckBox() }
    func getScrollBar() -> ScrollBar { return LightScrollBar() }
}

class DarkUIFactory: UIFactory {
    func getButton() -> Button { return DarkButton() }
    func getCheckBox() -> CheckBox { return DarkCheckBox() }
    func getScrollBar() -> ScrollBar { return DarkScrollBar() }
}
```
### Builder Pattern
object의 생성 과정에 많은 argument가 필요하다거나 복잡할 때 이를 간단하게 만들 수 있습니다. builder의 각 setter 메서드에서 자기 자신인 builder를 리턴하도록 하면 체인 형태로 사용할 수도 있습니다.
![1920px-Builder_UML_class_diagram svg](https://user-images.githubusercontent.com/42381560/196039602-86858d4d-2fb7-449d-826c-8309f664a2f2.png)
```swift
class Cat {

    var height: Float
    var weight: Float
    var color: UIColor

    init(height: Float, weight: Float, color: UIColor) {
        self.height = height
        self.weight = weight
        self.color = color
    }
}

class CatBuilder {
    var height: Float = .zero
    var weight: Float = .zero
    var color: UIColor = .white

    func setHeight(_ h: Float) -> CatBuilder {
        self.height = h
        return self
    }

    func setWeight(_ w: Float) -> CatBuilder {
        self.weight = w
        return self
    }

    func setColor(_ c: UIColor) -> CatBuilder {
        self.color = c
        return self
    }

    func build() -> Cat {
        return Cat(height: height, weight: weight, color: color)
    }
}

var cat = CatBuilder()
    .setHeight(20)
    .setWeight(10)
    .setColor(.green)
    .build()
```
Builder 클래스를 상속하여 specific한 builder를 정의할 수 있습니다.
```swift
class WhiteCatBuilder: CatBuilder {
    override init() {
        super.init()
        self.color = .white
    }
}

class BlackCatBuilder: CatBuilder {
    override init() {
        super.init()
        self.color = .black
    }
}
```
필수는 아니지만 빌더를 세팅해 주는 목적의 Director를 정의할 수 있습니다.
```swift
class CatDirector {
    func setSamllCat(builder: CatBuilder) {
        builder
            .setWeight(5)
            .setHeight(5)
    }

    func setBigCat(builder: CatBuilder) {
        builder
            .setWeight(100)
            .setHeight(100)
    }
}

var catDirector = CatDirector()
var blackCatBuilder = BlackCatBuilder()
catDirector.setBigCat(builder: blackCatBuilder)
var cat = blackCatBuilder.build() // 100cm, 100kg, black
```
### Singleton Pattern
프로세스가 실행 중에 오직 하나의 object만 생성되도록 강제하는 디자인 패턴입니다.
```swift
class Cat {
    static let shared = Cat()
    private init() { }
}
```
### Prototype Pattern
일반적인 `Cat` class를 정의하고 `name`만 다른 두 객체를 생성하려고 합니다.
#### 🙅‍♂️ 단순 생성하여 속성을 하나하나 지정하는 경우
```swift
class Cat {
    var color: String?
    var eyeColor: String?
    var noseColor: String?
    var tailColor: String?
    var name: String?
}

var kitty = Cat()
kitty.color = "white"
kitty.eyeColor = "white"
kitty.noseColor = "white"
kitty.tailColor = "white"
kitty.name = "kitty"

var nabi = Cat()
nabi.color = "white"
nabi.eyeColor = "white"
nabi.noseColor = "white"
nabi.tailColor = "white"
nabi.name = "nabi"
```
#### 🙅‍♂️ 단순 복사하는 경우
```swift
class Cat {
    var color: String?
    var eyeColor: String?
    var noseColor: String?
    var tailColor: String?
    var name: String?

    func clone() -> Cat {
        let newCat = Cat()
        newCat.color = self.color
        newCat.eyeColor = self.eyeColor
        newCat.noseColor = self.noseColor
        newCat.tailColor = self.tailColor
        newCat.name = self.name
        return newCat
    }
}

var kitty = Cat()
kitty.color = "white"
kitty.eyeColor = "white"
kitty.noseColor = "white"
kitty.tailColor = "white"
kitty.name = "kitty"

var nabi = kitty.clone()
nabi.name = "nabi"
```
#### 🙆‍♂️ 프로토타입을 이용하는 경우
```swift

class Cat {
    var color: String?
    var eyeColor: String?
    var noseColor: String?
    var tailColor: String?
    var name: String?

    func clone() -> Cat {
        let newCat = Cat()
        newCat.color = self.color
        newCat.eyeColor = self.eyeColor
        newCat.noseColor = self.noseColor
        newCat.tailColor = self.tailColor
        newCat.name = self.name
        return newCat
    }
}

class BlackCat: Cat {
    override init() {
        super.init()
        self.color = "black"
    }
}

class WhiteCat: Cat {
    override init() {
        super.init()
        self.color = "black"
    }
}

var blackCat = BlackCat()
blackCat.noseColor = "pink"
blackCat.tailColor = "green" // 'blackCat' is prototype

var kitty = blackCat.clone()
kitty.eyeColor = "white"
kitty.name = "kitty"

var nabi = blackCat.clone()
nabi.eyeColor = "blue"
nabi.name = "nabi"
```
## Structural Design Pattern
### Adapter Pattern
하나의 인터페이스를 다른 인터페이스로 전환하는 패턴입니다. Wrapper라고 부르기도 합니다.
```swift
protocol Animal {
    func walk()
}

class Cat: Animal {
    func walk() { print("cat walking") }
}

class Dog: Animal {
    func walk() { print("dog walking") }
}

func makeWalk(animal: Animal) {
    animal.walk()
}

var kitty = Cat()
var bingo = Dog()

makeWalk(animal: kitty) //"cat walking"
makeWalk(animal: bingo) //"dog walking"
```
```swift
class Fish { // fish doesn't have a walk method
    func swim() {
        print("fish swimming")
    }
}

var nemo = Fish()

//makeWalk(animal: nemo) ❌
```
```swift
class FishAdapter: Animal {
    var fish: Fish

    init(fish: Fish) {
        self.fish = fish
    }

    func walk() { self.fish.swim() }
}

var adaptedNemo = FishAdapter(fish: nemo)
makeWalk(animal: adaptedNemo)
```
### Proxy Pattern
서버의 예를 들어보겠습니다. 서버를 구성할 때 Client와 Server사이에 Proxy 서버를 두는 것이 일반적입니다. Proxy 서버는 Server와 커뮤니케이션 하면서 클라이언트에 Response를 전달하는 기능 외에도 그 목적에 따라 로그나 통계 기능을 가지는 경우도 있습니다. 혹은 프록시 뒤에 있는 서버단의 부하를 줄이기 위해서 Request에 대한 Response를 캐시로 가지고 있기도 합니다.

이처럼 Client 코드가 있고 사용하기를 원하는 Object가 있을 때 Object에 직접 접근하지 않고 Proxy를 두고 Client는 Proxy를 통해서만 Object의 기능을 사용하는 패턴입니다.
```swift
class Cat {
    func speak() { print("meow") }
}

var kitty = Cat()
kitty.speak()

class CatProxy {

    var cat: Cat

    init(cat: Cat) { self.cat = cat}

    func speak() {
        print("willSpeak") // validity checks, lazy init, more
        self.cat.speak()
        print("didSpeak") // loggings
    }
}

var kittyProxy = CatProxy(cat: kitty)
kittyProxy.speak()
```
프록시가 대상 오브젝트를 상속받거나 같은 프로토콜을 따르는 식으로 구성하는 것도 가능합니다.
```swift
protocol Animal {
    func speak()
}

class Cat: Animal {
    func speak() { print("meow") }
}

class CatProxy: Animal {

    var cat: Cat

    init(cat: Cat) {
        self.cat = cat
    }

    func speak() {
        print("bofore speak")
        self.cat.speak()
        print("after speak")
    }
}

func doSpeak(animal: Animal) {
    animal.speak()
}

var kitty = Cat()
var kittyProxy = CatProxy(cat: kitty)

doSpeak(animal: kittyProxy)
```
