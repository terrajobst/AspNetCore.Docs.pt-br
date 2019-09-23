---
title: Ativação de middleware com um contêiner de terceiros no ASP.NET Core
author: guardrex
description: Saiba como usar o middleware fortemente tipado com a ativação baseada em alocador e um contêiner de terceiros no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: e54a2bd366457fa2d898b7ee26e95021aec5389b
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187084"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>Ativação de middleware com um contêiner de terceiros no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Este artigo demonstra como usar o <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> e o <xref:Microsoft.AspNetCore.Http.IMiddleware> como um ponto de extensibilidade para a ativação do [middleware](xref:fundamentals/middleware/index) com um contêiner de terceiros. Para obter informações introdutórias sobre `IMiddlewareFactory` e `IMiddleware`, confira <xref:fundamentals/middleware/extensibility>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo demonstra a ativação do middleware por uma implementação de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`. O exemplo usa o contêiner de DI (injeção de dependência) [Simple Injector](https://simpleinjector.org).

A implementação do middleware do exemplo registra o valor fornecido por um parâmetro de cadeia de consulta (`key`). O middleware usa um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor da cadeia de consulta em um banco de dados em memória.

> [!NOTE]
> O aplicativo de exemplo usa o [Simple Injector](https://github.com/simpleinjector/SimpleInjector) simplesmente para fins de demonstração. O uso do Simple Injector não é um endosso. As abordagens de ativação do middleware descritas na documentação do Simple Injector e nos problemas do GitHub são recomendadas pelos mantenedores do Simple Injector. Para obter mais informações, confira a [Documentação do Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) e o [repositório do GitHub do Simple Injector](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware.

No aplicativo de amostra, um alocador de middleware é implementado para criar uma instância de `SimpleInjectorActivatedMiddleware`. O alocador de middleware usa o contêiner do Simple Injector para resolver o middleware:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo.

Middleware ativado por uma implementação de `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.cs*):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Uma extensão é criada para o middleware (*Middleware/MiddlewareExtensions.cs*):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

O `Startup.ConfigureServices` precisa executar várias tarefas:

* Configure o contêiner do Simple Injector.
* Registre o alocador e o middleware.
* Disponibilize o contexto do banco de dados do aplicativo por meio do contêiner do Simple Injector.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

O middleware é registrado no pipeline de processamento da solicitação em `Startup.Configure`:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este artigo demonstra como usar o <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> e o <xref:Microsoft.AspNetCore.Http.IMiddleware> como um ponto de extensibilidade para a ativação do [middleware](xref:fundamentals/middleware/index) com um contêiner de terceiros. Para obter informações introdutórias sobre `IMiddlewareFactory` e `IMiddleware`, confira <xref:fundamentals/middleware/extensibility>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo demonstra a ativação do middleware por uma implementação de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`. O exemplo usa o contêiner de DI (injeção de dependência) [Simple Injector](https://simpleinjector.org).

A implementação do middleware do exemplo registra o valor fornecido por um parâmetro de cadeia de consulta (`key`). O middleware usa um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor da cadeia de consulta em um banco de dados em memória.

> [!NOTE]
> O aplicativo de exemplo usa o [Simple Injector](https://github.com/simpleinjector/SimpleInjector) simplesmente para fins de demonstração. O uso do Simple Injector não é um endosso. As abordagens de ativação do middleware descritas na documentação do Simple Injector e nos problemas do GitHub são recomendadas pelos mantenedores do Simple Injector. Para obter mais informações, confira a [Documentação do Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) e o [repositório do GitHub do Simple Injector](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fornece métodos para a criação do middleware.

No aplicativo de amostra, um alocador de middleware é implementado para criar uma instância de `SimpleInjectorActivatedMiddleware`. O alocador de middleware usa o contêiner do Simple Injector para resolver o middleware:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> define o middleware para o pipeline de solicitação do aplicativo.

Middleware ativado por uma implementação de `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Uma extensão é criada para o middleware (*Middleware/MiddlewareExtensions.cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

O `Startup.ConfigureServices` precisa executar várias tarefas:

* Configure o contêiner do Simple Injector.
* Registre o alocador e o middleware.
* Disponibilize o contexto do banco de dados do aplicativo por meio do contêiner do Simple Injector.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

O middleware é registrado no pipeline de processamento da solicitação em `Startup.Configure`:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Middleware](xref:fundamentals/middleware/index)
* [Ativação de middleware de fábrica](xref:fundamentals/middleware/extensibility)
* [Repositório do GitHub do Simple Injector](https://github.com/simpleinjector/SimpleInjector)
* [Documentação do Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html)
