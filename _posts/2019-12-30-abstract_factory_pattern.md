---
layout: default
title: Abstract Factory Pattern
---

# Abstract Factory Pattern

> An abstract factory pattern provides a layer of consistency around concrete factories by creating a rule for a group of families that create similar objects.

An abstract factory pattern is constructed using a structure like below:

1. `abstract class` that defines what concrete factories need to do, for example: `CreateAppetizer()` or `CreateDessert()`
2. `class` that implements the abstract class (the abstract factory)

### Implementation code

```c#
public abstract class AbstractFactory
{
    public abstract AbstractObjectA CreateObjectA();
    public abstract AbstractObjectB CreateObjectB();
}

public class ConcreteFactory : AbstractFactory
{
    public override AbstractObjectA CreateObjectA()
    {
      return new ConcreteObjectA();
    }

    public override AbstractObjectB CreateObjectB()
    {
      return new ConcreteObjectB();
    }
}

public class AnotherConcreteFactory : AbstractFactory
{
    public override AbstractObjectA CreateObjectA()
    {
      return new AnotherConcreteObjectA();
    }
    
    public override AbstractObjectB CreateObjectB()
    {
      return new AnotherConcreteObjectB();
    }
}
```

Below is the actual code example that implements this design pattern.

### Code Example

```c#
public abstract class HotelDiningMenuFactory
{
    public abstract IAppetizer CreateAppetizer();
    public abstract IEntree CreateEntree();
    public abstract IDessert CreateDessert();
}

public class RegularMenuFactory : HotelDiningMenuFactory
{
    public override IAppetizer CreateAppetizer()
    {
      return new RegularAppetizer();
    }

    public override IEntree CreateEntree()
    {
      return new RegularEntree();
    }

    public override IDessert CreateDessert()
    {
      return new RegularDessert();
    }
}

public class ExquisiteMenuFactory : HotelDiningMenuFactory
{
    public override IAppetizer CreateAppetizer()
    {
      return new ExquisiteAppetizer();
    }
    
    public override IEntree CreateEntree()
    {
      return new ExquisiteEntree();
    }

    public override IDessert CreateDessert()
    {
      return new ExquisiteDessert();
    }
}
```

As we can see from above, there's one abstract class named `HotelDiningMenuFactory` that defines creation of dining menus. Concrete factories named `RegularDiningMenuFactory` and `ExquisiteDiningMenuFactory` implement `HotelDiningMenuFactory` by overriding abstract creational methods and returning actual objects (in our example, the appetizer, entree, and desert.)

### Why would we use this pattern?

From the example, we can tell that the `HotelDiningMenuFactory` ultimately does the same thing. It creates three courses: appetizer, entree, and dessert. This dining format will apply to all the course menus -- whether it's a cheap or an expensive dining menu, what gets returned are the three courses.

By creating an abstract factory that concrete factories can inherit from, we can assure that all dining menus that inherit from the same `HotelDiningMenuFactory` will use the same methods to create different courses and assure that they return the expected courses. By this, I mean that there won't be a dining course that inherits from the abstract factory and say "I'll create an appetizer!" and then end up creating something else, say, like a boba. (Is boba a dessert? Or is it something like the air that we should consistently breathe in?)

From the fact that this pattern does introduce a coupling between the abstract class and the concrete classes, there comes one disadvantage for using this pattern -- when we want to add another course or get rid of, say, the appetizer course. Since this pattern is inherently difficult to support new kinds of products, we would need to make a necessary change on the abstract class and ALL of its concrete classes which could be challenging.

To sum up, where this abstract factory pattern will come handy is: When you are sure about the rough things that you want to do and when you expect them to not (or rarely) change. Three-course dining is a good example.

Happy new year!
###### 12/30/2019

### [Go back](https://www.skylar.page)
