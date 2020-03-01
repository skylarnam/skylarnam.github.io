---
layout: default
title: Publish-Subscribe Pattern
---

# Publish-Subscribe Pattern

> A publish-subscribe pattern, commonly called as pub-sub pattern, allows one class to subscribe for an event and the other to publish the event where the two classes have no direct messaging. Using this pattern, they can establish a one-way connection very specific to the event that's being sent.

> This design pattern gets very useful when the one who's subscribing to the event needs to perform something **remotely** as soon as it receives a signal that what it's looking for.

### Implementation code

```c#
public interface IPublishSubscribeService
{
    void Publish();

    void Subscribe();

    void Unsubscribe();
}

public class PublishSubscribeService : IPublishSubscribeService
{
    public void Publish()
    {
        // Invokes the event to the listeners.
    }

    public void Subscribe()
    {
        // Adds a new listener to a specific event.
    }

    public void Unsubscribe()
    {
        // Removes the listner to a specific event.
    }
}
```

Below is the actual code example that implements this design pattern.

### Code Example

```c#
public interface IPublishSubscribeService
{
    void Publish<T>(T @event)
        where T : PublishSubscribeEvent;

    void Subscribe<T>(Action<T> listener)
        where T : PublishSubscribeEvent;

    void Unsubscribe<T>(Action<T> listener)
        where T : PublishSubscribeEvent;
}

public class PublishSubscribeService : IPublishSubscribeService
{
    private readonly IDictionary<Type, ISet<object>> events = new Dictionary<Type, ISet<object>>();
    private readonly ReaderWriterLockSlim lock = new ReaderWriterLockSlim();
    
    public void Publish<T>(T @event)
        where T : PublishSubscribeEvent
    {
        this.lock.EnterReadLock();

        try
        {
            if (this.events.TryGetValue(typeof(T), out var listeners))
            {
                foreach (var listener in listeners)
                {
                    if (listener is Action<T> targetListener)
                    {
                        targetListener.Invoke(@event);
                    }
                }
            }
        }
        finally
        {
            this.lock.ExitReadLock();
        }
    }

    public void Subscribe<T>(Action<T> listener)
        where T : PublishSubscribeEvent
    {
        this.lock.EnterUpgradeableReadLock();

        try
        {
            this.lock.EnterWriteLock();

            try
            {
                if (this.events.TryGetValue(typeof(T), out var listeners))
                {
                    listeners.Add(listener);
                }
                else
                {
                    this.events.Add(typeof(T), new HashSet<object>() { listener });
                }
            }
            finally
            {
                this.lock.ExitWriteLock();
            }
        }
        finally
        {
            this.lock.ExitUpgradeableReadLock();
        }
    }

    public void Unsubscribe<T>(Action<T> listener)
        where T : PublishSubscribeEvent
    {
        this.lock.EnterUpgradeableReadLock();
        
        try
        {
            if (this.events.TryGetValue(typeof(T), out var listeners))
            {
                this.lock.EnterWriteLock();

                try
                {
                    listeners.Remove(listener);
                }
                finally
                {
                    this.lock.ExitWriteLock();
                }
            }
        }
        finally
        {
            this.lock.ExitUpgradeableReadLock();
        }
    }
}
```

The real-world example above shows how to safely call `Publish<T>(T @event)`, `Subscribe<T>(Action<T> listener)`, and `Unsubscribe<T>(Action<T> listener)` where T is a `PublishSubscribeEvent`. The service user will then only have to create a new event class that inherits from a `PublishSubscribeEvent` with parameters needed to publish an event or subscribe/unsubscribe from an event.

A `ReaderWriterLockSlim` is used here to prevent unwanted side effects such as deadlocks. It's actually such a powerful class with nice features and even provides a `UpgradeableReadLock` that you can use to first put a lock on read which could later be upgraded to a write lock. You can use this in other situations as well: [ReaderWriterLockSlim Documentation](https://docs.microsoft.com/en-us/dotnet/api/system.threading.readerwriterlockslim?view=netframework-4.8) I'm going to make a new blog post just to describe how cool it is. Stay tuned!
###### 2/29/2020

### [Go back](https://www.skylar.page)