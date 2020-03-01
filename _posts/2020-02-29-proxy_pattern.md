---
layout: default
title: Proxy Pattern
---

# Proxy Pattern

> A "proxy", which is also known as a surrogate or placeholder, is invented in the first place to provide a substitute for the original object like its literal meaning. It is widely used for many reasons, where two of them are 1. security benefits (since the original object isn't directly manipulated) and 2. reduced cost of creating a full object.

> Since the main purpose of a proxy is to hide the original object and control access to it, it is important to make sure that the proxy verifies the access in a correcty way, through a service or a client.

### Implementation code

```c#
public interface ISomeObject
{
    void DoSomething();
}

public class RealObject : ISomeObject
{
    public void DoSomething()
    {
        Console.WriteLine("This is some expensive work.");
    }
}

public class ProxyObject : ISomeObject
{
    private readonly RealObject realObject;

    public void DoSomething()
    {
        if (this.realObject is null)
        {
            realObject = new RealObject();
        }

        DoSomeValidation();

        realObject.DoSomething();
    }
}
```

Note that we don't create the real object more than once and does some validation before the real object can do anything.
Below is the actual code example that implements this design pattern.

### Code Example

```c#
public interface IInternetService
{
    Task<ConnectionResult> InitializeAsync();

    Task<ConnectionResult> ConnectAsync();
}

public class InternetService : IInternetService
{
    private bool isInitialized;

    public Task<ConnectionResult> InitializeAsync()
    {
        var success = true;
        if (!this.isInitialized)
        {
            try
            {
                DoSomeValidation();
                this.isInitialized = true;
            }
            catch
            {
                success = false;
            }
        }

        // ConnectionResult will likely have some other values in addition to "success."
        return Task.FromResult(new ConnectionResult(success));
    }

    public Task<ConnectionResult> ConnectAsync()
    {
        VerifyInitialized();
        
        return Execute(paramA, paramB, paramc);
    }

    private void DoSomeValidation()
    {
        // Does some validation here.
    }

    private void VerifyInitialized()
    {
        if (!.this.isInitialized)
        {
            throw new InvalidOperationException($"{nameof(InternetService)} has to be initialized before attempting to initiate a connection.");
        }
    }

    private async Task<ConnectionResult> Execute(object paramA, object paramB, object paramC)
    {
        // Does an actual execution with all the parameters.
    }
}

public class InternetServiceProxy : IInternetService
{
    private readonly IInternetConnectionService connectionClient;

    public InternetServiceProxy()
    {
        this.connectionClient = CreateInternetConnectionServiceClient();
    }

    public Task<ConnectionResult> InitializeAsync() => this.connectionClient.Handle(someParams));

    public Task<ConnectionResult> ConnectAsync() => this.connectionClient.Handle(someOtherParams));
}
```

As you can see from the example above, the actual object does a pretty heavy work. Also, the proxy itself doesn't do much here; it delegates most of the validation and connection to a real object using an `IInternetConnectionService` client. Here, the proxy is intentionally created to be light and quick to use.

### Why would we use this pattern?

I already mentioned this, but there are two main reasons that we'd want to use the proxy design pattern:

1. An additional layer of security is needed to provide a controlled access (protection proxy).
2. The original object is too complex or heavy and a proxy will alleviate a lot of that. (Virutal proxy)

There are other kinds of proxies as well as a remote proxy. **JsonRPC** is a good example. The website ([JsonRPC Specification](https://www.jsonrpc.org/specification)) describes its product well: JSON-RPC is a stateless, light-weight remote procedure call (RPC) protocol. Primarily this specification defines several data structures and the rules around their processing. It is transport agnostic in that the concepts can be used within the same process, over sockets, over http, or in many various message passing environments.
###### 2/29/2020

### [Go back](https://www.skypar.page)