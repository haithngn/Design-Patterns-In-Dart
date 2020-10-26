Design Patterns implemented in Dart 2.10.1
========================================

Creational Patterns
* [x] [Singleton](#singleton)
* [x] [Abstract Factory](#abstract-factory)
* [x] [Builder](#builder)
* [x] [Factory Method](#factory-method)
* [x] [Prototype](#prototype)
* [x] [Object Pool](#object-pool)

Structural Patterns
* [x] [Adapter](#adapter)
* [x] [Bridge](#bridge)
* [ ] [Composite](#composite)
* [ ] [Decorator](#decorator)
* [ ] [Facade](#facade)
* [ ] [Flyweight](#flyweight)
* [ ] [Proxy](#proxy)

Behavioral Patterns
* [x] [Chain of Responsibility](#chain-of-responsibility)
* [ ] [Command](#command)
* [ ] [Interpreter](#interpreter)
* [ ] [Iterator](#iterator)
* [ ] [Mediator](#mediator)
* [ ] [Memento](#memento)
* [ ] [Observer](#observer)
* [ ] [State](#state)
* [ ] [Strategy](#strategy)
* [ ] [Template Method](#template-method)
* [ ] [Visitor](#visitor)

[Creational](https://en.wikipedia.org/wiki/Creational_pattern)
============

Singleton
--------------------------
In software engineering, the singleton pattern is a software design pattern that restricts the instantiation of a class to one "single" instance. This is useful when exactly one object is needed to coordinate actions across the system. The term comes from the mathematical concept of a singleton. - [Wikipedia](https://en.wikipedia.org/wiki/Singleton_pattern)
### Implementation
```dart
class FactorySingleton {
  static final FactorySingleton _singleton = FactorySingleton._internal();

  factory FactorySingleton() {
    return _singleton;
  }

  FactorySingleton._internal();
}
```

### Usage
```dart
var singletoA = FactorySingleton();
var singletoB = FactorySingleton();
print(identical(singletoA, singletoB));
```

Factory Method
--------------------------
The factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. - [Wikipedia](https://en.wikipedia.org/wiki/Factory_method_pattern)
### Implementation
```dart
abstract class Repairer {
  void perform();
}

class CarRepairer extends Repairer {
  @override
  void perform() {
    print("Starting repair a car.");
  }
}

class BicycleRepairer extends Repairer {
  @override
  void perform() {
    print("Starting repair a bicycle.");
  }
}

enum Vehicle {
  Car, Bicycle
}

abstract class VehicleFactory {
  static Repairer getRepairer(Vehicle vehicle) {
    switch (vehicle) {
      case Vehicle.Car:
        return CarRepairer();
      case Vehicle.Bicycle:
        return BicycleRepairer();
    }
  }
}
```
### Usage
```dart
var bicycleRepairer = VehicleFactory.getRepairer(Vehicle.Bicycle);
bicycleRepairer.perform();

var carRepairer = VehicleFactory.getRepairer(Vehicle.Car);
carRepairer.perform();
```

Abstract Factory
--------------------------
The abstract factory pattern provides a way to encapsulate a group of individual factories that have a common theme without specifying their concrete classes. - [Wikipedia](https://en.wikipedia.org/wiki/Abstract_factory_pattern)
### Implementation
```dart
enum Vehicle {
  Car, Bicycle
}

abstract class Repairer {
  void replaceTires();
  void paint();
}

abstract class VehicleAbstractFactory {
  Repairer getRepairer();
}

class VehicleFactory {
  static VehicleAbstractFactory getFactory(Vehicle vehicle) {
    switch (vehicle) {
      case Vehicle.Car:
        return CarRepairFactory();
      case Vehicle.Bicycle:
        return BicycleRepairFactory();
    }
  }
}

class BicycleRepairFactory implements VehicleAbstractFactory {
  @override
  Repairer getRepairer() {
    return BicycleRepairer();
  }
}

class BicycleRepairer extends Repairer {
  @override
  void paint() {
    print("Hi sir, your bicycle will be paint on next 30 mins");
  }

  @override
  void replaceTires() {
    print("Hi sir, the bicycle's tires have been fixed.");
  }
}

class CarRepairFactory implements VehicleAbstractFactory {
  @override
  Repairer getRepairer() {
    return CarRepairer();
  }
}

class CarRepairer extends Repairer {
  @override
  void paint() {
    print("Hi sir, your car will be paint on next 3 hours");
  }

  @override
  void replaceTires() {
    print("Hi sir, the car's tires have been replaced.");
  }
}
```
### Usage
```dart
  var factory = VehicleFactory.getFactory(Vehicle.Car);
  var carRepairer = factory.getRepairer();
  carRepairer.replaceTires();
  carRepairer.paint();

  var bicycleFactory = VehicleFactory.getFactory(Vehicle.Bicycle);
  var bicycleRepairer = bicycleFactory.getRepairer();
  bicycleRepairer.replaceTires();
  bicycleRepairer.paint();
```

Builder
--------------------------
The builder pattern is a design pattern designed to provide a flexible solution to various object creation problems in object-oriented programming. The intent of the Builder design pattern is to separate the construction of a complex object from its representation. - [Wikipedia](https://en.wikipedia.org/wiki/Builder_pattern)
### Implementation
```dart
enum MotherBoard {
  Asus, Intel
}

enum CPU {
  INTEL_Core_I5, INTEL_Core_7, AMD
}

enum RAM {
  DDR3, DDR4, DDR5
}

enum NetworkCard {
  TP_Link_TG, Intel_I350_T4
}

enum Keyboard {
  Magic_Keyboard_2, Keychron_K6, Keychron_K8
}

enum Storage {
  Seagate_SSD, SamSung_HDD
}

enum Monitor {
  SamSung_LCL, DELL_LCD, LG_LCD
}

enum Mouse {
  Logitech_M590, Magic_Mouse
}

class Computer {
  MotherBoard motherboard;
  CPU processor;
  RAM memory;
  Storage storage;
  NetworkCard networkCard;
  Monitor monitor;
  Keyboard keyboard;
  Mouse mouse;

  Computer(this.motherboard, this.processor, this.memory, this.storage,
      this.networkCard, this.monitor, this.keyboard, this.mouse);

  @override
  String toString() {
    return " Computer Configuration:\n Processor: $processor \n Motherboard: $motherboard \n "
        "Memory: $memory \n Storage: $storage \n Network card: $networkCard \n "
        "Monitor: $monitor \n Keyboard: $keyboard \n Mouse: $mouse";
  }
}

abstract class ComputerBuilder {
  ComputerBuilder motherboard(MotherBoard mainboard);
  ComputerBuilder processor(CPU cpu);
  ComputerBuilder memory(RAM memory);
  ComputerBuilder network(NetworkCard card);
  ComputerBuilder storage(Storage disk);
  ComputerBuilder monitor(Monitor monitor);
  ComputerBuilder keyboard(Keyboard keyboard);
  ComputerBuilder mouse(Mouse mouse);
  Computer build();
}

class MartianComputerBuilder extends ComputerBuilder {
  Keyboard _keyboard;
  RAM _memory;
  Mouse _mouse;
  NetworkCard _networkCard;
  CPU _processor;
  Storage _storage;
  MotherBoard _motherboard;
  Monitor _monitor;

  @override
  ComputerBuilder keyboard(Keyboard keyboard) {
    this._keyboard = keyboard;
    return this;
  }

  @override
  ComputerBuilder memory(RAM memory) {
    this._memory = memory;
    return this;
  }

  @override
  ComputerBuilder mouse(Mouse mouse) {
    this._mouse = mouse;
    return this;
  }

  @override
  ComputerBuilder network(NetworkCard card) {
    this._networkCard = card;
    return this;
  }

  @override
  ComputerBuilder processor(CPU cpu) {
    this._processor = cpu;
    return this;
  }

  @override
  ComputerBuilder storage(Storage disk) {
    this._storage = disk;
    return this;
  }

  @override
  ComputerBuilder motherboard(MotherBoard mainboard) {
    this._motherboard = mainboard;
    return this;
  }

  @override
  ComputerBuilder monitor(Monitor monitor) {
    this._monitor = monitor;
    return this;
  }

  @override
  Computer build() {
    return Computer(_motherboard, _processor, _memory, _storage, _networkCard, _monitor, _keyboard, _mouse);
  }
}
```
### Usage
```dart
var computer = MartianComputerBuilder()
      .motherboard(MotherBoard.Asus)
      .processor(CPU.INTEL_Core_I5)
      .memory(RAM.DDR3)
      .network(NetworkCard.TP_Link_TG)
      .storage(Storage.Seagate_SSD)
      .monitor(Monitor.DELL_LCD)
      .keyboard(Keyboard.Keychron_K6)
      .mouse(Mouse.Logitech_M590).build();
print(computer);
```

Prototype
--------------------------
The prototype pattern is a creational design pattern in software development. It is used when the type of objects to create is determined by a prototypical instance, which is cloned to produce new objects. - [Wikipedia](https://en.wikipedia.org/wiki/Prototype_pattern)
### Implementation
```dart
enum OS {
  CentOS, OpenSUSE, Ubuntu
}

enum IDE {
  AndroidStudio, IntelIJ
}

abstract class Computer {
  void installIDE(IDE ide);
}

abstract class Cloneable {
  Computer clone();
}


class LinuxComputer extends Computer implements Cloneable {
  OS _operatingSystem;
  IDE _ide;

  LinuxComputer(this._operatingSystem);

  @override
  Computer clone() {
    return LinuxComputer(this._operatingSystem);
  }

  void installIDE(IDE ide) {
    _ide = ide;
  }

  @override
  String toString() {
    return "OS: $_operatingSystem, IDE: $_ide";
  }
}
```
### Usage
```dart
  var androidComputer = new LinuxComputer(OS.CentOS);
  androidComputer.installIDE(IDE.AndroidStudio);

  print(androidComputer);

  var javaComputer = androidComputer.clone();
  javaComputer.installIDE(IDE.IntelIJ);
  print(javaComputer);
```

Object Pool
--------------------------
The object pool pattern is a software creational design pattern that uses a set of initialized objects kept ready to use – a "pool" – rather than allocating and destroying them on demand. - [Wikipedia](https://en.wikipedia.org/wiki/Adapter_pattern)
### Implementation
```dart
class Developer {
  String id;

  Developer(this.id);

  void develop() {
    print("Writes code, fixes bug");
  }
  @override
  String toString() {
    return id;
  }
}

class DeveloperPool {
  List<Developer> _availableDevelopers = [];
  List<Developer> _inUseDevelopers = [];

  Developer pick() {
    var dev = _availableDevelopers.length != 0 ? _availableDevelopers.first : null;
    if (dev == null) {
      int rand = Random().nextInt(100);
      String id = "developer_id $rand";
      dev = Developer(id);
      _inUseDevelopers.add(dev);
    }
    return dev;
  }

   void release(Developer developer) {
    _inUseDevelopers.removeWhere((Developer dev) => dev.id == developer.id);
    _availableDevelopers.add(developer);
   }

   void terminate(Developer developer) {
      if (_inUseDevelopers.firstWhere((dev) => dev.id == developer.id) != null) {
        throw ("Cannot terminate a develop who is now using.");
     }
      _availableDevelopers.removeWhere((dev) => dev.id == developer.id);
   }
}
```
### Usage
```dart
  var pool = DeveloperPool();
  var webDev = pool.pick();
  print(webDev);
  webDev.develop();

  var mobile = pool.pick();
  print(mobile);

  pool.release(webDev);
  var dom = pool.pick();
  print(dom);

  try {
    pool.terminate(mobile);
  } catch (error) {
    print(error);
  }
```

## [Structural](https://en.wikipedia.org/wiki/Structural_pattern)
--------------------------
Adapter
--------------------------
The adapter pattern is a software design pattern (also known as wrapper, an alternative naming shared with the decorator pattern) that allows the interface of an existing class to be used as another interface. - [Wikipedia](https://en.wikipedia.org/wiki/Adapter_pattern)
### Implementation
```dart
//Target
abstract class MartianTarget {
  void send(String signal);
}

//Adaptee
class KryptonianAdaptee {
  void receive(String sentence) {
    print("Retrieving sentence : $sentence from the adapter");
  }
}

//Adapter
class UniversalAdapter extends MartianTarget {
  KryptonianAdaptee _adaptee;

  UniversalAdapter(this._adaptee);

  @override
  void send(String sentence) {
    print("Target sending a sentence... $sentence");
    String output = _translate(sentence);
    print("Decrypted!");
    print("Sending to the adaptee...");
    _adaptee.receive(output);
  }

  String _translate(String sentence) {
    print("Decrypting...");
    return "⋥ ⋦ ⋧ ⋨ ⋩ ⋪ ⋫ ⋬ ⋭";
  }

}
```
### Usage
```dart
var superman = KryptonianAdaptee();
MartianTarget matt = UniversalAdapter(superman);
matt.send("⁕ ⁖ ⁗ ⁘ ⁙ ⁚ ⁛ ⁜ ⁝ ⁞");
```

Bridge
--------------------------
The bridge pattern is a design pattern used in software engineering that is meant to "decouple an abstraction from its implementation so that the two can vary independently" - [Wikipedia](https://en.wikipedia.org/wiki/Bridge_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Composite
--------------------------
The composite pattern is a partitioning design pattern. The composite pattern describes a group of objects that are treated the same way as a single instance of the same type of object. - [Wikipedia](https://en.wikipedia.org/wiki/Composite_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Decorator
--------------------------
The decorator pattern is a design pattern that allows behavior to be added to an individual object, dynamically, without affecting the behavior of other objects from the same class. - [Wikipedia](https://en.wikipedia.org/wiki/Decorator_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Facade
--------------------------
The facade pattern (also spelled façade) is a software-design pattern commonly used in object-oriented programming. Analogous to a facade in architecture, a facade is an object that serves as a front-facing interface masking more complex underlying or structural code - [Wikipedia](https://en.wikipedia.org/wiki/Facade_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Flyweight
--------------------------
The flyweight is a software design pattern. A flyweight is an object that minimizes memory usage by sharing as much data as possible with other similar objects; it is a way to use objects in large numbers when a simple repeated representation would use an unacceptable amount of memory. - [Wikipedia](https://en.wikipedia.org/wiki/Flyweight_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Proxy
--------------------------
In short, a proxy is a wrapper or agent object that is being called by the client to access the real serving object behind the scenes. - [Wikipedia](https://en.wikipedia.org/wiki/Proxy_pattern)
### Implementation
```dart
```
### Usage
```dart
```

[Behavioral](http://en.wikipedia.org/wiki/Behavioral_pattern)
--------------------------

Chain of Responsibility
--------------------------
The chain-of-responsibility pattern is a design pattern consisting of a source of command objects and a series of processing objects.[1] Each processing object contains logic that defines the types of command objects that it can handle; the rest are passed to the next processing object in the chain. - [Wikipedia](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern)
### Implementation
```dart
enum SeverityLevel {
  TRACE, INFO, DEBUG, WARNING, ERROR, FATAL
}

abstract class Logger {
  SeverityLevel _level;
  Logger _nextLogger;

  Logger(this._level);

  Logger setNextLogger(Logger logger) {
    this._nextLogger = logger;
    return _nextLogger;
  }

  void log(SeverityLevel severity, String message) {
    if (_level == severity) {
      _write(message);
    }
    if (_nextLogger != null) {
      _nextLogger.log(severity, message);
    }
  }

  void _write(String message);
}

class ConsoleLogger extends Logger {
  ConsoleLogger(SeverityLevel level) : super(level);
  @override
  void _write(String message) {
    print("[CONSOLE]: $message");
  }
}

class FileLogger extends Logger {
  FileLogger(SeverityLevel level) : super(level);

  @override
  void _write(String message) {
    print("[FILE]: $message");
  }
}

class EmailLogger extends Logger {
  EmailLogger(SeverityLevel level) : super(level);

  @override
  void _write(String message) {
    print("[EMAIL]: $message");
  }
}

enum Variant {
  DEBUG, UAT
}

abstract class LoggerFactory {
  static Logger getLogger(Variant variant) {
    switch (variant) {
      case Variant.UAT:
        var consoleLogger = ConsoleLogger(SeverityLevel.DEBUG);
        var fileLogger = consoleLogger.setNextLogger(FileLogger(SeverityLevel.ERROR));
        fileLogger.setNextLogger(EmailLogger(SeverityLevel.FATAL));
        return consoleLogger;
      case Variant.DEBUG:
        var consoleLogger = ConsoleLogger(SeverityLevel.DEBUG);
        consoleLogger.setNextLogger(FileLogger(SeverityLevel.ERROR));
        return consoleLogger;
    }
    var consoleLogger = ConsoleLogger(SeverityLevel.DEBUG);
    return consoleLogger;
  }
}
```
### Usage
```dart
  var log = LoggerFactory.getLogger(Variant.UAT);
  log.log(SeverityLevel.DEBUG, "Log to console");
  log.log(SeverityLevel.ERROR, "Writing to log file.");
  log.log(SeverityLevel.FATAL, "Sending to emails.");
```

Command
--------------------------
The command pattern is a behavioral design pattern in which an object is used to encapsulate all information needed to perform an action or trigger an event at a later time. This information includes the method name, the object that owns the method and values for the method parameters. - [Wikipedia](https://en.wikipedia.org/wiki/Command_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Interpreter
--------------------------
The interpreter pattern is a design pattern that specifies how to evaluate sentences in a language. The basic idea is to have a class for each symbol (terminal or nonterminal) in a specialized computer language. The syntax tree of a sentence in the language is an instance of the composite pattern and is used to evaluate (interpret) the sentence for a client. - [Wikipedia](https://en.wikipedia.org/wiki/Interpreter_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Iterator
--------------------------
The iterator pattern is a design pattern in which an iterator is used to traverse a container and access the container's elements. The iterator pattern decouples algorithms from containers; in some cases, algorithms are necessarily container-specific and thus cannot be decoupled. - [Wikipedia](https://en.wikipedia.org/wiki/Iterator_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Mediator
--------------------------
The mediator pattern defines an object that encapsulates how a set of objects interact. This pattern is considered to be a behavioral pattern due to the way it can alter the program's running behavior. - [Wikipedia](https://en.wikipedia.org/wiki/Mediator_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Memento
--------------------------
The memento pattern is a software design pattern that provides the ability to restore an object to its previous state (undo via rollback). - [Wikipedia](https://en.wikipedia.org/wiki/Memento_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Observer
--------------------------
The observer pattern is a software design pattern in which an object, called the subject, maintains a list of its dependents, called observers, and notifies them automatically of any state changes, usually by calling one of their methods. - [Wikipedia](https://en.wikipedia.org/wiki/Observer_pattern)
### Implementation
```dart
```
### Usage
```dart
```

State
--------------------------
The state pattern is a behavioral software design pattern that allows an object to alter its behavior when its internal state changes - [Wikipedia](https://en.wikipedia.org/wiki/State_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Strategy
--------------------------
The strategy pattern (also known as the policy pattern) is a behavioral software design pattern that enables selecting an algorithm at runtime. - [Wikipedia](https://en.wikipedia.org/wiki/Strategy_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Template Method
--------------------------
The template method is a method in a superclass, usually an abstract superclass, and defines the skeleton of an operation in terms of a number of high-level steps. - [Wikipedia](https://en.wikipedia.org/wiki/Template_method_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Visitor
--------------------------
The visitor design pattern is a way of separating an algorithm from an object structure on which it operates. A practical result of this separation is the ability to add new operations to existing object structures without modifying the structures. - [Wikipedia](https://en.wikipedia.org/wiki/Visitor_pattern)
### Implementation
```dart
```
### Usage
```dart
```

Contribution
==========
Feel free to create a pull request

License
==========

This repository is released under the [MIT license](./LICENSE).
