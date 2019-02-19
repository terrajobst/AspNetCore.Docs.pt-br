---
title: Roteamento no ASP.NET Core
author: rick-anderson
description: Descubra como o roteamento do ASP.NET Core é responsável por mapear URIs de solicitação para seletores de ponto de extremidade e expedir solicitações de entrada para pontos de extremidade.
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: fundamentals/routing
ms.openlocfilehash: 3dbb2d358ec9e3dcdd96c3771576911d906d796f
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248427"
---
# <a name="routing-in-aspnet-core"></a>Roteamento no ASP.NET Core

Por [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/) e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="<= aspnetcore-1.1"

Para obter a versão 1.1 deste tópico, baixe [Roteamento no ASP.NET Core (versão 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Routing_1.x.pdf).

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

O roteamento é responsável por mapear URIs de solicitação para seletores de ponto de extremidade e expedir solicitações de entrada para pontos de extremidade. As rotas são definidas no aplicativo e configuradas quando o aplicativo é iniciado. Uma rota pode opcionalmente extrair os valores da URL contida na solicitação e esses valores podem então ser usados para o processamento da solicitação. Usando as informações de rota do aplicativo, o roteamento também pode gerar URLs que são mapeadas para seletores de ponto de extremidade.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Para usar os últimos cenários de roteamento no ASP.NET Core 2.2, especifique a [versão de compatibilidade](xref:mvc/compatibility-version) com o registro de serviços MVC em `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

A opção <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> determina se o roteamento deve usar internamente a lógica baseada em ponto de extremidade ou a lógica baseada em <xref:Microsoft.AspNetCore.Routing.IRouter> do ASP.NET Core 2.1 ou anterior. Quando a versão de compatibilidade é definida como 2.2 ou posterior, o valor padrão é `true`. Defina o valor como `false` para usar a lógica de roteamento anterior:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Para obter mais informações sobre o roteamento baseado em <xref:Microsoft.AspNetCore.Routing.IRouter>, confira a [versão do ASP.NET Core 2.1 deste tópico](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O roteamento é responsável por mapear URIs de solicitação para manipuladores de rotas e expedir as solicitações de entrada. As rotas são definidas no aplicativo e configuradas quando o aplicativo é iniciado. Uma rota pode opcionalmente extrair os valores da URL contida na solicitação e esses valores podem então ser usados para o processamento da solicitação. Usando rotas configuradas no aplicativo, o roteamento pode gerar URLs que são mapeadas para manipuladores de rotas.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Para usar os últimos cenários de roteamento no ASP.NET Core 2.1, especifique a [versão de compatibilidade](xref:mvc/compatibility-version) com o registro de serviços MVC em `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

::: moniker-end

> [!IMPORTANT]
> Este documento aborda o roteamento de nível inferior do ASP.NET Core. Para obter informações sobre o roteamento do ASP.NET Core MVC, confira <xref:mvc/controllers/routing>. Para obter informações sobre convenções de roteamento no Razor Pages, confira <xref:razor-pages/razor-pages-conventions>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Conceitos básicos sobre roteamento

A maioria dos aplicativos deve escolher um esquema de roteamento básico e descritivo para que as URLs sejam legíveis e significativas. A rota convencional padrão `{controller=Home}/{action=Index}/{id?}`:

* Dá suporte a um esquema de roteamento básico e descritivo.
* É um ponto de partida útil para aplicativos baseados em interface do usuário.

Os desenvolvedores geralmente adicionam outras rotas concisas às áreas de alto tráfego de um aplicativo em situações especiais (por exemplo, pontos de extremidade de blog e comércio eletrônico) usando o [roteamento de atributo](xref:mvc/controllers/routing#attribute-routing) ou rotas convencionais dedicadas.

As APIs da Web devem usar o roteamento de atributo para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por verbos HTTP. Isso significa que muitas operações (por exemplo, GET, POST) no mesmo recurso lógico usarão a mesma URL. O roteamento de atributo fornece um nível de controle necessário para projetar cuidadosamente o layout de ponto de extremidade público de uma API.

Os aplicativos do Razor Pages usam o roteamento convencional padrão para fornecer recursos nomeados na pasta *Pages* de um aplicativo. Estão disponíveis convenções adicionais que permitem a personalização do comportamento de roteamento do Razor Pages. Para obter mais informações, consulte <xref:razor-pages/index> e <xref:razor-pages/razor-pages-conventions>.

O suporte à geração de URL permite que o aplicativo seja desenvolvido sem hard-coding das URLs para vincular o aplicativo. Esse suporte permite começar com uma configuração de roteamento básica e modificar as rotas, depois que o layout de recurso do aplicativo é determinado.

::: moniker range=">= aspnetcore-2.2"

O roteamento usa *pontos de extremidade* (`Endpoint`) para representar os pontos de extremidade lógicos em um aplicativo.

Um ponto de extremidade define um delegado para processar solicitações e uma coleção de metadados arbitrários. Os metadados usados implementam interesses paralelos com base em políticas e na configuração anexada a cada ponto de extremidade.

O sistema de roteamento tem as seguintes características:

* A sintaxe do modelo de rota é usada para definir rotas com os parâmetros de rota com tokens criados.
* A configuração de ponto de extremidade de estilo convencional e de estilo de atributo é permitida.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> é usado para determinar se um parâmetro de URL contém um valor válido para determinada restrição de ponto de extremidade.
* Modelos de aplicativo, como MVC/Razor Pages, registram todos os seus pontos de extremidade, que têm uma implementação previsível de cenários de roteamento.
* A implementação de roteamento toma decisões de roteamento, sempre que desejado no pipeline de middleware.
* O Middleware que aparece após um Middleware de Roteamento pode inspecionar o resultado da decisão de ponto de extremidade do Middleware de Roteamento para determinado URI de solicitação.
* É possível enumerar todos os pontos de extremidade no aplicativo em qualquer lugar do pipeline de middleware.
* Um aplicativo pode usar o roteamento para gerar URLs (por exemplo, para redirecionamento ou links) com base nas informações de ponto de extremidade e evitar URLs embutidas em código, o que ajuda na facilidade de manutenção.
* A geração de URL baseia-se em endereços, que dá suporte à extensibilidade arbitrária:

  * A API de Gerador de Link (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) pode ser resolvida em qualquer lugar usando a [DI (Injeção de Dependência)](xref:fundamentals/dependency-injection) para gerar URLs.
  * Quando a API de Gerador de Link não está disponível por meio da DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> oferece métodos para criar URLs.

> [!NOTE]
> Com o lançamento do roteamento de ponto de extremidade no ASP.NET Core 2.2, a vinculação de ponto de extremidade fica limitada às ações e às páginas do MVC/Razor Pages. As expansões de funcionalidades de vinculação de ponto de extremidade estão planejadas para versões futuras.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O roteamento usa *rotas* (implementações de <xref:Microsoft.AspNetCore.Routing.IRouter>) para:

* Mapear solicitações de entrada para *manipuladores de rotas*.
* Gerar as URLs usadas nas respostas.

Por padrão, um aplicativo tem uma única coleção de rotas. Quando uma solicitação é recebida, as rotas na coleção são processadas na ordem em que existem na coleção. A estrutura tenta corresponder uma URL de solicitação de entrada a uma rota na coleção chamando o método <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> em cada rota da coleção. Uma resposta pode usar o roteamento para gerar URLs (por exemplo, para redirecionamento ou links) com base nas informações de rotas e evitar URLs embutidas em código, o que ajuda na facilidade de manutenção.

O sistema de roteamento tem as seguintes características:

* A sintaxe do modelo de rota é usada para definir rotas com os parâmetros de rota com tokens criados.
* A configuração de ponto de extremidade de estilo convencional e de estilo de atributo é permitida.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> é usado para determinar se um parâmetro de URL contém um valor válido para determinada restrição de ponto de extremidade.
* Modelos de aplicativo, como MVC/Razor Pages, registram todas as suas rotas, que têm uma implementação previsível de cenários de roteamento.
* Uma resposta pode usar o roteamento para gerar URLs (por exemplo, para redirecionamento ou links) com base nas informações de rotas e evitar URLs embutidas em código, o que ajuda na facilidade de manutenção.
* A geração de URL baseia-se em rotas, que dá suporte à extensibilidade arbitrária. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> oferece métodos para criar URLs.

::: moniker-end

O roteamento está conectado ao pipeline do [middleware](xref:fundamentals/middleware/index) pela classe <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>. O [ASP.NET Core MVC](xref:mvc/overview) adiciona o roteamento ao pipeline de middleware como parte de sua configuração e manipula o roteamento nos aplicativos do MVC e do Razor Pages. Para saber como usar o roteamento como um componente autônomo, confira a seção [Usar o Middleware de Roteamento](#use-routing-middleware).

### <a name="url-matching"></a>Correspondência de URL

::: moniker range=">= aspnetcore-2.2"

A correspondência de URL é o processo pelo qual o roteamento expede uma solicitação de entrada para um *ponto de extremidade*. Esse processo se baseia nos dados do caminho da URL, mas pode ser estendido para considerar qualquer dado na solicitação. A capacidade de expedir solicitações para manipuladores separados é fundamental para dimensionar o tamanho e a complexidade de um aplicativo.

O sistema de roteamento no roteamento de ponto de extremidade é responsável por todas as decisões de expedição. Como o middleware aplica políticas com base no ponto de extremidade selecionado, é importante que qualquer decisão que possa afetar a expedição ou a aplicação de políticas de segurança seja feita dentro do sistema de roteamento.

Quando o delegado do ponto de extremidade é executado, as propriedades de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) são definidas com valores apropriados com base no processamento da solicitação executado até o momento.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Correspondência de URL é o processo pelo qual o roteamento expede uma solicitação de entrada para um *manipulador*. Esse processo se baseia nos dados do caminho da URL, mas pode ser estendido para considerar qualquer dado na solicitação. A capacidade de expedir solicitações para manipuladores separados é fundamental para dimensionar o tamanho e a complexidade de um aplicativo.

As solicitações de entrada entram no <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, que chama o método <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> em cada rota na sequência. A instância <xref:Microsoft.AspNetCore.Routing.IRouter> escolhe se deseja *manipular* a solicitação definindo o [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) como um <xref:Microsoft.AspNetCore.Http.RequestDelegate> não nulo. Se uma rota definir um manipulador para a solicitação, o processamento de rotas será interrompido e o manipulador será invocado para processar a solicitação. Se nenhum manipulador de rotas é encontrado para processar a solicitação, o middleware transmite a solicitação para o próximo middleware no pipeline de solicitação.

A entrada primária para <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> é o [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associado à solicitação atual. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) e [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) são saídas definidas depois que é encontrada uma correspondência de uma rota.

Uma correspondência que chama <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> também define as propriedades do [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) com valores apropriados com base no processamento da solicitação executado até o momento.

::: moniker-end

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) é um dicionário de *valores de rota* produzido por meio da rota. Esses valores geralmente são determinados pela criação de token da URL e podem ser usados para aceitar a entrada do usuário ou tomar outras decisões de expedição dentro do aplicativo.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) é um recipiente de propriedades de dados adicionais relacionados à rota correspondente. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> são fornecidos para dar suporte à associação de dados de estado com cada rota para que o aplicativo possa tomar decisões com base em qual rota teve uma correspondência. Esses valores são definidos pelo desenvolvedor e **não** afetam de forma alguma o comportamento do roteamento. Além disso, os valores em stash em [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) podem ser de qualquer tipo, ao contrário de [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), que devem ser conversíveis de/para cadeias de caracteres.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) é uma lista das rotas que participaram da correspondência bem-sucedida da solicitação. As rotas podem ser aninhadas uma dentro da outra. A propriedade <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> reflete o caminho pela árvore lógica de rotas que resultou em uma correspondência. Em geral, o primeiro item em <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> é a coleção de rotas e deve ser usado para a geração de URL. O último item em <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> é o manipulador de rotas que teve uma correspondência.

