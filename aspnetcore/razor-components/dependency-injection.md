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
# <a name="razor-components-dependency-injection"></a>Injeção de dependência de componentes do Razor

Por [Rainer Stropek](https://www.timecockpit.com)

Dá suporte a componentes do Razor [injeção de dependência (DI)](xref:fundamentals/dependency-injection). Os aplicativos podem usar os serviços internos, injetando-as em componentes. Aplicativos também podem definir e registrar os serviços personalizados e disponibilizá-los em todo o aplicativo por meio da DI.

## <a name="dependency-injection"></a>Injeção de dependência

Injeção de dependência é uma técnica para acessar os serviços configurados em um local central. Isso pode ser útil em aplicativos de componentes do Razor para:

* Compartilhar uma única instância de uma classe de serviço em vários componentes, conhecidos como um *singleton* service.
* Desacoplar componentes das classes de serviço concreto usando abstrações de referência. Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo. A interface é implementada por um concreto `DataAccess` de classe e registrado como um serviço no contêiner de serviço do aplicativo. Quando um componente usa DI para receber um `IDataAccess` implementação, o componente não está acoplada ao tipo concreto. A implementação pode ser trocada, talvez uma implementação fictícia em testes de unidade.

Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.

## <a name="add-services-to-di"></a>Adicionar serviços para injeção de dependência

Depois de criar um novo aplicativo, examine o `Startup.ConfigureServices` método:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

O `ConfigureServices` método recebe um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que é uma lista de objetos do descritor de serviço (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). Serviços são adicionados, fornecendo os descritores de serviço para a coleção de serviço. O exemplo a seguir demonstra o conceito com o `IDataAccess` interface e sua implementação concreta `DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

Serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.

| Tempo de vida | Descrição |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | Injeção de dependência cria uma *única instância* do serviço. Todos os componentes que exigem um `Singleton` serviço recebe uma instância do mesmo serviço. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Sempre que um componente obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe um *nova instância* do serviço. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor do lado do cliente atualmente não tem o conceito de escopos de injeção de dependência. `Scoped` se comporta como `Singleton`. No entanto, os componentes do ASP.NET Core Razor suportam o `Scoped` tempo de vida. Em um componente do Razor, um registro de serviço com escopo está no escopo para a conexão. Por esse motivo, usar os serviços com escopo é preferencial para os serviços que devem ser direcionados para o usuário atual, mesmo se a intenção atual é executar o lado do cliente no navegador. |

O sistema de DI é baseado no sistema de DI no ASP.NET Core. Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.

## <a name="default-services"></a>Serviços padrão

Serviços padrão são automaticamente adicionados à coleção de serviço do aplicativo.

| Serviço | Descrição |
| ------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI (singleton). Observe que esta instância do `HttpClient` usa o navegador para tratar o tráfego HTTP em segundo plano. [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) é definida automaticamente como o prefixo URI base do aplicativo. `HttpClient` é fornecida apenas para aplicativos do lado do cliente Blazor. |
| `IJSRuntime` | Representa uma instância de um tempo de execução do JavaScript para o qual chamadas podem ser distribuídas. Para obter mais informações, consulte <xref:razor-components/javascript-interop>. |
| `IUriHelper` | Contém os auxiliares para trabalhar com o estado de navegação e URIs (singleton). `IUriHelper` é fornecido para aplicativos Blazor e componentes do Razor. |

É possível usar um provedor de serviço personalizado em vez do provedor de serviço padrão adicionado pelo modelo padrão. Um provedor de serviços personalizados automaticamente não fornece os serviços padrão listados na tabela. Se você usa um provedor de serviço personalizado e exige qualquer um dos serviços mostrados na tabela, adicione os serviços necessários para o novo provedor de serviço.

## <a name="request-a-service-in-a-component"></a>Um serviço em um componente de solicitação

Depois que os serviços são adicionados à coleção de serviços, inserir os serviços em modelos do Razor dos componentes usando o [ @inject ](xref:mvc/views/razor#section-4) diretiva do Razor. `@inject` tem dois parâmetros:

* Nome do tipo: O tipo do serviço para injetar.
* Nome da propriedade: O nome da propriedade que está recebendo o serviço de aplicativo injetado. Observe que a propriedade não exige a criação manual. O compilador cria a propriedade.

Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.

Use várias `@inject` instruções para inserir serviços diferentes.

O exemplo a seguir mostra como usar `@inject`. O serviço que implementa `Services.IDataAccess` é injetado na propriedade do componente `DataRepository`. Observe como o código está usando apenas o `IDataAccess` abstração:

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.cshtml?highlight=2-3,23)]

Internamente, a propriedade gerada (`DataRepository`) é decorado com o `InjectAttribute` atributo. Normalmente, esse atributo não é usado diretamente. Se uma classe base é necessária para componentes e injetadas propriedades também são necessárias para a classe base, `InjectAttribute` podem ser adicionadas manualmente:

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

Em componentes derivados da classe base, o `@inject` diretiva não é necessária. O `InjectAttribute` da classe base é suficiente:

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="dependency-injection-in-services"></a>Injeção de dependência nos serviços

Serviços complexos podem exigir serviços adicionais. No exemplo anterior, `DataAccess` pode exigir o `HttpClient` serviço padrão. `@inject` (ou o `InjectAttribute`) não está disponível para uso em serviços. *Injeção de construtor* deve ser usado em vez disso. Os serviços necessários são adicionados, adicionando parâmetros ao construtor do serviço. Quando a injeção de dependência cria o serviço, ele reconhece os serviços que ele requer no construtor e fornece-os adequadamente.

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

* Deve haver um construtor cujos argumentos podem ser atendidos pela injeção de dependência. Observe que os parâmetros adicionais não cobertos pela DI sejam permitidos se especificar os valores padrão.
* O construtor aplicável deve ser *público*.
* Deve haver apenas um construtor aplicável. No caso de uma ambiguidade, injeção de dependência gera uma exceção.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
