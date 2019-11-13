---
title: 将 WCF 双工服务迁移到 WCF 开发人员的 gRPC-gRPC
description: 了解如何将各种形式的 WCF 双工服务迁移到 gRPC 流式处理服务。
author: markrendle
ms.date: 09/02/2019
ms.openlocfilehash: 1702c9f7659f056af9009e81847f28c6e65b277c
ms.sourcegitcommit: 337bdc5a463875daf2cc6883e5a2da97d56f5000
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "73841479"
---
# <a name="migrate-wcf-duplex-services-to-grpc"></a><span data-ttu-id="1952d-103">将 WCF 双工服务迁移到 gRPC</span><span class="sxs-lookup"><span data-stu-id="1952d-103">Migrate WCF duplex services to gRPC</span></span>

<span data-ttu-id="1952d-104">现在已准备好基本概念，本节将介绍更复杂的*流式处理*gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="1952d-104">Now that the basic concepts are in place, this section will look at the more complicated *streaming* gRPC services.</span></span>

<span data-ttu-id="1952d-105">可以通过多种方式在 Windows Communication Foundation （WCF）中使用双工服务。</span><span class="sxs-lookup"><span data-stu-id="1952d-105">There are multiple ways to use duplex services in Windows Communication Foundation (WCF).</span></span> <span data-ttu-id="1952d-106">某些服务由客户端启动，然后从服务器流式传输数据。</span><span class="sxs-lookup"><span data-stu-id="1952d-106">Some services are initiated by the client and then they stream data from the server.</span></span> <span data-ttu-id="1952d-107">其他全双工服务可能涉及更多的双向通信，例如 WCF 文档中的经典 "计算器" 示例。</span><span class="sxs-lookup"><span data-stu-id="1952d-107">Other full-duplex services might involve more ongoing two-way communication like the classic "Calculator" example from the WCF documentation.</span></span> <span data-ttu-id="1952d-108">本章将使用两个可能的 WCF "股票行情" 实现并将其迁移到 gRPC：一个使用 server 流式处理 RPC，另一个使用双向流式处理 RPC。</span><span class="sxs-lookup"><span data-stu-id="1952d-108">This chapter will take two possible WCF "Stock Ticker" implementations and migrate them to gRPC: one using a server streaming RPC, and the other one using a bidirectional streaming RPC.</span></span>

## <a name="server-streaming-rpc"></a><span data-ttu-id="1952d-109">服务器流式处理 RPC</span><span class="sxs-lookup"><span data-stu-id="1952d-109">Server streaming RPC</span></span>

