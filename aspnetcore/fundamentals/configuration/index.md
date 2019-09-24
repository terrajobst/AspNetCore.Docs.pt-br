---
title: Configuração no ASP.NET Core
author: guardrex
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 357a3d89648086f0329cd16bc9d72863df9bdcd6
ms.sourcegitcommit: 8a36be1bfee02eba3b07b7a86085ec25c38bae6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71217790"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="ff293-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff293-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="ff293-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ff293-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ff293-105">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="ff293-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="ff293-106">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="ff293-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="ff293-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ff293-107">Azure Key Vault</span></span>
* <span data-ttu-id="ff293-108">Configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="ff293-108">Azure App Configuration</span></span>
* <span data-ttu-id="ff293-109">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="ff293-109">Command-line arguments</span></span>
* <span data-ttu-id="ff293-110">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="ff293-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="ff293-111">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="ff293-111">Directory files</span></span>
* <span data-ttu-id="ff293-112">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="ff293-112">Environment variables</span></span>
* <span data-ttu-id="ff293-113">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="ff293-113">In-memory .NET objects</span></span>
* <span data-ttu-id="ff293-114">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="ff293-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff293-115">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos implicitamente pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="ff293-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff293-116">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ff293-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="ff293-117">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="ff293-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="ff293-118">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="ff293-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="ff293-119">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="ff293-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="ff293-120">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="ff293-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="ff293-121">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff293-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="ff293-122">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="ff293-122">Host versus app configuration</span></span>

<span data-ttu-id="ff293-123">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="ff293-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="ff293-124">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="ff293-125">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="ff293-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="ff293-126">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="ff293-127">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="ff293-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="ff293-128">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="ff293-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff293-129">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="ff293-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="ff293-130">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="ff293-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="ff293-131">O seguinte se aplica a aplicativos que usam o [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="ff293-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="ff293-132">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="ff293-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="ff293-133">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="ff293-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="ff293-134">Variáveis ​​de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="ff293-135">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="ff293-136">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="ff293-137">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="ff293-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="ff293-138">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="ff293-139">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="ff293-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="ff293-140">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="ff293-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="ff293-141">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="ff293-141">Enable IIS integration.</span></span>
* <span data-ttu-id="ff293-142">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="ff293-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="ff293-143">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="ff293-144">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="ff293-145">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="ff293-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="ff293-146">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="ff293-147">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff293-148">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="ff293-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="ff293-149">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="ff293-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="ff293-150">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="ff293-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="ff293-151">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ff293-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="ff293-152">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="ff293-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="ff293-153">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="ff293-154">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="ff293-155">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="ff293-156">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="ff293-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="ff293-157">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="ff293-158">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="ff293-159">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="ff293-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="ff293-160">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="ff293-161">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ff293-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="ff293-162">Segurança</span><span class="sxs-lookup"><span data-stu-id="ff293-162">Security</span></span>

<span data-ttu-id="ff293-163">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="ff293-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="ff293-164">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="ff293-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="ff293-165">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ff293-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="ff293-166">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="ff293-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="ff293-167">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="ff293-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="ff293-168"><xref:security/app-secrets> &ndash; Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="ff293-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="ff293-169">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="ff293-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="ff293-170">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="ff293-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="ff293-171">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff293-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="ff293-172">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ff293-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="ff293-173">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="ff293-173">Hierarchical configuration data</span></span>

<span data-ttu-id="ff293-174">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="ff293-175">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="ff293-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="ff293-176">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="ff293-177">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="ff293-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="ff293-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-178">section0:key0</span></span>
* <span data-ttu-id="ff293-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-179">section0:key1</span></span>
* <span data-ttu-id="ff293-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-180">section1:key0</span></span>
* <span data-ttu-id="ff293-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-181">section1:key1</span></span>

<span data-ttu-id="ff293-182">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="ff293-183">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="ff293-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="ff293-184">Convenções</span><span class="sxs-lookup"><span data-stu-id="ff293-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="ff293-185">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="ff293-185">Sources and providers</span></span>

