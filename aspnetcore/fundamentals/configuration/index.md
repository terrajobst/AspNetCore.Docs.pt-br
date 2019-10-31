---
title: Configuração no ASP.NET Core
author: guardrex
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: c63609cfb91a1668b8e125c54fcfecf5f4ec259b
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143353"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="eaa55-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaa55-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="eaa55-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eaa55-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="eaa55-105">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="eaa55-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="eaa55-106">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="eaa55-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="eaa55-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="eaa55-107">Azure Key Vault</span></span>
* <span data-ttu-id="eaa55-108">Configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="eaa55-108">Azure App Configuration</span></span>
* <span data-ttu-id="eaa55-109">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="eaa55-109">Command-line arguments</span></span>
* <span data-ttu-id="eaa55-110">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="eaa55-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="eaa55-111">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="eaa55-111">Directory files</span></span>
* <span data-ttu-id="eaa55-112">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="eaa55-112">Environment variables</span></span>
* <span data-ttu-id="eaa55-113">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="eaa55-113">In-memory .NET objects</span></span>
* <span data-ttu-id="eaa55-114">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="eaa55-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eaa55-115">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos implicitamente pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="eaa55-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eaa55-116">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="eaa55-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="eaa55-117">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="eaa55-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="eaa55-118">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="eaa55-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="eaa55-119">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="eaa55-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="eaa55-120">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="eaa55-121">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eaa55-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="eaa55-122">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="eaa55-122">Host versus app configuration</span></span>

<span data-ttu-id="eaa55-123">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="eaa55-124">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="eaa55-125">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="eaa55-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="eaa55-126">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="eaa55-127">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="eaa55-128">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="eaa55-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eaa55-129">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="eaa55-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="eaa55-130">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="eaa55-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="eaa55-131">O seguinte se aplica a aplicativos que usam o [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="eaa55-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="eaa55-132">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="eaa55-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="eaa55-133">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="eaa55-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="eaa55-134">Variáveis ​​de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="eaa55-135">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="eaa55-136">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="eaa55-137">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="eaa55-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="eaa55-138">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="eaa55-139">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="eaa55-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="eaa55-140">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="eaa55-141">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="eaa55-141">Enable IIS integration.</span></span>
* <span data-ttu-id="eaa55-142">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="eaa55-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="eaa55-143">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="eaa55-144">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="eaa55-145">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="eaa55-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="eaa55-146">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="eaa55-147">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eaa55-148">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="eaa55-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="eaa55-149">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="eaa55-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="eaa55-150">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="eaa55-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="eaa55-151">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="eaa55-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="eaa55-152">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="eaa55-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="eaa55-153">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="eaa55-154">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="eaa55-155">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="eaa55-156">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="eaa55-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="eaa55-157">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="eaa55-158">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="eaa55-159">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="eaa55-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="eaa55-160">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="eaa55-161">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa55-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="eaa55-162">Segurança</span><span class="sxs-lookup"><span data-stu-id="eaa55-162">Security</span></span>

<span data-ttu-id="eaa55-163">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="eaa55-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="eaa55-164">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="eaa55-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="eaa55-165">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="eaa55-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="eaa55-166">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="eaa55-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="eaa55-167">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="eaa55-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="eaa55-168"><xref:security/app-secrets> &ndash; Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="eaa55-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="eaa55-169">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="eaa55-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="eaa55-170">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="eaa55-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="eaa55-171">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaa55-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="eaa55-172">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="eaa55-173">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="eaa55-173">Hierarchical configuration data</span></span>

<span data-ttu-id="eaa55-174">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="eaa55-175">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="eaa55-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="eaa55-176">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="eaa55-177">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="eaa55-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="eaa55-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-178">section0:key0</span></span>
* <span data-ttu-id="eaa55-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-179">section0:key1</span></span>
* <span data-ttu-id="eaa55-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-180">section1:key0</span></span>
* <span data-ttu-id="eaa55-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-181">section1:key1</span></span>

<span data-ttu-id="eaa55-182">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="eaa55-183">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="eaa55-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="eaa55-184">Convenções</span><span class="sxs-lookup"><span data-stu-id="eaa55-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="eaa55-185">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="eaa55-185">Sources and providers</span></span>

<span data-ttu-id="eaa55-186">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="eaa55-187">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="eaa55-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="eaa55-188">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="eaa55-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="eaa55-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetado em um <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> do Razor Pages para obter a configuração para a classe:</span><span class="sxs-lookup"><span data-stu-id="eaa55-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

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

<span data-ttu-id="eaa55-191">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="eaa55-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="eaa55-192">Novas</span><span class="sxs-lookup"><span data-stu-id="eaa55-192">Keys</span></span>

