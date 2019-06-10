---
title: Tratar erros no ASP.NET Core
author: tdykstra
description: Descubra como tratar erros em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/03/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 6b92cb6b68b1c70d67f42284d548729e598f9a8b
ms.sourcegitcommit: c5339594101d30b189f61761275b7d310e80d18a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/02/2019
ms.locfileid: "66458439"
---
# <a name="handle-errors-in-aspnet-core"></a>Tratar erros no ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)

Este artigo apresenta abordagens comuns para o tratamento de erros em aplicativos ASP.NET Core.

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Como baixar](xref:index#how-to-download-a-sample).) O artigo inclui instruções sobre como definir as diretivas de pré-processador (`#if`, `#endif`, `#define`) no aplicativo de exemplo para habilitar cenários diferentes.

## <a name="developer-exception-page"></a>Página de exceção do desenvolvedor

A *Página de exceção do desenvolvedor* exibe informações detalhadas sobre as exceções de solicitação. A página é disponibilizada pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Adicione código ao método `Startup.Configure` para habilitar a página quando o aplicativo estiver sendo executado no [ambiente](xref:fundamentals/environments) de desenvolvimento:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Coloque a chamada para <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> antes de qualquer middleware no qual você deseja capturar exceções.

> [!WARNING]
> Habilite a página de exceção do desenvolvedor **somente quando o aplicativo estiver em execução no ambiente de desenvolvimento**. Não é recomendável compartilhar informações de exceção detalhadas publicamente quando o aplicativo é executado em produção. Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.

A página inclui as seguintes informações sobre a exceção e a solicitação:

* Rastreamento de pilha
* Parâmetros de cadeia de caracteres de consulta (se houver algum)
* Cookies (se houver algum)
* Cabeçalhos

Para ver a Página de exceção do desenvolvedor no [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use a diretiva de pré-processador `DevEnvironment` e selecione **Disparar uma exceção** na página inicial.

## <a name="exception-handler-page"></a>Página do Manipulador de exceção

Use o Middleware de tratamento de exceção para configurar uma página personalizada de tratamento de erro para o ambiente de produção. O middleware:

* Captura e registra em log as exceções.
* Executa novamente a solicitação em um pipeline alternativo para a página ou controlador indicado. A solicitação não será executada novamente se a resposta tiver sido iniciada.

No exemplo a seguir, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> inclui o Middleware de Manipulação de Exceções em ambientes que não são de desenvolvimento:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

O modelo de aplicativo Razor Pages fornece uma Página de erro ( *.cshtml*) e uma classe <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) na pasta *Páginas*. Para um aplicativo MVC, o modelo de projeto inclui um Método de ação de erro e uma Exibição de erro. Este é o método de ação:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Não decore o método de ação do manipulador de erro com atributos de método HTTP, como `HttpGet`. Verbos explícitos impedem algumas solicitações de chegar ao método. Permita acesso anônimo ao método para que os usuários não autenticados possam capazes receber a exibição de erro.

### <a name="access-the-exception"></a>Acessar a exceção

Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acessar a exceção e o caminho de solicitação original em uma página ou controlador de manipulador de erro:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **Não** forneça informações de erro confidenciais aos clientes. Fornecer erros é um risco à segurança.

Para ver a página de tratamento de exceções no [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use as diretivas de pré-processador `ProdEnvironment` e `ErrorHandlerPage` e selecione **Disparar uma exceção** na página inicial.

## <a name="exception-handler-lambda"></a>Lambda do Manipulador de exceção

Uma alternativa a uma [página personalizada de manipulador de exceção](#exception-handler-page) é fornecer um lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>. Usar um lambda permite acessar o erro antes de retornar a resposta.

Este é um exemplo de como usar um lambda para a manipulação de exceção:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

> [!WARNING]
> **Não** forneça informações de erro confidenciais de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para clientes. Fornecer erros é um risco à segurança.

Para ver o resultado do lambda de tratamento de exceções no [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use as diretivas de pré-processador `ProdEnvironment` e `ErrorHandlerLambda` e selecione **Disparar uma exceção** na página inicial.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Por padrão, o aplicativo ASP.NET Core não fornece uma página de código de status para códigos de status HTTP, como *404 - Não Encontrado*. O aplicativo retornar um código de status e um corpo de resposta vazio. Use o middleware das Páginas de código de status para fornecer páginas de código de status.

O middleware é disponibilizado pelo pacote [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Para habilitar os manipuladores padrão somente texto para os códigos de status de erros comuns, chame <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> no método `Startup.Configure`:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Chame `UseStatusCodePages` antes do middleware de tratamento da solicitação (por exemplo, o middleware de arquivos estáticos e o middleware MVC).

Este é um exemplo de texto exibido pelos manipuladores padrão:

```
Status Code: 404; Not Found
```

Para ver um dos diversos formatos de páginas de código de status no [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use uma das diretivas de pré-processador que começam com `StatusCodePages` e selecione **Disparar um 404** na página inicial.

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages com cadeia de caracteres de formato

Para personalizar o texto e o tipo de conteúdo de resposta, use uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> que leva um tipo de conteúdo e uma cadeia de caracteres de formato:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages com lambda

Para especificar a manipulação de erro personalizada e o código de gravação de resposta, use a sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> que leva uma expressão lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirect"></a>UseStatusCodePagesWithRedirect

O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:

* Envia um código de status *302 – Encontrado* ao cliente.
* Redireciona o cliente para o local fornecido no modelo de URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

O modelo de URL pode incluir um espaço reservado `{0}` para o código de status, conforme mostrado no exemplo. Se o modelo de URL começar com um til (~), este será substituído pelo `PathBase` do aplicativo. Se você apontar para um ponto de extremidade dentro do aplicativo, crie um modo de exibição do MVC ou Razor Page para o ponto de extremidade. Consulte um exemplo de Razor Pages em *Pages/StatusCode.cshtml* no [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método normalmente é usado quando o aplicativo:

* Deveria redirecionar o cliente para um terminal diferente, geralmente em situações nas quais um aplicativo diferente processa o erro. Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade redirecionado.
* Não deveria preservar e retornar o código de status original com a resposta de redirecionamento inicial.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:

* Retorna o código de status original ao cliente.
* Gera o corpo da resposta ao executar novamente o pipeline de solicitação por meio de um caminho alternativo.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Se você apontar para um ponto de extremidade dentro do aplicativo, crie um modo de exibição do MVC ou Razor Page para o ponto de extremidade. Consulte um exemplo de Razor Pages em *Pages/StatusCode.cshtml* no [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método normalmente é usado quando o aplicativo tem que:

* Processar a solicitação sem redirecionar para um ponto de extremidade diferente. Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade originalmente solicitado.
* Preservar e retornar o código de status original com a resposta.

Os modelos de URL e cadeia de caracteres de consulta podem incluir um espaço reservado (`{0}`) para o código de status. O modelo de URL deve começar com uma barra (`/`). Ao usar um espaço reservado no caminho, verifique se o ponto de extremidade (página ou controlador) pode processar o segmento de linha. Por exemplo, uma Página do Razor para erros deve aceitar o valor de segmento de linha opcional com a diretiva `@page`:

```cshtml
@page "{code?}"
```

O ponto de extremidade que processa o erro pode obter a URL original que gerou o erro, conforme mostrado no exemplo a seguir:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Desabilitar páginas de código de status

Para desabilitar as páginas de código de status de um método de ação ou controlador MVC, use o atributo [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).

Para desabilitar as páginas de código de status de solicitações específicas em um método manipulador Razor Pages ou em um controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Código de tratamento de exceção

Código em páginas de tratamento de exceção pode gerar exceções. Geralmente, é uma boa ideia que páginas de erro de produção sejam compostas por conteúdo puramente estático.

### <a name="response-headers"></a>Cabeçalhos de resposta

Depois que os cabeçalhos de resposta são enviados:

* O aplicativo já não consegue alterar o código de status da resposta.
* As páginas de exceção ou manipuladores não podem ser executados. A resposta deve ser concluída ou a conexão será anulada.

## <a name="server-exception-handling"></a>Tratamento de exceções do servidor

Além da lógica de tratamento de exceção no aplicativo, a [implementação do servidor HTTP](xref:fundamentals/servers/index) pode lidar com algumas exceções. Se o servidor capturar uma exceção antes que os cabeçalhos de resposta sejam enviados, o servidor enviará uma resposta *500 Erro Interno do Servidor* sem um corpo de resposta. Se o servidor capturar uma exceção depois que os cabeçalhos de resposta forem enviados, o servidor fechará a conexão. As solicitações que não são manipuladas pelo aplicativo são manipuladas pelo servidor. Qualquer exceção que ocorrer quando o servidor estiver tratando a solicitação será tratada pelo tratamento de exceção do servidor. As páginas de erro personalizadas do aplicativo, o middleware de tratamento de exceção e os filtros configurados não afetam esse comportamento.

## <a name="startup-exception-handling"></a>Tratamento de exceção na inicialização

Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo. O host pode ser configurado para [capturar erros de inicialização](xref:fundamentals/host/web-host#capture-startup-errors) e [capturar erros detalhados](xref:fundamentals/host/web-host#detailed-errors).

A camada de hospedagem só poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta. Se a associação falhar:

* A camada de hospedagem registrará uma exceção crítica.
* O processo dotnet falhará.
* Nenhuma página de erro é exibida quando o servidor HTTP é [Kestrel](xref:fundamentals/servers/kestrel).

Quando executado no [IIS](/iis) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um erro *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se o processo não puder ser iniciado. Para obter mais informações, consulte <xref:host-and-deploy/iis/troubleshoot>. Para obter informações sobre como solucionar problemas de inicialização com o Serviço de Aplicativo do Azure, consulte <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="database-error-page"></a>Página de erro do banco de dados

O middleware da [Página de erro de banco de dados](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) captura as exceções relacionadas a banco de dados que podem ser resolvidas com o uso de migrações do Entity Framework. Quando estas exceções ocorrem, é gerada uma resposta HTML com detalhes das ações possíveis para resolver o problema. Esta página só deve ser habilitada no Ambiente de desenvolvimento. Habilite a página adicionando código a `Startup.Configure`:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

## <a name="exception-filters"></a>Filtros de exceção

Em aplicativos MVC, os filtros de exceção podem ser configurados globalmente, por controlador ou por ação. Em aplicativos do Razor Pages, eles podem ser configurados globalmente ou por modelo de página. Esses filtros tratam qualquer exceção sem tratamento ocorrida durante a execução de uma ação do controlador ou de outro filtro. Para obter mais informações, consulte <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Os filtros de exceção são úteis para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de tratamento de exceção. É recomendável usar o middleware. Use filtros somente onde você precisar fazer o tratamento de erro de forma diferente com base na ação de MVC escolhida.

## <a name="model-state-errors"></a>Erros de estado do modelo

Confira informações sobre como lidar com erros de estado de modelo em [model binding](xref:mvc/models/model-binding) e [Validação de modelos](xref:mvc/models/validation).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
