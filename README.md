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
