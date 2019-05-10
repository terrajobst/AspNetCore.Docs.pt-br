---
title: Registro em log e diagnóstico no SignalR do ASP.NET Core
author: anurse
description: Saiba como coletar o diagnóstico do seu aplicativo SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 02/27/2019
uid: signalr/diagnostics
ms.openlocfilehash: b6bd21314ed183488999bcff3553e53493537a11
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896883"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="c5807-103">Registro em log e diagnóstico no SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5807-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="c5807-104">Por [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="c5807-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="c5807-105">Este artigo fornece orientações para reunir diagnósticos de seu aplicativo SignalR do ASP.NET Core para ajudar a solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="c5807-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="c5807-106">Registro em log do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="c5807-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="c5807-107">Logs do lado do servidor podem conter informações confidenciais de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c5807-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="c5807-108">**Nunca** postar logs brutos de aplicativos de produção em fóruns públicos, como o GitHub.</span><span class="sxs-lookup"><span data-stu-id="c5807-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="c5807-109">Como o SignalR é parte do ASP.NET Core, ele usa o sistema de registro do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5807-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="c5807-110">Na configuração padrão, SignalR registra informações muito pouco, mas isso pode configurado.</span><span class="sxs-lookup"><span data-stu-id="c5807-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="c5807-111">Consulte a documentação sobre [registro do ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o registro do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5807-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="c5807-112">O SignalR usa duas categorias de agente:</span><span class="sxs-lookup"><span data-stu-id="c5807-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="c5807-113">`Microsoft.AspNetCore.SignalR` -para logs relacionados aos protocolos de Hub, ativando Hubs, invocar métodos e outras atividades relacionadas ao Hub.</span><span class="sxs-lookup"><span data-stu-id="c5807-113">`Microsoft.AspNetCore.SignalR` - for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="c5807-114">`Microsoft.AspNetCore.Http.Connections` -para os logs relacionados a todos os transportes, como WebSockets, sondagem longa e eventos do Server-sent e infraestrutura de baixo nível SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5807-114">`Microsoft.AspNetCore.Http.Connections` - for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="c5807-115">Para habilitar logs detalhados do SignalR, configure ambos os prefixos anteriores para o `Debug` nível em seu `appsettings.json` arquivo adicionando itens a seguir para o `LogLevel` na seção `Logging`:</span><span class="sxs-lookup"><span data-stu-id="c5807-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your `appsettings.json` file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[Configuring logging](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="c5807-116">Você também pode configurar isso no código em seu `CreateWebHostBuilder` método:</span><span class="sxs-lookup"><span data-stu-id="c5807-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[Configuring logging in code](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="c5807-117">Se você não estiver usando a configuração baseada em JSON, defina os seguintes valores de configuração no seu sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="c5807-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="c5807-118">Verifique a documentação para o seu sistema de configuração determinar como especificar valores de configuração aninhada.</span><span class="sxs-lookup"><span data-stu-id="c5807-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="c5807-119">Por exemplo, ao usar variáveis de ambiente, duas `_` os caracteres são usados em vez do `:` (como: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="c5807-119">For example, when using environment variables, two `_` characters are used instead of the `:` (such as: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="c5807-120">É recomendável usar o `Debug` nível ao reunir mais detalhadas de diagnóstico para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c5807-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="c5807-121">O `Trace` nível produz diagnósticos de nível muito baixo e raramente é necessária para diagnosticar problemas em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c5807-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="c5807-122">Logs do lado do servidor de acesso</span><span class="sxs-lookup"><span data-stu-id="c5807-122">Access server-side logs</span></span>

<span data-ttu-id="c5807-123">Como você pode acessar os logs do lado do servidor depende do ambiente no qual você está executando.</span><span class="sxs-lookup"><span data-stu-id="c5807-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="c5807-124">Como um aplicativo de console fora do IIS</span><span class="sxs-lookup"><span data-stu-id="c5807-124">As a console app outside IIS</span></span>

<span data-ttu-id="c5807-125">Se você estiver executando em um aplicativo de console, o [agente de Console](xref:fundamentals/logging/index#console-provider) deve ser habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="c5807-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="c5807-126">Logs do SignalR serão exibidos no console.</span><span class="sxs-lookup"><span data-stu-id="c5807-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="c5807-127">Dentro do IIS Express do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5807-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="c5807-128">O Visual Studio exibe a saída de log na **saída** janela.</span><span class="sxs-lookup"><span data-stu-id="c5807-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="c5807-129">Selecione o **servidor de Web do ASP.NET Core** lista suspensa da opção.</span><span class="sxs-lookup"><span data-stu-id="c5807-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="c5807-130">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="c5807-130">Azure App Service</span></span>

<span data-ttu-id="c5807-131">Habilitar a opção de "Log de aplicativo (Filesystem)" na seção "Logs de diagnóstico" do portal do serviço de aplicativo do Azure e configurar o nível para `Verbose`.</span><span class="sxs-lookup"><span data-stu-id="c5807-131">Enable the "Application Logging (Filesystem)" option in the "Diagnostics logs" section of the Azure App Service portal and configure the Level to `Verbose`.</span></span> <span data-ttu-id="c5807-132">Os logs devem estar disponíveis no "Log" serviço de streaming, bem como em logs no sistema de arquivos do seu serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c5807-132">Logs should be available from the "Log streaming" service, as well as in logs on the file system of your App Service.</span></span> <span data-ttu-id="c5807-133">Para obter mais informações, consulte a documentação sobre [streaming de log do Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="c5807-133">For more information, see the documentation on [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="c5807-134">Outros ambientes</span><span class="sxs-lookup"><span data-stu-id="c5807-134">Other environments</span></span>

<span data-ttu-id="c5807-135">Se você estiver executando em outro ambiente (Docker, Kubernetes, serviço do Windows, etc.), consulte a documentação completa sobre [log do ASP.NET Core](xref:fundamentals/logging/index) para obter mais informações sobre como configurar provedores de log adequados ao seu ambiente.</span><span class="sxs-lookup"><span data-stu-id="c5807-135">If you're running in another environment (Docker, Kubernetes, Windows Service, etc.), see the full documentation on [ASP.NET Core Logging](xref:fundamentals/logging/index) for more information on how to configure logging providers suitable to your environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="c5807-136">Log de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="c5807-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="c5807-137">Logs do lado do cliente podem conter informações confidenciais de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c5807-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="c5807-138">**Nunca** postar logs brutos de aplicativos de produção em fóruns públicos, como o GitHub.</span><span class="sxs-lookup"><span data-stu-id="c5807-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="c5807-139">Ao usar o cliente JavaScript, você pode configurar opções de registro em log usando o `configureLogging` método no `HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="c5807-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[Configuring logging in the JavaScript client](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="c5807-140">Para desabilitar o registro em log totalmente, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="c5807-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c5807-141">A tabela a seguir mostra os níveis de log disponíveis para o cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c5807-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="c5807-142">Definindo o nível de log para um desses valores permite o log no nível e todos os níveis acima na tabela.</span><span class="sxs-lookup"><span data-stu-id="c5807-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="c5807-143">Nível</span><span class="sxs-lookup"><span data-stu-id="c5807-143">Level</span></span> | <span data-ttu-id="c5807-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="c5807-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="c5807-145">Nenhuma mensagem será registrada.</span><span class="sxs-lookup"><span data-stu-id="c5807-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="c5807-146">Mensagens que indicam uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c5807-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="c5807-147">Mensagens que indicam uma falha na operação atual.</span><span class="sxs-lookup"><span data-stu-id="c5807-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="c5807-148">Mensagens que indicam um problema de não-fatais.</span><span class="sxs-lookup"><span data-stu-id="c5807-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="c5807-149">Mensagens informativas.</span><span class="sxs-lookup"><span data-stu-id="c5807-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="c5807-150">Mensagens de diagnóstico útil para depuração.</span><span class="sxs-lookup"><span data-stu-id="c5807-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="c5807-151">Mensagens de diagnóstico muito detalhadas projetadas para diagnosticar problemas específicos.</span><span class="sxs-lookup"><span data-stu-id="c5807-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="c5807-152">Depois de configurar o detalhamento, os logs serão gravados para o Console do navegador (ou a saída padrão em um aplicativo NodeJS).</span><span class="sxs-lookup"><span data-stu-id="c5807-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="c5807-153">Se você quiser enviar logs para um sistema de registro em log personalizado, você pode fornecer um objeto JavaScript que implementa o `ILogger` interface.</span><span class="sxs-lookup"><span data-stu-id="c5807-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="c5807-154">O único método que precisa ser implementado é `log`, que usa o nível do evento e a mensagem associada ao evento.</span><span class="sxs-lookup"><span data-stu-id="c5807-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="c5807-155">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="c5807-155">For example:</span></span>

[!code-typescript[Creating a custom logger](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="c5807-156">Log de cliente .NET</span><span class="sxs-lookup"><span data-stu-id="c5807-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="c5807-157">Logs do lado do cliente podem conter informações confidenciais de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c5807-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="c5807-158">**Nunca** postar logs brutos de aplicativos de produção em fóruns públicos, como o GitHub.</span><span class="sxs-lookup"><span data-stu-id="c5807-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="c5807-159">Para obter os logs do cliente .NET, você pode usar o `ConfigureLogging` método no `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c5807-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="c5807-160">Isso funciona da mesma forma que o `ConfigureLogging` método no `WebHostBuilder` e `HostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c5807-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="c5807-161">Você pode configurar os mesmos provedores de log que você usar no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5807-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="c5807-162">No entanto, você precisa instalar e habilitar os pacotes do NuGet para os provedores de log individuais manualmente.</span><span class="sxs-lookup"><span data-stu-id="c5807-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="c5807-163">Log de console</span><span class="sxs-lookup"><span data-stu-id="c5807-163">Console logging</span></span>

<span data-ttu-id="c5807-164">Para habilitar o log de Console, adicione a [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) pacote.</span><span class="sxs-lookup"><span data-stu-id="c5807-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="c5807-165">Em seguida, use o `AddConsole` método para configurar o agente de console:</span><span class="sxs-lookup"><span data-stu-id="c5807-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[Configuring console logging in .NET client](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="c5807-166">O log de janela de saída de depuração</span><span class="sxs-lookup"><span data-stu-id="c5807-166">Debug output window logging</span></span>

<span data-ttu-id="c5807-167">Você também pode configurar logs para ir para o **saída** janela no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c5807-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="c5807-168">Instalar o [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) de pacote e usar o `AddDebug` método:</span><span class="sxs-lookup"><span data-stu-id="c5807-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[Configuring debug output window logging in .NET client](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="c5807-169">Outros provedores de log</span><span class="sxs-lookup"><span data-stu-id="c5807-169">Other logging providers</span></span>

<span data-ttu-id="c5807-170">O SignalR dá suporte a outros provedores de log como Serilog, Seq, NLog ou qualquer outro sistema de registro em log que se integra ao `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="c5807-170">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="c5807-171">Se o seu sistema de log fornece um `ILoggerProvider`, você pode registrá-lo com `AddProvider`:</span><span class="sxs-lookup"><span data-stu-id="c5807-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[Configuring a custom logging provider in .NET client](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="c5807-172">Nível de detalhes de controle</span><span class="sxs-lookup"><span data-stu-id="c5807-172">Control verbosity</span></span>

<span data-ttu-id="c5807-173">Se você está fazendo logon de outros locais em seu aplicativo, alterando o nível padrão a `Debug` pode ser muito detalhado.</span><span class="sxs-lookup"><span data-stu-id="c5807-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="c5807-174">Você pode usar um filtro para configurar o nível de log para logs do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5807-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="c5807-175">Isso pode ser feito no código, em grande parte da mesma maneira que no servidor:</span><span class="sxs-lookup"><span data-stu-id="c5807-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="c5807-176">Rastreamentos de rede</span><span class="sxs-lookup"><span data-stu-id="c5807-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="c5807-177">Um rastreamento de rede contém todo o conteúdo de cada mensagem enviada pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c5807-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="c5807-178">**Nunca** postar rastreamentos de rede brutos de aplicativos de produção em fóruns públicos, como o GitHub.</span><span class="sxs-lookup"><span data-stu-id="c5807-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="c5807-179">Se você encontrar um problema, um rastreamento de rede, às vezes, pode fornecer várias informações úteis.</span><span class="sxs-lookup"><span data-stu-id="c5807-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="c5807-180">Isso é particularmente útil se você pretender arquivar um problema no nosso rastreador de problemas.</span><span class="sxs-lookup"><span data-stu-id="c5807-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="c5807-181">Coletar um rastreamento de rede com o Fiddler (opção preferencial)</span><span class="sxs-lookup"><span data-stu-id="c5807-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="c5807-182">Esse método funciona para todos os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c5807-182">This method works for all apps.</span></span>

<span data-ttu-id="c5807-183">O Fiddler é uma ferramenta muito poderosa para coletar rastreamentos HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5807-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="c5807-184">Instale-o em [telerik.com/fiddler](https://www.telerik.com/fiddler), iniciá-lo e, em seguida, execute o aplicativo e reproduza o problema.</span><span class="sxs-lookup"><span data-stu-id="c5807-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="c5807-185">O Fiddler está disponível para Windows, e há versões beta para macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="c5807-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="c5807-186">Se você se conectar usando HTTPS, há algumas etapas adicionais para garantir que o Fiddler pode descriptografar o tráfego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c5807-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="c5807-187">Para obter mais detalhes, consulte o [documentação do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="c5807-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="c5807-188">Depois de coletar o rastreamento, você pode exportar o rastreamento, escolhendo **arquivo** > **salvar** > **todas as sessões...**  na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="c5807-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions...** from the menu bar.</span></span>

![Exportando todas as sessões do Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="c5807-190">Coletar um rastreamento de rede com tcpdump (macOS e Linux somente)</span><span class="sxs-lookup"><span data-stu-id="c5807-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="c5807-191">Esse método funciona para todos os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c5807-191">This method works for all apps.</span></span>

<span data-ttu-id="c5807-192">Você pode coletar rastreamentos TCP brutos usando tcpdump, executando o seguinte comando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="c5807-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="c5807-193">Você talvez precise ser `root` ou prefixar o comando com `sudo` se você receber um erro de permissões:</span><span class="sxs-lookup"><span data-stu-id="c5807-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="c5807-194">Substitua `[interface]` com a interface de rede que você deseja capturar no.</span><span class="sxs-lookup"><span data-stu-id="c5807-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="c5807-195">Geralmente, isso é algo como `/dev/eth0` (para sua interface Ethernet padrão) ou `/dev/lo0` (para tráfego de localhost).</span><span class="sxs-lookup"><span data-stu-id="c5807-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="c5807-196">Para obter mais informações, consulte o `tcpdump` página do manual no sistema de host.</span><span class="sxs-lookup"><span data-stu-id="c5807-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="c5807-197">Coletar um rastreamento de rede no navegador</span><span class="sxs-lookup"><span data-stu-id="c5807-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="c5807-198">Esse método só funciona para aplicativos baseados em navegador.</span><span class="sxs-lookup"><span data-stu-id="c5807-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="c5807-199">A maioria das ferramentas de desenvolvedor do navegador tem uma guia de "Rede" que permite que você capture a atividade de rede entre o navegador e o servidor.</span><span class="sxs-lookup"><span data-stu-id="c5807-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="c5807-200">No entanto, esses rastreamentos não incluem o WebSocket e Server-Sent mensagens de evento.</span><span class="sxs-lookup"><span data-stu-id="c5807-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="c5807-201">Se você estiver usando esses transportes, usando uma ferramenta como o Fiddler ou TcpDump (descritos abaixo) é uma abordagem melhor.</span><span class="sxs-lookup"><span data-stu-id="c5807-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="c5807-202">Internet Explorer e Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="c5807-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="c5807-203">(As instruções são as mesmas para o Microsoft Edge e Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="c5807-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="c5807-204">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="c5807-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="c5807-205">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="c5807-205">Click the Network Tab</span></span>
3. <span data-ttu-id="c5807-206">Atualize a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="c5807-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="c5807-207">Clique no ícone Salvar na barra de ferramentas para exportar o rastreamento como um arquivo de "HAR":</span><span class="sxs-lookup"><span data-stu-id="c5807-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![O salvamento guia rede das ferramentas de ícone no desenvolvimento do Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="c5807-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="c5807-209">Google Chrome</span></span>

1. <span data-ttu-id="c5807-210">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="c5807-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="c5807-211">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="c5807-211">Click the Network Tab</span></span>
3. <span data-ttu-id="c5807-212">Atualize a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="c5807-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="c5807-213">Clique com botão direito em qualquer lugar na lista de solicitações e escolha "Salvar como HAR com conteúdo":</span><span class="sxs-lookup"><span data-stu-id="c5807-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Opção "Salvar como HAR com conteúdo" no guia de rede de ferramentas de desenvolvimento do Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="c5807-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="c5807-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="c5807-216">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="c5807-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="c5807-217">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="c5807-217">Click the Network Tab</span></span>
3. <span data-ttu-id="c5807-218">Atualize a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="c5807-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="c5807-219">Clique com botão direito em qualquer lugar na lista de solicitações e escolha "Salvar todos os como HAR"</span><span class="sxs-lookup"><span data-stu-id="c5807-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Opção "Salvar tudo como HAR" no guia de rede de ferramentas de desenvolvimento do Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="c5807-221">Anexar arquivos de diagnóstico de problemas do GitHub</span><span class="sxs-lookup"><span data-stu-id="c5807-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="c5807-222">Você pode anexar arquivos de diagnóstico para problemas do GitHub ao renomeá-las para que eles tenham um `.txt` extensão arrastando e soltando-os para o problema.</span><span class="sxs-lookup"><span data-stu-id="c5807-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="c5807-223">Não cole o conteúdo de arquivos de log ou de rastreamentos de rede no problema do GitHub.</span><span class="sxs-lookup"><span data-stu-id="c5807-223">Please don't paste the content of log files or network traces in GitHub issue.</span></span> <span data-ttu-id="c5807-224">Esses logs e rastreamentos podem ser muito grandes e GitHub geralmente truncará-los.</span><span class="sxs-lookup"><span data-stu-id="c5807-224">These logs and traces can be quite large and GitHub will usually truncate them.</span></span>

![Arrastar arquivos de log para um problema do GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="c5807-226">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c5807-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
