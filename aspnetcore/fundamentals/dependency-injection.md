---
title: Injeção de dependência no ASP.NET Core
author: guardrex
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: cc020d7397b03f8ecd6cebf98a14b4aaebb47940
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488683"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="e19ad-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e19ad-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="e19ad-104">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e19ad-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e19ad-105">O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="e19ad-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="e19ad-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="e19ad-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="e19ad-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e19ad-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="e19ad-108">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="e19ad-108">Overview of dependency injection</span></span>

<span data-ttu-id="e19ad-109">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="e19ad-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="e19ad-110">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="e19ad-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e19ad-111">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="e19ad-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="e19ad-112">A classe `MyDependency` é uma dependência da classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="e19ad-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="e19ad-113">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="e19ad-113">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="e19ad-114">A classe `MyDependency` é uma dependência da classe `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="e19ad-114">The `MyDependency` class is a dependency of the `HomeController` class:</span></span>

```csharp
public class HomeController : Controller
{
    MyDependency _dependency = new MyDependency();

    public async Task<IActionResult> Index()
    {
        await _dependency.WriteMessage(
            "HomeController.Index created this message.");

        return View();
    }
}
```

::: moniker-end

<span data-ttu-id="e19ad-115">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-115">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="e19ad-116">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="e19ad-116">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="e19ad-117">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="e19ad-117">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="e19ad-118">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="e19ad-118">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="e19ad-119">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e19ad-119">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="e19ad-120">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="e19ad-120">This implementation is difficult to unit test.</span></span> <span data-ttu-id="e19ad-121">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="e19ad-121">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="e19ad-122">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="e19ad-122">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="e19ad-123">Usando uma interface para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="e19ad-123">The use of an interface to abstract the dependency implementation.</span></span>
* <span data-ttu-id="e19ad-124">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-124">Registration of the dependency in a service container.</span></span> <span data-ttu-id="e19ad-125">O ASP.NET Core fornece um contêiner de serviço interno, o [IServiceProvider](/dotnet/api/system.iserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="e19ad-125">ASP.NET Core provides a built-in service container, [IServiceProvider](/dotnet/api/system.iserviceprovider).</span></span> <span data-ttu-id="e19ad-126">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e19ad-126">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="e19ad-127">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="e19ad-127">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="e19ad-128">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="e19ad-128">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="e19ad-129">No [exemplo de aplicativo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e19ad-129">In the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="e19ad-130">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="e19ad-130">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="e19ad-131">`MyDependency` solicita um [ILogger&lt;TCategoryName&gt; ](/dotnet/api/microsoft.extensions.logging.ilogger-1) em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="e19ad-131">`MyDependency` requests an [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) in its constructor.</span></span> <span data-ttu-id="e19ad-132">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="e19ad-132">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="e19ad-133">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="e19ad-133">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="e19ad-134">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="e19ad-134">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="e19ad-135">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="e19ad-135">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="e19ad-136">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-136">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="e19ad-137">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-137">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e19ad-138">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="e19ad-138">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="e19ad-139">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="e19ad-139">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

<span data-ttu-id="e19ad-140">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-140">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="e19ad-141">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="e19ad-141">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="e19ad-142">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="e19ad-142">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e19ad-143">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="e19ad-143">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="e19ad-144">Por exemplo, `services.AddMvc()` adiciona os serviços exigidos pelo MVC e por Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e19ad-144">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="e19ad-145">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="e19ad-145">We recommended that apps follow this convention.</span></span> <span data-ttu-id="e19ad-146">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-146">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="e19ad-147">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="e19ad-147">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="e19ad-148">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="e19ad-148">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="e19ad-149">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="e19ad-149">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="e19ad-150">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="e19ad-150">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/MyDependencyController.cs?name=snippet1&highlight=3,5-8,13-14)]

::: moniker-end

## <a name="framework-provided-services"></a><span data-ttu-id="e19ad-151">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="e19ad-151">Framework-provided services</span></span>

