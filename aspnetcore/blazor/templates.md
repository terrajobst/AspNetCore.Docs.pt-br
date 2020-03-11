---
title: Modelos de Blazor ASP.NET Core
author: guardrex
description: Saiba mais sobre os modelos de aplicativo do ASP.NET Core Blazor e a estrutura do projeto do Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: acfa4b8a42cbd310c6fc6dc973573578e94ef999
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664237"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>Modelos de Blazor ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

O Blazor Framework fornece modelos para desenvolver aplicativos para cada um dos modelos de Hospedagem de Blazor:

* Blazor Webassembly (`blazorwasm`)
* Servidor de Blazor (`blazorserver`)

Para obter mais informações sobre os modelos de Hospedagem de Blazor, consulte <xref:blazor/hosting-models>.

Para obter as instruções passo a passo sobre como criar um aplicativo Blazor de um modelo, consulte <xref:blazor/get-started>.

## <a name="opno-locblazor-project-structure"></a>estrutura do projeto Blazor

Os arquivos e pastas a seguir compõem um aplicativo Blazor gerado a partir de um modelo de Blazor:

* *Program.cs* &ndash; o ponto de entrada do aplicativo que configura:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core (servidorBlazor)
  * O netassembly host (Blazor Webassembly) &ndash; o código nesse arquivo é exclusivo para aplicativos criados a partir do modelo de Webassembly Blazor (`blazorwasm`).
    * O componente `App`, que é o componente raiz do aplicativo, é especificado como o elemento DOM `app` para o método `Add`.
    * Os serviços podem ser configurados com o método `ConfigureServices` no construtor de hosts (por exemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * A configuração pode ser fornecida por meio do host Builder (`builder.Configuration`).

* *Startup.cs* (servidorBlazor) &ndash; contém a lógica de inicialização do aplicativo. A classe `Startup` define dois métodos:

  * `ConfigureServices` &ndash; configura os serviços de injeção de [dependência (di)](xref:fundamentals/dependency-injection) do aplicativo. Em aplicativos do Blazor Server, os serviços são adicionados chamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>e o `WeatherForecastService` é adicionado ao contêiner de serviço para ser usado pelo componente de `FetchData` de exemplo.
  * `Configure` &ndash; configura o pipeline de tratamento de solicitação do aplicativo:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> é chamado para configurar um ponto de extremidade para a conexão em tempo real com o navegador. A conexão é criada com [SignalR](xref:signalr/introduction), que é uma estrutura para adicionar funcionalidade da Web em tempo real a aplicativos.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo (*pages/_Host. cshtml*) e habilitar a navegação.

* *wwwroot/index.html* (Blazor Webassembly) &ndash; página raiz do aplicativo implementada como uma página HTML:
  * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
  * A página especifica onde o componente de `App` raiz é renderizado. O componente `App` (*app. Razor*) é especificado como o elemento DOM `app` para o método `AddComponent` no `Startup.Configure`.
  * O arquivo JavaScript `_framework/blazor.webassembly.js` é carregado, que:
    * Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
    * Inicializa o tempo de execução para executar o aplicativo.

* *Pages/_Host. cshtml* (servidorBlazor) &ndash; página raiz do aplicativo implementada como uma página Razor:
  * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
  * O arquivo JavaScript `_framework/blazor.server.js` é carregado, que configura a conexão SignalR em tempo real entre o navegador e o servidor.
  * A página host especifica onde o componente de `App` raiz (*app. Razor*) é renderizado.

* *App. razor* &ndash; o componente raiz do aplicativo que configura o roteamento do lado do cliente usando o componente <xref:Microsoft.AspNetCore.Components.Routing.Router>. O componente `Router` intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.

* A pasta de *páginas* &ndash; contém os componentes/páginas roteáveis ( *. Razor*) que compõem o aplicativo Blazor. A rota para cada página é especificada usando a diretiva [`@page`](xref:mvc/views/razor#page) . O modelo inclui os seguintes componentes:
  * `Index` (*index. Razor*) &ndash; implementa a Home Page.
  * `Counter` (*Counter. Razor*) &ndash; implementa a página do contador.
  * `Error` (*Error. Razor*, somente aplicativo Blazor Server) &ndash; renderizado quando ocorre uma exceção sem tratamento no aplicativo.
  * `FetchData` (*FetchData. Razor*) &ndash; implementa a página buscar dados.

* A pasta *compartilhada* &ndash; contém outros componentes de interface do usuário ( *. Razor*) usados pelo aplicativo:
  * `MainLayout` (*MainLayout. Razor*) &ndash; o componente de layout do aplicativo.
  * `NavMenu` (*NavMenu. Razor*) &ndash; implementa a navegação na barra lateral. Inclui o [componente NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que renderiza links de navegação para outros componentes do Razor. O componente `NavLink` indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.

* *_Imports. razor* &ndash; inclui diretivas comuns do Razor para incluir nos componentes do aplicativo ( *. Razor*), como diretivas de [`@using`](xref:mvc/views/razor#using) para namespaces.

* A pasta de *dados* (Blazor Server) &ndash; contém a classe `WeatherForecast` e a implementação do `WeatherForecastService` que fornecem dados meteorológicos de exemplo para o componente de `FetchData` do aplicativo.

* *wwwroot* &ndash; a pasta [raiz da Web](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo.

* *appSettings. JSON* (Blazor Server) &ndash; definições de configuração para o aplicativo.
