---
title: Injeção de dependência de Blazor ASP.NET Core
author: guardrex
description: Veja como Blazor aplicativos podem injetar serviços em componentes.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/dependency-injection
ms.openlocfilehash: 17dd0f927064ae7c2b1e3e439fd93e2cb220a5a4
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879777"
---
# <a name="aspnet-core-opno-locblazor-dependency-injection"></a>Injeção de dependência de Blazor ASP.NET Core

Por [Rainer Stropek](https://www.timecockpit.com)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor dá suporte à [injeção de dependência (di)](xref:fundamentals/dependency-injection). Os aplicativos podem usar serviços internos injetando-os em componentes. Os aplicativos também podem definir e registrar serviços personalizados e torná-los disponíveis em todo o aplicativo por meio de DI.

DI é uma técnica para acessar os serviços configurados em um local central. Isso pode ser útil em aplicativos Blazor para:

* Compartilhe uma única instância de uma classe de serviço em vários componentes, conhecido como um serviço *singleton* .
* Dissociar componentes de classes de serviço concretas usando abstrações de referência. Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo. A interface é implementada por uma classe de `DataAccess` concreta e registrada como um serviço no contêiner de serviço do aplicativo. Quando um componente usa DI para receber uma implementação de `IDataAccess`, o componente não é acoplado ao tipo concreto. A implementação pode ser trocada, talvez para uma implementação fictícia em testes de unidade.

## <a name="default-services"></a>Serviços padrão

Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.

| Service | Tempo de vida | Descrição |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.<br><br>A instância do `HttpClient` em um aplicativo Webassembly Blazor usa o navegador para manipular o tráfego HTTP em segundo plano.<br><br>os aplicativos do Blazor Server não incluem uma `HttpClient` configurada como um serviço por padrão. Forneça um `HttpClient` para um aplicativo Blazor Server.<br><br>Para obter mais informações, consulte <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton | Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas. Para obter mais informações, consulte <xref:blazor/javascript-interop>. |
| `NavigationManager` | Singleton | Contém auxiliares para trabalhar com URIs e estado de navegação. Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/routing#uri-and-navigation-state-helpers). |

Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela. Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.

## <a name="add-services-to-an-app"></a>Adicionar serviços a um aplicativo

Depois de criar um novo aplicativo, examine o método `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

O método `ConfigureServices` é passado uma <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que é uma lista de objetos de descritor de serviço (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). Os serviços são adicionados fornecendo descritores de serviço à coleção de serviços. O exemplo a seguir demonstra o conceito com a interface `IDataAccess` e sua implementação concreta `DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.

| Tempo de vida | Descrição |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor aplicativos Webassembly não têm atualmente um conceito de escopos de DI. os serviços registrados `Scoped`se comportam como serviços `Singleton`. No entanto, o modelo de hospedagem do servidor Blazor dá suporte ao tempo de vida `Scoped`. Em aplicativos do Blazor Server, um registro de serviço com escopo é definido para a *conexão*. Por esse motivo, o uso de serviços com escopo é preferencial para serviços que devem ser delimitados para o usuário atual, mesmo que a intenção atual seja executar o lado do cliente no navegador. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI cria uma *única instância* do serviço. Todos os componentes que exigem um serviço de `Singleton` recebem uma instância do mesmo serviço. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Sempre que um componente Obtém uma instância de um serviço de `Transient` do contêiner de serviço, ele recebe uma *nova instância* do serviço. |

O sistema de DI é baseado no sistema de injeção de ASP.NET Core. Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Solicitar um serviço em um componente

Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando o [\@injetar](xref:mvc/views/razor#inject) diretiva Razor. `@inject` tem dois parâmetros:

* Digite &ndash; o tipo de serviço a injetar.
* Propriedade &ndash; o nome da propriedade que recebe o serviço de aplicativo injetado. A propriedade não requer criação manual. O compilador cria a propriedade.

Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.

Use várias instruções `@inject` para injetar serviços diferentes.

O exemplo a seguir mostra como usar `@inject`. O serviço que implementa `Services.IDataAccess` é injetado na `DataRepository`de propriedades do componente. Observe como o código está usando apenas a abstração de `IDataAccess`:

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Internamente, a propriedade gerada (`DataRepository`) usa o atributo `InjectAttribute`. Normalmente, esse atributo não é usado diretamente. Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione manualmente o `InjectAttribute`:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Em componentes derivados da classe base, a diretiva `@inject` não é necessária. O `InjectAttribute` da classe base é suficiente:

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Usar DI em serviços

Serviços complexos podem exigir serviços adicionais. No exemplo anterior, `DataAccess` pode exigir o serviço de `HttpClient` padrão. `@inject` (ou o `InjectAttribute`) não está disponível para uso em serviços. A *injeção de Construtor* deve ser usada em seu lugar. Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço. Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo.

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

Pré-requisitos para injeção de construtor:

* Um construtor deve existir cujos argumentos podem ser todos atendidos por DI. Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.
* O Construtor aplicável deve ser *público*.
* Um Construtor aplicável deve existir. No caso de uma ambiguidade, DI gera uma exceção.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classes de componente base do utilitário para gerenciar um escopo de DI

Em aplicativos ASP.NET Core, os serviços com escopo normalmente são incluídos no escopo da solicitação atual. Depois que a solicitação for concluída, todos os serviços com escopo ou transitórios serão descartados pelo sistema de DI. Em aplicativos Blazor Server, o escopo da solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e no escopo que vivem muito mais do que o esperado.

Para os serviços de escopo até o tempo de vida de um componente, o pode usar as classes base `OwningComponentBase` e `OwningComponentBase<TService>`. Essas classes base expõem uma propriedade `ScopedServices` do tipo `IServiceProvider` que resolve serviços que estão no escopo do tempo de vida do componente. Para criar um componente que herda de uma classe base no Razor, use a diretiva `@inherits`.

```cshtml
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
> Os serviços injetados no componente usando `@inject` ou o `InjectAttribute` não são criados no escopo do componente e estão vinculados ao escopo da solicitação.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
