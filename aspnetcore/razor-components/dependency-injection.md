---
title: Injeção de dependência de componentes do Razor
author: guardrex
description: Veja como Blazor e Razor componentes de aplicativos podem usar os serviços, injetando-as em componentes.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
uid: razor-components/dependency-injection
ms.openlocfilehash: 40aec2e3a5032039c7d921f67d7d333b03c07fb1
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "59515330"
---
# <a name="razor-components-dependency-injection"></a><span data-ttu-id="b6446-103">Injeção de dependência de componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="b6446-103">Razor Components dependency injection</span></span>

<span data-ttu-id="b6446-104">Por [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="b6446-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="b6446-105">Dá suporte a componentes do Razor [injeção de dependência (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b6446-105">Razor Components supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b6446-106">Os aplicativos podem usar os serviços internos, injetando-as em componentes.</span><span class="sxs-lookup"><span data-stu-id="b6446-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="b6446-107">Aplicativos também podem definir e registrar os serviços personalizados e disponibilizá-los em todo o aplicativo por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="b6446-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="b6446-108">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="b6446-108">Dependency injection</span></span>

<span data-ttu-id="b6446-109">Injeção de dependência é uma técnica para acessar os serviços configurados em um local central.</span><span class="sxs-lookup"><span data-stu-id="b6446-109">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="b6446-110">Isso pode ser útil em aplicativos de componentes do Razor para:</span><span class="sxs-lookup"><span data-stu-id="b6446-110">This can be useful in Razor Components apps to:</span></span>

* <span data-ttu-id="b6446-111">Compartilhar uma única instância de uma classe de serviço em vários componentes, conhecidos como um *singleton* service.</span><span class="sxs-lookup"><span data-stu-id="b6446-111">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="b6446-112">Desacoplar componentes das classes de serviço concreto usando abstrações de referência.</span><span class="sxs-lookup"><span data-stu-id="b6446-112">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="b6446-113">Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6446-113">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="b6446-114">A interface é implementada por um concreto `DataAccess` de classe e registrado como um serviço no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6446-114">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="b6446-115">Quando um componente usa DI para receber um `IDataAccess` implementação, o componente não está acoplada ao tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="b6446-115">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="b6446-116">A implementação pode ser trocada, talvez uma implementação fictícia em testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="b6446-116">The implementation can be swapped, perhaps to a mock implementation in unit tests.</span></span>

<span data-ttu-id="b6446-117">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b6446-117">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="add-services-to-di"></a><span data-ttu-id="b6446-118">Adicionar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="b6446-118">Add services to DI</span></span>

<span data-ttu-id="b6446-119">Depois de criar um novo aplicativo, examine o `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="b6446-119">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="b6446-120">O `ConfigureServices` método recebe um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que é uma lista de objetos do descritor de serviço (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="b6446-120">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="b6446-121">Serviços são adicionados, fornecendo os descritores de serviço para a coleção de serviço.</span><span class="sxs-lookup"><span data-stu-id="b6446-121">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="b6446-122">O exemplo a seguir demonstra o conceito com o `IDataAccess` interface e sua implementação concreta `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="b6446-122">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="b6446-123">Serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="b6446-123">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="b6446-124">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="b6446-124">Lifetime</span></span> | <span data-ttu-id="b6446-125">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6446-125">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="b6446-126">Injeção de dependência cria uma *única instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="b6446-126">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="b6446-127">Todos os componentes que exigem um `Singleton` serviço recebe uma instância do mesmo serviço.</span><span class="sxs-lookup"><span data-stu-id="b6446-127">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="b6446-128">Sempre que um componente obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe um *nova instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="b6446-128">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="b6446-129">Blazor do lado do cliente atualmente não tem o conceito de escopos de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="b6446-129">Client-side Blazor doesn't currently have the concept of DI scopes.</span></span> <span data-ttu-id="b6446-130">`Scoped` se comporta como `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="b6446-130">`Scoped` behaves like `Singleton`.</span></span> <span data-ttu-id="b6446-131">No entanto, os componentes do ASP.NET Core Razor suportam o `Scoped` tempo de vida.</span><span class="sxs-lookup"><span data-stu-id="b6446-131">However, ASP.NET Core Razor Components support the `Scoped` lifetime.</span></span> <span data-ttu-id="b6446-132">Em um componente do Razor, um registro de serviço com escopo está no escopo para a conexão.</span><span class="sxs-lookup"><span data-stu-id="b6446-132">In a Razor Component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="b6446-133">Por esse motivo, usar os serviços com escopo é preferencial para os serviços que devem ser direcionados para o usuário atual, mesmo se a intenção atual é executar o lado do cliente no navegador.</span><span class="sxs-lookup"><span data-stu-id="b6446-133">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |

<span data-ttu-id="b6446-134">O sistema de DI é baseado no sistema de DI no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6446-134">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="b6446-135">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b6446-135">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="default-services"></a><span data-ttu-id="b6446-136">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="b6446-136">Default services</span></span>

<span data-ttu-id="b6446-137">Serviços padrão são automaticamente adicionados à coleção de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6446-137">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="b6446-138">Serviço</span><span class="sxs-lookup"><span data-stu-id="b6446-138">Service</span></span> | <span data-ttu-id="b6446-139">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6446-139">Description</span></span> |
| ------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="b6446-140">Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI (singleton).</span><span class="sxs-lookup"><span data-stu-id="b6446-140">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI (singleton).</span></span> <span data-ttu-id="b6446-141">Observe que esta instância do `HttpClient` usa o navegador para tratar o tráfego HTTP em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="b6446-141">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="b6446-142">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) é definida automaticamente como o prefixo URI base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b6446-142">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="b6446-143">`HttpClient` é fornecida apenas para aplicativos do lado do cliente Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6446-143">`HttpClient` is only provided to client-side Blazor apps.</span></span> |
| `IJSRuntime` | <span data-ttu-id="b6446-144">Representa uma instância de um tempo de execução do JavaScript para o qual chamadas podem ser distribuídas.</span><span class="sxs-lookup"><span data-stu-id="b6446-144">Represents an instance of a JavaScript runtime to which calls may be dispatched.</span></span> <span data-ttu-id="b6446-145">Para obter mais informações, consulte <xref:razor-components/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="b6446-145">For more information, see <xref:razor-components/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="b6446-146">Contém os auxiliares para trabalhar com o estado de navegação e URIs (singleton).</span><span class="sxs-lookup"><span data-stu-id="b6446-146">Contains helpers for working with URIs and navigation state (singleton).</span></span> <span data-ttu-id="b6446-147">`IUriHelper` é fornecido para aplicativos Blazor e componentes do Razor.</span><span class="sxs-lookup"><span data-stu-id="b6446-147">`IUriHelper` is provided to both Blazor and Razor Components apps.</span></span> |

