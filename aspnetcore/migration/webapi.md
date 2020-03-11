---
title: Migrar de ASP.NET Web API para ASP.NET Core
author: ardalis
description: Saiba como migrar uma implementação de API da Web da API Web do ASP.NET 4. x para ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/webapi
ms.openlocfilehash: 7f61b78c589fc9d01061b50554e5a639e372c3d8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661843"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrar de ASP.NET Web API para ASP.NET Core

De [Scott Addie](https://twitter.com/scott_addie) e [Steve Smith](https://ardalis.com/)

Uma API Web do ASP.NET 4. x é um serviço HTTP que atinge uma ampla variedade de clientes, incluindo navegadores e dispositivos móveis. ASP.NET Core unifica os modelos de aplicativo de API Web e MVC do ASP.NET 4. x em um modelo de programação mais simples conhecido como ASP.NET Core MVC. Este artigo demonstra as etapas necessárias para migrar da API Web do ASP.NET 4. x para ASP.NET Core MVC.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Examine o projeto de API Web do ASP.NET 4. x

Como ponto de partida, este artigo usa o projeto *ProductsApp* criado em [introdução com ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). Nesse projeto, um projeto de API Web simples ASP.NET 4. x é configurado da seguinte maneira.

No *global.asax.cs*, é feita uma chamada para `WebApiConfig.Register`:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

A classe `WebApiConfig` é encontrada na pasta *App_Start* e tem um método de `Register` estático:

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

Essa classe configura o [Roteamento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), embora ele não esteja realmente sendo usado no projeto. Ele também configura a tabela de roteamento, que é usada pelo ASP.NET Web API. Nesse caso, a API Web ASP.NET 4. x espera que as URLs correspondam ao formato `/api/{controller}/{id}`, com `{id}` sendo opcional.

O projeto *ProductsApp* inclui um controlador. O controlador herda de `ApiController` e contém duas ações:

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

O modelo de `Product` usado pelo `ProductsController` é uma classe simples:

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

As seções a seguir demonstram a migração do projeto de API Web para ASP.NET Core MVC.

## <a name="create-destination-project"></a>Criar projeto de destino

Conclua as seguintes etapas no Visual Studio:

* Vá para o **arquivo** > **novo** > **projeto** > **outros tipos de projeto** > soluções do **Visual Studio**. Selecione **solução em branco**e nomeie a solução *WebAPIMigration*. Clique no botão **OK**.
* Adicione o projeto *ProductsApp* existente à solução.
* Adicione um novo projeto de **aplicativo Web ASP.NET Core** à solução. Selecione a estrutura de destino **.NET Core** na lista suspensa e selecione o modelo projeto de **API** . Nomeie o projeto *ProductsCore*e clique no botão **OK** .

A solução agora contém dois projetos. As seções a seguir explicam como migrar o conteúdo do projeto *ProductsApp* para o projeto *ProductsCore* .

## <a name="migrate-configuration"></a>Migrar configuração

ASP.NET Core não usa a pasta *App_Start* ou o arquivo *global. asax* , e o arquivo *Web. config* é adicionado no momento da publicação. *Startup.cs* é a substituição para *global. asax* e está localizada na raiz do projeto. A classe `Startup` manipula todas as tarefas de inicialização do aplicativo. Para obter mais informações, consulte <xref:fundamentals/startup>.

No ASP.NET Core MVC, o roteamento de atributo é incluído por padrão quando <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> é chamado em `Startup.Configure`. A chamada a seguir `UseMvc` substitui o arquivo */webapiconfig.cs App_Start* do projeto *ProductsApp* :

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>Migrar modelos e controladores

Copie sobre o controlador do projeto *ProductApp* e o modelo que ele usa. Siga estas etapas:

1. Copie os *controladores/ProductsController. cs* do projeto original para o novo.
1. Copie a pasta *modelos* inteira do projeto original para o novo.
1. Altere os namespaces dos arquivos copiados para que correspondam ao novo nome do projeto (*ProductsCore*). Ajuste a instrução `using ProductsApp.Models;` em *ProductsController.cs* também.

Neste ponto, a criação do aplicativo resulta em vários erros de compilação. Os erros ocorrem porque os seguintes componentes não existem no ASP.NET Core:

* Classe `ApiController`
* Namespace `System.Web.Http`
* interface `IHttpActionResult`

Corrija os erros da seguinte maneira:

1. Alterar `ApiController` para <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Adicione `using Microsoft.AspNetCore.Mvc;` para resolver a referência de `ControllerBase`.
1. Excluir `using System.Web.Http;`.
1. Altere o tipo de retorno da ação de `GetProduct` de `IHttpActionResult` para `ActionResult<Product>`.

Simplifique a instrução `return` da ação de `GetProduct` para o seguinte:

```csharp
return product;
```

## <a name="configure-routing"></a>Configurar o roteamento

Configure o roteamento da seguinte maneira:

1. Marque a classe `ProductsController` com os seguintes atributos:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    O atributo [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) anterior configura o padrão de roteamento de atributo do controlador. O atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) torna o atributo roteando um requisito para todas as ações neste controlador.

    O roteamento de atributos dá suporte a tokens, como `[controller]` e `[action]`. No tempo de execução, cada token é substituído pelo nome do controlador ou da ação, respectivamente, ao qual o atributo foi aplicado. Os tokens reduzem o número de cadeias de caracteres mágicos no projeto. Os tokens também garantem que as rotas permaneçam sincronizadas com os controladores e ações correspondentes quando refatoração de renomeação automática são aplicadas.
