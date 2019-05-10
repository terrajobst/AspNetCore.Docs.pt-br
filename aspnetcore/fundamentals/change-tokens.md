---
title: Detectar alterações com tokens de alteração no ASP.NET Core
author: guardrex
description: Saiba como usar tokens de alteração para controlar alterações.
ms.author: riande
ms.date: 11/10/2017
uid: fundamentals/change-tokens
ms.openlocfilehash: f7dad52fc19b6dc5c8668ad852e69acd65a72162
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886871"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="218d0-103">Detectar alterações com tokens de alteração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="218d0-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="218d0-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="218d0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="218d0-105">Um *token de alteração* é um bloco de construção de uso geral e de baixo nível, usado para controlar as alterações.</span><span class="sxs-lookup"><span data-stu-id="218d0-105">A *change token* is a general-purpose, low-level building block used to track changes.</span></span>

<span data-ttu-id="218d0-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="218d0-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="218d0-107">Interface IChangeToken</span><span class="sxs-lookup"><span data-stu-id="218d0-107">IChangeToken interface</span></span>

<span data-ttu-id="218d0-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) propaga notificações de que ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="218d0-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) propagates notifications that a change has occurred.</span></span> <span data-ttu-id="218d0-109">`IChangeToken` reside no namespace [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives).</span><span class="sxs-lookup"><span data-stu-id="218d0-109">`IChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="218d0-110">Para aplicativos que não usam o metapacote [Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), veja o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="218d0-110">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="218d0-111">`IChangeToken` tem duas propriedades:</span><span class="sxs-lookup"><span data-stu-id="218d0-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="218d0-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) indique se o token gera retornos de chamada de forma proativa.</span><span class="sxs-lookup"><span data-stu-id="218d0-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="218d0-113">Se `ActiveChangedCallbacks` é definido como `false`, um retorno de chamada nunca é chamado e o aplicativo precisa sondar `HasChanged` em busca de alterações.</span><span class="sxs-lookup"><span data-stu-id="218d0-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="218d0-114">Também é possível que um token nunca seja cancelado se não ocorrerem alterações ou o ouvinte de alteração subjacente for removido ou desabilitado.</span><span class="sxs-lookup"><span data-stu-id="218d0-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="218d0-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) obtém um valor que indica se uma alteração ocorreu.</span><span class="sxs-lookup"><span data-stu-id="218d0-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) gets a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="218d0-116">A interface tem um método, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), que registra um retorno de chamada que é invocado quando o token é alterado.</span><span class="sxs-lookup"><span data-stu-id="218d0-116">The interface has one method, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="218d0-117">`HasChanged` precisa ser definido antes de o retorno de chamada ser invocado.</span><span class="sxs-lookup"><span data-stu-id="218d0-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="218d0-118">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="218d0-118">ChangeToken class</span></span>

<span data-ttu-id="218d0-119">`ChangeToken` é uma classe estática usada para propagar notificações de que ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="218d0-119">`ChangeToken` is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="218d0-120">`ChangeToken` reside no namespace [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives).</span><span class="sxs-lookup"><span data-stu-id="218d0-120">`ChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="218d0-121">Para aplicativos que não usam o metapacote [Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app), veja o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="218d0-121">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="218d0-122">O método `ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) registra um `Action` para chamar sempre que o token é alterado:</span><span class="sxs-lookup"><span data-stu-id="218d0-122">The `ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="218d0-123">`Func<IChangeToken>` produz o token.</span><span class="sxs-lookup"><span data-stu-id="218d0-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="218d0-124">`Action` é chamado quando o token é alterado.</span><span class="sxs-lookup"><span data-stu-id="218d0-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="218d0-125">`ChangeToken` tem uma sobrecarga [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) que usa um parâmetro `TState` adicional que é passado para o consumidor de token `Action`.</span><span class="sxs-lookup"><span data-stu-id="218d0-125">`ChangeToken` has an [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) overload that takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="218d0-126">`OnChange` retorna um [IDisposable](/dotnet/api/system.idisposable).</span><span class="sxs-lookup"><span data-stu-id="218d0-126">`OnChange` returns an [IDisposable](/dotnet/api/system.idisposable).</span></span> <span data-ttu-id="218d0-127">A chamada a [Dispose](/dotnet/api/system.idisposable.dispose) interrompe a escuta do token de outras alterações e libera os recursos do token.</span><span class="sxs-lookup"><span data-stu-id="218d0-127">Calling [Dispose](/dotnet/api/system.idisposable.dispose) stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="218d0-128">Usos de exemplo de tokens de alteração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="218d0-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="218d0-129">Tokens de alteração são usados nas áreas proeminentes do monitoramento do ASP.NET Core de alterações em objetos:</span><span class="sxs-lookup"><span data-stu-id="218d0-129">Change tokens are used in prominent areas of ASP.NET Core monitoring changes to objects:</span></span>