<span data-ttu-id="ff293-186">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="ff293-187">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="ff293-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="ff293-188">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="ff293-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="ff293-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ff293-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetado em um <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> do Razor Pages para obter a configuração para a classe:</span><span class="sxs-lookup"><span data-stu-id="ff293-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    // The _config local variable is used to obtain configuration 
    // throughout the class.
}
```

<span data-ttu-id="ff293-191">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="ff293-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="ff293-192">Novas</span><span class="sxs-lookup"><span data-stu-id="ff293-192">Keys</span></span>

<span data-ttu-id="ff293-193">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="ff293-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="ff293-194">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ff293-194">Keys are case-insensitive.</span></span> <span data-ttu-id="ff293-195">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="ff293-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="ff293-196">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="ff293-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="ff293-197">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="ff293-197">Hierarchical keys</span></span>
  * <span data-ttu-id="ff293-198">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="ff293-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="ff293-199">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="ff293-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="ff293-200">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="ff293-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="ff293-201">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="ff293-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="ff293-202">Você deve fornecer o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="ff293-203">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ff293-204">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="ff293-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="ff293-205">Valores</span><span class="sxs-lookup"><span data-stu-id="ff293-205">Values</span></span>

<span data-ttu-id="ff293-206">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="ff293-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="ff293-207">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="ff293-207">Values are strings.</span></span>
* <span data-ttu-id="ff293-208">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="ff293-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="ff293-209">Provedores</span><span class="sxs-lookup"><span data-stu-id="ff293-209">Providers</span></span>

<span data-ttu-id="ff293-210">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff293-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="ff293-211">Provider</span><span class="sxs-lookup"><span data-stu-id="ff293-211">Provider</span></span> | <span data-ttu-id="ff293-212">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="ff293-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="ff293-213">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="ff293-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="ff293-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ff293-214">Azure Key Vault</span></span> |
| <span data-ttu-id="ff293-215">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="ff293-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="ff293-216">Configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="ff293-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="ff293-217">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="ff293-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="ff293-218">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="ff293-218">Command-line parameters</span></span> |
| [<span data-ttu-id="ff293-219">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="ff293-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="ff293-220">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="ff293-220">Custom source</span></span> |
| [<span data-ttu-id="ff293-221">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="ff293-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="ff293-222">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="ff293-222">Environment variables</span></span> |
| [<span data-ttu-id="ff293-223">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="ff293-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="ff293-224">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="ff293-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="ff293-225">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="ff293-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="ff293-226">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="ff293-226">Directory files</span></span> |
| [<span data-ttu-id="ff293-227">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="ff293-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="ff293-228">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="ff293-228">In-memory collections</span></span> |
| <span data-ttu-id="ff293-229">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="ff293-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="ff293-230">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="ff293-230">File in the user profile directory</span></span> |

<span data-ttu-id="ff293-231">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="ff293-232">Os provedores de configuração descritos neste tópico estão descritos em ordem alfabética, não na ordem na qual seu código pode organizá-los.</span><span class="sxs-lookup"><span data-stu-id="ff293-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="ff293-233">Organize os provedores de configuração em seu código para atender às suas prioridades para as fontes de configuração subjacentes.</span><span class="sxs-lookup"><span data-stu-id="ff293-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="ff293-234">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="ff293-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="ff293-235">Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="ff293-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="ff293-236">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ff293-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="ff293-237">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="ff293-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="ff293-238">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="ff293-238">Environment variables</span></span>
1. <span data-ttu-id="ff293-239">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="ff293-239">Command-line arguments</span></span>

<span data-ttu-id="ff293-240">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="ff293-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="ff293-241">A sequência anterior de provedores é usada quando você inicializa um novo construtor de host com o `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ff293-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ff293-242">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ff293-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="ff293-243">Configurar o construtor de host com ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="ff293-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="ff293-244">Para configurar o construtor de host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> e forneça a configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="ff293-245">`ConfigureHostConfiguration` é usado para inicializar o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> para uso posterior no processo de build.</span><span class="sxs-lookup"><span data-stu-id="ff293-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="ff293-246">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="ff293-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="ff293-247">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="ff293-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="ff293-248">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="ff293-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="ff293-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="ff293-250">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ff293-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="ff293-251">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="ff293-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="ff293-252">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="ff293-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="ff293-253">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="ff293-253">Consume configuration during app startup</span></span>

<span data-ttu-id="ff293-254">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ff293-254">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ff293-255">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="ff293-255">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="ff293-256">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="ff293-256">Command-line Configuration Provider</span></span>

<span data-ttu-id="ff293-257">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ff293-257">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="ff293-258">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff293-258">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff293-259">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="ff293-259">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="ff293-260">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ff293-260">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ff293-261">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="ff293-261">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="ff293-262">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ff293-262">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="ff293-263">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ff293-263">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ff293-264">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ff293-264">Environment variables.</span></span>

<span data-ttu-id="ff293-265">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="ff293-265">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="ff293-266">Os argumentos de linha de comando passados em tempo de execução substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="ff293-266">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="ff293-267">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="ff293-267">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="ff293-268">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="ff293-268">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="ff293-269">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ff293-269">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ff293-270">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="ff293-270">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="ff293-271">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="ff293-271">**Example**</span></span>

<span data-ttu-id="ff293-272">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="ff293-272">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="ff293-273">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="ff293-273">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="ff293-274">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="ff293-274">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="ff293-275">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ff293-275">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="ff293-276">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ff293-276">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="ff293-277">Arguments</span><span class="sxs-lookup"><span data-stu-id="ff293-277">Arguments</span></span>

