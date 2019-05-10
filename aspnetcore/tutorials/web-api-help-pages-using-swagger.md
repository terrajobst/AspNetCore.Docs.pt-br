---
title: Páginas de ajuda da API Web ASP.NET Core com o Swagger/OpenAPI
author: rsuter
description: Este tutorial oferece um passo a passo de como adicionar o Swagger para gerar a documentação e as páginas de ajuda para um aplicativo de API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/20/2018
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: d7a6ed158dcb464bb80c83773ed7d455b25ce44b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887721"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="7f98e-103">Páginas de ajuda da API Web ASP.NET Core com o Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="7f98e-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="7f98e-104">Por [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](http://rsuter.com)</span><span class="sxs-lookup"><span data-stu-id="7f98e-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](http://rsuter.com)</span></span>

<span data-ttu-id="7f98e-105">Ao consumir uma API Web, entender seus vários métodos pode ser um desafio para um desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="7f98e-105">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="7f98e-106">O [Swagger](https://swagger.io/), também conhecido como [OpenAPI](https://www.openapis.org/), resolve o problema da geração de páginas de ajuda e de documentação úteis para APIs Web.</span><span class="sxs-lookup"><span data-stu-id="7f98e-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="7f98e-107">Ele oferece benefícios, como documentação interativa, geração de SDK de cliente e capacidade de descoberta de API.</span><span class="sxs-lookup"><span data-stu-id="7f98e-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="7f98e-108">Neste artigo, são exibidas as implementações do .NET do Swagger [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) e [NSwag](https://github.com/RSuter/NSwag):</span><span class="sxs-lookup"><span data-stu-id="7f98e-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="7f98e-109">O **Swashbuckle.AspNetCore** é um projeto de software livre para geração de documentos do Swagger para APIs Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f98e-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="7f98e-110">O **NSwag** é outro projeto de software livre para geração de documentos do Swagger e a integração da [interface do usuário do Swagger](https://swagger.io/swagger-ui/) ou do [ReDoc](https://github.com/Rebilly/ReDoc) em APIs Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f98e-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="7f98e-111">Além disso, o NSwag oferece abordagens para gerar o código de cliente C# e TypeScript para sua API.</span><span class="sxs-lookup"><span data-stu-id="7f98e-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="7f98e-112">O que é o Swagger / OpenAPI?</span><span class="sxs-lookup"><span data-stu-id="7f98e-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="7f98e-113">O Swagger é uma especificação independente de linguagem para descrever APIs [REST](https://en.wikipedia.org/wiki/Representational_state_transfer).</span><span class="sxs-lookup"><span data-stu-id="7f98e-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="7f98e-114">O projeto de Swagger foi doado para a [Iniciativa OpenAPI](https://www.openapis.org/), na qual agora é chamado de OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="7f98e-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="7f98e-115">Ambos os nomes são intercambiáveis, mas OpenAPI é o preferencial.</span><span class="sxs-lookup"><span data-stu-id="7f98e-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="7f98e-116">Ele permite que computadores e pessoas entendam os recursos de um serviço sem nenhum acesso direto à implementação (código-fonte, acesso à rede, documentação).</span><span class="sxs-lookup"><span data-stu-id="7f98e-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="7f98e-117">Uma meta é minimizar a quantidade de trabalho necessário para conectar-se a serviços desassociados.</span><span class="sxs-lookup"><span data-stu-id="7f98e-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="7f98e-118">Outra meta é reduzir o período de tempo necessário para documentar um serviço com precisão.</span><span class="sxs-lookup"><span data-stu-id="7f98e-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="swagger-specification-swaggerjson"></a><span data-ttu-id="7f98e-119">Especificação do Swagger (swagger.json)</span><span class="sxs-lookup"><span data-stu-id="7f98e-119">Swagger specification (swagger.json)</span></span>

<span data-ttu-id="7f98e-120">O ponto central para o fluxo do Swagger é a especificação do Swagger que é, por padrão, um documento chamado *swagger.json*.</span><span class="sxs-lookup"><span data-stu-id="7f98e-120">The core to the Swagger flow is the Swagger specification&mdash;by default, a document named *swagger.json*.</span></span> <span data-ttu-id="7f98e-121">Ele é gerado pela cadeia de ferramentas do Swagger (ou por implementações de terceiros) com base no seu serviço.</span><span class="sxs-lookup"><span data-stu-id="7f98e-121">It's generated by the Swagger tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="7f98e-122">Ele descreve os recursos da API e como acessá-lo com HTTP.</span><span class="sxs-lookup"><span data-stu-id="7f98e-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="7f98e-123">Ele gera a interface do usuário do Swagger e é usado pela cadeia de ferramentas para habilitar a geração e a descoberta de código de cliente.</span><span class="sxs-lookup"><span data-stu-id="7f98e-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="7f98e-124">Aqui está um exemplo de uma especificação do Swagger, resumida:</span><span class="sxs-lookup"><span data-stu-id="7f98e-124">Here's an example of a Swagger specification, reduced for brevity:</span></span>

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a><span data-ttu-id="7f98e-125">Interface do usuário do Swagger</span><span class="sxs-lookup"><span data-stu-id="7f98e-125">Swagger UI</span></span>

<span data-ttu-id="7f98e-126">A [Interface do usuário do Swagger](https://swagger.io/swagger-ui/) oferece uma interface do usuário baseada na Web que conta com informações sobre o serviço, usando a especificação do Swagger gerada.</span><span class="sxs-lookup"><span data-stu-id="7f98e-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated Swagger specification.</span></span> <span data-ttu-id="7f98e-127">O Swashbuckle e o NSwag incluem uma versão incorporada da interface do usuário do Swagger, para que ele possa ser hospedado em seu aplicativo ASP.NET Core usando uma chamada de registro de middleware.</span><span class="sxs-lookup"><span data-stu-id="7f98e-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="7f98e-128">A interface do usuário da Web tem esta aparência:</span><span class="sxs-lookup"><span data-stu-id="7f98e-128">The web UI looks like this:</span></span>

![Interface do usuário do Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="7f98e-130">Todo método de ação pública nos controladores pode ser testado da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="7f98e-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="7f98e-131">Clique em um nome de método para expandir a seção.</span><span class="sxs-lookup"><span data-stu-id="7f98e-131">Click a method name to expand the section.</span></span> <span data-ttu-id="7f98e-132">Adicione os parâmetros necessários e, em seguida, clique em **Experimente!**.</span><span class="sxs-lookup"><span data-stu-id="7f98e-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Teste GET de Swagger de exemplo](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="7f98e-134">A versão da interface do usuário do Swagger usada para as capturas de tela é a versão 2.</span><span class="sxs-lookup"><span data-stu-id="7f98e-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="7f98e-135">Para obter um exemplo da versão 3, confira [Exemplo Petstore](http://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="7f98e-135">For a version 3 example, see [Petstore example](http://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7f98e-136">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="7f98e-136">Next steps</span></span>

* [<span data-ttu-id="7f98e-137">Introdução ao Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="7f98e-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="7f98e-138">Introdução ao NSwag</span><span class="sxs-lookup"><span data-stu-id="7f98e-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
