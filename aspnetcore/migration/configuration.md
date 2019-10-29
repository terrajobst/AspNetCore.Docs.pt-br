---
title: Migrar configuração para ASP.NET Core
author: ardalis
description: Saiba como migrar a configuração de um projeto MVC do ASP.NET para um projeto ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: 455e66b94dd69ee6aab88768b64c525d56b8bbcf
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033899"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="1e9b8-103">Migrar configuração para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e9b8-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="1e9b8-104">Por [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="1e9b8-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="1e9b8-105">No artigo anterior, começamos a [migrar um projeto ASP.NET MVC para ASP.NET Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="1e9b8-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="1e9b8-106">Neste artigo, migramos a configuração.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="1e9b8-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1e9b8-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="1e9b8-108">Configuração da instalação</span><span class="sxs-lookup"><span data-stu-id="1e9b8-108">Setup configuration</span></span>

<span data-ttu-id="1e9b8-109">ASP.NET Core não usa mais os arquivos *global. asax* e *Web. config* que as versões anteriores do ASP.net utilizaram.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="1e9b8-110">Nas versões anteriores do ASP.NET, a lógica de inicialização do aplicativo foi colocada em um método `Application_StartUp` dentro de *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="1e9b8-111">Posteriormente, no ASP.NET MVC, um arquivo *Startup.cs* foi incluído na raiz do projeto; e ele foi chamado quando o aplicativo foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="1e9b8-112">ASP.NET Core adotou essa abordagem completamente colocando toda a lógica de inicialização no arquivo *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="1e9b8-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="1e9b8-113">O arquivo *Web. config* também foi substituído em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="1e9b8-114">A configuração em si agora pode ser configurada, como parte do procedimento de inicialização do aplicativo descrito em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="1e9b8-115">A configuração ainda pode utilizar arquivos XML, mas normalmente ASP.NET Core projetos posicionarão valores de configuração em um arquivo formatado em JSON, como *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="1e9b8-116">O sistema de configuração de ASP.NET Core também pode acessar facilmente variáveis de ambiente, o que pode fornecer um [local mais seguro e robusto](xref:security/app-secrets) para valores específicos do ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="1e9b8-117">Isso é especialmente verdadeiro para segredos como cadeias de conexão e chaves de API que não devem ser verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="1e9b8-118">Consulte [configuração](xref:fundamentals/configuration/index) para saber mais sobre a configuração no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="1e9b8-119">Para este artigo, estamos começando com o projeto de ASP.NET Core migrado parcialmente do [artigo anterior](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="1e9b8-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="1e9b8-120">Para configurar a configuração, adicione o seguinte construtor e propriedade ao arquivo *Startup.cs* localizado na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="1e9b8-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="1e9b8-121">Observe que neste ponto, o arquivo *Startup.cs* não será compilado, pois ainda precisamos adicionar a seguinte instrução `using`:</span><span class="sxs-lookup"><span data-stu-id="1e9b8-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="1e9b8-122">Adicione um arquivo *appSettings. JSON* à raiz do projeto usando o modelo de item apropriado:</span><span class="sxs-lookup"><span data-stu-id="1e9b8-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![Adicionar a AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="1e9b8-124">Migrar definições de configuração de Web. config</span><span class="sxs-lookup"><span data-stu-id="1e9b8-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="1e9b8-125">Nosso projeto MVC ASP.NET incluía a cadeia de conexão de banco de dados necessária em *Web. config*, no elemento `<connectionStrings>`.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="1e9b8-126">Em nosso projeto de ASP.NET Core, vamos armazenar essas informações no arquivo *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="1e9b8-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="1e9b8-127">Abra *appSettings. JSON*e observe que ele já inclui o seguinte:</span><span class="sxs-lookup"><span data-stu-id="1e9b8-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="1e9b8-128">Na linha realçada descrita acima, altere o nome do banco de dados de **_CHANGE_ME** para o nome do seu banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="1e9b8-129">Resumo</span><span class="sxs-lookup"><span data-stu-id="1e9b8-129">Summary</span></span>

<span data-ttu-id="1e9b8-130">ASP.NET Core coloca toda a lógica de inicialização para o aplicativo em um único arquivo, no qual os serviços e as dependências necessários podem ser definidos e configurados.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="1e9b8-131">Ele substitui o arquivo *Web. config* por um recurso de configuração flexível que pode aproveitar uma variedade de formatos de arquivo, como JSON, bem como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="1e9b8-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