<span data-ttu-id="ff293-278">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="ff293-278">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="ff293-279">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="ff293-279">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="ff293-280">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="ff293-280">Key prefix</span></span>               | <span data-ttu-id="ff293-281">Exemplo</span><span class="sxs-lookup"><span data-stu-id="ff293-281">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="ff293-282">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="ff293-282">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="ff293-283">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="ff293-283">Two dashes (`--`)</span></span>        | <span data-ttu-id="ff293-284">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="ff293-284">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="ff293-285">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="ff293-285">Forward slash (`/`)</span></span>      | <span data-ttu-id="ff293-286">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="ff293-286">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="ff293-287">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="ff293-287">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="ff293-288">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="ff293-288">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="ff293-289">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="ff293-289">Switch mappings</span></span>

<span data-ttu-id="ff293-290">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="ff293-290">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="ff293-291">Quando você cria manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, pode fornecer um dicionário de substituições de opções para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="ff293-291">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="ff293-292">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="ff293-292">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="ff293-293">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-293">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="ff293-294">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="ff293-294">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="ff293-295">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="ff293-295">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="ff293-296">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="ff293-296">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="ff293-297">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="ff293-297">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="ff293-298">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="ff293-298">Create a switch mappings dictionary.</span></span> <span data-ttu-id="ff293-299">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="ff293-299">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="ff293-300">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="ff293-300">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="ff293-301">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="ff293-301">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="ff293-302">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ff293-302">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ff293-303">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="ff293-303">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="ff293-304">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="ff293-304">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="ff293-305">Chave</span><span class="sxs-lookup"><span data-stu-id="ff293-305">Key</span></span>       | <span data-ttu-id="ff293-306">Valor</span><span class="sxs-lookup"><span data-stu-id="ff293-306">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="ff293-307">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="ff293-307">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="ff293-308">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="ff293-308">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="ff293-309">Chave</span><span class="sxs-lookup"><span data-stu-id="ff293-309">Key</span></span>               | <span data-ttu-id="ff293-310">Valor</span><span class="sxs-lookup"><span data-stu-id="ff293-310">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="ff293-311">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="ff293-311">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="ff293-312">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ff293-312">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="ff293-313">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff293-313">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="ff293-314">O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) permite que você defina variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o Provedor de Configuração de Variáveis de Ambiente.</span><span class="sxs-lookup"><span data-stu-id="ff293-314">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="ff293-315">Para obter mais informações, confira [Aplicativos Azure: Substituir a configuração do aplicativo no Portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="ff293-315">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff293-316">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `DOTNET_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host genérico](xref:fundamentals/host/generic-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="ff293-316">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="ff293-317">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ff293-317">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff293-318">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="ff293-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="ff293-319">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ff293-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="ff293-320">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="ff293-320">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="ff293-321">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="ff293-321">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="ff293-322">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ff293-322">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="ff293-323">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ff293-323">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ff293-324">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="ff293-324">Command-line arguments.</span></span>

<span data-ttu-id="ff293-325">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="ff293-325">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="ff293-326">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em tempo de execução para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="ff293-326">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="ff293-327">Se precisar fornecer configuração do aplicativo com base em variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no `ConfigureAppConfiguration`e chame `AddEnvironmentVariables` com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="ff293-327">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call additional providers here as needed.
    // Call AddEnvironmentVariables last if you need to allow
    // environment variables to override values from other 
    // providers.
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
}
```

<span data-ttu-id="ff293-328">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="ff293-328">**Example**</span></span>

<span data-ttu-id="ff293-329">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="ff293-329">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="ff293-330">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="ff293-330">Run the sample app.</span></span> <span data-ttu-id="ff293-331">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ff293-331">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="ff293-332">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="ff293-332">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="ff293-333">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="ff293-333">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="ff293-334">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ff293-334">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="ff293-335">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="ff293-335">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="ff293-336">Se você quiser expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *Pages/Index.cshtml.cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="ff293-336">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="ff293-337">Prefixos</span><span class="sxs-lookup"><span data-stu-id="ff293-337">Prefixes</span></span>

<span data-ttu-id="ff293-338">Variáveis de ambiente carregadas na configuração do aplicativo são filtradas quando você fornece um prefixo para o método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="ff293-338">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="ff293-339">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="ff293-339">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="ff293-340">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-340">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="ff293-341">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ff293-341">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="ff293-342">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ff293-342">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ff293-343">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="ff293-343">**Connection string prefixes**</span></span>

<span data-ttu-id="ff293-344">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-344">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="ff293-345">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="ff293-345">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="ff293-346">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="ff293-346">Connection string prefix</span></span> | <span data-ttu-id="ff293-347">Provider</span><span class="sxs-lookup"><span data-stu-id="ff293-347">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="ff293-348">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="ff293-348">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="ff293-349">MySQL</span><span class="sxs-lookup"><span data-stu-id="ff293-349">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="ff293-350">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="ff293-350">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="ff293-351">SQL Server</span><span class="sxs-lookup"><span data-stu-id="ff293-351">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="ff293-352">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="ff293-352">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="ff293-353">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="ff293-353">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="ff293-354">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="ff293-354">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="ff293-355">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="ff293-355">Environment variable key</span></span> | <span data-ttu-id="ff293-356">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="ff293-356">Converted configuration key</span></span> | <span data-ttu-id="ff293-357">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="ff293-357">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="ff293-358">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="ff293-358">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="ff293-359">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ff293-359">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="ff293-360">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="ff293-360">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="ff293-361">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ff293-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="ff293-362">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ff293-362">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="ff293-363">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="ff293-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="ff293-364">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="ff293-364">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="ff293-365">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="ff293-365">File Configuration Provider</span></span>

