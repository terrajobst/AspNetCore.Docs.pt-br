---
title: Configuração no ASP.NET Core
author: guardrex
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: df49286c3f050b8e90cb5427cf03e2b896a39294
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885566"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="9bc6a-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9bc6a-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="9bc6a-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9bc6a-105">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="9bc6a-106">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="9bc6a-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9bc6a-107">Azure Key Vault</span></span>
* <span data-ttu-id="9bc6a-108">Configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="9bc6a-108">Azure App Configuration</span></span>
* <span data-ttu-id="9bc6a-109">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="9bc6a-109">Command-line arguments</span></span>
* <span data-ttu-id="9bc6a-110">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="9bc6a-111">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="9bc6a-111">Directory files</span></span>
* <span data-ttu-id="9bc6a-112">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="9bc6a-112">Environment variables</span></span>
* <span data-ttu-id="9bc6a-113">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="9bc6a-113">In-memory .NET objects</span></span>
* <span data-ttu-id="9bc6a-114">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="9bc6a-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9bc6a-115">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos implicitamente pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9bc6a-116">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="9bc6a-117">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="9bc6a-118">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="9bc6a-119">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="9bc6a-120">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9bc6a-121">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9bc6a-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9bc6a-122">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="9bc6a-122">Host versus app configuration</span></span>

<span data-ttu-id="9bc6a-123">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9bc6a-124">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9bc6a-125">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9bc6a-126">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="9bc6a-127">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="9bc6a-128">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="9bc6a-128">Other configuration</span></span>