* <span data-ttu-id="218d0-130">Para monitorar as alterações em arquivos, o método [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) de [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) cria um `IChangeToken` para os arquivos especificados ou para pasta a ser inspecionada.</span><span class="sxs-lookup"><span data-stu-id="218d0-130">For monitoring changes to files, [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)'s [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="218d0-131">Tokens `IChangeToken` podem ser adicionados a entradas de cache para disparar remoções do cache após as alterações.</span><span class="sxs-lookup"><span data-stu-id="218d0-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="218d0-132">Para alterações `TOptions`, a implementação padrão de [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) de [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) tem uma sobrecarga que aceita uma ou mais instâncias [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1).</span><span class="sxs-lookup"><span data-stu-id="218d0-132">For `TOptions` changes, the default [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) implementation of [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) has an overload that accepts one or more [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1) instances.</span></span> <span data-ttu-id="218d0-133">Cada instância retorna um `IChangeToken` para registrar um retorno de chamada de notificação de alteração para o controle de alterações de opções.</span><span class="sxs-lookup"><span data-stu-id="218d0-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitoring-for-configuration-changes"></a><span data-ttu-id="218d0-134">Monitorando alterações de configuração</span><span class="sxs-lookup"><span data-stu-id="218d0-134">Monitoring for configuration changes</span></span>

<span data-ttu-id="218d0-135">Por padrão, os modelos do ASP.NET Core usam [arquivos de configuração JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) para carregar as definições de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="218d0-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="218d0-136">Esses arquivos são configurados com o método de extensão [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) no [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) que aceita um parâmetro `reloadOnChange` (ASP.NET Core 1.1 e posterior).</span><span class="sxs-lookup"><span data-stu-id="218d0-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) extension method on [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) that accepts a `reloadOnChange` parameter (ASP.NET Core 1.1 and later).</span></span> <span data-ttu-id="218d0-137">`reloadOnChange` indica se a configuração deve ser recarregada após alterações de arquivo.</span><span class="sxs-lookup"><span data-stu-id="218d0-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="218d0-138">Confira essa configuração no método prático [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) do [WebHost](/dotnet/api/microsoft.aspnetcore.webhost):</span><span class="sxs-lookup"><span data-stu-id="218d0-138">See this setting in the [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) convenience method [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder):</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

<span data-ttu-id="218d0-139">A configuração baseada em arquivo é representada por [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource).</span><span class="sxs-lookup"><span data-stu-id="218d0-139">File-based configuration is represented by [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource).</span></span> <span data-ttu-id="218d0-140">A `FileConfigurationSource` usa o [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) para monitorar arquivos.</span><span class="sxs-lookup"><span data-stu-id="218d0-140">`FileConfigurationSource` uses [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) to monitor files.</span></span>

<span data-ttu-id="218d0-141">Por padrão, o `IFileMonitor` é fornecido por um [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider), que usa [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) para monitorar as alterações no arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="218d0-141">By default, the `IFileMonitor` is provided by a [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider), which uses [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) to monitor for configuration file changes.</span></span>

<span data-ttu-id="218d0-142">O aplicativo de exemplo demonstra duas implementações para monitorar as alterações de configuração.</span><span class="sxs-lookup"><span data-stu-id="218d0-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="218d0-143">Se o arquivo *appsettings.json* é alterado ou a versão do Ambiente do arquivo é alterada, cada implementação executa um código personalizado.</span><span class="sxs-lookup"><span data-stu-id="218d0-143">If either the *appsettings.json* file changes or the Environment version of the file changes, each implementation executes custom code.</span></span> <span data-ttu-id="218d0-144">O aplicativo de exemplo grava uma mensagem no console.</span><span class="sxs-lookup"><span data-stu-id="218d0-144">The sample app writes a message to the console.</span></span>