<span data-ttu-id="ff293-366"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="ff293-366"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="ff293-367">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="ff293-367">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="ff293-368">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="ff293-368">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="ff293-369">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="ff293-369">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="ff293-370">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="ff293-370">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="ff293-371">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="ff293-371">INI Configuration Provider</span></span>

<span data-ttu-id="ff293-372">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ff293-372">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="ff293-373">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff293-373">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff293-374">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="ff293-374">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="ff293-375">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="ff293-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="ff293-376">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="ff293-376">Whether the file is optional.</span></span>
* <span data-ttu-id="ff293-377">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="ff293-377">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="ff293-378">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="ff293-378">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="ff293-379">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ff293-379">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="ff293-380">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="ff293-380">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="ff293-381">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="ff293-381">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="ff293-382">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="ff293-382">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ff293-383">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-383">section0:key0</span></span>
* <span data-ttu-id="ff293-384">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-384">section0:key1</span></span>
* <span data-ttu-id="ff293-385">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="ff293-385">section1:subsection:key</span></span>
* <span data-ttu-id="ff293-386">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="ff293-386">section2:subsection0:key</span></span>
* <span data-ttu-id="ff293-387">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="ff293-387">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="ff293-388">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="ff293-388">JSON Configuration Provider</span></span>

<span data-ttu-id="ff293-389">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ff293-389">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="ff293-390">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff293-390">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff293-391">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="ff293-391">Overloads permit specifying:</span></span>

* <span data-ttu-id="ff293-392">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="ff293-392">Whether the file is optional.</span></span>
* <span data-ttu-id="ff293-393">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="ff293-393">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="ff293-394">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="ff293-394">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="ff293-395">`AddJsonFile` é chamado automaticamente duas vezes quando você inicializa um novo construtor de host com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ff293-395">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ff293-396">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="ff293-396">The method is called to load configuration from:</span></span>

* <span data-ttu-id="ff293-397">*appsettings.json* &ndash; Esse arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="ff293-397">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="ff293-398">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ff293-398">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="ff293-399">*appsettings.{Environment}.json* &ndash; A versão de ambiente do arquivo é carregada com base em [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="ff293-399">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="ff293-400">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="ff293-400">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="ff293-401">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="ff293-401">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="ff293-402">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="ff293-402">Environment variables.</span></span>
* <span data-ttu-id="ff293-403">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ff293-403">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="ff293-404">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="ff293-404">Command-line arguments.</span></span>

<span data-ttu-id="ff293-405">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="ff293-405">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="ff293-406">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="ff293-406">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="ff293-407">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="ff293-407">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="ff293-408">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="ff293-408">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="ff293-409">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="ff293-409">**Example**</span></span>

<span data-ttu-id="ff293-410">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="ff293-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="ff293-411">A configuração é carregada de *appsettings.json* e de *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="ff293-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="ff293-412">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="ff293-412">Run the sample app.</span></span> <span data-ttu-id="ff293-413">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ff293-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="ff293-414">Observe que a saída contém pares chave-valor para a configuração mostrada na tabela de acordo com o ambiente.</span><span class="sxs-lookup"><span data-stu-id="ff293-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="ff293-415">Chaves de configuração de registro usam os dois-pontos (`:`) como um separador hierárquico.</span><span class="sxs-lookup"><span data-stu-id="ff293-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="ff293-416">Chave</span><span class="sxs-lookup"><span data-stu-id="ff293-416">Key</span></span>                        | <span data-ttu-id="ff293-417">Valor de Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="ff293-417">Development Value</span></span> | <span data-ttu-id="ff293-418">Valor de Produção</span><span class="sxs-lookup"><span data-stu-id="ff293-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="ff293-419">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="ff293-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="ff293-420">Informações</span><span class="sxs-lookup"><span data-stu-id="ff293-420">Information</span></span>       | <span data-ttu-id="ff293-421">Informações</span><span class="sxs-lookup"><span data-stu-id="ff293-421">Information</span></span>      |
| <span data-ttu-id="ff293-422">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="ff293-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="ff293-423">Informações</span><span class="sxs-lookup"><span data-stu-id="ff293-423">Information</span></span>       | <span data-ttu-id="ff293-424">Informações</span><span class="sxs-lookup"><span data-stu-id="ff293-424">Information</span></span>      |
| <span data-ttu-id="ff293-425">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="ff293-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="ff293-426">Depurar</span><span class="sxs-lookup"><span data-stu-id="ff293-426">Debug</span></span>             | <span data-ttu-id="ff293-427">Erro</span><span class="sxs-lookup"><span data-stu-id="ff293-427">Error</span></span>            |
| <span data-ttu-id="ff293-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="ff293-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="ff293-429">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="ff293-429">XML Configuration Provider</span></span>

