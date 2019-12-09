---
title: Páginas de ajuda da API Web ASP.NET Core com o Swagger/OpenAPI
author: RicoSuter
description: Este tutorial oferece um passo a passo de como adicionar o Swagger para gerar a documentação e as páginas de ajuda para um aplicativo de API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 4408e02996b958bf009903aa1e4eeda9ad4f457c
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944077"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Páginas de ajuda da API Web ASP.NET Core com o Swagger/OpenAPI

Por [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://blog.rsuter.com/)

Ao consumir uma API Web, entender seus vários métodos pode ser um desafio para um desenvolvedor. O [Swagger](https://swagger.io/), também conhecido como [OpenAPI](https://www.openapis.org/), resolve o problema da geração de páginas de ajuda e de documentação úteis para APIs Web. Ele oferece benefícios, como documentação interativa, geração de SDK de cliente e capacidade de descoberta de API.

Neste artigo, são exibidas as implementações do .NET do Swagger [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) e [NSwag](https://github.com/RicoSuter/NSwag):

* O **Swashbuckle.AspNetCore** é um projeto de software livre para geração de documentos do Swagger para APIs Web ASP.NET Core.

* O **NSwag** é outro projeto de software livre para geração de documentos do Swagger e a integração da [interface do usuário do Swagger](https://swagger.io/swagger-ui/) ou do [ReDoc](https://github.com/Rebilly/ReDoc) em APIs Web ASP.NET Core. Além disso, o NSwag oferece abordagens para gerar o código de cliente C# e TypeScript para sua API.

## <a name="what-is-swagger--openapi"></a>O que é o Swagger / OpenAPI?

O Swagger é uma especificação independente de linguagem para descrever APIs [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). O projeto de Swagger foi doado para a [Iniciativa OpenAPI](https://www.openapis.org/), na qual agora é chamado de OpenAPI. Ambos os nomes são intercambiáveis, mas OpenAPI é o preferencial. Ele permite que computadores e pessoas entendam os recursos de um serviço sem nenhum acesso direto à implementação (código-fonte, acesso à rede, documentação). Uma meta é minimizar a quantidade de trabalho necessário para conectar-se a serviços desassociados. Outra meta é reduzir o período de tempo necessário para documentar um serviço com precisão.

## <a name="swagger-specification-swaggerjson"></a>Especificação do Swagger (swagger.json)

O ponto central para o fluxo do Swagger é a especificação do Swagger que é, por padrão, um documento chamado *swagger.json*. Ele é gerado pela cadeia de ferramentas do Swagger (ou por implementações de terceiros) com base no seu serviço. Ele descreve os recursos da API e como acessá-lo com HTTP. Ele gera a interface do usuário do Swagger e é usado pela cadeia de ferramentas para habilitar a geração e a descoberta de código de cliente. Aqui está um exemplo de uma especificação do Swagger, resumida:

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

## <a name="swagger-ui"></a>Interface do usuário do Swagger

A [Interface do usuário do Swagger](https://swagger.io/swagger-ui/) oferece uma interface do usuário baseada na Web que conta com informações sobre o serviço, usando a especificação do Swagger gerada. O Swashbuckle e o NSwag incluem uma versão incorporada da interface do usuário do Swagger, para que ele possa ser hospedado em seu aplicativo ASP.NET Core usando uma chamada de registro de middleware. A interface do usuário da Web tem esta aparência:

![Interface do usuário do Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Todo método de ação pública nos controladores pode ser testado da interface do usuário. Clique em um nome de método para expandir a seção. Adicione os parâmetros necessários e, em seguida, clique em **Experimente!** .

![Teste GET de Swagger de exemplo](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> A versão da interface do usuário do Swagger usada para as capturas de tela é a versão 2. Para obter um exemplo da versão 3, confira [Exemplo Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Introdução ao Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Introdução ao NSwag](xref:tutorials/get-started-with-nswag)
