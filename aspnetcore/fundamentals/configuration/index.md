---
title: Configuração no ASP.NET Core
author: rick-anderson
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: b4fa082c5a53bc9ecb3c7b8ddcbf243ef0d94ba7
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989688"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="90997-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90997-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="90997-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="90997-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90997-105">A configuração no ASP.NET Core é executada usando um ou mais [provedores de configuração](#cp).</span><span class="sxs-lookup"><span data-stu-id="90997-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="90997-106">Os provedores de configuração lêem dados de configuração de pares chave-valor usando uma variedade de fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="90997-107">Arquivos de configurações, como *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="90997-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="90997-108">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-108">Environment variables</span></span>
* <span data-ttu-id="90997-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90997-109">Azure Key Vault</span></span>
* <span data-ttu-id="90997-110">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="90997-110">Azure App Configuration</span></span>
* <span data-ttu-id="90997-111">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="90997-111">Command-line arguments</span></span>
* <span data-ttu-id="90997-112">Provedores personalizados, instalados ou criados</span><span class="sxs-lookup"><span data-stu-id="90997-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="90997-113">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="90997-113">Directory files</span></span>
* <span data-ttu-id="90997-114">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="90997-114">In-memory .NET objects</span></span>

<span data-ttu-id="90997-115">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="90997-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="90997-116">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="90997-116">Default configuration</span></span>