<span data-ttu-id="ff293-430">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ff293-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="ff293-431">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff293-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff293-432">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="ff293-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="ff293-433">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="ff293-433">Whether the file is optional.</span></span>
* <span data-ttu-id="ff293-434">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="ff293-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="ff293-435">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="ff293-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="ff293-436">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="ff293-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="ff293-437">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="ff293-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="ff293-438">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ff293-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="ff293-439">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="ff293-439">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="ff293-440">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="ff293-440">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="ff293-441">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="ff293-441">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ff293-442">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-442">section0:key0</span></span>
* <span data-ttu-id="ff293-443">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-443">section0:key1</span></span>
* <span data-ttu-id="ff293-444">section1:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-444">section1:key0</span></span>
* <span data-ttu-id="ff293-445">section1:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-445">section1:key1</span></span>

<span data-ttu-id="ff293-446">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="ff293-446">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="ff293-447">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="ff293-447">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ff293-448">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-448">section:section0:key:key0</span></span>
* <span data-ttu-id="ff293-449">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-449">section:section0:key:key1</span></span>
* <span data-ttu-id="ff293-450">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-450">section:section1:key:key0</span></span>
* <span data-ttu-id="ff293-451">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-451">section:section1:key:key1</span></span>

<span data-ttu-id="ff293-452">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="ff293-452">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="ff293-453">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="ff293-453">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="ff293-454">key:attribute</span><span class="sxs-lookup"><span data-stu-id="ff293-454">key:attribute</span></span>
* <span data-ttu-id="ff293-455">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="ff293-455">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="ff293-456">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="ff293-456">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="ff293-457">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-457">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="ff293-458">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="ff293-458">The key is the file name.</span></span> <span data-ttu-id="ff293-459">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="ff293-459">The value contains the file's contents.</span></span> <span data-ttu-id="ff293-460">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="ff293-460">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="ff293-461">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff293-461">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="ff293-462">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="ff293-462">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="ff293-463">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="ff293-463">Overloads permit specifying:</span></span>

* <span data-ttu-id="ff293-464">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="ff293-464">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="ff293-465">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="ff293-465">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="ff293-466">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="ff293-466">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="ff293-467">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="ff293-467">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="ff293-468">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ff293-468">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<span data-ttu-id="ff293-469">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="ff293-469">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

## <a name="memory-configuration-provider"></a><span data-ttu-id="ff293-470">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="ff293-470">Memory Configuration Provider</span></span>

<span data-ttu-id="ff293-471">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-471">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="ff293-472">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff293-472">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="ff293-473">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="ff293-473">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="ff293-474">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-474">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="ff293-475">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="ff293-475">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="ff293-476">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="ff293-476">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="ff293-477">GetValue</span><span class="sxs-lookup"><span data-stu-id="ff293-477">GetValue</span></span>

<span data-ttu-id="ff293-478">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um valor de configuração com uma chave especificada e o converte para o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="ff293-478">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="ff293-479">Uma sobrecarga permite que você forneça um valor padrão se a chave não for encontrada.</span><span class="sxs-lookup"><span data-stu-id="ff293-479">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="ff293-480">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ff293-480">The following example:</span></span>

* <span data-ttu-id="ff293-481">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="ff293-481">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="ff293-482">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="ff293-482">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="ff293-483">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="ff293-483">Types the value as an `int`.</span></span>
* <span data-ttu-id="ff293-484">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="ff293-484">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="ff293-485">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="ff293-485">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="ff293-486">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="ff293-486">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="ff293-487">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="ff293-487">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="ff293-488">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="ff293-488">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="ff293-489">section0:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-489">section0:key0</span></span>
* <span data-ttu-id="ff293-490">section0:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-490">section0:key1</span></span>
* <span data-ttu-id="ff293-491">section1:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-491">section1:key0</span></span>
* <span data-ttu-id="ff293-492">section1:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-492">section1:key1</span></span>
* <span data-ttu-id="ff293-493">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-493">section2:subsection0:key0</span></span>
* <span data-ttu-id="ff293-494">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-494">section2:subsection0:key1</span></span>
* <span data-ttu-id="ff293-495">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="ff293-495">section2:subsection1:key0</span></span>
* <span data-ttu-id="ff293-496">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="ff293-496">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="ff293-497">GetSection</span><span class="sxs-lookup"><span data-stu-id="ff293-497">GetSection</span></span>

<span data-ttu-id="ff293-498">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="ff293-498">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="ff293-499">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="ff293-499">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="ff293-500">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="ff293-500">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="ff293-501">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="ff293-501">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="ff293-502">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="ff293-502">`GetSection` never returns `null`.</span></span> <span data-ttu-id="ff293-503">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="ff293-503">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="ff293-504">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="ff293-504">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="ff293-505"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="ff293-505">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="ff293-506">GetChildren</span><span class="sxs-lookup"><span data-stu-id="ff293-506">GetChildren</span></span>

