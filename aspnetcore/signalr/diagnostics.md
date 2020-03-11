---
title: Registro em log e diagnóstico no ASP.NET Core SignalR
author: anurse
description: Saiba como coletar diagnósticos de seu aplicativo ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: c5bd2ac27f8ca486b0d75aed8439747f72448625
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660968"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="e6b65-103">Registro em log e diagnóstico no ASP.NET Core Signalr</span><span class="sxs-lookup"><span data-stu-id="e6b65-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="e6b65-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="e6b65-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="e6b65-105">Este artigo fornece orientação para a coleta de diagnósticos de seu aplicativo de sinal de ASP.NET Core para ajudar a solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="e6b65-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="e6b65-106">Registro em log do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="e6b65-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="e6b65-107">Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6b65-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e6b65-108">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="e6b65-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e6b65-109">Como o Signalr faz parte do ASP.NET Core, ele usa o sistema de registro em log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6b65-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="e6b65-110">Na configuração padrão, o Signalr registra muito pouca informação, mas isso pode ser configurado.</span><span class="sxs-lookup"><span data-stu-id="e6b65-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="e6b65-111">Consulte a documentação em [log de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6b65-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="e6b65-112">O signalr usa duas categorias de agente:</span><span class="sxs-lookup"><span data-stu-id="e6b65-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="e6b65-113">`Microsoft.AspNetCore.SignalR` &ndash; para logs relacionados a protocolos de Hub, ativação de hubs, invocação de métodos e outras atividades relacionadas ao Hub.</span><span class="sxs-lookup"><span data-stu-id="e6b65-113">`Microsoft.AspNetCore.SignalR` &ndash; for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="e6b65-114">`Microsoft.AspNetCore.Http.Connections` &ndash; para logs relacionados aos transportes, como WebSockets, sondagem longa e eventos enviados pelo servidor e infraestrutura de sinal de baixo nível.</span><span class="sxs-lookup"><span data-stu-id="e6b65-114">`Microsoft.AspNetCore.Http.Connections` &ndash; for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="e6b65-115">Para habilitar logs detalhados do Signalr, configure os dois prefixos anteriores para o nível de `Debug` no arquivo *appSettings. JSON* adicionando os seguintes itens à subseção `LogLevel` no `Logging`:</span><span class="sxs-lookup"><span data-stu-id="e6b65-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="e6b65-116">Você também pode configurar isso no código do seu método de `CreateWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="e6b65-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="e6b65-117">Se você não estiver usando a configuração baseada em JSON, defina os seguintes valores de configuração em seu sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="e6b65-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="e6b65-118">Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados.</span><span class="sxs-lookup"><span data-stu-id="e6b65-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="e6b65-119">Por exemplo, ao usar variáveis de ambiente, são usados dois caracteres `_` em vez da `:` (por exemplo, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="e6b65-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="e6b65-120">É recomendável usar o nível de `Debug` ao coletar diagnósticos mais detalhados para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6b65-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="e6b65-121">O nível de `Trace` produz diagnósticos de nível muito baixo e raramente é necessário para diagnosticar problemas em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6b65-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="e6b65-122">Acessar logs do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="e6b65-122">Access server-side logs</span></span>

<span data-ttu-id="e6b65-123">A maneira como você acessa os logs do lado do servidor depende do ambiente no qual você está executando o.</span><span class="sxs-lookup"><span data-stu-id="e6b65-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="e6b65-124">Como um aplicativo de console fora do IIS</span><span class="sxs-lookup"><span data-stu-id="e6b65-124">As a console app outside IIS</span></span>

<span data-ttu-id="e6b65-125">Se você estiver executando o em um aplicativo de console, o [agente de log do console](xref:fundamentals/logging/index#console-provider) deverá ser habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="e6b65-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="e6b65-126">Os logs do signalr serão exibidos no console do.</span><span class="sxs-lookup"><span data-stu-id="e6b65-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="e6b65-127">Dentro de IIS Express do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6b65-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="e6b65-128">O Visual Studio exibe a saída de log na janela **saída** .</span><span class="sxs-lookup"><span data-stu-id="e6b65-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="e6b65-129">Selecione a opção de menu suspenso do **ASP.NET Core Web Server** .</span><span class="sxs-lookup"><span data-stu-id="e6b65-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="e6b65-130">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e6b65-130">Azure App Service</span></span>

<span data-ttu-id="e6b65-131">Habilite a opção de **log do aplicativo (Filesystem)** na seção **logs de diagnóstico** do portal do serviço Azure app e configure o **nível** para `Verbose`.</span><span class="sxs-lookup"><span data-stu-id="e6b65-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="e6b65-132">Os logs devem estar disponíveis no serviço de **streaming de log** e nos logs no sistema de arquivos do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6b65-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="e6b65-133">Para obter mais informações, consulte [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="e6b65-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="e6b65-134">Outros ambientes</span><span class="sxs-lookup"><span data-stu-id="e6b65-134">Other environments</span></span>

<span data-ttu-id="e6b65-135">Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, kubernetes ou serviço do Windows), consulte <xref:fundamentals/logging/index> para obter mais informações sobre como configurar provedores de log adequados para o ambiente.</span><span class="sxs-lookup"><span data-stu-id="e6b65-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="e6b65-136">Log de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="e6b65-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="e6b65-137">Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6b65-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e6b65-138">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="e6b65-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e6b65-139">Ao usar o cliente JavaScript, você pode configurar as opções de log usando o método `configureLogging` em `HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="e6b65-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="e6b65-140">Para desabilitar totalmente o registro em log, especifique `signalR.LogLevel.None` no método `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="e6b65-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="e6b65-141">A tabela a seguir mostra os níveis de log disponíveis para o cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e6b65-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="e6b65-142">Definir o nível de log como um desses valores habilita o registro em log nesse nível e todos os níveis acima dele na tabela.</span><span class="sxs-lookup"><span data-stu-id="e6b65-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="e6b65-143">Nível</span><span class="sxs-lookup"><span data-stu-id="e6b65-143">Level</span></span> | <span data-ttu-id="e6b65-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="e6b65-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="e6b65-145">Nenhuma mensagem é registrada em log.</span><span class="sxs-lookup"><span data-stu-id="e6b65-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="e6b65-146">Mensagens que indicam uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6b65-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="e6b65-147">Mensagens que indicam uma falha na operação atual.</span><span class="sxs-lookup"><span data-stu-id="e6b65-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="e6b65-148">Mensagens que indicam um problema não fatal.</span><span class="sxs-lookup"><span data-stu-id="e6b65-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="e6b65-149">Mensagens informativas.</span><span class="sxs-lookup"><span data-stu-id="e6b65-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="e6b65-150">Mensagens de diagnóstico úteis para depuração.</span><span class="sxs-lookup"><span data-stu-id="e6b65-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="e6b65-151">Mensagens de diagnóstico muito detalhadas projetadas para diagnosticar problemas específicos.</span><span class="sxs-lookup"><span data-stu-id="e6b65-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="e6b65-152">Depois de configurar o detalhamento, os logs serão gravados no console do navegador (ou na saída padrão em um aplicativo NodeJS).</span><span class="sxs-lookup"><span data-stu-id="e6b65-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="e6b65-153">Se você quiser enviar logs para um sistema de registro em log personalizado, poderá fornecer um objeto JavaScript que implementa a interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="e6b65-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="e6b65-154">O único método que precisa ser implementado é `log`, que usa o nível do evento e a mensagem associada ao evento.</span><span class="sxs-lookup"><span data-stu-id="e6b65-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="e6b65-155">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e6b65-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="e6b65-156">Log de cliente .NET</span><span class="sxs-lookup"><span data-stu-id="e6b65-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="e6b65-157">Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6b65-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e6b65-158">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="e6b65-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e6b65-159">Para obter logs do cliente .NET, você pode usar o método `ConfigureLogging` em `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e6b65-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="e6b65-160">Isso funciona da mesma maneira que o método de `ConfigureLogging` em `WebHostBuilder` e `HostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e6b65-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="e6b65-161">Você pode configurar os mesmos provedores de log usados no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e6b65-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="e6b65-162">No entanto, você precisa instalar e habilitar manualmente os pacotes NuGet para os provedores de log individuais.</span><span class="sxs-lookup"><span data-stu-id="e6b65-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="e6b65-163">Log de console</span><span class="sxs-lookup"><span data-stu-id="e6b65-163">Console logging</span></span>

<span data-ttu-id="e6b65-164">Para habilitar o log do console, adicione o pacote [Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="e6b65-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="e6b65-165">Em seguida, use o método `AddConsole` para configurar o agente de log do console:</span><span class="sxs-lookup"><span data-stu-id="e6b65-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="e6b65-166">Depurar log da janela de saída</span><span class="sxs-lookup"><span data-stu-id="e6b65-166">Debug output window logging</span></span>

<span data-ttu-id="e6b65-167">Você também pode configurar logs para ir para a janela de **saída** no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e6b65-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="e6b65-168">Instale o pacote [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) e use o método `AddDebug`:</span><span class="sxs-lookup"><span data-stu-id="e6b65-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="e6b65-169">Outros provedores de log</span><span class="sxs-lookup"><span data-stu-id="e6b65-169">Other logging providers</span></span>

<span data-ttu-id="e6b65-170">o SignalR dá suporte a outros provedores de log, como Serilog, Seq, NLog ou qualquer outro sistema de registro em log que se integre ao `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="e6b65-170">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="e6b65-171">Se o sistema de registro em log fornecer um `ILoggerProvider`, você poderá registrá-lo com `AddProvider`:</span><span class="sxs-lookup"><span data-stu-id="e6b65-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="e6b65-172">Detalhes do controle</span><span class="sxs-lookup"><span data-stu-id="e6b65-172">Control verbosity</span></span>

<span data-ttu-id="e6b65-173">Se você estiver fazendo logon de outros locais em seu aplicativo, alterar o nível padrão para `Debug` pode ser muito detalhado.</span><span class="sxs-lookup"><span data-stu-id="e6b65-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="e6b65-174">Você pode usar um filtro para configurar o nível de log para logs de SignalR.</span><span class="sxs-lookup"><span data-stu-id="e6b65-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="e6b65-175">Isso pode ser feito no código, praticamente da mesma forma que no servidor:</span><span class="sxs-lookup"><span data-stu-id="e6b65-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="e6b65-176">Rastreamentos de rede</span><span class="sxs-lookup"><span data-stu-id="e6b65-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="e6b65-177">Um rastreamento de rede contém todo o conteúdo de cada mensagem enviada pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e6b65-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="e6b65-178">**Nunca** poste rastreamentos de rede brutos de aplicativos de produção para fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="e6b65-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e6b65-179">Se você encontrar um problema, um rastreamento de rede pode, às vezes, fornecer muitas informações úteis.</span><span class="sxs-lookup"><span data-stu-id="e6b65-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="e6b65-180">Isso é particularmente útil se você pretende arquivar um problema em nosso Issue Tracker.</span><span class="sxs-lookup"><span data-stu-id="e6b65-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="e6b65-181">Coletar um rastreamento de rede com o Fiddler (opção preferencial)</span><span class="sxs-lookup"><span data-stu-id="e6b65-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="e6b65-182">Esse método funciona para todos os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="e6b65-182">This method works for all apps.</span></span>

<span data-ttu-id="e6b65-183">O Fiddler é uma ferramenta muito poderosa para coletar rastreamentos HTTP.</span><span class="sxs-lookup"><span data-stu-id="e6b65-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="e6b65-184">Instale-o em [Telerik.com/Fiddler](https://www.telerik.com/fiddler), inicie-o e execute o aplicativo e reproduza o problema.</span><span class="sxs-lookup"><span data-stu-id="e6b65-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="e6b65-185">O Fiddler está disponível para o Windows e há versões beta para macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="e6b65-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="e6b65-186">Se você se conectar usando HTTPS, haverá algumas etapas adicionais para garantir que o Fiddler possa descriptografar o tráfego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e6b65-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="e6b65-187">Para obter mais detalhes, consulte a [documentação do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="e6b65-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="e6b65-188">Depois de coletar o rastreamento, você pode exportar o rastreamento escolhendo **arquivo** > **salvar** > **todas as sessões** na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="e6b65-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportando todas as sessões do Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="e6b65-190">Coletar um rastreamento de rede com tcpdump (somente macOS e Linux)</span><span class="sxs-lookup"><span data-stu-id="e6b65-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="e6b65-191">Esse método funciona para todos os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="e6b65-191">This method works for all apps.</span></span>

<span data-ttu-id="e6b65-192">Você pode coletar rastreamentos TCP brutos usando tcpdump executando o comando a seguir de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="e6b65-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="e6b65-193">Talvez seja necessário `root` ou prefixar o comando com `sudo` se você receber um erro de permissões:</span><span class="sxs-lookup"><span data-stu-id="e6b65-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="e6b65-194">Substitua `[interface]` pela interface de rede que você deseja capturar.</span><span class="sxs-lookup"><span data-stu-id="e6b65-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="e6b65-195">Normalmente, isso é algo como `/dev/eth0` (para a sua interface Ethernet padrão) ou `/dev/lo0` (para tráfego de localhost).</span><span class="sxs-lookup"><span data-stu-id="e6b65-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="e6b65-196">Para obter mais informações, consulte a página do `tcpdump` Man no sistema host.</span><span class="sxs-lookup"><span data-stu-id="e6b65-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="e6b65-197">Coletar um rastreamento de rede no navegador</span><span class="sxs-lookup"><span data-stu-id="e6b65-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="e6b65-198">Esse método só funciona para aplicativos baseados em navegador.</span><span class="sxs-lookup"><span data-stu-id="e6b65-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="e6b65-199">A maioria dos Ferramentas para Desenvolvedores de navegador tem uma guia de "rede" que permite capturar a atividade de rede entre o navegador e o servidor.</span><span class="sxs-lookup"><span data-stu-id="e6b65-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="e6b65-200">No entanto, esses rastreamentos não incluem o WebSocket e as mensagens de evento enviadas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="e6b65-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="e6b65-201">Se você estiver usando esses transportes, usar uma ferramenta como Fiddler ou TcpDump (descrita abaixo) é uma abordagem melhor.</span><span class="sxs-lookup"><span data-stu-id="e6b65-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="e6b65-202">Microsoft Edge e Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e6b65-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="e6b65-203">(As instruções são as mesmas para o Edge e para o Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="e6b65-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="e6b65-204">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="e6b65-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="e6b65-205">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="e6b65-205">Click the Network Tab</span></span>
3. <span data-ttu-id="e6b65-206">Atualizar a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="e6b65-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="e6b65-207">Clique no ícone salvar na barra de ferramentas para exportar o rastreamento como um arquivo "HAR":</span><span class="sxs-lookup"><span data-stu-id="e6b65-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![O ícone salvar na guia rede de ferramentas de desenvolvimento do Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="e6b65-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="e6b65-209">Google Chrome</span></span>

1. <span data-ttu-id="e6b65-210">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="e6b65-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="e6b65-211">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="e6b65-211">Click the Network Tab</span></span>
3. <span data-ttu-id="e6b65-212">Atualizar a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="e6b65-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="e6b65-213">Clique com o botão direito do mouse em qualquer lugar na lista de solicitações e escolha "salvar como HAR com conteúdo":</span><span class="sxs-lookup"><span data-stu-id="e6b65-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Opção "salvar como HAR com conteúdo" na guia rede de ferramentas de desenvolvimento do Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="e6b65-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e6b65-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="e6b65-216">Pressione F12 para abrir as ferramentas de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="e6b65-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="e6b65-217">Clique na guia rede</span><span class="sxs-lookup"><span data-stu-id="e6b65-217">Click the Network Tab</span></span>
3. <span data-ttu-id="e6b65-218">Atualizar a página (se necessário) e reproduzir o problema</span><span class="sxs-lookup"><span data-stu-id="e6b65-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="e6b65-219">Clique com o botão direito do mouse em qualquer lugar na lista de solicitações e escolha "salvar tudo como HAR"</span><span class="sxs-lookup"><span data-stu-id="e6b65-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Opção "salvar tudo como HAR" na guia de rede de ferramentas de desenvolvimento do Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="e6b65-221">Anexar arquivos de diagnóstico a problemas do GitHub</span><span class="sxs-lookup"><span data-stu-id="e6b65-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="e6b65-222">Você pode anexar arquivos de diagnóstico a problemas do GitHub renomeando-os para que eles tenham uma extensão `.txt` e, em seguida, arrastando-os e soltando-os no problema.</span><span class="sxs-lookup"><span data-stu-id="e6b65-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="e6b65-223">Não Cole o conteúdo dos arquivos de log ou dos rastreamentos de rede em um problema do GitHub.</span><span class="sxs-lookup"><span data-stu-id="e6b65-223">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="e6b65-224">Esses logs e rastreamentos podem ser bem grandes, e o GitHub geralmente os trunca.</span><span class="sxs-lookup"><span data-stu-id="e6b65-224">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Arrastando arquivos de log para um problema do GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="e6b65-226">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e6b65-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