<span data-ttu-id="e19ad-152">O método `Startup.ConfigureServices` é responsável por definir os serviços usados pelo aplicativo, incluindo recursos de plataforma como o Entity Framework Core e o ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="e19ad-152">The `Startup.ConfigureServices` method is responsible for defining the services the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="e19ad-153">Inicialmente, a `IServiceCollection` fornecida ao `ConfigureServices` tem os seguintes serviços definidos (dependendo de [como o host foi configurado](xref:fundamentals/index#host)):</span><span class="sxs-lookup"><span data-stu-id="e19ad-153">Initially, the `IServiceCollection` provided to `ConfigureServices` has the following services defined (depending on [how the host was configured](xref:fundamentals/index#host)):</span></span>

| <span data-ttu-id="e19ad-154">Tipo de serviço</span><span class="sxs-lookup"><span data-stu-id="e19ad-154">Service Type</span></span> | <span data-ttu-id="e19ad-155">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="e19ad-155">Lifetime</span></span> |
| ------------ | -------- |
| [<span data-ttu-id="e19ad-156">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span><span class="sxs-lookup"><span data-stu-id="e19ad-156">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | <span data-ttu-id="e19ad-157">Transitório</span><span class="sxs-lookup"><span data-stu-id="e19ad-157">Transient</span></span> |
| [<span data-ttu-id="e19ad-158">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e19ad-158">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | <span data-ttu-id="e19ad-159">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-159">Singleton</span></span> |
| [<span data-ttu-id="e19ad-160">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="e19ad-160">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | <span data-ttu-id="e19ad-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-161">Singleton</span></span> |
| [<span data-ttu-id="e19ad-162">Microsoft.AspNetCore.Hosting.IStartup</span><span class="sxs-lookup"><span data-stu-id="e19ad-162">Microsoft.AspNetCore.Hosting.IStartup</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | <span data-ttu-id="e19ad-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-163">Singleton</span></span> |
| [<span data-ttu-id="e19ad-164">Microsoft.AspNetCore.Hosting.IStartupFilter</span><span class="sxs-lookup"><span data-stu-id="e19ad-164">Microsoft.AspNetCore.Hosting.IStartupFilter</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | <span data-ttu-id="e19ad-165">Transitório</span><span class="sxs-lookup"><span data-stu-id="e19ad-165">Transient</span></span> |
| [<span data-ttu-id="e19ad-166">Microsoft.AspNetCore.Hosting.Server.IServer</span><span class="sxs-lookup"><span data-stu-id="e19ad-166">Microsoft.AspNetCore.Hosting.Server.IServer</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | <span data-ttu-id="e19ad-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-167">Singleton</span></span> |
| [<span data-ttu-id="e19ad-168">Microsoft.AspNetCore.Http.IHttpContextFactory</span><span class="sxs-lookup"><span data-stu-id="e19ad-168">Microsoft.AspNetCore.Http.IHttpContextFactory</span></span>](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | <span data-ttu-id="e19ad-169">Transitório</span><span class="sxs-lookup"><span data-stu-id="e19ad-169">Transient</span></span> |
| [<span data-ttu-id="e19ad-170">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="e19ad-170">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.logging.ilogger) | <span data-ttu-id="e19ad-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-171">Singleton</span></span> |
| [<span data-ttu-id="e19ad-172">Microsoft.Extensions.Logging.ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="e19ad-172">Microsoft.Extensions.Logging.ILoggerFactory</span></span>](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | <span data-ttu-id="e19ad-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-173">Singleton</span></span> |
| [<span data-ttu-id="e19ad-174">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span><span class="sxs-lookup"><span data-stu-id="e19ad-174">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span></span>](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | <span data-ttu-id="e19ad-175">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-175">Singleton</span></span> |
| [<span data-ttu-id="e19ad-176">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="e19ad-176">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | <span data-ttu-id="e19ad-177">Transitório</span><span class="sxs-lookup"><span data-stu-id="e19ad-177">Transient</span></span> |
| [<span data-ttu-id="e19ad-178">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="e19ad-178">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.ioptions-1) | <span data-ttu-id="e19ad-179">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-179">Singleton</span></span> |
| [<span data-ttu-id="e19ad-180">System.Diagnostics.DiagnosticSource</span><span class="sxs-lookup"><span data-stu-id="e19ad-180">System.Diagnostics.DiagnosticSource</span></span>](/dotnet/core/api/system.diagnostics.diagnosticsource) | <span data-ttu-id="e19ad-181">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-181">Singleton</span></span> |
| [<span data-ttu-id="e19ad-182">System.Diagnostics.DiagnosticListener</span><span class="sxs-lookup"><span data-stu-id="e19ad-182">System.Diagnostics.DiagnosticListener</span></span>](/dotnet/core/api/system.diagnostics.diagnosticlistener) | <span data-ttu-id="e19ad-183">Singleton</span><span class="sxs-lookup"><span data-stu-id="e19ad-183">Singleton</span></span> |

<span data-ttu-id="e19ad-184">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-184">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="e19ad-185">O código a seguir é um exemplo de como adicionar outros serviços ao contêiner usando os métodos de extensão [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity) e [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span><span class="sxs-lookup"><span data-stu-id="e19ad-185">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity), and [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

<span data-ttu-id="e19ad-186">Para saber mais, confira a [Classe ServiceCollection](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="e19ad-186">For more information, see the [ServiceCollection Class](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="e19ad-187">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="e19ad-187">Service lifetimes</span></span>

<span data-ttu-id="e19ad-188">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="e19ad-188">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="e19ad-189">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="e19ad-189">ASP.NET Core services can be configured with the following lifetimes:</span></span>

<span data-ttu-id="e19ad-190">**Transitório**</span><span class="sxs-lookup"><span data-stu-id="e19ad-190">**Transient**</span></span>

<span data-ttu-id="e19ad-191">Os serviços com tempo de vida transitório são criados sempre que são solicitados.</span><span class="sxs-lookup"><span data-stu-id="e19ad-191">Transient lifetime services are created each time they're requested.</span></span> <span data-ttu-id="e19ad-192">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="e19ad-192">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="e19ad-193">**Com escopo**</span><span class="sxs-lookup"><span data-stu-id="e19ad-193">**Scoped**</span></span>

<span data-ttu-id="e19ad-194">Os serviços com tempo de vida com escopo são criados uma vez por solicitação.</span><span class="sxs-lookup"><span data-stu-id="e19ad-194">Scoped lifetime services are created once per request.</span></span>

> [!WARNING]
> <span data-ttu-id="e19ad-195">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-195">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="e19ad-196">Não injete por meio de injeção de construtor porque isso força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="e19ad-196">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="e19ad-197">Para obter mais informações, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="e19ad-197">For more information, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="e19ad-198">**Singleton**</span><span class="sxs-lookup"><span data-stu-id="e19ad-198">**Singleton**</span></span>

<span data-ttu-id="e19ad-199">Serviços de tempo de vida singleton são criados na primeira solicitação (ou quando `ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="e19ad-199">Singleton lifetime services are created the first time they're requested (or when `ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="e19ad-200">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="e19ad-200">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="e19ad-201">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-201">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="e19ad-202">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="e19ad-202">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="e19ad-203">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="e19ad-203">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="e19ad-204">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="e19ad-204">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="e19ad-205">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="e19ad-205">Constructor injection behavior</span></span>

<span data-ttu-id="e19ad-206">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="e19ad-206">Services can be resolved by two mechanisms:</span></span>

* `IServiceProvider`
* <span data-ttu-id="e19ad-207">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; Permite a criação de objetos sem registro do serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="e19ad-207">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="e19ad-208">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="e19ad-208">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="e19ad-209">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="e19ad-209">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="e19ad-210">Quando os serviços são resolvidos por `IServiceProvider` ou `ActivatorUtilities`, a injeção do construtor exige um construtor *público*.</span><span class="sxs-lookup"><span data-stu-id="e19ad-210">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="e19ad-211">Quando os serviços são resolvidos por `ActivatorUtilities`, a injeção de construtor exige a existência de apenas de um construtor aplicável.</span><span class="sxs-lookup"><span data-stu-id="e19ad-211">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="e19ad-212">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="e19ad-212">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="e19ad-213">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="e19ad-213">Entity Framework contexts</span></span>

<span data-ttu-id="e19ad-214">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="e19ad-214">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the request.</span></span> <span data-ttu-id="e19ad-215">O tempo de vida padrão tem escopo se um tempo de vida não é especificado por uma sobrecarga de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> ao registrar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e19ad-215">The default lifetime is scoped if a lifetime isn't specified by an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> overload when registering the database context.</span></span> <span data-ttu-id="e19ad-216">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-216">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="e19ad-217">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="e19ad-217">Lifetime and registration options</span></span>

<span data-ttu-id="e19ad-218">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-218">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="e19ad-219">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="e19ad-219">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="e19ad-220">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-220">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="e19ad-221">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="e19ad-221">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="e19ad-222">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="e19ad-222">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="e19ad-223">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="e19ad-223">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="e19ad-224">Se os serviços temporários forem criados mediante solicitação, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-224">If transient services are created when requested, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="e19ad-225">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-225">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="e19ad-226">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-226">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="e19ad-227">Se os serviços com escopo forem criados por solicitação, o `OperationId` do serviço `IOperationScoped` será o mesmo de `OperationService` dentro de uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="e19ad-227">If scoped services are created per request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a request.</span></span> <span data-ttu-id="e19ad-228">Em todas as solicitações, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="e19ad-228">Across requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="e19ad-229">Se os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-229">If singleton and singleton-instance services are created once and used across all requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="e19ad-230">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="e19ad-230">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=12-15,18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="e19ad-231">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-231">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="e19ad-232">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="e19ad-232">It's clear when this type is in use (its GUID is all zeroes).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e19ad-233">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="e19ad-233">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="e19ad-234">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-234">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="e19ad-235">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="e19ad-235">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="e19ad-236">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="e19ad-236">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="e19ad-237">O exemplo de aplicativo inclui um `OperationsController` que solicita cada tipo de `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-237">The sample app includes an `OperationsController` that requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="e19ad-238">A ação `Index` define os serviços no `ViewBag` para exibição dos valores `OperationId` do serviço:</span><span class="sxs-lookup"><span data-stu-id="e19ad-238">The `Index` action sets the services into the `ViewBag` for display of the service's `OperationId` values:</span></span>

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/OperationsController.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="e19ad-239">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="e19ad-239">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="e19ad-240">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="e19ad-240">**First request:**</span></span>

<span data-ttu-id="e19ad-241">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="e19ad-241">Controller operations:</span></span>

<span data-ttu-id="e19ad-242">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="e19ad-242">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="e19ad-243">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e19ad-243">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e19ad-244">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e19ad-244">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e19ad-245">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e19ad-245">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e19ad-246">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="e19ad-246">`OperationService` operations:</span></span>

<span data-ttu-id="e19ad-247">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="e19ad-247">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="e19ad-248">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e19ad-248">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e19ad-249">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e19ad-249">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e19ad-250">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e19ad-250">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e19ad-251">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="e19ad-251">**Second request:**</span></span>

<span data-ttu-id="e19ad-252">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="e19ad-252">Controller operations:</span></span>

<span data-ttu-id="e19ad-253">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="e19ad-253">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="e19ad-254">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e19ad-254">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e19ad-255">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e19ad-255">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e19ad-256">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e19ad-256">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e19ad-257">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="e19ad-257">`OperationService` operations:</span></span>

<span data-ttu-id="e19ad-258">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="e19ad-258">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="e19ad-259">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e19ad-259">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e19ad-260">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e19ad-260">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e19ad-261">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e19ad-261">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e19ad-262">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="e19ad-262">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="e19ad-263">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="e19ad-263">*Transient* objects are always different.</span></span> <span data-ttu-id="e19ad-264">Observe que o valor `OperationId` transitório da primeira e da segunda solicitações é diferente para as duas operações `OperationService` e em todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="e19ad-264">Note that the transient `OperationId` value for both the first and second requests are different for both `OperationService` operations and across requests.</span></span> <span data-ttu-id="e19ad-265">Uma nova instância é fornecida para cada solicitação e serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-265">A new instance is provided to each service and request.</span></span>
* <span data-ttu-id="e19ad-266">Os objetos *com escopo* são os mesmos em uma solicitação, mas diferentes entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="e19ad-266">*Scoped* objects are the same within a request but different across requests.</span></span>
* <span data-ttu-id="e19ad-267">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-267">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="e19ad-268">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="e19ad-268">Call services from main</span></span>

<span data-ttu-id="e19ad-269">Crie um [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) com [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e19ad-269">Create an [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) with [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="e19ad-270">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="e19ad-270">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="e19ad-271">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e19ad-271">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = CreateWebHostBuilder(args).Build();

    using (var serviceScope = host.Services.CreateScope())
    {
        var services = serviceScope.ServiceProvider;

        try
        {
            var serviceContext = services.GetRequiredService<MyScopedService>();
            // Use the context here
        }
        catch (Exception ex)
        {
            var logger = services.GetRequiredService<ILogger<Program>>();
            logger.LogError(ex, "An error occurred.");
        }
    }

    host.Run();
}
```

## <a name="scope-validation"></a><span data-ttu-id="e19ad-272">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="e19ad-272">Scope validation</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e19ad-273">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="e19ad-273">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="e19ad-274">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="e19ad-274">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="e19ad-275">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="e19ad-275">Scoped services aren't directly or indirectly injected into singletons.</span></span>

::: moniker-end

<span data-ttu-id="e19ad-276">O provedor de serviços raiz é criado quando [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) é chamado.</span><span class="sxs-lookup"><span data-stu-id="e19ad-276">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="e19ad-277">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="e19ad-277">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="e19ad-278">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="e19ad-278">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="e19ad-279">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="e19ad-279">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="e19ad-280">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="e19ad-280">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="e19ad-281">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="e19ad-281">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="e19ad-282">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="e19ad-282">Request Services</span></span>

<span data-ttu-id="e19ad-283">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices).</span><span class="sxs-lookup"><span data-stu-id="e19ad-283">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices) collection.</span></span>

<span data-ttu-id="e19ad-284">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e19ad-284">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="e19ad-285">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-285">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="e19ad-286">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="e19ad-286">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="e19ad-287">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="e19ad-287">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="e19ad-288">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="e19ad-288">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="e19ad-289">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-289">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="e19ad-290">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="e19ad-290">Design services for dependency injection</span></span>

<span data-ttu-id="e19ad-291">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="e19ad-291">Best practices are to:</span></span>

* <span data-ttu-id="e19ad-292">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="e19ad-292">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="e19ad-293">Evite chamadas de método estático com estado.</span><span class="sxs-lookup"><span data-stu-id="e19ad-293">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="e19ad-294">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="e19ad-294">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="e19ad-295">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="e19ad-295">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="e19ad-296">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="e19ad-296">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="e19ad-297">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="e19ad-297">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="e19ad-298">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="e19ad-298">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="e19ad-299">Lembre-se de que as classes de modelo de página de Razor Pages e as classes do controlador de MVC devem se concentrar em questões de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="e19ad-299">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="e19ad-300">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="e19ad-300">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="e19ad-301">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="e19ad-301">Disposal of services</span></span>

<span data-ttu-id="e19ad-302">O contêiner chama `Dispose` para os tipos `IDisposable` criados por ele.</span><span class="sxs-lookup"><span data-stu-id="e19ad-302">The container calls `Dispose` for the `IDisposable` types it creates.</span></span> <span data-ttu-id="e19ad-303">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="e19ad-303">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

::: moniker range="= aspnetcore-1.0"

> [!NOTE]
> <span data-ttu-id="e19ad-304">No ASP.NET Core 1.0, as chamadas do contêiner descartam *todos* os objetos `IDisposable`, incluindo aqueles que ele não criou.</span><span class="sxs-lookup"><span data-stu-id="e19ad-304">In ASP.NET Core 1.0, the container calls dispose on *all* `IDisposable` objects, including those it didn't create.</span></span>

::: moniker-end

## <a name="default-service-container-replacement"></a><span data-ttu-id="e19ad-305">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="e19ad-305">Default service container replacement</span></span>

<span data-ttu-id="e19ad-306">O contêiner de serviço interno deve atender às necessidades da estrutura e da maioria dos aplicativos do consumidor.</span><span class="sxs-lookup"><span data-stu-id="e19ad-306">The built-in service container is meant to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="e19ad-307">É recomendado usar o contêiner interno, a menos que você precise de um recurso específico que não seja compatível com ele.</span><span class="sxs-lookup"><span data-stu-id="e19ad-307">We recommend using the built-in container unless you need a specific feature that it doesn't support.</span></span> <span data-ttu-id="e19ad-308">Alguns dos recursos compatíveis com contêineres de terceiros não encontrados no contêiner interno:</span><span class="sxs-lookup"><span data-stu-id="e19ad-308">Some of the features supported in 3rd party containers not found in the built-in container:</span></span>

* <span data-ttu-id="e19ad-309">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="e19ad-309">Property injection</span></span>
* <span data-ttu-id="e19ad-310">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="e19ad-310">Injection based on name</span></span>
* <span data-ttu-id="e19ad-311">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="e19ad-311">Child containers</span></span>
* <span data-ttu-id="e19ad-312">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="e19ad-312">Custom lifetime management</span></span>
* <span data-ttu-id="e19ad-313">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="e19ad-313">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="e19ad-314">Confira o [arquivo readme.md de injeção de dependência](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) para obter uma lista de alguns dos contêineres que dão suporte a adaptadores.</span><span class="sxs-lookup"><span data-stu-id="e19ad-314">See the [Dependency Injection readme.md file](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) for a list of some of the containers that support adapters.</span></span>

<span data-ttu-id="e19ad-315">O exemplo a seguir substitui o contêiner interno por [Autofac](https://autofac.org/):</span><span class="sxs-lookup"><span data-stu-id="e19ad-315">The following sample replaces the built-in container with [Autofac](https://autofac.org/):</span></span>

* <span data-ttu-id="e19ad-316">Instale os pacotes de contêiner apropriados:</span><span class="sxs-lookup"><span data-stu-id="e19ad-316">Install the appropriate container package(s):</span></span>

  * [<span data-ttu-id="e19ad-317">Autofac</span><span class="sxs-lookup"><span data-stu-id="e19ad-317">Autofac</span></span>](https://www.nuget.org/packages/Autofac/)
  * [<span data-ttu-id="e19ad-318">Autofac.Extensions.DependencyInjection</span><span class="sxs-lookup"><span data-stu-id="e19ad-318">Autofac.Extensions.DependencyInjection</span></span>](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* <span data-ttu-id="e19ad-319">Configure o contêiner no `Startup.ConfigureServices` e retorne um `IServiceProvider`:</span><span class="sxs-lookup"><span data-stu-id="e19ad-319">Configure the container in `Startup.ConfigureServices` and return an `IServiceProvider`:</span></span>

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    <span data-ttu-id="e19ad-320">Para usar um contêiner de terceiros, `Startup.ConfigureServices` deve retornar `IServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="e19ad-320">To use a 3rd party container, `Startup.ConfigureServices` must return `IServiceProvider`.</span></span>

* <span data-ttu-id="e19ad-321">Configure o Autofac em `DefaultModule`:</span><span class="sxs-lookup"><span data-stu-id="e19ad-321">Configure Autofac in `DefaultModule`:</span></span>

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

<span data-ttu-id="e19ad-322">Em tempo de execução, o Autofac é usado para resolver tipos e injetar dependências.</span><span class="sxs-lookup"><span data-stu-id="e19ad-322">At runtime, Autofac is used to resolve types and inject dependencies.</span></span> <span data-ttu-id="e19ad-323">Para saber mais sobre como usar o Autofac com o ASP.NET Core, consulte a [documentação do Autofac](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span><span class="sxs-lookup"><span data-stu-id="e19ad-323">To learn more about using Autofac with ASP.NET Core, see the [Autofac documentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span></span>

### <a name="thread-safety"></a><span data-ttu-id="e19ad-324">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="e19ad-324">Thread safety</span></span>

<span data-ttu-id="e19ad-325">Os serviços Singleton precisam ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e19ad-325">Singleton services need to be thread safe.</span></span> <span data-ttu-id="e19ad-326">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ser thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="e19ad-326">If a singleton service has a dependency on a transient service, the transient service may also need to be thread safe depending how it's used by the singleton.</span></span>

<span data-ttu-id="e19ad-327">O método de fábrica de um único serviço, como o segundo argumento para [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e19ad-327">The factory method of single service, such as the second argument to [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), doesn't need to be thread-safe.</span></span> <span data-ttu-id="e19ad-328">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="e19ad-328">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="e19ad-329">Recomendações</span><span class="sxs-lookup"><span data-stu-id="e19ad-329">Recommendations</span></span>

* <span data-ttu-id="e19ad-330">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="e19ad-330">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="e19ad-331">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-331">C# does not support asynchronous constructors, therefore the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="e19ad-332">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-332">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="e19ad-333">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="e19ad-333">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="e19ad-334">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="e19ad-334">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e19ad-335">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="e19ad-335">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="e19ad-336">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="e19ad-336">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="e19ad-337">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="e19ad-337">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) for use elsewhere).</span></span>

* <span data-ttu-id="e19ad-338">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="e19ad-338">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="e19ad-339">Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI.</span><span class="sxs-lookup"><span data-stu-id="e19ad-339">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead.</span></span> <span data-ttu-id="e19ad-340">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="e19ad-340">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="e19ad-341">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="e19ad-341">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="e19ad-342">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span><span class="sxs-lookup"><span data-stu-id="e19ad-342">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span></span>

<span data-ttu-id="e19ad-343">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="e19ad-343">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="e19ad-344">As exceções são raras &mdash;principalmente casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="e19ad-344">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="e19ad-345">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="e19ad-345">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="e19ad-346">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="e19ad-346">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e19ad-347">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e19ad-347">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="e19ad-348">Como escrever um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="e19ad-348">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="e19ad-349">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="e19ad-349">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="e19ad-350">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="e19ad-350">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="e19ad-351">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e19ad-351">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