<span data-ttu-id="ff293-507">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="ff293-507">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="ff293-508">Exists</span><span class="sxs-lookup"><span data-stu-id="ff293-508">Exists</span></span>

<span data-ttu-id="ff293-509">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="ff293-509">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="ff293-510">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-510">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="ff293-511">Associar a uma classe</span><span class="sxs-lookup"><span data-stu-id="ff293-511">Bind to a class</span></span>

<span data-ttu-id="ff293-512">A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*.</span><span class="sxs-lookup"><span data-stu-id="ff293-512">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="ff293-513">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="ff293-513">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="ff293-514">Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="ff293-514">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="ff293-515">O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="ff293-515">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="ff293-516">A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:</span><span class="sxs-lookup"><span data-stu-id="ff293-516">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="ff293-517">Os seguintes pares chave-valor de configuração são criados:</span><span class="sxs-lookup"><span data-stu-id="ff293-517">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="ff293-518">Chave</span><span class="sxs-lookup"><span data-stu-id="ff293-518">Key</span></span>                   | <span data-ttu-id="ff293-519">Valor</span><span class="sxs-lookup"><span data-stu-id="ff293-519">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="ff293-520">starship:name</span><span class="sxs-lookup"><span data-stu-id="ff293-520">starship:name</span></span>         | <span data-ttu-id="ff293-521">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="ff293-521">USS Enterprise</span></span>                                    |
| <span data-ttu-id="ff293-522">starship:registry</span><span class="sxs-lookup"><span data-stu-id="ff293-522">starship:registry</span></span>     | <span data-ttu-id="ff293-523">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="ff293-523">NCC-1701</span></span>                                          |
| <span data-ttu-id="ff293-524">starship:class</span><span class="sxs-lookup"><span data-stu-id="ff293-524">starship:class</span></span>        | <span data-ttu-id="ff293-525">Constituição</span><span class="sxs-lookup"><span data-stu-id="ff293-525">Constitution</span></span>                                      |
| <span data-ttu-id="ff293-526">starship:length</span><span class="sxs-lookup"><span data-stu-id="ff293-526">starship:length</span></span>       | <span data-ttu-id="ff293-527">304,8</span><span class="sxs-lookup"><span data-stu-id="ff293-527">304.8</span></span>                                             |
| <span data-ttu-id="ff293-528">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="ff293-528">starship:commissioned</span></span> | <span data-ttu-id="ff293-529">False</span><span class="sxs-lookup"><span data-stu-id="ff293-529">False</span></span>                                             |
| <span data-ttu-id="ff293-530">marca</span><span class="sxs-lookup"><span data-stu-id="ff293-530">trademark</span></span>             | <span data-ttu-id="ff293-531">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="ff293-531">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="ff293-532">O aplicativo de exemplo chama `GetSection` com a chave `starship`.</span><span class="sxs-lookup"><span data-stu-id="ff293-532">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="ff293-533">Os pares chave-valor `starship` são isolados.</span><span class="sxs-lookup"><span data-stu-id="ff293-533">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="ff293-534">O método `Bind` é chamado na subseção passando uma instância da classe `Starship`.</span><span class="sxs-lookup"><span data-stu-id="ff293-534">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="ff293-535">Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="ff293-535">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="ff293-536">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="ff293-536">Bind to an object graph</span></span>

<span data-ttu-id="ff293-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="ff293-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="ff293-538">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="ff293-538">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="ff293-539">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="ff293-539">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="ff293-540">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="ff293-540">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="ff293-541">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="ff293-541">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="ff293-542">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="ff293-542">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="ff293-543">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="ff293-543">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="ff293-544">O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:</span><span class="sxs-lookup"><span data-stu-id="ff293-544">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="ff293-545">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="ff293-545">Bind an array to a class</span></span>

<span data-ttu-id="ff293-546">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="ff293-546">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="ff293-547">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-547">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="ff293-548">Qualquer formato de matriz que exponha um segmento de chave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar matrizes a uma matriz de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="ff293-548">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="ff293-549">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="ff293-549">Binding is provided by convention.</span></span> <span data-ttu-id="ff293-550">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="ff293-550">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="ff293-551">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="ff293-551">**In-memory array processing**</span></span>

