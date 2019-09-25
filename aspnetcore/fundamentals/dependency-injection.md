---
title: Injeção de dependência no ASP.NET Core
author: guardrex
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/24/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: fefd0b9df71d5b0e7c30a31620292fd37eeecfa4
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248269"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="d98b7-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d98b7-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="d98b7-104">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d98b7-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d98b7-105">O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d98b7-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="d98b7-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d98b7-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="d98b7-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d98b7-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="d98b7-108">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d98b7-108">Overview of dependency injection</span></span>

<span data-ttu-id="d98b7-109">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="d98b7-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="d98b7-110">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="d98b7-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="d98b7-111">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="d98b7-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="d98b7-112">A classe `MyDependency` é uma dependência da classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="d98b7-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="d98b7-113">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="d98b7-114">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="d98b7-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="d98b7-115">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="d98b7-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="d98b7-116">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="d98b7-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="d98b7-117">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d98b7-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="d98b7-118">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="d98b7-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="d98b7-119">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="d98b7-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="d98b7-120">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d98b7-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="d98b7-121">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="d98b7-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="d98b7-122">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="d98b7-123">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="d98b7-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="d98b7-124">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d98b7-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="d98b7-125">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="d98b7-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="d98b7-126">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="d98b7-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="d98b7-127">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d98b7-127">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="d98b7-128">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="d98b7-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="d98b7-129">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="d98b7-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="d98b7-130">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="d98b7-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="d98b7-131">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="d98b7-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="d98b7-132">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="d98b7-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="d98b7-133">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="d98b7-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="d98b7-134">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="d98b7-135">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d98b7-136">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d98b7-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="d98b7-137">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="d98b7-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

<span data-ttu-id="d98b7-138">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="d98b7-139">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="d98b7-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="d98b7-140">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d98b7-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="d98b7-141">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="d98b7-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="d98b7-142">Por exemplo, `services.AddMvc()` adiciona os serviços exigidos pelo MVC e por Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d98b7-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="d98b7-143">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="d98b7-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="d98b7-144">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="d98b7-145">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="d98b7-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="d98b7-146">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="d98b7-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="d98b7-147">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="d98b7-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="d98b7-148">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="d98b7-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

## <a name="services-injected-into-startup"></a><span data-ttu-id="d98b7-149">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="d98b7-149">Services injected into Startup</span></span>

<span data-ttu-id="d98b7-150">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="d98b7-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="d98b7-151">Os serviços podem ser injetados `Startup.Configure`em:</span><span class="sxs-lookup"><span data-stu-id="d98b7-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="d98b7-152">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d98b7-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d98b7-153">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="d98b7-153">Framework-provided services</span></span>

<span data-ttu-id="d98b7-154">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d98b7-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="d98b7-155">Inicialmente, o `IServiceCollection` fornecido para `ConfigureServices` o tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d98b7-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="d98b7-156">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="d98b7-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="d98b7-157">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="d98b7-157">A small sample of framework-registered services is listed in the following table.</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="d98b7-158">Tipo de serviço</span><span class="sxs-lookup"><span data-stu-id="d98b7-158">Service Type</span></span> | <span data-ttu-id="d98b7-159">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="d98b7-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d98b7-160">Transitório</span><span class="sxs-lookup"><span data-stu-id="d98b7-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="d98b7-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="d98b7-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="d98b7-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="d98b7-164">Transitório</span><span class="sxs-lookup"><span data-stu-id="d98b7-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="d98b7-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="d98b7-166">Transitório</span><span class="sxs-lookup"><span data-stu-id="d98b7-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="d98b7-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="d98b7-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="d98b7-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="d98b7-170">Transitório</span><span class="sxs-lookup"><span data-stu-id="d98b7-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="d98b7-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="d98b7-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="d98b7-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-173">Singleton</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="d98b7-174">Tipo de serviço</span><span class="sxs-lookup"><span data-stu-id="d98b7-174">Service Type</span></span> | <span data-ttu-id="d98b7-175">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="d98b7-175">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="d98b7-176">Transitório</span><span class="sxs-lookup"><span data-stu-id="d98b7-176">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="d98b7-177">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-177">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="d98b7-178">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-178">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="d98b7-179">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-179">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="d98b7-180">Transitório</span><span class="sxs-lookup"><span data-stu-id="d98b7-180">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="d98b7-181">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-181">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="d98b7-182">Transitório</span><span class="sxs-lookup"><span data-stu-id="d98b7-182">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="d98b7-183">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-183">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="d98b7-184">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-184">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="d98b7-185">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-185">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="d98b7-186">Transitório</span><span class="sxs-lookup"><span data-stu-id="d98b7-186">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="d98b7-187">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-187">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="d98b7-188">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-188">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="d98b7-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-189">Singleton</span></span> |

