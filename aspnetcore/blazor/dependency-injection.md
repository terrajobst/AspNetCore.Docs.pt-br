---
title: Injeção de dependência de Blazor ASP.NET Core
author: guardrex
description: Veja como Blazor aplicativos podem injetar serviços em componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 6930d721f04fd5f7cad2ba472724497a157fda0f
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159970"
---
# <a name="aspnet-core-opno-locblazor-dependency-injection"></a><span data-ttu-id="5d412-103">Injeção de dependência de Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5d412-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="5d412-104">Por [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="5d412-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="5d412-105"> dá suporte à [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5d412-105"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5d412-106">Os aplicativos podem usar serviços internos injetando-os em componentes.</span><span class="sxs-lookup"><span data-stu-id="5d412-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="5d412-107">Os aplicativos também podem definir e registrar serviços personalizados e torná-los disponíveis em todo o aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="5d412-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="5d412-108">DI é uma técnica para acessar os serviços configurados em um local central.</span><span class="sxs-lookup"><span data-stu-id="5d412-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="5d412-109">Isso pode ser útil em aplicativos Blazor para:</span><span class="sxs-lookup"><span data-stu-id="5d412-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="5d412-110">Compartilhe uma única instância de uma classe de serviço em vários componentes, conhecido como um serviço *singleton* .</span><span class="sxs-lookup"><span data-stu-id="5d412-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="5d412-111">Dissociar componentes de classes de serviço concretas usando abstrações de referência.</span><span class="sxs-lookup"><span data-stu-id="5d412-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="5d412-112">Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d412-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="5d412-113">A interface é implementada por uma classe de `DataAccess` concreta e registrada como um serviço no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d412-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="5d412-114">Quando um componente usa DI para receber uma implementação de `IDataAccess`, o componente não é acoplado ao tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="5d412-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="5d412-115">A implementação pode ser trocada, talvez para uma implementação fictícia em testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="5d412-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="5d412-116">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="5d412-116">Default services</span></span>

