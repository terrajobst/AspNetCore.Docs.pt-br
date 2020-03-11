---
title: Injeção de dependência de Blazor ASP.NET Core
author: guardrex
description: Veja como Blazor aplicativos podem injetar serviços em componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658070"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="856b1-103">ASP.NET Core injeção de dependência mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="856b1-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="856b1-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="856b1-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="856b1-105">O mais incrivelmente dá suporte à [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="856b1-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="856b1-106">Os aplicativos podem usar serviços internos injetando-os em componentes.</span><span class="sxs-lookup"><span data-stu-id="856b1-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="856b1-107">Os aplicativos também podem definir e registrar serviços personalizados e torná-los disponíveis em todo o aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="856b1-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="856b1-108">DI é uma técnica para acessar os serviços configurados em um local central.</span><span class="sxs-lookup"><span data-stu-id="856b1-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="856b1-109">Isso pode ser útil em aplicativos mais incrivelmente para:</span><span class="sxs-lookup"><span data-stu-id="856b1-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="856b1-110">Compartilhe uma única instância de uma classe de serviço em vários componentes, conhecido como um serviço *singleton* .</span><span class="sxs-lookup"><span data-stu-id="856b1-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="856b1-111">Dissociar componentes de classes de serviço concretas usando abstrações de referência.</span><span class="sxs-lookup"><span data-stu-id="856b1-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="856b1-112">Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="856b1-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="856b1-113">A interface é implementada por uma classe de `DataAccess` concreta e registrada como um serviço no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="856b1-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="856b1-114">Quando um componente usa DI para receber uma implementação de `IDataAccess`, o componente não é acoplado ao tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="856b1-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="856b1-115">A implementação pode ser trocada, talvez para uma implementação fictícia em testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="856b1-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="856b1-116">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="856b1-116">Default services</span></span>

<span data-ttu-id="856b1-117">Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="856b1-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="856b1-118">Service</span><span class="sxs-lookup"><span data-stu-id="856b1-118">Service</span></span> | <span data-ttu-id="856b1-119">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="856b1-119">Lifetime</span></span> | <span data-ttu-id="856b1-120">Descrição</span><span class="sxs-lookup"><span data-stu-id="856b1-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="856b1-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="856b1-121">Singleton</span></span> | <span data-ttu-id="856b1-122">Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.</span><span class="sxs-lookup"><span data-stu-id="856b1-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="856b1-123">A instância do `HttpClient` em um aplicativo Webassembly mais incrivelmente usa o navegador para manipular o tráfego HTTP em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="856b1-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="856b1-124">Os aplicativos de servidor mais incrivelmente não incluem um `HttpClient` configurado como um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="856b1-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="856b1-125">Forneça um `HttpClient` para um aplicativo de servidor mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="856b1-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="856b1-126">Para obter mais informações, consulte <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="856b1-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="856b1-127">Singleton (Webassembly de mais incrivelmente)</span><span class="sxs-lookup"><span data-stu-id="856b1-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="856b1-128">Com escopo (servidor mais incrivelmente)</span><span class="sxs-lookup"><span data-stu-id="856b1-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="856b1-129">Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas.</span><span class="sxs-lookup"><span data-stu-id="856b1-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="856b1-130">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="856b1-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="856b1-131">Singleton (Webassembly de mais incrivelmente)</span><span class="sxs-lookup"><span data-stu-id="856b1-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="856b1-132">Com escopo (servidor mais incrivelmente)</span><span class="sxs-lookup"><span data-stu-id="856b1-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="856b1-133">Contém auxiliares para trabalhar com URIs e estado de navegação.</span><span class="sxs-lookup"><span data-stu-id="856b1-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="856b1-134">Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="856b1-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="856b1-135">Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela.</span><span class="sxs-lookup"><span data-stu-id="856b1-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="856b1-136">Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.</span><span class="sxs-lookup"><span data-stu-id="856b1-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="856b1-137">Adicionar serviços a um aplicativo</span><span class="sxs-lookup"><span data-stu-id="856b1-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="856b1-138">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="856b1-138">Blazor WebAssembly</span></span>

<span data-ttu-id="856b1-139">Configure serviços para a coleção de serviços do aplicativo no método `Main` de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="856b1-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="856b1-140">No exemplo a seguir, a implementação de `MyDependency` é registrada para `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="856b1-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="856b1-141">Depois que o host é criado, os serviços podem ser acessados do escopo de DI raiz antes de qualquer componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="856b1-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="856b1-142">Isso pode ser útil para executar a lógica de inicialização antes de renderizar o conteúdo:</span><span class="sxs-lookup"><span data-stu-id="856b1-142">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="856b1-143">O host também fornece uma instância de configuração central para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="856b1-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="856b1-144">Com base no exemplo anterior, a URL do serviço meteorológico é passada de uma fonte de configuração padrão (por exemplo, *appSettings. JSON*) para `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="856b1-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="856b1-145">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="856b1-145">Blazor Server</span></span>

<span data-ttu-id="856b1-146">Depois de criar um novo aplicativo, examine o método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="856b1-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="856b1-147">O método `ConfigureServices` é passado uma <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que é uma lista de objetos de descritor de serviço (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="856b1-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="856b1-148">Os serviços são adicionados fornecendo descritores de serviço à coleção de serviços.</span><span class="sxs-lookup"><span data-stu-id="856b1-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="856b1-149">O exemplo a seguir demonstra o conceito com a interface `IDataAccess` e sua implementação concreta `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="856b1-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="856b1-150">Tempo de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="856b1-150">Service lifetime</span></span>

<span data-ttu-id="856b1-151">Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="856b1-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="856b1-152">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="856b1-152">Lifetime</span></span> | <span data-ttu-id="856b1-153">Descrição</span><span class="sxs-lookup"><span data-stu-id="856b1-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="856b1-154"> aplicativos Webassembly não têm atualmente um conceito de escopos de DI.</span><span class="sxs-lookup"><span data-stu-id="856b1-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="856b1-155">os serviços registrados `Scoped`se comportam como serviços `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="856b1-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="856b1-156">No entanto, o modelo de hospedagem do servidor Blazor dá suporte ao tempo de vida `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="856b1-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="856b1-157">Em aplicativos do Blazor Server, um registro de serviço com escopo é definido para a *conexão*.</span><span class="sxs-lookup"><span data-stu-id="856b1-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="856b1-158">Por esse motivo, o uso de serviços com escopo é preferencial para serviços que devem ser delimitados para o usuário atual, mesmo que a intenção atual seja executar o lado do cliente no navegador.</span><span class="sxs-lookup"><span data-stu-id="856b1-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="856b1-159">DI cria uma *única instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="856b1-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="856b1-160">Todos os componentes que exigem um serviço de `Singleton` recebem uma instância do mesmo serviço.</span><span class="sxs-lookup"><span data-stu-id="856b1-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="856b1-161">Sempre que um componente Obtém uma instância de um serviço de `Transient` do contêiner de serviço, ele recebe uma *nova instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="856b1-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="856b1-162">O sistema de DI é baseado no sistema de injeção de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="856b1-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="856b1-163">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="856b1-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="856b1-164">Solicitar um serviço em um componente</span><span class="sxs-lookup"><span data-stu-id="856b1-164">Request a service in a component</span></span>

<span data-ttu-id="856b1-165">Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando o [\@injetar](xref:mvc/views/razor#inject) diretiva Razor.</span><span class="sxs-lookup"><span data-stu-id="856b1-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="856b1-166">`@inject` tem dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="856b1-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="856b1-167">Digite &ndash; o tipo de serviço a injetar.</span><span class="sxs-lookup"><span data-stu-id="856b1-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="856b1-168">Propriedade &ndash; o nome da propriedade que recebe o serviço de aplicativo injetado.</span><span class="sxs-lookup"><span data-stu-id="856b1-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="856b1-169">A propriedade não requer criação manual.</span><span class="sxs-lookup"><span data-stu-id="856b1-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="856b1-170">O compilador cria a propriedade.</span><span class="sxs-lookup"><span data-stu-id="856b1-170">The compiler creates the property.</span></span>

<span data-ttu-id="856b1-171">Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="856b1-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="856b1-172">Use várias instruções `@inject` para injetar serviços diferentes.</span><span class="sxs-lookup"><span data-stu-id="856b1-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="856b1-173">O exemplo a seguir mostra como usar `@inject`.</span><span class="sxs-lookup"><span data-stu-id="856b1-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="856b1-174">O serviço que implementa `Services.IDataAccess` é injetado na `DataRepository`de propriedades do componente.</span><span class="sxs-lookup"><span data-stu-id="856b1-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="856b1-175">Observe como o código está usando apenas a abstração de `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="856b1-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="856b1-176">Internamente, a propriedade gerada (`DataRepository`) usa o atributo `InjectAttribute`.</span><span class="sxs-lookup"><span data-stu-id="856b1-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="856b1-177">Normalmente, esse atributo não é usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="856b1-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="856b1-178">Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione manualmente o `InjectAttribute`:</span><span class="sxs-lookup"><span data-stu-id="856b1-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="856b1-179">Em componentes derivados da classe base, a diretiva `@inject` não é necessária.</span><span class="sxs-lookup"><span data-stu-id="856b1-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="856b1-180">O `InjectAttribute` da classe base é suficiente:</span><span class="sxs-lookup"><span data-stu-id="856b1-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="856b1-181">Usar DI em serviços</span><span class="sxs-lookup"><span data-stu-id="856b1-181">Use DI in services</span></span>

<span data-ttu-id="856b1-182">Serviços complexos podem exigir serviços adicionais.</span><span class="sxs-lookup"><span data-stu-id="856b1-182">Complex services might require additional services.</span></span> <span data-ttu-id="856b1-183">No exemplo anterior, `DataAccess` pode exigir o serviço de `HttpClient` padrão.</span><span class="sxs-lookup"><span data-stu-id="856b1-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="856b1-184">`@inject` (ou o `InjectAttribute`) não está disponível para uso em serviços.</span><span class="sxs-lookup"><span data-stu-id="856b1-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="856b1-185">A *injeção de Construtor* deve ser usada em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="856b1-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="856b1-186">Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço.</span><span class="sxs-lookup"><span data-stu-id="856b1-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="856b1-187">Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo.</span><span class="sxs-lookup"><span data-stu-id="856b1-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="856b1-188">Pré-requisitos para injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="856b1-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="856b1-189">Um construtor deve existir cujos argumentos podem ser todos atendidos por DI.</span><span class="sxs-lookup"><span data-stu-id="856b1-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="856b1-190">Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.</span><span class="sxs-lookup"><span data-stu-id="856b1-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="856b1-191">O Construtor aplicável deve ser *público*.</span><span class="sxs-lookup"><span data-stu-id="856b1-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="856b1-192">Um Construtor aplicável deve existir.</span><span class="sxs-lookup"><span data-stu-id="856b1-192">One applicable constructor must exist.</span></span> <span data-ttu-id="856b1-193">No caso de uma ambiguidade, DI gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="856b1-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="856b1-194">Classes de componente base do utilitário para gerenciar um escopo de DI</span><span class="sxs-lookup"><span data-stu-id="856b1-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="856b1-195">Em aplicativos ASP.NET Core, os serviços com escopo normalmente são incluídos no escopo da solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="856b1-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="856b1-196">Depois que a solicitação for concluída, todos os serviços com escopo ou transitórios serão descartados pelo sistema de DI.</span><span class="sxs-lookup"><span data-stu-id="856b1-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="856b1-197">Em aplicativos Blazor Server, o escopo da solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e no escopo que vivem muito mais do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="856b1-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="856b1-198">Em Blazor aplicativos Webassembly, os serviços registrados com um tempo de vida no escopo são tratados como singletons, de modo que eles vivem mais do que os serviços com escopo em aplicativos típicos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="856b1-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="856b1-199">Uma abordagem que limita um tempo de vida de serviço em Blazor aplicativos é o uso do tipo `OwningComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="856b1-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="856b1-200">`OwningComponentBase` é um tipo abstrato derivado de `ComponentBase` que cria um escopo de DI correspondente ao tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="856b1-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="856b1-201">Usando esse escopo, é possível usar os serviços de DI com um tempo de vida de escopo e tê-los ativos, desde que o componente.</span><span class="sxs-lookup"><span data-stu-id="856b1-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="856b1-202">Quando o componente é destruído, os serviços do provedor de serviço no escopo do componente também são descartados.</span><span class="sxs-lookup"><span data-stu-id="856b1-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="856b1-203">Isso pode ser útil para serviços que:</span><span class="sxs-lookup"><span data-stu-id="856b1-203">This can be useful for services that:</span></span>

* <span data-ttu-id="856b1-204">Deve ser reutilizado dentro de um componente, pois o tempo de vida transitório é inadequado.</span><span class="sxs-lookup"><span data-stu-id="856b1-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="856b1-205">Não devem ser compartilhados entre componentes, pois o tempo de vida singleton é inadequado.</span><span class="sxs-lookup"><span data-stu-id="856b1-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="856b1-206">Duas versões do tipo de `OwningComponentBase` estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="856b1-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="856b1-207">`OwningComponentBase` é um filho abstrato e descartável do tipo de `ComponentBase` com uma propriedade `ScopedServices` protegida do tipo `IServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="856b1-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="856b1-208">Esse provedor pode ser usado para resolver serviços que têm o escopo definido para o tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="856b1-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="856b1-209">Os serviços de DI injetados no componente usando `@inject` ou a `InjectAttribute` (`[Inject]`) não são criados no escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="856b1-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="856b1-210">Para usar o escopo do componente, os serviços devem ser resolvidos usando `ScopedServices.GetRequiredService` ou `ScopedServices.GetService`.</span><span class="sxs-lookup"><span data-stu-id="856b1-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="856b1-211">Todos os serviços resolvidos usando o provedor de `ScopedServices` têm suas dependências fornecidas do mesmo escopo.</span><span class="sxs-lookup"><span data-stu-id="856b1-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="856b1-212">`OwningComponentBase<T>` deriva de `OwningComponentBase` e adiciona uma propriedade `Service` que retorna uma instância de `T` do provedor de injeção de escopo.</span><span class="sxs-lookup"><span data-stu-id="856b1-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="856b1-213">Esse tipo é uma maneira conveniente de acessar serviços com escopo sem usar uma instância do `IServiceProvider` quando há um serviço primário que o aplicativo requer do contêiner DI usando o escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="856b1-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="856b1-214">A propriedade `ScopedServices` está disponível, portanto, o aplicativo pode obter serviços de outros tipos, se necessário.</span><span class="sxs-lookup"><span data-stu-id="856b1-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="856b1-215">Uso de Entity Framework DbContext de DI</span><span class="sxs-lookup"><span data-stu-id="856b1-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="856b1-216">Um tipo de serviço comum a ser recuperado de DI em aplicativos Web é Entity Framework (EF) `DbContext` objetos.</span><span class="sxs-lookup"><span data-stu-id="856b1-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="856b1-217">O registro de serviços do EF usando `IServiceCollection.AddDbContext` adiciona o `DbContext` como um serviço com escopo por padrão.</span><span class="sxs-lookup"><span data-stu-id="856b1-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="856b1-218">O registro como um serviço com escopo pode levar a problemas em aplicativos Blazor porque faz com que `DbContext` instâncias sejam de longa duração e compartilhadas entre o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="856b1-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="856b1-219">`DbContext` não é thread-safe e não deve ser usado simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="856b1-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="856b1-220">Dependendo do aplicativo, o uso de `OwningComponentBase` para limitar o escopo de um `DbContext` a um único componente *pode* resolver o problema.</span><span class="sxs-lookup"><span data-stu-id="856b1-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="856b1-221">Se um componente não usar um `DbContext` em paralelo, derivar o componente do `OwningComponentBase` e recuperar o `DbContext` do `ScopedServices` será suficiente porque garante que:</span><span class="sxs-lookup"><span data-stu-id="856b1-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="856b1-222">Componentes separados não compartilham um `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="856b1-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="856b1-223">O `DbContext` só fica desde que o componente dependa dele.</span><span class="sxs-lookup"><span data-stu-id="856b1-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="856b1-224">Se um único componente puder usar um `DbContext` simultaneamente (por exemplo, sempre que um usuário selecionar um botão), mesmo usando `OwningComponentBase` não evitará problemas com operações simultâneas do EF.</span><span class="sxs-lookup"><span data-stu-id="856b1-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="856b1-225">Nesse caso, use um `DbContext` diferente para cada operação lógica do EF.</span><span class="sxs-lookup"><span data-stu-id="856b1-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="856b1-226">Use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="856b1-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="856b1-227">Crie o `DbContext` diretamente usando `DbContextOptions<TContext>` como um argumento, que pode ser recuperado de DI e seja thread-safe.</span><span class="sxs-lookup"><span data-stu-id="856b1-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="856b1-228">Registre o `DbContext` no contêiner de serviço com um tempo de vida transitório:</span><span class="sxs-lookup"><span data-stu-id="856b1-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="856b1-229">Ao registrar o contexto, use `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="856b1-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="856b1-230">O método de extensão `AddDbContext` usa dois parâmetros opcionais do tipo `ServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="856b1-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="856b1-231">Para usar essa abordagem, somente o parâmetro `contextLifetime` precisa ser `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="856b1-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="856b1-232">`optionsLifetime` pode manter seu valor padrão de `ServiceLifetime.Scoped`.</span><span class="sxs-lookup"><span data-stu-id="856b1-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="856b1-233">O `DbContext` transitório pode ser injetado como normal (usando `@inject`) em componentes que não executarão várias operações de EF em paralelo.</span><span class="sxs-lookup"><span data-stu-id="856b1-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="856b1-234">Os que podem executar várias operações de EF simultaneamente podem solicitar objetos `DbContext` separados para cada operação em paralelo usando `IServiceProvider.GetRequiredService`.</span><span class="sxs-lookup"><span data-stu-id="856b1-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="856b1-235">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="856b1-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