<span data-ttu-id="ff293-552">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="ff293-552">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="ff293-553">Chave</span><span class="sxs-lookup"><span data-stu-id="ff293-553">Key</span></span>             | <span data-ttu-id="ff293-554">Valor</span><span class="sxs-lookup"><span data-stu-id="ff293-554">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="ff293-555">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="ff293-555">array:entries:0</span></span> | <span data-ttu-id="ff293-556">value0</span><span class="sxs-lookup"><span data-stu-id="ff293-556">value0</span></span> |
| <span data-ttu-id="ff293-557">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="ff293-557">array:entries:1</span></span> | <span data-ttu-id="ff293-558">value1</span><span class="sxs-lookup"><span data-stu-id="ff293-558">value1</span></span> |
| <span data-ttu-id="ff293-559">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="ff293-559">array:entries:2</span></span> | <span data-ttu-id="ff293-560">value2</span><span class="sxs-lookup"><span data-stu-id="ff293-560">value2</span></span> |
| <span data-ttu-id="ff293-561">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="ff293-561">array:entries:4</span></span> | <span data-ttu-id="ff293-562">value4</span><span class="sxs-lookup"><span data-stu-id="ff293-562">value4</span></span> |
| <span data-ttu-id="ff293-563">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="ff293-563">array:entries:5</span></span> | <span data-ttu-id="ff293-564">value5</span><span class="sxs-lookup"><span data-stu-id="ff293-564">value5</span></span> |

<span data-ttu-id="ff293-565">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="ff293-565">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

