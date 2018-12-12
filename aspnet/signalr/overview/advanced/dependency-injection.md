---
uid: signalr/overview/advanced/dependency-injection
title: Injeção de dependência no SignalR | Microsoft Docs
author: MikeWasson
description: Versões de software usado neste tópico, o Visual Studio 2013 .NET 4.5 SignalR versões anteriores de versão 2 deste tópico para obter informações sobre versões anteriores do...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: a14121ae-02cf-4024-8af0-9dd0dc810690
msc.legacyurl: /signalr/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: f5959902b030621a19d912d7db2ea0faf95b7a7c
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287776"
---
<a name="dependency-injection-in-signalr"></a><span data-ttu-id="7d962-103">Injeção de dependência no SignalR</span><span class="sxs-lookup"><span data-stu-id="7d962-103">Dependency Injection in SignalR</span></span>
====================
<span data-ttu-id="7d962-104">por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="7d962-104">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="7d962-105">Versões de software usadas neste tópico</span><span class="sxs-lookup"><span data-stu-id="7d962-105">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="7d962-106">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="7d962-106">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="7d962-107">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="7d962-107">.NET 4.5</span></span>
> - <span data-ttu-id="7d962-108">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="7d962-108">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="7d962-109">Versões anteriores deste tópico</span><span class="sxs-lookup"><span data-stu-id="7d962-109">Previous versions of this topic</span></span>
>
> <span data-ttu-id="7d962-110">Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="7d962-110">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="7d962-111">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="7d962-111">Questions and comments</span></span>
>
> <span data-ttu-id="7d962-112">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="7d962-112">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="7d962-113">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="7d962-113">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="7d962-114">Injeção de dependência é uma maneira de remover dependências embutidas entre objetos, tornando mais fácil para substituir as dependências de um objeto, seja por testes (usando objetos fictícios) ou para alterar o comportamento de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="7d962-114">Dependency injection is a way to remove hard-coded dependencies between objects, making it easier to replace an object's dependencies, either for testing (using mock objects) or to change run-time behavior.</span></span> <span data-ttu-id="7d962-115">Este tutorial mostra como realizar a injeção de dependência em hubs do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7d962-115">This tutorial shows how to perform dependency injection on SignalR hubs.</span></span> <span data-ttu-id="7d962-116">Ele também mostra como usar contêineres IoC com SignalR.</span><span class="sxs-lookup"><span data-stu-id="7d962-116">It also shows how to use IoC containers with SignalR.</span></span> <span data-ttu-id="7d962-117">Um contêiner IoC é uma estrutura geral para injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="7d962-117">An IoC container is a general framework for dependency injection.</span></span>

## <a name="what-is-dependency-injection"></a><span data-ttu-id="7d962-118">O que é a injeção de dependência?</span><span class="sxs-lookup"><span data-stu-id="7d962-118">What is Dependency Injection?</span></span>

<span data-ttu-id="7d962-119">Ignore esta seção se você já estiver familiarizado com a injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="7d962-119">Skip this section if you are already familiar with dependency injection.</span></span>

<span data-ttu-id="7d962-120">*Injeção de dependência* (DI) é um padrão em que os objetos não são responsáveis por criar suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="7d962-120">*Dependency injection* (DI) is a pattern where objects are not responsible for creating their own dependencies.</span></span> <span data-ttu-id="7d962-121">Aqui está um exemplo simples para motivar a injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="7d962-121">Here is a simple example to motivate DI.</span></span> <span data-ttu-id="7d962-122">Suponha que você tenha um objeto que precisa para registrar mensagens.</span><span class="sxs-lookup"><span data-stu-id="7d962-122">Suppose you have an object that needs to log messages.</span></span> <span data-ttu-id="7d962-123">Você pode definir uma interface de registro em log:</span><span class="sxs-lookup"><span data-stu-id="7d962-123">You might define a logging interface:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

<span data-ttu-id="7d962-124">No seu objeto, você pode criar um `ILogger` para as mensagens de log:</span><span class="sxs-lookup"><span data-stu-id="7d962-124">In your object, you can create an `ILogger` to log messages:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