<span data-ttu-id="9bc6a-129">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-129">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="9bc6a-130">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-130">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="9bc6a-131">*Launch. json*/*launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-131">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="9bc6a-132">Em <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-132">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="9bc6a-133">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-133">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="9bc6a-134">o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-134">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="9bc6a-135">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-135">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="9bc6a-136">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="9bc6a-136">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9bc6a-137">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-137">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="9bc6a-138">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-138">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="9bc6a-139">O seguinte se aplica a aplicativos que usam o [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-139">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="9bc6a-140">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-140">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="9bc6a-141">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-141">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9bc6a-142">Variáveis ​​de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-142">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9bc6a-143">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-143">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9bc6a-144">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-144">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9bc6a-145">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="9bc6a-145">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="9bc6a-146">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-146">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="9bc6a-147">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-147">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="9bc6a-148">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-148">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="9bc6a-149">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-149">Enable IIS integration.</span></span>
* <span data-ttu-id="9bc6a-150">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-150">App configuration is provided from:</span></span>
  * <span data-ttu-id="9bc6a-151">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-151">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9bc6a-152">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-152">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9bc6a-153">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-153">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="9bc6a-154">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-154">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="9bc6a-155">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9bc6a-156">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-156">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="9bc6a-157">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-157">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="9bc6a-158">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-158">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9bc6a-159">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-159">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="9bc6a-160">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-160">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9bc6a-161">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-161">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9bc6a-162">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-162">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9bc6a-163">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-163">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9bc6a-164">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-164">App configuration is provided from:</span></span>
  * <span data-ttu-id="9bc6a-165">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-165">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9bc6a-166">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-166">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9bc6a-167">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-167">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="9bc6a-168">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-168">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="9bc6a-169">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-169">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="9bc6a-170">Segurança</span><span class="sxs-lookup"><span data-stu-id="9bc6a-170">Security</span></span>

<span data-ttu-id="9bc6a-171">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-171">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="9bc6a-172">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-172">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="9bc6a-173">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-173">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9bc6a-174">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-174">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9bc6a-175">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-175">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="9bc6a-176"><xref:security/app-secrets> &ndash; inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-176"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="9bc6a-177">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-177">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="9bc6a-178">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-178">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="9bc6a-179">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-179">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="9bc6a-180">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-180">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9bc6a-181">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="9bc6a-181">Hierarchical configuration data</span></span>

<span data-ttu-id="9bc6a-182">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-182">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9bc6a-183">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-183">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="9bc6a-184">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-184">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="9bc6a-185">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-185">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="9bc6a-186">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-186">section0:key0</span></span>
* <span data-ttu-id="9bc6a-187">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-187">section0:key1</span></span>
* <span data-ttu-id="9bc6a-188">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-188">section1:key0</span></span>
* <span data-ttu-id="9bc6a-189">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-189">section1:key1</span></span>

<span data-ttu-id="9bc6a-190">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-190"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9bc6a-191">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-191">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="9bc6a-192">Convenções</span><span class="sxs-lookup"><span data-stu-id="9bc6a-192">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="9bc6a-193">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="9bc6a-193">Sources and providers</span></span>

<span data-ttu-id="9bc6a-194">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-194">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="9bc6a-195">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-195">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="9bc6a-196">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-196">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="9bc6a-197"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-197"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9bc6a-198"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetada em um Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-198"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="9bc6a-199">Nos exemplos a seguir, o campo `_config` é usado para acessar os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-199">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="9bc6a-200">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-200">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="9bc6a-201">Keys</span><span class="sxs-lookup"><span data-stu-id="9bc6a-201">Keys</span></span>

<span data-ttu-id="9bc6a-202">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-202">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="9bc6a-203">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-203">Keys are case-insensitive.</span></span> <span data-ttu-id="9bc6a-204">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-204">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9bc6a-205">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-205">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="9bc6a-206">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="9bc6a-206">Hierarchical keys</span></span>
  * <span data-ttu-id="9bc6a-207">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-207">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9bc6a-208">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-208">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9bc6a-209">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-209">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="9bc6a-210">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-210">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9bc6a-211">Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-211">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9bc6a-212">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-212">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9bc6a-213">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-213">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="9bc6a-214">Valores</span><span class="sxs-lookup"><span data-stu-id="9bc6a-214">Values</span></span>

<span data-ttu-id="9bc6a-215">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-215">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="9bc6a-216">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-216">Values are strings.</span></span>
* <span data-ttu-id="9bc6a-217">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-217">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="9bc6a-218">Provedores</span><span class="sxs-lookup"><span data-stu-id="9bc6a-218">Providers</span></span>

<span data-ttu-id="9bc6a-219">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-219">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9bc6a-220">Provider</span><span class="sxs-lookup"><span data-stu-id="9bc6a-220">Provider</span></span> | <span data-ttu-id="9bc6a-221">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="9bc6a-221">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="9bc6a-222">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-222">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="9bc6a-223">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9bc6a-223">Azure Key Vault</span></span> |
| <span data-ttu-id="9bc6a-224">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-224">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="9bc6a-225">Configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="9bc6a-225">Azure App Configuration</span></span> |
| [<span data-ttu-id="9bc6a-226">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="9bc6a-226">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="9bc6a-227">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="9bc6a-227">Command-line parameters</span></span> |
| [<span data-ttu-id="9bc6a-228">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="9bc6a-228">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="9bc6a-229">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="9bc6a-229">Custom source</span></span> |
| [<span data-ttu-id="9bc6a-230">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="9bc6a-230">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="9bc6a-231">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="9bc6a-231">Environment variables</span></span> |
| [<span data-ttu-id="9bc6a-232">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="9bc6a-232">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="9bc6a-233">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-233">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="9bc6a-234">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="9bc6a-234">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="9bc6a-235">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="9bc6a-235">Directory files</span></span> |
| [<span data-ttu-id="9bc6a-236">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="9bc6a-236">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="9bc6a-237">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="9bc6a-237">In-memory collections</span></span> |
| <span data-ttu-id="9bc6a-238">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-238">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="9bc6a-239">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="9bc6a-239">File in the user profile directory</span></span> |

<span data-ttu-id="9bc6a-240">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-240">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="9bc6a-241">Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-241">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="9bc6a-242">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-242">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="9bc6a-243">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-243">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9bc6a-244">Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-244">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="9bc6a-245">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9bc6a-245">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="9bc6a-246">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-246">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="9bc6a-247">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="9bc6a-247">Environment variables</span></span>
1. <span data-ttu-id="9bc6a-248">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="9bc6a-248">Command-line arguments</span></span>

<span data-ttu-id="9bc6a-249">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-249">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9bc6a-250">A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-250">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9bc6a-251">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-251">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="9bc6a-252">Configurar o construtor de host com ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="9bc6a-252">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="9bc6a-253">Para configurar o construtor de host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> e forneça a configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-253">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="9bc6a-254">`ConfigureHostConfiguration` é usado para inicializar o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> para uso posterior no processo de build.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-254">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="9bc6a-255">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-255">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="9bc6a-256">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="9bc6a-256">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="9bc6a-257">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-257">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

::: moniker-end

## <a name="configureappconfiguration"></a><span data-ttu-id="9bc6a-258">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9bc6a-258">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9bc6a-259">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-259">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="9bc6a-260">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="9bc6a-260">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="9bc6a-261">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-261">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="9bc6a-262">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="9bc6a-262">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="9bc6a-263">Para remover os provedores adicionados por `CreateDefaultBuilder`, chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-263">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="9bc6a-264">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="9bc6a-264">Consume configuration during app startup</span></span>

<span data-ttu-id="9bc6a-265">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-265">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9bc6a-266">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-266">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="9bc6a-267">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="9bc6a-267">Command-line Configuration Provider</span></span>

<span data-ttu-id="9bc6a-268">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-268">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="9bc6a-269">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-269">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9bc6a-270">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-270">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="9bc6a-271">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-271">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9bc6a-272">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-272">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9bc6a-273">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-273">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9bc6a-274">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-274">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9bc6a-275">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-275">Environment variables.</span></span>

<span data-ttu-id="9bc6a-276">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-276">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="9bc6a-277">Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-277">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="9bc6a-278">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-278">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="9bc6a-279">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-279">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="9bc6a-280">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-280">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9bc6a-281">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-281">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="9bc6a-282">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="9bc6a-282">**Example**</span></span>

<span data-ttu-id="9bc6a-283">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-283">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="9bc6a-284">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-284">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="9bc6a-285">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-285">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="9bc6a-286">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-286">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9bc6a-287">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-287">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="9bc6a-288">Arguments</span><span class="sxs-lookup"><span data-stu-id="9bc6a-288">Arguments</span></span>

<span data-ttu-id="9bc6a-289">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-289">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="9bc6a-290">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-290">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="9bc6a-291">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="9bc6a-291">Key prefix</span></span>               | <span data-ttu-id="9bc6a-292">Exemplo</span><span class="sxs-lookup"><span data-stu-id="9bc6a-292">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="9bc6a-293">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="9bc6a-293">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="9bc6a-294">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-294">Two dashes (`--`)</span></span>        | <span data-ttu-id="9bc6a-295">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-295">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="9bc6a-296">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="9bc6a-296">Forward slash (`/`)</span></span>      | <span data-ttu-id="9bc6a-297">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-297">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="9bc6a-298">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-298">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="9bc6a-299">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-299">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="9bc6a-300">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="9bc6a-300">Switch mappings</span></span>

<span data-ttu-id="9bc6a-301">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-301">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="9bc6a-302">Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, forneça um dicionário de substituições de switch para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-302">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9bc6a-303">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-303">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9bc6a-304">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-304">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9bc6a-305">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-305">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9bc6a-306">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-306">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9bc6a-307">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-307">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="9bc6a-308">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-308">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9bc6a-309">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-309">Create a switch mappings dictionary.</span></span> <span data-ttu-id="9bc6a-310">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-310">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="9bc6a-311">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-311">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="9bc6a-312">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-312">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="9bc6a-313">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-313">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9bc6a-314">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-314">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="9bc6a-315">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-315">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="9bc6a-316">Chave</span><span class="sxs-lookup"><span data-stu-id="9bc6a-316">Key</span></span>       | <span data-ttu-id="9bc6a-317">Value</span><span class="sxs-lookup"><span data-stu-id="9bc6a-317">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="9bc6a-318">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-318">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="9bc6a-319">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-319">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="9bc6a-320">Chave</span><span class="sxs-lookup"><span data-stu-id="9bc6a-320">Key</span></span>               | <span data-ttu-id="9bc6a-321">Value</span><span class="sxs-lookup"><span data-stu-id="9bc6a-321">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="9bc6a-322">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="9bc6a-322">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="9bc6a-323">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-323">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="9bc6a-324">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-324">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9bc6a-325">[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-325">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="9bc6a-326">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-326">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9bc6a-327">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `DOTNET_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host genérico](xref:fundamentals/host/generic-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-327">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="9bc6a-328">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-328">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9bc6a-329">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-329">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="9bc6a-330">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-330">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="9bc6a-331">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-331">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9bc6a-332">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-332">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="9bc6a-333">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-333">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9bc6a-334">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-334">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9bc6a-335">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-335">Command-line arguments.</span></span>

<span data-ttu-id="9bc6a-336">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-336">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="9bc6a-337">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-337">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="9bc6a-338">Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-338">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="9bc6a-339">Chame `AddEnvironmentVariables` último para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-339">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="9bc6a-340">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="9bc6a-340">**Example**</span></span>

<span data-ttu-id="9bc6a-341">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-341">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="9bc6a-342">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-342">Run the sample app.</span></span> <span data-ttu-id="9bc6a-343">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-343">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9bc6a-344">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-344">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="9bc6a-345">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-345">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="9bc6a-346">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-346">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="9bc6a-347">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-347">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="9bc6a-348">Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-348">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="9bc6a-349">Prefixos</span><span class="sxs-lookup"><span data-stu-id="9bc6a-349">Prefixes</span></span>

<span data-ttu-id="9bc6a-350">As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-350">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="9bc6a-351">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-351">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="9bc6a-352">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-352">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="9bc6a-353">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-353">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="9bc6a-354">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-354">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9bc6a-355">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="9bc6a-355">**Connection string prefixes**</span></span>

<span data-ttu-id="9bc6a-356">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-356">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9bc6a-357">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-357">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9bc6a-358">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="9bc6a-358">Connection string prefix</span></span> | <span data-ttu-id="9bc6a-359">Provider</span><span class="sxs-lookup"><span data-stu-id="9bc6a-359">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="9bc6a-360">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="9bc6a-360">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="9bc6a-361">MySQL</span><span class="sxs-lookup"><span data-stu-id="9bc6a-361">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="9bc6a-362">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="9bc6a-362">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="9bc6a-363">SQL Server</span><span class="sxs-lookup"><span data-stu-id="9bc6a-363">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="9bc6a-364">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-364">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9bc6a-365">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-365">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9bc6a-366">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-366">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9bc6a-367">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="9bc6a-367">Environment variable key</span></span> | <span data-ttu-id="9bc6a-368">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="9bc6a-368">Converted configuration key</span></span> | <span data-ttu-id="9bc6a-369">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="9bc6a-369">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="9bc6a-370">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-370">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="9bc6a-371">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-371">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="9bc6a-372">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-372">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="9bc6a-373">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-373">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="9bc6a-374">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-374">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="9bc6a-375">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-375">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="9bc6a-376">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-376">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="9bc6a-377">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="9bc6a-377">File Configuration Provider</span></span>

<span data-ttu-id="9bc6a-378"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-378"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9bc6a-379">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-379">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="9bc6a-380">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="9bc6a-380">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9bc6a-381">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="9bc6a-381">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="9bc6a-382">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="9bc6a-382">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9bc6a-383">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="9bc6a-383">INI Configuration Provider</span></span>

<span data-ttu-id="9bc6a-384">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-384">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9bc6a-385">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-385">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9bc6a-386">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-386">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="9bc6a-387">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-387">Overloads permit specifying:</span></span>

* <span data-ttu-id="9bc6a-388">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-388">Whether the file is optional.</span></span>
* <span data-ttu-id="9bc6a-389">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-389">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9bc6a-390">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-390">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9bc6a-391">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-391">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9bc6a-392">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-392">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="9bc6a-393">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-393">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9bc6a-394">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-394">section0:key0</span></span>
* <span data-ttu-id="9bc6a-395">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-395">section0:key1</span></span>
* <span data-ttu-id="9bc6a-396">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="9bc6a-396">section1:subsection:key</span></span>
* <span data-ttu-id="9bc6a-397">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="9bc6a-397">section2:subsection0:key</span></span>
* <span data-ttu-id="9bc6a-398">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="9bc6a-398">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="9bc6a-399">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="9bc6a-399">JSON Configuration Provider</span></span>

<span data-ttu-id="9bc6a-400">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-400">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="9bc6a-401">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-401">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9bc6a-402">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-402">Overloads permit specifying:</span></span>

* <span data-ttu-id="9bc6a-403">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-403">Whether the file is optional.</span></span>
* <span data-ttu-id="9bc6a-404">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-404">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9bc6a-405">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-405">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9bc6a-406">`AddJsonFile` é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-406">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9bc6a-407">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-407">The method is called to load configuration from:</span></span>

* <span data-ttu-id="9bc6a-408">*appSettings. json* &ndash; este arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-408">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="9bc6a-409">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-409">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="9bc6a-410">*appSettings. {Environment}. JSON* &ndash; a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-410">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="9bc6a-411">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-411">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9bc6a-412">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-412">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9bc6a-413">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-413">Environment variables.</span></span>
* <span data-ttu-id="9bc6a-414">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-414">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9bc6a-415">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-415">Command-line arguments.</span></span>

<span data-ttu-id="9bc6a-416">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-416">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="9bc6a-417">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-417">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="9bc6a-418">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-418">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9bc6a-419">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="9bc6a-419">**Example**</span></span>

<span data-ttu-id="9bc6a-420">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-420">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9bc6a-421">A primeira chamada para `AddJsonFile` carrega a configuração de *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-421">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="9bc6a-422">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-422">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="9bc6a-423">Para *appSettings. Development. JSON* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-423">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="9bc6a-424">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-424">Run the sample app.</span></span> <span data-ttu-id="9bc6a-425">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-425">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9bc6a-426">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-426">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="9bc6a-427">O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-427">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="9bc6a-428">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-428">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="9bc6a-429">Abra o arquivo *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9bc6a-429">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="9bc6a-430">No perfil de `ConfigurationSample`, altere o valor da variável de ambiente `ASPNETCORE_ENVIRONMENT` para `Production`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-430">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="9bc6a-431">Salve o arquivo e execute o aplicativo com `dotnet run` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-431">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="9bc6a-432">As configurações em *appSettings. O Development. JSON* não substitui mais as configurações em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-432">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="9bc6a-433">O nível de log para a `Logging:LogLevel:Default` de chave é `Information`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-433">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="9bc6a-434">A primeira chamada para `AddJsonFile` carrega a configuração de *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-434">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="9bc6a-435">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-435">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="9bc6a-436">Para *appSettings. Development. JSON* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-436">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="9bc6a-437">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-437">Run the sample app.</span></span> <span data-ttu-id="9bc6a-438">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-438">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9bc6a-439">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-439">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="9bc6a-440">O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-440">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="9bc6a-441">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-441">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="9bc6a-442">Abra o arquivo *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9bc6a-442">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="9bc6a-443">No perfil de `ConfigurationSample`, altere o valor da variável de ambiente `ASPNETCORE_ENVIRONMENT` para `Production`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-443">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="9bc6a-444">Salve o arquivo e execute o aplicativo com `dotnet run` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-444">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="9bc6a-445">As configurações em *appSettings. O Development. JSON* não substitui mais as configurações em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-445">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="9bc6a-446">O nível de log para a `Logging:LogLevel:Default` de chave é `Warning`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-446">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

::: moniker-end

### <a name="xml-configuration-provider"></a><span data-ttu-id="9bc6a-447">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="9bc6a-447">XML Configuration Provider</span></span>

<span data-ttu-id="9bc6a-448">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-448">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9bc6a-449">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-449">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9bc6a-450">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-450">Overloads permit specifying:</span></span>

* <span data-ttu-id="9bc6a-451">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-451">Whether the file is optional.</span></span>
* <span data-ttu-id="9bc6a-452">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-452">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9bc6a-453">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-453">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9bc6a-454">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-454">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="9bc6a-455">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-455">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="9bc6a-456">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-456">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9bc6a-457">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-457">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="9bc6a-458">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-458">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9bc6a-459">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-459">section0:key0</span></span>
* <span data-ttu-id="9bc6a-460">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-460">section0:key1</span></span>
* <span data-ttu-id="9bc6a-461">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-461">section1:key0</span></span>
* <span data-ttu-id="9bc6a-462">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-462">section1:key1</span></span>

<span data-ttu-id="9bc6a-463">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-463">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="9bc6a-464">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-464">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9bc6a-465">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-465">section:section0:key:key0</span></span>
* <span data-ttu-id="9bc6a-466">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-466">section:section0:key:key1</span></span>
* <span data-ttu-id="9bc6a-467">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-467">section:section1:key:key0</span></span>
* <span data-ttu-id="9bc6a-468">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-468">section:section1:key:key1</span></span>

<span data-ttu-id="9bc6a-469">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-469">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9bc6a-470">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-470">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9bc6a-471">key:attribute</span><span class="sxs-lookup"><span data-stu-id="9bc6a-471">key:attribute</span></span>
* <span data-ttu-id="9bc6a-472">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="9bc6a-472">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9bc6a-473">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="9bc6a-473">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="9bc6a-474">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-474">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9bc6a-475">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-475">The key is the file name.</span></span> <span data-ttu-id="9bc6a-476">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-476">The value contains the file's contents.</span></span> <span data-ttu-id="9bc6a-477">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-477">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9bc6a-478">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-478">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9bc6a-479">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-479">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9bc6a-480">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-480">Overloads permit specifying:</span></span>

* <span data-ttu-id="9bc6a-481">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-481">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9bc6a-482">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-482">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9bc6a-483">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-483">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9bc6a-484">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-484">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9bc6a-485">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-485">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="9bc6a-486">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="9bc6a-486">Memory Configuration Provider</span></span>

<span data-ttu-id="9bc6a-487">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-487">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9bc6a-488">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-488">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9bc6a-489">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-489">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="9bc6a-490">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-490">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="9bc6a-491">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-491">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="9bc6a-492">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-492">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="9bc6a-493">GetValue</span><span class="sxs-lookup"><span data-stu-id="9bc6a-493">GetValue</span></span>

<span data-ttu-id="9bc6a-494">[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-494">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="9bc6a-495">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-495">An overload accepts a default value.</span></span>

<span data-ttu-id="9bc6a-496">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-496">The following example:</span></span>

* <span data-ttu-id="9bc6a-497">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-497">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="9bc6a-498">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-498">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="9bc6a-499">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-499">Types the value as an `int`.</span></span>
* <span data-ttu-id="9bc6a-500">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-500">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9bc6a-501">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="9bc6a-501">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9bc6a-502">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-502">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="9bc6a-503">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-503">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="9bc6a-504">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-504">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="9bc6a-505">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-505">section0:key0</span></span>
* <span data-ttu-id="9bc6a-506">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-506">section0:key1</span></span>
* <span data-ttu-id="9bc6a-507">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-507">section1:key0</span></span>
* <span data-ttu-id="9bc6a-508">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-508">section1:key1</span></span>
* <span data-ttu-id="9bc6a-509">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-509">section2:subsection0:key0</span></span>
* <span data-ttu-id="9bc6a-510">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-510">section2:subsection0:key1</span></span>
* <span data-ttu-id="9bc6a-511">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-511">section2:subsection1:key0</span></span>
* <span data-ttu-id="9bc6a-512">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-512">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="9bc6a-513">GetSection</span><span class="sxs-lookup"><span data-stu-id="9bc6a-513">GetSection</span></span>

<span data-ttu-id="9bc6a-514">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-514">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="9bc6a-515">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-515">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="9bc6a-516">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-516">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="9bc6a-517">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-517">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="9bc6a-518">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-518">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9bc6a-519">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-519">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9bc6a-520">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-520">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9bc6a-521"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-521">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="9bc6a-522">GetChildren</span><span class="sxs-lookup"><span data-stu-id="9bc6a-522">GetChildren</span></span>

<span data-ttu-id="9bc6a-523">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-523">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="9bc6a-524">Exists</span><span class="sxs-lookup"><span data-stu-id="9bc6a-524">Exists</span></span>

<span data-ttu-id="9bc6a-525">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-525">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="9bc6a-526">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-526">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="9bc6a-527">Associar a uma classe</span><span class="sxs-lookup"><span data-stu-id="9bc6a-527">Bind to a class</span></span>

<span data-ttu-id="9bc6a-528">A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-528">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="9bc6a-529">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-529">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9bc6a-530">Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-530">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="9bc6a-531">O associador associa valores a todas as propriedades públicas de leitura/gravação do tipo fornecido.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-531">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="9bc6a-532">Os campos **não** estão associados.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-532">Fields are **not** bound.</span></span>

<span data-ttu-id="9bc6a-533">O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="9bc6a-533">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9bc6a-534">A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-534">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="9bc6a-535">Os seguintes pares chave-valor de configuração são criados:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-535">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="9bc6a-536">Chave</span><span class="sxs-lookup"><span data-stu-id="9bc6a-536">Key</span></span>                   | <span data-ttu-id="9bc6a-537">Value</span><span class="sxs-lookup"><span data-stu-id="9bc6a-537">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="9bc6a-538">starship:name</span><span class="sxs-lookup"><span data-stu-id="9bc6a-538">starship:name</span></span>         | <span data-ttu-id="9bc6a-539">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="9bc6a-539">USS Enterprise</span></span>                                    |
| <span data-ttu-id="9bc6a-540">starship:registry</span><span class="sxs-lookup"><span data-stu-id="9bc6a-540">starship:registry</span></span>     | <span data-ttu-id="9bc6a-541">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="9bc6a-541">NCC-1701</span></span>                                          |
| <span data-ttu-id="9bc6a-542">starship:class</span><span class="sxs-lookup"><span data-stu-id="9bc6a-542">starship:class</span></span>        | <span data-ttu-id="9bc6a-543">Constituição</span><span class="sxs-lookup"><span data-stu-id="9bc6a-543">Constitution</span></span>                                      |
| <span data-ttu-id="9bc6a-544">starship:length</span><span class="sxs-lookup"><span data-stu-id="9bc6a-544">starship:length</span></span>       | <span data-ttu-id="9bc6a-545">304,8</span><span class="sxs-lookup"><span data-stu-id="9bc6a-545">304.8</span></span>                                             |
| <span data-ttu-id="9bc6a-546">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="9bc6a-546">starship:commissioned</span></span> | <span data-ttu-id="9bc6a-547">False</span><span class="sxs-lookup"><span data-stu-id="9bc6a-547">False</span></span>                                             |
| <span data-ttu-id="9bc6a-548">marca</span><span class="sxs-lookup"><span data-stu-id="9bc6a-548">trademark</span></span>             | <span data-ttu-id="9bc6a-549">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="9bc6a-549">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="9bc6a-550">O aplicativo de exemplo chama `GetSection` com a chave `starship`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-550">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="9bc6a-551">Os pares chave-valor `starship` são isolados.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-551">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="9bc6a-552">O método `Bind` é chamado na subseção passando uma instância da classe `Starship`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-552">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="9bc6a-553">Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-553">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="9bc6a-554">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="9bc6a-554">Bind to an object graph</span></span>

<span data-ttu-id="9bc6a-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="9bc6a-556">Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-556">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="9bc6a-557">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="9bc6a-557">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9bc6a-558">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-558">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="9bc6a-559">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-559">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="9bc6a-560">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-560">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="9bc6a-561">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-561">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="9bc6a-562">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-562">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="9bc6a-563">O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-563">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9bc6a-564">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="9bc6a-564">Bind an array to a class</span></span>

<span data-ttu-id="9bc6a-565">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="9bc6a-565">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="9bc6a-566">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-566">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9bc6a-567">Qualquer formato de matriz que expõe um segmento de chave numérico (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar a matriz a uma matriz de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-567">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="9bc6a-568">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-568">Binding is provided by convention.</span></span> <span data-ttu-id="9bc6a-569">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-569">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="9bc6a-570">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="9bc6a-570">**In-memory array processing**</span></span>

<span data-ttu-id="9bc6a-571">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-571">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="9bc6a-572">Chave</span><span class="sxs-lookup"><span data-stu-id="9bc6a-572">Key</span></span>             | <span data-ttu-id="9bc6a-573">Value</span><span class="sxs-lookup"><span data-stu-id="9bc6a-573">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9bc6a-574">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-574">array:entries:0</span></span> | <span data-ttu-id="9bc6a-575">value0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-575">value0</span></span> |
| <span data-ttu-id="9bc6a-576">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-576">array:entries:1</span></span> | <span data-ttu-id="9bc6a-577">value1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-577">value1</span></span> |
| <span data-ttu-id="9bc6a-578">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="9bc6a-578">array:entries:2</span></span> | <span data-ttu-id="9bc6a-579">value2</span><span class="sxs-lookup"><span data-stu-id="9bc6a-579">value2</span></span> |
| <span data-ttu-id="9bc6a-580">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="9bc6a-580">array:entries:4</span></span> | <span data-ttu-id="9bc6a-581">value4</span><span class="sxs-lookup"><span data-stu-id="9bc6a-581">value4</span></span> |
| <span data-ttu-id="9bc6a-582">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="9bc6a-582">array:entries:5</span></span> | <span data-ttu-id="9bc6a-583">value5</span><span class="sxs-lookup"><span data-stu-id="9bc6a-583">value5</span></span> |

<span data-ttu-id="9bc6a-584">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-584">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="9bc6a-585">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-585">The array skips a value for index &num;3.</span></span> <span data-ttu-id="9bc6a-586">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-586">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="9bc6a-587">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-587">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9bc6a-588">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-588">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="9bc6a-589">A sintaxe [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-589">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="9bc6a-590">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-590">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="9bc6a-591">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-591">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9bc6a-592">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-592">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9bc6a-593">0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-593">0</span></span>                            | <span data-ttu-id="9bc6a-594">value0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-594">value0</span></span>                       |
| <span data-ttu-id="9bc6a-595">1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-595">1</span></span>                            | <span data-ttu-id="9bc6a-596">value1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-596">value1</span></span>                       |
| <span data-ttu-id="9bc6a-597">2</span><span class="sxs-lookup"><span data-stu-id="9bc6a-597">2</span></span>                            | <span data-ttu-id="9bc6a-598">value2</span><span class="sxs-lookup"><span data-stu-id="9bc6a-598">value2</span></span>                       |
| <span data-ttu-id="9bc6a-599">3</span><span class="sxs-lookup"><span data-stu-id="9bc6a-599">3</span></span>                            | <span data-ttu-id="9bc6a-600">value4</span><span class="sxs-lookup"><span data-stu-id="9bc6a-600">value4</span></span>                       |
| <span data-ttu-id="9bc6a-601">4</span><span class="sxs-lookup"><span data-stu-id="9bc6a-601">4</span></span>                            | <span data-ttu-id="9bc6a-602">value5</span><span class="sxs-lookup"><span data-stu-id="9bc6a-602">value5</span></span>                       |

<span data-ttu-id="9bc6a-603">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-603">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9bc6a-604">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-604">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9bc6a-605">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-605">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9bc6a-606">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-606">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="9bc6a-607">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-607">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="9bc6a-608">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-608">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="9bc6a-609">No `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-609">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="9bc6a-610">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-610">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="9bc6a-611">Chave</span><span class="sxs-lookup"><span data-stu-id="9bc6a-611">Key</span></span>             | <span data-ttu-id="9bc6a-612">Value</span><span class="sxs-lookup"><span data-stu-id="9bc6a-612">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9bc6a-613">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="9bc6a-613">array:entries:3</span></span> | <span data-ttu-id="9bc6a-614">value3</span><span class="sxs-lookup"><span data-stu-id="9bc6a-614">value3</span></span> |

<span data-ttu-id="9bc6a-615">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-615">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="9bc6a-616">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-616">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9bc6a-617">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-617">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9bc6a-618">0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-618">0</span></span>                            | <span data-ttu-id="9bc6a-619">value0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-619">value0</span></span>                       |
| <span data-ttu-id="9bc6a-620">1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-620">1</span></span>                            | <span data-ttu-id="9bc6a-621">value1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-621">value1</span></span>                       |
| <span data-ttu-id="9bc6a-622">2</span><span class="sxs-lookup"><span data-stu-id="9bc6a-622">2</span></span>                            | <span data-ttu-id="9bc6a-623">value2</span><span class="sxs-lookup"><span data-stu-id="9bc6a-623">value2</span></span>                       |
| <span data-ttu-id="9bc6a-624">3</span><span class="sxs-lookup"><span data-stu-id="9bc6a-624">3</span></span>                            | <span data-ttu-id="9bc6a-625">value3</span><span class="sxs-lookup"><span data-stu-id="9bc6a-625">value3</span></span>                       |
| <span data-ttu-id="9bc6a-626">4</span><span class="sxs-lookup"><span data-stu-id="9bc6a-626">4</span></span>                            | <span data-ttu-id="9bc6a-627">value4</span><span class="sxs-lookup"><span data-stu-id="9bc6a-627">value4</span></span>                       |
| <span data-ttu-id="9bc6a-628">5</span><span class="sxs-lookup"><span data-stu-id="9bc6a-628">5</span></span>                            | <span data-ttu-id="9bc6a-629">value5</span><span class="sxs-lookup"><span data-stu-id="9bc6a-629">value5</span></span>                       |

<span data-ttu-id="9bc6a-630">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="9bc6a-630">**JSON array processing**</span></span>

<span data-ttu-id="9bc6a-631">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-631">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="9bc6a-632">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-632">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="9bc6a-633">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-633">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="9bc6a-634">Chave</span><span class="sxs-lookup"><span data-stu-id="9bc6a-634">Key</span></span>                     | <span data-ttu-id="9bc6a-635">Value</span><span class="sxs-lookup"><span data-stu-id="9bc6a-635">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="9bc6a-636">json_array:key</span><span class="sxs-lookup"><span data-stu-id="9bc6a-636">json_array:key</span></span>          | <span data-ttu-id="9bc6a-637">valueA</span><span class="sxs-lookup"><span data-stu-id="9bc6a-637">valueA</span></span> |
| <span data-ttu-id="9bc6a-638">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-638">json_array:subsection:0</span></span> | <span data-ttu-id="9bc6a-639">valueB</span><span class="sxs-lookup"><span data-stu-id="9bc6a-639">valueB</span></span> |
| <span data-ttu-id="9bc6a-640">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-640">json_array:subsection:1</span></span> | <span data-ttu-id="9bc6a-641">valueC</span><span class="sxs-lookup"><span data-stu-id="9bc6a-641">valueC</span></span> |
| <span data-ttu-id="9bc6a-642">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="9bc6a-642">json_array:subsection:2</span></span> | <span data-ttu-id="9bc6a-643">valueD</span><span class="sxs-lookup"><span data-stu-id="9bc6a-643">valueD</span></span> |

<span data-ttu-id="9bc6a-644">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-644">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9bc6a-645">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-645">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="9bc6a-646">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-646">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="9bc6a-647">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-647">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="9bc6a-648">Valor `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="9bc6a-648">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="9bc6a-649">0</span><span class="sxs-lookup"><span data-stu-id="9bc6a-649">0</span></span>                                   | <span data-ttu-id="9bc6a-650">valueB</span><span class="sxs-lookup"><span data-stu-id="9bc6a-650">valueB</span></span>                              |
| <span data-ttu-id="9bc6a-651">1</span><span class="sxs-lookup"><span data-stu-id="9bc6a-651">1</span></span>                                   | <span data-ttu-id="9bc6a-652">valueC</span><span class="sxs-lookup"><span data-stu-id="9bc6a-652">valueC</span></span>                              |
| <span data-ttu-id="9bc6a-653">2</span><span class="sxs-lookup"><span data-stu-id="9bc6a-653">2</span></span>                                   | <span data-ttu-id="9bc6a-654">valueD</span><span class="sxs-lookup"><span data-stu-id="9bc6a-654">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="9bc6a-655">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="9bc6a-655">Custom configuration provider</span></span>

<span data-ttu-id="9bc6a-656">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-656">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9bc6a-657">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-657">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9bc6a-658">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-658">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9bc6a-659">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-659">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9bc6a-660">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-660">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9bc6a-661">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-661">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9bc6a-662">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-662">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9bc6a-663">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-663">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9bc6a-664">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-664">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9bc6a-665">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-665">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9bc6a-666">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-666">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9bc6a-667">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-667">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9bc6a-668">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-668">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9bc6a-669">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-669">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9bc6a-670">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-670">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="9bc6a-671">Como [as chaves de configuração](#keys)não diferenciam maiúsculas de minúsculas, o dicionário usado para inicializar o banco de dados é criado com o comparador que não diferencia maiúsculas de minúsculas ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-671">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="9bc6a-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9bc6a-673">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-673">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9bc6a-674">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-674">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9bc6a-675">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-675">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9bc6a-676">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-676">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9bc6a-677">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-677">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9bc6a-678">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-678">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9bc6a-679">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-679">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9bc6a-680">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-680">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9bc6a-681">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-681">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9bc6a-682">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-682">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="9bc6a-683">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-683">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9bc6a-684">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-684">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9bc6a-685">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-685">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9bc6a-686">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-686">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="9bc6a-687">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="9bc6a-687">Access configuration during startup</span></span>

<span data-ttu-id="9bc6a-688">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-688">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9bc6a-689">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-689">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="9bc6a-690">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="9bc6a-690">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="9bc6a-691">Acessar a configuração em uma página do Razor Pages ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="9bc6a-691">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="9bc6a-692">Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-692">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="9bc6a-693">Em uma página do Razor:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-693">In a Razor Pages page:</span></span>

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

<span data-ttu-id="9bc6a-694">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="9bc6a-694">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9bc6a-695">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="9bc6a-695">Add configuration from an external assembly</span></span>

<span data-ttu-id="9bc6a-696">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-696">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9bc6a-697">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9bc6a-697">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bc6a-698">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9bc6a-698">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
