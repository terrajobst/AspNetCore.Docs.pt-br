---
title: Detectar alterações com tokens de alteração no ASP.NET Core
author: guardrex
description: Saiba como usar tokens de alteração para controlar alterações.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/03/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 8b73b72d093b33edeb91bc78080e05aa312579ec
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561654"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="c8c15-103">Detectar alterações com tokens de alteração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8c15-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="c8c15-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c8c15-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c8c15-105">Um *token de alteração* é um bloco de construção de uso geral e de baixo nível, usado para controlar as alterações de estado.</span><span class="sxs-lookup"><span data-stu-id="c8c15-105">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="c8c15-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c8c15-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="c8c15-107">Interface IChangeToken</span><span class="sxs-lookup"><span data-stu-id="c8c15-107">IChangeToken interface</span></span>

<span data-ttu-id="c8c15-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificações de que ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="c8c15-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="c8c15-109">`IChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c8c15-109">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c8c15-110">Para aplicativos que não usam o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app), crie uma referência de pacote para o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="c8c15-110">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="c8c15-111">`IChangeToken` tem duas propriedades:</span><span class="sxs-lookup"><span data-stu-id="c8c15-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="c8c15-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica se o token de forma proativa gera retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="c8c15-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="c8c15-113">Se `ActiveChangedCallbacks` é definido como `false`, um retorno de chamada nunca é chamado e o aplicativo precisa sondar `HasChanged` em busca de alterações.</span><span class="sxs-lookup"><span data-stu-id="c8c15-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="c8c15-114">Também é possível que um token nunca seja cancelado se não ocorrerem alterações ou o ouvinte de alteração subjacente for removido ou desabilitado.</span><span class="sxs-lookup"><span data-stu-id="c8c15-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="c8c15-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recebe um valor que indica se uma alteração ocorreu.</span><span class="sxs-lookup"><span data-stu-id="c8c15-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="c8c15-116">A interface `IChangeToken` inclui o método [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra um retorno de chamada que é invocado quando o token é alterado.</span><span class="sxs-lookup"><span data-stu-id="c8c15-116">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="c8c15-117">`HasChanged` precisa ser definido antes de o retorno de chamada ser invocado.</span><span class="sxs-lookup"><span data-stu-id="c8c15-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="c8c15-118">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="c8c15-118">ChangeToken class</span></span>

<span data-ttu-id="c8c15-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> é uma classe estática usada para propagar notificações de que ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="c8c15-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="c8c15-120">`ChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c8c15-120">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c8c15-121">Para aplicativos que não usam o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app), crie uma referência de pacote para o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="c8c15-121">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="c8c15-122">O método [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra um `Action` para chamar sempre que o token é alterado:</span><span class="sxs-lookup"><span data-stu-id="c8c15-122">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="c8c15-123">`Func<IChangeToken>` produz o token.</span><span class="sxs-lookup"><span data-stu-id="c8c15-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="c8c15-124">`Action` é chamado quando o token é alterado.</span><span class="sxs-lookup"><span data-stu-id="c8c15-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="c8c15-125">A sobrecarga [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) usa um parâmetro `TState` adicional que é passado para o consumidor de token `Action`.</span><span class="sxs-lookup"><span data-stu-id="c8c15-125">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="c8c15-126">`OnChange` retorna um <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="c8c15-126">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="c8c15-127">A chamada <xref:System.IDisposable.Dispose*> interrompe a escuta do token de outras alterações e libera os recursos do token.</span><span class="sxs-lookup"><span data-stu-id="c8c15-127">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="c8c15-128">Usos de exemplo de tokens de alteração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8c15-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="c8c15-129">Os tokens de alteração são usados nas áreas proeminentes do ASP.NET Core para monitorar alterações em objetos:</span><span class="sxs-lookup"><span data-stu-id="c8c15-129">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="c8c15-130">Para monitorar as alterações em arquivos, o método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> cria um `IChangeToken` para os arquivos especificados ou para pasta a ser inspecionada.</span><span class="sxs-lookup"><span data-stu-id="c8c15-130">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="c8c15-131">Tokens `IChangeToken` podem ser adicionados a entradas de cache para disparar remoções do cache após as alterações.</span><span class="sxs-lookup"><span data-stu-id="c8c15-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="c8c15-132">Para as alterações de `TOptions`, a implementação <xref:Microsoft.Extensions.Options.OptionsMonitor`1> padrão de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tem uma sobrecarga que aceita uma ou mais instâncias <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="c8c15-132">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="c8c15-133">Cada instância retorna um `IChangeToken` para registrar um retorno de chamada de notificação de alteração para o controle de alterações de opções.</span><span class="sxs-lookup"><span data-stu-id="c8c15-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="c8c15-134">Monitorar as alterações de configuração</span><span class="sxs-lookup"><span data-stu-id="c8c15-134">Monitor for configuration changes</span></span>

<span data-ttu-id="c8c15-135">Por padrão, os modelos do ASP.NET Core usam [arquivos de configuração JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) para carregar as definições de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8c15-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="c8c15-136">Esses arquivos são configurados com o método de extensão [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) no <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que aceita um parâmetro `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="c8c15-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="c8c15-137">`reloadOnChange` indica se a configuração deve ser recarregada após alterações de arquivo.</span><span class="sxs-lookup"><span data-stu-id="c8c15-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="c8c15-138">Essa configuração é exibida no método de conveniência <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> de <xref:Microsoft.AspNetCore.WebHost>:</span><span class="sxs-lookup"><span data-stu-id="c8c15-138">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="c8c15-139">A configuração baseada em arquivo é representada por <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="c8c15-139">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="c8c15-140">`FileConfigurationSource` usa <xref:Microsoft.Extensions.FileProviders.IFileProvider> para monitorar arquivos.</span><span class="sxs-lookup"><span data-stu-id="c8c15-140">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="c8c15-141">Por padrão, o `IFileMonitor` é fornecido por um <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, que usa <xref:System.IO.FileSystemWatcher> para monitorar as alterações do arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="c8c15-141">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="c8c15-142">O aplicativo de exemplo demonstra duas implementações para monitorar as alterações de configuração.</span><span class="sxs-lookup"><span data-stu-id="c8c15-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="c8c15-143">Se qualquer um dos arquivos *appsettings* forem alterados, ambas as implementações de monitoramento de arquivo executam código personalizado&mdash;o aplicativo de exemplo grava uma mensagem no console.</span><span class="sxs-lookup"><span data-stu-id="c8c15-143">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="c8c15-144">O `FileSystemWatcher` de um arquivo de configuração pode disparar vários retornos de chamada de token para uma única alteração de arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="c8c15-144">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="c8c15-145">Para garantir que o código personalizado é executado somente depois que os vários retornos de chamada de token são acionados, a implementação da amostra verifica os hashes do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c8c15-145">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="c8c15-146">O exemplo usa o hash de arquivo SHA1.</span><span class="sxs-lookup"><span data-stu-id="c8c15-146">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="c8c15-147">Uma nova tentativa é implementada com uma retirada exponencial.</span><span class="sxs-lookup"><span data-stu-id="c8c15-147">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="c8c15-148">A nova tentativa está presente porque o bloqueio de arquivo pode ocorrer, o que impede temporariamente a computação de um novo hash em um arquivo.</span><span class="sxs-lookup"><span data-stu-id="c8c15-148">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="c8c15-149">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8c15-149">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="c8c15-150">Token de alteração de inicialização simples</span><span class="sxs-lookup"><span data-stu-id="c8c15-150">Simple startup change token</span></span>

<span data-ttu-id="c8c15-151">Registre um retorno de chamada `Action` do consumidor de token para notificações de alteração no token de recarregamento de configuração.</span><span class="sxs-lookup"><span data-stu-id="c8c15-151">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="c8c15-152">No `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c8c15-152">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="c8c15-153">`config.GetReloadToken()` fornece o token.</span><span class="sxs-lookup"><span data-stu-id="c8c15-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="c8c15-154">O retorno de chamada é o método `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="c8c15-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="c8c15-155">O `state` do retorno de chamada é usado para passar no `IHostingEnvironment`, que é útil para especificar o arquivo de configuração *appsettings* correto para monitorar (por exemplo, *appsettings.Development.JSON* quando estiver no Ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="c8c15-155">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="c8c15-156">Hashes de arquivo são usados para impedir que a instrução `WriteConsole` seja executada várias vezes, devido a vários retornos de chamada de token quando o arquivo de configuração é alterado somente uma vez.</span><span class="sxs-lookup"><span data-stu-id="c8c15-156">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="c8c15-157">Esse sistema é executado, desde que o aplicativo esteja em execução e não possa ser desabilitado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="c8c15-157">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="c8c15-158">Monitorar alterações de configuração como um serviço</span><span class="sxs-lookup"><span data-stu-id="c8c15-158">Monitor configuration changes as a service</span></span>

<span data-ttu-id="c8c15-159">A amostra implementa:</span><span class="sxs-lookup"><span data-stu-id="c8c15-159">The sample implements:</span></span>

* <span data-ttu-id="c8c15-160">Monitoramento de token de inicialização básica.</span><span class="sxs-lookup"><span data-stu-id="c8c15-160">Basic startup token monitoring.</span></span>
* <span data-ttu-id="c8c15-161">Monitoramento como serviço.</span><span class="sxs-lookup"><span data-stu-id="c8c15-161">Monitoring as a service.</span></span>
* <span data-ttu-id="c8c15-162">Um mecanismo para habilitar e desabilitar o monitoramento.</span><span class="sxs-lookup"><span data-stu-id="c8c15-162">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="c8c15-163">A amostra estabelece uma interface `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="c8c15-163">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="c8c15-164">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8c15-164">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="c8c15-165">O construtor da classe implementada, `ConfigurationMonitor`, registra um retorno de chamada para notificações de alteração:</span><span class="sxs-lookup"><span data-stu-id="c8c15-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="c8c15-166">`config.GetReloadToken()` fornece o token.</span><span class="sxs-lookup"><span data-stu-id="c8c15-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="c8c15-167">`InvokeChanged` é o método de retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="c8c15-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="c8c15-168">O `state` nesta instância é uma referência à instância `IConfigurationMonitor` que é usada para acessar o estado de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="c8c15-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="c8c15-169">Duas propriedades são usadas:</span><span class="sxs-lookup"><span data-stu-id="c8c15-169">Two properties are used:</span></span>

* <span data-ttu-id="c8c15-170">`MonitoringEnabled` &ndash; indica se o retorno de chamada deve executar seu código personalizado.</span><span class="sxs-lookup"><span data-stu-id="c8c15-170">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="c8c15-171">`CurrentState` &ndash; descreve o estado atual de monitoramento para uso na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="c8c15-171">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="c8c15-172">O método `InvokeChanged` é semelhante à abordagem anterior, exceto que ele:</span><span class="sxs-lookup"><span data-stu-id="c8c15-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="c8c15-173">Não executa o código, a menos que `MonitoringEnabled` seja `true`.</span><span class="sxs-lookup"><span data-stu-id="c8c15-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="c8c15-174">Gera saída do `state` atual em sua saída `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="c8c15-174">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="c8c15-175">Uma instância `ConfigurationMonitor` é registrada como um serviço em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c8c15-175">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="c8c15-176">A página Índice oferece ao usuário o controle sobre o monitoramento de configuração.</span><span class="sxs-lookup"><span data-stu-id="c8c15-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="c8c15-177">A instância de `IConfigurationMonitor` é injetada no `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="c8c15-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="c8c15-178">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8c15-178">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="c8c15-179">O monitor de configuração (`_monitor`) é usado para habilitar ou desabilitar o monitoramento e definir o estado atual de comentários de interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="c8c15-179">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="c8c15-180">Quando `OnPostStartMonitoring` é disparado, o monitoramento é habilitado e o estado atual é desmarcado.</span><span class="sxs-lookup"><span data-stu-id="c8c15-180">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="c8c15-181">Quando `OnPostStopMonitoring` é disparado, o monitoramento é desabilitado e o estado é definido para refletir que o monitoramento não está ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="c8c15-181">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="c8c15-182">Os botões na interface do usuário habilitam e desabilitar o monitoramento.</span><span class="sxs-lookup"><span data-stu-id="c8c15-182">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="c8c15-183">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c8c15-183">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="c8c15-184">Monitor alterações de arquivos armazenados em cache</span><span class="sxs-lookup"><span data-stu-id="c8c15-184">Monitor cached file changes</span></span>

<span data-ttu-id="c8c15-185">O conteúdo do arquivo pode ser armazenado em cache em memória usando <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="c8c15-185">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="c8c15-186">O cache na memória é descrito no tópico [Cache na memória](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="c8c15-186">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="c8c15-187">Sem realizar etapas adicionais, como a implementação descrita abaixo, dados *obsoletos* (desatualizados) são retornados de um cache se os dados de origem são alterados.</span><span class="sxs-lookup"><span data-stu-id="c8c15-187">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="c8c15-188">Por exemplo, não levando em conta o status de um arquivo de origem armazenado em cache durante a renovação de um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) resulta em dados de arquivo de cache obsoletos.</span><span class="sxs-lookup"><span data-stu-id="c8c15-188">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="c8c15-189">Cada solicitação de dados renova o período de expiração deslizante, mas o arquivo nunca é recarregado no cache.</span><span class="sxs-lookup"><span data-stu-id="c8c15-189">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="c8c15-190">Os recursos do aplicativo que usam o conteúdo armazenado em cache do arquivo estão sujeitos ao possível recebimento de conteúdo obsoleto.</span><span class="sxs-lookup"><span data-stu-id="c8c15-190">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="c8c15-191">O uso de tokens de alteração em um cenário de cache de arquivo impede a presença do conteúdo de arquivo obsoleto no cache.</span><span class="sxs-lookup"><span data-stu-id="c8c15-191">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="c8c15-192">O aplicativo de exemplo demonstra uma implementação da abordagem.</span><span class="sxs-lookup"><span data-stu-id="c8c15-192">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="c8c15-193">A amostra usa `GetFileContent` para:</span><span class="sxs-lookup"><span data-stu-id="c8c15-193">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="c8c15-194">Retornar o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c8c15-194">Return file content.</span></span>
* <span data-ttu-id="c8c15-195">Implementar um algoritmo de repetição com retirada exponencial para abranger casos em que um bloqueio de arquivo impede temporariamente a leitura do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c8c15-195">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="c8c15-196">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8c15-196">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="c8c15-197">Um `FileService` é criado para manipular pesquisas de arquivos armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="c8c15-197">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="c8c15-198">A chamada de método `GetFileContent` do serviço tenta obter o conteúdo do arquivo do cache em memória e retorná-lo para o chamador (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="c8c15-198">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="c8c15-199">Se o conteúdo armazenado em cache não é encontrado com a chave de cache, as seguintes ações são executadas:</span><span class="sxs-lookup"><span data-stu-id="c8c15-199">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="c8c15-200">O conteúdo do arquivo é obtido com `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="c8c15-200">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="c8c15-201">Um token de alteração é obtido do provedor de arquivo com [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="c8c15-201">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="c8c15-202">O retorno de chamada do token é disparado quando o arquivo é modificado.</span><span class="sxs-lookup"><span data-stu-id="c8c15-202">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="c8c15-203">O conteúdo do arquivo é armazenado em cache com um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="c8c15-203">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="c8c15-204">O token de alteração é anexado com [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para remover a entrada do cache se o arquivo é alterado enquanto ele é armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="c8c15-204">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="c8c15-205">No exemplo a seguir, os arquivos são armazenados na raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8c15-205">In the following example, files are stored in the app's content root.</span></span> <span data-ttu-id="c8c15-206">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) é usado para obter <xref:Microsoft.Extensions.FileProviders.IFileProvider> apontando para <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8c15-206">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="c8c15-207">O `filePath` é obtido com [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="c8c15-207">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="c8c15-208">O `FileService` é registrado no contêiner de serviço junto com o serviço de cache da memória.</span><span class="sxs-lookup"><span data-stu-id="c8c15-208">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="c8c15-209">No `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c8c15-209">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="c8c15-210">O modelo de página carrega o conteúdo do arquivo usando o serviço.</span><span class="sxs-lookup"><span data-stu-id="c8c15-210">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="c8c15-211">No método `OnGet` da página de índice (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="c8c15-211">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="c8c15-212">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="c8c15-212">CompositeChangeToken class</span></span>

<span data-ttu-id="c8c15-213">Para representar uma ou mais instâncias de `IChangeToken` em um único objeto, use a classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="c8c15-213">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="c8c15-214">`HasChanged` nos relatórios de token compostos `true` se um token representado `HasChanged` é `true`.</span><span class="sxs-lookup"><span data-stu-id="c8c15-214">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="c8c15-215">`ActiveChangeCallbacks` nos relatórios de token compostos `true` se um token representado `ActiveChangeCallbacks` é `true`.</span><span class="sxs-lookup"><span data-stu-id="c8c15-215">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="c8c15-216">Se ocorrerem vários eventos de alteração simultâneos, o retorno de chamada de alteração composto será invocado uma vez.</span><span class="sxs-lookup"><span data-stu-id="c8c15-216">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c8c15-217">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c8c15-217">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
