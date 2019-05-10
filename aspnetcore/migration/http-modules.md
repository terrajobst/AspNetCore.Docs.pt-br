---
title: Migrar módulos e manipuladores HTTP para middleware do ASP.NET Core
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 516230a66ee3edba986c91d79684256aa8e4c994
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087012"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migrar módulos e manipuladores HTTP para middleware do ASP.NET Core

Por [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)

Este artigo mostra como migrar do ASP.NET existentes [módulos HTTP e manipuladores de System. webServer](/iis/configuration/system.webserver/) para o ASP.NET Core [middleware](xref:fundamentals/middleware/index).

## <a name="modules-and-handlers-revisited"></a>Módulos e manipuladores revistos

Antes de prosseguir para o middleware do ASP.NET Core, vamos primeiro recapitular como funcionam os manipuladores e módulos HTTP:

![Manipulador de módulos](http-modules/_static/moduleshandlers.png)

**Os manipuladores são:**

* As classes que implementam [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* Usado para manipular solicitações com um determinado nome de arquivo ou a extensão, como *.report*

* [Configurado](/iis/configuration/system.webserver/handlers/) em *Web. config*

**Módulos são:**

* As classes que implementam [IHttpModule](/dotnet/api/system.web.ihttpmodule)

* Chamado para cada solicitação

* Capazes de curto-circuito (interromper o processamento adicional de uma solicitação)

* Capaz de adicionar à resposta HTTP, ou criar seus próprios

* [Configurado](/iis/configuration/system.webserver/modules/) em *Web. config*

**A ordem na qual os módulos de processam solicitações de entrada é determinada por:**

1. O [ciclo de vida do aplicativo](https://msdn.microsoft.com/library/ms227673.aspx), que é disparado pelo ASP.NET um eventos da série: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Cada módulo pode criar um manipulador para um ou mais eventos.

2. Para o mesmo evento, a ordem na qual eles foram configurados na *Web. config*.

Além de módulos, você pode adicionar manipuladores para os eventos de ciclo de vida para seus *Global.asax.cs* arquivo. Esses manipuladores executar após os manipuladores nos módulos configurados.

## <a name="from-handlers-and-modules-to-middleware"></a>De manipuladores e módulos para middleware

**Middleware são mais simples do que os manipuladores e módulos HTTP:**

* Módulos, manipuladores *Global.asax.cs*, *Web. config* (exceto para a configuração do IIS) e o ciclo de vida do aplicativo serão excluídos

* As funções dos módulos e manipuladores foram tomadas a tecla TAB middleware

* Middleware são configurados usando o código em vez de em *Web. config*

* [Ramificação do pipeline](xref:fundamentals/middleware/index#use-run-and-map) permite que você envia solicitações para o middleware específico, com base em não apenas a URL, mas também em cabeçalhos de solicitação, cadeias de caracteres de consulta, etc.

**Middleware são muito semelhantes aos módulos:**

* Invocado em princípio, para cada solicitação

* Capazes de curto-circuito a uma solicitação por [não passar a solicitação para o próximo middleware](#http-modules-shortcircuiting-middleware)

* Capaz de criar sua própria resposta HTTP

**Middleware e módulos são processados em uma ordem diferente:**

* Pedido de middleware é baseado na ordem em que eles são ser inseridos no pipeline de solicitação, enquanto a ordem dos módulos se baseia principalmente na [ciclo de vida do aplicativo](https://msdn.microsoft.com/library/ms227673.aspx) eventos

* Ordem de middleware para respostas é o inverso do que para solicitações, enquanto a ordem dos módulos é o mesmo para solicitações e respostas

* Consulte [criar um pipeline de middleware com o IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Middleware](http-modules/_static/middleware.png)

Observe como na imagem acima, o middleware de autenticação sofrido curto-circuito a solicitação.

## <a name="migrating-module-code-to-middleware"></a>Migrando o código de módulo para middleware

Um módulo HTTP existente será semelhante a este:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Conforme mostrado na [Middleware](xref:fundamentals/middleware/index) página, um middleware do ASP.NET Core é uma classe que expõe um `Invoke` levando método um `HttpContext` e retornando um `Task`. Seu novo middleware terá esta aparência:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

O modelo de middleware anterior foi obtido da seção em [escrever middleware](xref:fundamentals/middleware/write).

O *MyMiddlewareExtensions* classe auxiliar torna mais fácil de configurar seu middleware em seu `Startup` classe. O `UseMyMiddleware` método adiciona sua classe de middleware ao pipeline de solicitação. Serviços necessários para o middleware são injetados no construtor do middleware.

<a name="http-modules-shortcircuiting-middleware"></a>

O módulo pode encerrar uma solicitação, por exemplo, se o usuário não autorizado:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Um middleware lida com isso, não chamando `Invoke` no próximo middleware no pipeline. Tenha em mente que isso não encerra completamente a solicitação, porque o middleware anterior ainda será chamado quando a resposta faz seu caminho através do pipeline.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Ao migrar a funcionalidade do módulo para seu novo middleware, você pode achar que seu código não era compilado porque o `HttpContext` classe foi alterado significativamente no ASP.NET Core. [Mais tarde](#migrating-to-the-new-httpcontext), você verá como migrar para ASP.NET Core HttpContext novo.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migrando inserção de módulo no pipeline de solicitação

Módulos HTTP normalmente são adicionados ao pipeline de solicitação usando *Web. config*:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Converter isso por [adicionando seu novo middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) ao pipeline de solicitação no seu `Startup` classe:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

O ponto exato no pipeline de onde você insere seu middleware novo depende do evento que ele tratado como um módulo (`BeginRequest`, `EndRequest`, etc.) e sua ordem na sua lista de módulos *Web. config*.

Conforme anteriormente não mencionado, há mais nenhum ciclo de vida do aplicativo no ASP.NET Core e a ordem na qual as respostas são processadas pelo middleware é diferente da ordem usada pelos módulos. Isso pode tornar sua decisão de ordenação mais desafiador.

Se a ordenação se torna um problema, você pode dividir seu módulo em vários componentes de middleware que podem ser ordenados de forma independente.

## <a name="migrating-handler-code-to-middleware"></a>Migrando o código de manipulador para middleware

Um manipulador HTTP é semelhante a:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

Em seu projeto ASP.NET Core, isso pode ser traduzido para um middleware semelhante a esta:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Este middleware é muito semelhante ao middleware correspondente aos módulos. A única diferença é que não há aqui nenhuma chamada para `_next.Invoke(context)`. Isso faz sentido, porque o manipulador não é no final do pipeline de solicitação, portanto, não haverá nenhum próximo middleware para invocar.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migrando inserção manipulador no pipeline de solicitação

Configurar um manipulador HTTP é feito na *Web. config* e pode ter esta aparência:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Você pode converter isso adicionando seu novo middleware de manipulador para o pipeline de solicitação no seu `Startup` classe, semelhante ao middleware convertido de módulos. O problema com essa abordagem é que ele seria enviar todas as solicitações para seu novo middleware de manipulador. No entanto, você precisará apenas solicitações com uma determinada extensão para alcançar seu middleware. Essa seria a mesma funcionalidade que tinha com seu manipulador HTTP.

Uma solução é ramificar o pipeline para solicitações com uma determinada extensão, usando o `MapWhen` método de extensão. Você pode fazer isso no mesmo `Configure` método onde você pode adicionar o outro middleware:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` usa esses parâmetros:

1. Um lambda que usa o `HttpContext` e retorna `true` se a solicitação deve ir para baixo a ramificação. Isso significa que você pode ramificar solicitações não apenas com base em sua extensão, mas também em cabeçalhos de solicitação, parâmetros de cadeia de caracteres de consulta, etc.

2. Um lambda que usa um `IApplicationBuilder` e adiciona o middleware para a ramificação. Isso significa que você pode adicionar outro middleware para a ramificação na frente do seu manipulador middleware.

Middleware adicionado ao pipeline antes da ramificação será invocada em todas as solicitações; a ramificação não terá impacto sobre eles.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Opções de middleware usando o padrão de opções de carregamento

Alguns manipuladores e módulos têm opções de configuração que são armazenadas em *Web. config*. No entanto, no ASP.NET Core um novo modelo de configuração é usado no lugar de *Web. config*.

O novo [sistema de configuração](xref:fundamentals/configuration/index) fornece essas opções para resolver isso:

* Injetar diretamente as opções para o middleware, como mostra a [próxima seção](#loading-middleware-options-through-direct-injection).

* Use o [padrão de opções](xref:fundamentals/configuration/options):

1. Crie uma classe para manter suas opções de middleware, por exemplo:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Store os valores de opção

   O sistema de configuração permite que você armazene valores de opção em qualquer local desejado. No entanto, a maioria dos locais use *appSettings. JSON*, portanto, vamos dar essa abordagem:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection* aqui é um nome de seção. Ele não precisa ser igual ao nome da sua classe de opções.

3. Associar os valores de opção com a classe de opções

    O padrão de opções usa a estrutura de injeção de dependência do ASP.NET Core para associar o tipo de opções (como `MyMiddlewareOptions`) com um `MyMiddlewareOptions` objeto que tem as opções reais.

    Atualização de seu `Startup` classe:

   1. Se você estiver usando *appSettings. JSON*, adicione-o ao construtor de configuração no `Startup` construtor:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Configure o serviço de opções:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Associe suas opções de sua classe de opções:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Injete as opções para o construtor de middleware. Isso é semelhante a injeção de opções em um controlador.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   O [UseMiddleware](#http-modules-usemiddleware) método de extensão que adiciona o middleware para o `IApplicationBuilder` se encarrega de injeção de dependência.

   Isso não é limitado a `IOptions` objetos. Qualquer outro objeto requer que seu middleware pode ser injetado dessa maneira.

## <a name="loading-middleware-options-through-direct-injection"></a>Opções de middleware por meio de injeção direta de carregamento

O padrão de opções tem a vantagem que ele cria o acoplamento solto entre valores de opções e seus consumidores. Depois que você associou a uma classe de opções com os valores de opções propriamente dito, qualquer outra classe pode obter acesso às opções por meio da estrutura de injeção de dependência. Não é necessário para passar ao redor de valores de opções.

Isso divide entanto se você quiser usar o mesmo middleware duas vezes, com opções diferentes. Por exemplo um middleware de autorização usado em ramificações diferentes, permitindo que diferentes funções. É possível associar dois objetos diferentes opções com a classe de opções de um.

A solução é obter os objetos de opções com os valores de opções propriamente dito no seu `Startup` de classe e passá-las diretamente para cada instância do seu middleware.

1. Adicionar uma segunda chave a ser *appSettings. JSON*

   Para adicionar um segundo conjunto de opções para o *appSettings. JSON* de arquivo, use uma nova chave de para identificá-lo exclusivamente:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Recuperar valores de opções e passá-los para o middleware. O `Use...` método de extensão (o que adiciona seu middleware ao pipeline) é um lugar lógico para transmitir os valores de opção: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Habilite o middleware para utilize um parâmetro de opções. Forneça uma sobrecarga da `Use...` método de extensão (que usa o parâmetro options e passa-o para `UseMiddleware`). Quando `UseMiddleware` é chamado com parâmetros, ele passa os parâmetros para o construtor de middleware quando ele instancia o objeto de middleware.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Observe como isso encapsula o objeto de opções em um `OptionsWrapper` objeto. Isso implementa `IOptions`, conforme o esperado pelo construtor de middleware.

## <a name="migrating-to-the-new-httpcontext"></a>Migrando para o novo HttpContext

Você viu antes que o `Invoke` método no seu middleware usa um parâmetro de tipo `HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` foi alterado significativamente no ASP.NET Core. Esta seção mostra como converter as propriedades mais usadas da [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) para o novo `Microsoft.AspNetCore.Http.HttpContext`.

### <a name="httpcontext"></a>HttpContext

**HttpContext. Items** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**ID de solicitação exclusiva (nenhum equivalente System.Web.HttpContext)**

Fornece uma id exclusiva para cada solicitação. Muito útil para incluir em seus logs.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext.Request.HttpMethod** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext.Request.QueryString** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext.Request.Url** e **HttpContext.Request.RawUrl** traduzir para:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext.Request.IsSecureConnection** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext.Request.UserHostAddress** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext.Request.Cookies** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext.Request.Headers** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext.Request.UserAgent** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext.Request.UrlReferrer** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext.Request.ContentType** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext.Request.Form** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Ler os valores de formulário apenas se for o tipo de conteúdo sub *x-www-form-urlencoded* ou *dados de formulário*.

**HttpContext.Request.InputStream** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Use esse código somente em um middleware de tipo de manipulador, no final de um pipeline.
>
>Você pode ler o corpo bruto, conforme mostrado acima apenas uma vez por solicitação. Middleware de tentativa de ler o corpo após a primeira leitura lê um corpo vazio.
>
>Isso não se aplica à leitura de um formulário, conforme mostrado anteriormente, porque isso é feito de um buffer.

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext.Response.Status** e **HttpContext.Response.StatusDescription** traduzir para:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext.Response.ContentEncoding** e **HttpContext.Response.ContentType** traduzir para:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext.Response.ContentType** em seu próprio também se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext.Response.Output** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

Atendendo a um arquivo é discutida [aqui](../fundamentals/request-features.md#middleware-and-request-features).

**HttpContext.Response.Headers**

Enviar cabeçalhos de resposta é complicado pelo fato de que se você defini-las depois que nada foi escrito para o corpo da resposta, eles não funcionará.

A solução é definir um método de retorno de chamada que será chamado direita antes de gravar do início da resposta. Isso é feito melhor no início do `Invoke` método em seu middleware. É esse método de retorno de chamada que define os cabeçalhos de resposta.

O código a seguir define um método de retorno de chamada chamado `SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

O `SetHeaders` método de retorno de chamada teria esta aparência:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

Cookies de viagem para o navegador em um *Set-Cookie* cabeçalho de resposta. Como resultado, o envio de cookies requer mesmo retorno de chamada usado para enviar cabeçalhos de resposta:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

O `SetCookies` método de retorno de chamada deve ser semelhante ao seguinte:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Recursos adicionais

* [Visão geral de módulos HTTP e de manipuladores HTTP](/iis/configuration/system.webserver/)
* [Configuração](xref:fundamentals/configuration/index)
* [Inicialização de aplicativos](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware/index)