<span data-ttu-id="218d0-145">O `FileSystemWatcher` de um arquivo de configuração pode disparar vários retornos de chamada de token para uma única alteração de arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="218d0-145">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="218d0-146">A implementação da amostra protege contra esse problema verificando os hashes de arquivo nos arquivos de configuração.</span><span class="sxs-lookup"><span data-stu-id="218d0-146">The sample's implementation guards against this problem by checking file hashes on the configuration files.</span></span> <span data-ttu-id="218d0-147">A verificação de hashes de arquivo garante que, pelo menos, um dos arquivos de configuração foi alterado antes de executar o código personalizado.</span><span class="sxs-lookup"><span data-stu-id="218d0-147">Checking file hashes ensures that at least one of the configuration files has changed before running the custom code.</span></span> <span data-ttu-id="218d0-148">A amostra usa o hash de arquivo SHA1 (*Utilities/Utilities.cs*):</span><span class="sxs-lookup"><span data-stu-id="218d0-148">The sample uses SHA1 file hashing (*Utilities/Utilities.cs*):</span></span>

   [!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   <span data-ttu-id="218d0-149">Uma nova tentativa é implementada com uma retirada exponencial.</span><span class="sxs-lookup"><span data-stu-id="218d0-149">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="218d0-150">A nova tentativa está presente porque o bloqueio de arquivo pode ocorrer, o que impede temporariamente a computação de um novo hash em um dos arquivos.</span><span class="sxs-lookup"><span data-stu-id="218d0-150">The re-try is present because file locking may occur that temporarily prevents computing a new hash on one of the files.</span></span>

### <a name="simple-startup-change-token"></a><span data-ttu-id="218d0-151">Token de alteração de inicialização simples</span><span class="sxs-lookup"><span data-stu-id="218d0-151">Simple startup change token</span></span>

<span data-ttu-id="218d0-152">Registre um retorno de chamada `Action` do consumidor de token para notificações de alteração no token de recarregamento de configuração (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="218d0-152">Register a token consumer `Action` callback for change notifications to the configuration reload token (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="218d0-153">`config.GetReloadToken()` fornece o token.</span><span class="sxs-lookup"><span data-stu-id="218d0-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="218d0-154">O retorno de chamada é o método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="218d0-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet3)]

<span data-ttu-id="218d0-155">O `state` do retorno de chamada é usado para passar o `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="218d0-155">The `state` of the callback is used to pass in the `IHostingEnvironment`.</span></span> <span data-ttu-id="218d0-156">Isso é útil para determinar o arquivo de configuração JSON *appsettings* correto a ser monitorado, *appsettings.&lt;Environment&gt;.json*.</span><span class="sxs-lookup"><span data-stu-id="218d0-156">This is useful to determine the correct *appsettings* configuration JSON file to monitor, *appsettings.&lt;Environment&gt;.json*.</span></span> <span data-ttu-id="218d0-157">Hashes de arquivo são usados para impedir que a instrução `WriteConsole` seja executada várias vezes, devido a vários retornos de chamada de token quando o arquivo de configuração é alterado somente uma vez.</span><span class="sxs-lookup"><span data-stu-id="218d0-157">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="218d0-158">Esse sistema é executado, desde que o aplicativo esteja em execução e não possa ser desabilitado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="218d0-158">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitoring-configuration-changes-as-a-service"></a><span data-ttu-id="218d0-159">Monitorando alterações de configuração como um serviço</span><span class="sxs-lookup"><span data-stu-id="218d0-159">Monitoring configuration changes as a service</span></span>

<span data-ttu-id="218d0-160">A amostra implementa:</span><span class="sxs-lookup"><span data-stu-id="218d0-160">The sample implements:</span></span>

* <span data-ttu-id="218d0-161">Monitoramento de token de inicialização básica.</span><span class="sxs-lookup"><span data-stu-id="218d0-161">Basic startup token monitoring.</span></span>
* <span data-ttu-id="218d0-162">Monitoramento como serviço.</span><span class="sxs-lookup"><span data-stu-id="218d0-162">Monitoring as a service.</span></span>
* <span data-ttu-id="218d0-163">Um mecanismo para habilitar e desabilitar o monitoramento.</span><span class="sxs-lookup"><span data-stu-id="218d0-163">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="218d0-164">A amostra estabelece uma interface `IConfigurationMonitor` (*Extensions/ConfigurationMonitor.cs*):</span><span class="sxs-lookup"><span data-stu-id="218d0-164">The sample establishes an `IConfigurationMonitor` interface (*Extensions/ConfigurationMonitor.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="218d0-165">O construtor da classe implementada, `ConfigurationMonitor`, registra um retorno de chamada para notificações de alteração:</span><span class="sxs-lookup"><span data-stu-id="218d0-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="218d0-166">`config.GetReloadToken()` fornece o token.</span><span class="sxs-lookup"><span data-stu-id="218d0-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="218d0-167">`InvokeChanged` é o método de retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="218d0-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="218d0-168">O `state` nesta instância é uma referência à instância `IConfigurationMonitor` que é usada para acessar o estado de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="218d0-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that is used to access the monitoring state.</span></span> <span data-ttu-id="218d0-169">Duas propriedades são usadas:</span><span class="sxs-lookup"><span data-stu-id="218d0-169">Two properties are used:</span></span>

* <span data-ttu-id="218d0-170">`MonitoringEnabled` indica se o retorno de chamada deve executar seu código personalizado.</span><span class="sxs-lookup"><span data-stu-id="218d0-170">`MonitoringEnabled` indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="218d0-171">`CurrentState` descreve o estado atual de monitoramento para uso na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="218d0-171">`CurrentState` describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="218d0-172">O método `InvokeChanged` é semelhante à abordagem anterior, exceto que ele:</span><span class="sxs-lookup"><span data-stu-id="218d0-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="218d0-173">Não executa o código, a menos que `MonitoringEnabled` seja `true`.</span><span class="sxs-lookup"><span data-stu-id="218d0-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="218d0-174">Anota o `state` atual e sua saída `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="218d0-174">Notes the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="218d0-175">Uma instância `ConfigurationMonitor` é registrada como um serviço em `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="218d0-175">An instance `ConfigurationMonitor` is registered as a service in `ConfigureServices` of *Startup.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="218d0-176">A página Índice oferece ao usuário o controle sobre o monitoramento de configuração.</span><span class="sxs-lookup"><span data-stu-id="218d0-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="218d0-177">A instância de `IConfigurationMonitor` é injetada no `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="218d0-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`:</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="218d0-178">Um botão habilita e desabilita o monitoramento:</span><span class="sxs-lookup"><span data-stu-id="218d0-178">A button enables and disables monitoring:</span></span>

[!code-cshtml[](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="218d0-179">Quando `OnPostStartMonitoring` é disparado, o monitoramento é habilitado e o estado atual é desmarcado.</span><span class="sxs-lookup"><span data-stu-id="218d0-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="218d0-180">Quando `OnPostStopMonitoring` é disparado, o monitoramento é desabilitado e o estado é definido para refletir que o monitoramento não está ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="218d0-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

## <a name="monitoring-cached-file-changes"></a><span data-ttu-id="218d0-181">Monitorando alterações de arquivos armazenados em cache</span><span class="sxs-lookup"><span data-stu-id="218d0-181">Monitoring cached file changes</span></span>

<span data-ttu-id="218d0-182">O conteúdo do arquivo pode ser armazenado em cache em memória usando [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span><span class="sxs-lookup"><span data-stu-id="218d0-182">File content can be cached in-memory using [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="218d0-183">O cache na memória é descrito no tópico [Cache na memória](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="218d0-183">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="218d0-184">Sem realizar etapas adicionais, como a implementação descrita abaixo, dados *obsoletos* (desatualizados) são retornados de um cache se os dados de origem são alterados.</span><span class="sxs-lookup"><span data-stu-id="218d0-184">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="218d0-185">Não levando em conta o status de um arquivo de origem armazenado em cache durante a renovação de um período de [expiração deslizante](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) resulta em dados de cache obsoletos.</span><span class="sxs-lookup"><span data-stu-id="218d0-185">Not taking into account the status of a cached source file when renewing a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period leads to stale cache data.</span></span> <span data-ttu-id="218d0-186">Cada solicitação de dados renova o período de expiração deslizante, mas o arquivo nunca é recarregado no cache.</span><span class="sxs-lookup"><span data-stu-id="218d0-186">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="218d0-187">Os recursos do aplicativo que usam o conteúdo armazenado em cache do arquivo estão sujeitos ao possível recebimento de conteúdo obsoleto.</span><span class="sxs-lookup"><span data-stu-id="218d0-187">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="218d0-188">O uso de tokens de alteração em um cenário de cache de arquivo impede o conteúdo de arquivo obsoleto no cache.</span><span class="sxs-lookup"><span data-stu-id="218d0-188">Using change tokens in a file caching scenario prevents stale file content in the cache.</span></span> <span data-ttu-id="218d0-189">O aplicativo de exemplo demonstra uma implementação da abordagem.</span><span class="sxs-lookup"><span data-stu-id="218d0-189">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="218d0-190">A amostra usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="218d0-190">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="218d0-191">Retornar o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="218d0-191">Return file content.</span></span>
* <span data-ttu-id="218d0-192">Implementar um algoritmo de repetição com retirada exponencial para abranger casos em que um bloqueio de arquivo está impedindo temporariamente a leitura de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="218d0-192">Implement a retry algorithm with exponential back-off to cover cases where a file lock is temporarily preventing a file from being read.</span></span>

<span data-ttu-id="218d0-193">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="218d0-193">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="218d0-194">Um `FileService` é criado para manipular pesquisas de arquivos armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="218d0-194">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="218d0-195">A chamada de método `GetFileContent` do serviço tenta obter o conteúdo do arquivo do cache em memória e retorná-lo para o chamador (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="218d0-195">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="218d0-196">Se o conteúdo armazenado em cache não é encontrado com a chave de cache, as seguintes ações são executadas:</span><span class="sxs-lookup"><span data-stu-id="218d0-196">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="218d0-197">O conteúdo do arquivo é obtido com `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="218d0-197">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="218d0-198">Um token de alteração é obtido do provedor de arquivo com [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch).</span><span class="sxs-lookup"><span data-stu-id="218d0-198">A change token is obtained from the file provider with [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch).</span></span> <span data-ttu-id="218d0-199">O retorno de chamada do token é disparado quando o arquivo é modificado.</span><span class="sxs-lookup"><span data-stu-id="218d0-199">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="218d0-200">O conteúdo do arquivo é armazenado em cache com um período de [expiração deslizante](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration).</span><span class="sxs-lookup"><span data-stu-id="218d0-200">The file content is cached with a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period.</span></span> <span data-ttu-id="218d0-201">O token de alteração é anexado com [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) para remover a entrada do cache se o arquivo é alterado enquanto ele é armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="218d0-201">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) to evict the cache entry if the file changes while it's cached.</span></span>

[!code-csharp[](change-tokens/sample/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="218d0-202">O `FileService` é registrado no contêiner de serviço junto com o serviço de cache da memória (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="218d0-202">The `FileService` is registered in the service container along with the memory caching service (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet4)]

<span data-ttu-id="218d0-203">O modelo de página carrega o conteúdo do arquivo usando o serviço (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="218d0-203">The page model loads the file's content using the service (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="218d0-204">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="218d0-204">CompositeChangeToken class</span></span>

<span data-ttu-id="218d0-205">Para representar uma ou mais instâncias de `IChangeToken` em um único objeto, use a classe [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken).</span><span class="sxs-lookup"><span data-stu-id="218d0-205">For representing one or more `IChangeToken` instances in a single object, use the [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        { 
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="218d0-206">`HasChanged` nos relatórios de token compostos `true` se um token representado `HasChanged` é `true`.</span><span class="sxs-lookup"><span data-stu-id="218d0-206">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="218d0-207">`ActiveChangeCallbacks` nos relatórios de token compostos `true` se um token representado `ActiveChangeCallbacks` é `true`.</span><span class="sxs-lookup"><span data-stu-id="218d0-207">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="218d0-208">Se ocorrerem vários eventos de alteração simultâneos, o retorno de chamada de alteração composto será invocado exatamente uma vez.</span><span class="sxs-lookup"><span data-stu-id="218d0-208">If multiple concurrent change events occur, the composite change callback is invoked exactly one time.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="218d0-209">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="218d0-209">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
