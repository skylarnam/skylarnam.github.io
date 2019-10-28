# Decorator Pattern

> The decorator pattern allows to add a new functionality to an existing object without having to necessarily change its structure.

The decorator pattern is comprised of these four pieces:

1. `interface` of a component
2. `class` of a component that implements the interface, called a **concrete component**
3. `abstract class` that implements the interface that will be the **decorator**
4. Another `class` that implements the decorator, called a **concrete decorator**

### Implementation code

```c#
public interface Component
{
    int Operation();
}

public class ConcreteComponent : Component
{
    public int Operation()
    {
        return 0;
    }
}

public abstract class Decorator : Component
{
    private Component component;

    public Decorator(Component component)
    {
        this.component = component;
    }

    public virtual int Operation()
    {
        return this.component.Operation();
    }
}

public class ConcreteDecorator : Decorator
{
    public ConcreteDecorator(Component component)
        : base(component)
    {
    }

    public override int Operation()
    {
        return base.Operation();
    }
}
```

Below is the actual code example that implements this design pattern.

### Code Example

```c#
public interface Drink
{
    double CalculatePrice();
}

public class Boba : Drink
{
    public double CalculatePrice()
    {
        return 5.0;
    }
}

public abstract class DrinkDecorator : Drink
{
    private Drink drink;

    public DrinkDecorator(Drink drink)
    {
        this.drink = drink;
    }

    public virtual double CalculatePrice()
    {
        return this.drink.CalculatePrice();
    }
}

public class DiscountBoba : DrinkDecorator
{
    private const double discount = 0.1;

    public DiscountBoba(DrinkDecorator drink)
        : base(drink)
    {
    }

    public override double CalculatePrice()
    {
        var price = base.CalculatePrice();
        return price * (1 - discount);
    }
}
```

As you can see from above, we have an `interface` called `Drink` and a drink called `Boba` implements it.

Say we want to give a discount on a boba drink. An easy way to do so is to go to the `Boba` class and change the method `CalculatePrice()` to return the discounted price instead of the full price. However, that involves a direct modification of the concrete `Boba` class that we probably will not want to do -- it introduces more coupling and a steeper maintenance cost.

That's why we implement another `abstract class` called `DrinkDecorator` that takes in a `Drink`. We can take `Boba` in as a constructor variable and let `DiscountBoba` implement the `DrinkDecorator`. Then, what that class will do becomes pretty easy to understand. We do `base.CalculatePrice()` inside the overridden method inside `DiscountBoba` and we also know that the base will be of type `Drink`, where in this case, will be a `Boba`. We apply the discount on top of the price that is returned from the base class.

By introducing `DrinkDecorator` that decorates `Drink`, we did not have to modify any concrete implementation of `Boba` and could still give a nice 10% discount on it.

Hope this helped!

### [Go back](https://www.skylar.page)