### <a name="url-generation"></a>Geração de URL

::: moniker range=">= aspnetcore-2.2"

Geração de URL é o processo pelo qual o roteamento pode criar um caminho de URL de acordo com um conjunto de valores de rota. Isso permite uma separação lógica entre os pontos de extremidade e as URLs que os acessam.

O roteamento de ponto de extremidade inclui a API de Gerador de Link (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator> é um serviço singleton que pode ser recuperado por meio da DI. A API pode ser usada fora do contexto de uma solicitação em execução. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> do MVC e cenários que dependem de <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, como [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro), Auxiliares de HTML e [Resultados da Ação](xref:mvc/controllers/actions), usam o gerador de link para fornecer funcionalidades de geração de link.

O gerador de link é respaldado pelo conceito de um *endereço* e *esquemas de endereço*. Um esquema de endereço é uma maneira de determinar os pontos de extremidade que devem ser considerados para a geração de link. Por exemplo, os cenários de nome de rota e valores de rota com os quais muitos usuários estão familiarizados no MVC/Razor Pages são implementados como um esquema de endereço.

O gerador de link pode ser vinculado a ações e páginas do MVC/Razor Pages por meio dos seguintes métodos de extensão:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Uma sobrecarga desses métodos aceita argumentos que incluem o `HttpContext`. Esses métodos são funcionalmente equivalentes a `Url.Action` e `Url.Page`, mas oferecem mais flexibilidade e opções.

Os métodos `GetPath*` são mais semelhantes a `Url.Action` e `Url.Page`, pois geram um URI que contém um caminho absoluto. Os métodos `GetUri*` sempre geram um URI absoluto que contém um esquema e um host. Os métodos que aceitam um `HttpContext` geram um URI no contexto da solicitação em execução. Os valores de rota de ambiente, o caminho base da URL, o esquema e o host da solicitação em execução são usados, a menos que sejam substituídos.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> é chamado com um endereço. A geração de um URI ocorre em duas etapas:

1. Um endereço é associado a uma lista de pontos de extremidade que correspondem ao endereço.
1. O `RoutePattern` de cada ponto de extremidade é avaliado até que seja encontrado um padrão de rota correspondente aos valores fornecidos. A saída resultante é combinada com as outras partes de URI fornecidas ao gerador de link e é retornada.

Os métodos fornecidos pelo <xref:Microsoft.AspNetCore.Routing.LinkGenerator> dão suporte a funcionalidades de geração de link padrão para qualquer tipo de endereço. A maneira mais conveniente usar o gerador de link é por meio de métodos de extensão que executam operações para um tipo de endereço específico.

| Método de extensão   | Descrição                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Gera um URI com um caminho absoluto com base nos valores fornecidos. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Gera um URI absoluto com base nos valores fornecidos.             |

> [!WARNING]
> Preste atenção às seguintes implicações da chamada de métodos <xref:Microsoft.AspNetCore.Routing.LinkGenerator>:
>
> * Use métodos de extensão de `GetUri*` com cuidado em uma configuração de aplicativo que não valide o cabeçalho `Host` das solicitações de entrada. Se o cabeçalho `Host` das solicitações de entrada não é validado, uma entrada de solicitação não confiável pode ser enviada novamente ao cliente em URIs em uma exibição/página. Recomendamos que todos os aplicativos de produção configurem seu servidor para validar o cabeçalho `Host` com os valores válidos conhecidos.
>
> * Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> com cuidado no middleware em combinação com `Map` ou `MapWhen`. `Map*` altera o caminho base da solicitação em execução, o que afeta a saída da geração de link. Todas as APIs de <xref:Microsoft.AspNetCore.Routing.LinkGenerator> permitem a especificação de um caminho base. Sempre especifique um caminho base vazio para desfazer o efeito de `Map*` na geração de link.

## <a name="differences-from-earlier-versions-of-routing"></a>Diferenças das versões anteriores de roteamento

Existem algumas diferenças entre o roteamento de ponto de extremidade no ASP.NET Core 2.2 ou posterior e nas versões anteriores de roteamento no ASP.NET Core:

* O sistema de roteamento do ponto de extremidade não dá suporte à extensibilidade baseada em <xref:Microsoft.AspNetCore.Routing.IRouter>, incluindo a herança de <xref:Microsoft.AspNetCore.Routing.Route>.

* O roteamento de ponto de extremidade não dá suporte a [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). Use a [versão de compatibilidade](xref:mvc/compatibility-version) 2.1 (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) para continuar usando o shim de compatibilidade.

* O Roteamento de Ponto de Extremidade tem um comportamento diferente para o uso de maiúsculas dos URIs gerados ao usar rotas convencionais.

  Considere o seguinte modelo de rota padrão:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Suponha que você gere um link para uma ação usando a seguinte rota:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  Com o roteamento baseado em <xref:Microsoft.AspNetCore.Routing.IRouter>, esse código gera um URI igual a `/blog/ReadPost/17`, que respeita o uso de maiúsculas do valor de rota fornecido. O roteamento de ponto de extremidade no ASP.NET Core 2.2 ou posterior produz `/Blog/ReadPost/17` ("Blog" está em letras maiúsculas). O roteamento de ponto de extremidade fornece a interface `IOutboundParameterTransformer` que pode ser usada para personalizar esse comportamento globalmente ou para aplicar diferentes convenções ao mapeamento de URLs.

  Para obter mais informações, confira a seção [Referência de transformador de parâmetro](#parameter-transformer-reference).

* A Geração de Link usada pelo MVC/Razor Pages com rotas convencionais tem um comportamento diferente ao tentar estabelecer um vínculo com um controlador/uma ação ou uma página não existente.

  Considere o seguinte modelo de rota padrão:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Suponha que você gere um link para uma ação usando o modelo padrão com o seguinte:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  Com o roteamento baseado em `IRouter`, o resultado é sempre `/Blog/ReadPost/17`, mesmo se o `BlogController` não existe ou não tem um método de ação `ReadPost`. Conforme esperado, o roteamento de ponto de extremidade no ASP.NET Core 2.2 ou posterior produz `/Blog/ReadPost/17` se o método de ação existe. *No entanto, o roteamento de ponto de extremidade produz uma cadeia de caracteres vazia se a ação não existe.* Conceitualmente, o roteamento de ponto de extremidade não pressupõe a existência do ponto de extremidade, caso a ação não exista.

* O *algoritmo de invalidação de valor de ambiente* da geração de link tem um comportamento diferente quando usado com o roteamento de ponto de extremidade.

  A *invalidação de valor de ambiente* é o algoritmo que decide quais valores de rota da solicitação em execução no momento (os valores de ambiente) podem ser usados em operações de geração de link. O roteamento convencional sempre invalidava valores de rota extras ao estabelecer o vínculo com uma ação diferente. O roteamento de atributo não tinha esse comportamento antes do lançamento do ASP.NET Core 2.2. Em versões anteriores do ASP.NET Core, os links para outra ação que usam os mesmos nomes de parâmetro de rota resultavam em erros de geração de link. No ASP.NET Core 2.2 ou posterior, as duas formas de roteamento invalidam os valores ao estabelecer o vínculo com outra ação.

  Considere o exemplo a seguir no ASP.NET Core 2.1 ou anterior. Ao estabelecer o vínculo com outra ação (ou outra página), os valores de rota podem ser reutilizados de maneiras indesejadas.

  Em */Pages/Store/Product.cshtml*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  Em */Pages/Login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  Se o URI é `/Store/Product/18` no ASP.NET Core 2.1 ou anterior, o link gerado na página Store/Info por `@Url.Page("/Login")` é `/Login/18`. O valor de `id` igual a 18 é reutilizado, mesmo que o destino do link seja uma parte totalmente diferente do aplicativo. O valor de rota de `id` no contexto da página `/Login` provavelmente é um valor de ID de usuário, e não um valor de ID do produto (product ID) da loja.

  No roteamento de ponto de extremidade com o ASP.NET Core 2.2 ou posterior, o resultado é `/Login`. Os valores de ambiente não são reutilizados quando o destino vinculado é uma ação ou uma página diferente.

* Sintaxe do parâmetro de rota de viagem de ida e volta: as barras "/" não são codificadas ao usar uma sintaxe do parâmetro catch-all de asterisco duplo (`**`).

  Durante a geração de link, o sistema de roteamento codifica o valor capturado em um parâmetro catch-all de asterisco duplo (`**`) (por exemplo, `{**myparametername}`), exceto as barras "/". O catch-all de asterisco duplo é compatível com o roteamento baseado em `IRouter` no ASP.NET Core 2.2 ou posterior.

  A sintaxe do parâmetro catch-all de asterisco único em versões anteriores do ASP.NET Core (`{*myparametername}`) permanece com suporte e as barras "/" são codificadas.

  | Rota              | Link gerado com<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (a barra "/" é codificada)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Exemplo de middleware

No exemplo a seguir, um middleware usa a API de <xref:Microsoft.AspNetCore.Routing.LinkGenerator> para criar um link para um método de ação que lista os produtos da loja. O uso do gerador de link com sua injeção em uma classe e uma chamada a `GenerateLink` está disponível para qualquer classe em um aplicativo.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Geração de URL é o processo pelo qual o roteamento pode criar um caminho de URL de acordo com um conjunto de valores de rota. Isso permite uma separação lógica entre os manipuladores de rotas e as URLs que os acessam.

A geração de URL segue um processo iterativo semelhante, mas começa com o código da estrutura ou do usuário chamando o método <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> da coleção de rotas. Cada *rota* tem seu método <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> chamado em sequência, até que um <xref:Microsoft.AspNetCore.Routing.VirtualPathData> não nulo seja retornado.

As entradas primárias para <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> são:

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

As rotas usam principalmente os valores de rota fornecidos por <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> e <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> para decidir se é possível gerar uma URL e quais valores serão incluídos. Os <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> são o conjunto de valores de rota produzidos pela correspondência da solicitação atual. Por outro lado, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> são os valores de rota que especificam como gerar a URL desejada para a operação atual. O <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> é fornecido para o caso de uma rota precisar obter serviços ou dados adicionais associados ao contexto atual.

> [!TIP]
> Considere [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) como um conjunto de substituições de [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). A geração de URL tenta reutilizar os valores de rota da solicitação atual para gerar URLs para links usando a mesma rota ou os mesmos valores de rota.

A saída de <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> é um <xref:Microsoft.AspNetCore.Routing.VirtualPathData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> é um paralelo de <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> contém o <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> da URL de saída e algumas propriedades adicionais que devem ser definidas pela rota.

A propriedade [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) contém o *caminho virtual* produzido pela rota. Dependendo das suas necessidades, talvez você precise processar ainda mais o caminho. Se você desejar renderizar a URL gerada em HTML, preceda o caminho base do aplicativo.

O [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) é uma referência à rota que gerou a URL com êxito.

As propriedades de [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) são um dicionário de dados adicionais relacionados à rota que gerou a URL. Isso é o paralelo de [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

::: moniker-end

### <a name="create-routes"></a>Criar rotas

::: moniker range="< aspnetcore-2.2"

O roteamento fornece a classe <xref:Microsoft.AspNetCore.Routing.Route> como a implementação padrão de <xref:Microsoft.AspNetCore.Routing.IRouter>. <xref:Microsoft.AspNetCore.Routing.Route> usa a sintaxe de *modelo de rota* para definir padrões que corresponderão ao caminho da URL quando <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> for chamado. <xref:Microsoft.AspNetCore.Routing.Route> usa o mesmo modelo de rota para gerar uma URL quando <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> é chamado.

::: moniker-end

A maioria dos aplicativos cria rotas chamando <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ou um dos métodos de extensão semelhantes definidos em <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Qualquer um dos métodos de extensão de <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> cria uma instância de <xref:Microsoft.AspNetCore.Routing.Route> e a adicionam à coleção de rotas.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> não aceita um parâmetro de manipulador de rotas. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> apenas adiciona rotas que são manipuladas pelo <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Para saber mais sobre o roteamento no MVC, confira <xref:mvc/controllers/routing>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> não aceita um parâmetro de manipulador de rotas. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> apenas adiciona rotas que são manipuladas pelo <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. O manipulador padrão é um `IRouter`, e o manipulador não pode manipular a solicitação. Por exemplo, o ASP.NET Core MVC normalmente é configurado como um manipulador padrão que só manipula as solicitações que correspondem a um controlador e a uma ação disponíveis. Para saber mais sobre o roteamento no MVC, confira <xref:mvc/controllers/routing>.

::: moniker-end

O exemplo de código a seguir é um exemplo de uma chamada <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> usada por uma definição de rota típica do ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Esse modelo corresponde a um caminho de URL e extrai os valores de rota. Por exemplo, o caminho `/Products/Details/17` gera os seguintes valores de rota: `{ controller = Products, action = Details, id = 17 }`.

Os valores de rota são determinados pela divisão do caminho da URL em segmentos e pela correspondência de cada segmento com o nome do *parâmetro de rota* no modelo de rota. Os parâmetros de rota são nomeados. Os parâmetros são definidos com a colocação do nome do parâmetro em chaves `{ ... }`.

O modelo anterior também pode corresponder ao caminho da URL `/` e produzir os valores `{ controller = Home, action = Index }`. Isso ocorre porque os parâmetros de rota `{controller}` e `{action}` têm valores padrão e o parâmetro de rota `id` é opcional. Um sinal de igual (`=`) seguido de um valor após o nome do parâmetro de rota define um valor padrão para o parâmetro. Um ponto de interrogação (`?`) após o nome do parâmetro de rota define o parâmetro como opcional.

Os parâmetros de rota com um valor padrão *sempre* produzem um valor de rota quando a rota corresponde. Os parâmetros opcionais não produzem um valor de rota quando não há nenhum segmento de caminho de URL correspondente. Confira a seção [Referência de modelo de rota](#route-template-reference) para obter uma descrição completa dos recursos e da sintaxe de modelo de rota.

No seguinte exemplo, a definição do parâmetro de rota `{id:int}` define uma [restrição de rota](#route-constraint-reference) para o parâmetro de rota `id`:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Esse modelo corresponde a um caminho de URL, como `/Products/Details/17`, mas não como `/Products/Details/Apples`. As restrições de rota implementam <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> e inspecionam valores de rota para verificá-los. Neste exemplo, o valor de rota `id` precisa ser conversível em um inteiro. Confira [route-constraint-reference](#route-constraint-reference) para obter uma explicação das restrições de rota fornecidas pela estrutura.

Sobrecargas adicionais de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> aceitam valores para `constraints`, `dataTokens` e `defaults`. O uso típico desses parâmetros é passar um objeto de tipo anônimo, no qual os nomes da propriedade do tipo anônimo correspondem aos nomes do parâmetro de rota.

Os seguintes exemplos de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> criam rotas equivalentes:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> A sintaxe embutida para a definição de restrições e padrões pode ser interessante para rotas simples. No entanto, há cenários, como tokens de dados, que não dão suporte à sintaxe embutida.

O seguinte exemplo demonstra mais alguns cenários:

::: moniker range=">= aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

O modelo anterior corresponde a um caminho de URL, como `/Blog/All-About-Routing/Introduction`, e extrai os valores `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Os valores de rota padrão para `controller` e `action` são produzidos pela rota, mesmo que não haja nenhum parâmetro de rota correspondente no modelo. Os valores padrão podem ser especificados no modelo de rota. O parâmetro de rota `article` é definido como um *catch-all* pela aparência de um asterisco duplo (`**`) antes do nome do parâmetro de rota. Os parâmetros de rota catch-all capturam o restante do caminho de URL e também podem corresponder à cadeia de caracteres vazia.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

O modelo anterior corresponde a um caminho de URL, como `/Blog/All-About-Routing/Introduction`, e extrai os valores `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Os valores de rota padrão para `controller` e `action` são produzidos pela rota, mesmo que não haja nenhum parâmetro de rota correspondente no modelo. Os valores padrão podem ser especificados no modelo de rota. O parâmetro de rota `article` é definido como um *catch-all* pela aparência de um asterisco (`*`) antes do nome do parâmetro de rota. Os parâmetros de rota catch-all capturam o restante do caminho de URL e também podem corresponder à cadeia de caracteres vazia.

::: moniker-end

O seguinte exemplo adiciona restrições de rota e tokens de dados:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

O modelo anterior corresponde a um caminho de URL como `/en-US/Products/5` e extrai os valores de `{ controller = Products, action = Details, id = 5 }` e os tokens de dados `{ locale = en-US }`.

![Tokens locais do Windows](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Geração de URL da classe de rota

A classe <xref:Microsoft.AspNetCore.Routing.Route> também pode executar a geração de URL pela combinação de um conjunto de valores de rota com seu modelo de rota. Logicamente, este é o processo inverso de correspondência do caminho de URL.

> [!TIP]
> Para entender melhor a geração de URL, imagine qual URL você deseja gerar e, em seguida, pense em como um modelo de rota corresponderia a essa URL. Quais valores serão produzidos? Este é o equivalente aproximado de como funciona a geração de URL na classe <xref:Microsoft.AspNetCore.Routing.Route>.

O seguinte exemplo usa uma rota padrão geral do ASP.NET Core MVC:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Com os valores de rota `{ controller = Products, action = List }`, a URL `/Products/List` é gerada. Os valores de rota são substituídos pelos parâmetros de rota correspondentes para formar o caminho de URL. Como `id` é um parâmetro de rota opcional, a URL é gerada com êxito sem um valor para `id`.

Com os valores de rota `{ controller = Home, action = Index }`, a URL `/` é gerada. Os valores de rota fornecidos correspondem aos valores padrão, sendo que os segmentos correspondentes aos valores padrão são omitidos com segurança.

A viagem de ida e volta gerada pelas duas URLs com a definição de rota a seguir (`/Home/Index` e `/`) produz os mesmos valores de rota que foram usados para gerar a URL.

> [!NOTE]
> Um aplicativo que usa o ASP.NET Core MVC deve usar <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> para gerar URLs, em vez de chamar o roteamento diretamente.

Para obter mais informações sobre a geração de URL, confira a seção [Referência de geração de URL](#url-generation-reference).

## <a name="use-routing-middleware"></a>Usar o middleware de roteamento

Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) no arquivo de projeto do aplicativo.

Adicione o roteamento ao contêiner de serviço em `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

As rotas precisam ser configuradas no método `Startup.Configure`. O aplicativo de exemplo usa as seguintes APIs:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; Corresponde apenas às solicitações HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

A tabela a seguir mostra as respostas com os URIs fornecidos.

| URI                    | Resposta                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Olá! Valores de rota: [operation, create], [id, 3] |
| `/package/track/-3`    | Olá! Valores de rota: [operation, track], [id, -3] |
| `/package/track/-3/`   | Olá! Valores de rota: [operation, track], [id, -3] |
| `/package/track/`      | A solicitação é ignorada; sem correspondência.              |
| `GET /hello/Joe`       | Olá, Joe!                                          |
| `POST /hello/Joe`      | A solicitação é ignorada; corresponde apenas a HTTP GET. |
| `GET /hello/Joe/Smith` | A solicitação é ignorada; sem correspondência.              |

::: moniker range="< aspnetcore-2.2"

Se você estiver configurando uma única rota, chame <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passando uma instância de `IRouter`. Você não precisa usar <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

::: moniker-end

A estrutura fornece um conjunto de métodos de extensão para a criação de rotas (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

::: moniker range="< aspnetcore-2.2"

Alguns dos métodos listados, como <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, exigem um <xref:Microsoft.AspNetCore.Http.RequestDelegate>. O <xref:Microsoft.AspNetCore.Http.RequestDelegate> é usado como o *manipulador de rotas* quando a rota corresponde. Outros métodos nesta família permitem configurar um pipeline de middleware a ser usado como o manipulador de rotas. Se o método `Map*` não aceitar um manipulador, como <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, ele usará o <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.

::: moniker-end

Os métodos `Map[Verb]` usam restrições para limitar a rota ao Verbo HTTP no nome do método. Por exemplo, veja <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> e <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Referência de modelo de rota

Os tokens entre chaves (`{ ... }`) definem os *parâmetros de rota* que serão associados se a rota for correspondida. Você pode definir mais de um parâmetro de rota em um segmento de rota, mas eles precisam ser separados por um valor literal. Por exemplo, `{controller=Home}{action=Index}` não é uma rota válida, já que não há nenhum valor literal entre `{controller}` e `{action}`. Esses parâmetros de rota precisam ter um nome e podem ter atributos adicionais especificados.

Um texto literal diferente dos parâmetros de rota (por exemplo, `{id}`) e do separador de caminho `/` precisa corresponder ao texto na URL. A correspondência de texto não diferencia maiúsculas de minúsculas e se baseia na representação decodificada do caminho de URLs. Para encontrar a correspondência de um delimitador de parâmetro de rota literal (`{` ou `}`), faça o escape do delimitador repetindo o caractere (`{{` ou `}}`).

Padrões de URL que tentam capturar um nome de arquivo com uma extensão de arquivo opcional apresentam considerações adicionais. Por exemplo, considere o modelo `files/{filename}.{ext?}`. Quando existem valores para `filename` e `ext`, ambos os valores são populados. Se apenas existir um valor para `filename` na URL, a rota encontrará uma correspondência, pois o ponto à direita (`.`) é opcional. As URLs a seguir correspondem a essa rota:

* `/files/myFile.txt`
* `/files/myFile`

::: moniker range=">= aspnetcore-2.2"

Você pode usar um asterisco (`*`) ou um asterisco duplo (`**`) como um prefixo para um parâmetro de rota para associá-lo ao restante do URI. Eles são chamados de parâmetros *catch-all*. Por exemplo, `blog/{**slug}` corresponde a qualquer URI que começa com `/blog` e tem qualquer valor depois dele, que é atribuído ao valor de rota `slug`. Os parâmetros catch-all também podem corresponder à cadeia de caracteres vazia.

O parâmetro catch-all faz o escape dos caracteres corretos quando a rota é usada para gerar uma URL, incluindo os caracteres separadores de caminho (`/`). Por exemplo, a rota `foo/{*path}` com valores de rota `{ path = "my/path" }` gera `foo/my%2Fpath`. Observe o escape da barra invertida. Para fazer a viagem de ida e volta dos caracteres separadores de caminho, use o prefixo do parâmetro da rota `**`. A rota `foo/{**path}` com `{ path = "my/path" }` gera `foo/my/path`.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Você pode usar o asterisco (`*`) como um prefixo para um parâmetro de rota a ser associado ao restante do URI. Isso é chamado de parâmetro *catch-all*. Por exemplo, `blog/{*slug}` corresponde a qualquer URI que começa com `/blog` e tem qualquer valor depois dele, que é atribuído ao valor de rota `slug`. Os parâmetros catch-all também podem corresponder à cadeia de caracteres vazia.

O parâmetro catch-all faz o escape dos caracteres corretos quando a rota é usada para gerar uma URL, incluindo os caracteres separadores de caminho (`/`). Por exemplo, a rota `foo/{*path}` com valores de rota `{ path = "my/path" }` gera `foo/my%2Fpath`. Observe o escape da barra invertida.

::: moniker-end

Os parâmetros de rota podem ter *valores padrão*, designados pela especificação do valor padrão após o nome do parâmetro separado por um sinal de igual (`=`). Por exemplo, `{controller=Home}` define `Home` como o valor padrão de `controller`. O valor padrão é usado se nenhum valor está presente na URL para o parâmetro. Os parâmetros de rota se tornam opcionais com o acréscimo de um ponto de interrogação (`?`) ao final do nome do parâmetro, como em `id?`. A diferença entre valores opcionais e parâmetros de rota padrão é que um parâmetro de rota com um valor padrão sempre produz um valor – um parâmetro opcional tem um valor somente quando um valor é fornecido pela URL de solicitação.

Os parâmetros de rota podem ter restrições que precisam corresponder ao valor de rota associado da URL. A adição de dois-pontos (`:`) e do nome da restrição após o nome do parâmetro de rota especifica uma *restrição embutida* em um parâmetro de rota. Se a restrição exigir argumentos, eles ficarão entre parênteses (`(...)`) após o nome da restrição. Várias restrições embutidas podem ser especificadas por meio do acréscimo de outros dois-pontos (`:`) e do nome da restrição.

O nome da restrição e os argumentos são passados para o serviço <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> para criar uma instância de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> a ser usada no processamento de URL. Por exemplo, o modelo de rota `blog/{article:minlength(10)}` especifica uma restrição `minlength` com o argumento `10`. Para obter mais informações sobre as restrições de rota e uma lista das restrições fornecidas pela estrutura, confira a seção [Referência de restrição de rota](#route-constraint-reference).

::: moniker range=">= aspnetcore-2.2"

Os parâmetros de rota também podem ter transformadores de parâmetro, que transformam o valor de um parâmetro ao gerar links e fazer a correspondência de ações e páginas com URLs. Assim como as restrições, os transformadores de parâmetro podem ser adicionados embutidos a um parâmetro de rota colocando dois-pontos (`:`) e o nome do transformador após o nome do parâmetro de rota. Por exemplo, o modelo de rota `blog/{article:slugify}` especifica um transformador `slugify`. Para obter mais informações sobre transformadores de parâmetro, confira a seção [Referência de transformador de parâmetro](#parameter-transformer-reference).

::: moniker-end

A tabela a seguir demonstra modelos de rota de exemplo e seu comportamento.

| Modelo de rota                           | URI de correspondência de exemplo    | O URI de solicitação&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Somente corresponde ao caminho único `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Faz a correspondência e define `Page` como `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Faz a correspondência e define `Page` como `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | É mapeado para o controlador `Products` e a ação `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | É mapeado para o controlador `Products` e a ação `Details` (`id` definido como 123). |
| `{controller=Home}/{action=Index}/{id?`} | `/`                     | É mapeado para o controlador `Home` e o método `Index` (`id` é ignorado).        |

Em geral, o uso de um modelo é a abordagem mais simples para o roteamento. Restrições e padrões também podem ser especificados fora do modelo de rota.

> [!TIP]
> Habilite o [Log](xref:fundamentals/logging/index) para ver como as implementações de roteamento internas, como <xref:Microsoft.AspNetCore.Routing.Route>, correspondem às solicitações.

## <a name="reserved-routing-names"></a>Nomes reservados de roteamento

As seguintes palavras-chave são nomes reservados e não podem ser usadas como nomes de rota ou parâmetros:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Referência de restrição de rota

As restrições de rota são executadas quando ocorre uma correspondência com a URL de entrada e é criado um token do caminho da URL em valores de rota. Em geral, as restrições da rota inspecionam o valor de rota associado por meio do modelo de rota e tomam uma decisão do tipo "sim/não" sobre se o valor é aceitável ou não. Algumas restrições da rota usam dados fora do valor de rota para considerar se a solicitação pode ser encaminhada. Por exemplo, a <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> pode aceitar ou rejeitar uma solicitação de acordo com o verbo HTTP. As restrições são usadas em solicitações de roteamento e na geração de link.

> [!WARNING]
> Não use restrições para a **validação de entrada**. Se as restrições forem usadas para a **validação de entrada**, uma entrada inválida resultará em uma resposta *404 – Não Encontrado*, em vez de *400 – Solicitação Inválida* com uma mensagem de erro apropriada. As restrições de rota são usadas para **desfazer a ambiguidade** entre rotas semelhantes, não para validar as entradas de uma rota específica.

A tabela a seguir demonstra restrições de rota de exemplo e seu comportamento esperado.

| restrição | Exemplo | Correspondências de exemplo | Observações |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Corresponde a qualquer inteiro |
| `bool` | `{active:bool}` | `true`, `FALSE` | Corresponde a `true` ou `false` (não diferencia maiúsculas de minúsculas) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Corresponde a um valor `DateTime` válido (na cultura invariável – veja o aviso) |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Corresponde a um valor `decimal` válido (na cultura invariável – veja o aviso) |
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Corresponde a um valor `double` válido (na cultura invariável – veja o aviso) |
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Corresponde a um valor `float` válido (na cultura invariável – veja o aviso) |
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Corresponde a um valor `Guid` válido |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Corresponde a um valor `long` válido |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | A cadeia de caracteres deve ter, no mínimo, 4 caracteres |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | A cadeia de caracteres não pode ser maior que 8 caracteres |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | A cadeia de caracteres deve ter exatamente 12 caracteres |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | A cadeia de caracteres deve ter, pelo menos, 8 e não mais de 16 caracteres |
| `min(value)` | `{age:min(18)}` | `19` | O valor inteiro deve ser, pelo menos, 18 |
| `max(value)` | `{age:max(120)}` | `91` | O valor inteiro não deve ser maior que 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | O valor inteiro deve ser, pelo menos, 18, mas não maior que 120 |
| `alpha` | `{name:alpha}` | `Rick` | A cadeia de caracteres deve consistir em um ou mais caracteres alfabéticos (`a`-`z`, não diferencia maiúsculas de minúsculas) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | A cadeia de caracteres deve corresponder à expressão regular (veja as dicas sobre como definir uma expressão regular) |
| `required` | `{name:required}` | `Rick` | Usado para impor que um valor não parâmetro está presente durante a geração de URL |

Várias restrições delimitadas por vírgula podem ser aplicadas a um único parâmetro. Por exemplo, a restrição a seguir restringe um parâmetro para um valor inteiro de 1 ou maior:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável. Essas restrições consideram que a URL não é localizável. As restrições de rota fornecidas pela estrutura não modificam os valores armazenados nos valores de rota. Todos os valores de rota analisados com base na URL são armazenados como cadeias de caracteres. Por exemplo, a restrição `float` tenta converter o valor de rota em um float, mas o valor convertido é usado somente para verificar se ele pode ser convertido em um float.

## <a name="regular-expressions"></a>Expressões regulares

A estrutura do ASP.NET Core adiciona `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` ao construtor de expressão regular. Confira <xref:System.Text.RegularExpressions.RegexOptions> para obter uma descrição desses membros.

As expressões regulares usam delimitadores e tokens semelhantes aos usados pelo Roteamento e pela linguagem C#. Os tokens de expressão regular precisam ter escape. Para usar a expressão regular `^\d{3}-\d{2}-\d{4}$` no roteamento, a expressão precisa ter os caracteres `\` (barra invertida) fornecidos na cadeia de caracteres como caracteres `\\` (barra invertida dupla) no arquivo de origem C# para fazer o escape do caractere de escape da cadeia de caracteres `\` (a menos que estejam sendo usados [literais de cadeia de caracteres textuais](/dotnet/csharp/language-reference/keywords/string)). Para fazer o escape dos caracteres de delimitador de parâmetro de roteamento (`{`, `}`, `[`, `]`), duplique os caracteres na expressão (`{{`, `}`, `[[`, `]]`). A tabela a seguir mostra uma expressão regular e a versão com escape.

| Expressão regular    | Expressão regular com escape     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

As expressões regulares usadas no roteamento geralmente começam com o caractere de acento circunflexo (`^`) e correspondem à posição inicial da cadeia de caracteres. As expressões geralmente terminam com o caractere de cifrão (`$`) e correspondem ao final da cadeia de caracteres. Os caracteres `^` e `$` garantem que a expressão regular corresponde a todo o valor do parâmetro de rota. Sem os caracteres `^` e `$`, a expressão regular corresponde a qualquer subcadeia de caracteres na cadeia de caracteres, o que geralmente não é o desejado. A tabela a seguir fornece exemplos e explica por que eles encontram ou não uma correspondência.

| Expressão   | Cadeia de Caracteres    | Corresponder a | Comentário               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Sim   | A subcadeia de caracteres corresponde     |
| `[a-z]{2}`   | 123abc456 | Sim   | A subcadeia de caracteres corresponde     |
| `[a-z]{2}`   | mz        | Sim   | Corresponde à expressão    |
| `[a-z]{2}`   | MZ        | Sim   | Não diferencia maiúsculas de minúsculas    |
| `^[a-z]{2}$` | hello     | Não    | Confira `^` e `$` acima |
| `^[a-z]{2}$` | 123abc456 | Não    | Confira `^` e `$` acima |

Para saber mais sobre a sintaxe de expressões regulares, confira [Expressões regulares do .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Para restringir um parâmetro a um conjunto conhecido de valores possíveis, use uma expressão regular. Por exemplo, `{action:regex(^(list|get|create)$)}` apenas corresponde o valor da rota `action` a `list`, `get` ou `create`. Se passada para o dicionário de restrições, a cadeia de caracteres `^(list|get|create)$` é equivalente. As restrições passadas para o dicionário de restrições (não embutidas em um modelo) que não correspondem a uma das restrições conhecidas também são tratadas como expressões regulares.

## <a name="custom-route-constraints"></a>Restrições de rota personalizadas

Além das restrições de rota internas, é possível criar restrições de rota personalizadas com a implementação da interface do <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. A interface do <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> contém um único método, `Match`, que retorna `true` quando a restrição é satisfeita. Caso contrário, retorna `false`.

Para usar uma <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> personalizada, o tipo de restrição de rota deve ser registrado com o <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> do aplicativo, no contêiner de serviço do aplicativo. O <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> é um dicionário que mapeia as chaves de restrição de rota para implementações de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> que validam essas restrições. É possível atualizar o <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> do aplicativo no `Startup.ConfigureServices` como parte de uma chamada [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou configurando <xref:Microsoft.AspNetCore.Routing.RouteOptions> diretamente com `services.Configure<RouteOptions>`. Por exemplo:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

a restrição pode então ser aplicada às rotas da maneira usual, usando o nome especificado ao registrar o tipo de restrição. Por exemplo:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

::: moniker range=">= aspnetcore-2.2"

## <a name="parameter-transformer-reference"></a>Referência de parâmetro de transformador

Transformadores de parâmetro:

* Executar ao gerar um link para um <xref:Microsoft.AspNetCore.Routing.Route>.
* Implementar `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* São configurados usando <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Usam o valor de rota do parâmetro e o transformam em um novo valor de cadeia de caracteres.
* Resultam no uso do valor transformado no link gerado.

Por exemplo, um transformador de parâmetro `slugify` personalizado em padrão de rota `blog\{article:slugify}` com `Url.Action(new { article = "MyTestArticle" })` gera `blog\my-test-article`.

Para usar um transformador de parâmetro em um padrão de rota, configure-o primeiro usando <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> em `Startup.ConfigureServices`:

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Os transformadores de parâmetro são usados pela estrutura para transformar o URI no qual um ponto de extremidade é resolvido. Por exemplo, o ASP.NET Core MVC usa os transformadores de parâmetro para transformar o valor de rota usado para corresponder a um `area`, `controller`, `action` e `page`.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Com a rota anterior, a ação `SubscriptionManagementController.GetAll()` é combinada com o URI `/subscription-management/get-all`. Um transformador de parâmetro não altera os valores de rota usados para gerar um link. Por exemplo, `Url.Action("GetAll", "SubscriptionManagement")` gera `/subscription-management/get-all`.

ASP.NET Core fornece convenções de API para usar transformadores de parâmetro com as rotas geradas:

* ASP.NET Core MVC tem a convenção de API `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention`. Essa convenção aplica um transformador de parâmetro especificado a todas as rotas de atributo no aplicativo. O transformador de parâmetro transforma os tokens de rota do atributo conforme elas são substituídas. Para obter mais informações, confira [Usar um transformador de parâmetro para personalizar a substituição de token](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* O Razor Pages tem a convenção de API `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention`. Essa convenção aplica um transformador de parâmetro especificado a todas as Razor Pages descobertas automaticamente. O transformador de parâmetro transforma os segmentos de nome de arquivo e pasta de rotas do Razor Pages. Para obter mais informações, confira [Usar um transformador de parâmetros para personalizar rotas de página](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

::: moniker-end

## <a name="url-generation-reference"></a>Referência de geração de URL

O exemplo a seguir mostra como gerar um link para uma rota com base em um dicionário de valores de rota e em um <xref:Microsoft.AspNetCore.Routing.RouteCollection>.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

O <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> gerado no final do exemplo anterior é `/package/create/123`. O dicionário fornece os valores de rota `operation` e `id` do modelo "Rastrear rota do pacote", `package/{operation}/{id}`. Para obter detalhes, consulte o código de exemplo na seção [Usar o middleware de roteamento](#use-routing-middleware) ou no [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/samples).

O segundo parâmetro para o construtor <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> é uma coleção de *valores de ambiente*. Os valores de ambiente são convenientes de serem usados porque limitam o número de valores que um desenvolvedor precisa especificar em um contexto de solicitação. Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link. Na ação `About` de um aplicativo ASP.NET Core MVC do `HomeController`, não é necessário especificar o valor de rota do controlador a ser vinculado à ação `Index` – o valor de ambiente `Home` é usado.

Os valores de ambiente que não correspondem a um parâmetro são ignorados. Os valores de ambiente também são ignorados quando um valor fornecido explicitamente substitui o valor de ambiente. A correspondência ocorre da esquerda para a direita na URL.

Valores fornecidos explicitamente, mas que não correspondem a um segmento da rota, são adicionados à cadeia de consulta. A tabela a seguir mostra o resultado do uso do modelo de rota `{controller}/{action}/{id?}`.

| Valores de ambiente                     | Valores explícitos                        | Resultado                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controlador = "Home"                | ação = "About"                       | `/Home/About`           |
| controlador = "Home"                | controlador = "Order", ação = "About" | `/Order/About`          |
| controlador = "Home", cor = "Red" | ação = "About"                       | `/Home/About`           |
| controlador = "Home"                | ação = "About", cor = "Red"        | `/Home/About?color=Red` |

Se uma rota tem um valor padrão que não corresponde a um parâmetro e esse valor é fornecido de forma explícita, ele precisa corresponder ao valor padrão:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

A geração de link somente gera um link para essa rota quando os valores correspondentes de `controller` e `action` são fornecidos.

## <a name="complex-segments"></a>Segmentos complexos

Segmentos complexos (por exemplo, `[Route("/x{token}y")]`) são processados por meio da combinação de literais da direita para a esquerda, de uma maneira diferente de Greedy. Confira [este código](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) para ver uma explicação detalhada de como os segmentos complexos são combinados. O [exemplo de código](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) não é usado pelo ASP.NET Core, mas fornece uma explicação adequada sobre segmentos complexos.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->
