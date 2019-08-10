---
title: ASP.NET Core injeção de dependência mais incrivelmente
author: guardrex
description: Veja como os aplicativos mais incrivelmente podem injetar serviços em componentes.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/dependency-injection
ms.openlocfilehash: a9330caa81eec0910206312283b3424c70cd1289
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68948386"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="53d55-103">ASP.NET Core injeção de dependência mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="53d55-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="53d55-104">Por [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="53d55-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="53d55-105">O mais incrivelmente dá suporte à [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="53d55-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="53d55-106">Os aplicativos podem usar serviços internos injetando-os em componentes.</span><span class="sxs-lookup"><span data-stu-id="53d55-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="53d55-107">Os aplicativos também podem definir e registrar serviços personalizados e torná-los disponíveis em todo o aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="53d55-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="53d55-108">DI é uma técnica para acessar os serviços configurados em um local central.</span><span class="sxs-lookup"><span data-stu-id="53d55-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="53d55-109">Isso pode ser útil em aplicativos mais incrivelmente para:</span><span class="sxs-lookup"><span data-stu-id="53d55-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="53d55-110">Compartilhe uma única instância de uma classe de serviço em vários componentes, conhecido como um serviço *singleton* .</span><span class="sxs-lookup"><span data-stu-id="53d55-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="53d55-111">Dissociar componentes de classes de serviço concretas usando abstrações de referência.</span><span class="sxs-lookup"><span data-stu-id="53d55-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="53d55-112">Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="53d55-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="53d55-113">A interface é implementada por uma `DataAccess` classe concreta e registrada como um serviço no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="53d55-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="53d55-114">Quando um componente usa di para receber uma `IDataAccess` implementação, o componente não é acoplado ao tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="53d55-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="53d55-115">A implementação pode ser trocada, talvez para uma implementação fictícia em testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="53d55-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="53d55-116">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="53d55-116">Default services</span></span>

<span data-ttu-id="53d55-117">Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="53d55-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="53d55-118">Serviço</span><span class="sxs-lookup"><span data-stu-id="53d55-118">Service</span></span> | <span data-ttu-id="53d55-119">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="53d55-119">Lifetime</span></span> | <span data-ttu-id="53d55-120">Descrição</span><span class="sxs-lookup"><span data-stu-id="53d55-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="53d55-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="53d55-121">Singleton</span></span> | <span data-ttu-id="53d55-122">Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.</span><span class="sxs-lookup"><span data-stu-id="53d55-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span> <span data-ttu-id="53d55-123">Observe que essa instância do `HttpClient` usa o navegador para manipular o tráfego HTTP em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="53d55-123">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="53d55-124">[HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) é definido automaticamente como o prefixo de URI de base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="53d55-124">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="53d55-125">Para obter mais informações, consulte <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="53d55-125">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="53d55-126">Singleton</span><span class="sxs-lookup"><span data-stu-id="53d55-126">Singleton</span></span> | <span data-ttu-id="53d55-127">Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas.</span><span class="sxs-lookup"><span data-stu-id="53d55-127">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="53d55-128">Para obter mais informações, consulte <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="53d55-128">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="53d55-129">Singleton</span><span class="sxs-lookup"><span data-stu-id="53d55-129">Singleton</span></span> | <span data-ttu-id="53d55-130">Contém auxiliares para trabalhar com URIs e estado de navegação.</span><span class="sxs-lookup"><span data-stu-id="53d55-130">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="53d55-131">Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="53d55-131">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="53d55-132">Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela.</span><span class="sxs-lookup"><span data-stu-id="53d55-132">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="53d55-133">Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.</span><span class="sxs-lookup"><span data-stu-id="53d55-133">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="53d55-134">Adicionar serviços a um aplicativo</span><span class="sxs-lookup"><span data-stu-id="53d55-134">Add services to an app</span></span>

<span data-ttu-id="53d55-135">Depois de criar um novo aplicativo, examine `Startup.ConfigureServices` o método:</span><span class="sxs-lookup"><span data-stu-id="53d55-135">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="53d55-136">O `ConfigureServices` método é passado um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que é uma lista de objetos do descritor<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>de serviço ().</span><span class="sxs-lookup"><span data-stu-id="53d55-136">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="53d55-137">Os serviços são adicionados fornecendo descritores de serviço à coleção de serviços.</span><span class="sxs-lookup"><span data-stu-id="53d55-137">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="53d55-138">O exemplo a seguir demonstra o conceito com `IDataAccess` a interface e sua implementação `DataAccess`concreta:</span><span class="sxs-lookup"><span data-stu-id="53d55-138">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="53d55-139">Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="53d55-139">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="53d55-140">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="53d55-140">Lifetime</span></span> | <span data-ttu-id="53d55-141">Descrição</span><span class="sxs-lookup"><span data-stu-id="53d55-141">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="53d55-142">No momento, o lado do cliente não tem um conceito de escopos de DI.</span><span class="sxs-lookup"><span data-stu-id="53d55-142">Blazor client-side doesn't currently have a concept of DI scopes.</span></span> <span data-ttu-id="53d55-143">`Scoped`-serviços registrados se comportam como `Singleton` serviços.</span><span class="sxs-lookup"><span data-stu-id="53d55-143">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="53d55-144">No entanto, o modelo de hospedagem do lado `Scoped` do servidor dá suporte ao tempo de vida.</span><span class="sxs-lookup"><span data-stu-id="53d55-144">However, the server-side hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="53d55-145">Em um componente Razor, um registro de serviço com escopo é definido para a conexão.</span><span class="sxs-lookup"><span data-stu-id="53d55-145">In a Razor component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="53d55-146">Por esse motivo, o uso de serviços com escopo é preferencial para serviços que devem ser delimitados para o usuário atual, mesmo que a intenção atual seja executar o lado do cliente no navegador.</span><span class="sxs-lookup"><span data-stu-id="53d55-146">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="53d55-147">DI cria uma *única instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="53d55-147">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="53d55-148">Todos os componentes que exigem `Singleton` um serviço recebem uma instância do mesmo serviço.</span><span class="sxs-lookup"><span data-stu-id="53d55-148">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="53d55-149">Sempre que um componente Obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe uma *nova instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="53d55-149">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="53d55-150">O sistema de DI é baseado no sistema de injeção de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53d55-150">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="53d55-151">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="53d55-151">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="53d55-152">Solicitar um serviço em um componente</span><span class="sxs-lookup"><span data-stu-id="53d55-152">Request a service in a component</span></span>

<span data-ttu-id="53d55-153">Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando [ \@](xref:mvc/views/razor#inject) a diretiva injetar Razor.</span><span class="sxs-lookup"><span data-stu-id="53d55-153">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="53d55-154">`@inject`tem dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="53d55-154">`@inject` has two parameters:</span></span>

* <span data-ttu-id="53d55-155">Digite &ndash; o tipo do serviço a injetar.</span><span class="sxs-lookup"><span data-stu-id="53d55-155">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="53d55-156">Propriedade &ndash; o nome da propriedade que recebe o serviço de aplicativo injetado.</span><span class="sxs-lookup"><span data-stu-id="53d55-156">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="53d55-157">A propriedade não requer criação manual.</span><span class="sxs-lookup"><span data-stu-id="53d55-157">The property doesn't require manual creation.</span></span> <span data-ttu-id="53d55-158">O compilador cria a propriedade.</span><span class="sxs-lookup"><span data-stu-id="53d55-158">The compiler creates the property.</span></span>

<span data-ttu-id="53d55-159">Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="53d55-159">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="53d55-160">Use várias `@inject` instruções para injetar serviços diferentes.</span><span class="sxs-lookup"><span data-stu-id="53d55-160">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="53d55-161">O exemplo a seguir mostra como usar `@inject`.</span><span class="sxs-lookup"><span data-stu-id="53d55-161">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="53d55-162">A implementação `Services.IDataAccess` do serviço é injetada na propriedade `DataRepository`do componente.</span><span class="sxs-lookup"><span data-stu-id="53d55-162">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="53d55-163">Observe como o código está usando apenas a `IDataAccess` abstração:</span><span class="sxs-lookup"><span data-stu-id="53d55-163">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="53d55-164">Internamente, a propriedade gerada (`DataRepository`) é decorada com `InjectAttribute` o atributo.</span><span class="sxs-lookup"><span data-stu-id="53d55-164">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="53d55-165">Normalmente, esse atributo não é usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="53d55-165">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="53d55-166">Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione `InjectAttribute`manualmente:</span><span class="sxs-lookup"><span data-stu-id="53d55-166">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="53d55-167">Em componentes derivados da classe base, a `@inject` diretiva não é necessária.</span><span class="sxs-lookup"><span data-stu-id="53d55-167">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="53d55-168">O `InjectAttribute` da classe base é suficiente:</span><span class="sxs-lookup"><span data-stu-id="53d55-168">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="53d55-169">Usar DI em serviços</span><span class="sxs-lookup"><span data-stu-id="53d55-169">Use DI in services</span></span>

<span data-ttu-id="53d55-170">Serviços complexos podem exigir serviços adicionais.</span><span class="sxs-lookup"><span data-stu-id="53d55-170">Complex services might require additional services.</span></span> <span data-ttu-id="53d55-171">No exemplo anterior, `DataAccess` pode exigir o `HttpClient` serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="53d55-171">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="53d55-172">`@inject`(ou o `InjectAttribute`) não está disponível para uso em serviços.</span><span class="sxs-lookup"><span data-stu-id="53d55-172">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="53d55-173">A *injeção de Construtor* deve ser usada em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="53d55-173">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="53d55-174">Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço.</span><span class="sxs-lookup"><span data-stu-id="53d55-174">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="53d55-175">Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo.</span><span class="sxs-lookup"><span data-stu-id="53d55-175">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="53d55-176">Pré-requisitos para injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="53d55-176">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="53d55-177">Um construtor deve existir cujos argumentos podem ser todos atendidos por DI.</span><span class="sxs-lookup"><span data-stu-id="53d55-177">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="53d55-178">Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.</span><span class="sxs-lookup"><span data-stu-id="53d55-178">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="53d55-179">O Construtor aplicável deve ser *público*.</span><span class="sxs-lookup"><span data-stu-id="53d55-179">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="53d55-180">Um Construtor aplicável deve existir.</span><span class="sxs-lookup"><span data-stu-id="53d55-180">One applicable constructor must exist.</span></span> <span data-ttu-id="53d55-181">No caso de uma ambiguidade, DI gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="53d55-181">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53d55-182">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="53d55-182">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