<span data-ttu-id="7d962-125">Isso funciona, mas não é o melhor design.</span><span class="sxs-lookup"><span data-stu-id="7d962-125">This works, but it's not the best design.</span></span> <span data-ttu-id="7d962-126">Se você quiser substituir `FileLogger` com outra `ILogger` implementação, você precisará modificar `SomeComponent`.</span><span class="sxs-lookup"><span data-stu-id="7d962-126">If you want to replace `FileLogger` with another `ILogger` implementation, you will have to modify `SomeComponent`.</span></span> <span data-ttu-id="7d962-127">Suponhamos que muitos dos outros objetos usam `FileLogger`, você precisará alterar todas elas.</span><span class="sxs-lookup"><span data-stu-id="7d962-127">Supposing that a lot of other objects use `FileLogger`, you will need to change all of them.</span></span> <span data-ttu-id="7d962-128">Ou se você decidir fazer `FileLogger` um singleton, você também precisará fazer alterações em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7d962-128">Or if you decide to make `FileLogger` a singleton, you'll also need to make changes throughout the application.</span></span>

<span data-ttu-id="7d962-129">Uma abordagem melhor é "inserir" um `ILogger` no objeto — por exemplo, usando um argumento do construtor:</span><span class="sxs-lookup"><span data-stu-id="7d962-129">A better approach is to "inject" an `ILogger` into the object—for example, by using a constructor argument:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

<span data-ttu-id="7d962-130">Agora, o objeto não é responsável por selecionar quais `ILogger` usar.</span><span class="sxs-lookup"><span data-stu-id="7d962-130">Now the object is not responsible for selecting which `ILogger` to use.</span></span> <span data-ttu-id="7d962-131">Você pode alternar `ILogger` implementações sem alterar os objetos que dependem dele.</span><span class="sxs-lookup"><span data-stu-id="7d962-131">You can swich `ILogger` implementations without changing the objects that depend on it.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

<span data-ttu-id="7d962-132">Esse padrão é chamado [injeção de construtor](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection).</span><span class="sxs-lookup"><span data-stu-id="7d962-132">This pattern is called [constructor injection](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection).</span></span> <span data-ttu-id="7d962-133">Outro padrão é a injeção de setter, em que você definir a dependência por meio de um método de setter ou propriedade.</span><span class="sxs-lookup"><span data-stu-id="7d962-133">Another pattern is setter injection, where you set the dependency through a setter method or property.</span></span>

## <a name="simple-dependency-injection-in-signalr"></a><span data-ttu-id="7d962-134">Injeção de dependência simples no SignalR</span><span class="sxs-lookup"><span data-stu-id="7d962-134">Simple Dependency Injection in SignalR</span></span>

<span data-ttu-id="7d962-135">Considere o aplicativo de bate-papo do tutorial [Introdução ao SignalR](../getting-started/tutorial-getting-started-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="7d962-135">Consider the Chat application from the tutorial [Getting Started with SignalR](../getting-started/tutorial-getting-started-with-signalr.md).</span></span> <span data-ttu-id="7d962-136">Aqui está a classe hub desse aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7d962-136">Here is the hub class from that application:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

<span data-ttu-id="7d962-137">Suponha que você deseja armazenar as mensagens de bate-papo no servidor antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="7d962-137">Suppose that you want to store chat messages on the server before sending them.</span></span> <span data-ttu-id="7d962-138">Você pode definir uma interface que abstrai essa funcionalidade e usar injeção de dependência para injetar a interface para o `ChatHub` classe.</span><span class="sxs-lookup"><span data-stu-id="7d962-138">You might define an interface that abstracts this functionality, and use DI to inject the interface into the `ChatHub` class.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

<span data-ttu-id="7d962-139">O único problema é que um aplicativo de SignalR não cria diretamente hubs; O SignalR cria para você.</span><span class="sxs-lookup"><span data-stu-id="7d962-139">The only problem is that a SignalR application does not directly create hubs; SignalR creates them for you.</span></span> <span data-ttu-id="7d962-140">Por padrão, o SignalR espera que uma classe de hub para ter um construtor sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="7d962-140">By default, SignalR expects a hub class to have a parameterless constructor.</span></span> <span data-ttu-id="7d962-141">No entanto, você pode facilmente registrar uma função para criar instâncias de hub e usar essa função para executar a injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="7d962-141">However, you can easily register a function to create hub instances, and use this function to perform DI.</span></span> <span data-ttu-id="7d962-142">Registrar a função chamando **GlobalHost.DependencyResolver.Register**.</span><span class="sxs-lookup"><span data-stu-id="7d962-142">Register the function by calling **GlobalHost.DependencyResolver.Register**.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample7.cs)]