<span data-ttu-id="eaa55-193">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="eaa55-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="eaa55-194">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="eaa55-194">Keys are case-insensitive.</span></span> <span data-ttu-id="eaa55-195">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="eaa55-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="eaa55-196">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="eaa55-197">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="eaa55-197">Hierarchical keys</span></span>
  * <span data-ttu-id="eaa55-198">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="eaa55-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="eaa55-199">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="eaa55-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="eaa55-200">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="eaa55-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="eaa55-201">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="eaa55-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="eaa55-202">Você deve fornecer o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="eaa55-203">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="eaa55-204">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="eaa55-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="eaa55-205">Valores</span><span class="sxs-lookup"><span data-stu-id="eaa55-205">Values</span></span>

<span data-ttu-id="eaa55-206">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="eaa55-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="eaa55-207">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="eaa55-207">Values are strings.</span></span>
* <span data-ttu-id="eaa55-208">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="eaa55-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="eaa55-209">Provedores</span><span class="sxs-lookup"><span data-stu-id="eaa55-209">Providers</span></span>

<span data-ttu-id="eaa55-210">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaa55-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="eaa55-211">Provider</span><span class="sxs-lookup"><span data-stu-id="eaa55-211">Provider</span></span> | <span data-ttu-id="eaa55-212">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="eaa55-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="eaa55-213">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="eaa55-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="eaa55-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="eaa55-214">Azure Key Vault</span></span> |
| <span data-ttu-id="eaa55-215">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="eaa55-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="eaa55-216">Configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="eaa55-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="eaa55-217">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="eaa55-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="eaa55-218">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="eaa55-218">Command-line parameters</span></span> |
| [<span data-ttu-id="eaa55-219">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="eaa55-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="eaa55-220">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="eaa55-220">Custom source</span></span> |
| [<span data-ttu-id="eaa55-221">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="eaa55-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="eaa55-222">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="eaa55-222">Environment variables</span></span> |
| [<span data-ttu-id="eaa55-223">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="eaa55-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="eaa55-224">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="eaa55-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="eaa55-225">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="eaa55-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="eaa55-226">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="eaa55-226">Directory files</span></span> |
| [<span data-ttu-id="eaa55-227">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="eaa55-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="eaa55-228">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="eaa55-228">In-memory collections</span></span> |
| <span data-ttu-id="eaa55-229">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="eaa55-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="eaa55-230">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="eaa55-230">File in the user profile directory</span></span> |

<span data-ttu-id="eaa55-231">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="eaa55-232">Os provedores de configuração descritos neste tópico estão descritos em ordem alfabética, não na ordem na qual seu código pode organizá-los.</span><span class="sxs-lookup"><span data-stu-id="eaa55-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="eaa55-233">Organize os provedores de configuração em seu código para atender às suas prioridades para as fontes de configuração subjacentes.</span><span class="sxs-lookup"><span data-stu-id="eaa55-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="eaa55-234">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="eaa55-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="eaa55-235">Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="eaa55-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="eaa55-236">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="eaa55-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="eaa55-237">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="eaa55-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="eaa55-238">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="eaa55-238">Environment variables</span></span>
1. <span data-ttu-id="eaa55-239">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="eaa55-239">Command-line arguments</span></span>

<span data-ttu-id="eaa55-240">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="eaa55-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="eaa55-241">A sequência anterior de provedores é usada quando você inicializa um novo construtor de host com o `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="eaa55-242">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa55-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="eaa55-243">Configurar o construtor de host com ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="eaa55-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="eaa55-244">Para configurar o construtor de host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> e forneça a configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="eaa55-245">`ConfigureHostConfiguration` é usado para inicializar o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> para uso posterior no processo de build.</span><span class="sxs-lookup"><span data-stu-id="eaa55-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="eaa55-246">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="eaa55-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="eaa55-247">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="eaa55-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="eaa55-248">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="eaa55-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="eaa55-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="eaa55-250">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="eaa55-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="eaa55-251">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="eaa55-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="eaa55-252">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="eaa55-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="eaa55-253">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="eaa55-253">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="eaa55-254">Para remover os provedores adicionados por `CreateDefaultBuilder`, chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="eaa55-254">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="eaa55-255">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="eaa55-255">Consume configuration during app startup</span></span>

<span data-ttu-id="eaa55-256">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-256">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eaa55-257">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="eaa55-257">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="eaa55-258">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="eaa55-258">Command-line Configuration Provider</span></span>

<span data-ttu-id="eaa55-259">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="eaa55-259">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa55-260">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-260">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa55-261">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-261">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="eaa55-262">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa55-262">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="eaa55-263">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="eaa55-263">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="eaa55-264">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa55-264">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="eaa55-265">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="eaa55-265">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="eaa55-266">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="eaa55-266">Environment variables.</span></span>

<span data-ttu-id="eaa55-267">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="eaa55-267">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="eaa55-268">Os argumentos de linha de comando passados em tempo de execução substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="eaa55-268">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="eaa55-269">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="eaa55-269">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="eaa55-270">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-270">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="eaa55-271">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-271">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="eaa55-272">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="eaa55-272">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="eaa55-273">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="eaa55-273">**Example**</span></span>

<span data-ttu-id="eaa55-274">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-274">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="eaa55-275">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="eaa55-275">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="eaa55-276">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-276">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="eaa55-277">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-277">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="eaa55-278">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-278">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="eaa55-279">Arguments</span><span class="sxs-lookup"><span data-stu-id="eaa55-279">Arguments</span></span>

<span data-ttu-id="eaa55-280">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="eaa55-280">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="eaa55-281">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="eaa55-281">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="eaa55-282">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="eaa55-282">Key prefix</span></span>               | <span data-ttu-id="eaa55-283">Exemplo</span><span class="sxs-lookup"><span data-stu-id="eaa55-283">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="eaa55-284">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="eaa55-284">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="eaa55-285">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="eaa55-285">Two dashes (`--`)</span></span>        | <span data-ttu-id="eaa55-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="eaa55-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="eaa55-287">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="eaa55-287">Forward slash (`/`)</span></span>      | <span data-ttu-id="eaa55-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="eaa55-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="eaa55-289">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="eaa55-289">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="eaa55-290">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="eaa55-290">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="eaa55-291">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="eaa55-291">Switch mappings</span></span>

<span data-ttu-id="eaa55-292">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="eaa55-292">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="eaa55-293">Quando você cria manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, pode fornecer um dicionário de substituições de opções para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-293">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="eaa55-294">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="eaa55-294">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="eaa55-295">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-295">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="eaa55-296">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="eaa55-296">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="eaa55-297">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="eaa55-297">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="eaa55-298">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="eaa55-298">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="eaa55-299">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="eaa55-299">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="eaa55-300">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="eaa55-300">Create a switch mappings dictionary.</span></span> <span data-ttu-id="eaa55-301">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="eaa55-301">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="eaa55-302">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="eaa55-302">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="eaa55-303">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="eaa55-303">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="eaa55-304">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-304">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="eaa55-305">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="eaa55-305">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="eaa55-306">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="eaa55-306">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="eaa55-307">Chave</span><span class="sxs-lookup"><span data-stu-id="eaa55-307">Key</span></span>       | <span data-ttu-id="eaa55-308">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa55-308">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="eaa55-309">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="eaa55-309">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="eaa55-310">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="eaa55-310">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="eaa55-311">Chave</span><span class="sxs-lookup"><span data-stu-id="eaa55-311">Key</span></span>               | <span data-ttu-id="eaa55-312">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa55-312">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="eaa55-313">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="eaa55-313">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="eaa55-314">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="eaa55-314">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa55-315">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-315">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="eaa55-316">O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) permite que você defina variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o Provedor de Configuração de Variáveis de Ambiente.</span><span class="sxs-lookup"><span data-stu-id="eaa55-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="eaa55-317">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="eaa55-317">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eaa55-318">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `DOTNET_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host genérico](xref:fundamentals/host/generic-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="eaa55-319">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa55-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eaa55-320">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-320">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="eaa55-321">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa55-321">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="eaa55-322">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="eaa55-322">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="eaa55-323">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-323">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="eaa55-324">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa55-324">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="eaa55-325">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="eaa55-325">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="eaa55-326">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="eaa55-326">Command-line arguments.</span></span>

<span data-ttu-id="eaa55-327">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="eaa55-327">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="eaa55-328">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em tempo de execução para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="eaa55-328">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="eaa55-329">Se precisar fornecer configuração do aplicativo com base em variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no `ConfigureAppConfiguration`e chame `AddEnvironmentVariables` com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-329">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

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

<span data-ttu-id="eaa55-330">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="eaa55-330">**Example**</span></span>

<span data-ttu-id="eaa55-331">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-331">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="eaa55-332">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-332">Run the sample app.</span></span> <span data-ttu-id="eaa55-333">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-333">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="eaa55-334">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-334">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="eaa55-335">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="eaa55-335">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="eaa55-336">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="eaa55-336">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="eaa55-337">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-337">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="eaa55-338">Se você quiser expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *Pages/Index.cshtml.cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="eaa55-338">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="eaa55-339">Prefixos</span><span class="sxs-lookup"><span data-stu-id="eaa55-339">Prefixes</span></span>

<span data-ttu-id="eaa55-340">Variáveis de ambiente carregadas na configuração do aplicativo são filtradas quando você fornece um prefixo para o método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-340">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="eaa55-341">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="eaa55-341">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="eaa55-342">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-342">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="eaa55-343">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="eaa55-343">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="eaa55-344">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa55-344">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="eaa55-345">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="eaa55-345">**Connection string prefixes**</span></span>

<span data-ttu-id="eaa55-346">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-346">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="eaa55-347">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-347">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="eaa55-348">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="eaa55-348">Connection string prefix</span></span> | <span data-ttu-id="eaa55-349">Provider</span><span class="sxs-lookup"><span data-stu-id="eaa55-349">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="eaa55-350">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="eaa55-350">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="eaa55-351">MySQL</span><span class="sxs-lookup"><span data-stu-id="eaa55-351">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="eaa55-352">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="eaa55-352">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="eaa55-353">SQL Server</span><span class="sxs-lookup"><span data-stu-id="eaa55-353">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="eaa55-354">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="eaa55-354">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="eaa55-355">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="eaa55-355">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="eaa55-356">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="eaa55-356">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="eaa55-357">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="eaa55-357">Environment variable key</span></span> | <span data-ttu-id="eaa55-358">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="eaa55-358">Converted configuration key</span></span> | <span data-ttu-id="eaa55-359">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="eaa55-359">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="eaa55-360">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="eaa55-360">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="eaa55-361">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="eaa55-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="eaa55-362">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="eaa55-362">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="eaa55-363">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="eaa55-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="eaa55-364">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="eaa55-364">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="eaa55-365">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="eaa55-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="eaa55-366">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="eaa55-366">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="eaa55-367">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="eaa55-367">File Configuration Provider</span></span>

<span data-ttu-id="eaa55-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="eaa55-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="eaa55-369">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="eaa55-369">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="eaa55-370">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="eaa55-370">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="eaa55-371">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="eaa55-371">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="eaa55-372">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="eaa55-372">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="eaa55-373">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="eaa55-373">INI Configuration Provider</span></span>

<span data-ttu-id="eaa55-374">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="eaa55-374">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa55-375">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-375">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa55-376">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="eaa55-376">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="eaa55-377">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa55-377">Overloads permit specifying:</span></span>

* <span data-ttu-id="eaa55-378">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="eaa55-378">Whether the file is optional.</span></span>
* <span data-ttu-id="eaa55-379">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-379">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="eaa55-380">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-380">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="eaa55-381">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="eaa55-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="eaa55-382">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="eaa55-382">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="eaa55-383">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="eaa55-383">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="eaa55-384">section0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-384">section0:key0</span></span>
* <span data-ttu-id="eaa55-385">section0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-385">section0:key1</span></span>
* <span data-ttu-id="eaa55-386">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="eaa55-386">section1:subsection:key</span></span>
* <span data-ttu-id="eaa55-387">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="eaa55-387">section2:subsection0:key</span></span>
* <span data-ttu-id="eaa55-388">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="eaa55-388">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="eaa55-389">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="eaa55-389">JSON Configuration Provider</span></span>

<span data-ttu-id="eaa55-390">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="eaa55-390">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="eaa55-391">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-391">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa55-392">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa55-392">Overloads permit specifying:</span></span>

* <span data-ttu-id="eaa55-393">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="eaa55-393">Whether the file is optional.</span></span>
* <span data-ttu-id="eaa55-394">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-394">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="eaa55-395">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-395">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="eaa55-396">`AddJsonFile` é chamado automaticamente duas vezes quando você inicializa um novo construtor de host com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-396">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="eaa55-397">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="eaa55-397">The method is called to load configuration from:</span></span>

* <span data-ttu-id="eaa55-398">*appsettings.json* &ndash; Esse arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="eaa55-398">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="eaa55-399">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa55-399">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="eaa55-400">*appsettings.{Environment}.json* &ndash; A versão de ambiente do arquivo é carregada com base em [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="eaa55-400">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="eaa55-401">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa55-401">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="eaa55-402">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="eaa55-402">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="eaa55-403">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="eaa55-403">Environment variables.</span></span>
* <span data-ttu-id="eaa55-404">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="eaa55-404">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="eaa55-405">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="eaa55-405">Command-line arguments.</span></span>

<span data-ttu-id="eaa55-406">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="eaa55-406">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="eaa55-407">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="eaa55-407">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="eaa55-408">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-408">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="eaa55-409">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="eaa55-409">**Example**</span></span>

<span data-ttu-id="eaa55-410">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="eaa55-411">A configuração é carregada de *appsettings.json* e de *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa55-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="eaa55-412">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-412">Run the sample app.</span></span> <span data-ttu-id="eaa55-413">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="eaa55-414">Observe que a saída contém pares chave-valor para a configuração mostrada na tabela de acordo com o ambiente.</span><span class="sxs-lookup"><span data-stu-id="eaa55-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="eaa55-415">Chaves de configuração de registro usam os dois-pontos (`:`) como um separador hierárquico.</span><span class="sxs-lookup"><span data-stu-id="eaa55-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="eaa55-416">Chave</span><span class="sxs-lookup"><span data-stu-id="eaa55-416">Key</span></span>                        | <span data-ttu-id="eaa55-417">Valor de Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="eaa55-417">Development Value</span></span> | <span data-ttu-id="eaa55-418">Valor de Produção</span><span class="sxs-lookup"><span data-stu-id="eaa55-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="eaa55-419">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="eaa55-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="eaa55-420">Informações</span><span class="sxs-lookup"><span data-stu-id="eaa55-420">Information</span></span>       | <span data-ttu-id="eaa55-421">Informações</span><span class="sxs-lookup"><span data-stu-id="eaa55-421">Information</span></span>      |
| <span data-ttu-id="eaa55-422">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="eaa55-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="eaa55-423">Informações</span><span class="sxs-lookup"><span data-stu-id="eaa55-423">Information</span></span>       | <span data-ttu-id="eaa55-424">Informações</span><span class="sxs-lookup"><span data-stu-id="eaa55-424">Information</span></span>      |
| <span data-ttu-id="eaa55-425">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="eaa55-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="eaa55-426">Depurar</span><span class="sxs-lookup"><span data-stu-id="eaa55-426">Debug</span></span>             | <span data-ttu-id="eaa55-427">Erro</span><span class="sxs-lookup"><span data-stu-id="eaa55-427">Error</span></span>            |
| <span data-ttu-id="eaa55-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="eaa55-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="eaa55-429">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="eaa55-429">XML Configuration Provider</span></span>

<span data-ttu-id="eaa55-430">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="eaa55-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa55-431">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa55-432">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa55-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="eaa55-433">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="eaa55-433">Whether the file is optional.</span></span>
* <span data-ttu-id="eaa55-434">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="eaa55-435">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="eaa55-436">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="eaa55-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="eaa55-437">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="eaa55-438">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="eaa55-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="eaa55-439">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="eaa55-439">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="eaa55-440">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="eaa55-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="eaa55-441">section0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-441">section0:key0</span></span>
* <span data-ttu-id="eaa55-442">section0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-442">section0:key1</span></span>
* <span data-ttu-id="eaa55-443">section1:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-443">section1:key0</span></span>
* <span data-ttu-id="eaa55-444">section1:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-444">section1:key1</span></span>

<span data-ttu-id="eaa55-445">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="eaa55-445">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="eaa55-446">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="eaa55-446">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="eaa55-447">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-447">section:section0:key:key0</span></span>
* <span data-ttu-id="eaa55-448">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-448">section:section0:key:key1</span></span>
* <span data-ttu-id="eaa55-449">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-449">section:section1:key:key0</span></span>
* <span data-ttu-id="eaa55-450">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-450">section:section1:key:key1</span></span>

<span data-ttu-id="eaa55-451">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="eaa55-451">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="eaa55-452">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="eaa55-452">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="eaa55-453">key:attribute</span><span class="sxs-lookup"><span data-stu-id="eaa55-453">key:attribute</span></span>
* <span data-ttu-id="eaa55-454">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="eaa55-454">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="eaa55-455">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="eaa55-455">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="eaa55-456">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-456">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="eaa55-457">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-457">The key is the file name.</span></span> <span data-ttu-id="eaa55-458">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-458">The value contains the file's contents.</span></span> <span data-ttu-id="eaa55-459">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="eaa55-459">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="eaa55-460">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-460">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="eaa55-461">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="eaa55-461">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="eaa55-462">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa55-462">Overloads permit specifying:</span></span>

* <span data-ttu-id="eaa55-463">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="eaa55-463">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="eaa55-464">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="eaa55-464">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="eaa55-465">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-465">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="eaa55-466">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-466">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="eaa55-467">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="eaa55-467">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="eaa55-468">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="eaa55-468">Memory Configuration Provider</span></span>

<span data-ttu-id="eaa55-469">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-469">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="eaa55-470">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-470">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa55-471">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-471">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="eaa55-472">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-472">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="eaa55-473">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="eaa55-473">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="eaa55-474">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="eaa55-474">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="eaa55-475">GetValue</span><span class="sxs-lookup"><span data-stu-id="eaa55-475">GetValue</span></span>

<span data-ttu-id="eaa55-476">[ConfigurationBinder. GetValue \<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e a converte para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-476">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="eaa55-477">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="eaa55-477">An overload accepts a default value.</span></span>

<span data-ttu-id="eaa55-478">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="eaa55-478">The following example:</span></span>

* <span data-ttu-id="eaa55-479">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-479">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="eaa55-480">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-480">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="eaa55-481">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-481">Types the value as an `int`.</span></span>
* <span data-ttu-id="eaa55-482">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="eaa55-482">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="eaa55-483">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="eaa55-483">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="eaa55-484">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="eaa55-484">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="eaa55-485">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="eaa55-485">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="eaa55-486">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="eaa55-486">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="eaa55-487">section0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-487">section0:key0</span></span>
* <span data-ttu-id="eaa55-488">section0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-488">section0:key1</span></span>
* <span data-ttu-id="eaa55-489">section1:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-489">section1:key0</span></span>
* <span data-ttu-id="eaa55-490">section1:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-490">section1:key1</span></span>
* <span data-ttu-id="eaa55-491">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-491">section2:subsection0:key0</span></span>
* <span data-ttu-id="eaa55-492">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-492">section2:subsection0:key1</span></span>
* <span data-ttu-id="eaa55-493">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="eaa55-493">section2:subsection1:key0</span></span>
* <span data-ttu-id="eaa55-494">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="eaa55-494">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="eaa55-495">GetSection</span><span class="sxs-lookup"><span data-stu-id="eaa55-495">GetSection</span></span>

<span data-ttu-id="eaa55-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="eaa55-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="eaa55-497">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="eaa55-497">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="eaa55-498">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="eaa55-498">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="eaa55-499">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="eaa55-499">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="eaa55-500">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-500">`GetSection` never returns `null`.</span></span> <span data-ttu-id="eaa55-501">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-501">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="eaa55-502">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="eaa55-502">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="eaa55-503"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="eaa55-503">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="eaa55-504">GetChildren</span><span class="sxs-lookup"><span data-stu-id="eaa55-504">GetChildren</span></span>

<span data-ttu-id="eaa55-505">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="eaa55-505">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="eaa55-506">Exists</span><span class="sxs-lookup"><span data-stu-id="eaa55-506">Exists</span></span>

<span data-ttu-id="eaa55-507">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="eaa55-507">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="eaa55-508">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-508">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="eaa55-509">Associar a uma classe</span><span class="sxs-lookup"><span data-stu-id="eaa55-509">Bind to a class</span></span>

<span data-ttu-id="eaa55-510">A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*.</span><span class="sxs-lookup"><span data-stu-id="eaa55-510">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="eaa55-511">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-511">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="eaa55-512">Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="eaa55-512">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="eaa55-513">O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="eaa55-513">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="eaa55-514">A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:</span><span class="sxs-lookup"><span data-stu-id="eaa55-514">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="eaa55-515">Os seguintes pares chave-valor de configuração são criados:</span><span class="sxs-lookup"><span data-stu-id="eaa55-515">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="eaa55-516">Chave</span><span class="sxs-lookup"><span data-stu-id="eaa55-516">Key</span></span>                   | <span data-ttu-id="eaa55-517">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa55-517">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="eaa55-518">starship:name</span><span class="sxs-lookup"><span data-stu-id="eaa55-518">starship:name</span></span>         | <span data-ttu-id="eaa55-519">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="eaa55-519">USS Enterprise</span></span>                                    |
| <span data-ttu-id="eaa55-520">starship:registry</span><span class="sxs-lookup"><span data-stu-id="eaa55-520">starship:registry</span></span>     | <span data-ttu-id="eaa55-521">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="eaa55-521">NCC-1701</span></span>                                          |
| <span data-ttu-id="eaa55-522">starship:class</span><span class="sxs-lookup"><span data-stu-id="eaa55-522">starship:class</span></span>        | <span data-ttu-id="eaa55-523">Constituição</span><span class="sxs-lookup"><span data-stu-id="eaa55-523">Constitution</span></span>                                      |
| <span data-ttu-id="eaa55-524">starship:length</span><span class="sxs-lookup"><span data-stu-id="eaa55-524">starship:length</span></span>       | <span data-ttu-id="eaa55-525">304,8</span><span class="sxs-lookup"><span data-stu-id="eaa55-525">304.8</span></span>                                             |
| <span data-ttu-id="eaa55-526">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="eaa55-526">starship:commissioned</span></span> | <span data-ttu-id="eaa55-527">False</span><span class="sxs-lookup"><span data-stu-id="eaa55-527">False</span></span>                                             |
| <span data-ttu-id="eaa55-528">marca</span><span class="sxs-lookup"><span data-stu-id="eaa55-528">trademark</span></span>             | <span data-ttu-id="eaa55-529">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="eaa55-529">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="eaa55-530">O aplicativo de exemplo chama `GetSection` com a chave `starship`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-530">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="eaa55-531">Os pares chave-valor `starship` são isolados.</span><span class="sxs-lookup"><span data-stu-id="eaa55-531">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="eaa55-532">O método `Bind` é chamado na subseção passando uma instância da classe `Starship`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-532">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="eaa55-533">Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="eaa55-533">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="eaa55-534">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="eaa55-534">Bind to an object graph</span></span>

<span data-ttu-id="eaa55-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="eaa55-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="eaa55-536">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="eaa55-536">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="eaa55-537">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="eaa55-537">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="eaa55-538">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-538">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="eaa55-539">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="eaa55-539">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="eaa55-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="eaa55-541">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-541">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="eaa55-542">O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:</span><span class="sxs-lookup"><span data-stu-id="eaa55-542">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="eaa55-543">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="eaa55-543">Bind an array to a class</span></span>

<span data-ttu-id="eaa55-544">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="eaa55-544">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="eaa55-545">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-545">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="eaa55-546">Qualquer formato de matriz que exponha um segmento de chave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar matrizes a uma matriz de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="eaa55-546">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="eaa55-547">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="eaa55-547">Binding is provided by convention.</span></span> <span data-ttu-id="eaa55-548">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="eaa55-548">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="eaa55-549">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="eaa55-549">**In-memory array processing**</span></span>

<span data-ttu-id="eaa55-550">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="eaa55-550">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="eaa55-551">Chave</span><span class="sxs-lookup"><span data-stu-id="eaa55-551">Key</span></span>             | <span data-ttu-id="eaa55-552">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa55-552">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="eaa55-553">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="eaa55-553">array:entries:0</span></span> | <span data-ttu-id="eaa55-554">value0</span><span class="sxs-lookup"><span data-stu-id="eaa55-554">value0</span></span> |
| <span data-ttu-id="eaa55-555">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="eaa55-555">array:entries:1</span></span> | <span data-ttu-id="eaa55-556">value1</span><span class="sxs-lookup"><span data-stu-id="eaa55-556">value1</span></span> |
| <span data-ttu-id="eaa55-557">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="eaa55-557">array:entries:2</span></span> | <span data-ttu-id="eaa55-558">value2</span><span class="sxs-lookup"><span data-stu-id="eaa55-558">value2</span></span> |
| <span data-ttu-id="eaa55-559">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="eaa55-559">array:entries:4</span></span> | <span data-ttu-id="eaa55-560">value4</span><span class="sxs-lookup"><span data-stu-id="eaa55-560">value4</span></span> |
| <span data-ttu-id="eaa55-561">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="eaa55-561">array:entries:5</span></span> | <span data-ttu-id="eaa55-562">value5</span><span class="sxs-lookup"><span data-stu-id="eaa55-562">value5</span></span> |

<span data-ttu-id="eaa55-563">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="eaa55-563">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="eaa55-564">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="eaa55-564">The array skips a value for index &num;3.</span></span> <span data-ttu-id="eaa55-565">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="eaa55-565">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="eaa55-566">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="eaa55-566">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="eaa55-567">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="eaa55-567">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="eaa55-568">A sintaxe [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="eaa55-568">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="eaa55-569">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-569">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="eaa55-570">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="eaa55-570">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="eaa55-571">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="eaa55-571">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="eaa55-572">0</span><span class="sxs-lookup"><span data-stu-id="eaa55-572">0</span></span>                            | <span data-ttu-id="eaa55-573">value0</span><span class="sxs-lookup"><span data-stu-id="eaa55-573">value0</span></span>                       |
| <span data-ttu-id="eaa55-574">1</span><span class="sxs-lookup"><span data-stu-id="eaa55-574">1</span></span>                            | <span data-ttu-id="eaa55-575">value1</span><span class="sxs-lookup"><span data-stu-id="eaa55-575">value1</span></span>                       |
| <span data-ttu-id="eaa55-576">2</span><span class="sxs-lookup"><span data-stu-id="eaa55-576">2</span></span>                            | <span data-ttu-id="eaa55-577">value2</span><span class="sxs-lookup"><span data-stu-id="eaa55-577">value2</span></span>                       |
| <span data-ttu-id="eaa55-578">3</span><span class="sxs-lookup"><span data-stu-id="eaa55-578">3</span></span>                            | <span data-ttu-id="eaa55-579">value4</span><span class="sxs-lookup"><span data-stu-id="eaa55-579">value4</span></span>                       |
| <span data-ttu-id="eaa55-580">4</span><span class="sxs-lookup"><span data-stu-id="eaa55-580">4</span></span>                            | <span data-ttu-id="eaa55-581">value5</span><span class="sxs-lookup"><span data-stu-id="eaa55-581">value5</span></span>                       |

<span data-ttu-id="eaa55-582">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-582">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="eaa55-583">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="eaa55-583">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="eaa55-584">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="eaa55-584">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="eaa55-585">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-585">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="eaa55-586">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="eaa55-586">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="eaa55-587">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-587">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="eaa55-588">No `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="eaa55-588">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="eaa55-589">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-589">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="eaa55-590">Chave</span><span class="sxs-lookup"><span data-stu-id="eaa55-590">Key</span></span>             | <span data-ttu-id="eaa55-591">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa55-591">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="eaa55-592">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="eaa55-592">array:entries:3</span></span> | <span data-ttu-id="eaa55-593">value3</span><span class="sxs-lookup"><span data-stu-id="eaa55-593">value3</span></span> |

<span data-ttu-id="eaa55-594">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="eaa55-594">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="eaa55-595">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="eaa55-595">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="eaa55-596">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="eaa55-596">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="eaa55-597">0</span><span class="sxs-lookup"><span data-stu-id="eaa55-597">0</span></span>                            | <span data-ttu-id="eaa55-598">value0</span><span class="sxs-lookup"><span data-stu-id="eaa55-598">value0</span></span>                       |
| <span data-ttu-id="eaa55-599">1</span><span class="sxs-lookup"><span data-stu-id="eaa55-599">1</span></span>                            | <span data-ttu-id="eaa55-600">value1</span><span class="sxs-lookup"><span data-stu-id="eaa55-600">value1</span></span>                       |
| <span data-ttu-id="eaa55-601">2</span><span class="sxs-lookup"><span data-stu-id="eaa55-601">2</span></span>                            | <span data-ttu-id="eaa55-602">value2</span><span class="sxs-lookup"><span data-stu-id="eaa55-602">value2</span></span>                       |
| <span data-ttu-id="eaa55-603">3</span><span class="sxs-lookup"><span data-stu-id="eaa55-603">3</span></span>                            | <span data-ttu-id="eaa55-604">value3</span><span class="sxs-lookup"><span data-stu-id="eaa55-604">value3</span></span>                       |
| <span data-ttu-id="eaa55-605">4</span><span class="sxs-lookup"><span data-stu-id="eaa55-605">4</span></span>                            | <span data-ttu-id="eaa55-606">value4</span><span class="sxs-lookup"><span data-stu-id="eaa55-606">value4</span></span>                       |
| <span data-ttu-id="eaa55-607">5</span><span class="sxs-lookup"><span data-stu-id="eaa55-607">5</span></span>                            | <span data-ttu-id="eaa55-608">value5</span><span class="sxs-lookup"><span data-stu-id="eaa55-608">value5</span></span>                       |

<span data-ttu-id="eaa55-609">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="eaa55-609">**JSON array processing**</span></span>

<span data-ttu-id="eaa55-610">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="eaa55-610">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="eaa55-611">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="eaa55-611">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="eaa55-612">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="eaa55-612">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="eaa55-613">Chave</span><span class="sxs-lookup"><span data-stu-id="eaa55-613">Key</span></span>                     | <span data-ttu-id="eaa55-614">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa55-614">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="eaa55-615">json_array:key</span><span class="sxs-lookup"><span data-stu-id="eaa55-615">json_array:key</span></span>          | <span data-ttu-id="eaa55-616">valueA</span><span class="sxs-lookup"><span data-stu-id="eaa55-616">valueA</span></span> |
| <span data-ttu-id="eaa55-617">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="eaa55-617">json_array:subsection:0</span></span> | <span data-ttu-id="eaa55-618">valueB</span><span class="sxs-lookup"><span data-stu-id="eaa55-618">valueB</span></span> |
| <span data-ttu-id="eaa55-619">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="eaa55-619">json_array:subsection:1</span></span> | <span data-ttu-id="eaa55-620">valueC</span><span class="sxs-lookup"><span data-stu-id="eaa55-620">valueC</span></span> |
| <span data-ttu-id="eaa55-621">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="eaa55-621">json_array:subsection:2</span></span> | <span data-ttu-id="eaa55-622">valueD</span><span class="sxs-lookup"><span data-stu-id="eaa55-622">valueD</span></span> |

<span data-ttu-id="eaa55-623">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="eaa55-623">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="eaa55-624">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-624">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="eaa55-625">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-625">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="eaa55-626">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="eaa55-626">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="eaa55-627">Valor `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="eaa55-627">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="eaa55-628">0</span><span class="sxs-lookup"><span data-stu-id="eaa55-628">0</span></span>                                   | <span data-ttu-id="eaa55-629">valueB</span><span class="sxs-lookup"><span data-stu-id="eaa55-629">valueB</span></span>                              |
| <span data-ttu-id="eaa55-630">1</span><span class="sxs-lookup"><span data-stu-id="eaa55-630">1</span></span>                                   | <span data-ttu-id="eaa55-631">valueC</span><span class="sxs-lookup"><span data-stu-id="eaa55-631">valueC</span></span>                              |
| <span data-ttu-id="eaa55-632">2</span><span class="sxs-lookup"><span data-stu-id="eaa55-632">2</span></span>                                   | <span data-ttu-id="eaa55-633">valueD</span><span class="sxs-lookup"><span data-stu-id="eaa55-633">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="eaa55-634">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="eaa55-634">Custom configuration provider</span></span>

<span data-ttu-id="eaa55-635">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="eaa55-635">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="eaa55-636">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="eaa55-636">The provider has the following characteristics:</span></span>

* <span data-ttu-id="eaa55-637">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-637">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="eaa55-638">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="eaa55-638">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="eaa55-639">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="eaa55-639">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="eaa55-640">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="eaa55-640">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="eaa55-641">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-641">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="eaa55-642">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eaa55-642">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eaa55-643">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-643">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="eaa55-644">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="eaa55-644">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="eaa55-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="eaa55-646">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-646">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="eaa55-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="eaa55-648">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-648">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="eaa55-649">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="eaa55-649">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="eaa55-650">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-650">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="eaa55-651">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-651">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="eaa55-652">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-652">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="eaa55-653">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-653">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eaa55-654">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-654">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="eaa55-655">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="eaa55-655">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="eaa55-656">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-656">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="eaa55-657">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-657">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="eaa55-658">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-658">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="eaa55-659">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-659">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="eaa55-660">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="eaa55-660">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="eaa55-661">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-661">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="eaa55-662">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-662">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="eaa55-663">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-663">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="eaa55-664">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa55-664">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="eaa55-665">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="eaa55-665">Access configuration during startup</span></span>

<span data-ttu-id="eaa55-666">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eaa55-666">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eaa55-667">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="eaa55-667">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="eaa55-668">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="eaa55-668">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="eaa55-669">Acessar a configuração em uma página do Razor Pages ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="eaa55-669">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="eaa55-670">Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.</span><span class="sxs-lookup"><span data-stu-id="eaa55-670">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="eaa55-671">Em uma página do Razor:</span><span class="sxs-lookup"><span data-stu-id="eaa55-671">In a Razor Pages page:</span></span>

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

<span data-ttu-id="eaa55-672">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="eaa55-672">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="eaa55-673">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="eaa55-673">Add configuration from an external assembly</span></span>

<span data-ttu-id="eaa55-674">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eaa55-674">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="eaa55-675">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="eaa55-675">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eaa55-676">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="eaa55-676">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
