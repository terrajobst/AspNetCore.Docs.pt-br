---
title: Registro em log no ASP.NET Core
author: tdykstra
description: Saiba mais sobre a estrutura de registros no ASP.NET Core. Descubra os provedores de log internos e saiba mais sobre os provedores de terceiros populares.
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/01/2019
uid: fundamentals/logging/index
ms.openlocfilehash: f028a94a9b2014129a96cff2c06ba00f99ea5d15
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500400"
---
# <a name="logging-in-aspnet-core"></a><span data-ttu-id="6254e-104">Registro em log no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6254e-104">Logging in ASP.NET Core</span></span>

<span data-ttu-id="6254e-105">Por [Steve Smith](https://ardalis.com/) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="6254e-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="6254e-106">O ASP.NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="6254e-106">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="6254e-107">Este artigo mostra como usar a API de registro em log com provedores internos.</span><span class="sxs-lookup"><span data-stu-id="6254e-107">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="6254e-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6254e-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="6254e-109">Adicionar provedores</span><span class="sxs-lookup"><span data-stu-id="6254e-109">Add providers</span></span>

<span data-ttu-id="6254e-110">Um provedor de log exibe ou armazena logs.</span><span class="sxs-lookup"><span data-stu-id="6254e-110">A logging provider displays or stores logs.</span></span> <span data-ttu-id="6254e-111">Por exemplo, o provedor de Console exibe os logs no console, e o provedor do Azure Application Insights armazena-os no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="6254e-111">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="6254e-112">Os logs podem ser enviados para vários destinos por meio da adição de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="6254e-112">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="6254e-113">Para adicionar um provedor, chame o método de extensão `Add{provider name}` do provedor no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6254e-113">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="6254e-114">O modelo de projeto padrão chama o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que adiciona os seguintes provedores de log:</span><span class="sxs-lookup"><span data-stu-id="6254e-114">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="6254e-115">Console</span><span class="sxs-lookup"><span data-stu-id="6254e-115">Console</span></span>
* <span data-ttu-id="6254e-116">Depurar</span><span class="sxs-lookup"><span data-stu-id="6254e-116">Debug</span></span>
* <span data-ttu-id="6254e-117">EventSource (a partir do ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="6254e-117">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="6254e-118">Se você usar `CreateDefaultBuilder`, poderá substituir os provedores padrão por aqueles que preferir.</span><span class="sxs-lookup"><span data-stu-id="6254e-118">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="6254e-119">Chame <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e adicione os provedores desejados.</span><span class="sxs-lookup"><span data-stu-id="6254e-119">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="6254e-120">Para usar um provedor, instale o pacote NuGet e chame o método de extensão do provedor em uma instância de <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span><span class="sxs-lookup"><span data-stu-id="6254e-120">To use a provider, install its NuGet package and call the provider's extension method on an instance of <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="6254e-121">A [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do ASP.NET Core fornece a instância de `ILoggerFactory`.</span><span class="sxs-lookup"><span data-stu-id="6254e-121">ASP.NET Core [dependency injection (DI)](xref:fundamentals/dependency-injection) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="6254e-122">Os métodos de extensão `AddConsole` e `AddDebug` são definidos nos pacotes [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) e [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/).</span><span class="sxs-lookup"><span data-stu-id="6254e-122">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="6254e-123">Cada método de extensão chama o método `ILoggerFactory.AddProvider`, passando uma instância do provedor.</span><span class="sxs-lookup"><span data-stu-id="6254e-123">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span>

> [!NOTE]
> <span data-ttu-id="6254e-124">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adiciona provedores de log no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6254e-124">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adds logging providers in the `Startup.Configure` method.</span></span> <span data-ttu-id="6254e-125">Para obter saída de log do código que é executado anteriormente, adicione provedores de log no construtor de classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="6254e-125">To obtain log output from code that executes earlier, add logging providers in the `Startup` class constructor.</span></span>

::: moniker-end

<span data-ttu-id="6254e-126">Saiba mais sobre [provedores de log internos](#built-in-logging-providers) e [provedores de log de terceiros](#third-party-logging-providers) mais adiante no artigo.</span><span class="sxs-lookup"><span data-stu-id="6254e-126">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="6254e-127">Criar logs</span><span class="sxs-lookup"><span data-stu-id="6254e-127">Create logs</span></span>

<span data-ttu-id="6254e-128">Obtenha um objeto <xref:Microsoft.Extensions.Logging.ILogger%601> da DI.</span><span class="sxs-lookup"><span data-stu-id="6254e-128">Get an <xref:Microsoft.Extensions.Logging.ILogger%601> object from DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="6254e-129">O exemplo de controlador a seguir cria os logs `Information` e `Warning`.</span><span class="sxs-lookup"><span data-stu-id="6254e-129">The following controller example creates `Information` and `Warning` logs.</span></span> <span data-ttu-id="6254e-130">A *categoria* é `TodoApiSample.Controllers.TodoController` (o nome de classe totalmente qualificado do `TodoController` no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="6254e-130">The *category* is `TodoApiSample.Controllers.TodoController` (the fully qualified class name of `TodoController` in the sample app):</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="6254e-131">O exemplo do Razor Pages a seguir cria logs com `Information` como o *nível* e `TodoApiSample.Pages.AboutModel` como a *categoria*:</span><span class="sxs-lookup"><span data-stu-id="6254e-131">The following Razor Pages example creates logs with `Information` as the *level* and `TodoApiSample.Pages.AboutModel` as the *category*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="6254e-132">O exemplo acima cria logs com `Information` e `Warning` como o *nível* e a classe `TodoController` como a *categoria*.</span><span class="sxs-lookup"><span data-stu-id="6254e-132">The preceding example creates logs with `Information` and `Warning` as the *level* and `TodoController` class as the *category*.</span></span> 

::: moniker-end

<span data-ttu-id="6254e-133">O *nível* de log indica a gravidade do evento registrado.</span><span class="sxs-lookup"><span data-stu-id="6254e-133">The Log *level* indicates the severity of the logged event.</span></span> <span data-ttu-id="6254e-134">A *categoria* do log é uma cadeia de caracteres associada a cada log.</span><span class="sxs-lookup"><span data-stu-id="6254e-134">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="6254e-135">A instância `ILogger<T>` cria logs que têm o nome totalmente qualificado do tipo `T` como a categoria.</span><span class="sxs-lookup"><span data-stu-id="6254e-135">The `ILogger<T>` instance creates logs that have the fully qualified name of type `T` as the category.</span></span> <span data-ttu-id="6254e-136">[Níveis](#log-level) e [categorias](#log-category) serão explicados com mais detalhes posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="6254e-136">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="6254e-137">Criar logs na inicialização</span><span class="sxs-lookup"><span data-stu-id="6254e-137">Create logs in Startup</span></span>

<span data-ttu-id="6254e-138">Para gravar logs na classe `Startup`, inclua um parâmetro `ILogger` na assinatura de construtor:</span><span class="sxs-lookup"><span data-stu-id="6254e-138">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-program"></a><span data-ttu-id="6254e-139">Criar logs no programa</span><span class="sxs-lookup"><span data-stu-id="6254e-139">Create logs in Program</span></span>

<span data-ttu-id="6254e-140">Para gravar logs na classe `Program`, obtenha uma instância `ILogger` da DI:</span><span class="sxs-lookup"><span data-stu-id="6254e-140">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="6254e-141">Sem métodos de agente assíncronos</span><span class="sxs-lookup"><span data-stu-id="6254e-141">No asynchronous logger methods</span></span>

<span data-ttu-id="6254e-142">O registro em log deve ser tão rápido que não justifique o custo de desempenho de código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="6254e-142">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="6254e-143">Se o armazenamento de dados em log estiver lento, não grave diretamente nele.</span><span class="sxs-lookup"><span data-stu-id="6254e-143">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="6254e-144">Grave as mensagens de log em um repositório rápido primeiro e, depois, mova-as para um repositório lento.</span><span class="sxs-lookup"><span data-stu-id="6254e-144">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="6254e-145">Por exemplo, se você estiver enviado logs para o SQL Server, convém não fazer isso diretamente em um método `Log`, uma vez que os métodos `Log` são síncronos.</span><span class="sxs-lookup"><span data-stu-id="6254e-145">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="6254e-146">Em vez disso, adicione mensagens de log de forma síncrona a uma fila na memória e faça com que uma função de trabalho de plano de fundo efetue pull das mensagens para fora da fila para fazer o trabalho assíncrono de envio de dados por push para o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6254e-146">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="6254e-147">Configuração</span><span class="sxs-lookup"><span data-stu-id="6254e-147">Configuration</span></span>

<span data-ttu-id="6254e-148">A configuração do provedor de logs é fornecida por um ou mais provedores de sincronização:</span><span class="sxs-lookup"><span data-stu-id="6254e-148">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="6254e-149">Formatos de arquivo (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="6254e-149">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="6254e-150">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="6254e-150">Command-line arguments.</span></span>
* <span data-ttu-id="6254e-151">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="6254e-151">Environment variables.</span></span>
* <span data-ttu-id="6254e-152">Objetos do .NET na memória.</span><span class="sxs-lookup"><span data-stu-id="6254e-152">In-memory .NET objects.</span></span>
* <span data-ttu-id="6254e-153">O armazenamento do [Secret Manager](xref:security/app-secrets) não criptografado.</span><span class="sxs-lookup"><span data-stu-id="6254e-153">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="6254e-154">Um repositório de usuário criptografado, como o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="6254e-154">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="6254e-155">Provedores personalizados (instalados ou criados).</span><span class="sxs-lookup"><span data-stu-id="6254e-155">Custom providers (installed or created).</span></span>

<span data-ttu-id="6254e-156">Por exemplo, a configuração de log geralmente é fornecida pela seção `Logging` dos arquivos de configurações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6254e-156">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="6254e-157">O exemplo a seguir mostra o conteúdo de um típico arquivo *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="6254e-157">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

::: moniker range=">= aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="6254e-158">A propriedade `Logging` pode ter `LogLevel` e propriedades do provedor de logs (o Console é mostrado).</span><span class="sxs-lookup"><span data-stu-id="6254e-158">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="6254e-159">A propriedade `LogLevel` em `Logging` especifica o [nível](#log-level) mínimo para log nas categorias selecionadas.</span><span class="sxs-lookup"><span data-stu-id="6254e-159">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="6254e-160">No exemplo, as categorias `System` e `Microsoft` têm log no nível `Information`, e todas as outras no nível `Debug`.</span><span class="sxs-lookup"><span data-stu-id="6254e-160">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="6254e-161">Outras propriedades em `Logging` especificam provedores de logs.</span><span class="sxs-lookup"><span data-stu-id="6254e-161">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="6254e-162">O exemplo se refere ao provedor de Console.</span><span class="sxs-lookup"><span data-stu-id="6254e-162">The example is for the Console provider.</span></span> <span data-ttu-id="6254e-163">Se um provedor oferecer suporte a [escopos de log](#log-scopes), `IncludeScopes` indicará se eles estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6254e-163">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="6254e-164">Uma propriedade de provedor (como `Console`, no exemplo) também pode especificar uma propriedade `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="6254e-164">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="6254e-165">`LogLevel` em um provedor especifica os níveis de log para esse provedor.</span><span class="sxs-lookup"><span data-stu-id="6254e-165">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="6254e-166">Se os níveis forem especificados em `Logging.{providername}.LogLevel`, eles substituirão o que estiver definido em `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="6254e-166">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

<span data-ttu-id="6254e-167">Chaves `LogLevel` representam nomes de log.</span><span class="sxs-lookup"><span data-stu-id="6254e-167">`LogLevel` keys represent log names.</span></span> <span data-ttu-id="6254e-168">A chave `Default` aplica-se a logs não listados de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="6254e-168">The `Default` key applies to logs not explicitly listed.</span></span> <span data-ttu-id="6254e-169">O valor representa o [nível de log](#log-level) aplicado ao log fornecido.</span><span class="sxs-lookup"><span data-stu-id="6254e-169">The value represents the [log level](#log-level) applied to the given log.</span></span>

::: moniker-end

<span data-ttu-id="6254e-170">Saiba mais sobre como implementar provedores de configuração em <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="6254e-170">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="6254e-171">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="6254e-171">Sample logging output</span></span>

<span data-ttu-id="6254e-172">Com o código de exemplo mostrado na seção anterior, os logs serão exibidos no console quando o aplicativo for executado pela linha de comando.</span><span class="sxs-lookup"><span data-stu-id="6254e-172">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="6254e-173">Aqui está um exemplo da saída do console:</span><span class="sxs-lookup"><span data-stu-id="6254e-173">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="6254e-174">Os logs anteriores foram gerados por meio de uma solicitação HTTP Get para o aplicativo de exemplo em `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="6254e-174">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="6254e-175">Veja um exemplo de como os mesmos logs aparecem na janela Depuração quando você executa o aplicativo de exemplo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="6254e-175">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="6254e-176">Os logs criados pelas chamadas `ILogger` mostradas na seção anterior começam com "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="6254e-176">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="6254e-177">Os logs que começam com categorias "Microsoft" são de código da estrutura ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6254e-177">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="6254e-178">O ASP.NET Core e o código do aplicativo estão usando a mesma API de registro em log e os mesmos provedores.</span><span class="sxs-lookup"><span data-stu-id="6254e-178">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="6254e-179">O restante deste artigo explica alguns detalhes e opções para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6254e-179">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="6254e-180">Pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="6254e-180">NuGet packages</span></span>

<span data-ttu-id="6254e-181">As interfaces `ILogger` e `ILoggerFactory` estão em [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e as implementações padrão para elas estão em [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="6254e-181">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="6254e-182">Categoria de log</span><span class="sxs-lookup"><span data-stu-id="6254e-182">Log category</span></span>

<span data-ttu-id="6254e-183">Quando um objeto `ILogger` é criado, uma *categoria* é especificada para ele.</span><span class="sxs-lookup"><span data-stu-id="6254e-183">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="6254e-184">Essa categoria é incluída em cada mensagem de log criada por essa instância de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="6254e-184">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="6254e-185">A categoria pode ser qualquer cadeia de caracteres, mas a convenção é usar o nome da classe, como "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="6254e-185">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="6254e-186">Use `ILogger<T>` para obter uma instância `ILogger` que usa o nome de tipo totalmente qualificado do `T` como a categoria:</span><span class="sxs-lookup"><span data-stu-id="6254e-186">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="6254e-187">Para especificar explicitamente a categoria, chame `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="6254e-187">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="6254e-188">`ILogger<T>` é equivalente a chamar `CreateLogger` com o nome de tipo totalmente qualificado de `T`.</span><span class="sxs-lookup"><span data-stu-id="6254e-188">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="6254e-189">Nível de log</span><span class="sxs-lookup"><span data-stu-id="6254e-189">Log level</span></span>

<span data-ttu-id="6254e-190">Todo log especifica um valor <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="6254e-190">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="6254e-191">O nível de log indica a gravidade ou importância.</span><span class="sxs-lookup"><span data-stu-id="6254e-191">The log level indicates the severity or importance.</span></span> <span data-ttu-id="6254e-192">Por exemplo, você pode gravar um log `Information` quando um método é finalizado normalmente e um log `Warning` quando um método retorna um código de status *404 Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="6254e-192">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="6254e-193">O código a seguir cria os logs `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="6254e-193">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="6254e-194">No código anterior, o primeiro parâmetro é a [ID de evento de log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="6254e-194">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="6254e-195">O segundo parâmetro é um modelo de mensagem com espaços reservados para valores de argumento fornecidos pelos parâmetros de método restantes.</span><span class="sxs-lookup"><span data-stu-id="6254e-195">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="6254e-196">Os parâmetros de método serão explicados com posteriormente neste artigo, na [seção de modelos de mensagem](#log-message-template).</span><span class="sxs-lookup"><span data-stu-id="6254e-196">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="6254e-197">Os métodos de log que incluem o nível no nome do método (por exemplo, `LogInformation` e `LogWarning`) são [métodos de extensão para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="6254e-197">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="6254e-198">Esses métodos chamam um método `Log` que recebe um parâmetro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="6254e-198">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="6254e-199">Você pode chamar o método `Log` diretamente em vez de um desses métodos de extensão, mas a sintaxe é relativamente complicada.</span><span class="sxs-lookup"><span data-stu-id="6254e-199">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="6254e-200">Para saber mais, veja <xref:Microsoft.Extensions.Logging.ILogger> e o [código-fonte de extensões de agente](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="6254e-200">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="6254e-201">O ASP.NET Core define os seguintes níveis de log, ordenados aqui da menor para a maior gravidade.</span><span class="sxs-lookup"><span data-stu-id="6254e-201">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="6254e-202">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="6254e-202">Trace = 0</span></span>

  <span data-ttu-id="6254e-203">Para obter informações que normalmente são valiosas somente para depuração.</span><span class="sxs-lookup"><span data-stu-id="6254e-203">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="6254e-204">Essas mensagens podem conter dados confidenciais de aplicativos e, portanto, não devem ser habilitadas em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="6254e-204">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="6254e-205">*Desabilitado por padrão.*</span><span class="sxs-lookup"><span data-stu-id="6254e-205">*Disabled by default.*</span></span>

* <span data-ttu-id="6254e-206">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="6254e-206">Debug = 1</span></span>

  <span data-ttu-id="6254e-207">Para obter informações que possam ser úteis durante o desenvolvimento e a depuração.</span><span class="sxs-lookup"><span data-stu-id="6254e-207">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="6254e-208">Exemplo: `Entering method Configure with flag set to true.` habilite logs de nível `Debug` em produção somente ao solucionar problemas, devido ao alto volume de logs.</span><span class="sxs-lookup"><span data-stu-id="6254e-208">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="6254e-209">Information = 2</span><span class="sxs-lookup"><span data-stu-id="6254e-209">Information = 2</span></span>

  <span data-ttu-id="6254e-210">Para rastrear o fluxo geral do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6254e-210">For tracking the general flow of the app.</span></span> <span data-ttu-id="6254e-211">Esses logs normalmente têm algum valor a longo prazo.</span><span class="sxs-lookup"><span data-stu-id="6254e-211">These logs typically have some long-term value.</span></span> <span data-ttu-id="6254e-212">Exemplo: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="6254e-212">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="6254e-213">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="6254e-213">Warning = 3</span></span>

  <span data-ttu-id="6254e-214">Para eventos anormais ou inesperados no fluxo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6254e-214">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="6254e-215">Eles podem incluir erros ou outras condições que não fazem com que o aplicativo pare, mas que talvez precisem ser investigados.</span><span class="sxs-lookup"><span data-stu-id="6254e-215">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="6254e-216">Exceções manipuladas são um local comum para usar o nível de log `Warning`.</span><span class="sxs-lookup"><span data-stu-id="6254e-216">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="6254e-217">Exemplo: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="6254e-217">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="6254e-218">Error = 4</span><span class="sxs-lookup"><span data-stu-id="6254e-218">Error = 4</span></span>

  <span data-ttu-id="6254e-219">Para erros e exceções que não podem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="6254e-219">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="6254e-220">Essas mensagens indicam uma falha na atividade ou na operação atual (como a solicitação HTTP atual) e não uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6254e-220">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="6254e-221">Mensagem de log de exemplo:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="6254e-221">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="6254e-222">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="6254e-222">Critical = 5</span></span>

  <span data-ttu-id="6254e-223">Para falhas que exigem atenção imediata.</span><span class="sxs-lookup"><span data-stu-id="6254e-223">For failures that require immediate attention.</span></span> <span data-ttu-id="6254e-224">Exemplos: cenários de perda de dados, espaço em disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="6254e-224">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="6254e-225">Use o nível de log para controlar a quantidade de saída de log que é gravada em uma mídia de armazenamento específica ou em uma janela de exibição.</span><span class="sxs-lookup"><span data-stu-id="6254e-225">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="6254e-226">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="6254e-226">For example:</span></span>

* <span data-ttu-id="6254e-227">Em produção, envie `Trace` por meio do nível `Information` para um armazenamento de dados com volume.</span><span class="sxs-lookup"><span data-stu-id="6254e-227">In production, send `Trace` through `Information` level to a volume data store.</span></span> <span data-ttu-id="6254e-228">Envie `Warning` por meio de `Critical` para um armazenamento de dados com valor.</span><span class="sxs-lookup"><span data-stu-id="6254e-228">Send `Warning` through `Critical` to a value data store.</span></span>
* <span data-ttu-id="6254e-229">Durante o desenvolvimento, envie `Warning` por meio `Critical` para o console e adicione `Trace` por meio de `Information` ao solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="6254e-229">During development, send `Warning` through `Critical` to the console, and add `Trace` through `Information` when troubleshooting.</span></span>

<span data-ttu-id="6254e-230">A seção [Filtragem de log](#log-filtering) mais adiante neste artigo explicará como controlar os níveis de log que um provedor manipula.</span><span class="sxs-lookup"><span data-stu-id="6254e-230">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="6254e-231">O ASP.NET Core grava logs para eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="6254e-231">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="6254e-232">Os exemplos de log anteriores neste artigo excluíram logs abaixo do nível `Information`, portanto, logs de nível `Debug` ou `Trace` não foram criados.</span><span class="sxs-lookup"><span data-stu-id="6254e-232">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="6254e-233">Veja um exemplo de logs de console produzidos por meio da execução do aplicativo de exemplo configurado para mostrar logs `Debug`:</span><span class="sxs-lookup"><span data-stu-id="6254e-233">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="6254e-234">ID de evento de log</span><span class="sxs-lookup"><span data-stu-id="6254e-234">Log event ID</span></span>

<span data-ttu-id="6254e-235">Cada log pode especificar uma *ID do evento*.</span><span class="sxs-lookup"><span data-stu-id="6254e-235">Each log can specify an *event ID*.</span></span> <span data-ttu-id="6254e-236">O aplicativo de exemplo faz isso usando uma classe `LoggingEvents` definida localmente:</span><span class="sxs-lookup"><span data-stu-id="6254e-236">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="6254e-237">Uma ID de evento associa um conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="6254e-237">An event ID associates a set of events.</span></span> <span data-ttu-id="6254e-238">Por exemplo, todos os logs relacionados à exibição de uma lista de itens em uma página podem ser 1001.</span><span class="sxs-lookup"><span data-stu-id="6254e-238">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="6254e-239">O provedor de logs pode armazenar a ID do evento em um campo de ID na mensagem de log ou não armazenar.</span><span class="sxs-lookup"><span data-stu-id="6254e-239">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="6254e-240">O provedor de Depuração não mostra IDs de eventos.</span><span class="sxs-lookup"><span data-stu-id="6254e-240">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="6254e-241">O provedor de console mostra IDs de evento entre colchetes após a categoria:</span><span class="sxs-lookup"><span data-stu-id="6254e-241">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="6254e-242">Modelo de mensagem de log</span><span class="sxs-lookup"><span data-stu-id="6254e-242">Log message template</span></span>

<span data-ttu-id="6254e-243">Cada log especifica um modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="6254e-243">Each log specifies a message template.</span></span> <span data-ttu-id="6254e-244">O modelo de mensagem pode conter espaços reservados para os quais são fornecidos argumentos.</span><span class="sxs-lookup"><span data-stu-id="6254e-244">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="6254e-245">Use nomes para os espaços reservados, não números.</span><span class="sxs-lookup"><span data-stu-id="6254e-245">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="6254e-246">A ordem dos espaços reservados e não de seus nomes, determina quais parâmetros serão usados para fornecer seus valores.</span><span class="sxs-lookup"><span data-stu-id="6254e-246">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="6254e-247">No código a seguir, observe que os nomes de parâmetro estão fora de sequência no modelo de mensagem:</span><span class="sxs-lookup"><span data-stu-id="6254e-247">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="6254e-248">Esse código cria uma mensagem de log com os valores de parâmetro na sequência:</span><span class="sxs-lookup"><span data-stu-id="6254e-248">This code creates a log message with the parameter values in sequence:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="6254e-249">A estrutura de registros funciona dessa maneira para que os provedores de logs possam implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="6254e-249">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="6254e-250">Os próprios argumentos são passados para o sistema de registro em log, não apenas o modelo de mensagem formatado.</span><span class="sxs-lookup"><span data-stu-id="6254e-250">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="6254e-251">Essas informações permitem que os provedores de log armazenem os valores de parâmetro como campos.</span><span class="sxs-lookup"><span data-stu-id="6254e-251">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="6254e-252">Por exemplo, suponha que as chamadas de método do agente sejam assim:</span><span class="sxs-lookup"><span data-stu-id="6254e-252">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="6254e-253">Se você estiver enviando os logs para o Armazenamento de Tabelas do Azure, cada entidade da Tabela do Azure poderá ter propriedades `ID` e `RequestTime`, o que simplificará as consultas nos dados de log.</span><span class="sxs-lookup"><span data-stu-id="6254e-253">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="6254e-254">Uma consulta pode encontrar todos os logs em determinado intervalo de `RequestTime` sem analisar o tempo limite da mensagem de texto.</span><span class="sxs-lookup"><span data-stu-id="6254e-254">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="6254e-255">Exceções de registro em log</span><span class="sxs-lookup"><span data-stu-id="6254e-255">Logging exceptions</span></span>

<span data-ttu-id="6254e-256">Os métodos de agente têm sobrecargas que permitem que você passe uma exceção, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="6254e-256">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="6254e-257">Provedores diferentes manipulam as informações de exceção de maneiras diferentes.</span><span class="sxs-lookup"><span data-stu-id="6254e-257">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="6254e-258">Aqui está um exemplo da saída do provedor Depuração do código mostrado acima.</span><span class="sxs-lookup"><span data-stu-id="6254e-258">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="6254e-259">Filtragem de log</span><span class="sxs-lookup"><span data-stu-id="6254e-259">Log filtering</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="6254e-260">Você pode especificar um nível de log mínimo para um provedor e uma categoria específicos ou para todos os provedores ou todas as categorias.</span><span class="sxs-lookup"><span data-stu-id="6254e-260">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="6254e-261">Os logs abaixo do nível mínimo não serão passados para esse provedor, para que não sejam exibidos ou armazenados.</span><span class="sxs-lookup"><span data-stu-id="6254e-261">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="6254e-262">Para suprimir todos os logs, especifique `LogLevel.None` como o nível de log mínimo.</span><span class="sxs-lookup"><span data-stu-id="6254e-262">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="6254e-263">O valor inteiro de `LogLevel.None` é 6, que é maior do que `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="6254e-263">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="6254e-264">Criar regras de filtro na configuração</span><span class="sxs-lookup"><span data-stu-id="6254e-264">Create filter rules in configuration</span></span>

<span data-ttu-id="6254e-265">O código do modelo de projeto chama `CreateDefaultBuilder` para configurar o registro em log para os provedores Console e Depuração.</span><span class="sxs-lookup"><span data-stu-id="6254e-265">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="6254e-266">O método `CreateDefaultBuilder` também configura o registro em log para procurar a configuração em uma seção `Logging`, usando código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="6254e-266">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17)]

<span data-ttu-id="6254e-267">Os dados de configuração especificam níveis de log mínimo por provedor e por categoria, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="6254e-267">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="6254e-268">Este JSON cria seis regras de filtro, uma para o provedor Depuração, quatro para o provedor Console e uma para todos os provedores.</span><span class="sxs-lookup"><span data-stu-id="6254e-268">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="6254e-269">Apenas uma regra é escolhida para cada provedor quando um objeto `ILogger` é criado.</span><span class="sxs-lookup"><span data-stu-id="6254e-269">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="6254e-270">Regras de filtro no código</span><span class="sxs-lookup"><span data-stu-id="6254e-270">Filter rules in code</span></span>

<span data-ttu-id="6254e-271">O exemplo a seguir mostra como registrar regras de filtro no código:</span><span class="sxs-lookup"><span data-stu-id="6254e-271">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="6254e-272">O segundo `AddFilter` especifica o provedor Depuração usando seu nome de tipo.</span><span class="sxs-lookup"><span data-stu-id="6254e-272">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="6254e-273">O primeiro `AddFilter` se aplica a todos os provedores porque ele não especifica um tipo de provedor.</span><span class="sxs-lookup"><span data-stu-id="6254e-273">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="6254e-274">Como as regras de filtragem são aplicadas</span><span class="sxs-lookup"><span data-stu-id="6254e-274">How filtering rules are applied</span></span>

<span data-ttu-id="6254e-275">Os dados de configuração e o código `AddFilter`, mostrados nos exemplos anteriores, criam as regras mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="6254e-275">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="6254e-276">As primeiras seis vêm do exemplo de configuração e as últimas duas vêm do exemplo de código.</span><span class="sxs-lookup"><span data-stu-id="6254e-276">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="6254e-277">Número</span><span class="sxs-lookup"><span data-stu-id="6254e-277">Number</span></span> | <span data-ttu-id="6254e-278">Provider</span><span class="sxs-lookup"><span data-stu-id="6254e-278">Provider</span></span>      | <span data-ttu-id="6254e-279">Categorias que começam com...</span><span class="sxs-lookup"><span data-stu-id="6254e-279">Categories that begin with ...</span></span>          | <span data-ttu-id="6254e-280">Nível de log mínimo</span><span class="sxs-lookup"><span data-stu-id="6254e-280">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="6254e-281">1</span><span class="sxs-lookup"><span data-stu-id="6254e-281">1</span></span>      | <span data-ttu-id="6254e-282">Depurar</span><span class="sxs-lookup"><span data-stu-id="6254e-282">Debug</span></span>         | <span data-ttu-id="6254e-283">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="6254e-283">All categories</span></span>                          | <span data-ttu-id="6254e-284">Informações</span><span class="sxs-lookup"><span data-stu-id="6254e-284">Information</span></span>       |
| <span data-ttu-id="6254e-285">2</span><span class="sxs-lookup"><span data-stu-id="6254e-285">2</span></span>      | <span data-ttu-id="6254e-286">Console</span><span class="sxs-lookup"><span data-stu-id="6254e-286">Console</span></span>       | <span data-ttu-id="6254e-287">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="6254e-287">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="6254e-288">Aviso</span><span class="sxs-lookup"><span data-stu-id="6254e-288">Warning</span></span>           |
| <span data-ttu-id="6254e-289">3</span><span class="sxs-lookup"><span data-stu-id="6254e-289">3</span></span>      | <span data-ttu-id="6254e-290">Console</span><span class="sxs-lookup"><span data-stu-id="6254e-290">Console</span></span>       | <span data-ttu-id="6254e-291">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="6254e-291">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="6254e-292">Depurar</span><span class="sxs-lookup"><span data-stu-id="6254e-292">Debug</span></span>             |
| <span data-ttu-id="6254e-293">4</span><span class="sxs-lookup"><span data-stu-id="6254e-293">4</span></span>      | <span data-ttu-id="6254e-294">Console</span><span class="sxs-lookup"><span data-stu-id="6254e-294">Console</span></span>       | <span data-ttu-id="6254e-295">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="6254e-295">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="6254e-296">Erro</span><span class="sxs-lookup"><span data-stu-id="6254e-296">Error</span></span>             |
| <span data-ttu-id="6254e-297">5</span><span class="sxs-lookup"><span data-stu-id="6254e-297">5</span></span>      | <span data-ttu-id="6254e-298">Console</span><span class="sxs-lookup"><span data-stu-id="6254e-298">Console</span></span>       | <span data-ttu-id="6254e-299">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="6254e-299">All categories</span></span>                          | <span data-ttu-id="6254e-300">Informações</span><span class="sxs-lookup"><span data-stu-id="6254e-300">Information</span></span>       |
| <span data-ttu-id="6254e-301">6</span><span class="sxs-lookup"><span data-stu-id="6254e-301">6</span></span>      | <span data-ttu-id="6254e-302">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="6254e-302">All providers</span></span> | <span data-ttu-id="6254e-303">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="6254e-303">All categories</span></span>                          | <span data-ttu-id="6254e-304">Depurar</span><span class="sxs-lookup"><span data-stu-id="6254e-304">Debug</span></span>             |
| <span data-ttu-id="6254e-305">7</span><span class="sxs-lookup"><span data-stu-id="6254e-305">7</span></span>      | <span data-ttu-id="6254e-306">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="6254e-306">All providers</span></span> | <span data-ttu-id="6254e-307">Sistema</span><span class="sxs-lookup"><span data-stu-id="6254e-307">System</span></span>                                  | <span data-ttu-id="6254e-308">Depurar</span><span class="sxs-lookup"><span data-stu-id="6254e-308">Debug</span></span>             |
| <span data-ttu-id="6254e-309">8</span><span class="sxs-lookup"><span data-stu-id="6254e-309">8</span></span>      | <span data-ttu-id="6254e-310">Depurar</span><span class="sxs-lookup"><span data-stu-id="6254e-310">Debug</span></span>         | <span data-ttu-id="6254e-311">Microsoft</span><span class="sxs-lookup"><span data-stu-id="6254e-311">Microsoft</span></span>                               | <span data-ttu-id="6254e-312">Rastrear</span><span class="sxs-lookup"><span data-stu-id="6254e-312">Trace</span></span>             |

<span data-ttu-id="6254e-313">Quando um objeto `ILogger` é criado, o objeto `ILoggerFactory` seleciona uma única regra por provedor para aplicar a esse agente.</span><span class="sxs-lookup"><span data-stu-id="6254e-313">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="6254e-314">Todas as mensagens gravadas pela instância `ILogger` são filtradas com base nas regras selecionadas.</span><span class="sxs-lookup"><span data-stu-id="6254e-314">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="6254e-315">A regra mais específica possível para cada par de categoria e provedor é selecionada dentre as regras disponíveis.</span><span class="sxs-lookup"><span data-stu-id="6254e-315">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="6254e-316">O algoritmo a seguir é usado para cada provedor quando um `ILogger` é criado para uma determinada categoria:</span><span class="sxs-lookup"><span data-stu-id="6254e-316">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="6254e-317">Selecione todas as regras que correspondem ao provedor ou seu alias.</span><span class="sxs-lookup"><span data-stu-id="6254e-317">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="6254e-318">Se nenhuma correspondência for encontrada, selecione todas as regras com um provedor vazio.</span><span class="sxs-lookup"><span data-stu-id="6254e-318">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="6254e-319">Do resultado da etapa anterior, selecione as regras com o prefixo de categoria de maior correspondência.</span><span class="sxs-lookup"><span data-stu-id="6254e-319">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="6254e-320">Se nenhuma correspondência for encontrada, selecione todas as regras que não especificam uma categoria.</span><span class="sxs-lookup"><span data-stu-id="6254e-320">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="6254e-321">Se várias regras forem selecionadas, use a **última**.</span><span class="sxs-lookup"><span data-stu-id="6254e-321">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="6254e-322">Se nenhuma regra for selecionada, use `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="6254e-322">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="6254e-323">Com a lista anterior de regras, suponha que você crie um objeto `ILogger` para a categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="6254e-323">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="6254e-324">Para o provedor Depuração as regras 1, 6 e 8 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="6254e-324">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="6254e-325">A regra 8 é mais específica, portanto é a que será selecionada.</span><span class="sxs-lookup"><span data-stu-id="6254e-325">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="6254e-326">Para o provedor Console as regras 3, 4, 5 e 6 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="6254e-326">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="6254e-327">A regra 3 é a mais específica.</span><span class="sxs-lookup"><span data-stu-id="6254e-327">Rule 3 is most specific.</span></span>

<span data-ttu-id="6254e-328">A instância `ILogger` resultante envia logs de nível `Trace` e superior para o provedor Depuração.</span><span class="sxs-lookup"><span data-stu-id="6254e-328">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="6254e-329">Logs de nível `Debug` e superior são enviados para o provedor Console.</span><span class="sxs-lookup"><span data-stu-id="6254e-329">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="6254e-330">Aliases de provedor</span><span class="sxs-lookup"><span data-stu-id="6254e-330">Provider aliases</span></span>

<span data-ttu-id="6254e-331">Cada provedor define um *alias* que pode ser usado na configuração no lugar do nome de tipo totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="6254e-331">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="6254e-332">Para os provedores internos, use os seguintes aliases:</span><span class="sxs-lookup"><span data-stu-id="6254e-332">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="6254e-333">Console</span><span class="sxs-lookup"><span data-stu-id="6254e-333">Console</span></span>
* <span data-ttu-id="6254e-334">Depurar</span><span class="sxs-lookup"><span data-stu-id="6254e-334">Debug</span></span>
* <span data-ttu-id="6254e-335">EventSource</span><span class="sxs-lookup"><span data-stu-id="6254e-335">EventSource</span></span>
* <span data-ttu-id="6254e-336">EventLog</span><span class="sxs-lookup"><span data-stu-id="6254e-336">EventLog</span></span>
* <span data-ttu-id="6254e-337">TraceSource</span><span class="sxs-lookup"><span data-stu-id="6254e-337">TraceSource</span></span>
* <span data-ttu-id="6254e-338">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="6254e-338">AzureAppServicesFile</span></span>
* <span data-ttu-id="6254e-339">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="6254e-339">AzureAppServicesBlob</span></span>
* <span data-ttu-id="6254e-340">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="6254e-340">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="6254e-341">Nível mínimo padrão</span><span class="sxs-lookup"><span data-stu-id="6254e-341">Default minimum level</span></span>

<span data-ttu-id="6254e-342">Há uma configuração de nível mínimo que entra em vigor somente se nenhuma regra de código ou de configuração se aplicar a um provedor e uma categoria determinados.</span><span class="sxs-lookup"><span data-stu-id="6254e-342">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="6254e-343">O exemplo a seguir mostra como definir o nível mínimo:</span><span class="sxs-lookup"><span data-stu-id="6254e-343">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="6254e-344">Se você não definir explicitamente o nível mínimo, o valor padrão será `Information`, o que significa que logs `Trace` e `Debug` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="6254e-344">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="6254e-345">Funções de filtro</span><span class="sxs-lookup"><span data-stu-id="6254e-345">Filter functions</span></span>

<span data-ttu-id="6254e-346">Uma função de filtro é invocada para todos os provedores e categorias que não têm regras atribuídas a eles por configuração ou código.</span><span class="sxs-lookup"><span data-stu-id="6254e-346">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="6254e-347">O código na função tem acesso ao tipo de provedor, à categoria e ao nível de log.</span><span class="sxs-lookup"><span data-stu-id="6254e-347">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="6254e-348">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="6254e-348">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="6254e-349">Alguns provedores de log permitem especificar quando os logs devem ser gravados em uma mídia de armazenamento ou ignorados, com base no nível de log e na categoria.</span><span class="sxs-lookup"><span data-stu-id="6254e-349">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="6254e-350">Os métodos de extensão `AddConsole` e `AddDebug` fornecem sobrecargas que aceitam critérios de filtragem.</span><span class="sxs-lookup"><span data-stu-id="6254e-350">The `AddConsole` and `AddDebug` extension methods provide overloads that accept filtering criteria.</span></span> <span data-ttu-id="6254e-351">O código de exemplo a seguir faz com que o provedor de console ignore os logs abaixo do nível `Warning`, enquanto o provedor Depuração ignora os logs criados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="6254e-351">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="6254e-352">O método `AddEventLog` tem uma sobrecarga que recebe uma instância `EventLogSettings`, que pode conter uma função de filtragem em sua propriedade `Filter`.</span><span class="sxs-lookup"><span data-stu-id="6254e-352">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="6254e-353">O provedor TraceSource não fornece nenhuma dessas sobrecargas, porque seu nível de registro em log e outros parâmetros são baseados no `SourceSwitch` e no `TraceListener` que ele usa.</span><span class="sxs-lookup"><span data-stu-id="6254e-353">The TraceSource provider doesn't provide any of those overloads, since its logging level and other parameters are based on the `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="6254e-354">Para definir regras de filtragem para todos os provedores registrados com uma instância `ILoggerFactory`, use o método de extensão `WithFilter`.</span><span class="sxs-lookup"><span data-stu-id="6254e-354">To set filtering rules for all providers that are registered with an `ILoggerFactory` instance, use the `WithFilter` extension method.</span></span> <span data-ttu-id="6254e-355">O exemplo abaixo limita os logs de estrutura (categoria começa com "Microsoft" ou "Sistema") a avisos, enquanto registra em nível de depuração os logs criados por código de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6254e-355">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while logging at debug level for logs created by application code.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="6254e-356">Para impedir a gravação de logs, especifique `LogLevel.None` como o nível de log mínimo.</span><span class="sxs-lookup"><span data-stu-id="6254e-356">To prevent any logs from being written, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="6254e-357">O valor inteiro de `LogLevel.None` é 6, que é maior do que `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="6254e-357">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="6254e-358">O método de extensão `WithFilter` é fornecido pelo pacote NuGet [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter).</span><span class="sxs-lookup"><span data-stu-id="6254e-358">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="6254e-359">O método retorna um nova instância `ILoggerFactory`, que filtrará as mensagens de log passadas para todos os provedores de agente registrados com ela.</span><span class="sxs-lookup"><span data-stu-id="6254e-359">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="6254e-360">Isso não afeta nenhuma outra instância de `ILoggerFactory`, incluindo a instância de `ILoggerFactory` original.</span><span class="sxs-lookup"><span data-stu-id="6254e-360">It doesn't affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="6254e-361">Categorias e níveis de sistema</span><span class="sxs-lookup"><span data-stu-id="6254e-361">System categories and levels</span></span>

<span data-ttu-id="6254e-362">Veja algumas categorias usadas pelo ASP.NET Core e Entity Framework Core, com anotações sobre quais logs esperar delas:</span><span class="sxs-lookup"><span data-stu-id="6254e-362">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="6254e-363">Categoria</span><span class="sxs-lookup"><span data-stu-id="6254e-363">Category</span></span>                            | <span data-ttu-id="6254e-364">Observações</span><span class="sxs-lookup"><span data-stu-id="6254e-364">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="6254e-365">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="6254e-365">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="6254e-366">Diagnóstico geral de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6254e-366">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="6254e-367">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="6254e-367">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="6254e-368">Quais chaves foram consideradas, encontradas e usadas.</span><span class="sxs-lookup"><span data-stu-id="6254e-368">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="6254e-369">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="6254e-369">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="6254e-370">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="6254e-370">Hosts allowed.</span></span> |
| <span data-ttu-id="6254e-371">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="6254e-371">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="6254e-372">Quanto tempo levou para que as solicitações de HTTP fossem concluídas e em que horário foram iniciadas.</span><span class="sxs-lookup"><span data-stu-id="6254e-372">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="6254e-373">Quais assemblies de inicialização de hospedagem foram carregados.</span><span class="sxs-lookup"><span data-stu-id="6254e-373">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="6254e-374">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="6254e-374">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="6254e-375">Diagnóstico do MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="6254e-375">MVC and Razor diagnostics.</span></span> <span data-ttu-id="6254e-376">Model binding, execução de filtro, compilação de exibição, seleção de ação.</span><span class="sxs-lookup"><span data-stu-id="6254e-376">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="6254e-377">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="6254e-377">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="6254e-378">Informações de correspondência de rotas.</span><span class="sxs-lookup"><span data-stu-id="6254e-378">Route matching information.</span></span> |
| <span data-ttu-id="6254e-379">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="6254e-379">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="6254e-380">Respostas de início, parada e atividade da conexão.</span><span class="sxs-lookup"><span data-stu-id="6254e-380">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="6254e-381">Informações sobre o certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6254e-381">HTTPS certificate information.</span></span> |
| <span data-ttu-id="6254e-382">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="6254e-382">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="6254e-383">Arquivos atendidos.</span><span class="sxs-lookup"><span data-stu-id="6254e-383">Files served.</span></span> |
| <span data-ttu-id="6254e-384">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="6254e-384">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="6254e-385">Diagnóstico geral do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6254e-385">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="6254e-386">Atividade e configuração do banco de dados, detecção de alterações, migrações.</span><span class="sxs-lookup"><span data-stu-id="6254e-386">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="6254e-387">Escopos de log</span><span class="sxs-lookup"><span data-stu-id="6254e-387">Log scopes</span></span>

 <span data-ttu-id="6254e-388">Um *escopo* pode agrupar um conjunto de operações lógicas.</span><span class="sxs-lookup"><span data-stu-id="6254e-388">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="6254e-389">Esse agrupamento pode ser usado para anexar os mesmos dados para cada log criado como parte de um conjunto.</span><span class="sxs-lookup"><span data-stu-id="6254e-389">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="6254e-390">Por exemplo, todo log criado como parte do processamento de uma transação pode incluir a ID da transação.</span><span class="sxs-lookup"><span data-stu-id="6254e-390">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="6254e-391">Um escopo é um tipo `IDisposable` retornado pelo método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e que dura até que seja descartado.</span><span class="sxs-lookup"><span data-stu-id="6254e-391">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="6254e-392">Use um escopo por meio do encapsulamento de chamadas de agente em um bloco `using`:</span><span class="sxs-lookup"><span data-stu-id="6254e-392">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="6254e-393">O código a seguir habilita os escopos para o provedor de console:</span><span class="sxs-lookup"><span data-stu-id="6254e-393">The following code enables scopes for the console provider:</span></span>

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="6254e-394">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6254e-394">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="6254e-395">A configuração da opção de agente de console `IncludeScopes` é necessária para habilitar o registro em log baseado em escopo.</span><span class="sxs-lookup"><span data-stu-id="6254e-395">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="6254e-396">Saiba mais sobre como configurar na seção [Configuração](#configuration).</span><span class="sxs-lookup"><span data-stu-id="6254e-396">For information on configuration, see the [Configuration](#configuration) section.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="6254e-397">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6254e-397">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="6254e-398">A configuração da opção de agente de console `IncludeScopes` é necessária para habilitar o registro em log baseado em escopo.</span><span class="sxs-lookup"><span data-stu-id="6254e-398">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="6254e-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6254e-399">*Startup.cs*:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

<span data-ttu-id="6254e-400">Cada mensagem de log inclui as informações com escopo definido:</span><span class="sxs-lookup"><span data-stu-id="6254e-400">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="6254e-401">Provedores de log internos</span><span class="sxs-lookup"><span data-stu-id="6254e-401">Built-in logging providers</span></span>

<span data-ttu-id="6254e-402">O ASP.NET Core vem com os seguintes provedores:</span><span class="sxs-lookup"><span data-stu-id="6254e-402">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="6254e-403">Console</span><span class="sxs-lookup"><span data-stu-id="6254e-403">Console</span></span>](#console-provider)
* [<span data-ttu-id="6254e-404">Depurar</span><span class="sxs-lookup"><span data-stu-id="6254e-404">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="6254e-405">EventSource</span><span class="sxs-lookup"><span data-stu-id="6254e-405">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="6254e-406">EventLog</span><span class="sxs-lookup"><span data-stu-id="6254e-406">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="6254e-407">TraceSource</span><span class="sxs-lookup"><span data-stu-id="6254e-407">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="6254e-408">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="6254e-408">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="6254e-409">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="6254e-409">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="6254e-410">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="6254e-410">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="6254e-411">Para saber mais sobre log de stdout, confira <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> e <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span><span class="sxs-lookup"><span data-stu-id="6254e-411">For information about stdout logging, see <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> and <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="6254e-412">Provedor do console</span><span class="sxs-lookup"><span data-stu-id="6254e-412">Console provider</span></span>

<span data-ttu-id="6254e-413">O pacote de provedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envia a saída de log para o console.</span><span class="sxs-lookup"><span data-stu-id="6254e-413">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

<span data-ttu-id="6254e-414">As [sobrecargas do AddConsole](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) permitem que você passe um nível de log mínimo, uma função de filtro e um valor booliano que indica se escopos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="6254e-414">[AddConsole overloads](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) let you pass in a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span> <span data-ttu-id="6254e-415">Outra opção é passar um objeto `IConfiguration`, que pode especificar suporte de escopos e níveis de log.</span><span class="sxs-lookup"><span data-stu-id="6254e-415">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span>

<span data-ttu-id="6254e-416">O provedor de console tem um impacto significativo no desempenho e geralmente não é adequado para uso em produção.</span><span class="sxs-lookup"><span data-stu-id="6254e-416">The console provider has a significant impact on performance and is generally not appropriate for use in production.</span></span>

<span data-ttu-id="6254e-417">Quando você cria um novo projeto no Visual Studio, o método `AddConsole` tem essa aparência:</span><span class="sxs-lookup"><span data-stu-id="6254e-417">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="6254e-418">Esse código se refere à seção `Logging` do arquivo *appSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6254e-418">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

<span data-ttu-id="6254e-419">As configurações mostradas limitam os logs de estrutura a avisos, permitindo que o aplicativo para faça registros no nível de depuração, conforme explicado na [Filtragem de log](#log-filtering) seção.</span><span class="sxs-lookup"><span data-stu-id="6254e-419">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="6254e-420">Para obter mais informações, consulte [Configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6254e-420">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

::: moniker-end

<span data-ttu-id="6254e-421">Para ver a saída de registro em log de console, abra um prompt de comando na pasta do projeto e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6254e-421">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```console
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="6254e-422">Depurar provedor</span><span class="sxs-lookup"><span data-stu-id="6254e-422">Debug provider</span></span>

<span data-ttu-id="6254e-423">O pacote de provedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) grava a saída de log usando a classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chamadas de método `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="6254e-423">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="6254e-424">No Linux, esse provedor grava logs em */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="6254e-424">On Linux, this provider writes logs to */var/log/message*.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

<span data-ttu-id="6254e-425">As [sobrecargas de AddDebug](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) permitem que você passe um nível de log mínimo ou uma função de filtro.</span><span class="sxs-lookup"><span data-stu-id="6254e-425">[AddDebug overloads](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) let you pass in a minimum log level or a filter function.</span></span>

::: moniker-end

### <a name="eventsource-provider"></a><span data-ttu-id="6254e-426">Provedor EventSource</span><span class="sxs-lookup"><span data-stu-id="6254e-426">EventSource provider</span></span>

<span data-ttu-id="6254e-427">Para aplicativos que se destinam ao ASP.NET Core 1.1.0 ou posterior, o pacote de provedor [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) pode implementar o rastreamento de eventos.</span><span class="sxs-lookup"><span data-stu-id="6254e-427">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="6254e-428">No Windows, ele usa [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="6254e-428">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="6254e-429">O provedor é multiplataforma, mas ainda não há ferramentas de coleta e exibição de eventos para Linux ou macOS.</span><span class="sxs-lookup"><span data-stu-id="6254e-429">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventSourceLogger();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventSourceLogger();
```

::: moniker-end

<span data-ttu-id="6254e-430">Uma boa maneira de coletar e exibir logs é usar o [utilitário PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="6254e-430">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="6254e-431">Há outras ferramentas para exibir os logs do ETW, mas o PerfView proporciona a melhor experiência para trabalhar com os eventos de ETW emitidos pelo ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="6254e-431">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span>

<span data-ttu-id="6254e-432">Para configurar o PerfView para coletar eventos registrados por esse provedor, adicione a cadeia de caracteres `*Microsoft-Extensions-Logging` à lista **Provedores Adicionais**.</span><span class="sxs-lookup"><span data-stu-id="6254e-432">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="6254e-433">(Não se esqueça do asterisco no início da cadeia de caracteres).</span><span class="sxs-lookup"><span data-stu-id="6254e-433">(Don't miss the asterisk at the start of the string.)</span></span>

![Outros provedores de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="6254e-435">Provedor EventLog do Windows</span><span class="sxs-lookup"><span data-stu-id="6254e-435">Windows EventLog provider</span></span>

<span data-ttu-id="6254e-436">O pacote de provedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envia a saída de log para o Log de Eventos do Windows.</span><span class="sxs-lookup"><span data-stu-id="6254e-436">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

<span data-ttu-id="6254e-437">As [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permitem que você passe `EventLogSettings` ou um nível de log mínimo.</span><span class="sxs-lookup"><span data-stu-id="6254e-437">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

::: moniker-end

### <a name="tracesource-provider"></a><span data-ttu-id="6254e-438">Provedor TraceSource</span><span class="sxs-lookup"><span data-stu-id="6254e-438">TraceSource provider</span></span>

<span data-ttu-id="6254e-439">O pacote de provedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa as bibliotecas e provedores de <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="6254e-439">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddTraceSource(sourceSwitchName);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

::: moniker-end

<span data-ttu-id="6254e-440">As [sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permitem que você passe um comutador de fonte e um ouvinte de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="6254e-440">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="6254e-441">Para usar esse provedor, o aplicativo deve ser executado no .NET Framework (em vez do .NET Core).</span><span class="sxs-lookup"><span data-stu-id="6254e-441">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="6254e-442">O provedor pode rotear mensagens a uma variedade de [ouvintes](/dotnet/framework/debug-trace-profile/trace-listeners), como o <xref:System.Diagnostics.TextWriterTraceListener> usado no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="6254e-442">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="6254e-443">O exemplo a seguir configura um provedor `TraceSource` que registra mensagens `Warning` e superiores na janela de console.</span><span class="sxs-lookup"><span data-stu-id="6254e-443">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

### <a name="azure-app-service-provider"></a><span data-ttu-id="6254e-444">Provedor do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="6254e-444">Azure App Service provider</span></span>

<span data-ttu-id="6254e-445">O pacote de provedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) grava logs em arquivos de texto no sistema de arquivos de um aplicativo do Serviço de Aplicativo do Azure e no [armazenamento de blobs](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) em uma conta de Armazenamento do Azure.</span><span class="sxs-lookup"><span data-stu-id="6254e-445">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="6254e-446">O pacote de provedor está disponível para aplicativos destinados ao .NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="6254e-446">The provider package is available for apps targeting .NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="6254e-447">Se você estiver direcionando para o .NET Core, observe os seguintes pontos:</span><span class="sxs-lookup"><span data-stu-id="6254e-447">If targeting .NET Core, note the following points:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="6254e-448">O pacote de provedor está incluído no [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage) do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6254e-448">The provider package is included in the ASP.NET Core [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="6254e-449">O pacote de provedor não está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6254e-449">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="6254e-450">Para usar o provedor, instale o pacote.</span><span class="sxs-lookup"><span data-stu-id="6254e-450">To use the provider, install the package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="6254e-451">Se você estiver direcionando para o .NET Framework ou referenciando o metapacote `Microsoft.AspNetCore.App`, adicione o pacote do provedor ao projeto.</span><span class="sxs-lookup"><span data-stu-id="6254e-451">If targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> <span data-ttu-id="6254e-452">Chame `AddAzureWebAppDiagnostics`:</span><span class="sxs-lookup"><span data-stu-id="6254e-452">Invoke `AddAzureWebAppDiagnostics`:</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="6254e-453">Uma sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite passar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="6254e-453">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="6254e-454">O objeto settings pode substituir as configurações padrão, como o modelo de saída de registro em log, o nome do blob e o limite do tamanho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="6254e-454">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="6254e-455">(O *modelo Output* é um modelo de mensagem aplicado a todos os logs, além daquele fornecido com uma chamada ao método `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="6254e-455">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6254e-456">Para definir as configurações do provedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="6254e-456">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

<span data-ttu-id="6254e-457">Quando você implanta em um aplicativo do Serviço de Aplicativo, o aplicativo respeita as configurações na seção [Logs do Serviço de Aplicativo](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) da página **Serviço de Aplicativo** do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="6254e-457">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="6254e-458">Quando as configurações a seguir são atualizadas, as alterações entram em vigor imediatamente sem a necessidade de uma reinicialização ou reimplantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6254e-458">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="6254e-459">**Log de aplicativo (Sistema de Arquivos)**</span><span class="sxs-lookup"><span data-stu-id="6254e-459">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="6254e-460">**Log de aplicativo (Blob)**</span><span class="sxs-lookup"><span data-stu-id="6254e-460">**Application Logging (Blob)**</span></span>

<span data-ttu-id="6254e-461">O local padrão para arquivos de log é na pasta *D:\\home\\LogFiles\\Application* e o nome de arquivo padrão é *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="6254e-461">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="6254e-462">O limite padrão de tamanho do arquivo é 10 MB e o número padrão máximo de arquivos mantidos é 2.</span><span class="sxs-lookup"><span data-stu-id="6254e-462">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="6254e-463">O nome de blob padrão é *{app-name}{timestamp}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="6254e-463">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="6254e-464">O provedor funciona somente quando o projeto é executado no ambiente do Azure.</span><span class="sxs-lookup"><span data-stu-id="6254e-464">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="6254e-465">Ele não tem nenhum efeito quando o projeto é executado localmente&mdash;ele não grava em arquivos locais ou no armazenamento de desenvolvimento local para blobs.</span><span class="sxs-lookup"><span data-stu-id="6254e-465">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="6254e-466">Fluxo de log do Azure</span><span class="sxs-lookup"><span data-stu-id="6254e-466">Azure log streaming</span></span>

<span data-ttu-id="6254e-467">O fluxo de log do Azure permite que você exiba a atividade de log em tempo real:</span><span class="sxs-lookup"><span data-stu-id="6254e-467">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="6254e-468">O servidor de aplicativos</span><span class="sxs-lookup"><span data-stu-id="6254e-468">The app server</span></span>
* <span data-ttu-id="6254e-469">Do servidor Web</span><span class="sxs-lookup"><span data-stu-id="6254e-469">The web server</span></span>
* <span data-ttu-id="6254e-470">De uma solicitação de rastreio com falha</span><span class="sxs-lookup"><span data-stu-id="6254e-470">Failed request tracing</span></span>

<span data-ttu-id="6254e-471">Para configurar o fluxo de log do Azure:</span><span class="sxs-lookup"><span data-stu-id="6254e-471">To configure Azure log streaming:</span></span>

* <span data-ttu-id="6254e-472">Navegue até a página **Logs do Serviço de Aplicativo** da página do portal do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6254e-472">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="6254e-473">Defina o **Log de aplicativo (Sistema de Arquivos)** como **Ativado**.</span><span class="sxs-lookup"><span data-stu-id="6254e-473">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="6254e-474">Escolha o **Nível** de log.</span><span class="sxs-lookup"><span data-stu-id="6254e-474">Choose the log **Level**.</span></span>

<span data-ttu-id="6254e-475">Navegue até a página **Fluxo de Log** para exibir as mensagens de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6254e-475">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="6254e-476">Elas são registradas pelo aplicativo por meio da interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="6254e-476">They're logged by the app through the `ILogger` interface.</span></span>

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="6254e-477">Log de rastreamento do Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="6254e-477">Azure Application Insights trace logging</span></span>

<span data-ttu-id="6254e-478">O pacote de provedor [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) grava os logs no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="6254e-478">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="6254e-479">O Application Insights é um serviço que monitora um aplicativo web e fornece ferramentas para consultar e analisar os dados de telemetria.</span><span class="sxs-lookup"><span data-stu-id="6254e-479">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="6254e-480">Se você usar esse provedor, poderá consultar e analisar os logs usando as ferramentas do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="6254e-480">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="6254e-481">O provedor de registro em log está incluído como uma dependência de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que é o pacote que fornece toda a telemetria disponível para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6254e-481">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="6254e-482">Se você usar esse pacote, não precisará instalar o pacote de provedor.</span><span class="sxs-lookup"><span data-stu-id="6254e-482">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="6254e-483">Não use o pacote [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) &mdash;que é para o ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="6254e-483">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="6254e-484">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="6254e-484">For more information, see the following resources:</span></span>

* [<span data-ttu-id="6254e-485">Visão geral do Application Insights</span><span class="sxs-lookup"><span data-stu-id="6254e-485">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="6254e-486">[Application Insights para aplicativos ASP.NET Core](/azure/azure-monitor/app/asp-net-core-no-visualstudio) – Comece aqui se você deseja implementar toda a gama de telemetria do Application Insights junto com o registro em log.</span><span class="sxs-lookup"><span data-stu-id="6254e-486">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core-no-visualstudio) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="6254e-487">[ApplicationInsightsLoggerProvider para logs do .NET Core ILogger](/azure/azure-monitor/app/ilogger) – Comece aqui se você quiser implementar o provedor de log sem o restante da telemetria do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="6254e-487">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="6254e-488">[Application Insights logging adapters](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md) (Adaptadores de registro em log do Application Insights).</span><span class="sxs-lookup"><span data-stu-id="6254e-488">[Application Insights logging adapters](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span></span>
* <span data-ttu-id="6254e-489">[Instalar, configurar e inicializar o SDK do Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) – Tutorial interativo no site da Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="6254e-489">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>
::: moniker-end

> [!NOTE]
> <span data-ttu-id="6254e-490">A partir de 1/5/2019, o artigo chamado [Application Insights para ASP.NET Core](/azure/azure-monitor/app/asp-net-core) passa a estar desatualizado e as etapas do tutorial não funcionarão.</span><span class="sxs-lookup"><span data-stu-id="6254e-490">As of 5/1/2019, the article titled [Application Insights for ASP.NET Core](/azure/azure-monitor/app/asp-net-core) is out of date, and the tutorial steps don't work.</span></span> <span data-ttu-id="6254e-491">Em vez disso, confira [Application Insights para aplicativos ASP.NET Core](/azure/azure-monitor/app/asp-net-core-no-visualstudio).</span><span class="sxs-lookup"><span data-stu-id="6254e-491">Refer to [Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core-no-visualstudio) instead.</span></span> <span data-ttu-id="6254e-492">Estamos cientes do problema e estamos trabalhando para corrigi-lo.</span><span class="sxs-lookup"><span data-stu-id="6254e-492">We are aware of the issue and are working to correct it.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="6254e-493">Provedores de log de terceiros</span><span class="sxs-lookup"><span data-stu-id="6254e-493">Third-party logging providers</span></span>

<span data-ttu-id="6254e-494">Estruturas de log de terceiros que funcionam com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6254e-494">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="6254e-495">[elmah.io](https://elmah.io/) ([repositório GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="6254e-495">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="6254e-496">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([repositório do GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="6254e-496">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="6254e-497">[JSNLog](http://jsnlog.com/) ([repositório GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="6254e-497">[JSNLog](http://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="6254e-498">[KissLog.net](https://kisslog.net/) ([Repositório do GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="6254e-498">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="6254e-499">[Loggr](http://loggr.net/) ([repositório GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="6254e-499">[Loggr](http://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="6254e-500">[NLog](http://nlog-project.org/) ([repositório GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="6254e-500">[NLog](http://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="6254e-501">[Sentry](https://sentry.io/welcome/) ([repositório GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="6254e-501">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="6254e-502">[Serilog](https://serilog.net/) ([repositório GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="6254e-502">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="6254e-503">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositório Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="6254e-503">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="6254e-504">Algumas estruturas de terceiros podem fazer o [log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="6254e-504">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="6254e-505">Usar uma estrutura de terceiros é semelhante ao uso de um dos provedores internos:</span><span class="sxs-lookup"><span data-stu-id="6254e-505">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="6254e-506">Adicione um pacote NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="6254e-506">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="6254e-507">Chame um `ILoggerFactory`.</span><span class="sxs-lookup"><span data-stu-id="6254e-507">Call an `ILoggerFactory`.</span></span>

<span data-ttu-id="6254e-508">Para saber mais, consulte a documentação de cada provedor.</span><span class="sxs-lookup"><span data-stu-id="6254e-508">For more information, see each provider's documentation.</span></span> <span data-ttu-id="6254e-509">Não há suporte para provedores de log de terceiros na Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6254e-509">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6254e-510">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6254e-510">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