1. Defina o modo de compatibilidade do projeto para ASP.NET Core 2,2:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    A alteração anterior:

    * É necessário para usar o atributo `[ApiController]` no nível do controlador.
    * Optamos pelos comportamentos potencialmente em potencial introduzidos no ASP.NET Core 2,2.
1. Habilite solicitações HTTP Get para as ações de `ProductController`:
    * Aplique o atributo [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) à ação `GetAllProducts`.
    * Aplique o atributo `[HttpGet("{id}")]` à ação `GetProduct`.

Após as alterações anteriores e a remoção de instruções de `using` não usadas, o arquivo *ProductsController.cs* terá esta aparência:

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

Execute o projeto migrado e navegue até `/api/products`. Uma lista completa de três produtos é exibida. Navegue até `/api/products/1`. O primeiro produto é exibido.

## <a name="compatibility-shim"></a>Correção de compatibilidade

A biblioteca [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fornece um Shim de compatibilidade para mover projetos de API Web ASP.NET 4. x para ASP.NET Core. O Shim de compatibilidade estende ASP.NET Core para dar suporte a um número de convenções da API Web 2 do ASP.NET 4. x. O exemplo de porta anteriormente neste documento é básico o bastante para que o Shim de compatibilidade seja desnecessário. Para projetos maiores, o uso do Shim de compatibilidade pode ser útil para a ponte temporária da lacuna da API entre ASP.NET Core e a API Web 2 do ASP.NET 4. x.

O Shim da compatibilidade da API Web deve ser usado como uma medida temporária para dar suporte à migração de projetos de API Web grandes ASP.NET 4. x para ASP.NET Core. Ao longo do tempo, os projetos devem ser atualizados para usar padrões de ASP.NET Core em vez de depender do Shim de compatibilidade.

Os recursos de compatibilidade incluídos no `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluem:

* Adiciona um tipo de `ApiController` para que os tipos base dos controladores não precisem ser atualizados.
* Habilita a associação de modelo de estilo de API Web. ASP.NET Core funções de associação de modelo MVC da mesma forma que o do ASP.NET 4. x MVC 5, por padrão. O Shim de compatibilidade altera a associação de modelo para ser mais semelhante às convenções de associação de modelo da API Web 2 do ASP.NET 4. x. Por exemplo, tipos complexos são associados automaticamente do corpo da solicitação.
* Estende a associação de modelo para que as ações do controlador possam ter parâmetros do tipo `HttpRequestMessage`.
* Adiciona os formatadores de mensagem, permitindo que as ações retornem resultados do tipo `HttpResponseMessage`.
* Adiciona métodos de resposta adicionais que as ações da API Web 2 podem ter usado para atender às respostas:
  * geradores de `HttpResponseMessage`:
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Métodos de resultado da ação:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Adiciona uma instância de `IContentNegotiator` ao contêiner de injeção de dependência (DI) do aplicativo e disponibiliza os tipos relacionados à negociação de conteúdo de [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Exemplos desses tipos incluem `DefaultContentNegotiator` e `MediaTypeFormatter`.

Para usar o Shim de compatibilidade:

1. Instale o pacote NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Registre os serviços da correção de compatibilidade com o contêiner de DI do aplicativo chamando `services.AddMvc().AddWebApiConventions()` em `Startup.ConfigureServices`.
1. Defina rotas específicas da API Web usando `MapWebApiRoute` no `IRouteBuilder` na chamada de `IApplicationBuilder.UseMvc` do aplicativo.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