<span data-ttu-id="5d412-117">Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5d412-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="5d412-118">Service</span><span class="sxs-lookup"><span data-stu-id="5d412-118">Service</span></span> | <span data-ttu-id="5d412-119">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="5d412-119">Lifetime</span></span> | <span data-ttu-id="5d412-120">Descrição</span><span class="sxs-lookup"><span data-stu-id="5d412-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="5d412-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="5d412-121">Singleton</span></span> | <span data-ttu-id="5d412-122">Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.</span><span class="sxs-lookup"><span data-stu-id="5d412-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="5d412-123">A instância do `HttpClient` em um aplicativo Webassembly Blazor usa o navegador para manipular o tráfego HTTP em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="5d412-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="5d412-124">os aplicativos do Blazor Server não incluem uma `HttpClient` configurada como um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="5d412-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="5d412-125">Forneça um `HttpClient` para um aplicativo Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="5d412-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="5d412-126">Para obter mais informações, consulte <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="5d412-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="5d412-127">Singleton (Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="5d412-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="5d412-128">Com escopo (servidorBlazor)</span><span class="sxs-lookup"><span data-stu-id="5d412-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="5d412-129">Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas.</span><span class="sxs-lookup"><span data-stu-id="5d412-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="5d412-130">Para obter mais informações, consulte <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="5d412-130">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="5d412-131">Singleton (Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="5d412-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="5d412-132">Com escopo (servidorBlazor)</span><span class="sxs-lookup"><span data-stu-id="5d412-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="5d412-133">Contém auxiliares para trabalhar com URIs e estado de navegação.</span><span class="sxs-lookup"><span data-stu-id="5d412-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="5d412-134">Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="5d412-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="5d412-135">Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela.</span><span class="sxs-lookup"><span data-stu-id="5d412-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="5d412-136">Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.</span><span class="sxs-lookup"><span data-stu-id="5d412-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="5d412-137">Adicionar serviços a um aplicativo</span><span class="sxs-lookup"><span data-stu-id="5d412-137">Add services to an app</span></span>

<span data-ttu-id="5d412-138">Depois de criar um novo aplicativo, examine o método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5d412-138">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="5d412-139">O método `ConfigureServices` é passado uma <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que é uma lista de objetos de descritor de serviço (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="5d412-139">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="5d412-140">Os serviços são adicionados fornecendo descritores de serviço à coleção de serviços.</span><span class="sxs-lookup"><span data-stu-id="5d412-140">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="5d412-141">O exemplo a seguir demonstra o conceito com a interface `IDataAccess` e sua implementação concreta `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="5d412-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="5d412-142">Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="5d412-142">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="5d412-143">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="5d412-143">Lifetime</span></span> | <span data-ttu-id="5d412-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="5d412-144">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="5d412-145"> aplicativos Webassembly não têm atualmente um conceito de escopos de DI.</span><span class="sxs-lookup"><span data-stu-id="5d412-145"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="5d412-146">os serviços registrados `Scoped`se comportam como serviços `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="5d412-146">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="5d412-147">No entanto, o modelo de hospedagem do servidor Blazor dá suporte ao tempo de vida `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="5d412-147">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="5d412-148">Em aplicativos do Blazor Server, um registro de serviço com escopo é definido para a *conexão*.</span><span class="sxs-lookup"><span data-stu-id="5d412-148">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="5d412-149">Por esse motivo, o uso de serviços com escopo é preferencial para serviços que devem ser delimitados para o usuário atual, mesmo que a intenção atual seja executar o lado do cliente no navegador.</span><span class="sxs-lookup"><span data-stu-id="5d412-149">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="5d412-150">DI cria uma *única instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="5d412-150">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="5d412-151">Todos os componentes que exigem um serviço de `Singleton` recebem uma instância do mesmo serviço.</span><span class="sxs-lookup"><span data-stu-id="5d412-151">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="5d412-152">Sempre que um componente Obtém uma instância de um serviço de `Transient` do contêiner de serviço, ele recebe uma *nova instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="5d412-152">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="5d412-153">O sistema de DI é baseado no sistema de injeção de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d412-153">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="5d412-154">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5d412-154">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="5d412-155">Solicitar um serviço em um componente</span><span class="sxs-lookup"><span data-stu-id="5d412-155">Request a service in a component</span></span>

<span data-ttu-id="5d412-156">Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando o [\@injetar](xref:mvc/views/razor#inject) diretiva Razor.</span><span class="sxs-lookup"><span data-stu-id="5d412-156">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="5d412-157">`@inject` tem dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="5d412-157">`@inject` has two parameters:</span></span>

* <span data-ttu-id="5d412-158">Digite &ndash; o tipo de serviço a injetar.</span><span class="sxs-lookup"><span data-stu-id="5d412-158">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="5d412-159">Propriedade &ndash; o nome da propriedade que recebe o serviço de aplicativo injetado.</span><span class="sxs-lookup"><span data-stu-id="5d412-159">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="5d412-160">A propriedade não requer criação manual.</span><span class="sxs-lookup"><span data-stu-id="5d412-160">The property doesn't require manual creation.</span></span> <span data-ttu-id="5d412-161">O compilador cria a propriedade.</span><span class="sxs-lookup"><span data-stu-id="5d412-161">The compiler creates the property.</span></span>

<span data-ttu-id="5d412-162">Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5d412-162">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="5d412-163">Use várias instruções `@inject` para injetar serviços diferentes.</span><span class="sxs-lookup"><span data-stu-id="5d412-163">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="5d412-164">O exemplo a seguir mostra como usar `@inject`.</span><span class="sxs-lookup"><span data-stu-id="5d412-164">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="5d412-165">O serviço que implementa `Services.IDataAccess` é injetado na `DataRepository`de propriedades do componente.</span><span class="sxs-lookup"><span data-stu-id="5d412-165">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="5d412-166">Observe como o código está usando apenas a abstração de `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="5d412-166">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="5d412-167">Internamente, a propriedade gerada (`DataRepository`) usa o atributo `InjectAttribute`.</span><span class="sxs-lookup"><span data-stu-id="5d412-167">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="5d412-168">Normalmente, esse atributo não é usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="5d412-168">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="5d412-169">Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione manualmente o `InjectAttribute`:</span><span class="sxs-lookup"><span data-stu-id="5d412-169">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="5d412-170">Em componentes derivados da classe base, a diretiva `@inject` não é necessária.</span><span class="sxs-lookup"><span data-stu-id="5d412-170">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="5d412-171">O `InjectAttribute` da classe base é suficiente:</span><span class="sxs-lookup"><span data-stu-id="5d412-171">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="5d412-172">Usar DI em serviços</span><span class="sxs-lookup"><span data-stu-id="5d412-172">Use DI in services</span></span>

<span data-ttu-id="5d412-173">Serviços complexos podem exigir serviços adicionais.</span><span class="sxs-lookup"><span data-stu-id="5d412-173">Complex services might require additional services.</span></span> <span data-ttu-id="5d412-174">No exemplo anterior, `DataAccess` pode exigir o serviço de `HttpClient` padrão.</span><span class="sxs-lookup"><span data-stu-id="5d412-174">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="5d412-175">`@inject` (ou o `InjectAttribute`) não está disponível para uso em serviços.</span><span class="sxs-lookup"><span data-stu-id="5d412-175">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="5d412-176">A *injeção de Construtor* deve ser usada em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="5d412-176">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="5d412-177">Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço.</span><span class="sxs-lookup"><span data-stu-id="5d412-177">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="5d412-178">Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo.</span><span class="sxs-lookup"><span data-stu-id="5d412-178">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="5d412-179">Pré-requisitos para injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="5d412-179">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="5d412-180">Um construtor deve existir cujos argumentos podem ser todos atendidos por DI.</span><span class="sxs-lookup"><span data-stu-id="5d412-180">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="5d412-181">Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.</span><span class="sxs-lookup"><span data-stu-id="5d412-181">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="5d412-182">O Construtor aplicável deve ser *público*.</span><span class="sxs-lookup"><span data-stu-id="5d412-182">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="5d412-183">Um Construtor aplicável deve existir.</span><span class="sxs-lookup"><span data-stu-id="5d412-183">One applicable constructor must exist.</span></span> <span data-ttu-id="5d412-184">No caso de uma ambiguidade, DI gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="5d412-184">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="5d412-185">Classes de componente base do utilitário para gerenciar um escopo de DI</span><span class="sxs-lookup"><span data-stu-id="5d412-185">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="5d412-186">Em aplicativos ASP.NET Core, os serviços com escopo normalmente são incluídos no escopo da solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="5d412-186">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="5d412-187">Depois que a solicitação for concluída, todos os serviços com escopo ou transitórios serão descartados pelo sistema de DI.</span><span class="sxs-lookup"><span data-stu-id="5d412-187">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="5d412-188">Em aplicativos Blazor Server, o escopo da solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e no escopo que vivem muito mais do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="5d412-188">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="5d412-189">Para os serviços de escopo até o tempo de vida de um componente, o pode usar as classes base `OwningComponentBase` e `OwningComponentBase<TService>`.</span><span class="sxs-lookup"><span data-stu-id="5d412-189">To scope services to the lifetime of a component, can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="5d412-190">Essas classes base expõem uma propriedade `ScopedServices` do tipo `IServiceProvider` que resolve serviços que estão no escopo do tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="5d412-190">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="5d412-191">Para criar um componente que herda de uma classe base no Razor, use a diretiva `@inherits`.</span><span class="sxs-lookup"><span data-stu-id="5d412-191">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

```razor
@page "/users"
@attribute [Authorize]
@inherits OwningComponentBase<Data.ApplicationDbContext>

<h1>Users (@Service.Users.Count())</h1>
<ul>
    @foreach (var user in Service.Users)
    {
        <li>@user.UserName</li>
    }
</ul>
```

> [!NOTE]
> <span data-ttu-id="5d412-192">Os serviços injetados no componente usando `@inject` ou o `InjectAttribute` não são criados no escopo do componente e estão vinculados ao escopo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="5d412-192">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5d412-193">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5d412-193">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
