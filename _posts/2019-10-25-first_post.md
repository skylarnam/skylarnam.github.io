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

### Code Example

```c#
public interface IDrink
{
    double CalculatePrice();
}

public class Boba : IDrink
{
    public double CalculatePrice()
    {
        return 5.0;
    }
}

public abstract class DrinkDecorator : IDrink
{
    private IDrink drink;

    public DrinkDecorator(IDrink drink)
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

TBD... More to be added

#### [Go back](https://www.skylar.page)