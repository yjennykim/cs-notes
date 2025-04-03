## Ch1: Intro to Design Patterns

- <b>Design Principle: Identify the aspects of your
application that vary and separate
them from what stays the same.</b>
	-  take the parts that vary and encapsulate them, so that later you can alter or extend the parts that vary without affecting those that don’t

What is an interface?
- When you write an interface, you're basically saying: "I need something that..."
- Defines what the behavior a an object will have, but it will not actually specify the behavior.  It is a contract - a guarantee - that a certain class can do something.
- There is no implementation. Only describes What methods need to be implemented

Problem: How to design a Duck, when not all ducks fly or quack?
- Don't put a `Flyable` interface into Duck, because then every duck would have to define its own behavior for flying. Lots of duplicate code. Instead:
- Pull out Quacking and Flying behavior
- Use an interface to represent each behavior – for instance, `FlyBehavior` and `QuackBehavior` – and each implementation of a behavior will implement one of those interfaces.
	- Class for `FlyWithWings` that inherits from `FlyBehavior`
	- Classes for `Quack`, `Squeak` that inherit from `QuackBehavior`
- With this design, other types of objects can reuse our fly and quack behaviors since these behaviors are not tucked into our Duck classes