<span data-ttu-id="b6446-148">É possível usar um provedor de serviço personalizado em vez do provedor de serviço padrão adicionado pelo modelo padrão.</span><span class="sxs-lookup"><span data-stu-id="b6446-148">It's possible to use a custom service provider instead of the default service provider added by the default template.</span></span> <span data-ttu-id="b6446-149">Um provedor de serviços personalizados automaticamente não fornece os serviços padrão listados na tabela.</span><span class="sxs-lookup"><span data-stu-id="b6446-149">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="b6446-150">Se você usa um provedor de serviço personalizado e exige qualquer um dos serviços mostrados na tabela, adicione os serviços necessários para o novo provedor de serviço.</span><span class="sxs-lookup"><span data-stu-id="b6446-150">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="b6446-151">Um serviço em um componente de solicitação</span><span class="sxs-lookup"><span data-stu-id="b6446-151">Request a service in a component</span></span>

<span data-ttu-id="b6446-152">Depois que os serviços são adicionados à coleção de serviços, inserir os serviços em modelos do Razor dos componentes usando o [ @inject ](xref:mvc/views/razor#section-4) diretiva do Razor.</span><span class="sxs-lookup"><span data-stu-id="b6446-152">After services are added to the service collection, inject the services into the components' Razor templates using the [@inject](xref:mvc/views/razor#section-4) Razor directive.</span></span> <span data-ttu-id="b6446-153">`@inject` tem dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="b6446-153">`@inject` has two parameters:</span></span>

* <span data-ttu-id="b6446-154">Nome do tipo: O tipo do serviço para injetar.</span><span class="sxs-lookup"><span data-stu-id="b6446-154">Type name: The type of the service to inject.</span></span>
* <span data-ttu-id="b6446-155">Nome da propriedade: O nome da propriedade que está recebendo o serviço de aplicativo injetado.</span><span class="sxs-lookup"><span data-stu-id="b6446-155">Property name: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="b6446-156">Observe que a propriedade não exige a criação manual.</span><span class="sxs-lookup"><span data-stu-id="b6446-156">Note that the property doesn't require manual creation.</span></span> <span data-ttu-id="b6446-157">O compilador cria a propriedade.</span><span class="sxs-lookup"><span data-stu-id="b6446-157">The compiler creates the property.</span></span>

<span data-ttu-id="b6446-158">Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b6446-158">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="b6446-159">Use várias `@inject` instruções para inserir serviços diferentes.</span><span class="sxs-lookup"><span data-stu-id="b6446-159">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="b6446-160">O exemplo a seguir mostra como usar `@inject`.</span><span class="sxs-lookup"><span data-stu-id="b6446-160">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="b6446-161">O serviço que implementa `Services.IDataAccess` é injetado na propriedade do componente `DataRepository`.</span><span class="sxs-lookup"><span data-stu-id="b6446-161">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="b6446-162">Observe como o código está usando apenas o `IDataAccess` abstração:</span><span class="sxs-lookup"><span data-stu-id="b6446-162">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.cshtml?highlight=2-3,23)]

<span data-ttu-id="b6446-163">Internamente, a propriedade gerada (`DataRepository`) é decorado com o `InjectAttribute` atributo.</span><span class="sxs-lookup"><span data-stu-id="b6446-163">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="b6446-164">Normalmente, esse atributo não é usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="b6446-164">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="b6446-165">Se uma classe base é necessária para componentes e injetadas propriedades também são necessárias para a classe base, `InjectAttribute` podem ser adicionadas manualmente:</span><span class="sxs-lookup"><span data-stu-id="b6446-165">If a base class is required for components and injected properties are also required for the base class, `InjectAttribute` can be manually added:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // Dependency injection works even if using the
    // InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="b6446-166">Em componentes derivados da classe base, o `@inject` diretiva não é necessária.</span><span class="sxs-lookup"><span data-stu-id="b6446-166">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="b6446-167">O `InjectAttribute` da classe base é suficiente:</span><span class="sxs-lookup"><span data-stu-id="b6446-167">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="dependency-injection-in-services"></a><span data-ttu-id="b6446-168">Injeção de dependência nos serviços</span><span class="sxs-lookup"><span data-stu-id="b6446-168">Dependency injection in services</span></span>

<span data-ttu-id="b6446-169">Serviços complexos podem exigir serviços adicionais.</span><span class="sxs-lookup"><span data-stu-id="b6446-169">Complex services might require additional services.</span></span> <span data-ttu-id="b6446-170">No exemplo anterior, `DataAccess` pode exigir o `HttpClient` serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="b6446-170">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="b6446-171">`@inject` (ou o `InjectAttribute`) não está disponível para uso em serviços.</span><span class="sxs-lookup"><span data-stu-id="b6446-171">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="b6446-172">*Injeção de construtor* deve ser usado em vez disso.</span><span class="sxs-lookup"><span data-stu-id="b6446-172">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="b6446-173">Os serviços necessários são adicionados, adicionando parâmetros ao construtor do serviço.</span><span class="sxs-lookup"><span data-stu-id="b6446-173">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="b6446-174">Quando a injeção de dependência cria o serviço, ele reconhece os serviços que ele requer no construtor e fornece-os adequadamente.</span><span class="sxs-lookup"><span data-stu-id="b6446-174">When dependency injection creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="b6446-175">Pré-requisitos para injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="b6446-175">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="b6446-176">Deve haver um construtor cujos argumentos podem ser atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="b6446-176">There must be one constructor whose arguments can all be fulfilled by dependency injection.</span></span> <span data-ttu-id="b6446-177">Observe que os parâmetros adicionais não cobertos pela DI sejam permitidos se especificar os valores padrão.</span><span class="sxs-lookup"><span data-stu-id="b6446-177">Note that additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="b6446-178">O construtor aplicável deve ser *público*.</span><span class="sxs-lookup"><span data-stu-id="b6446-178">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="b6446-179">Deve haver apenas um construtor aplicável.</span><span class="sxs-lookup"><span data-stu-id="b6446-179">There must only be one applicable constructor.</span></span> <span data-ttu-id="b6446-180">No caso de uma ambiguidade, injeção de dependência gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="b6446-180">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b6446-181">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b6446-181">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
