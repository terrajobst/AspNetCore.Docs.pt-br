---
title: Estado de sessão e aplicativo no ASP.NET Core
author: rick-anderson
description: Descubra abordagens para preservar o estado da sessão e do aplicativo entre as solicitações.
ms.author: riande
ms.custom: mvc
ms.date: 03/04/2019
uid: fundamentals/app-state
ms.openlocfilehash: 2e3591ac1d6b1670b27b1ed9e42f59ba2b956b37
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400704"
---
# <a name="session-and-app-state-in-aspnet-core"></a>Estado de sessão e aplicativo no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose) e [Luke Latham](https://github.com/guardrex)

O HTTP é um protocolo sem estado. Sem realizar etapas adicionais, as solicitações HTTP são mensagens independentes que não mantêm os valores de usuário nem o estado do aplicativo. Este artigo descreve várias abordagens para preservar dados do usuário e estado de aplicativo entre solicitações.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Gerenciamento de estado

O estado pode ser armazenado usando várias abordagens. Cada abordagem é descrita posteriormente neste tópico.

| Abordagem de armazenamento | Mecanismo de armazenamento |
| ---------------- | ----------------- |
| [Cookies](#cookies) | Cookies HTTP (podem incluir dados armazenados usando o código de aplicativo do lado do servidor) |
| [Estado de sessão](#session-state) | Cookies HTTP e o código de aplicativo do lado do servidor |
| [TempData](#tempdata) | Estado de sessão ou cookies HTTP |
| [Cadeias de consulta](#query-strings) | Cadeias de caracteres de consulta HTTP |
| [Campos ocultos](#hidden-fields) | Campos de formulário HTTP |
| [HttpContext.Items](#httpcontextitems) | Código do aplicativo do lado do servidor |
| [Cache](#cache) | Código do aplicativo do lado do servidor |
| [Injeção de dependência](#dependency-injection) | Código do aplicativo do lado do servidor |

## <a name="cookies"></a>Cookies

Cookies armazenam dados entre solicitações. Como os cookies são enviados com cada solicitação, seu tamanho deve ser reduzido ao mínimo. Idealmente, somente um identificador deve ser armazenado em um cookie com os dados armazenados pelo aplicativo. A maioria dos navegadores restringe o tamanho do cookie a 4.096 bytes. Somente um número limitado de cookies está disponível para cada domínio.

Uma vez que os cookies estão sujeitos à adulteração, eles devem ser validados pelo aplicativo. Os cookies podem ser excluídos por usuários e expirarem em clientes. No entanto, os cookies geralmente são a forma mais durável de persistência de dados no cliente.

Frequentemente, cookies são usados para personalização quando o conteúdo é personalizado para um usuário conhecido. O usuário é apenas identificado, e não autenticado, na maioria dos casos. O cookie pode armazenar o nome do usuário, o nome da conta ou a ID de usuário único (como um GUID). Você pode usar o cookie para acessar as configurações personalizadas do usuário, como cor preferida da tela de fundo do site.

Esteja ciente do [RGPD (Regulamento Geral sobre a Proteção de Dados) da União Europeia](https://ec.europa.eu/info/law/law-topic/data-protection) ao emitir cookies e lidar com questões de privacidade. Para obter mais informações, veja [Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) no ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Estado de sessão

Estado de sessão é um cenário do ASP.NET Core para o armazenamento de dados de usuário enquanto o usuário procura um aplicativo Web. Estado de sessão usa um armazenamento mantido pelo aplicativo para que os dados persistam entre solicitações de um cliente. Os dados da sessão são apoiados por um cache e considerados dados efêmeros&mdash;o site deve continuar funcionando sem os dados da sessão. Os dados críticos do aplicativo devem ser armazenados no banco de dados do usuário e armazenados em cache na sessão apenas como uma otimização de desempenho.

> [!NOTE]
> A sessão não é compatível com os aplicativos [SignalR](xref:signalr/index) porque um [Hub SignalR](xref:signalr/hubs) pode ser executado independente de um contexto HTTP. Por exemplo, isso pode ocorrer quando uma solicitação de sondagem longa é mantida aberta por um hub além do tempo de vida do contexto HTTP da solicitação.

O ASP.NET Core mantém o estado de sessão fornecendo um cookie para o cliente que contém uma ID de sessão, que é enviada para o aplicativo com cada solicitação. O aplicativo usa a ID da sessão para buscar os dados da sessão.

Estado de sessão exibe os seguintes comportamentos:

* Uma vez que o cookie da sessão é específico ao navegador, não é possível compartilhar sessões entre navegadores.
* Cookies da sessão são excluídos quando a sessão do navegador termina.
* Se um cookie for recebido de uma sessão expirada, será criada uma nova sessão que usa o mesmo cookie de sessão.
* Sessões vazias não são mantidas&mdash;a sessão deve ter pelo menos um valor definido nela para que mantenha a sessão entre solicitações. Quando uma sessão não é mantida, uma nova ID de sessão é gerada para cada nova solicitação.
* O aplicativo mantém uma sessão por um tempo limitado após a última solicitação. O aplicativo define o tempo limite da sessão ou usa o valor padrão de 20 minutos. Estado de sessão é ideal para armazenar dados de usuário específicos para uma sessão em particular, mas em que os dados não requerem armazenamento permanente entre sessões.
* Dados da sessão são excluídos quando a implementação [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) é chamada ou quando a sessão expira.
* Não há nenhum mecanismo padrão para informar o código do aplicativo de que um navegador cliente foi fechado ou quando o cookie de sessão foi excluído ou expirou no cliente.
Os modelos de páginas do ASP.NET Core MVC e Razor incluem suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados). Os cookies do estado de sessão não são marcados como essenciais por padrão, portanto, o estado de sessão não é funcional, a menos que o rastreamento seja permitido pelo visitante do site. Para obter mais informações, consulte <xref:security/gdpr#tempdata-provider-and-session-state-cookies-are-not-essential>.

> [!WARNING]
> Não armazene dados confidenciais no estado de sessão. O usuário não pode fechar o navegador e limpar o cookie de sessão. Alguns navegadores mantêm cookies de sessão válidos entre as janelas do navegador. Uma sessão não pode ser restringida a um único usuário&mdash;o próximo usuário pode continuar a procurar o aplicativo com o mesmo cookie de sessão.

O provedor de cache na memória armazena dados de sessão na memória do servidor em que o aplicativo reside. Em um cenário de farm de servidores:

* Use *sessões persistentes* para vincular cada sessão a uma instância de aplicativo específico em um servidor individual. O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) usa o [ARR (Application Request Routing)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) para impor as sessões persistentes por padrão. Entretanto, sessões autoadesivas podem afetar a escalabilidade e complicar atualizações de aplicativos Web. Uma abordagem melhor é usar um Redis ou cache distribuído do SQL Server, que não requer sessões persistentes. Para obter mais informações, consulte <xref:performance/caching/distributed>.
* O cookie de sessão é criptografado por meio de [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). A Proteção de Dados deve ser configurada corretamente para ler os cookies de sessão em cada computador. Para obter mais informações, veja <xref:security/data-protection/introduction> e [Provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurar o estado de sessão

::: moniker range=">= aspnetcore-2.0"

O pacote [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/), incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), fornece middleware para gerenciar o estado de sessão. Para habilitar o middleware da sessão, `Startup` deve conter:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

O pacote [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) fornece middleware para gerenciar o estado de sessão. Para habilitar o middleware da sessão, `Startup` deve conter:

::: moniker-end

* Qualquer um dos caches de memória [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). A implementação `IDistributedCache` é usada como um repositório de backup para a sessão. Para obter mais informações, consulte <xref:performance/caching/distributed>.
* Uma chamada para [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) em `ConfigureServices`.
* Uma chamada para [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) em `Configure`.

O código a seguir mostra como configurar o provedor de sessão na memória com uma implementação padrão na memória de `IDistributedCache`:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13-18,39)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Startup.cs?name=snippet1&highlight=5,7-12,19)]

::: moniker-end

A ordem do middleware é importante. No exemplo anterior, uma exceção `InvalidOperationException` ocorre quando `UseSession` é invocado após `UseMvc`. Para obter mais informações, veja [Ordenação de Middleware](xref:fundamentals/middleware/index#order).

[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) estará disponível depois que o estado de sessão for configurado.

`HttpContext.Session` não pode ser acessado antes que `UseSession` tenha sido chamado.

Não é possível criar uma nova sessão com um novo cookie de sessão depois que o aplicativo começou a gravar no fluxo de resposta. A exceção é registrada no log do servidor Web e não é exibida no navegador.

### <a name="load-session-state-asynchronously"></a>Carregue o estado de sessão de maneira assíncrona

O provedor de sessão padrão no ASP.NET Core carregará os registros da sessão do repositório de backup [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) subjacente de maneira assíncrona somente se o método [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) for chamado explicitamente antes dos métodos [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set) ou [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove). Se `LoadAsync` não for chamado primeiro, o registro de sessão subjacente será carregado de maneira síncrona, o que pode incorrer em uma penalidade de desempenho em escala.

Para que aplicativos imponham esse padrão, encapsule as implementações [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) e [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) com versões que geram uma exceção se o método `LoadAsync` não for chamado antes de `TryGetValue`, `Set` ou `Remove`. Registre as versões encapsuladas no contêiner de serviços.

### <a name="session-options"></a>Opções da sessão

Para substituir os padrões da sessão, use [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

::: moniker range=">= aspnetcore-2.0"

| Opção | Descrição |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Determina as configurações usadas para criar o cookie. [Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) assume como padrão [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). [Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) assume como padrão [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) assume como padrão [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`). [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) assume `true` como padrão . [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) assume `false` como padrão. |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | O `IdleTimeout` indica por quanto tempo a sessão pode ficar ociosa antes de seu conteúdo ser abandonado. Cada acesso à sessão redefine o tempo limite. Essa configuração aplica-se somente ao conteúdo da sessão, não ao cookie. O padrão é de 20 minutos. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | O tempo máximo permitido para carregar uma sessão do repositório ou para confirmá-la de volta para o repositório. Essa configuração pode se aplicar somente a operações assíncronas. Esse tempo limite pode ser desabilitado usando [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). O padrão é 1 minuto. |

A sessão usa um cookie para rastrear e identificar solicitações de um único navegador. Por padrão, esse cookie se chama `.AspNetCore.Session`, e usa um caminho de `/`. Uma vez que o padrão do cookie não especifica um domínio, ele não fica disponível para o script do lado do cliente na página (porque [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) usa `true` como padrão).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| Opção | Descrição |
| ------ | ----------- |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiedomain) | Determina o domínio usado para criar o cookie. `CookieDomain` não é definido por padrão. |
| [CookieHttpOnly](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiehttponly) | Determina se o navegador deve permitir que o cookie seja acessado pelo JavaScript do lado do cliente. O padrão é `true`, o que significa que o cookie é transmitido apenas às solicitações HTTP e não é disponibilizado para o script na página. |
| [CookieName](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiename) | Determina o nome do cookie usado para manter a ID da sessão. O padrão é [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`). |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiepath) | Determina o caminho usado para criar o cookie. Usa como padrão [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`). |
| [CookieSecure](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiesecure) | Determina se o cookie deve ser transmitido apenas em solicitações HTTPS. O padrão é [CookieSecurePolicy.None](/dotnet/api/microsoft.aspnetcore.http.cookiesecurepolicy) (`2`). |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | O `IdleTimeout` indica por quanto tempo a sessão pode ficar ociosa antes de seu conteúdo ser abandonado. Cada acesso à sessão redefine o tempo limite. Observe que isso só se aplica ao conteúdo da sessão, não o cookie. O padrão é de 20 minutos. |

A sessão usa um cookie para rastrear e identificar solicitações de um único navegador. Por padrão, esse cookie se chama `.AspNet.Session`, e usa um caminho de `/`.

::: moniker-end

Para substituir os padrões de sessão do cookie, use `SessionOptions`:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=13-18)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples_snapshot/1.x/SessionSample/Startup.cs?name=snippet1&highlight=5-9)]

::: moniker-end

O aplicativo usa a propriedade [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) para determinar por quanto tempo uma sessão pode ficar ociosa antes de seu conteúdo no cache do servidor ser abandonado. Essa propriedade é independente da expiração do cookie. Cada solicitação que passa pelo [Middleware da Sessão](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) redefine o tempo limite.

Estado de sessão é *sem bloqueio*. Se duas solicitações tentarem simultaneamente modificar o conteúdo de uma sessão, a última solicitação substituirá a primeira. `Session` é implementado como uma *sessão coerente*, o que significa que todo o conteúdo é armazenado junto. Quando duas solicitações buscam modificar valores de sessão diferentes, a última solicitação pode substituir as alterações de sessão feitas pelo primeira.

### <a name="set-and-get-session-values"></a>Definir e obter valores de Session

Estado de sessão é acessado de uma classe [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) Razor Pages ou classe [Controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller) MVC com [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Esta propriedade é uma implementação de [ISession](/dotnet/api/microsoft.aspnetcore.http.isession).

::: moniker range=">= aspnetcore-2.0"

A implementação de `ISession` fornece vários métodos de extensão para definir e recuperar valores de inteiro e cadeia de caracteres. Os métodos de extensão estão no namespace [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) (adicione uma instrução `using Microsoft.AspNetCore.Http;` para acessar os métodos de extensão) quando o pacote [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) for referenciado pelo projeto. Ambos os pacotes são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

A implementação de `ISession` fornece vários métodos de extensão para definir e recuperar valores de inteiro e cadeia de caracteres. Os métodos de extensão estão no namespace [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) (adicione uma instrução `using Microsoft.AspNetCore.Http;` para acessar os métodos de extensão) quando o pacote [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) for referenciado pelo projeto.

::: moniker-end

Métodos de extensão `ISession`:

* [Get(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

O exemplo a seguir recupera o valor da sessão para a chave `IndexModel.SessionKeyName` (`_Name` no aplicativo de exemplo) em uma página do Razor Pages:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

O exemplo a seguir mostra como definir e obter um número inteiro e uma cadeia de caracteres:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet1&highlight=10-11,18-19)]

::: moniker-end

Todos os dados de sessão devem ser serializados para habilitar um cenário de cache distribuído, mesmo ao usar o cache na memória. Cadeia de caracteres mínima e serializadores número são fornecidos (confira os métodos e os métodos de extensão de [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)). Tipos complexos devem ser serializados pelo usuário por meio de outro mecanismo, como JSON.

Adicione os seguintes métodos de extensão para definir e obter objetos serializáveis:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

::: moniker-end

O exemplo a seguir mostra como definir e obter um objeto serializável com os métodos de extensão:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet2&highlight=4,12)]

::: moniker-end

## <a name="tempdata"></a>TempData

O ASP.NET Core expõe a [propriedade TempData de um modelo de página do Razor Pages](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.tempdata) ou [TempData de um controlador MVC](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata). Essa propriedade armazena dados até eles serem lidos. Os métodos [Keep](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.keep) e [Peek](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.peek) podem ser usados para examinar os dados sem exclusão. TempData é particularmente útil para redirecionamento em casos em que são necessários dados para mais de uma única solicitação. TempData é implementado por provedores de TempData usando cookies ou estado de sessão.

### <a name="tempdata-providers"></a>Provedores de TempData

::: moniker range=">= aspnetcore-2.0"

No ASP.NET Core 2.0 ou posterior, o provedor TempData baseado em cookies é usado por padrão para armazenar TempData em cookies.

Os dados do cookie são criptografados usando [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), codificado com [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), em seguida, em partes. Como o cookie é dividido em partes, o limite de tamanho de cookie único encontrado no ASP.NET Core 1.x não se aplica. Os dados do cookie não são compactados porque a compactação de dados criptografados pode levar a problemas de segurança, como os ataques [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) e [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Para obter mais informações sobre o provedor de TempData baseado em cookie, consulte [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

No ASP.NET Core 1.0 e 1.1, o provedor de TempData do estado de sessão é o provedor padrão.

::: moniker-end

### <a name="choose-a-tempdata-provider"></a>Escolha um provedor de TempData

Escolher um provedor de TempData envolve várias considerações, como:

1. O aplicativo já usa estado de sessão? Se for o caso, usar o provedor de TempData do estado de sessão não terá custos adicionais para o aplicativo (além do tamanho dos dados).
2. O aplicativo usa TempData apenas raramente para quantidades relativamente pequenas de dados (até 500 bytes)? Em caso afirmativo, o provedor de TempData do cookie adiciona um pequeno custo a cada solicitação que transportar TempData. Caso contrário, o provedor de TempData do estado de sessão pode ser útil para evitar fazer viagens de ida e volta para uma grande quantidade de dados a cada solicitação até que TempData seja consumido.
3. O aplicativo é executado em um farm de servidores em vários servidores? Se for o caso, não será necessária nenhuma configuração adicional para usar o provedor TempData do cookie fora da Proteção de Dados (confira <xref:security/data-protection/introduction> e [Provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> A maioria dos clientes da Web (como navegadores da Web) impõem limites quanto ao tamanho máximo de cada cookie, o número total de cookies ou ambos. Ao usar o provedor TempData do cookie, verifique se o aplicativo não ultrapassará esses limites. Considere o tamanho total dos dados. Conta para aumento no tamanho de cookie devido à criptografia e ao agrupamento.

### <a name="configure-the-tempdata-provider"></a>Configurar o provedor de TempData

::: moniker range=">= aspnetcore-2.0"

O provedor de TempData baseado em cookie é habilitado por padrão.

Para habilitar o provedor TempData baseado em sessão, use o método de extensão [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider):

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

O código da classe `Startup` a seguir configura o provedor de TempData baseado em sessão:

[!code-csharp[](app-state/samples_snapshot_2/1.x/SessionSample/Startup.cs?name=snippet1&highlight=4,9)]

::: moniker-end

A ordem do middleware é importante. No exemplo anterior, uma exceção `InvalidOperationException` ocorre quando `UseSession` é invocado após `UseMvc`. Para obter mais informações, veja [Ordenação de Middleware](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Se o alvo for o .NET Framework e o provedor TempData baseado em sessão for usado, adicione o pacote [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) ao projeto.

## <a name="query-strings"></a>Cadeias de consulta

É possível passar uma quantidade limitada de dados de uma solicitação para outra adicionando-a à cadeia de caracteres de consulta da nova solicitação. Isso é útil para capturar o estado de uma maneira persistente que permita que links com estado inserido sejam compartilhados por email ou por redes sociais. Uma vez que cadeias de consulta de URL são públicas, nunca use cadeias de consulta para dados confidenciais.

Além da possibilidade de ocorrência de compartilhamento não intencional, incluir dados em cadeias de consulta pode criar oportunidades para ataques de [CSRF (solicitação intersite forjada)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)), que podem enganar os usuários para que eles visitem sites mal-intencionados enquanto estão autenticados. Invasores então podem roubar dados do usuário do aplicativo ou executar ações mal-intencionadas em nome do usuário. Qualquer estado de sessão ou aplicativo preservado deve se proteger contra ataques CSRF. Para obter mais informações, confira [Impedir ataques de XSRF/CSRF (solicitação intersite forjada)](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Campos ocultos

Dados podem ser salvos em campos de formulário ocultos e postados novamente na solicitação seguinte. Isso é comum em formulários com várias páginas. Uma vez que o cliente potencialmente pode adulterar os dados, o aplicativo deve sempre revalidar os dados armazenados nos campos ocultos.

## <a name="httpcontextitems"></a>HttpContext.Items

A coleção [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) é usada para armazenar dados durante o processamento de uma única solicitação. O conteúdo da coleção é descartado após uma solicitação ser processada. A coleção `Items` costuma ser usada para permitir que componentes ou middleware se comuniquem quando operam em diferentes momentos durante uma solicitação e não têm nenhuma maneira direta de passar parâmetros.

No exemplo a seguir, [middleware](xref:fundamentals/middleware/index) adiciona `isVerified` à coleção `Items`.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Mais tarde no pipeline, outro middleware poderá acessar o valor de `isVerified`:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Para middleware usado apenas por um único aplicativo, chaves `string` são aceitáveis. Middleware compartilhado entre instâncias do aplicativo deve usar chaves de objeto únicas para evitar colisões de chaves. O exemplo a seguir mostra como usar uma chave de objeto exclusiva definida em uma classe de middleware:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=5,14)]

::: moniker-end

Outros códigos podem acessar o valor armazenado em `HttpContext.Items` usando a chave exposta pela classe do middleware:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet3)]

::: moniker-end

Essa abordagem também tem a vantagem de eliminar o uso de cadeias de caracteres de chave no código.

## <a name="cache"></a>Cache

O cache é uma maneira eficiente de armazenar e recuperar dados. O aplicativo pode controlar o tempo de vida de itens em cache.

Dados armazenados em cache não são associados uma solicitação, usuário ou sessão específico. **Tenha cuidado para não armazenar em cache dados específicos do usuário que podem ser recuperados pelas solicitações de outros usuários.**

Para obter mais informações, consulte <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Injeção de dependência

Use a [Injeção de dependência](xref:fundamentals/dependency-injection) para disponibilizar dados para todos os usuários:

1. Defina um serviço que contenha os dados. Por exemplo, uma classe denominada `MyAppData` está definida:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Adicione a classe de serviço a `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Consuma a classe de serviço de dados:

    ::: moniker range=">= aspnetcore-2.0"

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="< aspnetcore-2.0"

    ```csharp
    public class HomeController : Controller
    {
        public HomeController(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

    ::: moniker-end

## <a name="common-errors"></a>Erros comuns

* "Não é possível resolver o serviço para o tipo 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' ao tentar ativar 'Microsoft.AspNetCore.Session.DistributedSessionStore'."

  Geralmente, isso é causado quando não é configurada pelo menos uma implementação de `IDistributedCache`. Para obter mais informações, consulte <xref:performance/caching/distributed> e <xref:performance/caching/memory>.

* Caso a sessão de middleware não consiga manter uma sessão (por exemplo, se o repositório de backup não estiver disponível), o middleware registrará a exceção e a solicitação continuará normalmente. Isso leva a um comportamento imprevisível.

  Por exemplo, um usuário armazena um carrinho de compras na sessão. O usuário adiciona um item ao carrinho, mas a confirmação falha. O aplicativo não sabe sobre a falha, assim, relata ao usuário que o item foi adicionado ao seu carrinho, o que não é verdade.

  A abordagem recomendada para verificar se há erros é chamar `await feature.Session.CommitAsync();` do código de aplicativo quando o aplicativo tiver terminado de gravar na sessão. `CommitAsync` gerará uma exceção se o repositório de backup não estiver disponível. Se `CommitAsync` falhar, o aplicativo poderá processar a exceção. `LoadAsync` gera sob as mesmas condições em que o armazenamento de dados não está disponível.

## <a name="additional-resources"></a>Recursos adicionais

<xref:host-and-deploy/web-farm>