<span data-ttu-id="ff293-566">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="ff293-566">The array skips a value for index &num;3.</span></span> <span data-ttu-id="ff293-567">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="ff293-567">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="ff293-568">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="ff293-568">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="ff293-569">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="ff293-569">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="ff293-570">A sintaxe [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="ff293-570">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="ff293-571">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-571">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="ff293-572">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="ff293-572">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="ff293-573">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="ff293-573">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="ff293-574">0</span><span class="sxs-lookup"><span data-stu-id="ff293-574">0</span></span>                            | <span data-ttu-id="ff293-575">value0</span><span class="sxs-lookup"><span data-stu-id="ff293-575">value0</span></span>                       |
| <span data-ttu-id="ff293-576">1</span><span class="sxs-lookup"><span data-stu-id="ff293-576">1</span></span>                            | <span data-ttu-id="ff293-577">value1</span><span class="sxs-lookup"><span data-stu-id="ff293-577">value1</span></span>                       |
| <span data-ttu-id="ff293-578">2</span><span class="sxs-lookup"><span data-stu-id="ff293-578">2</span></span>                            | <span data-ttu-id="ff293-579">value2</span><span class="sxs-lookup"><span data-stu-id="ff293-579">value2</span></span>                       |
| <span data-ttu-id="ff293-580">3</span><span class="sxs-lookup"><span data-stu-id="ff293-580">3</span></span>                            | <span data-ttu-id="ff293-581">value4</span><span class="sxs-lookup"><span data-stu-id="ff293-581">value4</span></span>                       |
| <span data-ttu-id="ff293-582">4</span><span class="sxs-lookup"><span data-stu-id="ff293-582">4</span></span>                            | <span data-ttu-id="ff293-583">value5</span><span class="sxs-lookup"><span data-stu-id="ff293-583">value5</span></span>                       |

<span data-ttu-id="ff293-584">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="ff293-584">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="ff293-585">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="ff293-585">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="ff293-586">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="ff293-586">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="ff293-587">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-587">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="ff293-588">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="ff293-588">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="ff293-589">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="ff293-589">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="ff293-590">No `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="ff293-590">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="ff293-591">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-591">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="ff293-592">Chave</span><span class="sxs-lookup"><span data-stu-id="ff293-592">Key</span></span>             | <span data-ttu-id="ff293-593">Valor</span><span class="sxs-lookup"><span data-stu-id="ff293-593">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="ff293-594">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="ff293-594">array:entries:3</span></span> | <span data-ttu-id="ff293-595">value3</span><span class="sxs-lookup"><span data-stu-id="ff293-595">value3</span></span> |

<span data-ttu-id="ff293-596">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="ff293-596">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="ff293-597">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="ff293-597">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="ff293-598">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="ff293-598">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="ff293-599">0</span><span class="sxs-lookup"><span data-stu-id="ff293-599">0</span></span>                            | <span data-ttu-id="ff293-600">value0</span><span class="sxs-lookup"><span data-stu-id="ff293-600">value0</span></span>                       |
| <span data-ttu-id="ff293-601">1</span><span class="sxs-lookup"><span data-stu-id="ff293-601">1</span></span>                            | <span data-ttu-id="ff293-602">value1</span><span class="sxs-lookup"><span data-stu-id="ff293-602">value1</span></span>                       |
| <span data-ttu-id="ff293-603">2</span><span class="sxs-lookup"><span data-stu-id="ff293-603">2</span></span>                            | <span data-ttu-id="ff293-604">value2</span><span class="sxs-lookup"><span data-stu-id="ff293-604">value2</span></span>                       |
| <span data-ttu-id="ff293-605">3</span><span class="sxs-lookup"><span data-stu-id="ff293-605">3</span></span>                            | <span data-ttu-id="ff293-606">value3</span><span class="sxs-lookup"><span data-stu-id="ff293-606">value3</span></span>                       |
| <span data-ttu-id="ff293-607">4</span><span class="sxs-lookup"><span data-stu-id="ff293-607">4</span></span>                            | <span data-ttu-id="ff293-608">value4</span><span class="sxs-lookup"><span data-stu-id="ff293-608">value4</span></span>                       |
| <span data-ttu-id="ff293-609">5</span><span class="sxs-lookup"><span data-stu-id="ff293-609">5</span></span>                            | <span data-ttu-id="ff293-610">value5</span><span class="sxs-lookup"><span data-stu-id="ff293-610">value5</span></span>                       |

<span data-ttu-id="ff293-611">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="ff293-611">**JSON array processing**</span></span>

<span data-ttu-id="ff293-612">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="ff293-612">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="ff293-613">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="ff293-613">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="ff293-614">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="ff293-614">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="ff293-615">Chave</span><span class="sxs-lookup"><span data-stu-id="ff293-615">Key</span></span>                     | <span data-ttu-id="ff293-616">Valor</span><span class="sxs-lookup"><span data-stu-id="ff293-616">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="ff293-617">json_array:key</span><span class="sxs-lookup"><span data-stu-id="ff293-617">json_array:key</span></span>          | <span data-ttu-id="ff293-618">valueA</span><span class="sxs-lookup"><span data-stu-id="ff293-618">valueA</span></span> |
| <span data-ttu-id="ff293-619">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="ff293-619">json_array:subsection:0</span></span> | <span data-ttu-id="ff293-620">valueB</span><span class="sxs-lookup"><span data-stu-id="ff293-620">valueB</span></span> |
| <span data-ttu-id="ff293-621">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="ff293-621">json_array:subsection:1</span></span> | <span data-ttu-id="ff293-622">valueC</span><span class="sxs-lookup"><span data-stu-id="ff293-622">valueC</span></span> |
| <span data-ttu-id="ff293-623">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="ff293-623">json_array:subsection:2</span></span> | <span data-ttu-id="ff293-624">valueD</span><span class="sxs-lookup"><span data-stu-id="ff293-624">valueD</span></span> |

<span data-ttu-id="ff293-625">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="ff293-625">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="ff293-626">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="ff293-626">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="ff293-627">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="ff293-627">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="ff293-628">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="ff293-628">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="ff293-629">Valor `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="ff293-629">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="ff293-630">0</span><span class="sxs-lookup"><span data-stu-id="ff293-630">0</span></span>                                   | <span data-ttu-id="ff293-631">valueB</span><span class="sxs-lookup"><span data-stu-id="ff293-631">valueB</span></span>                              |
| <span data-ttu-id="ff293-632">1</span><span class="sxs-lookup"><span data-stu-id="ff293-632">1</span></span>                                   | <span data-ttu-id="ff293-633">valueC</span><span class="sxs-lookup"><span data-stu-id="ff293-633">valueC</span></span>                              |
| <span data-ttu-id="ff293-634">2</span><span class="sxs-lookup"><span data-stu-id="ff293-634">2</span></span>                                   | <span data-ttu-id="ff293-635">valueD</span><span class="sxs-lookup"><span data-stu-id="ff293-635">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="ff293-636">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="ff293-636">Custom configuration provider</span></span>

<span data-ttu-id="ff293-637">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="ff293-637">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="ff293-638">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="ff293-638">The provider has the following characteristics:</span></span>

* <span data-ttu-id="ff293-639">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="ff293-639">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="ff293-640">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="ff293-640">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="ff293-641">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="ff293-641">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="ff293-642">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="ff293-642">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="ff293-643">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-643">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="ff293-644">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ff293-644">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff293-645">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-645">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="ff293-646">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="ff293-646">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="ff293-647">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-647">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="ff293-648">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="ff293-648">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="ff293-649">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-649">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="ff293-650">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="ff293-650">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="ff293-651">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="ff293-651">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="ff293-652">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-652">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="ff293-653">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ff293-653">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="ff293-654">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-654">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="ff293-655">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-655">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff293-656">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-656">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="ff293-657">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="ff293-657">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="ff293-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="ff293-659">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="ff293-659">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="ff293-660">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-660">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="ff293-661">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="ff293-661">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="ff293-662">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="ff293-662">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="ff293-663">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-663">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="ff293-664">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ff293-664">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="ff293-665">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-665">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="ff293-666">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff293-666">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="ff293-667">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="ff293-667">Access configuration during startup</span></span>

<span data-ttu-id="ff293-668">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ff293-668">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ff293-669">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="ff293-669">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="ff293-670">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, confira [Inicialização do aplicativo: Métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="ff293-670">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="ff293-671">Acessar a configuração em uma página do Razor Pages ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="ff293-671">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="ff293-672">Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.</span><span class="sxs-lookup"><span data-stu-id="ff293-672">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="ff293-673">Em uma página do Razor:</span><span class="sxs-lookup"><span data-stu-id="ff293-673">In a Razor Pages page:</span></span>

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

<span data-ttu-id="ff293-674">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="ff293-674">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="ff293-675">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="ff293-675">Add configuration from an external assembly</span></span>

<span data-ttu-id="ff293-676">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ff293-676">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="ff293-677">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ff293-677">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff293-678">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ff293-678">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