<span data-ttu-id="7d962-143">Agora o SignalR irá chamar essa função anônima sempre que precisar criar um `ChatHub` instância.</span><span class="sxs-lookup"><span data-stu-id="7d962-143">Now SignalR will invoke this anonymous function whenever it needs to create a `ChatHub` instance.</span></span>

## <a name="ioc-containers"></a><span data-ttu-id="7d962-144">Contêineres IoC</span><span class="sxs-lookup"><span data-stu-id="7d962-144">IoC Containers</span></span>

<span data-ttu-id="7d962-145">O código anterior é adequado para casos simples.</span><span class="sxs-lookup"><span data-stu-id="7d962-145">The previous code is fine for simple cases.</span></span> <span data-ttu-id="7d962-146">Mas você ainda precisava escrever isso:</span><span class="sxs-lookup"><span data-stu-id="7d962-146">But you still had to write this:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

<span data-ttu-id="7d962-147">Em um aplicativo complexo com muitas dependências, você precisa escrever muito desse código de "Conectando".</span><span class="sxs-lookup"><span data-stu-id="7d962-147">In a complex application with many dependencies, you might need to write a lot of this "wiring" code.</span></span> <span data-ttu-id="7d962-148">Esse código pode ser difícil de manter, especialmente se as dependências são aninhadas.</span><span class="sxs-lookup"><span data-stu-id="7d962-148">This code can be hard to maintain, especially if dependencies are nested.</span></span> <span data-ttu-id="7d962-149">Também é difícil de teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="7d962-149">It is also hard to unit test.</span></span>

<span data-ttu-id="7d962-150">Uma solução é usar um contêiner IoC.</span><span class="sxs-lookup"><span data-stu-id="7d962-150">One solution is to use an IoC container.</span></span> <span data-ttu-id="7d962-151">Um contêiner IoC é um componente de software que é responsável por gerenciar as dependências. Você registra tipos com o contêiner e, em seguida, usa o contêiner para criar objetos.</span><span class="sxs-lookup"><span data-stu-id="7d962-151">An IoC container is a software component that is responsible for managing dependencies.You register types with the container, and then use the container to create objects.</span></span> <span data-ttu-id="7d962-152">O contêiner automaticamente detecta as relações de dependência.</span><span class="sxs-lookup"><span data-stu-id="7d962-152">The container automatically figures out the dependency relations.</span></span> <span data-ttu-id="7d962-153">Vários contêineres IoC também permitem controlar coisas como o tempo de vida do objeto e o escopo.</span><span class="sxs-lookup"><span data-stu-id="7d962-153">Many IoC containers also allow you to control things like object lifetime and scope.</span></span>

> [!NOTE]
> <span data-ttu-id="7d962-154">"IoC" significa "inversão de controle", que é um padrão geral em que uma estrutura chama o código do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7d962-154">"IoC" stands for "inversion of control", which is a general pattern where a framework calls into application code.</span></span> <span data-ttu-id="7d962-155">Um contêiner IoC constrói seus objetos para você, o que "inverte" o fluxo normal de controle.</span><span class="sxs-lookup"><span data-stu-id="7d962-155">An IoC container constructs your objects for you, which "inverts" the usual flow of control.</span></span>


## <a name="using-ioc-containers-in-signalr"></a><span data-ttu-id="7d962-156">Usando contêineres IoC no SignalR</span><span class="sxs-lookup"><span data-stu-id="7d962-156">Using IoC Containers in SignalR</span></span>

<span data-ttu-id="7d962-157">O aplicativo de bate-papo provavelmente é muito simple para se beneficiar de um contêiner de IoC.</span><span class="sxs-lookup"><span data-stu-id="7d962-157">The Chat application is probably too simple to benefit from an IoC container.</span></span> <span data-ttu-id="7d962-158">Em vez disso, vamos examinar a [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample) exemplo.</span><span class="sxs-lookup"><span data-stu-id="7d962-158">Instead, let's look at the [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample) sample.</span></span>

<span data-ttu-id="7d962-159">O exemplo StockTicker define duas classes principais:</span><span class="sxs-lookup"><span data-stu-id="7d962-159">The StockTicker sample defines two main classes:</span></span>