::: moniker-end

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="d98b7-190">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="d98b7-190">Register additional services with extension methods</span></span>

<span data-ttu-id="d98b7-191">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-191">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="d98b7-192">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="d98b7-192">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="d98b7-193">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="d98b7-193">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="d98b7-194">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="d98b7-194">Service lifetimes</span></span>

<span data-ttu-id="d98b7-195">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="d98b7-195">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="d98b7-196">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="d98b7-196">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="d98b7-197">Transitório</span><span class="sxs-lookup"><span data-stu-id="d98b7-197">Transient</span></span>

<span data-ttu-id="d98b7-198">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-198">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="d98b7-199">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="d98b7-199">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="d98b7-200">Com escopo</span><span class="sxs-lookup"><span data-stu-id="d98b7-200">Scoped</span></span>

<span data-ttu-id="d98b7-201">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="d98b7-201">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="d98b7-202">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-202">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="d98b7-203">Não injete por meio de injeção de construtor porque isso força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="d98b7-203">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="d98b7-204">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="d98b7-204">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="d98b7-205">Singleton</span><span class="sxs-lookup"><span data-stu-id="d98b7-205">Singleton</span></span>

<span data-ttu-id="d98b7-206">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="d98b7-206">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="d98b7-207">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="d98b7-207">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="d98b7-208">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-208">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="d98b7-209">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="d98b7-209">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="d98b7-210">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="d98b7-210">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="d98b7-211">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="d98b7-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="d98b7-212">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="d98b7-212">Service registration methods</span></span>