<span data-ttu-id="1952d-110">在[示例 SIMPLESTOCKTICKER WCF 解决方案](https://github.com/dotnet-architecture/grpc-for-wcf-developers/tree/master/SimpleStockTickerSample/wcf/SimpleStockTicker) *SimpleStockPriceTicker*中，有一个双工服务，其中客户端开始与股票符号列表建立连接，而服务器使用*回调接口*在更新可用时发送更新。</span><span class="sxs-lookup"><span data-stu-id="1952d-110">In the [sample SimpleStockTicker WCF solution](https://github.com/dotnet-architecture/grpc-for-wcf-developers/tree/master/SimpleStockTickerSample/wcf/SimpleStockTicker), *SimpleStockPriceTicker*, there's a duplex service where the client starts the connection with a list of stock symbols, and the server uses the *callback interface* to send updates as they become available.</span></span> <span data-ttu-id="1952d-111">客户端实现该接口以响应来自服务器的调用。</span><span class="sxs-lookup"><span data-stu-id="1952d-111">The client implements that interface to respond to calls from the server.</span></span>

### <a name="the-wcf-solution"></a><span data-ttu-id="1952d-112">WCF 解决方案</span><span class="sxs-lookup"><span data-stu-id="1952d-112">The WCF solution</span></span>

<span data-ttu-id="1952d-113">WCF 解决方案实现为 .NET Framework 4.x 控制台应用程序中的自承载 Wcf-nettcp 服务器。</span><span class="sxs-lookup"><span data-stu-id="1952d-113">The WCF solution is implemented as a self-hosted NetTCP server in a .NET Framework 4.x console application.</span></span>

#### <a name="the-servicecontract"></a><span data-ttu-id="1952d-114">ServiceContract</span><span class="sxs-lookup"><span data-stu-id="1952d-114">The ServiceContract</span></span>

```csharp
[ServiceContract(SessionMode = SessionMode.Required, CallbackContract = typeof(ISimpleStockTickerCallback))]
public interface ISimpleStockTickerService
{
    [OperationContract(IsOneWay = true)]
    void Subscribe(string[] symbols);
}
```

<span data-ttu-id="1952d-115">服务具有不具有返回类型的单个方法，因为它将使用回调接口 `ISimpleStockTickerCallback` 将数据实时发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="1952d-115">The service has a single method with no return type, because it will be using the callback interface `ISimpleStockTickerCallback` to send data to the client in real time.</span></span>

#### <a name="the-callback-interface"></a><span data-ttu-id="1952d-116">回调接口</span><span class="sxs-lookup"><span data-stu-id="1952d-116">The callback interface</span></span>

```csharp
[ServiceContract]
public interface ISimpleStockTickerCallback
{
    [OperationContract(IsOneWay = true)]
    void Update(string symbol, decimal price);
}
```

<span data-ttu-id="1952d-117">这些接口的实现可以在解决方案中找到，还可以在可提供测试数据的虚假外部依赖关系中找到。</span><span class="sxs-lookup"><span data-stu-id="1952d-117">The implementations of these interfaces can be found in the solution, along with faked external dependencies to provide test data.</span></span>

### <a name="grpc-streaming"></a><span data-ttu-id="1952d-118">gRPC 流式处理</span><span class="sxs-lookup"><span data-stu-id="1952d-118">gRPC streaming</span></span>

<span data-ttu-id="1952d-119">处理实时数据的 gRPC 方式有所不同。</span><span class="sxs-lookup"><span data-stu-id="1952d-119">The gRPC way of handling real-time data is different.</span></span> <span data-ttu-id="1952d-120">从客户端到服务器的调用可以创建一个持久性流，该流可对异步到达的消息进行监视。</span><span class="sxs-lookup"><span data-stu-id="1952d-120">A call from client to server can create a persistent stream, which can be monitored for messages arriving asynchronously.</span></span> <span data-ttu-id="1952d-121">尽管存在差异，但流可以是处理此数据的更直观的方式，并且在现代编程中更为相关，重点在于 LINQ、反应流、函数编程等。</span><span class="sxs-lookup"><span data-stu-id="1952d-121">Despite the difference, streams can be a more intuitive way of dealing with this data and are more relevant in modern programming with the emphasis on LINQ, Reactive Streams, functional programming, and so on.</span></span>

<span data-ttu-id="1952d-122">服务定义需要两条消息：一个用于请求，另一个用于流。</span><span class="sxs-lookup"><span data-stu-id="1952d-122">The service definition needs two messages: one for the request, and one for the stream.</span></span> <span data-ttu-id="1952d-123">服务在其 `return` 声明中使用 `stream` 关键字返回 `StockTickerUpdate` 消息的流。</span><span class="sxs-lookup"><span data-stu-id="1952d-123">The service returns a stream of the `StockTickerUpdate` message using the `stream` keyword in its `return` declaration.</span></span> <span data-ttu-id="1952d-124">建议将 `Timestamp` 添加到更新，以显示价格变化的准确时间。</span><span class="sxs-lookup"><span data-stu-id="1952d-124">It's recommended that you add a `Timestamp` to the update to show the exact time the price changed.</span></span>

#### <a name="simple_stock_tickerproto"></a><span data-ttu-id="1952d-125">simple_stock_ticker proto</span><span class="sxs-lookup"><span data-stu-id="1952d-125">simple_stock_ticker.proto</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "TraderSys.SimpleStockTickerServer.Protos";

import "google/protobuf/timestamp.proto";

package SimpleStockTickerServer;

service SimpleStockTicker {
  rpc Subscribe (SubscribeRequest) returns (stream StockTickerUpdate);
}

message SubscribeRequest {
  repeated string symbols = 1;
}

message StockTickerUpdate {
  string symbol = 1;
  int32 price_cents = 2;
  google.protobuf.Timestamp time = 3;
}
```

### <a name="implement-the-simplestockticker"></a><span data-ttu-id="1952d-126">实现 SimpleStockTicker</span><span class="sxs-lookup"><span data-stu-id="1952d-126">Implement the SimpleStockTicker</span></span>

<span data-ttu-id="1952d-127">通过将 `TraderSys.StockMarket` 类库中的三个类复制到目标解决方案中的新 .NET Standard 类库，重用 WCF 项目中的虚设 `StockPriceSubscriber`。</span><span class="sxs-lookup"><span data-stu-id="1952d-127">Reuse the fake `StockPriceSubscriber` from the WCF project by copying the three classes from the `TraderSys.StockMarket` class library into a new .NET Standard class library in the target solution.</span></span> <span data-ttu-id="1952d-128">为了更好地遵循最佳做法，请添加一个 `Factory` 类型来创建它的实例，并向 ASP.NET Core 的依赖项注入服务注册 `IStockPriceSubscriberFactory`。</span><span class="sxs-lookup"><span data-stu-id="1952d-128">To better follow best practices, add a `Factory` type to create instances of it and register the `IStockPriceSubscriberFactory` with ASP.NET Core's dependency injection services.</span></span>

#### <a name="the-factory-implementation"></a><span data-ttu-id="1952d-129">工厂实现</span><span class="sxs-lookup"><span data-stu-id="1952d-129">The factory implementation</span></span>

```csharp
public interface IStockPriceSubscriberFactory
{
    IStockPriceSubscriber GetSubscriber(string[] symbols);
}

public class StockPriceSubscriberFactory : IStockPriceSubscriberFactory
{
    public IStockPriceSubscriber GetSubscriber(string[] symbols)
    {
        return new StockPriceSubscriber(symbols);
    }
}
```

#### <a name="registering-the-factory"></a><span data-ttu-id="1952d-130">注册工厂</span><span class="sxs-lookup"><span data-stu-id="1952d-130">Registering the factory</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton<IStockPriceSubscriberFactory, StockPriceSubscriberFactory>();
}
```

<span data-ttu-id="1952d-131">现在，此类可用于实现 gRPC StockTicker 服务。</span><span class="sxs-lookup"><span data-stu-id="1952d-131">Now, this class can be used to implement the gRPC StockTicker service.</span></span>

#### <a name="stocktickerservicecs"></a><span data-ttu-id="1952d-132">StockTickerService.cs</span><span class="sxs-lookup"><span data-stu-id="1952d-132">StockTickerService.cs</span></span>

```csharp
public class StockTickerService : Protos.SimpleStockTicker.SimpleStockTickerBase
{
    private readonly IStockPriceSubscriberFactory _subscriberFactory;

    public StockTickerService(IStockPriceSubscriberFactory subscriberFactory)
    {
        _subscriberFactory = subscriberFactory;
    }

    public override async Task Subscribe(SubscribeRequest request, IServerStreamWriter<StockTickerUpdate> responseStream, ServerCallContext context)
    {
        var subscriber = _subscriberFactory.GetSubscriber(request.Symbols.ToArray());

        subscriber.Update += async (sender, args) =>
            await WriteUpdateAsync(responseStream, args.Symbol, args.Price);

        await AwaitCancellation(context.CancellationToken);
    }

    private async Task WriteUpdateAsync(IServerStreamWriter<StockTickerUpdate> stream, string symbol, decimal price)
    {
        try
        {
            await stream.WriteAsync(new StockTickerUpdate
            {
                Symbol = symbol,
                PriceCents = (int)(price * 100),
                Time = Timestamp.FromDateTimeOffset(DateTimeOffset.UtcNow)
            });
        }
        catch (Exception e)
        {
            // Handle any errors due to broken connection etc.
            _logger.LogError($"Failed to write message: {e.Message}");
        }
    }

    private static Task AwaitCancellation(CancellationToken token)
    {
        var completion = new TaskCompletionSource<object>();
        token.Register(() => completion.SetResult(null));
        return completion.Task;
    }
}
```

<span data-ttu-id="1952d-133">正如您所看到的，尽管 `.proto` 文件中的声明表明方法 "返回" `StockTickerUpdate` 消息的流，实际上它返回 vanilla `Task`。</span><span class="sxs-lookup"><span data-stu-id="1952d-133">As you can see, although the declaration in the `.proto` file says the method "returns" a stream of `StockTickerUpdate` messages, in fact it returns a vanilla `Task`.</span></span> <span data-ttu-id="1952d-134">创建流的作业由生成的代码和 gRPC 运行时库处理，后者提供 `IServerStreamWriter<StockTickerUpdate>` 的响应流，可供使用。</span><span class="sxs-lookup"><span data-stu-id="1952d-134">The job of creating the stream is handled by the generated code and the gRPC runtime libraries, which provide the `IServerStreamWriter<StockTickerUpdate>` response stream, ready to use.</span></span>

<span data-ttu-id="1952d-135">不同于 WCF 双工服务，其中服务类的实例在连接打开时保持活动状态，gRPC 服务使用返回的任务使服务保持活动状态。</span><span class="sxs-lookup"><span data-stu-id="1952d-135">Unlike a WCF duplex service, where the instance of the service class is kept alive while the connection is open, the gRPC service uses the returned Task to keep the service alive.</span></span> <span data-ttu-id="1952d-136">直到连接关闭，该任务才会完成。</span><span class="sxs-lookup"><span data-stu-id="1952d-136">The Task shouldn't complete until the connection is closed.</span></span>

<span data-ttu-id="1952d-137">服务可以通过使用 `ServerCallContext`中的 `CancellationToken` 来确定客户端何时关闭了连接。</span><span class="sxs-lookup"><span data-stu-id="1952d-137">The service can tell when the client has closed the connection by using the `CancellationToken` from the `ServerCallContext`.</span></span> <span data-ttu-id="1952d-138">`AwaitCancellation`使用简单的静态方法来创建一个在取消标记时完成的任务。</span><span class="sxs-lookup"><span data-stu-id="1952d-138">A simple static method, `AwaitCancellation`, is used to create a Task that completes when the token is canceled.</span></span>

<span data-ttu-id="1952d-139">在 `Subscribe` 方法中，获取 `StockPriceSubscriber` 并添加写入响应流的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="1952d-139">In the `Subscribe` method, then, get a `StockPriceSubscriber` and add an event handler that writes to the response stream.</span></span> <span data-ttu-id="1952d-140">然后，等待连接关闭，然后在立即释放 `subscriber` 以防止它尝试将数据写入到关闭的流。</span><span class="sxs-lookup"><span data-stu-id="1952d-140">Then wait for the connection to be closed, before immediately disposing the `subscriber` to prevent it trying to write data to the closed stream.</span></span>

<span data-ttu-id="1952d-141">`WriteUpdateAsync` 方法有一个 `try`/`catch` 块，用于处理将消息写入流时可能发生的任何错误。</span><span class="sxs-lookup"><span data-stu-id="1952d-141">The `WriteUpdateAsync` method has a `try`/`catch` block to handle any errors that might happen when writing a message to the stream.</span></span> <span data-ttu-id="1952d-142">这是跨网络的持久连接中的一个重要考虑因素，这可能会在任何毫秒中断，不管是有意发生还是因某个原因发生故障。</span><span class="sxs-lookup"><span data-stu-id="1952d-142">This is an important consideration in persistent connections over networks, which could be broken at any millisecond, whether intentionally or because of a failure somewhere.</span></span>

### <a name="using-the-stocktickerservice-from-a-client-application"></a><span data-ttu-id="1952d-143">使用客户端应用程序中的 StockTickerService</span><span class="sxs-lookup"><span data-stu-id="1952d-143">Using the StockTickerService from a client application</span></span>

<span data-ttu-id="1952d-144">按照上一部分中的相同步骤，从 `.proto` 文件创建可共享的客户端类库。</span><span class="sxs-lookup"><span data-stu-id="1952d-144">Follow the same steps in the previous section to create a shareable client class library from the `.proto` file.</span></span> <span data-ttu-id="1952d-145">在示例中，有一个 .NET Core 3.0 控制台应用程序，它演示了如何使用客户端。</span><span class="sxs-lookup"><span data-stu-id="1952d-145">In the sample, there's a .NET Core 3.0 console application that demonstrates how to use the client.</span></span>

#### <a name="example-programcs"></a><span data-ttu-id="1952d-146">示例 Program.cs</span><span class="sxs-lookup"><span data-stu-id="1952d-146">Example Program.cs</span></span>

```csharp
class Program
{
    static async Task Main(string[] args)
    {
        using var channel = GrpcChannel.ForAddress("https://localhost:5001");
        var client = new SimpleStockTicker.SimpleStockTickerClient(channel);

        var request = new SubscribeRequest();
        request.Symbols.AddRange(args);

        using var stream = client.Subscribe(request);

        var tokenSource = new CancellationTokenSource();

        var task = DisplayAsync(stream.ResponseStream, tokenSource.Token);

        WaitForExitKey();

        tokenSource.Cancel();
        await task;
    }
}
```

<span data-ttu-id="1952d-147">在这种情况下，生成的客户端上的 `Subscribe` 方法不是异步的。</span><span class="sxs-lookup"><span data-stu-id="1952d-147">In this case, the `Subscribe` method on the generated client isn't asynchronous.</span></span> <span data-ttu-id="1952d-148">流将立即创建并可供使用，因为其 `MoveNext` 方法是异步的，并且首次调用时，它将不会完成，直到连接处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="1952d-148">The stream is created and usable right away, because its `MoveNext` method is asynchronous and the first time it's called it won't complete until the connection is alive.</span></span>

<span data-ttu-id="1952d-149">流传递到 async `DisplayAsync` 方法;然后，应用程序会等待用户按某个键，然后取消 `DisplayAsync` 方法，并等待该任务在退出前完成。</span><span class="sxs-lookup"><span data-stu-id="1952d-149">The stream is passed to an async `DisplayAsync` method; the application then waits for the user to press a key, then cancels the `DisplayAsync` method and waits for the task to complete before exiting.</span></span>

> [!NOTE]
> <span data-ttu-id="1952d-150">在 `Main` 方法退出时， C#此代码将使用新的 8 "using 声明" 语法来释放流和信道。</span><span class="sxs-lookup"><span data-stu-id="1952d-150">This code is using the new C# 8 "using declaration" syntax to dispose of the stream and the channel when the `Main` method exits.</span></span> <span data-ttu-id="1952d-151">这是一小小的更改，但也有一小部分会减少缩进和空行。</span><span class="sxs-lookup"><span data-stu-id="1952d-151">It's a small change, but a nice one that reduces indentations and empty lines.</span></span>

#### <a name="consume-the-stream"></a><span data-ttu-id="1952d-152">使用流</span><span class="sxs-lookup"><span data-stu-id="1952d-152">Consume the stream</span></span>

<span data-ttu-id="1952d-153">WCF 使用回调接口允许服务器直接在客户端上调用方法。</span><span class="sxs-lookup"><span data-stu-id="1952d-153">WCF used callback interfaces to allow the server to call methods directly on the client.</span></span> <span data-ttu-id="1952d-154">gRPC 流的工作方式不同。</span><span class="sxs-lookup"><span data-stu-id="1952d-154">gRPC streams work differently.</span></span> <span data-ttu-id="1952d-155">客户端循环访问返回的流并处理消息，就像它们是从返回 `IEnumerable`的本地方法返回的一样。</span><span class="sxs-lookup"><span data-stu-id="1952d-155">The client iterates over the returned stream and processes messages, just as though they were returned from a local method returning an `IEnumerable`.</span></span>

<span data-ttu-id="1952d-156">`IAsyncStreamReader<T>` 类型的工作方式非常类似于 `IEnumerator<T>`：有一个 `MoveNext` 方法，只要有更多数据，就会返回 true，并且返回最新值的 `Current` 属性。</span><span class="sxs-lookup"><span data-stu-id="1952d-156">The `IAsyncStreamReader<T>` type works much like an `IEnumerator<T>`: there's a `MoveNext` method that will return true as long as there's more data, and a `Current` property that returns the latest value.</span></span> <span data-ttu-id="1952d-157">唯一的区别是 `MoveNext` 方法返回 `Task<bool>` 而不只是 `bool`。</span><span class="sxs-lookup"><span data-stu-id="1952d-157">The only difference is that the `MoveNext` method returns a `Task<bool>` instead of just a `bool`.</span></span> <span data-ttu-id="1952d-158">`ReadAllAsync` 扩展方法在可与新的 `await foreach` 语法C#一起使用的标准 8 `IAsyncEnumerable` 中包装流。</span><span class="sxs-lookup"><span data-stu-id="1952d-158">The `ReadAllAsync` extension method wraps the stream in a standard C# 8 `IAsyncEnumerable` that can be used with the new `await foreach` syntax.</span></span>

```csharp
static async Task DisplayAsync(IAsyncStreamReader<StockTickerUpdate> stream, CancellationToken token)
{
    try
    {
        await foreach (var update in stream.ReadAllAsync(token))
        {
            Console.WriteLine($"{update.Symbol}: {update.Price}");
        }
    }
    catch (RpcException e) when (e.StatusCode == StatusCode.Cancelled)
    {
        return;
    }
    catch (OperationCanceledException)
    {
        Console.WriteLine("Finished.");
    }
}
```

> [!TIP]
> <span data-ttu-id="1952d-159">本章末尾的[客户端库](client-libraries.md#iobservable)部分介绍如何添加扩展方法和类，以便使用反应性编程模式的开发人员将 `IAsyncStreamReader<T>` 包装到 `IObservable<T>` 中。</span><span class="sxs-lookup"><span data-stu-id="1952d-159">The section on [client libraries](client-libraries.md#iobservable) at the end of this chapter looks at how to add an extension method and classes to wrap `IAsyncStreamReader<T>` in an `IObservable<T>` for developers using reactive programming patterns.</span></span>

<span data-ttu-id="1952d-160">同样，请注意在此处捕获异常，因为可能存在网络故障，以及由于代码正在使用 <xref:System.Threading.CancellationToken> 来中断循环而无法引发的 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="1952d-160">Again, be careful to catch exceptions here because of the possibility of network failure, as well as the <xref:System.OperationCanceledException> that will inevitably be thrown because the code is using a <xref:System.Threading.CancellationToken> to break the loop.</span></span> <span data-ttu-id="1952d-161">`RpcException` 类型有很多有关 gRPC 运行时错误的有用信息，包括 `StatusCode`。</span><span class="sxs-lookup"><span data-stu-id="1952d-161">The `RpcException` type has a lot of useful information about gRPC runtime errors, including the `StatusCode`.</span></span> <span data-ttu-id="1952d-162">有关详细信息，请参阅[第4章中的*错误处理*](error-handling.md)。</span><span class="sxs-lookup"><span data-stu-id="1952d-162">For more information, see [*Error handling* in Chapter 4](error-handling.md).</span></span>

## <a name="bidirectional-streaming"></a><span data-ttu-id="1952d-163">双向流式处理</span><span class="sxs-lookup"><span data-stu-id="1952d-163">Bidirectional streaming</span></span>

<span data-ttu-id="1952d-164">WCF 全双工服务允许在两个方向上进行异步实时消息传送。</span><span class="sxs-lookup"><span data-stu-id="1952d-164">A WCF full-duplex service allows for asynchronous, real-time messaging in both directions.</span></span> <span data-ttu-id="1952d-165">在服务器流式处理示例中，客户端启动一个请求，然后接收一个更新流。</span><span class="sxs-lookup"><span data-stu-id="1952d-165">In the server streaming example, the client starts a request, then receives a stream of updates.</span></span> <span data-ttu-id="1952d-166">该服务的更好版本可让客户端在列表中添加和删除股票，而不必停止并创建新的订阅。</span><span class="sxs-lookup"><span data-stu-id="1952d-166">A better version of that service would allow the client to add and remove stocks from the list without having to stop and create a new subscription.</span></span> <span data-ttu-id="1952d-167">已在[FullStockTicker 示例解决方案](https://github.com/dotnet-architecture/grpc-for-wcf-developers/tree/master/FullStockTickerSample/wcf/FullStockTicker)中实现了该功能。</span><span class="sxs-lookup"><span data-stu-id="1952d-167">That functionality has been implemented in the [FullStockTicker sample solution](https://github.com/dotnet-architecture/grpc-for-wcf-developers/tree/master/FullStockTickerSample/wcf/FullStockTicker).</span></span>

<span data-ttu-id="1952d-168">`IFullStockTickerService` 接口提供了三种方法：</span><span class="sxs-lookup"><span data-stu-id="1952d-168">The `IFullStockTickerService` interface provides three methods:</span></span>

- <span data-ttu-id="1952d-169">`Subscribe` 开始连接。</span><span class="sxs-lookup"><span data-stu-id="1952d-169">`Subscribe` starts the connection.</span></span>
- <span data-ttu-id="1952d-170">`AddSymbol` 添加要观看的股票符号。</span><span class="sxs-lookup"><span data-stu-id="1952d-170">`AddSymbol` adds a stock symbol to watch.</span></span>
- <span data-ttu-id="1952d-171">`RemoveSymbol` 从监视列表中删除符号。</span><span class="sxs-lookup"><span data-stu-id="1952d-171">`RemoveSymbol` removes a symbol from the watched list.</span></span>

```csharp
[ServiceContract(SessionMode = SessionMode.Required, CallbackContract = typeof(IFullStockTickerCallback))]
public interface IFullStockTickerService
{
    [OperationContract(IsOneWay = true)]
    void Subscribe();

    [OperationContract(IsOneWay = true)]
    void AddSymbol(string symbol);

    [OperationContract(IsOneWay = true)]
    void RemoveSymbol(string symbol);
}
```

<span data-ttu-id="1952d-172">回调接口保持不变。</span><span class="sxs-lookup"><span data-stu-id="1952d-172">The callback interface remains the same.</span></span>

<span data-ttu-id="1952d-173">在 gRPC 中实现此模式不是很简单，因为现在有两个数据流，其中一条消息传递到服务器，另一个从服务器到客户端。</span><span class="sxs-lookup"><span data-stu-id="1952d-173">Implementing this pattern in gRPC is less straightforward, because there are now two streams of data with messages being passed: one from client to server, and another from server to client.</span></span> <span data-ttu-id="1952d-174">不能使用多种方法来实现添加和删除操作，但可以使用 `Any` 类型或 `oneof` 关键字在单个流上传递多种类型的消息，如[第3章](protobuf-any-oneof.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="1952d-174">It isn't possible to use multiple methods to implement the Add and Remove operation, but more than one type of message can be passed on a single stream, using either the `Any` type or `oneof` keyword, which were covered in [Chapter 3](protobuf-any-oneof.md).</span></span>

<span data-ttu-id="1952d-175">对于有一组可接受的特定类型的情况，`oneof` 是一种更好的方法。</span><span class="sxs-lookup"><span data-stu-id="1952d-175">For a case where there's a specific set of types that are acceptable, `oneof` is a better way to go.</span></span> <span data-ttu-id="1952d-176">使用可容纳 `AddSymbolRequest` 或 `RemoveSymbolRequest`的 `ActionMessage`。</span><span class="sxs-lookup"><span data-stu-id="1952d-176">Use an `ActionMessage` that can hold either an `AddSymbolRequest` or a `RemoveSymbolRequest`.</span></span>

```protobuf
message ActionMessage {
  oneof action {
    AddSymbolRequest add = 1;
    RemoveSymbolRequest remove = 2;
  }
}

message AddSymbolRequest {
  string symbol = 1;
}

message RemoveSymbolRequest {
  string symbol = 1;
}
```

<span data-ttu-id="1952d-177">声明一个双向流式处理服务，该服务采用 `ActionMessage` 的消息流。</span><span class="sxs-lookup"><span data-stu-id="1952d-177">Declare a bi-directional streaming service that takes a stream of `ActionMessage` messages.</span></span>

```protobuf
service FullStockTicker {
  rpc Subscribe (stream ActionMessage) returns (stream StockTickerUpdate);
}
```

<span data-ttu-id="1952d-178">此服务的实现与前面的示例类似，不同之处在于 `Subscribe` 方法的第一个参数现在是 `IAsyncStreamReader<ActionMessage>`，可用于处理 `Add` 和 `Remove` 请求。</span><span class="sxs-lookup"><span data-stu-id="1952d-178">The implementation for this service is similar to the previous example, except the first parameter of the `Subscribe` method is now an `IAsyncStreamReader<ActionMessage>`, which can be used to handle the `Add` and `Remove` requests.</span></span>

```csharp
public override async Task Subscribe(IAsyncStreamReader<ActionMessage> requestStream, IServerStreamWriter<StockTickerUpdate> responseStream, ServerCallContext context)
{
    using var subscriber = _subscriberFactory.GetSubscriber();

    subscriber.Update += async (sender, args) =>
        await WriteUpdateAsync(responseStream, args.Symbol, args.Price);

    var actionsTask = HandleActions(requestStream, subscriber, context.CancellationToken);

    _logger.LogInformation("Subscription started.");
    await AwaitCancellation(context.CancellationToken);

    try { await actionsTask; } catch { /* Ignored */ }

    _logger.LogInformation("Subscription finished.");
}

private async Task WriteUpdateAsync(IServerStreamWriter<StockTickerUpdate> stream, string symbol, decimal price)
{
    try
    {
        await stream.WriteAsync(new StockTickerUpdate
        {
            Symbol = symbol,
            PriceCents = (int)(price * 100),
            Time = Timestamp.FromDateTimeOffset(DateTimeOffset.UtcNow)
        });
    }
    catch (Exception e)
    {
        // Handle any errors due to broken connection etc.
        _logger.LogError($"Failed to write message: {e.Message}");
    }
}

private static Task AwaitCancellation(CancellationToken token)
{
    var completion = new TaskCompletionSource<object>();
    token.Register(() => completion.SetResult(null));
    return completion.Task;
}
```

<span data-ttu-id="1952d-179">GRPC 为我们生成的 `ActionMessage` 类保证只可以设置一个 `Add` 属性和 `Remove` 属性，找出哪一种不 `null` 是查找所使用的消息类型的有效方法，但还有更好的方法。</span><span class="sxs-lookup"><span data-stu-id="1952d-179">The `ActionMessage` class that gRPC has generated for us guarantees that only one of the `Add` and `Remove` properties can be set, and finding which one isn't `null` is a valid way of finding which type of message is used, but there's a better way.</span></span> <span data-ttu-id="1952d-180">代码生成还在 `ActionMessage` 类中创建了一个 `enum ActionOneOfCase`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="1952d-180">The code generation also created an `enum ActionOneOfCase` in the `ActionMessage` class, which looks like this:</span></span>

```csharp
public enum ActionOneofCase {
    None = 0,
    Add = 1,
    Remove = 2,
}
```

<span data-ttu-id="1952d-181">`ActionMessage` 对象上 `ActionCase` 的属性可与 `switch` 语句一起使用，以确定要设置的字段。</span><span class="sxs-lookup"><span data-stu-id="1952d-181">The property `ActionCase` on the `ActionMessage` object can be used with a `switch` statement to determine which field is set.</span></span>

```csharp
private async Task HandleActions(IAsyncStreamReader<ActionMessage> requestStream, IFullStockPriceSubscriber subscriber, CancellationToken token)
{
    await foreach (var action in requestStream.ReadAllAsync(token))
    {
        switch (action.ActionCase)
        {
            case ActionMessage.ActionOneofCase.None:
                _logger.LogWarning("No Action specified.");
                break;
            case ActionMessage.ActionOneofCase.Add:
                subscriber.Add(action.Add.Symbol);
                break;
            case ActionMessage.ActionOneofCase.Remove:
                subscriber.Remove(action.Remove.Symbol);
                break;
            default:
                _logger.LogWarning($"Unknown Action '{action.ActionCase}'.");
                break;
        }
    }
}
```

> [!TIP]
> <span data-ttu-id="1952d-182">`switch` 语句有一个 `default` 用例，在遇到未知的 `ActionOneOfCase` 值时记录警告。</span><span class="sxs-lookup"><span data-stu-id="1952d-182">The `switch` statement has a `default` case that logs a warning if an unknown `ActionOneOfCase` value is encountered.</span></span> <span data-ttu-id="1952d-183">这在指示客户端正在使用更高版本的 `.proto` 文件（该文件添加了更多操作）时很有用。</span><span class="sxs-lookup"><span data-stu-id="1952d-183">This could be useful in indicating that a client is using a later version of the `.proto` file which has added more actions.</span></span> <span data-ttu-id="1952d-184">这是使用 `switch` 比测试已知字段 `null` 更好的原因之一。</span><span class="sxs-lookup"><span data-stu-id="1952d-184">This is one reason why using a `switch` is better than testing for `null` on known fields.</span></span>

### <a name="use-the-fullstocktickerservice-from-a-client-application"></a><span data-ttu-id="1952d-185">使用客户端应用程序中的 FullStockTickerService</span><span class="sxs-lookup"><span data-stu-id="1952d-185">Use the FullStockTickerService from a client application</span></span>

<span data-ttu-id="1952d-186">提供了一个简单的 .NET Core 3.0 WPF 应用程序，用于演示如何使用更复杂的客户端。</span><span class="sxs-lookup"><span data-stu-id="1952d-186">There's a simple .NET Core 3.0 WPF application to demonstrate use of this more complex client.</span></span> <span data-ttu-id="1952d-187">可[在 GitHub 上](https://github.com/dotnet-architecture/grpc-for-wcf-developers/tree/master/FullStockTickerSample/grpc/FullStockTicker)找到完整的应用程序。</span><span class="sxs-lookup"><span data-stu-id="1952d-187">The full application can be found [on GitHub](https://github.com/dotnet-architecture/grpc-for-wcf-developers/tree/master/FullStockTickerSample/grpc/FullStockTicker).</span></span>

<span data-ttu-id="1952d-188">在 `MainWindowViewModel` 类中使用客户端，该类从依赖关系注入中获取 `FullStockTicker.FullStockTickerClient` 类型的实例。</span><span class="sxs-lookup"><span data-stu-id="1952d-188">The client is used in the `MainWindowViewModel` class, which gets an instance of the `FullStockTicker.FullStockTickerClient` type from dependency injection.</span></span>

```csharp
public class MainWindowViewModel : IAsyncDisposable, INotifyPropertyChanged
{
    private readonly FullStockTicker.FullStockTickerClient _client;
    private readonly AsyncDuplexStreamingCall<ActionMessage, StockTickerUpdate> _duplexStream;
    private readonly CancellationTokenSource _cancellationTokenSource;
    private readonly Task _responseTask;
    private string _addSymbol;

    public MainWindowViewModel(FullStockTicker.FullStockTickerClient client)
    {
        _cancellationTokenSource = new CancellationTokenSource();
        _client = client;
        _duplexStream = _client.Subscribe();
        _responseTask = HandleResponsesAsync(_cancellationTokenSource.Token);

        AddCommand = new AsyncCommand(Add, CanAdd);
    }
```

<span data-ttu-id="1952d-189">`client.Subscribe()` 方法返回的对象现在是 gRPC 库类型的一个实例 `AsyncDuplexStreamingCall<TRequest, TResponse>`，它提供了用于将请求发送到服务器的 `RequestStream`，以及用于处理响应的 `ResponseStream`。</span><span class="sxs-lookup"><span data-stu-id="1952d-189">The object returned by the `client.Subscribe()` method is now an instance of the gRPC library type `AsyncDuplexStreamingCall<TRequest, TResponse>`, which provides a `RequestStream` for sending requests to the server, and a `ResponseStream` for handling responses.</span></span>

<span data-ttu-id="1952d-190">从一些 WPF `ICommand` 方法使用请求流来添加和删除符号。</span><span class="sxs-lookup"><span data-stu-id="1952d-190">The request stream is used from some WPF `ICommand` methods to add and remove symbols.</span></span> <span data-ttu-id="1952d-191">对于每个操作，请在 `ActionMessage` 对象上设置相关字段：</span><span class="sxs-lookup"><span data-stu-id="1952d-191">For each operation, set the relevant field on an `ActionMessage` object:</span></span>

```csharp
private async Task Add()
{
    if (CanAdd())
    {
        await _duplexStream.RequestStream.WriteAsync(new ActionMessage {Add = new AddSymbolRequest {Symbol = AddSymbol}});
    }
}

public async Task Remove(PriceViewModel priceViewModel)
{
    await _duplexStream.RequestStream.WriteAsync(new ActionMessage {Remove = new RemoveSymbolRequest {Symbol = priceViewModel.Symbol}});
    Prices.Remove(priceViewModel);
}
```

> [!IMPORTANT]
> <span data-ttu-id="1952d-192">如果对某一消息设置 `oneof` 字段的值，则将自动清除先前设置的所有字段。</span><span class="sxs-lookup"><span data-stu-id="1952d-192">Setting a `oneof` field's value on a message automatically clears any fields that have been previously set.</span></span>

<span data-ttu-id="1952d-193">响应流在 `async` 方法中进行处理，当窗口关闭时，它返回的 `Task` 将被释放。</span><span class="sxs-lookup"><span data-stu-id="1952d-193">The stream of responses is handled in an `async` method, and the `Task` it returns is held to be disposed when the window is closed.</span></span>

```csharp
private async Task HandleResponsesAsync(CancellationToken token)
{
    var stream = _duplexStream.ResponseStream;

    try
    {
        await foreach (var update in stream.ReadAllAsync(token))
        {
            var price = Prices.FirstOrDefault(p => p.Symbol.Equals(update.Symbol));
            if (price == null)
            {
                price = new PriceViewModel(this) {Symbol = update.Symbol, Price = update.PriceCents / 100m};
                Prices.Add(price);
            }
            else
            {
                price.Price = update.PriceCents / 100m;
            }
        }
    }
    catch (OperationCancelledException) { }
}
```

### <a name="client-clean-up"></a><span data-ttu-id="1952d-194">客户端清理</span><span class="sxs-lookup"><span data-stu-id="1952d-194">Client clean-up</span></span>

<span data-ttu-id="1952d-195">当窗口关闭并释放 `MainWindowViewModel` （从 `MainWindow`的 `Closed` 事件）时，建议您正确释放 `AsyncDuplexStreamingCall` 对象。</span><span class="sxs-lookup"><span data-stu-id="1952d-195">When the window is closed and the `MainWindowViewModel` is disposed (from the `Closed` event of `MainWindow`), it's recommended that you properly dispose the `AsyncDuplexStreamingCall` object.</span></span> <span data-ttu-id="1952d-196">具体而言，应调用 `RequestStream` 上的 `CompleteAsync` 方法以正常关闭服务器上的流。</span><span class="sxs-lookup"><span data-stu-id="1952d-196">In particular, the `CompleteAsync` method on the `RequestStream` should be called to gracefully close the stream on the server.</span></span> <span data-ttu-id="1952d-197">下面的示例演示示例视图模型中的 `DisposeAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1952d-197">The following example shows the `DisposeAsync` method from the sample view-model:</span></span>

```csharp
public ValueTask DisposeAsync()
{
    try
    {
        await _duplexStream.RequestStream.CompleteAsync().ConfigureAwait(false);
        await _responseTask.ConfigureAwait(false);
    }
    finally
    {
        _duplexStream.Dispose();
    }
}
```

<span data-ttu-id="1952d-198">关闭请求流使服务器能够及时释放其自己的资源。</span><span class="sxs-lookup"><span data-stu-id="1952d-198">Closing request streams enables the server to dispose of its own resources in a timely manner.</span></span> <span data-ttu-id="1952d-199">这可以提高服务的效率和可伸缩性，并可防止出现异常。</span><span class="sxs-lookup"><span data-stu-id="1952d-199">This improves the efficiency and scalability of services and prevents exceptions.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="1952d-200">[上一页](migrate-request-reply.md)
>[下一页](streaming-versus-repeated.md)</span><span class="sxs-lookup"><span data-stu-id="1952d-200">[Previous](migrate-request-reply.md)
[Next](streaming-versus-repeated.md)</span></span>