- <span data-ttu-id="7d962-160">`StockTickerHub`: A classe hub, que gerencia as conexões de cliente.</span><span class="sxs-lookup"><span data-stu-id="7d962-160">`StockTickerHub`: The hub class, which manages client connections.</span></span>
- <span data-ttu-id="7d962-161">`StockTicker`: Um singleton que mantém os preços de ações e os atualiza periodicamente.</span><span class="sxs-lookup"><span data-stu-id="7d962-161">`StockTicker`: A singleton that holds stock prices and periodically updates them.</span></span>

<span data-ttu-id="7d962-162">`StockTickerHub` contém uma referência à `StockTicker` singleton, enquanto `StockTicker` contém uma referência para o **IHubConnectionContext** para o `StockTickerHub`.</span><span class="sxs-lookup"><span data-stu-id="7d962-162">`StockTickerHub` holds a reference to the `StockTicker` singleton, while `StockTicker` holds a reference to the **IHubConnectionContext** for the `StockTickerHub`.</span></span> <span data-ttu-id="7d962-163">Ele usa essa interface para se comunicar com `StockTickerHub` instâncias.</span><span class="sxs-lookup"><span data-stu-id="7d962-163">It uses this interface to communicate with `StockTickerHub` instances.</span></span> <span data-ttu-id="7d962-164">(Para obter mais informações, consulte [transmissão de servidor com SignalR do ASP.NET](../getting-started/tutorial-server-broadcast-with-signalr.md).)</span><span class="sxs-lookup"><span data-stu-id="7d962-164">(For more information, see [Server Broadcast with ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).)</span></span>

<span data-ttu-id="7d962-165">Podemos usar um contêiner IoC para desembolar essas dependências de um pouco.</span><span class="sxs-lookup"><span data-stu-id="7d962-165">We can use an IoC container to untangle these dependencies a bit.</span></span> <span data-ttu-id="7d962-166">Primeiro, vamos simplificar as `StockTickerHub` e `StockTicker` classes.</span><span class="sxs-lookup"><span data-stu-id="7d962-166">First, let's simplify the `StockTickerHub` and `StockTicker` classes.</span></span> <span data-ttu-id="7d962-167">No código a seguir, comentei as partes que não precisamos.</span><span class="sxs-lookup"><span data-stu-id="7d962-167">In the following code, I've commented out the parts that we don't need.</span></span>

<span data-ttu-id="7d962-168">Remova o construtor sem parâmetros de `StockTickerHub`.</span><span class="sxs-lookup"><span data-stu-id="7d962-168">Remove the parameterless constructor from `StockTickerHub`.</span></span> <span data-ttu-id="7d962-169">Em vez disso, nós usaremos sempre DI para criar o hub.</span><span class="sxs-lookup"><span data-stu-id="7d962-169">Instead, we will always use DI to create the hub.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

<span data-ttu-id="7d962-170">Para StockTicker, remova a instância singleton.</span><span class="sxs-lookup"><span data-stu-id="7d962-170">For StockTicker, remove the singleton instance.</span></span> <span data-ttu-id="7d962-171">Posteriormente, usaremos o contêiner de IoC para controlar o tempo de vida StockTicker.</span><span class="sxs-lookup"><span data-stu-id="7d962-171">Later, we'll use the IoC container to control the StockTicker lifetime.</span></span> <span data-ttu-id="7d962-172">Além disso, verifique o construtor público.</span><span class="sxs-lookup"><span data-stu-id="7d962-172">Also, make the constructor public.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample10.cs?highlight=7)]

<span data-ttu-id="7d962-173">Em seguida, podemos refatorar o código, criando uma interface para `StockTicker`.</span><span class="sxs-lookup"><span data-stu-id="7d962-173">Next, we can refactor the code by creating an interface for `StockTicker`.</span></span> <span data-ttu-id="7d962-174">Vamos usar essa interface para desacoplar a `StockTickerHub` do `StockTicker` classe.</span><span class="sxs-lookup"><span data-stu-id="7d962-174">We'll use this interface to decouple the `StockTickerHub` from the `StockTicker` class.</span></span>

