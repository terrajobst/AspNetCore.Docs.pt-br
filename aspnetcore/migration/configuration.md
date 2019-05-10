---
title: Migrar a configuração para o ASP.NET Core
author: ardalis
description: Saiba como migrar a configuração de um projeto do ASP.NET MVC para um projeto ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: c379f1f64dc5ab8aeb48055124e86e4e60d93785
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894923"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="ac8c1-103">Migrar a configuração para o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac8c1-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="ac8c1-104">Por [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="ac8c1-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="ac8c1-105">No artigo anterior, começamos [migrar um projeto ASP.NET MVC para ASP.NET Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="ac8c1-105">In the previous article, we began [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="ac8c1-106">Neste artigo, vamos migrar a configuração.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="ac8c1-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac8c1-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="ac8c1-108">Configuração da instalação</span><span class="sxs-lookup"><span data-stu-id="ac8c1-108">Setup configuration</span></span>

<span data-ttu-id="ac8c1-109">O ASP.NET Core não usa o *global. asax* e *Web. config* arquivos utilizadas de versões anteriores do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="ac8c1-110">Em versões anteriores do ASP.NET, a lógica de inicialização do aplicativo foi colocada em uma `Application_StartUp` método dentro *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-110">In earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="ac8c1-111">Posteriormente, no ASP.NET MVC, uma *Startup.cs* arquivo foi incluído na raiz do projeto; e ele foi chamado quando o aplicativo foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="ac8c1-112">ASP.NET Core tem adotado essa abordagem completamente colocando toda lógica de inicialização na *Startup.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="ac8c1-113">O *Web. config* arquivo também foi substituído no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="ac8c1-114">Configuração em si agora pode ser configurada como parte do procedimento de inicialização de aplicativo descrito em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="ac8c1-115">Configuração ainda pode utilizar arquivos XML, mas normalmente projetos do ASP.NET Core serão coloca valores de configuração em um arquivo formatado em JSON, como *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="ac8c1-116">Sistema de configuração do ASP.NET Core também poderá acessar facilmente as variáveis de ambiente, que podem fornecer um [mais local seguro e robusto](xref:security/app-secrets) para valores específicos do ambiente.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="ac8c1-117">Isso é especialmente verdadeiro para segredos, como cadeias de caracteres de conexão e as chaves de API que não devem ser verificadas no controle de origem.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="ac8c1-118">Ver [configuração](xref:fundamentals/configuration/index) para saber mais sobre a configuração no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="ac8c1-119">Neste artigo, estamos começando com o projeto ASP.NET Core migrado parcialmente desde [o artigo anterior](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="ac8c1-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="ac8c1-120">A configuração da instalação, adicione o seguinte construtor e propriedade como o *Startup.cs* arquivo localizado na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="ac8c1-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="ac8c1-121">Observe que neste ponto, o *Startup.cs* arquivo não será compilado, pois precisamos adicionar o seguinte `using` instrução:</span><span class="sxs-lookup"><span data-stu-id="ac8c1-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="ac8c1-122">Adicionar um *appSettings. JSON* arquivo na raiz do projeto usando o modelo de item apropriado:</span><span class="sxs-lookup"><span data-stu-id="ac8c1-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![Adicione AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="ac8c1-124">Migrar definições da configuração da Web. config</span><span class="sxs-lookup"><span data-stu-id="ac8c1-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="ac8c1-125">Nosso projeto ASP.NET MVC incluído a cadeia de caracteres de conexão de banco de dados necessários no *Web. config*, no `<connectionStrings>` elemento.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="ac8c1-126">Em nosso projeto do ASP.NET Core, vamos armazenar essas informações na *appSettings. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="ac8c1-127">Abra *appSettings. JSON*e observe que ele já inclui o seguinte:</span><span class="sxs-lookup"><span data-stu-id="ac8c1-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="ac8c1-128">Na linha realçada descrita acima, altere o nome do banco de dados **_CHANGE_ME** para o nome do seu banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="ac8c1-129">Resumo</span><span class="sxs-lookup"><span data-stu-id="ac8c1-129">Summary</span></span>

<span data-ttu-id="ac8c1-130">ASP.NET Core coloca toda lógica de inicialização para o aplicativo em um único arquivo, em que os serviços necessários e dependências podem ser definidas e configuradas.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="ac8c1-131">Ele substitui o *Web. config* arquivo com um recurso de configuração flexíveis que pode aproveitar uma variedade de formatos de arquivo, como JSON, bem como as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ac8c1-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