<span data-ttu-id="90997-117">ASP.NET Core aplicativos Web criados com [dotnet novo](/dotnet/core/tools/dotnet-new) ou o Visual Studio geram o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="90997-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="90997-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="90997-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="90997-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adiciona um `IConfiguration` existente como uma origem.</span><span class="sxs-lookup"><span data-stu-id="90997-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="90997-120">No caso de configuração padrão, o adiciona a configuração do [host](#hvac) e a define como a primeira origem para a configuração do _aplicativo_ .</span><span class="sxs-lookup"><span data-stu-id="90997-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="90997-121">[appSettings. JSON](#appsettingsjson) usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="90997-122">*appSettings.* `Environment` *. JSON* usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="90997-123">Por exemplo, *appSettings*. ***Produção***. *JSON* e *appSettings*. ***Desenvolvimento***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="90997-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="90997-124">[Segredos do aplicativo](xref:security/app-secrets) quando o aplicativo é executado no ambiente de `Development`.</span><span class="sxs-lookup"><span data-stu-id="90997-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="90997-125">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="90997-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="90997-126">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-126">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="90997-127">Os provedores de configuração adicionados posteriormente substituem as configurações de chave anteriores.</span><span class="sxs-lookup"><span data-stu-id="90997-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="90997-128">Por exemplo, se `MyKey` for definido em *appSettings. JSON* e no ambiente, o valor de ambiente será usado.</span><span class="sxs-lookup"><span data-stu-id="90997-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="90997-129">Usando os provedores de configuração padrão, o [provedor de configuração de linha de comando](#command-line-configuration-provider) substitui todos os outros provedores.</span><span class="sxs-lookup"><span data-stu-id="90997-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="90997-130">Para obter mais informações sobre `CreateDefaultBuilder`, consulte [configurações padrão do Construtor](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="90997-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="90997-131">O código a seguir exibe os provedores de configuração habilitados na ordem em que foram adicionados:</span><span class="sxs-lookup"><span data-stu-id="90997-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="90997-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="90997-132">appsettings.json</span></span>

<span data-ttu-id="90997-133">Considere o seguinte arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="90997-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="90997-134">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="90997-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-135">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> padrão carrega a configuração na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="90997-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="90997-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="90997-136">*appsettings.json*</span></span>
1. <span data-ttu-id="90997-137">*appSettings.* `Environment` *. JSON* : Por exemplo, o *appSettings*. ***Produção***. *JSON* e *appSettings*. ***Desenvolvimento***. arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="90997-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="90997-138">A versão de ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="90997-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="90997-139">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="90997-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="90997-140">*appSettings*.`Environment`. valores *JSON* substituem chaves em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="90997-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="90997-141">Por exemplo, por padrão:</span><span class="sxs-lookup"><span data-stu-id="90997-141">For example, by default:</span></span>

* <span data-ttu-id="90997-142">Em desenvolvimento, *appSettings*. ***Desenvolvimento***. a configuração *JSON* substitui os valores encontrados em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="90997-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="90997-143">Em produção, *appSettings*. ***Produção***. a configuração *JSON* substitui os valores encontrados em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="90997-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="90997-144">Por exemplo, ao implantar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="90997-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="90997-145">Associar dados de configuração hierárquica usando o padrão de opções</span><span class="sxs-lookup"><span data-stu-id="90997-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="90997-146">A maneira preferida de ler valores de configuração relacionados é usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="90997-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="90997-147">Por exemplo, para ler os seguintes valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="90997-148">Crie a seguinte classe de `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="90997-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="90997-149">Todas as propriedades públicas de leitura/gravação do tipo estão associadas.</span><span class="sxs-lookup"><span data-stu-id="90997-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="90997-150">Os campos ***não*** estão associados.</span><span class="sxs-lookup"><span data-stu-id="90997-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="90997-151">O seguinte código:</span><span class="sxs-lookup"><span data-stu-id="90997-151">The following code:</span></span>

* <span data-ttu-id="90997-152">Chama [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) para associar a classe de `PositionOptions` à seção `Position`.</span><span class="sxs-lookup"><span data-stu-id="90997-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="90997-153">Exibe os dados de configuração do `Position`.</span><span class="sxs-lookup"><span data-stu-id="90997-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="90997-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="90997-155">`ConfigurationBinder.Get<T>` pode ser mais conveniente do que usar `ConfigurationBinder.Bind`.</span><span class="sxs-lookup"><span data-stu-id="90997-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="90997-156">O código a seguir mostra como usar `ConfigurationBinder.Get<T>` com a classe `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="90997-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-157">Uma abordagem alternativa ao usar o ***padrão de opções*** é associar a seção `Position` e adicioná-la ao [contêiner de serviço de injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="90997-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="90997-158">No código a seguir, `PositionOptions` é adicionado ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associado à configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="90997-159">Usando o código anterior, o código a seguir lê as opções de posição:</span><span class="sxs-lookup"><span data-stu-id="90997-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-160">Usando a configuração [padrão](#default) , os *arquivos appSettings. JSON* e *appSettings.* `Environment` *. JSON* são habilitados com [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="90997-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="90997-161">As alterações feitas no arquivo *appSettings. JSON* e *appsettings.* `Environment` *. JSON* ***depois*** que o aplicativo é iniciado são lidas pelo [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="90997-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="90997-162">Consulte [provedor de configuração JSON](#jcp) neste documento para obter informações sobre como adicionar arquivos de configuração JSON adicionais.</span><span class="sxs-lookup"><span data-stu-id="90997-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="90997-163">Gerenciador de segurança e segredo</span><span class="sxs-lookup"><span data-stu-id="90997-163">Security and secret manager</span></span>

<span data-ttu-id="90997-164">Diretrizes de dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="90997-165">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="90997-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="90997-166">O [Gerenciador de segredo](xref:security/app-secrets) pode ser usado para armazenar segredos no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="90997-167">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="90997-168">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="90997-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="90997-169">Por [padrão](#default), o [Gerenciador de segredo](xref:security/app-secrets) lê as definições de configuração após *appSettings. json* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="90997-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="90997-170">Para obter mais informações sobre como armazenar senhas ou outros dados confidenciais:</span><span class="sxs-lookup"><span data-stu-id="90997-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="90997-171"><xref:security/app-secrets>:  Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="90997-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="90997-172">O Gerenciador de segredo usa o [provedor de configuração de arquivo](#fcp) para armazenar segredos de usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="90997-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="90997-173">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="90997-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="90997-174">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="90997-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="90997-175">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-175">Environment variables</span></span>

<span data-ttu-id="90997-176">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração de pares chave-valor da variável de ambiente depois de ler *appSettings. JSON*, *appSettings.* `Environment` *. JSON*e o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="90997-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="90997-177">Portanto, os valores de chave lidos dos valores de substituição de ambiente lidos de *appSettings. JSON*, *appsettings.* `Environment` *. JSON*e o Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="90997-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="90997-178">Os seguintes comandos `set`:</span><span class="sxs-lookup"><span data-stu-id="90997-178">The following `set` commands:</span></span>

* <span data-ttu-id="90997-179">Defina as chaves de ambiente e os valores do [exemplo anterior](#appsettingsjson) no Windows.</span><span class="sxs-lookup"><span data-stu-id="90997-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="90997-180">Teste as configurações ao usar o [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="90997-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="90997-181">O comando `dotnet run` deve ser executado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="90997-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="90997-182">As configurações de ambiente anteriores:</span><span class="sxs-lookup"><span data-stu-id="90997-182">The preceding environment settings:</span></span>

* <span data-ttu-id="90997-183">São definidos apenas em processos iniciados na janela de comando em que foram definidos.</span><span class="sxs-lookup"><span data-stu-id="90997-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="90997-184">Não serão lidos por navegadores iniciados com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="90997-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="90997-185">Os comandos [setx](/windows-server/administration/windows-commands/setx) a seguir podem ser usados para definir as chaves de ambiente e os valores no Windows.</span><span class="sxs-lookup"><span data-stu-id="90997-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="90997-186">Ao contrário de `set`, as configurações de `setx` são persistidas.</span><span class="sxs-lookup"><span data-stu-id="90997-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="90997-187">`/M` define a variável no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="90997-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="90997-188">Se a opção `/M` não for usada, uma variável de ambiente do usuário será definida.</span><span class="sxs-lookup"><span data-stu-id="90997-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="90997-189">Para testar se os comandos anteriores substituem *appSettings. JSON* e *appsettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="90997-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="90997-190">Com o Visual Studio: Saia e reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="90997-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="90997-191">Com a CLI: Inicie uma nova janela de comando e insira `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="90997-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="90997-192">Chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> com uma cadeia de caracteres para especificar um prefixo para variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="90997-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="90997-193">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="90997-193">In the preceding code:</span></span>

* <span data-ttu-id="90997-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="90997-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="90997-195">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="90997-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="90997-196">Variáveis de ambiente definidas com o prefixo `MyCustomPrefix_` substituem os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="90997-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="90997-197">Isso inclui variáveis de ambiente sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="90997-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="90997-198">O prefixo é eliminado quando os pares chave-valor de configuração são lidos.</span><span class="sxs-lookup"><span data-stu-id="90997-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="90997-199">Os comandos a seguir testam o prefixo personalizado:</span><span class="sxs-lookup"><span data-stu-id="90997-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="90997-200">A [configuração padrão](#default) carrega variáveis de ambiente e argumentos de linha de comando prefixados com `DOTNET_` e `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="90997-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="90997-201">Os prefixos `DOTNET_` e `ASPNETCORE_` são usados por ASP.NET Core para a [configuração de host e aplicativo](xref:fundamentals/host/generic-host#host-configuration), mas não para a configuração do usuário.</span><span class="sxs-lookup"><span data-stu-id="90997-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="90997-202">Para obter mais informações sobre a configuração de host e aplicativo, consulte [host genérico .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="90997-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="90997-203">Em [Azure app serviço](https://azure.microsoft.com/services/app-service/), selecione **nova configuração de aplicativo** na página **Configurações > configuração** .</span><span class="sxs-lookup"><span data-stu-id="90997-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="90997-204">Azure App configurações do aplicativo de serviço são:</span><span class="sxs-lookup"><span data-stu-id="90997-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="90997-205">Criptografado em repouso e transmitido por um canal criptografado.</span><span class="sxs-lookup"><span data-stu-id="90997-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="90997-206">Exposto como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="90997-206">Exposed as environment variables.</span></span>

<span data-ttu-id="90997-207">Para obter mais informações, confira [Aplicativos Azure: Substituir a configuração do aplicativo no Portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="90997-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="90997-208">Consulte [prefixos de cadeia de conexão](#constr) para obter informações sobre cadeias de conexão do banco de dados</span><span class="sxs-lookup"><span data-stu-id="90997-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="90997-209">Linha de comando</span><span class="sxs-lookup"><span data-stu-id="90997-209">Command-line</span></span>

<span data-ttu-id="90997-210">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração de pares de chave-valor de argumento de linha de comando após as seguintes fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="90997-211">*appSettings. JSON* e *appSettings*.`Environment`. arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="90997-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="90997-212">[Segredos do aplicativo (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90997-213">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="90997-213">Environment variables.</span></span>

<span data-ttu-id="90997-214">Por [padrão](#default), os valores de configuração definidos nos valores de configuração de substituição de linha de comando são definidos com todos os outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="90997-215">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="90997-215">Command-line arguments</span></span>

<span data-ttu-id="90997-216">O comando a seguir define chaves e valores usando `=`:</span><span class="sxs-lookup"><span data-stu-id="90997-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="90997-217">O comando a seguir define chaves e valores usando `/`:</span><span class="sxs-lookup"><span data-stu-id="90997-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="90997-218">O comando a seguir define chaves e valores usando `--`:</span><span class="sxs-lookup"><span data-stu-id="90997-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="90997-219">O valor da chave:</span><span class="sxs-lookup"><span data-stu-id="90997-219">The key value:</span></span>

* <span data-ttu-id="90997-220">Deve seguir `=`ou a chave deve ter um prefixo de `--` ou `/` quando o valor segue um espaço.</span><span class="sxs-lookup"><span data-stu-id="90997-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="90997-221">Não será necessário se `=` for usado.</span><span class="sxs-lookup"><span data-stu-id="90997-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="90997-222">Por exemplo, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="90997-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="90997-223">No mesmo comando, não misture pares de chave-valor de argumento de linha de comando que usam `=` com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="90997-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="90997-224">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="90997-224">Switch mappings</span></span>

<span data-ttu-id="90997-225">Os mapeamentos de switch permitem a lógica de substituição de nome de **chave** .</span><span class="sxs-lookup"><span data-stu-id="90997-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="90997-226">Forneça um dicionário de substituições de switch para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="90997-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="90997-227">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="90997-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="90997-228">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="90997-229">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="90997-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="90997-230">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="90997-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="90997-231">Os comutadores devem começar com `-` ou `--`.</span><span class="sxs-lookup"><span data-stu-id="90997-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="90997-232">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="90997-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="90997-233">Para usar um dicionário de mapeamentos de opção, passe-o para a chamada para `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="90997-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="90997-234">O código a seguir mostra os valores de chave para as chaves substituídas:</span><span class="sxs-lookup"><span data-stu-id="90997-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-235">Execute o seguinte comando para testar a chave substituta:</span><span class="sxs-lookup"><span data-stu-id="90997-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="90997-236">Observação: Atualmente, `=` não pode ser usado para definir valores de substituição de chave com um único traço `-`.</span><span class="sxs-lookup"><span data-stu-id="90997-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="90997-237">Consulte [este problema do GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="90997-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="90997-238">O comando a seguir funciona para testar a substituição da chave:</span><span class="sxs-lookup"><span data-stu-id="90997-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="90997-239">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="90997-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="90997-240">A chamada `AddCommandLine` do método de `CreateDefaultBuilder` não inclui opções mapeadas, e não há como passar o dicionário switch-Mapping para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="90997-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90997-241">A solução não é passar os argumentos para `CreateDefaultBuilder` mas, em vez disso, permitir que o método de `AddCommandLine` do método `ConfigurationBuilder` processe os argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="90997-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="90997-242">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="90997-242">Hierarchical configuration data</span></span>

<span data-ttu-id="90997-243">A API de configuração do é ler dados de configuração hierárquicas mesclando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-243">The Configuration API is reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="90997-244">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="90997-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="90997-245">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações:</span><span class="sxs-lookup"><span data-stu-id="90997-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-246">A maneira preferida de ler dados de configuração hierárquicos é usar o padrão de opções.</span><span class="sxs-lookup"><span data-stu-id="90997-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="90997-247">Para obter mais informações, consulte [associar dados de configuração hierárquica](#optpat) neste documento.</span><span class="sxs-lookup"><span data-stu-id="90997-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="90997-248">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="90997-249">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="90997-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="90997-250">Chaves de configuração e valores</span><span class="sxs-lookup"><span data-stu-id="90997-250">Configuration keys and values</span></span>

<span data-ttu-id="90997-251">Chaves de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-251">Configuration keys:</span></span>

* <span data-ttu-id="90997-252">Não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="90997-252">Are case-insensitive.</span></span> <span data-ttu-id="90997-253">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="90997-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="90997-254">Se uma chave e um valor for definido em mais de um provedor de configuração, o valor do último provedor adicionado será usado.</span><span class="sxs-lookup"><span data-stu-id="90997-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="90997-255">Para obter mais informações, consulte [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="90997-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="90997-256">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="90997-256">Hierarchical keys</span></span>
  * <span data-ttu-id="90997-257">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="90997-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="90997-258">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="90997-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="90997-259">Um sublinhado duplo, `__`, tem suporte em todas as plataformas e é automaticamente convertido em dois-pontos `:`.</span><span class="sxs-lookup"><span data-stu-id="90997-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="90997-260">Em Azure Key Vault, as chaves hierárquicas usam `--` como um separador.</span><span class="sxs-lookup"><span data-stu-id="90997-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="90997-261">Escreva o código para substituir o `--` por um `:` quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-261">Write code to replace the `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="90997-262">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90997-263">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="90997-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="90997-264">Valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-264">Configuration values:</span></span>

* <span data-ttu-id="90997-265">São cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="90997-265">Are strings.</span></span>
* <span data-ttu-id="90997-266">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="90997-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="90997-267">Provedores de configuração</span><span class="sxs-lookup"><span data-stu-id="90997-267">Configuration providers</span></span>

<span data-ttu-id="90997-268">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="90997-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="90997-269">Provider</span><span class="sxs-lookup"><span data-stu-id="90997-269">Provider</span></span> | <span data-ttu-id="90997-270">Fornece a configuração de</span><span class="sxs-lookup"><span data-stu-id="90997-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="90997-271">Provedor de configuração do Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90997-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="90997-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90997-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="90997-273">Provedor de configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="90997-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="90997-274">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="90997-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="90997-275">Provedor de configuração de linha de comando</span><span class="sxs-lookup"><span data-stu-id="90997-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="90997-276">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="90997-276">Command-line parameters</span></span> |
| [<span data-ttu-id="90997-277">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="90997-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="90997-278">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="90997-278">Custom source</span></span> |
| [<span data-ttu-id="90997-279">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="90997-280">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-280">Environment variables</span></span> |
| [<span data-ttu-id="90997-281">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="90997-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="90997-282">Arquivos INI, JSON e XML</span><span class="sxs-lookup"><span data-stu-id="90997-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="90997-283">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="90997-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="90997-284">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="90997-284">Directory files</span></span> |
| [<span data-ttu-id="90997-285">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="90997-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="90997-286">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="90997-286">In-memory collections</span></span> |
| [<span data-ttu-id="90997-287">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="90997-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="90997-288">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="90997-288">File in the user profile directory</span></span> |

<span data-ttu-id="90997-289">As fontes de configuração são lidas na ordem em que seus provedores de configuração são especificados.</span><span class="sxs-lookup"><span data-stu-id="90997-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="90997-290">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="90997-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="90997-291">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="90997-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="90997-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="90997-292">*appsettings.json*</span></span>
1. <span data-ttu-id="90997-293">*appSettings*.`Environment`. *JSON*</span><span class="sxs-lookup"><span data-stu-id="90997-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="90997-294">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="90997-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="90997-295">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="90997-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="90997-296">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="90997-297">Uma prática comum é adicionar o provedor de configuração de linha de comando pela última vez em uma série de provedores para permitir que argumentos de linha de comando substituam a configuração definida pelos outros provedores.</span><span class="sxs-lookup"><span data-stu-id="90997-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="90997-298">A sequência anterior de provedores é usada na [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="90997-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="90997-299">Prefixos de cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="90997-299">Connection string prefixes</span></span>

<span data-ttu-id="90997-300">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="90997-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="90997-301">Essas cadeias de conexão estão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="90997-302">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo com a [configuração padrão](#default) ou quando nenhum prefixo é fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="90997-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="90997-303">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="90997-303">Connection string prefix</span></span> | <span data-ttu-id="90997-304">Provider</span><span class="sxs-lookup"><span data-stu-id="90997-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="90997-305">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="90997-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="90997-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="90997-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="90997-307">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="90997-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="90997-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="90997-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="90997-309">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="90997-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="90997-310">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="90997-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="90997-311">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="90997-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="90997-312">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-312">Environment variable key</span></span> | <span data-ttu-id="90997-313">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="90997-313">Converted configuration key</span></span> | <span data-ttu-id="90997-314">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="90997-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90997-315">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="90997-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90997-316">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90997-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90997-317">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="90997-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90997-318">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90997-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90997-319">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90997-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90997-320">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90997-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90997-321">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90997-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="90997-322">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="90997-322">JSON configuration provider</span></span>

<span data-ttu-id="90997-323">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor de arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="90997-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="90997-324">Sobrecargas podem especificar:</span><span class="sxs-lookup"><span data-stu-id="90997-324">Overloads can specify:</span></span>

* <span data-ttu-id="90997-325">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="90997-325">Whether the file is optional.</span></span>
* <span data-ttu-id="90997-326">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="90997-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="90997-327">Considere o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="90997-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="90997-328">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="90997-328">The preceding code:</span></span>

* <span data-ttu-id="90997-329">Configura o provedor de configuração JSON para carregar o arquivo *myconfig. JSON* com as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="90997-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="90997-330">`optional: true`: O arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="90997-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="90997-331">`reloadOnChange: true` : O arquivo é recarregado quando as alterações são salvas.</span><span class="sxs-lookup"><span data-stu-id="90997-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="90997-332">Lê os [provedores de configuração padrão](#default) antes do arquivo *myconfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="90997-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="90997-333">As configurações na configuração de substituição do arquivo *myconfig. JSON* nos provedores de configuração padrão, incluindo o [provedor de configuração de variáveis de ambiente](#evcp) e o provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="90997-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90997-334">Normalmente, você ***não*** deseja que um arquivo JSON personalizado substitua valores definidos no [provedor de configuração de variáveis de ambiente](#evcp) e no provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="90997-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90997-335">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="90997-336">No código anterior, as configurações em *myconfig. JSON* e *myconfig*.`Environment`. arquivos *JSON* :</span><span class="sxs-lookup"><span data-stu-id="90997-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="90997-337">Substitua as configurações em *appSettings. JSON* e *appSettings*.`Environment`. arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="90997-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="90997-338">São substituídos por configurações no [provedor de configuração de variáveis de ambiente](#evcp) e no [provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="90997-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90997-339">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *myconfig. JSON* :</span><span class="sxs-lookup"><span data-stu-id="90997-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="90997-340">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="90997-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="90997-341">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="90997-341">File configuration provider</span></span>

<span data-ttu-id="90997-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="90997-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="90997-343">Os provedores de configuração a seguir derivam de `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="90997-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="90997-344">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="90997-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="90997-345">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="90997-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="90997-346">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="90997-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="90997-347">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="90997-347">INI configuration provider</span></span>

<span data-ttu-id="90997-348">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="90997-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="90997-349">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="90997-350">No código anterior, as configurações no *MyIniConfig. ini* e no *MyIniConfig*.`Environment`. os arquivos *ini* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="90997-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="90997-351">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="90997-352">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="90997-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90997-353">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="90997-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="90997-354">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="90997-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="90997-355">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="90997-355">XML configuration provider</span></span>

<span data-ttu-id="90997-356">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="90997-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="90997-357">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="90997-358">No código anterior, as configurações em *MyXMLFile. xml* e *MyXMLFile*.`Environment`. os arquivos *XML* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="90997-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="90997-359">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="90997-360">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="90997-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="90997-361">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo *MyXMLFile. xml* :</span><span class="sxs-lookup"><span data-stu-id="90997-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="90997-362">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="90997-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-363">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="90997-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="90997-364">O código a seguir lê o arquivo de configuração anterior e exibe as chaves e valores:</span><span class="sxs-lookup"><span data-stu-id="90997-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-365">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="90997-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="90997-366">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="90997-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90997-367">key:attribute</span><span class="sxs-lookup"><span data-stu-id="90997-367">key:attribute</span></span>
* <span data-ttu-id="90997-368">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="90997-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="90997-369">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="90997-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="90997-370">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="90997-371">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-371">The key is the file name.</span></span> <span data-ttu-id="90997-372">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-372">The value contains the file's contents.</span></span> <span data-ttu-id="90997-373">O provedor de configuração de chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="90997-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="90997-374">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="90997-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="90997-375">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="90997-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="90997-376">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="90997-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="90997-377">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="90997-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="90997-378">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="90997-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="90997-379">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="90997-380">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="90997-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="90997-381">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="90997-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="90997-382">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="90997-382">Memory configuration provider</span></span>

<span data-ttu-id="90997-383">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="90997-384">O código a seguir adiciona uma coleção de memória ao sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="90997-385">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe as configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="90997-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-386">No código anterior, `config.AddInMemoryCollection(Dict)` é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="90997-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="90997-387">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="90997-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="90997-388">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="90997-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="90997-389">Consulte [associar uma matriz](#boa) para outro exemplo usando `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="90997-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="90997-390">GetValue</span><span class="sxs-lookup"><span data-stu-id="90997-390">GetValue</span></span>

<span data-ttu-id="90997-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e a converte no tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="90997-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-392">No código anterior, se `NumberKey` não for encontrado na configuração, o valor padrão de `99` será usado.</span><span class="sxs-lookup"><span data-stu-id="90997-392">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="90997-393">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="90997-393">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="90997-394">Para os exemplos a seguir, considere o seguinte arquivo *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="90997-394">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="90997-395">O código a seguir adiciona *MySubsection. JSON* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-395">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="90997-396">GetSection</span><span class="sxs-lookup"><span data-stu-id="90997-396">GetSection</span></span>

<span data-ttu-id="90997-397">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retorna uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="90997-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="90997-398">O código a seguir retorna valores para `section1`:</span><span class="sxs-lookup"><span data-stu-id="90997-398">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-399">O código a seguir retorna valores para `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="90997-399">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-400">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="90997-400">`GetSection` never returns `null`.</span></span> <span data-ttu-id="90997-401">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="90997-401">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="90997-402">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="90997-402">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="90997-403"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="90997-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="90997-404">GetChildren e existe</span><span class="sxs-lookup"><span data-stu-id="90997-404">GetChildren and Exists</span></span>

<span data-ttu-id="90997-405">O código a seguir chama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e retorna valores para `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="90997-405">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-406">O código anterior chama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para verificar se a seção existe:</span><span class="sxs-lookup"><span data-stu-id="90997-406">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="90997-407">Associar uma matriz</span><span class="sxs-lookup"><span data-stu-id="90997-407">Bind an array</span></span>

<span data-ttu-id="90997-408">O [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) dá suporte a matrizes de associação a objetos usando índices de matriz em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-408">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90997-409">Qualquer formato de matriz que expõe um segmento de chave numérica é capaz de associar a matriz a uma matriz de classe [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="90997-409">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="90997-410">Considere o *myArray. JSON* do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="90997-410">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="90997-411">O código a seguir adiciona *myArray. JSON* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-411">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="90997-412">O código a seguir lê a configuração e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="90997-412">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-413">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="90997-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="90997-414">Na saída anterior, o índice 3 tem o valor `value40`, correspondente a `"4": "value40",` no *myArray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="90997-414">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="90997-415">Os índices de matriz associados são contínuos e não associados ao índice de chave de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-415">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="90997-416">O associador de configuração não é capaz de ligar valores nulos ou criar entradas nulas em objetos associados</span><span class="sxs-lookup"><span data-stu-id="90997-416">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="90997-417">O código a seguir carrega a configuração de `array:entries` com o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="90997-417">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="90997-418">O código a seguir lê a configuração no `Dictionary` de `arrayDict` e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="90997-418">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-419">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="90997-419">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="90997-420">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="90997-420">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="90997-421">Quando os dados de configuração que contêm uma matriz são associados, os índices de matriz nas chaves de configuração são usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="90997-421">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="90997-422">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="90997-422">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="90997-423">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância de `ArrayExample` por qualquer provedor de configuração que leia o índice &num;3 chave/valor par.</span><span class="sxs-lookup"><span data-stu-id="90997-423">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="90997-424">Considere o seguinte arquivo *Value3. JSON* do download de exemplo:</span><span class="sxs-lookup"><span data-stu-id="90997-424">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="90997-425">O código a seguir inclui a configuração para *Value3. JSON* e o `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="90997-425">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="90997-426">O código a seguir lê a configuração anterior e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="90997-426">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="90997-427">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="90997-427">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="90997-428">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="90997-428">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="90997-429">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="90997-429">Custom configuration provider</span></span>

<span data-ttu-id="90997-430">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="90997-430">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="90997-431">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="90997-431">The provider has the following characteristics:</span></span>

* <span data-ttu-id="90997-432">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="90997-432">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="90997-433">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-433">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="90997-434">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="90997-434">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="90997-435">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="90997-435">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="90997-436">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-436">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="90997-437">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="90997-437">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="90997-438">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-438">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="90997-439">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="90997-439">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="90997-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="90997-441">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="90997-441">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="90997-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="90997-443">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="90997-443">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="90997-444">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="90997-444">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="90997-445">Como [as chaves de configuração](#keys)não diferenciam maiúsculas de minúsculas, o dicionário usado para inicializar o banco de dados é criado com o comparador que não diferencia maiúsculas de minúsculas ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="90997-445">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="90997-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="90997-447">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="90997-447">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="90997-448">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-448">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="90997-449">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-449">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="90997-450">Configuração de acesso na inicialização</span><span class="sxs-lookup"><span data-stu-id="90997-450">Access configuration in Startup</span></span>

<span data-ttu-id="90997-451">O código a seguir exibe os dados de configuração nos métodos `Startup`:</span><span class="sxs-lookup"><span data-stu-id="90997-451">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="90997-452">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, confira [Inicialização do aplicativo: Métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="90997-452">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="90997-453">Configuração de acesso no Razor Pages</span><span class="sxs-lookup"><span data-stu-id="90997-453">Access configuration in Razor Pages</span></span>

<span data-ttu-id="90997-454">O código a seguir exibe os dados de configuração em uma página Razor:</span><span class="sxs-lookup"><span data-stu-id="90997-454">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="90997-455">Acessar a configuração em um arquivo de exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="90997-455">Access configuration in a MVC view file</span></span>

<span data-ttu-id="90997-456">O código a seguir exibe os dados de configuração em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="90997-456">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="90997-457">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="90997-457">Host versus app configuration</span></span>

<span data-ttu-id="90997-458">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="90997-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="90997-459">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="90997-460">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="90997-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="90997-461">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="90997-462">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="90997-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="90997-463">Configuração de host padrão</span><span class="sxs-lookup"><span data-stu-id="90997-463">Default host configuration</span></span>

<span data-ttu-id="90997-464">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="90997-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="90997-465">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="90997-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="90997-466">Variáveis de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT`) usando o [provedor de configuração de variáveis de ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="90997-467">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="90997-468">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="90997-469">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="90997-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="90997-470">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="90997-471">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="90997-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="90997-472">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="90997-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="90997-473">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="90997-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="90997-474">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="90997-474">Other configuration</span></span>

<span data-ttu-id="90997-475">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="90997-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="90997-476">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="90997-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="90997-477">*Launch. json*/*launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="90997-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="90997-478">Em <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="90997-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="90997-479">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="90997-480">o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="90997-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="90997-481">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="90997-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="90997-482">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="90997-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="90997-483">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="90997-484">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="90997-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90997-485">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="90997-485">Additional resources</span></span>

* [<span data-ttu-id="90997-486">Código-fonte de configuração</span><span class="sxs-lookup"><span data-stu-id="90997-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90997-487">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="90997-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="90997-488">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="90997-489">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90997-489">Azure Key Vault</span></span>
* <span data-ttu-id="90997-490">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="90997-490">Azure App Configuration</span></span>
* <span data-ttu-id="90997-491">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="90997-491">Command-line arguments</span></span>
* <span data-ttu-id="90997-492">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="90997-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="90997-493">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="90997-493">Directory files</span></span>
* <span data-ttu-id="90997-494">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-494">Environment variables</span></span>
* <span data-ttu-id="90997-495">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="90997-495">In-memory .NET objects</span></span>
* <span data-ttu-id="90997-496">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="90997-496">Settings files</span></span>

<span data-ttu-id="90997-497">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="90997-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="90997-498">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="90997-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="90997-499">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="90997-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="90997-500">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="90997-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="90997-501">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="90997-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="90997-502">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="90997-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="90997-503">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="90997-503">Host versus app configuration</span></span>

<span data-ttu-id="90997-504">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="90997-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="90997-505">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="90997-506">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="90997-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="90997-507">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="90997-508">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="90997-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="90997-509">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="90997-509">Other configuration</span></span>

<span data-ttu-id="90997-510">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="90997-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="90997-511">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="90997-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="90997-512">*Launch. json*/*launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="90997-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="90997-513">Em <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="90997-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="90997-514">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="90997-515">o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="90997-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="90997-516">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="90997-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="90997-517">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="90997-517">Default configuration</span></span>

<span data-ttu-id="90997-518">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="90997-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="90997-519">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="90997-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="90997-520">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="90997-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="90997-521">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="90997-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="90997-522">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="90997-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="90997-523">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="90997-524">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="90997-525">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="90997-526">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="90997-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="90997-527">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="90997-528">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="90997-529">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="90997-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="90997-530">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="90997-531">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="90997-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="90997-532">Segurança</span><span class="sxs-lookup"><span data-stu-id="90997-532">Security</span></span>

<span data-ttu-id="90997-533">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="90997-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="90997-534">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="90997-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="90997-535">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="90997-536">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="90997-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="90997-537">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="90997-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="90997-538"><xref:security/app-secrets> &ndash; inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="90997-538"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="90997-539">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="90997-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="90997-540">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="90997-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="90997-541">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="90997-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="90997-542">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="90997-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="90997-543">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="90997-543">Hierarchical configuration data</span></span>

<span data-ttu-id="90997-544">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="90997-545">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="90997-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="90997-546">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="90997-547">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="90997-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="90997-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90997-548">section0:key0</span></span>
* <span data-ttu-id="90997-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90997-549">section0:key1</span></span>
* <span data-ttu-id="90997-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="90997-550">section1:key0</span></span>
* <span data-ttu-id="90997-551">section1:key1</span><span class="sxs-lookup"><span data-stu-id="90997-551">section1:key1</span></span>

<span data-ttu-id="90997-552">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="90997-553">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="90997-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="90997-554">Convenções</span><span class="sxs-lookup"><span data-stu-id="90997-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="90997-555">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="90997-555">Sources and providers</span></span>

<span data-ttu-id="90997-556">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="90997-557">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="90997-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="90997-558">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="90997-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="90997-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="90997-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetada em um Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.</span><span class="sxs-lookup"><span data-stu-id="90997-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="90997-561">Nos exemplos a seguir, o campo `_config` é usado para acessar os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="90997-562">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="90997-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="90997-563">Chaves</span><span class="sxs-lookup"><span data-stu-id="90997-563">Keys</span></span>

<span data-ttu-id="90997-564">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="90997-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="90997-565">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="90997-565">Keys are case-insensitive.</span></span> <span data-ttu-id="90997-566">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="90997-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="90997-567">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="90997-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="90997-568">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="90997-568">Hierarchical keys</span></span>
  * <span data-ttu-id="90997-569">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="90997-569">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="90997-570">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="90997-570">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="90997-571">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="90997-571">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="90997-572">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="90997-572">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="90997-573">Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-573">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="90997-574">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-574">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90997-575">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="90997-575">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="90997-576">Valores</span><span class="sxs-lookup"><span data-stu-id="90997-576">Values</span></span>

<span data-ttu-id="90997-577">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="90997-577">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="90997-578">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="90997-578">Values are strings.</span></span>
* <span data-ttu-id="90997-579">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="90997-579">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="90997-580">Provedores</span><span class="sxs-lookup"><span data-stu-id="90997-580">Providers</span></span>

<span data-ttu-id="90997-581">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="90997-581">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="90997-582">Provider</span><span class="sxs-lookup"><span data-stu-id="90997-582">Provider</span></span> | <span data-ttu-id="90997-583">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="90997-583">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="90997-584">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="90997-584">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="90997-585">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90997-585">Azure Key Vault</span></span> |
| <span data-ttu-id="90997-586">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="90997-586">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="90997-587">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="90997-587">Azure App Configuration</span></span> |
| [<span data-ttu-id="90997-588">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="90997-588">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="90997-589">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="90997-589">Command-line parameters</span></span> |
| [<span data-ttu-id="90997-590">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="90997-590">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="90997-591">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="90997-591">Custom source</span></span> |
| [<span data-ttu-id="90997-592">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-592">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="90997-593">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-593">Environment variables</span></span> |
| [<span data-ttu-id="90997-594">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="90997-594">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="90997-595">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="90997-595">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="90997-596">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="90997-596">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="90997-597">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="90997-597">Directory files</span></span> |
| [<span data-ttu-id="90997-598">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="90997-598">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="90997-599">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="90997-599">In-memory collections</span></span> |
| <span data-ttu-id="90997-600">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="90997-600">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="90997-601">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="90997-601">File in the user profile directory</span></span> |

<span data-ttu-id="90997-602">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-602">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="90997-603">Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza.</span><span class="sxs-lookup"><span data-stu-id="90997-603">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="90997-604">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="90997-604">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="90997-605">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="90997-605">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="90997-606">Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="90997-606">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="90997-607">Cofre da Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="90997-607">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="90997-608">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="90997-608">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="90997-609">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-609">Environment variables</span></span>
1. <span data-ttu-id="90997-610">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="90997-610">Command-line arguments</span></span>

<span data-ttu-id="90997-611">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="90997-611">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="90997-612">A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="90997-612">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90997-613">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="90997-613">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="90997-614">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="90997-614">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="90997-615">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-615">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="90997-616">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="90997-616">ConfigureAppConfiguration</span></span>

<span data-ttu-id="90997-617">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="90997-617">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="90997-618">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="90997-618">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="90997-619">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="90997-619">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="90997-620">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="90997-620">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="90997-621">Para remover os provedores adicionados por `CreateDefaultBuilder`, chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="90997-621">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="90997-622">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="90997-622">Consume configuration during app startup</span></span>

<span data-ttu-id="90997-623">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="90997-623">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="90997-624">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="90997-624">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="90997-625">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="90997-625">Command-line Configuration Provider</span></span>

<span data-ttu-id="90997-626">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.</span><span class="sxs-lookup"><span data-stu-id="90997-626">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="90997-627">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="90997-627">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90997-628">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="90997-628">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="90997-629">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="90997-629">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90997-630">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="90997-630">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="90997-631">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="90997-631">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="90997-632">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-632">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90997-633">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="90997-633">Environment variables.</span></span>

<span data-ttu-id="90997-634">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="90997-634">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="90997-635">Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="90997-635">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="90997-636">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="90997-636">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="90997-637">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="90997-637">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="90997-638">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="90997-638">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90997-639">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="90997-639">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="90997-640">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="90997-640">**Example**</span></span>

<span data-ttu-id="90997-641">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="90997-641">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="90997-642">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="90997-642">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="90997-643">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="90997-643">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="90997-644">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="90997-644">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="90997-645">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="90997-645">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="90997-646">Argumentos</span><span class="sxs-lookup"><span data-stu-id="90997-646">Arguments</span></span>

<span data-ttu-id="90997-647">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="90997-647">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="90997-648">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="90997-648">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="90997-649">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="90997-649">Key prefix</span></span>               | <span data-ttu-id="90997-650">{1&gt;Exemplo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="90997-650">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="90997-651">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="90997-651">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="90997-652">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="90997-652">Two dashes (`--`)</span></span>        | <span data-ttu-id="90997-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="90997-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="90997-654">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="90997-654">Forward slash (`/`)</span></span>      | <span data-ttu-id="90997-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="90997-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="90997-656">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="90997-656">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="90997-657">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="90997-657">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="90997-658">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="90997-658">Switch mappings</span></span>

<span data-ttu-id="90997-659">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="90997-659">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="90997-660">Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, forneça um dicionário de substituições de switch para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="90997-660">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="90997-661">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="90997-661">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="90997-662">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-662">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="90997-663">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="90997-663">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="90997-664">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="90997-664">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="90997-665">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="90997-665">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="90997-666">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="90997-666">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="90997-667">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="90997-667">Create a switch mappings dictionary.</span></span> <span data-ttu-id="90997-668">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="90997-668">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="90997-669">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="90997-669">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="90997-670">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="90997-670">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="90997-671">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="90997-671">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90997-672">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="90997-672">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="90997-673">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="90997-673">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="90997-674">Chave</span><span class="sxs-lookup"><span data-stu-id="90997-674">Key</span></span>       | <span data-ttu-id="90997-675">Valor</span><span class="sxs-lookup"><span data-stu-id="90997-675">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="90997-676">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="90997-676">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="90997-677">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="90997-677">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="90997-678">Chave</span><span class="sxs-lookup"><span data-stu-id="90997-678">Key</span></span>               | <span data-ttu-id="90997-679">Valor</span><span class="sxs-lookup"><span data-stu-id="90997-679">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="90997-680">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-680">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="90997-681">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.</span><span class="sxs-lookup"><span data-stu-id="90997-681">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="90997-682">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="90997-682">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="90997-683">[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="90997-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="90997-684">Para obter mais informações, confira [Aplicativos Azure: Substituir a configuração do aplicativo no Portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="90997-684">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="90997-685">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="90997-685">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="90997-686">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="90997-686">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90997-687">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="90997-687">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="90997-688">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="90997-688">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="90997-689">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="90997-689">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="90997-690">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-690">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90997-691">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="90997-691">Command-line arguments.</span></span>

<span data-ttu-id="90997-692">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="90997-692">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="90997-693">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="90997-693">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="90997-694">Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:</span><span class="sxs-lookup"><span data-stu-id="90997-694">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="90997-695">Chame `AddEnvironmentVariables` último para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.</span><span class="sxs-lookup"><span data-stu-id="90997-695">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="90997-696">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="90997-696">**Example**</span></span>

<span data-ttu-id="90997-697">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="90997-697">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="90997-698">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="90997-698">Run the sample app.</span></span> <span data-ttu-id="90997-699">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="90997-699">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="90997-700">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="90997-700">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="90997-701">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="90997-701">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="90997-702">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="90997-702">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="90997-703">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="90997-703">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="90997-704">Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="90997-704">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="90997-705">Prefixos</span><span class="sxs-lookup"><span data-stu-id="90997-705">Prefixes</span></span>

<span data-ttu-id="90997-706">As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="90997-706">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="90997-707">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-707">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="90997-708">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-708">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="90997-709">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="90997-709">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="90997-710">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="90997-710">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90997-711">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="90997-711">**Connection string prefixes**</span></span>

<span data-ttu-id="90997-712">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-712">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="90997-713">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="90997-713">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="90997-714">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="90997-714">Connection string prefix</span></span> | <span data-ttu-id="90997-715">Provider</span><span class="sxs-lookup"><span data-stu-id="90997-715">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="90997-716">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="90997-716">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="90997-717">MySQL</span><span class="sxs-lookup"><span data-stu-id="90997-717">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="90997-718">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="90997-718">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="90997-719">SQL Server</span><span class="sxs-lookup"><span data-stu-id="90997-719">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="90997-720">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="90997-720">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="90997-721">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="90997-721">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="90997-722">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="90997-722">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="90997-723">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="90997-723">Environment variable key</span></span> | <span data-ttu-id="90997-724">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="90997-724">Converted configuration key</span></span> | <span data-ttu-id="90997-725">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="90997-725">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90997-726">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="90997-726">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90997-727">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90997-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90997-728">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="90997-728">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90997-729">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90997-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90997-730">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90997-730">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="90997-731">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="90997-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="90997-732">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="90997-732">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="90997-733">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="90997-733">**Example**</span></span>

<span data-ttu-id="90997-734">Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:</span><span class="sxs-lookup"><span data-stu-id="90997-734">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="90997-735">Nome &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="90997-735">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="90997-736">Valor &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="90997-736">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="90997-737">Se `IConfiguration` for injetada e atribuída a um campo chamado `_config`, leia o valor:</span><span class="sxs-lookup"><span data-stu-id="90997-737">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="90997-738">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="90997-738">File Configuration Provider</span></span>

<span data-ttu-id="90997-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="90997-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="90997-740">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="90997-740">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="90997-741">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="90997-741">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="90997-742">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="90997-742">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="90997-743">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="90997-743">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="90997-744">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="90997-744">INI Configuration Provider</span></span>

<span data-ttu-id="90997-745">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="90997-745">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="90997-746">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="90997-746">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90997-747">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="90997-747">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="90997-748">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="90997-748">Overloads permit specifying:</span></span>

* <span data-ttu-id="90997-749">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="90997-749">Whether the file is optional.</span></span>
* <span data-ttu-id="90997-750">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="90997-750">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="90997-751">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-751">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="90997-752">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="90997-752">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="90997-753">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="90997-753">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="90997-754">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="90997-754">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90997-755">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90997-755">section0:key0</span></span>
* <span data-ttu-id="90997-756">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90997-756">section0:key1</span></span>
* <span data-ttu-id="90997-757">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="90997-757">section1:subsection:key</span></span>
* <span data-ttu-id="90997-758">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="90997-758">section2:subsection0:key</span></span>
* <span data-ttu-id="90997-759">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="90997-759">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="90997-760">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="90997-760">JSON Configuration Provider</span></span>

<span data-ttu-id="90997-761">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.</span><span class="sxs-lookup"><span data-stu-id="90997-761">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="90997-762">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="90997-762">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90997-763">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="90997-763">Overloads permit specifying:</span></span>

* <span data-ttu-id="90997-764">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="90997-764">Whether the file is optional.</span></span>
* <span data-ttu-id="90997-765">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="90997-765">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="90997-766">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-766">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="90997-767">`AddJsonFile` é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="90997-767">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90997-768">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="90997-768">The method is called to load configuration from:</span></span>

* <span data-ttu-id="90997-769">*appSettings. json* &ndash; este arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="90997-769">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="90997-770">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="90997-770">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="90997-771">*appSettings. {Environment}. JSON* &ndash; a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="90997-771">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="90997-772">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="90997-772">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="90997-773">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="90997-773">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="90997-774">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="90997-774">Environment variables.</span></span>
* <span data-ttu-id="90997-775">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-775">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="90997-776">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="90997-776">Command-line arguments.</span></span>

<span data-ttu-id="90997-777">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="90997-777">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="90997-778">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="90997-778">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="90997-779">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="90997-779">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="90997-780">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="90997-780">**Example**</span></span>

<span data-ttu-id="90997-781">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="90997-781">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="90997-782">A primeira chamada para `AddJsonFile` carrega a configuração de *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="90997-782">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="90997-783">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="90997-783">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="90997-784">Para *appSettings. Development. JSON* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="90997-784">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="90997-785">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="90997-785">Run the sample app.</span></span> <span data-ttu-id="90997-786">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="90997-786">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="90997-787">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-787">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="90997-788">O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="90997-788">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="90997-789">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="90997-789">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="90997-790">Abra o arquivo *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="90997-790">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="90997-791">No perfil de `ConfigurationSample`, altere o valor da variável de ambiente `ASPNETCORE_ENVIRONMENT` para `Production`.</span><span class="sxs-lookup"><span data-stu-id="90997-791">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="90997-792">Salve o arquivo e execute o aplicativo com `dotnet run` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="90997-792">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="90997-793">As configurações em *appSettings. O Development. JSON* não substitui mais as configurações em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="90997-793">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="90997-794">O nível de log para a `Logging:LogLevel:Default` de chave é `Warning`.</span><span class="sxs-lookup"><span data-stu-id="90997-794">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="90997-795">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="90997-795">XML Configuration Provider</span></span>

<span data-ttu-id="90997-796">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="90997-796">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="90997-797">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="90997-797">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90997-798">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="90997-798">Overloads permit specifying:</span></span>

* <span data-ttu-id="90997-799">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="90997-799">Whether the file is optional.</span></span>
* <span data-ttu-id="90997-800">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="90997-800">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="90997-801">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-801">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="90997-802">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="90997-802">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="90997-803">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-803">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="90997-804">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="90997-804">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="90997-805">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="90997-805">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="90997-806">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="90997-806">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90997-807">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90997-807">section0:key0</span></span>
* <span data-ttu-id="90997-808">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90997-808">section0:key1</span></span>
* <span data-ttu-id="90997-809">section1:key0</span><span class="sxs-lookup"><span data-stu-id="90997-809">section1:key0</span></span>
* <span data-ttu-id="90997-810">section1:key1</span><span class="sxs-lookup"><span data-stu-id="90997-810">section1:key1</span></span>

<span data-ttu-id="90997-811">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="90997-811">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="90997-812">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="90997-812">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90997-813">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="90997-813">section:section0:key:key0</span></span>
* <span data-ttu-id="90997-814">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="90997-814">section:section0:key:key1</span></span>
* <span data-ttu-id="90997-815">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="90997-815">section:section1:key:key0</span></span>
* <span data-ttu-id="90997-816">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="90997-816">section:section1:key:key1</span></span>

<span data-ttu-id="90997-817">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="90997-817">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="90997-818">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="90997-818">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="90997-819">key:attribute</span><span class="sxs-lookup"><span data-stu-id="90997-819">key:attribute</span></span>
* <span data-ttu-id="90997-820">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="90997-820">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="90997-821">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="90997-821">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="90997-822">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-822">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="90997-823">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-823">The key is the file name.</span></span> <span data-ttu-id="90997-824">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-824">The value contains the file's contents.</span></span> <span data-ttu-id="90997-825">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="90997-825">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="90997-826">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="90997-826">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="90997-827">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="90997-827">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="90997-828">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="90997-828">Overloads permit specifying:</span></span>

* <span data-ttu-id="90997-829">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="90997-829">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="90997-830">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="90997-830">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="90997-831">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="90997-831">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="90997-832">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="90997-832">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="90997-833">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="90997-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="90997-834">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="90997-834">Memory Configuration Provider</span></span>

<span data-ttu-id="90997-835">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-835">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="90997-836">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="90997-836">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="90997-837">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="90997-837">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="90997-838">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-838">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="90997-839">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="90997-839">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="90997-840">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-840">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="90997-841">GetValue</span><span class="sxs-lookup"><span data-stu-id="90997-841">GetValue</span></span>

<span data-ttu-id="90997-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e a converte para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="90997-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="90997-843">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="90997-843">An overload accepts a default value.</span></span>

<span data-ttu-id="90997-844">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="90997-844">The following example:</span></span>

* <span data-ttu-id="90997-845">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="90997-845">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="90997-846">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="90997-846">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="90997-847">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="90997-847">Types the value as an `int`.</span></span>
* <span data-ttu-id="90997-848">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="90997-848">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="90997-849">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="90997-849">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="90997-850">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="90997-850">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="90997-851">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="90997-851">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="90997-852">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-852">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="90997-853">section0:key0</span><span class="sxs-lookup"><span data-stu-id="90997-853">section0:key0</span></span>
* <span data-ttu-id="90997-854">section0:key1</span><span class="sxs-lookup"><span data-stu-id="90997-854">section0:key1</span></span>
* <span data-ttu-id="90997-855">section1:key0</span><span class="sxs-lookup"><span data-stu-id="90997-855">section1:key0</span></span>
* <span data-ttu-id="90997-856">section1:key1</span><span class="sxs-lookup"><span data-stu-id="90997-856">section1:key1</span></span>
* <span data-ttu-id="90997-857">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="90997-857">section2:subsection0:key0</span></span>
* <span data-ttu-id="90997-858">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="90997-858">section2:subsection0:key1</span></span>
* <span data-ttu-id="90997-859">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="90997-859">section2:subsection1:key0</span></span>
* <span data-ttu-id="90997-860">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="90997-860">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="90997-861">GetSection</span><span class="sxs-lookup"><span data-stu-id="90997-861">GetSection</span></span>

<span data-ttu-id="90997-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="90997-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="90997-863">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="90997-863">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="90997-864">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="90997-864">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="90997-865">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="90997-865">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="90997-866">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="90997-866">`GetSection` never returns `null`.</span></span> <span data-ttu-id="90997-867">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="90997-867">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="90997-868">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="90997-868">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="90997-869"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="90997-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="90997-870">GetChildren</span><span class="sxs-lookup"><span data-stu-id="90997-870">GetChildren</span></span>

<span data-ttu-id="90997-871">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="90997-871">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="90997-872">Exists</span><span class="sxs-lookup"><span data-stu-id="90997-872">Exists</span></span>

<span data-ttu-id="90997-873">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-873">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="90997-874">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-874">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="90997-875">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="90997-875">Bind to an object graph</span></span>

<span data-ttu-id="90997-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="90997-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="90997-877">Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.</span><span class="sxs-lookup"><span data-stu-id="90997-877">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="90997-878">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="90997-878">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="90997-879">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-879">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="90997-880">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="90997-880">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="90997-881">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="90997-881">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="90997-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="90997-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="90997-883">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="90997-883">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="90997-884">O código a seguir mostra como usar `Get<T>` com o exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="90997-884">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="90997-885">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="90997-885">Bind an array to a class</span></span>

<span data-ttu-id="90997-886">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="90997-886">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="90997-887">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-887">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="90997-888">Qualquer formato de matriz que expõe um segmento de chave numérico (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar a matriz a uma matriz de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="90997-888">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="90997-889">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="90997-889">Binding is provided by convention.</span></span> <span data-ttu-id="90997-890">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="90997-890">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="90997-891">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="90997-891">**In-memory array processing**</span></span>

<span data-ttu-id="90997-892">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="90997-892">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="90997-893">Chave</span><span class="sxs-lookup"><span data-stu-id="90997-893">Key</span></span>             | <span data-ttu-id="90997-894">Valor</span><span class="sxs-lookup"><span data-stu-id="90997-894">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="90997-895">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="90997-895">array:entries:0</span></span> | <span data-ttu-id="90997-896">value0</span><span class="sxs-lookup"><span data-stu-id="90997-896">value0</span></span> |
| <span data-ttu-id="90997-897">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="90997-897">array:entries:1</span></span> | <span data-ttu-id="90997-898">value1</span><span class="sxs-lookup"><span data-stu-id="90997-898">value1</span></span> |
| <span data-ttu-id="90997-899">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="90997-899">array:entries:2</span></span> | <span data-ttu-id="90997-900">value2</span><span class="sxs-lookup"><span data-stu-id="90997-900">value2</span></span> |
| <span data-ttu-id="90997-901">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="90997-901">array:entries:4</span></span> | <span data-ttu-id="90997-902">value4</span><span class="sxs-lookup"><span data-stu-id="90997-902">value4</span></span> |
| <span data-ttu-id="90997-903">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="90997-903">array:entries:5</span></span> | <span data-ttu-id="90997-904">value5</span><span class="sxs-lookup"><span data-stu-id="90997-904">value5</span></span> |

<span data-ttu-id="90997-905">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="90997-905">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="90997-906">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="90997-906">The array skips a value for index &num;3.</span></span> <span data-ttu-id="90997-907">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="90997-907">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="90997-908">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="90997-908">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="90997-909">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="90997-909">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="90997-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) sintaxe também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="90997-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="90997-911">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-911">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="90997-912">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="90997-912">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="90997-913">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="90997-913">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="90997-914">0</span><span class="sxs-lookup"><span data-stu-id="90997-914">0</span></span>                            | <span data-ttu-id="90997-915">value0</span><span class="sxs-lookup"><span data-stu-id="90997-915">value0</span></span>                       |
| <span data-ttu-id="90997-916">1</span><span class="sxs-lookup"><span data-stu-id="90997-916">1</span></span>                            | <span data-ttu-id="90997-917">value1</span><span class="sxs-lookup"><span data-stu-id="90997-917">value1</span></span>                       |
| <span data-ttu-id="90997-918">2</span><span class="sxs-lookup"><span data-stu-id="90997-918">2</span></span>                            | <span data-ttu-id="90997-919">value2</span><span class="sxs-lookup"><span data-stu-id="90997-919">value2</span></span>                       |
| <span data-ttu-id="90997-920">3</span><span class="sxs-lookup"><span data-stu-id="90997-920">3</span></span>                            | <span data-ttu-id="90997-921">value4</span><span class="sxs-lookup"><span data-stu-id="90997-921">value4</span></span>                       |
| <span data-ttu-id="90997-922">4</span><span class="sxs-lookup"><span data-stu-id="90997-922">4</span></span>                            | <span data-ttu-id="90997-923">value5</span><span class="sxs-lookup"><span data-stu-id="90997-923">value5</span></span>                       |

<span data-ttu-id="90997-924">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="90997-924">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="90997-925">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="90997-925">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="90997-926">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="90997-926">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="90997-927">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-927">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="90997-928">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="90997-928">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="90997-929">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="90997-929">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="90997-930">No `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="90997-930">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="90997-931">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-931">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="90997-932">Chave</span><span class="sxs-lookup"><span data-stu-id="90997-932">Key</span></span>             | <span data-ttu-id="90997-933">Valor</span><span class="sxs-lookup"><span data-stu-id="90997-933">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="90997-934">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="90997-934">array:entries:3</span></span> | <span data-ttu-id="90997-935">value3</span><span class="sxs-lookup"><span data-stu-id="90997-935">value3</span></span> |

<span data-ttu-id="90997-936">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="90997-936">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="90997-937">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="90997-937">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="90997-938">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="90997-938">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="90997-939">0</span><span class="sxs-lookup"><span data-stu-id="90997-939">0</span></span>                            | <span data-ttu-id="90997-940">value0</span><span class="sxs-lookup"><span data-stu-id="90997-940">value0</span></span>                       |
| <span data-ttu-id="90997-941">1</span><span class="sxs-lookup"><span data-stu-id="90997-941">1</span></span>                            | <span data-ttu-id="90997-942">value1</span><span class="sxs-lookup"><span data-stu-id="90997-942">value1</span></span>                       |
| <span data-ttu-id="90997-943">2</span><span class="sxs-lookup"><span data-stu-id="90997-943">2</span></span>                            | <span data-ttu-id="90997-944">value2</span><span class="sxs-lookup"><span data-stu-id="90997-944">value2</span></span>                       |
| <span data-ttu-id="90997-945">3</span><span class="sxs-lookup"><span data-stu-id="90997-945">3</span></span>                            | <span data-ttu-id="90997-946">value3</span><span class="sxs-lookup"><span data-stu-id="90997-946">value3</span></span>                       |
| <span data-ttu-id="90997-947">4</span><span class="sxs-lookup"><span data-stu-id="90997-947">4</span></span>                            | <span data-ttu-id="90997-948">value4</span><span class="sxs-lookup"><span data-stu-id="90997-948">value4</span></span>                       |
| <span data-ttu-id="90997-949">5</span><span class="sxs-lookup"><span data-stu-id="90997-949">5</span></span>                            | <span data-ttu-id="90997-950">value5</span><span class="sxs-lookup"><span data-stu-id="90997-950">value5</span></span>                       |

<span data-ttu-id="90997-951">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="90997-951">**JSON array processing**</span></span>

<span data-ttu-id="90997-952">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="90997-952">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="90997-953">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="90997-953">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="90997-954">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="90997-954">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="90997-955">Chave</span><span class="sxs-lookup"><span data-stu-id="90997-955">Key</span></span>                     | <span data-ttu-id="90997-956">Valor</span><span class="sxs-lookup"><span data-stu-id="90997-956">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="90997-957">json_array:key</span><span class="sxs-lookup"><span data-stu-id="90997-957">json_array:key</span></span>          | <span data-ttu-id="90997-958">valueA</span><span class="sxs-lookup"><span data-stu-id="90997-958">valueA</span></span> |
| <span data-ttu-id="90997-959">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="90997-959">json_array:subsection:0</span></span> | <span data-ttu-id="90997-960">valueB</span><span class="sxs-lookup"><span data-stu-id="90997-960">valueB</span></span> |
| <span data-ttu-id="90997-961">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="90997-961">json_array:subsection:1</span></span> | <span data-ttu-id="90997-962">valueC</span><span class="sxs-lookup"><span data-stu-id="90997-962">valueC</span></span> |
| <span data-ttu-id="90997-963">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="90997-963">json_array:subsection:2</span></span> | <span data-ttu-id="90997-964">valueD</span><span class="sxs-lookup"><span data-stu-id="90997-964">valueD</span></span> |

<span data-ttu-id="90997-965">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="90997-965">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="90997-966">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="90997-966">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="90997-967">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="90997-967">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="90997-968">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="90997-968">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="90997-969">Valor `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="90997-969">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="90997-970">0</span><span class="sxs-lookup"><span data-stu-id="90997-970">0</span></span>                                   | <span data-ttu-id="90997-971">valueB</span><span class="sxs-lookup"><span data-stu-id="90997-971">valueB</span></span>                              |
| <span data-ttu-id="90997-972">1</span><span class="sxs-lookup"><span data-stu-id="90997-972">1</span></span>                                   | <span data-ttu-id="90997-973">valueC</span><span class="sxs-lookup"><span data-stu-id="90997-973">valueC</span></span>                              |
| <span data-ttu-id="90997-974">2</span><span class="sxs-lookup"><span data-stu-id="90997-974">2</span></span>                                   | <span data-ttu-id="90997-975">valueD</span><span class="sxs-lookup"><span data-stu-id="90997-975">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="90997-976">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="90997-976">Custom configuration provider</span></span>

<span data-ttu-id="90997-977">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="90997-977">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="90997-978">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="90997-978">The provider has the following characteristics:</span></span>

* <span data-ttu-id="90997-979">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="90997-979">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="90997-980">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="90997-980">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="90997-981">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="90997-981">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="90997-982">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="90997-982">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="90997-983">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-983">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="90997-984">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="90997-984">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="90997-985">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-985">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="90997-986">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="90997-986">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="90997-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="90997-988">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="90997-988">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="90997-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="90997-990">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="90997-990">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="90997-991">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="90997-991">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="90997-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="90997-993">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="90997-993">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="90997-994">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-994">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="90997-995">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="90997-995">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="90997-996">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="90997-996">Access configuration during startup</span></span>

<span data-ttu-id="90997-997">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="90997-997">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="90997-998">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="90997-998">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="90997-999">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, confira [Inicialização do aplicativo: Métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="90997-999">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="90997-1000">Acessar a configuração em uma página do Razor Pages ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="90997-1000">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="90997-1001">Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.</span><span class="sxs-lookup"><span data-stu-id="90997-1001">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="90997-1002">Em uma página do Razor:</span><span class="sxs-lookup"><span data-stu-id="90997-1002">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="90997-1003">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="90997-1003">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="90997-1004">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="90997-1004">Add configuration from an external assembly</span></span>

<span data-ttu-id="90997-1005">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="90997-1005">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="90997-1006">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="90997-1006">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90997-1007">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="90997-1007">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