<span data-ttu-id="7d962-175">Visual Studio faz esse tipo de refatoração fácil.</span><span class="sxs-lookup"><span data-stu-id="7d962-175">Visual Studio makes this kind of refactoring easy.</span></span> <span data-ttu-id="7d962-176">Abra o arquivo StockTicker.cs, clique com botão direito no `StockTicker` declaração de classe e, em seguida, selecione **refatorar** ... **Extrair Interface**.</span><span class="sxs-lookup"><span data-stu-id="7d962-176">Open the file StockTicker.cs, right-click on the `StockTicker` class declaration, and select **Refactor** ... **Extract Interface**.</span></span>

![](dependency-injection/_static/image1.png)

<span data-ttu-id="7d962-177">No **extrair Interface** caixa de diálogo, clique em **Selecionar tudo**.</span><span class="sxs-lookup"><span data-stu-id="7d962-177">In the **Extract Interface** dialog, click **Select All**.</span></span> <span data-ttu-id="7d962-178">Deixe os outros padrões.</span><span class="sxs-lookup"><span data-stu-id="7d962-178">Leave the other defaults.</span></span> <span data-ttu-id="7d962-179">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="7d962-179">Click **OK**.</span></span>

![](dependency-injection/_static/image2.png)

<span data-ttu-id="7d962-180">O Visual Studio cria uma nova interface chamada `IStockTicker`e também será alterado `StockTicker` derivar `IStockTicker`.</span><span class="sxs-lookup"><span data-stu-id="7d962-180">Visual Studio creates a new interface named `IStockTicker`, and also changes `StockTicker` to derive from `IStockTicker`.</span></span>

<span data-ttu-id="7d962-181">Abra o arquivo IStockTicker.cs e alterar a interface **público**.</span><span class="sxs-lookup"><span data-stu-id="7d962-181">Open the file IStockTicker.cs and change the interface to **public**.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample11.cs?highlight=1)]

<span data-ttu-id="7d962-182">No `StockTickerHub` classe, altere as duas instâncias de `StockTicker` para `IStockTicker`:</span><span class="sxs-lookup"><span data-stu-id="7d962-182">In the `StockTickerHub` class, change the two instances of `StockTicker` to `IStockTicker`:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample12.cs?highlight=4,6)]

<span data-ttu-id="7d962-183">Criando um `IStockTicker` interface não é estritamente necessário, mas eu queria mostrar como a injeção de dependência pode ajudar a reduzir o acoplamento entre componentes em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7d962-183">Creating an `IStockTicker` interface isn't strictly necessary, but I wanted to show how DI can help to reduce coupling between components in your application.</span></span>

## <a name="add-the-ninject-library"></a><span data-ttu-id="7d962-184">Adicionar a biblioteca Ninject</span><span class="sxs-lookup"><span data-stu-id="7d962-184">Add the Ninject Library</span></span>

<span data-ttu-id="7d962-185">Há vários contêineres de IoC de código-fonte aberto para .NET.</span><span class="sxs-lookup"><span data-stu-id="7d962-185">There are many open-source IoC containers for .NET.</span></span> <span data-ttu-id="7d962-186">Para este tutorial, usarei [Ninject](http://www.ninject.org/).</span><span class="sxs-lookup"><span data-stu-id="7d962-186">For this tutorial, I'll use [Ninject](http://www.ninject.org/).</span></span> <span data-ttu-id="7d962-187">(Outras bibliotecas populares incluem [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Unity](https://github.com/unitycontainer/unity), e [StructureMap ](http://docs.structuremap.net).)</span><span class="sxs-lookup"><span data-stu-id="7d962-187">(Other popular libraries include [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Unity](https://github.com/unitycontainer/unity), and [StructureMap](http://docs.structuremap.net).)</span></span>

<span data-ttu-id="7d962-188">Use o Gerenciador de pacotes NuGet para instalar o [Ninject biblioteca](https://nuget.org/packages/Ninject/3.0.1.10).</span><span class="sxs-lookup"><span data-stu-id="7d962-188">Use NuGet Package Manager to install the [Ninject library](https://nuget.org/packages/Ninject/3.0.1.10).</span></span> <span data-ttu-id="7d962-189">No Visual Studio, do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet** > **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="7d962-189">In Visual Studio, from the **Tools** menu select **NuGet Package Manager** > **Package Manager Console**.</span></span> <span data-ttu-id="7d962-190">Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7d962-190">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](dependency-injection/samples/sample13.ps1)]

## <a name="replace-the-signalr-dependency-resolver"></a><span data-ttu-id="7d962-191">Substitua o resolvedor de dependência do SignalR</span><span class="sxs-lookup"><span data-stu-id="7d962-191">Replace the SignalR Dependency Resolver</span></span>

<span data-ttu-id="7d962-192">Para usar Ninject dentro do SignalR, crie uma classe que deriva de **DefaultDependencyResolver**.</span><span class="sxs-lookup"><span data-stu-id="7d962-192">To use Ninject within SignalR, create a class that derives from **DefaultDependencyResolver**.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample14.cs)]