<span data-ttu-id="d98b7-213">Cada método de extensão do registro do serviço oferece sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="d98b7-213">Each service registration extension method offers overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="d98b7-214">Método</span><span class="sxs-lookup"><span data-stu-id="d98b7-214">Method</span></span> | <span data-ttu-id="d98b7-215">Automático</span><span class="sxs-lookup"><span data-stu-id="d98b7-215">Automatic</span></span><br><span data-ttu-id="d98b7-216">objeto</span><span class="sxs-lookup"><span data-stu-id="d98b7-216">object</span></span><br><span data-ttu-id="d98b7-217">descarte</span><span class="sxs-lookup"><span data-stu-id="d98b7-217">disposal</span></span> | <span data-ttu-id="d98b7-218">Múltiplo</span><span class="sxs-lookup"><span data-stu-id="d98b7-218">Multiple</span></span><br><span data-ttu-id="d98b7-219">implementações</span><span class="sxs-lookup"><span data-stu-id="d98b7-219">implementations</span></span> | <span data-ttu-id="d98b7-220">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="d98b7-220">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="d98b7-221">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="d98b7-221">Example:</span></span><br>`services.AddScoped<IMyDep, MyDep>();` | <span data-ttu-id="d98b7-222">Sim</span><span class="sxs-lookup"><span data-stu-id="d98b7-222">Yes</span></span> | <span data-ttu-id="d98b7-223">Sim</span><span class="sxs-lookup"><span data-stu-id="d98b7-223">Yes</span></span> | <span data-ttu-id="d98b7-224">Não</span><span class="sxs-lookup"><span data-stu-id="d98b7-224">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="d98b7-225">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d98b7-225">Examples:</span></span><br>`services.AddScoped<IMyDep>(sp => new MyDep());`<br>`services.AddScoped<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="d98b7-226">Sim</span><span class="sxs-lookup"><span data-stu-id="d98b7-226">Yes</span></span> | <span data-ttu-id="d98b7-227">Sim</span><span class="sxs-lookup"><span data-stu-id="d98b7-227">Yes</span></span> | <span data-ttu-id="d98b7-228">Sim</span><span class="sxs-lookup"><span data-stu-id="d98b7-228">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="d98b7-229">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="d98b7-229">Example:</span></span><br>`services.AddScoped<MyDep>();` | <span data-ttu-id="d98b7-230">Sim</span><span class="sxs-lookup"><span data-stu-id="d98b7-230">Yes</span></span> | <span data-ttu-id="d98b7-231">Não</span><span class="sxs-lookup"><span data-stu-id="d98b7-231">No</span></span> | <span data-ttu-id="d98b7-232">Não</span><span class="sxs-lookup"><span data-stu-id="d98b7-232">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="d98b7-233">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d98b7-233">Examples:</span></span><br>`services.AddScoped<IMyDep>(new MyDep());`<br>`services.AddScoped<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="d98b7-234">Não</span><span class="sxs-lookup"><span data-stu-id="d98b7-234">No</span></span> | <span data-ttu-id="d98b7-235">Sim</span><span class="sxs-lookup"><span data-stu-id="d98b7-235">Yes</span></span> | <span data-ttu-id="d98b7-236">Sim</span><span class="sxs-lookup"><span data-stu-id="d98b7-236">Yes</span></span> |
| `Add{LIFETIME}(new {IMPLEMENTATION})`<br><span data-ttu-id="d98b7-237">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="d98b7-237">Examples:</span></span><br>`services.AddScoped(new MyDep());`<br>`services.AddScoped(new MyDep("A string!"));` | <span data-ttu-id="d98b7-238">Não</span><span class="sxs-lookup"><span data-stu-id="d98b7-238">No</span></span> | <span data-ttu-id="d98b7-239">Não</span><span class="sxs-lookup"><span data-stu-id="d98b7-239">No</span></span> | <span data-ttu-id="d98b7-240">Sim</span><span class="sxs-lookup"><span data-stu-id="d98b7-240">Yes</span></span> |

<span data-ttu-id="d98b7-241">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="d98b7-241">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="d98b7-242">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="d98b7-242">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="d98b7-243">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="d98b7-243">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="d98b7-244">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-244">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="d98b7-245">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="d98b7-245">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="d98b7-246">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="d98b7-246">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="d98b7-247">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="d98b7-247">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="d98b7-248">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-248">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="d98b7-249">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="d98b7-249">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="d98b7-250">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="d98b7-250">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="d98b7-251">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-251">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="d98b7-252">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-252">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="d98b7-253">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="d98b7-253">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="d98b7-254">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="d98b7-254">Constructor injection behavior</span></span>

<span data-ttu-id="d98b7-255">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="d98b7-255">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="d98b7-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permite a criação de objetos sem registro do serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d98b7-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="d98b7-257">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="d98b7-257">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="d98b7-258">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="d98b7-258">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="d98b7-259">Quando os serviços são resolvidos por `IServiceProvider` ou `ActivatorUtilities`, a injeção do construtor exige um construtor *público*.</span><span class="sxs-lookup"><span data-stu-id="d98b7-259">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="d98b7-260">Quando os serviços são resolvidos por `ActivatorUtilities`, a injeção de construtor exige a existência de apenas de um construtor aplicável.</span><span class="sxs-lookup"><span data-stu-id="d98b7-260">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="d98b7-261">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d98b7-261">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="d98b7-262">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="d98b7-262">Entity Framework contexts</span></span>

<span data-ttu-id="d98b7-263">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="d98b7-263">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="d98b7-264">O tempo de vida padrão terá escopo se um tempo de vida não for especificado por uma sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d98b7-264">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="d98b7-265">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-265">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="d98b7-266">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="d98b7-266">Lifetime and registration options</span></span>

<span data-ttu-id="d98b7-267">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-267">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="d98b7-268">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="d98b7-268">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="d98b7-269">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-269">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="d98b7-270">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="d98b7-270">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="d98b7-271">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="d98b7-271">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="d98b7-272">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="d98b7-272">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="d98b7-273">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-273">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="d98b7-274">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-274">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="d98b7-275">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-275">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="d98b7-276">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="d98b7-276">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="d98b7-277">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="d98b7-277">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="d98b7-278">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-278">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="d98b7-279">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="d98b7-279">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="d98b7-280">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-280">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="d98b7-281">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="d98b7-281">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="d98b7-282">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="d98b7-282">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="d98b7-283">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-283">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="d98b7-284">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="d98b7-284">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

<span data-ttu-id="d98b7-285">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="d98b7-285">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="d98b7-286">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="d98b7-286">**First request:**</span></span>

<span data-ttu-id="d98b7-287">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="d98b7-287">Controller operations:</span></span>

<span data-ttu-id="d98b7-288">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="d98b7-288">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="d98b7-289">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d98b7-289">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d98b7-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d98b7-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d98b7-291">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d98b7-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d98b7-292">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="d98b7-292">`OperationService` operations:</span></span>

<span data-ttu-id="d98b7-293">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="d98b7-293">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="d98b7-294">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="d98b7-294">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="d98b7-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d98b7-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d98b7-296">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d98b7-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d98b7-297">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="d98b7-297">**Second request:**</span></span>

<span data-ttu-id="d98b7-298">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="d98b7-298">Controller operations:</span></span>

<span data-ttu-id="d98b7-299">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="d98b7-299">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="d98b7-300">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d98b7-300">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d98b7-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d98b7-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d98b7-302">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d98b7-302">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d98b7-303">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="d98b7-303">`OperationService` operations:</span></span>

<span data-ttu-id="d98b7-304">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="d98b7-304">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="d98b7-305">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="d98b7-305">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="d98b7-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="d98b7-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="d98b7-307">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="d98b7-307">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="d98b7-308">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="d98b7-308">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="d98b7-309">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="d98b7-309">*Transient* objects are always different.</span></span> <span data-ttu-id="d98b7-310">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="d98b7-310">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="d98b7-311">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="d98b7-311">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="d98b7-312">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="d98b7-312">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="d98b7-313">Os objetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-313">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="d98b7-314">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="d98b7-314">Call services from main</span></span>

<span data-ttu-id="d98b7-315">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d98b7-315">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="d98b7-316">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="d98b7-316">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="d98b7-317">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d98b7-317">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

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
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
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
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

::: moniker-end

## <a name="scope-validation"></a><span data-ttu-id="d98b7-318">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="d98b7-318">Scope validation</span></span>

<span data-ttu-id="d98b7-319">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="d98b7-319">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d98b7-320">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="d98b7-320">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d98b7-321">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="d98b7-321">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d98b7-322">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="d98b7-322">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="d98b7-323">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="d98b7-323">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d98b7-324">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="d98b7-324">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d98b7-325">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="d98b7-325">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d98b7-326">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="d98b7-326">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d98b7-327">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="d98b7-327">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="d98b7-328">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="d98b7-328">Request Services</span></span>

<span data-ttu-id="d98b7-329">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="d98b7-329">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="d98b7-330">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d98b7-330">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="d98b7-331">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-331">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="d98b7-332">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="d98b7-332">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="d98b7-333">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="d98b7-333">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="d98b7-334">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="d98b7-334">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="d98b7-335">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="d98b7-335">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="d98b7-336">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d98b7-336">Design services for dependency injection</span></span>

<span data-ttu-id="d98b7-337">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="d98b7-337">Best practices are to:</span></span>

* <span data-ttu-id="d98b7-338">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="d98b7-338">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="d98b7-339">Evite chamadas de método estático com estado.</span><span class="sxs-lookup"><span data-stu-id="d98b7-339">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="d98b7-340">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="d98b7-340">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="d98b7-341">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="d98b7-341">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="d98b7-342">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="d98b7-342">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="d98b7-343">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="d98b7-343">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="d98b7-344">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="d98b7-344">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="d98b7-345">Lembre-se de que as classes de modelo de página de Razor Pages e as classes do controlador de MVC devem se concentrar em questões de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="d98b7-345">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="d98b7-346">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="d98b7-346">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="d98b7-347">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="d98b7-347">Disposal of services</span></span>

<span data-ttu-id="d98b7-348">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="d98b7-348">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="d98b7-349">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d98b7-349">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

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

## <a name="default-service-container-replacement"></a><span data-ttu-id="d98b7-350">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="d98b7-350">Default service container replacement</span></span>

<span data-ttu-id="d98b7-351">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="d98b7-351">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="d98b7-352">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="d98b7-352">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="d98b7-353">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="d98b7-353">Property injection</span></span>
* <span data-ttu-id="d98b7-354">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="d98b7-354">Injection based on name</span></span>
* <span data-ttu-id="d98b7-355">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="d98b7-355">Child containers</span></span>
* <span data-ttu-id="d98b7-356">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="d98b7-356">Custom lifetime management</span></span>
* <span data-ttu-id="d98b7-357">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="d98b7-357">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="d98b7-358">Os contêineres de terceiros a seguir podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d98b7-358">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="d98b7-359">Autofac</span><span class="sxs-lookup"><span data-stu-id="d98b7-359">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="d98b7-360">DryIoc</span><span class="sxs-lookup"><span data-stu-id="d98b7-360">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="d98b7-361">Gratuito</span><span class="sxs-lookup"><span data-stu-id="d98b7-361">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="d98b7-362">LightInject</span><span class="sxs-lookup"><span data-stu-id="d98b7-362">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="d98b7-363">Lamar</span><span class="sxs-lookup"><span data-stu-id="d98b7-363">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="d98b7-364">Stashbox</span><span class="sxs-lookup"><span data-stu-id="d98b7-364">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="d98b7-365">Unity</span><span class="sxs-lookup"><span data-stu-id="d98b7-365">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="d98b7-366">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="d98b7-366">Thread safety</span></span>

<span data-ttu-id="d98b7-367">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d98b7-367">Create thread-safe singleton services.</span></span> <span data-ttu-id="d98b7-368">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="d98b7-368">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="d98b7-369">O método de fábrica de um único serviço, como o segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d98b7-369">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="d98b7-370">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="d98b7-370">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d98b7-371">Recomendações</span><span class="sxs-lookup"><span data-stu-id="d98b7-371">Recommendations</span></span>

* <span data-ttu-id="d98b7-372">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="d98b7-372">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="d98b7-373">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-373">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="d98b7-374">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-374">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="d98b7-375">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="d98b7-375">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="d98b7-376">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d98b7-376">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d98b7-377">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="d98b7-377">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="d98b7-378">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="d98b7-378">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="d98b7-379">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="d98b7-379">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="d98b7-380">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="d98b7-380">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="d98b7-381">Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="d98b7-381">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="d98b7-382">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="d98b7-382">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="d98b7-383">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="d98b7-383">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="d98b7-384">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="d98b7-384">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="d98b7-385">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="d98b7-385">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="d98b7-386">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="d98b7-386">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="d98b7-387">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="d98b7-387">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="d98b7-388">As exceções são raras &mdash;principalmente casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="d98b7-388">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="d98b7-389">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="d98b7-389">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="d98b7-390">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="d98b7-390">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d98b7-391">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d98b7-391">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="d98b7-392">Como escrever um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d98b7-392">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="d98b7-393">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="d98b7-393">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="d98b7-394">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="d98b7-394">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="d98b7-395">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d98b7-395">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
