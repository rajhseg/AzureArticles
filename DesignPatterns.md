**Creational Pattern**
1. **Singleton** - Class has only one instance.
   
2. **FactoryMethod** - An interface for creating objects in a superclass but allows subclasses to alter the type of objects that will be created.

3. **AbstractFactory** - Provide an interface for creating families of related objects without specifying their concrete classes.

4. **Builder** - Construct complex objects step-by-step.

5. **Prototype** - Create new objects by copying an existing object.

<br/>

**Structural Pattern**

1. **Adapter** - Convert the interface of a class into another interface the client expects.

2. **Bridge** - Decouple an abstraction from its implementation.

3. **Composite** - Compose objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions uniformly.

4. **Decorator** - Allows behavior to be added to an individual object, dynamically, without modifying its class.

5. **Facade** - Provides a simplified interface to a complex subsystem. It hides the complexities of the system and provides an easier way to interact with it.

6. **Flyweight** - Minimizing memory usage by sharing as much data as possible with similar objects.

7. **Proxy** - Provides a surrogate or placeholder for another object to control access to it. It allows you to add functionality (like lazy loading, access control, logging, etc.) without changing the original object's code.

<br/>

**Behavioral Pattern**

1. **Iterator** - Provides a way to access elements of a collection sequentially without exposing its underlying representation.

2. **Chain of Responsiblity** - Allows passing a request along a chain of handlers. Each handler decides either to process the request or to pass it to the next handler in the chain.

3. **Observor** - Establish a publish-subscribe relationship where subjects notify observers about state changes without being tightly coupled.

4. **TemplateMethod** - defines the skeleton of an algorithm in a base class but lets subclasses override specific steps without changing the overall algorithm’s structure.

5. **Memento** - Capture and externalize an object's internal state so that the object can be restored to that state later—without violating encapsulation