<span data-ttu-id="7d962-193">Essa classe substitui o **GetService** e **GetServices** métodos **DefaultDependencyResolver**.</span><span class="sxs-lookup"><span data-stu-id="7d962-193">This class overrides the **GetService** and **GetServices** methods of **DefaultDependencyResolver**.</span></span> <span data-ttu-id="7d962-194">O SignalR chama esses métodos para criar vários objetos em tempo de execução, incluindo instâncias do hub, bem como vários serviços usados internamente pelo SignalR.</span><span class="sxs-lookup"><span data-stu-id="7d962-194">SignalR calls these methods to create various objects at runtime, including hub instances, as well as various services used internally by SignalR.</span></span>

- <span data-ttu-id="7d962-195">O **GetService** método cria uma única instância de um tipo.</span><span class="sxs-lookup"><span data-stu-id="7d962-195">The **GetService** method creates a single instance of a type.</span></span> <span data-ttu-id="7d962-196">Substitua este método para chamar o kernel de Ninject **TryGet** método.</span><span class="sxs-lookup"><span data-stu-id="7d962-196">Override this method to call the Ninject kernel's **TryGet** method.</span></span> <span data-ttu-id="7d962-197">Se esse método retornar nulo, volte para o resolvedor padrão.</span><span class="sxs-lookup"><span data-stu-id="7d962-197">If that method returns null, fall back to the default resolver.</span></span>
- <span data-ttu-id="7d962-198">O **GetServices** método cria uma coleção de objetos de um tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="7d962-198">The **GetServices** method creates a collection of objects of a specified type.</span></span> <span data-ttu-id="7d962-199">Substitua este método para concatenar os resultados de Ninject com os resultados do resolvedor padrão.</span><span class="sxs-lookup"><span data-stu-id="7d962-199">Override this method to concatenate the results from Ninject with the results from the default resolver.</span></span>

## <a name="configure-ninject-bindings"></a><span data-ttu-id="7d962-200">Configurar as ligações de Ninject</span><span class="sxs-lookup"><span data-stu-id="7d962-200">Configure Ninject Bindings</span></span>

<span data-ttu-id="7d962-201">Agora vamos usar Ninject para declarar associações de tipo.</span><span class="sxs-lookup"><span data-stu-id="7d962-201">Now we'll use Ninject to declare type bindings.</span></span>

<span data-ttu-id="7d962-202">Abra a classe do Startup.cs do seu aplicativo (que você seja criado manualmente, de acordo com as instruções de pacote em `readme.txt`, ou que foi criado com a adição de autenticação ao seu projeto).</span><span class="sxs-lookup"><span data-stu-id="7d962-202">Open your application's Startup.cs class (that you either created manually as per the package instructions in `readme.txt`, or that was created by adding authentication to your project).</span></span> <span data-ttu-id="7d962-203">No `Startup.Configuration` método, crie o contêiner Ninject, que chama o Ninject o *kernel*.</span><span class="sxs-lookup"><span data-stu-id="7d962-203">In the `Startup.Configuration` method, create the Ninject container, which Ninject calls the *kernel*.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample15.cs)]

<span data-ttu-id="7d962-204">Crie uma instância do nosso resolvedor de dependência personalizada:</span><span class="sxs-lookup"><span data-stu-id="7d962-204">Create an instance of our custom dependency resolver:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample16.cs)]

<span data-ttu-id="7d962-205">Criar uma associação para `IStockTicker` da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="7d962-205">Create a binding for `IStockTicker` as follows:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample17.cs)]

