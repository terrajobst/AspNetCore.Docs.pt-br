---
title: Migrar da API Web ASP.NET para ASP.NET Core
author: ardalis
description: Saiba como migrar uma implementação de API da web da API de Web do ASP.NET 4.x para ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/10/2018
uid: migration/webapi
ms.openlocfilehash: 9806c502f8f5244740f9f9614657a40cfaa03314
ms.sourcegitcommit: 1872d2e6f299093c78a6795a486929ffb0bbffff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53216827"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrar da API Web ASP.NET para ASP.NET Core

Por [Scott Addie](https://twitter.com/scott_addie) e [Steve Smith](https://ardalis.com/)

Uma API da Web do ASP.NET 4. x é um serviço HTTP que atinja uma ampla gama de clientes, incluindo navegadores e dispositivos móveis. Unifica o ASP.NET Core MVC do ASP.NET do 4.x e modelos de aplicativo de API da Web em um modelo de programação mais simples conhecido como o ASP.NET Core MVC. Este artigo demonstra as etapas necessárias para migrar da API de Web do ASP.NET 4.x para ASP.NET Core MVC.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/webapi/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [net-core-prereqs-vs-2.2](../includes/net-core-prereqs-vs-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Examine o projeto de API da Web do ASP.NET 4.x

Como ponto de partida, este artigo usa o *ProductsApp* projeto criado no [Introdução à API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). Nesse projeto, um projeto de API Web simples ASP.NET 4.x está configurado da seguinte maneira.

Na *Global.asax.cs*, é feita uma chamada para `WebApiConfig.Register`:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

O `WebApiConfig` classe é encontrada na *App_Start* pasta e tem um estático `Register` método:

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

Essa classe configura [roteamento de atributo](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), embora na verdade não está sendo usado no projeto. Ele também configura a tabela de roteamento, que é usada pela API Web ASP.NET. Nesse caso, a API da Web do ASP.NET 4.x espera que as URLs para corresponder ao formato `/api/{controller}/{id}`, com `{id}` opcionais.

O *ProductsApp* projeto inclui um controlador. O controlador herda de `ApiController` e contém duas ações:

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

O `Product` modelo usado pelo `ProductsController` é uma classe simples:

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

As seções a seguir demonstram a migração do projeto API da Web para ASP.NET Core MVC.

## <a name="create-destination-project"></a>Criar o projeto de destino

Conclua as seguintes etapas no Visual Studio:

* Vá para **arquivo** > **nova** > **projeto** > **outros tipos de projeto**  >  **Soluções do visual Studio**. Selecione **solução em branco**e o nome da solução *WebAPIMigration*. Clique o **Okey** botão.
* Adicionar existente *ProductsApp* projeto à solução.
* Adicione um novo **aplicativo Web ASP.NET Core** projeto à solução. Selecione o **.NET Core** estrutura na lista suspensa de destino e, em seguida, selecione o **API** modelo de projeto. Nomeie o projeto *ProductsCore*e clique no **Okey** botão.

Agora, a solução contém dois projetos. As seções a seguir explicam a migrar os *ProductsApp* o conteúdo do projeto para o *ProductsCore* projeto.

## <a name="migrate-configuration"></a>Migrar configuração

ASP.NET Core não usa o *App_Start* pasta ou o *global. asax* arquivo e o *Web. config* arquivo for adicionado no momento da publicação. *Startup.CS* é o substituto do *global. asax* e está localizado na raiz do projeto. O `Startup` classe manipula todas as tarefas de inicialização do aplicativo. Para obter mais informações, consulte <xref:fundamentals/startup>.

No ASP.NET Core MVC, o roteamento de atributo é incluído por padrão quando <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> é chamado no `Startup.Configure`. O seguinte `UseMvc` chamar substitui o *ProductsApp* do projeto *app_start* arquivo:

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>Migrar modelos e controladores

Copiar sobre a *ProductApp* controlador do projeto e o modelo utilizado. Siga estas etapas:

1. Cópia *Controllers/ProductsController.cs* do projeto original para o novo.
1. Copiar todo o *modelos* pasta do projeto original para o novo.
1. Alterar os namespaces dos arquivos copiados para coincidir com o novo nome do projeto (*ProductsCore*). Ajustar a `using ProductsApp.Models;` instrução no *ProductsController.cs* muito.

Neste ponto, criando os resultados do aplicativo em um número de erros de compilação. Os erros ocorrem porque os seguintes componentes não existem no ASP.NET Core:

* Classe `ApiController`
* Namespace `System.Web.Http`
* `IHttpActionResult` Interface

Corrija os erros da seguinte maneira:

1. Alteração `ApiController` para <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Adicione `using Microsoft.AspNetCore.Mvc;` para resolver o `ControllerBase` referência.
1. Excluir `using System.Web.Http;`.
1. Alterar o `GetProduct` tipo de retorno da ação de `IHttpActionResult` para `ActionResult<Product>`.

Simplificar a `GetProduct` da ação `return` instrução para o seguinte:

```csharp
return product;
```

## <a name="configure-routing"></a>Configurar o roteamento

Configure o roteamento da seguinte maneira:

1. Decore o `ProductsController` classe com os seguintes atributos:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Precedente [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) configura o atributo padrão de roteamento de atributo do controlador. O [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo faz o roteamento de um requisito para todas as ações nesse controlador de atributo.

    Roteamento de atributo oferece suporte a tokens, como `[controller]` e `[action]`. Em tempo de execução, cada token é substituído com o nome do controlador ou ação, respectivamente, para o qual o atributo foi aplicado. Os tokens de reduzem o número de cadeias de caracteres mágicas no projeto. Os tokens de também garantem rotas permanecerem sincronizadas com os correspondentes controladores e ações quando automático renomear refatorações são aplicadas.
1. Defina o modo de compatibilidade do projeto para o ASP.NET Core 2.2:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    A alteração anterior:

    * É necessário para usar o `[ApiController]` atributo no nível do controlador.
    * Aceita o potencialmente significativa comportamentos introduzidos no ASP.NET Core 2.2.
1. Habilitar as solicitações HTTP Get para o `ProductController` ações:
    * Aplicar a [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo para o `GetAllProducts` ação.
    * Aplicar a `[HttpGet("{id}")]` de atributo para o `GetProduct` ação.

Após as alterações anteriores e a remoção do não utilizado `using` instruções *ProductsController.cs* arquivo tem esta aparência:

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

Execute o projeto migrado e navegue até `/api/products`. É exibida uma lista completa dos três produtos. Navegue para `/api/products/1`. O primeiro produto será exibida.

## <a name="compatibility-shim"></a>Correção de compatibilidade

O [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) biblioteca fornece um shim de compatibilidade para mover projetos de API da Web do ASP.NET 4.x ASP.NET Core. O shim de compatibilidade se estende do ASP.NET Core para dar suporte a uma série de convenções da API de Web do ASP.NET 4. x 2. O exemplo portado anteriormente neste documento é bastante básico que o shim de compatibilidade era desnecessário. Para projetos maiores, usem o shim de compatibilidade pode ser útil para temporariamente preenchendo a lacuna de API entre o ASP.NET Core e ASP.NET 4.x Web API 2.

O shim de compatibilidade de API da Web destina-se a ser usado como uma medida temporária para dar suporte à migração grande API da Web projetos do ASP.NET 4.x ASP.NET Core. Ao longo do tempo, os projetos devem ser atualizados para usar os padrões do ASP.NET Core em vez de usar o shim de compatibilidade.

Recursos de compatibilidade incluídos no `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluem:

* Adiciona um `ApiController` tipo de forma que os tipos de base dos controladores não precisam ser atualizados.
* Habilita a associação de modelo de estilo de API da Web. Funções de associação de maneira semelhante do ASP.NET de modelo do ASP.NET Core MVC 4. x MVC 5, por padrão. As alterações de correção de compatibilidade associação de modelo para ser mais semelhante a convenções de associação de modelo do ASP.NET Web API 2 de 4. x. Por exemplo, tipos complexos são vinculados automaticamente do corpo da solicitação.
* Estende a associação de modelo para que as ações do controlador podem usar parâmetros de tipo `HttpRequestMessage`.
* Adiciona os formatadores de mensagem, permitindo que as ações para retornar os resultados do tipo `HttpResponseMessage`.
* Adiciona métodos de resposta adicionais que ações de API Web 2 podem ter usado para servir as respostas:
  * `HttpResponseMessage` geradores de:
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Métodos de ação de resultados:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Adiciona uma instância do `IContentNegotiator` para o aplicativo disponibiliza os tipos de conteúdo relacionado a negociação do e do contêiner de DI (injeção) de dependência [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Exemplos de tais tipos `DefaultContentNegotiator` e `MediaTypeFormatter`.

Para usar o shim de compatibilidade:

1. Instalar o [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) pacote do NuGet.
1. Registrar os serviços do shim de compatibilidade com o contêiner de injeção de dependência do aplicativo chamando `services.AddMvc().AddWebApiConventions()` em `Startup.ConfigureServices`.
1. Definir web específicos de API roteia usando `MapWebApiRoute` sobre o `IRouteBuilder` no aplicativo do `IApplicationBuilder.UseMvc` chamar.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
