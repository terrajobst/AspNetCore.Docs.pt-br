---
title: Migrar módulos e manipuladores HTTP para ASP.NET Core middleware
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: bdf27ccb742d4bc05bac71e6c96d71c38dcb4b62
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659680"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migrar módulos e manipuladores HTTP para ASP.NET Core middleware

Este artigo mostra como migrar [módulos e manipuladores HTTP ASP.net existentes de System. WebServer](/iis/configuration/system.webserver/) para ASP.NET Core [middleware](xref:fundamentals/middleware/index).

## <a name="modules-and-handlers-revisited"></a>Módulos e manipuladores revisitados

Antes de prosseguir para ASP.NET Core middleware, vamos primeiro recapitular como funcionam os módulos e manipuladores HTTP:

![Manipulador de módulos](http-modules/_static/moduleshandlers.png)

**Os manipuladores são:**

* Classes que implementam [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* Usado para lidar com solicitações com um determinado nome de arquivo ou extensão, como *. Report*

* [Configurado](/iis/configuration/system.webserver/handlers/) no *Web. config*

**Os módulos são:**

* Classes que implementam [IHttpModule](/dotnet/api/system.web.ihttpmodule)

* Invocado para cada solicitação

* Capacidade de curto-circuito (interromper o processamento de uma solicitação)

* Capaz de adicionar à resposta HTTP ou criar suas próprias

* [Configurado](/iis/configuration/system.webserver/modules/) no *Web. config*

**A ordem na qual os módulos processam solicitações de entrada é determinada por:**

1. O [ciclo de vida do aplicativo](https://msdn.microsoft.com/library/ms227673.aspx), que é uma série de eventos acionados por ASP.net: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Cada módulo pode criar um manipulador para um ou mais eventos.

2. Para o mesmo evento, a ordem na qual eles são configurados em *Web. config*.

Além dos módulos, você pode adicionar manipuladores para os eventos do ciclo de vida ao arquivo *global.asax.cs* . Esses manipuladores são executados após os manipuladores nos módulos configurados.

## <a name="from-handlers-and-modules-to-middleware"></a>De manipuladores e módulos para middleware

**O middleware é mais simples do que módulos e manipuladores HTTP:**

* Módulos, manipuladores, *global.asax.cs*, *Web. config* (exceto para a configuração do IIS) e o ciclo de vida do aplicativo foram eliminados

* As funções dos módulos e dos manipuladores foram tomadas pelo middleware

* O middleware é configurado usando código em vez de *Web. config*

* O [Branch de pipeline](xref:fundamentals/middleware/index#use-run-and-map) permite enviar solicitações para middleware específico, com base na URL, mas também em cabeçalhos de solicitação, cadeias de consulta, etc.

**Os middleware são muito semelhantes aos módulos:**

* Invocado em princípio para cada solicitação

* Capacidade de curto-circuito de uma solicitação, [não passando a solicitação para o próximo middleware](#http-modules-shortcircuiting-middleware)

* Capaz de criar sua própria resposta HTTP

**Os módulos e middleware são processados em uma ordem diferente:**

* A ordem do middleware baseia-se na ordem em que eles são inseridos no pipeline de solicitação, enquanto a ordem dos módulos é principalmente baseada nos eventos do [ciclo de vida do aplicativo](https://msdn.microsoft.com/library/ms227673.aspx)

* A ordem do middleware para respostas é o inverso do para solicitações, enquanto a ordem dos módulos é a mesma para solicitações e respostas

* Confira [criar um pipeline de middleware com IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Middleware](http-modules/_static/middleware.png)

Observe como na imagem acima, o middleware de autenticação curto-circuitou a solicitação.

## <a name="migrating-module-code-to-middleware"></a>Migrando o código do módulo para o middleware

Um módulo HTTP existente terá uma aparência semelhante a esta:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Conforme mostrado na página [middleware](xref:fundamentals/middleware/index) , um middleware ASP.NET Core é uma classe que expõe um método `Invoke` fazendo uma `HttpContext` e retornando um `Task`. Seu novo middleware terá a seguinte aparência:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

O modelo de middleware anterior foi tirado da seção sobre o [middleware de escrita](xref:fundamentals/middleware/write).

A classe auxiliar *MyMiddlewareExtensions* torna mais fácil configurar seu middleware em sua classe `Startup`. O método `UseMyMiddleware` adiciona sua classe de middleware ao pipeline de solicitação. Os serviços exigidos pelo middleware são injetados no construtor do middleware.

<a name="http-modules-shortcircuiting-middleware"></a>

O módulo pode encerrar uma solicitação, por exemplo, se o usuário não estiver autorizado:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Um middleware trata isso sem chamar `Invoke` no próximo middleware no pipeline. Lembre-se de que isso não encerra completamente a solicitação, porque o middleware anterior ainda será invocado quando a resposta voltar ao pipeline.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Ao migrar a funcionalidade do seu módulo para o novo middleware, você pode achar que seu código não é compilado porque a classe de `HttpContext` mudou significativamente em ASP.NET Core. [Posteriormente](#migrating-to-the-new-httpcontext), você verá como migrar para o novo ASP.NET Core HttpContext.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migrando a inserção de módulo para o pipeline de solicitação

Módulos HTTP são normalmente adicionados ao pipeline de solicitação usando *Web. config*:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Converta isso [adicionando o novo middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) ao pipeline de solicitação em sua classe de `Startup`:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

O ponto exato no pipeline em que você insere o novo middleware depende do evento que ele tratou como um módulo (`BeginRequest`, `EndRequest`, etc.) e sua ordem na lista de módulos em *Web. config*.

Como mencionado anteriormente, não há nenhum ciclo de vida do aplicativo em ASP.NET Core e a ordem em que as respostas são processadas pelo middleware difere da ordem usada pelos módulos. Isso pode tornar sua decisão de pedido mais desafiadora.

Se a ordenação se tornar um problema, você poderá dividir o módulo em vários componentes de middleware que podem ser ordenados de forma independente.

## <a name="migrating-handler-code-to-middleware"></a>Migrando o código do manipulador para o middleware

Um manipulador HTTP é semelhante a este:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

Em seu projeto de ASP.NET Core, você converteria isso em um middleware semelhante a este:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Esse middleware é muito semelhante ao middleware correspondente aos módulos. A única diferença real é que aqui não há nenhuma chamada para `_next.Invoke(context)`. Isso faz sentido, porque o manipulador está no final do pipeline de solicitação, portanto, não haverá o próximo middleware a ser invocado.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migrando a inserção do manipulador para o pipeline de solicitação

A configuração de um manipulador HTTP é feita em *Web. config* e é semelhante a esta:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Você pode converter isso adicionando o novo middleware do manipulador ao pipeline de solicitação em sua classe `Startup`, semelhante ao middleware convertido de módulos. O problema dessa abordagem é que ele enviaria todas as solicitações ao seu novo middleware de manipulador. No entanto, você só deseja que as solicitações com uma determinada extensão acessem o middleware. Isso forneceria a mesma funcionalidade que você tinha com o manipulador HTTP.

Uma solução é ramificar o pipeline para solicitações com uma determinada extensão, usando o método de extensão `MapWhen`. Você faz isso no mesmo método `Configure` em que adiciona o outro middleware:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` usa estes parâmetros:

1. Um lambda que usa o `HttpContext` e retorna `true` se a solicitação deve descer o Branch. Isso significa que você pode ramificar solicitações não apenas com base em sua extensão, mas também em cabeçalhos de solicitação, parâmetros de cadeia de caracteres de consulta, etc.

2. Um lambda que usa um `IApplicationBuilder` e adiciona todo o middleware para a ramificação. Isso significa que você pode adicionar middleware adicional à ramificação na frente do middleware do manipulador.

Middleware adicionado ao pipeline antes que a ramificação seja invocada em todas as solicitações; o Branch não terá nenhum impacto sobre eles.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Carregando opções de middleware usando o padrão de opções

Alguns módulos e manipuladores têm opções de configuração que são armazenadas em *Web. config*. No entanto, no ASP.NET Core um novo modelo de configuração é usado no lugar de *Web. config*.

O novo [sistema de configuração](xref:fundamentals/configuration/index) oferece essas opções para resolver isso:

* Insira diretamente as opções no middleware, conforme mostrado na [próxima seção](#loading-middleware-options-through-direct-injection).

* Use o [padrão de opções](xref:fundamentals/configuration/options):

1. Crie uma classe para manter suas opções de middleware, por exemplo:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Armazenar os valores de opção

   O sistema de configuração permite que você armazene valores de opção em qualquer lugar que desejar. No entanto, a maioria dos sites usa *appSettings. JSON*, portanto, seguiremos essa abordagem:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection* aqui é um nome de seção. Ele não precisa ser o mesmo que o nome da sua classe Options.

3. Associar os valores de opção à classe Options

    O padrão de opções usa a estrutura de injeção de dependência do ASP.NET Core para associar o tipo de opções (como `MyMiddlewareOptions`) a um objeto `MyMiddlewareOptions` que tem as opções reais.

    Atualize sua classe de `Startup`:

   1. Se você estiver usando *appSettings. JSON*, adicione-o ao construtor de configuração no construtor de `Startup`:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Configurar o serviço de opções:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Associe suas opções à sua classe Options:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Insira as opções em seu construtor de middleware. Isso é semelhante a injetar opções em um controlador.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   O método de extensão [UseMiddleware](#http-modules-usemiddleware) que adiciona seu middleware à `IApplicationBuilder` cuida da injeção de dependência.

   Isso não é limitado a `IOptions` objetos. Qualquer outro objeto que seu middleware requer pode ser injetado dessa maneira.

## <a name="loading-middleware-options-through-direct-injection"></a>Carregando opções de middleware por meio de injeção direta

O padrão de opções tem a vantagem de que ele cria um acoplamento flexível entre valores de opções e seus consumidores. Depois que você tiver associado uma classe Options com os valores de opções reais, qualquer outra classe poderá obter acesso às opções por meio da estrutura de injeção de dependência. Não há necessidade de passar pelos valores de opções.

Isso é interrompido no entanto, se você quiser usar o mesmo middleware duas vezes, com opções diferentes. Por exemplo, um middleware de autorização usado em diferentes ramificações que permitem diferentes funções. Você não pode associar dois objetos de opções diferentes com a classe One Options.

A solução é obter os objetos de opções com os valores de opções reais em sua classe `Startup` e passá-los diretamente para cada instância do middleware.

1. Adicionar uma segunda chave para *appSettings. JSON*

   Para adicionar um segundo conjunto de opções ao arquivo *appSettings. JSON* , use uma nova chave para identificá-la exclusivamente:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Recuperar valores de opções e passá-los para o middleware. O método de extensão de `Use...` (que adiciona o middleware ao pipeline) é um local lógico para passar os valores de opção: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Habilite o middleware para obter um parâmetro de opções. Forneça uma sobrecarga do método de extensão `Use...` (que usa o parâmetro options e o transmite para `UseMiddleware`). Quando `UseMiddleware` é chamado com parâmetros, ele passa os parâmetros para o construtor de middleware quando cria uma instância do objeto de middleware.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Observe como isso encapsula o objeto Options em um objeto `OptionsWrapper`. Isso implementa `IOptions`, conforme esperado pelo construtor de middleware.

## <a name="migrating-to-the-new-httpcontext"></a>Migrando para o novo HttpContext

Vimos anteriormente que o método `Invoke` em seu middleware usa um parâmetro do tipo `HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` mudou significativamente em ASP.NET Core. Esta seção mostra como converter as propriedades usadas com mais frequência de [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) para o novo `Microsoft.AspNetCore.Http.HttpContext`.

### <a name="httpcontext"></a>HttpContext

**HttpContext. Items** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**ID de solicitação exclusiva (sem equivalente de System. Web. HttpContext)**

Fornece uma ID exclusiva para cada solicitação. Muito útil para incluir em seus logs.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext. Request. HttpMethod** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext. Request. QueryString** traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext. Request. URL** e **HttpContext. Request. RawUrl** traduzir para:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext. Request. IsSecureConnection** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext. Request. UserHostAddress** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext. Request. cookies** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext. Request. RequestContext. RouteData** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext. Request. Headers** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext. Request. UserAgent** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext. Request. UrlReferrer** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext. Request. ContentType** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext. Request. Form** é convertido em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Leia valores de formulário somente se o subtipo de conteúdo for *x-www-form-urlencoded* ou *form-data*.

**HttpContext. Request. InputStream** se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Use este código somente em um middleware de tipo de manipulador, no final de um pipeline.
>
>Você pode ler o corpo bruto, conforme mostrado acima apenas uma vez por solicitação. O middleware tentando ler o corpo após a primeira leitura lerá um corpo vazio.
>
>Isso não se aplica à leitura de um formulário, como mostrado anteriormente, porque isso é feito de um buffer.

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext. Response. status** e **HttpContext. Response. StatusDescription** são convertidos em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext. Response. ContentEncoding** e **HttpContext. Response. ContentType** são convertidos em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext. Response. ContentType** em seu próprio também se traduz em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext. Response. Output** é convertido em:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext. Response. TransmitFile**

O fornecimento de um arquivo é discutido [aqui](../fundamentals/request-features.md#middleware-and-request-features).

**HttpContext. Response. Headers**

O envio de cabeçalhos de resposta é complicado pelo fato de que, se você defini-los depois que algo tiver sido gravado no corpo da resposta, eles não serão enviados.

A solução é definir um método de retorno de chamada que será chamado imediatamente antes de gravar na resposta começar. Isso é melhor feito no início do método de `Invoke` em seu middleware. Esse é o método de retorno de chamada que define seus cabeçalhos de resposta.

O código a seguir define um método de retorno de chamada chamado `SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

O método de retorno de chamada `SetHeaders` ficaria assim:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext. Response. cookies**

Os cookies viajam para o navegador em um cabeçalho de resposta *Set-cookie* . Como resultado, o envio de cookies requer o mesmo retorno de chamada usado para enviar cabeçalhos de resposta:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

O método de retorno de chamada `SetCookies` seria semelhante ao seguinte:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Recursos adicionais

* [Visão geral de manipuladores HTTP e módulos HTTP](/iis/configuration/system.webserver/)
* [Configuração](xref:fundamentals/configuration/index)
* [Inicialização de aplicativos](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware/index)