<span data-ttu-id="7d962-206">Esse código está dizendo que duas coisas.</span><span class="sxs-lookup"><span data-stu-id="7d962-206">This code is saying two things.</span></span> <span data-ttu-id="7d962-207">Primeiro, sempre que o aplicativo precisa de um `IStockTicker`, o kernel deve criar uma instância de `StockTicker`.</span><span class="sxs-lookup"><span data-stu-id="7d962-207">First, whenever the application needs an `IStockTicker`, the kernel should create an instance of `StockTicker`.</span></span> <span data-ttu-id="7d962-208">Segundo, o `StockTicker` classe deve ser criado como um objeto de singleton.</span><span class="sxs-lookup"><span data-stu-id="7d962-208">Second, the `StockTicker` class should be a created as a singleton object.</span></span> <span data-ttu-id="7d962-209">Ninject criará uma instância do objeto e retornar a mesma instância para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="7d962-209">Ninject will create one instance of the object, and return the same instance for each request.</span></span>

<span data-ttu-id="7d962-210">Criar uma associação para **IHubConnectionContext** da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="7d962-210">Create a binding for **IHubConnectionContext** as follows:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample18.cs)]

<span data-ttu-id="7d962-211">Esse código creatres uma função anônima que retorna um **IHubConnection**.</span><span class="sxs-lookup"><span data-stu-id="7d962-211">This code creatres an anonymous function that returns an **IHubConnection**.</span></span> <span data-ttu-id="7d962-212">O **WhenInjectedInto** Ninject usar essa função somente durante a criação do método informa `IStockTicker` instâncias.</span><span class="sxs-lookup"><span data-stu-id="7d962-212">The **WhenInjectedInto** method tells Ninject to use this function only when creating `IStockTicker` instances.</span></span> <span data-ttu-id="7d962-213">O motivo é que o SignalR cria **IHubConnectionContext** instâncias internamente, e não queremos substituir como o SignalR cria.</span><span class="sxs-lookup"><span data-stu-id="7d962-213">The reason is that SignalR creates **IHubConnectionContext** instances internally, and we don't want to override how SignalR creates them.</span></span> <span data-ttu-id="7d962-214">Essa função se aplica somente aos nossos `StockTicker` classe.</span><span class="sxs-lookup"><span data-stu-id="7d962-214">This function only applies to our `StockTicker` class.</span></span>

<span data-ttu-id="7d962-215">Passe o resolvedor de dependência para o **MapSignalR** método adicionando uma configuração de hub:</span><span class="sxs-lookup"><span data-stu-id="7d962-215">Pass the dependency resolver into the **MapSignalR** method by adding a hub configuration:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample19.cs)]

<span data-ttu-id="7d962-216">Atualize o método Startup.ConfigureSignalR na classe de inicialização do exemplo com o novo parâmetro:</span><span class="sxs-lookup"><span data-stu-id="7d962-216">Update the Startup.ConfigureSignalR method in the sample's Startup class with the new parameter:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample20.cs)]

<span data-ttu-id="7d962-217">Agora o SignalR usará o resolvedor especificado na **MapSignalR**, em vez do resolvedor padrão.</span><span class="sxs-lookup"><span data-stu-id="7d962-217">Now SignalR will use the resolver specified in **MapSignalR**, instead of the default resolver.</span></span>

<span data-ttu-id="7d962-218">Aqui está o código completo para a listagem `Startup.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="7d962-218">Here is the complete code listing for `Startup.Configuration`.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample21.cs)]

<span data-ttu-id="7d962-219">Para executar o aplicativo StockTicker no Visual Studio, pressione F5.</span><span class="sxs-lookup"><span data-stu-id="7d962-219">To run the StockTicker application in Visual Studio, press F5.</span></span> <span data-ttu-id="7d962-220">Na janela do navegador, navegue até `http://localhost:*port*/SignalR.Sample/StockTicker.html`.</span><span class="sxs-lookup"><span data-stu-id="7d962-220">In the browser window, navigate to `http://localhost:*port*/SignalR.Sample/StockTicker.html`.</span></span>

![](dependency-injection/_static/image3.png)

<span data-ttu-id="7d962-221">O aplicativo tem a mesma funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="7d962-221">The application has exactly the same functionality as before.</span></span> <span data-ttu-id="7d962-222">(Para obter uma descrição, consulte [transmissão de servidor com SignalR do ASP.NET](../getting-started/tutorial-server-broadcast-with-signalr.md).) Ainda não alteramos o comportamento. acabou de criar o código mais fácil de testar, manter e evoluem.</span><span class="sxs-lookup"><span data-stu-id="7d962-222">(For a description, see [Server Broadcast with ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).) We haven't changed the behavior; just made the code easier to test, maintain, and evolve.</span></span>
