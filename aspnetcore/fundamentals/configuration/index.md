---
title: Configuração no ASP.NET Core
author: guardrex
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: d0ef670aa0ac4960318f86ea7888b9eab71f17fd
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171893"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="85ef9-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85ef9-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="85ef9-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="85ef9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85ef9-105">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="85ef9-106">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="85ef9-107">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-107">Azure Key Vault</span></span>
* <span data-ttu-id="85ef9-108">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-108">Azure App Configuration</span></span>
* <span data-ttu-id="85ef9-109">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-109">Command-line arguments</span></span>
* <span data-ttu-id="85ef9-110">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="85ef9-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="85ef9-111">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="85ef9-111">Directory files</span></span>
* <span data-ttu-id="85ef9-112">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-112">Environment variables</span></span>
* <span data-ttu-id="85ef9-113">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="85ef9-113">In-memory .NET objects</span></span>
* <span data-ttu-id="85ef9-114">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="85ef9-114">Settings files</span></span>

<span data-ttu-id="85ef9-115">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos implicitamente pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="85ef9-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

<span data-ttu-id="85ef9-116">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="85ef9-116">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="85ef9-117">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="85ef9-117">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="85ef9-118">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-118">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="85ef9-119">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-119">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="85ef9-120">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="85ef9-120">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="85ef9-121">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="85ef9-121">Host versus app configuration</span></span>

<span data-ttu-id="85ef9-122">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-122">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="85ef9-123">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-123">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="85ef9-124">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="85ef9-124">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="85ef9-125">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-125">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="85ef9-126">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-126">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="85ef9-127">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="85ef9-127">Other configuration</span></span>

<span data-ttu-id="85ef9-128">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-128">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="85ef9-129">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="85ef9-129">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="85ef9-130">*Launch. json*/*launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-130">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="85ef9-131">Em <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-131">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="85ef9-132">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-132">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="85ef9-133">o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-133">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="85ef9-134">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-134">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="85ef9-135">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="85ef9-135">Default configuration</span></span>

<span data-ttu-id="85ef9-136">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="85ef9-136">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="85ef9-137">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="85ef9-137">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="85ef9-138">O seguinte se aplica a aplicativos que usam o [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="85ef9-138">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="85ef9-139">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="85ef9-139">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="85ef9-140">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="85ef9-140">Host configuration is provided from:</span></span>
  * <span data-ttu-id="85ef9-141">Variáveis ​​de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-141">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="85ef9-142">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-142">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="85ef9-143">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-143">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="85ef9-144">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="85ef9-144">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="85ef9-145">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-145">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="85ef9-146">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="85ef9-146">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="85ef9-147">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-147">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="85ef9-148">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="85ef9-148">Enable IIS integration.</span></span>
* <span data-ttu-id="85ef9-149">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="85ef9-149">App configuration is provided from:</span></span>
  * <span data-ttu-id="85ef9-150">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-150">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="85ef9-151">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-151">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="85ef9-152">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="85ef9-152">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="85ef9-153">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-153">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="85ef9-154">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-154">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="85ef9-155">Segurança</span><span class="sxs-lookup"><span data-stu-id="85ef9-155">Security</span></span>

<span data-ttu-id="85ef9-156">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="85ef9-156">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="85ef9-157">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="85ef9-157">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="85ef9-158">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-158">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="85ef9-159">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="85ef9-159">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="85ef9-160">Para obter mais informações, consulte estes tópicos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-160">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="85ef9-161"><xref:security/app-secrets> &ndash; inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="85ef9-161"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="85ef9-162">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="85ef9-162">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="85ef9-163">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="85ef9-163">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="85ef9-164">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85ef9-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="85ef9-165">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-165">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="85ef9-166">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="85ef9-166">Hierarchical configuration data</span></span>

<span data-ttu-id="85ef9-167">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-167">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="85ef9-168">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="85ef9-168">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="85ef9-169">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-169">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="85ef9-170">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="85ef9-170">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="85ef9-171">section0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-171">section0:key0</span></span>
* <span data-ttu-id="85ef9-172">section0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-172">section0:key1</span></span>
* <span data-ttu-id="85ef9-173">section1:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-173">section1:key0</span></span>
* <span data-ttu-id="85ef9-174">section1:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-174">section1:key1</span></span>

<span data-ttu-id="85ef9-175">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-175"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="85ef9-176">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="85ef9-176">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="85ef9-177">Convenções</span><span class="sxs-lookup"><span data-stu-id="85ef9-177">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="85ef9-178">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="85ef9-178">Sources and providers</span></span>

<span data-ttu-id="85ef9-179">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-179">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="85ef9-180">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="85ef9-180">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="85ef9-181">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-181">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="85ef9-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="85ef9-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetada em um Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.</span><span class="sxs-lookup"><span data-stu-id="85ef9-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="85ef9-184">Nos exemplos a seguir, o campo `_config` é usado para acessar os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-184">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="85ef9-185">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="85ef9-185">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="85ef9-186">simétricas</span><span class="sxs-lookup"><span data-stu-id="85ef9-186">Keys</span></span>

<span data-ttu-id="85ef9-187">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="85ef9-187">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="85ef9-188">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-188">Keys are case-insensitive.</span></span> <span data-ttu-id="85ef9-189">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="85ef9-189">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="85ef9-190">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-190">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="85ef9-191">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="85ef9-191">Hierarchical keys</span></span>
  * <span data-ttu-id="85ef9-192">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-192">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="85ef9-193">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-193">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="85ef9-194">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="85ef9-194">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="85ef9-195">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="85ef9-195">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="85ef9-196">Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-196">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="85ef9-197">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-197">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="85ef9-198">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="85ef9-198">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="85ef9-199">Valores</span><span class="sxs-lookup"><span data-stu-id="85ef9-199">Values</span></span>

<span data-ttu-id="85ef9-200">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="85ef9-200">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="85ef9-201">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="85ef9-201">Values are strings.</span></span>
* <span data-ttu-id="85ef9-202">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="85ef9-202">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="85ef9-203">Provedores</span><span class="sxs-lookup"><span data-stu-id="85ef9-203">Providers</span></span>

<span data-ttu-id="85ef9-204">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85ef9-204">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="85ef9-205">Provedor</span><span class="sxs-lookup"><span data-stu-id="85ef9-205">Provider</span></span> | <span data-ttu-id="85ef9-206">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="85ef9-206">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="85ef9-207">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="85ef9-207">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="85ef9-208">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-208">Azure Key Vault</span></span> |
| <span data-ttu-id="85ef9-209">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="85ef9-209">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="85ef9-210">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-210">Azure App Configuration</span></span> |
| [<span data-ttu-id="85ef9-211">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-211">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="85ef9-212">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-212">Command-line parameters</span></span> |
| [<span data-ttu-id="85ef9-213">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="85ef9-213">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="85ef9-214">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="85ef9-214">Custom source</span></span> |
| [<span data-ttu-id="85ef9-215">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-215">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="85ef9-216">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-216">Environment variables</span></span> |
| [<span data-ttu-id="85ef9-217">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="85ef9-217">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="85ef9-218">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="85ef9-218">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="85ef9-219">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="85ef9-219">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="85ef9-220">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="85ef9-220">Directory files</span></span> |
| [<span data-ttu-id="85ef9-221">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="85ef9-221">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="85ef9-222">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="85ef9-222">In-memory collections</span></span> |
| <span data-ttu-id="85ef9-223">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="85ef9-223">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="85ef9-224">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="85ef9-224">File in the user profile directory</span></span> |

<span data-ttu-id="85ef9-225">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-225">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="85ef9-226">Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza.</span><span class="sxs-lookup"><span data-stu-id="85ef9-226">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="85ef9-227">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="85ef9-227">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="85ef9-228">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="85ef9-228">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="85ef9-229">Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="85ef9-229">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="85ef9-230">Cofre da Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-230">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="85ef9-231">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="85ef9-231">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="85ef9-232">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-232">Environment variables</span></span>
1. <span data-ttu-id="85ef9-233">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-233">Command-line arguments</span></span>

<span data-ttu-id="85ef9-234">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="85ef9-234">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="85ef9-235">A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-235">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="85ef9-236">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-236">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="85ef9-237">Configurar o construtor de host com ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="85ef9-237">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="85ef9-238">Para configurar o construtor de host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> e forneça a configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-238">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="85ef9-239">`ConfigureHostConfiguration` é usado para inicializar o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> para uso posterior no processo de build.</span><span class="sxs-lookup"><span data-stu-id="85ef9-239">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="85ef9-240">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="85ef9-240">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="85ef9-241">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="85ef9-241">ConfigureAppConfiguration</span></span>

<span data-ttu-id="85ef9-242">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-242">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="85ef9-243">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-243">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="85ef9-244">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="85ef9-244">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="85ef9-245">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="85ef9-245">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="85ef9-246">Para remover os provedores adicionados por `CreateDefaultBuilder`, chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="85ef9-246">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="85ef9-247">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="85ef9-247">Consume configuration during app startup</span></span>

<span data-ttu-id="85ef9-248">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-248">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="85ef9-249">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="85ef9-249">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="85ef9-250">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-250">Command-line Configuration Provider</span></span>

<span data-ttu-id="85ef9-251">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-251">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="85ef9-252">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-252">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-253">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-253">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="85ef9-254">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-254">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="85ef9-255">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="85ef9-255">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="85ef9-256">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-256">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="85ef9-257">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-257">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="85ef9-258">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-258">Environment variables.</span></span>

<span data-ttu-id="85ef9-259">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="85ef9-259">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="85ef9-260">Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="85ef9-260">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="85ef9-261">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="85ef9-261">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="85ef9-262">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-262">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="85ef9-263">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-263">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="85ef9-264">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="85ef9-264">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="85ef9-265">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="85ef9-265">**Example**</span></span>

<span data-ttu-id="85ef9-266">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-266">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="85ef9-267">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="85ef9-267">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="85ef9-268">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-268">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="85ef9-269">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-269">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="85ef9-270">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-270">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="85ef9-271">Argumentos</span><span class="sxs-lookup"><span data-stu-id="85ef9-271">Arguments</span></span>

<span data-ttu-id="85ef9-272">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="85ef9-272">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="85ef9-273">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="85ef9-273">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="85ef9-274">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-274">Key prefix</span></span>               | <span data-ttu-id="85ef9-275">Exemplo</span><span class="sxs-lookup"><span data-stu-id="85ef9-275">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="85ef9-276">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="85ef9-276">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="85ef9-277">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="85ef9-277">Two dashes (`--`)</span></span>        | <span data-ttu-id="85ef9-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="85ef9-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="85ef9-279">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="85ef9-279">Forward slash (`/`)</span></span>      | <span data-ttu-id="85ef9-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="85ef9-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="85ef9-281">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="85ef9-281">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="85ef9-282">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-282">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="85ef9-283">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="85ef9-283">Switch mappings</span></span>

<span data-ttu-id="85ef9-284">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="85ef9-284">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="85ef9-285">Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, forneça um dicionário de substituições de switch para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-285">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="85ef9-286">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="85ef9-286">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="85ef9-287">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-287">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="85ef9-288">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="85ef9-288">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="85ef9-289">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="85ef9-289">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="85ef9-290">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="85ef9-290">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="85ef9-291">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-291">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="85ef9-292">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="85ef9-292">Create a switch mappings dictionary.</span></span> <span data-ttu-id="85ef9-293">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="85ef9-293">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="85ef9-294">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="85ef9-294">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="85ef9-295">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="85ef9-295">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="85ef9-296">A chamada `CreateDefaultBuilder` do método `AddCommandLine` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-296">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="85ef9-297">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `ConfigurationBuilder` do método `AddCommandLine` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="85ef9-297">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="85ef9-298">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="85ef9-298">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="85ef9-299">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-299">Key</span></span>       | <span data-ttu-id="85ef9-300">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-300">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="85ef9-301">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="85ef9-301">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="85ef9-302">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="85ef9-302">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="85ef9-303">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-303">Key</span></span>               | <span data-ttu-id="85ef9-304">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-304">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="85ef9-305">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-305">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="85ef9-306">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-306">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="85ef9-307">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-307">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="85ef9-308">[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-308">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="85ef9-309">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="85ef9-309">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="85ef9-310">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `DOTNET_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host genérico](xref:fundamentals/host/generic-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-310">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="85ef9-311">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-311">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="85ef9-312">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="85ef9-312">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="85ef9-313">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-313">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="85ef9-314">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-314">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="85ef9-315">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-315">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="85ef9-316">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="85ef9-316">Command-line arguments.</span></span>

<span data-ttu-id="85ef9-317">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-317">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="85ef9-318">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-318">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="85ef9-319">Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:</span><span class="sxs-lookup"><span data-stu-id="85ef9-319">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="85ef9-320">Chame `AddEnvironmentVariables` último para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.</span><span class="sxs-lookup"><span data-stu-id="85ef9-320">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="85ef9-321">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="85ef9-321">**Example**</span></span>

<span data-ttu-id="85ef9-322">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-322">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="85ef9-323">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-323">Run the sample app.</span></span> <span data-ttu-id="85ef9-324">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-324">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="85ef9-325">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-325">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="85ef9-326">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-326">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="85ef9-327">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-327">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="85ef9-328">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-328">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="85ef9-329">Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="85ef9-329">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="85ef9-330">Prefixos</span><span class="sxs-lookup"><span data-stu-id="85ef9-330">Prefixes</span></span>

<span data-ttu-id="85ef9-331">As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-331">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="85ef9-332">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-332">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="85ef9-333">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-333">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="85ef9-334">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-334">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="85ef9-335">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-335">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="85ef9-336">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="85ef9-336">**Connection string prefixes**</span></span>

<span data-ttu-id="85ef9-337">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-337">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="85ef9-338">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-338">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="85ef9-339">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="85ef9-339">Connection string prefix</span></span> | <span data-ttu-id="85ef9-340">Provedor</span><span class="sxs-lookup"><span data-stu-id="85ef9-340">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="85ef9-341">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="85ef9-341">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="85ef9-342">MySQL</span><span class="sxs-lookup"><span data-stu-id="85ef9-342">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="85ef9-343">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-343">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="85ef9-344">SQL Server</span><span class="sxs-lookup"><span data-stu-id="85ef9-344">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="85ef9-345">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="85ef9-345">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="85ef9-346">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="85ef9-346">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="85ef9-347">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="85ef9-347">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="85ef9-348">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-348">Environment variable key</span></span> | <span data-ttu-id="85ef9-349">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="85ef9-349">Converted configuration key</span></span> | <span data-ttu-id="85ef9-350">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="85ef9-350">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="85ef9-351">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="85ef9-351">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="85ef9-352">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-352">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="85ef9-353">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="85ef9-353">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="85ef9-354">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-354">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="85ef9-355">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="85ef9-355">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="85ef9-356">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-356">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="85ef9-357">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="85ef9-357">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="85ef9-358">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="85ef9-358">**Example**</span></span>

<span data-ttu-id="85ef9-359">Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-359">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="85ef9-360">Nome &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="85ef9-360">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="85ef9-361">Valor &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="85ef9-361">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="85ef9-362">Se `IConfiguration` for injetada e atribuída a um campo chamado `_config`, leia o valor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-362">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="85ef9-363">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="85ef9-363">File Configuration Provider</span></span>

<span data-ttu-id="85ef9-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="85ef9-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="85ef9-365">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-365">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="85ef9-366">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="85ef9-366">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="85ef9-367">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="85ef9-367">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="85ef9-368">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="85ef9-368">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="85ef9-369">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="85ef9-369">INI Configuration Provider</span></span>

<span data-ttu-id="85ef9-370">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-370">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="85ef9-371">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-371">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-372">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="85ef9-372">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="85ef9-373">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="85ef9-373">Overloads permit specifying:</span></span>

* <span data-ttu-id="85ef9-374">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="85ef9-374">Whether the file is optional.</span></span>
* <span data-ttu-id="85ef9-375">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-375">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="85ef9-376">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-376">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="85ef9-377">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="85ef9-377">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="85ef9-378">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="85ef9-378">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="85ef9-379">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-379">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="85ef9-380">section0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-380">section0:key0</span></span>
* <span data-ttu-id="85ef9-381">section0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-381">section0:key1</span></span>
* <span data-ttu-id="85ef9-382">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="85ef9-382">section1:subsection:key</span></span>
* <span data-ttu-id="85ef9-383">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="85ef9-383">section2:subsection0:key</span></span>
* <span data-ttu-id="85ef9-384">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="85ef9-384">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="85ef9-385">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="85ef9-385">JSON Configuration Provider</span></span>

<span data-ttu-id="85ef9-386">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-386">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="85ef9-387">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-387">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-388">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="85ef9-388">Overloads permit specifying:</span></span>

* <span data-ttu-id="85ef9-389">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="85ef9-389">Whether the file is optional.</span></span>
* <span data-ttu-id="85ef9-390">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-390">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="85ef9-391">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-391">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="85ef9-392">`AddJsonFile` é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-392">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="85ef9-393">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="85ef9-393">The method is called to load configuration from:</span></span>

* <span data-ttu-id="85ef9-394">*appSettings. json* &ndash; este arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="85ef9-394">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="85ef9-395">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-395">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="85ef9-396">*appSettings. {Environment}. JSON* &ndash; a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="85ef9-396">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="85ef9-397">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-397">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="85ef9-398">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="85ef9-398">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="85ef9-399">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-399">Environment variables.</span></span>
* <span data-ttu-id="85ef9-400">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-400">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="85ef9-401">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="85ef9-401">Command-line arguments.</span></span>

<span data-ttu-id="85ef9-402">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="85ef9-402">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="85ef9-403">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-403">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="85ef9-404">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-404">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="85ef9-405">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="85ef9-405">**Example**</span></span>

<span data-ttu-id="85ef9-406">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-406">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="85ef9-407">A primeira chamada para `AddJsonFile` carrega a configuração de *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-407">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="85ef9-408">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-408">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="85ef9-409">Para *appSettings. Development. JSON* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="85ef9-409">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="85ef9-410">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-410">Run the sample app.</span></span> <span data-ttu-id="85ef9-411">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-411">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="85ef9-412">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-412">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="85ef9-413">O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-413">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="85ef9-414">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="85ef9-414">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="85ef9-415">Abra o arquivo *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="85ef9-415">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="85ef9-416">No perfil de `ConfigurationSample`, altere o valor da variável de ambiente `ASPNETCORE_ENVIRONMENT` para `Production`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-416">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="85ef9-417">Salve o arquivo e execute o aplicativo com `dotnet run` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="85ef9-417">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="85ef9-418">As configurações em *appSettings. O Development. JSON* não substitui mais as configurações em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-418">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="85ef9-419">O nível de log para a `Logging:LogLevel:Default` de chave é `Information`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-419">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="85ef9-420">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="85ef9-420">XML Configuration Provider</span></span>

<span data-ttu-id="85ef9-421">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-421">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="85ef9-422">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-422">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-423">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="85ef9-423">Overloads permit specifying:</span></span>

* <span data-ttu-id="85ef9-424">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="85ef9-424">Whether the file is optional.</span></span>
* <span data-ttu-id="85ef9-425">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-425">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="85ef9-426">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-426">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="85ef9-427">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-427">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="85ef9-428">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-428">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="85ef9-429">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="85ef9-429">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="85ef9-430">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="85ef9-430">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="85ef9-431">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-431">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="85ef9-432">section0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-432">section0:key0</span></span>
* <span data-ttu-id="85ef9-433">section0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-433">section0:key1</span></span>
* <span data-ttu-id="85ef9-434">section1:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-434">section1:key0</span></span>
* <span data-ttu-id="85ef9-435">section1:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-435">section1:key1</span></span>

<span data-ttu-id="85ef9-436">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-436">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="85ef9-437">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-437">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="85ef9-438">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-438">section:section0:key:key0</span></span>
* <span data-ttu-id="85ef9-439">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-439">section:section0:key:key1</span></span>
* <span data-ttu-id="85ef9-440">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-440">section:section1:key:key0</span></span>
* <span data-ttu-id="85ef9-441">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-441">section:section1:key:key1</span></span>

<span data-ttu-id="85ef9-442">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="85ef9-442">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="85ef9-443">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-443">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="85ef9-444">key:attribute</span><span class="sxs-lookup"><span data-stu-id="85ef9-444">key:attribute</span></span>
* <span data-ttu-id="85ef9-445">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="85ef9-445">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="85ef9-446">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="85ef9-446">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="85ef9-447">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-447">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="85ef9-448">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-448">The key is the file name.</span></span> <span data-ttu-id="85ef9-449">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-449">The value contains the file's contents.</span></span> <span data-ttu-id="85ef9-450">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="85ef9-450">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="85ef9-451">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-451">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="85ef9-452">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="85ef9-452">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="85ef9-453">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="85ef9-453">Overloads permit specifying:</span></span>

* <span data-ttu-id="85ef9-454">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="85ef9-454">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="85ef9-455">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="85ef9-455">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="85ef9-456">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-456">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="85ef9-457">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-457">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="85ef9-458">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="85ef9-458">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="85ef9-459">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="85ef9-459">Memory Configuration Provider</span></span>

<span data-ttu-id="85ef9-460">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="85ef9-461">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-462">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="85ef9-463">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-463">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="85ef9-464">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="85ef9-464">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="85ef9-465">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-465">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="85ef9-466">GetValue</span><span class="sxs-lookup"><span data-stu-id="85ef9-466">GetValue</span></span>

<span data-ttu-id="85ef9-467">[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-467">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="85ef9-468">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="85ef9-468">An overload accepts a default value.</span></span>

<span data-ttu-id="85ef9-469">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="85ef9-469">The following example:</span></span>

* <span data-ttu-id="85ef9-470">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-470">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="85ef9-471">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-471">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="85ef9-472">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-472">Types the value as an `int`.</span></span>
* <span data-ttu-id="85ef9-473">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="85ef9-473">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="85ef9-474">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="85ef9-474">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="85ef9-475">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="85ef9-475">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="85ef9-476">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="85ef9-476">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="85ef9-477">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-477">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="85ef9-478">section0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-478">section0:key0</span></span>
* <span data-ttu-id="85ef9-479">section0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-479">section0:key1</span></span>
* <span data-ttu-id="85ef9-480">section1:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-480">section1:key0</span></span>
* <span data-ttu-id="85ef9-481">section1:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-481">section1:key1</span></span>
* <span data-ttu-id="85ef9-482">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-482">section2:subsection0:key0</span></span>
* <span data-ttu-id="85ef9-483">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-483">section2:subsection0:key1</span></span>
* <span data-ttu-id="85ef9-484">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-484">section2:subsection1:key0</span></span>
* <span data-ttu-id="85ef9-485">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-485">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="85ef9-486">GetSection</span><span class="sxs-lookup"><span data-stu-id="85ef9-486">GetSection</span></span>

<span data-ttu-id="85ef9-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="85ef9-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="85ef9-488">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="85ef9-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="85ef9-489">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="85ef9-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="85ef9-490">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="85ef9-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="85ef9-491">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="85ef9-492">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="85ef9-493">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="85ef9-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="85ef9-494"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="85ef9-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="85ef9-495">GetChildren</span><span class="sxs-lookup"><span data-stu-id="85ef9-495">GetChildren</span></span>

<span data-ttu-id="85ef9-496">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="85ef9-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="85ef9-497">Exists</span><span class="sxs-lookup"><span data-stu-id="85ef9-497">Exists</span></span>

<span data-ttu-id="85ef9-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="85ef9-499">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="85ef9-500">Associar a uma classe</span><span class="sxs-lookup"><span data-stu-id="85ef9-500">Bind to a class</span></span>

<span data-ttu-id="85ef9-501">A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="85ef9-502">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="85ef9-503">Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="85ef9-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="85ef9-504">O associador associa valores a todas as propriedades públicas de leitura/gravação do tipo fornecido.</span><span class="sxs-lookup"><span data-stu-id="85ef9-504">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="85ef9-505">Os campos **não** estão associados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-505">Fields are **not** bound.</span></span>

<span data-ttu-id="85ef9-506">O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="85ef9-506">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="85ef9-507">A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-507">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

<span data-ttu-id="85ef9-508">Os seguintes pares chave-valor de configuração são criados:</span><span class="sxs-lookup"><span data-stu-id="85ef9-508">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="85ef9-509">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-509">Key</span></span>                   | <span data-ttu-id="85ef9-510">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-510">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="85ef9-511">starship:name</span><span class="sxs-lookup"><span data-stu-id="85ef9-511">starship:name</span></span>         | <span data-ttu-id="85ef9-512">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="85ef9-512">USS Enterprise</span></span>                                    |
| <span data-ttu-id="85ef9-513">starship:registry</span><span class="sxs-lookup"><span data-stu-id="85ef9-513">starship:registry</span></span>     | <span data-ttu-id="85ef9-514">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="85ef9-514">NCC-1701</span></span>                                          |
| <span data-ttu-id="85ef9-515">starship:class</span><span class="sxs-lookup"><span data-stu-id="85ef9-515">starship:class</span></span>        | <span data-ttu-id="85ef9-516">Constituição</span><span class="sxs-lookup"><span data-stu-id="85ef9-516">Constitution</span></span>                                      |
| <span data-ttu-id="85ef9-517">starship:length</span><span class="sxs-lookup"><span data-stu-id="85ef9-517">starship:length</span></span>       | <span data-ttu-id="85ef9-518">304,8</span><span class="sxs-lookup"><span data-stu-id="85ef9-518">304.8</span></span>                                             |
| <span data-ttu-id="85ef9-519">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="85ef9-519">starship:commissioned</span></span> | <span data-ttu-id="85ef9-520">Falso</span><span class="sxs-lookup"><span data-stu-id="85ef9-520">False</span></span>                                             |
| <span data-ttu-id="85ef9-521">marca</span><span class="sxs-lookup"><span data-stu-id="85ef9-521">trademark</span></span>             | <span data-ttu-id="85ef9-522">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="85ef9-522">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="85ef9-523">O aplicativo de exemplo chama `GetSection` com a chave `starship`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-523">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="85ef9-524">Os pares chave-valor `starship` são isolados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-524">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="85ef9-525">O método `Bind` é chamado na subseção passando uma instância da classe `Starship`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-525">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="85ef9-526">Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="85ef9-526">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="85ef9-527">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="85ef9-527">Bind to an object graph</span></span>

<span data-ttu-id="85ef9-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="85ef9-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="85ef9-529">Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-529">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="85ef9-530">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="85ef9-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="85ef9-531">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="85ef9-532">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="85ef9-533">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="85ef9-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="85ef9-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="85ef9-535">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="85ef9-536">O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:</span><span class="sxs-lookup"><span data-stu-id="85ef9-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="85ef9-537">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="85ef9-537">Bind an array to a class</span></span>

<span data-ttu-id="85ef9-538">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="85ef9-538">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="85ef9-539">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-539">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="85ef9-540">Qualquer formato de matriz que expõe um segmento de chave numérico (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar a matriz a uma matriz de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="85ef9-540">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="85ef9-541">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="85ef9-541">Binding is provided by convention.</span></span> <span data-ttu-id="85ef9-542">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="85ef9-542">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="85ef9-543">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="85ef9-543">**In-memory array processing**</span></span>

<span data-ttu-id="85ef9-544">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="85ef9-544">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="85ef9-545">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-545">Key</span></span>             | <span data-ttu-id="85ef9-546">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-546">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="85ef9-547">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="85ef9-547">array:entries:0</span></span> | <span data-ttu-id="85ef9-548">value0</span><span class="sxs-lookup"><span data-stu-id="85ef9-548">value0</span></span> |
| <span data-ttu-id="85ef9-549">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="85ef9-549">array:entries:1</span></span> | <span data-ttu-id="85ef9-550">value1</span><span class="sxs-lookup"><span data-stu-id="85ef9-550">value1</span></span> |
| <span data-ttu-id="85ef9-551">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="85ef9-551">array:entries:2</span></span> | <span data-ttu-id="85ef9-552">value2</span><span class="sxs-lookup"><span data-stu-id="85ef9-552">value2</span></span> |
| <span data-ttu-id="85ef9-553">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="85ef9-553">array:entries:4</span></span> | <span data-ttu-id="85ef9-554">value4</span><span class="sxs-lookup"><span data-stu-id="85ef9-554">value4</span></span> |
| <span data-ttu-id="85ef9-555">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="85ef9-555">array:entries:5</span></span> | <span data-ttu-id="85ef9-556">value5</span><span class="sxs-lookup"><span data-stu-id="85ef9-556">value5</span></span> |

<span data-ttu-id="85ef9-557">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="85ef9-557">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="85ef9-558">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="85ef9-558">The array skips a value for index &num;3.</span></span> <span data-ttu-id="85ef9-559">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-559">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="85ef9-560">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="85ef9-560">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="85ef9-561">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="85ef9-561">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="85ef9-562">A sintaxe [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="85ef9-562">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="85ef9-563">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-563">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="85ef9-564">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="85ef9-564">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="85ef9-565">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-565">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="85ef9-566">0</span><span class="sxs-lookup"><span data-stu-id="85ef9-566">0</span></span>                            | <span data-ttu-id="85ef9-567">value0</span><span class="sxs-lookup"><span data-stu-id="85ef9-567">value0</span></span>                       |
| <span data-ttu-id="85ef9-568">1</span><span class="sxs-lookup"><span data-stu-id="85ef9-568">1</span></span>                            | <span data-ttu-id="85ef9-569">value1</span><span class="sxs-lookup"><span data-stu-id="85ef9-569">value1</span></span>                       |
| <span data-ttu-id="85ef9-570">2</span><span class="sxs-lookup"><span data-stu-id="85ef9-570">2</span></span>                            | <span data-ttu-id="85ef9-571">value2</span><span class="sxs-lookup"><span data-stu-id="85ef9-571">value2</span></span>                       |
| <span data-ttu-id="85ef9-572">3</span><span class="sxs-lookup"><span data-stu-id="85ef9-572">3</span></span>                            | <span data-ttu-id="85ef9-573">value4</span><span class="sxs-lookup"><span data-stu-id="85ef9-573">value4</span></span>                       |
| <span data-ttu-id="85ef9-574">4</span><span class="sxs-lookup"><span data-stu-id="85ef9-574">4</span></span>                            | <span data-ttu-id="85ef9-575">value5</span><span class="sxs-lookup"><span data-stu-id="85ef9-575">value5</span></span>                       |

<span data-ttu-id="85ef9-576">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-576">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="85ef9-577">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="85ef9-577">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="85ef9-578">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="85ef9-578">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="85ef9-579">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-579">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="85ef9-580">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="85ef9-580">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="85ef9-581">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-581">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="85ef9-582">Em `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-582">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="85ef9-583">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-583">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="85ef9-584">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-584">Key</span></span>             | <span data-ttu-id="85ef9-585">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-585">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="85ef9-586">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="85ef9-586">array:entries:3</span></span> | <span data-ttu-id="85ef9-587">value3</span><span class="sxs-lookup"><span data-stu-id="85ef9-587">value3</span></span> |

<span data-ttu-id="85ef9-588">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="85ef9-588">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="85ef9-589">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="85ef9-589">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="85ef9-590">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-590">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="85ef9-591">0</span><span class="sxs-lookup"><span data-stu-id="85ef9-591">0</span></span>                            | <span data-ttu-id="85ef9-592">value0</span><span class="sxs-lookup"><span data-stu-id="85ef9-592">value0</span></span>                       |
| <span data-ttu-id="85ef9-593">1</span><span class="sxs-lookup"><span data-stu-id="85ef9-593">1</span></span>                            | <span data-ttu-id="85ef9-594">value1</span><span class="sxs-lookup"><span data-stu-id="85ef9-594">value1</span></span>                       |
| <span data-ttu-id="85ef9-595">2</span><span class="sxs-lookup"><span data-stu-id="85ef9-595">2</span></span>                            | <span data-ttu-id="85ef9-596">value2</span><span class="sxs-lookup"><span data-stu-id="85ef9-596">value2</span></span>                       |
| <span data-ttu-id="85ef9-597">3</span><span class="sxs-lookup"><span data-stu-id="85ef9-597">3</span></span>                            | <span data-ttu-id="85ef9-598">value3</span><span class="sxs-lookup"><span data-stu-id="85ef9-598">value3</span></span>                       |
| <span data-ttu-id="85ef9-599">4</span><span class="sxs-lookup"><span data-stu-id="85ef9-599">4</span></span>                            | <span data-ttu-id="85ef9-600">value4</span><span class="sxs-lookup"><span data-stu-id="85ef9-600">value4</span></span>                       |
| <span data-ttu-id="85ef9-601">5</span><span class="sxs-lookup"><span data-stu-id="85ef9-601">5</span></span>                            | <span data-ttu-id="85ef9-602">value5</span><span class="sxs-lookup"><span data-stu-id="85ef9-602">value5</span></span>                       |

<span data-ttu-id="85ef9-603">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="85ef9-603">**JSON array processing**</span></span>

<span data-ttu-id="85ef9-604">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="85ef9-604">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="85ef9-605">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="85ef9-605">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="85ef9-606">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-606">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="85ef9-607">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-607">Key</span></span>                     | <span data-ttu-id="85ef9-608">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-608">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="85ef9-609">json_array:key</span><span class="sxs-lookup"><span data-stu-id="85ef9-609">json_array:key</span></span>          | <span data-ttu-id="85ef9-610">valueA</span><span class="sxs-lookup"><span data-stu-id="85ef9-610">valueA</span></span> |
| <span data-ttu-id="85ef9-611">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="85ef9-611">json_array:subsection:0</span></span> | <span data-ttu-id="85ef9-612">valueB</span><span class="sxs-lookup"><span data-stu-id="85ef9-612">valueB</span></span> |
| <span data-ttu-id="85ef9-613">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="85ef9-613">json_array:subsection:1</span></span> | <span data-ttu-id="85ef9-614">valueC</span><span class="sxs-lookup"><span data-stu-id="85ef9-614">valueC</span></span> |
| <span data-ttu-id="85ef9-615">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="85ef9-615">json_array:subsection:2</span></span> | <span data-ttu-id="85ef9-616">valueD</span><span class="sxs-lookup"><span data-stu-id="85ef9-616">valueD</span></span> |

<span data-ttu-id="85ef9-617">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-617">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="85ef9-618">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-618">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="85ef9-619">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-619">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="85ef9-620">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="85ef9-620">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="85ef9-621">`JsonArrayExample.Subsection` Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-621">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="85ef9-622">0</span><span class="sxs-lookup"><span data-stu-id="85ef9-622">0</span></span>                                   | <span data-ttu-id="85ef9-623">valueB</span><span class="sxs-lookup"><span data-stu-id="85ef9-623">valueB</span></span>                              |
| <span data-ttu-id="85ef9-624">1</span><span class="sxs-lookup"><span data-stu-id="85ef9-624">1</span></span>                                   | <span data-ttu-id="85ef9-625">valueC</span><span class="sxs-lookup"><span data-stu-id="85ef9-625">valueC</span></span>                              |
| <span data-ttu-id="85ef9-626">2</span><span class="sxs-lookup"><span data-stu-id="85ef9-626">2</span></span>                                   | <span data-ttu-id="85ef9-627">valueD</span><span class="sxs-lookup"><span data-stu-id="85ef9-627">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="85ef9-628">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="85ef9-628">Custom configuration provider</span></span>

<span data-ttu-id="85ef9-629">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="85ef9-629">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="85ef9-630">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="85ef9-630">The provider has the following characteristics:</span></span>

* <span data-ttu-id="85ef9-631">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-631">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="85ef9-632">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-632">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="85ef9-633">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="85ef9-633">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="85ef9-634">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="85ef9-634">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="85ef9-635">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-635">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="85ef9-636">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-636">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="85ef9-637">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-637">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="85ef9-638">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-638">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="85ef9-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="85ef9-640">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-640">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="85ef9-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="85ef9-642">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-642">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="85ef9-643">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="85ef9-643">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="85ef9-644">Como [as chaves de configuração](#keys)não diferenciam maiúsculas de minúsculas, o dicionário usado para inicializar o banco de dados é criado com o comparador que não diferencia maiúsculas de minúsculas ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="85ef9-644">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="85ef9-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="85ef9-646">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-646">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="85ef9-647">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-647">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="85ef9-648">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-648">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="85ef9-649">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="85ef9-649">Access configuration during startup</span></span>

<span data-ttu-id="85ef9-650">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-650">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="85ef9-651">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-651">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="85ef9-652">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="85ef9-652">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="85ef9-653">Acessar a configuração em uma página do Razor Pages ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="85ef9-653">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="85ef9-654">Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.</span><span class="sxs-lookup"><span data-stu-id="85ef9-654">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="85ef9-655">Em uma página do Razor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-655">In a Razor Pages page:</span></span>

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

<span data-ttu-id="85ef9-656">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="85ef9-656">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="85ef9-657">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="85ef9-657">Add configuration from an external assembly</span></span>

<span data-ttu-id="85ef9-658">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-658">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="85ef9-659">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-659">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="85ef9-660">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="85ef9-660">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="85ef9-661">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-661">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="85ef9-662">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-662">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="85ef9-663">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-663">Azure Key Vault</span></span>
* <span data-ttu-id="85ef9-664">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-664">Azure App Configuration</span></span>
* <span data-ttu-id="85ef9-665">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-665">Command-line arguments</span></span>
* <span data-ttu-id="85ef9-666">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="85ef9-666">Custom providers (installed or created)</span></span>
* <span data-ttu-id="85ef9-667">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="85ef9-667">Directory files</span></span>
* <span data-ttu-id="85ef9-668">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-668">Environment variables</span></span>
* <span data-ttu-id="85ef9-669">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="85ef9-669">In-memory .NET objects</span></span>
* <span data-ttu-id="85ef9-670">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="85ef9-670">Settings files</span></span>

<span data-ttu-id="85ef9-671">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="85ef9-671">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="85ef9-672">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="85ef9-672">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="85ef9-673">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="85ef9-673">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="85ef9-674">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-674">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="85ef9-675">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-675">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="85ef9-676">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="85ef9-676">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="85ef9-677">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="85ef9-677">Host versus app configuration</span></span>

<span data-ttu-id="85ef9-678">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-678">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="85ef9-679">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-679">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="85ef9-680">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="85ef9-680">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="85ef9-681">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-681">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="85ef9-682">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-682">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="85ef9-683">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="85ef9-683">Other configuration</span></span>

<span data-ttu-id="85ef9-684">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-684">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="85ef9-685">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="85ef9-685">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="85ef9-686">*Launch. json*/*launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-686">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="85ef9-687">Em <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-687">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="85ef9-688">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-688">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="85ef9-689">o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-689">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="85ef9-690">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-690">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="85ef9-691">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="85ef9-691">Default configuration</span></span>

<span data-ttu-id="85ef9-692">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="85ef9-692">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="85ef9-693">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="85ef9-693">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="85ef9-694">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="85ef9-694">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="85ef9-695">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="85ef9-695">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="85ef9-696">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="85ef9-696">Host configuration is provided from:</span></span>
  * <span data-ttu-id="85ef9-697">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-697">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="85ef9-698">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-698">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="85ef9-699">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-699">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="85ef9-700">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="85ef9-700">App configuration is provided from:</span></span>
  * <span data-ttu-id="85ef9-701">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-701">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="85ef9-702">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-702">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="85ef9-703">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="85ef9-703">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="85ef9-704">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-704">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="85ef9-705">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="85ef9-705">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="85ef9-706">Segurança</span><span class="sxs-lookup"><span data-stu-id="85ef9-706">Security</span></span>

<span data-ttu-id="85ef9-707">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="85ef9-707">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="85ef9-708">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="85ef9-708">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="85ef9-709">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-709">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="85ef9-710">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="85ef9-710">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="85ef9-711">Para obter mais informações, consulte estes tópicos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-711">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="85ef9-712"><xref:security/app-secrets> &ndash; inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="85ef9-712"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="85ef9-713">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="85ef9-713">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="85ef9-714">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="85ef9-714">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="85ef9-715">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85ef9-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="85ef9-716">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-716">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="85ef9-717">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="85ef9-717">Hierarchical configuration data</span></span>

<span data-ttu-id="85ef9-718">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-718">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="85ef9-719">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="85ef9-719">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="85ef9-720">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-720">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="85ef9-721">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="85ef9-721">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="85ef9-722">section0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-722">section0:key0</span></span>
* <span data-ttu-id="85ef9-723">section0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-723">section0:key1</span></span>
* <span data-ttu-id="85ef9-724">section1:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-724">section1:key0</span></span>
* <span data-ttu-id="85ef9-725">section1:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-725">section1:key1</span></span>

<span data-ttu-id="85ef9-726">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-726"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="85ef9-727">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="85ef9-727">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="85ef9-728">Convenções</span><span class="sxs-lookup"><span data-stu-id="85ef9-728">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="85ef9-729">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="85ef9-729">Sources and providers</span></span>

<span data-ttu-id="85ef9-730">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-730">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="85ef9-731">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="85ef9-731">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="85ef9-732">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-732">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="85ef9-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="85ef9-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetada em um Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.</span><span class="sxs-lookup"><span data-stu-id="85ef9-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="85ef9-735">Nos exemplos a seguir, o campo `_config` é usado para acessar os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-735">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="85ef9-736">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="85ef9-736">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="85ef9-737">simétricas</span><span class="sxs-lookup"><span data-stu-id="85ef9-737">Keys</span></span>

<span data-ttu-id="85ef9-738">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="85ef9-738">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="85ef9-739">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-739">Keys are case-insensitive.</span></span> <span data-ttu-id="85ef9-740">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="85ef9-740">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="85ef9-741">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-741">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="85ef9-742">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="85ef9-742">Hierarchical keys</span></span>
  * <span data-ttu-id="85ef9-743">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-743">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="85ef9-744">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-744">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="85ef9-745">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="85ef9-745">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="85ef9-746">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="85ef9-746">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="85ef9-747">Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-747">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="85ef9-748">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-748">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="85ef9-749">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="85ef9-749">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="85ef9-750">Valores</span><span class="sxs-lookup"><span data-stu-id="85ef9-750">Values</span></span>

<span data-ttu-id="85ef9-751">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="85ef9-751">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="85ef9-752">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="85ef9-752">Values are strings.</span></span>
* <span data-ttu-id="85ef9-753">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="85ef9-753">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="85ef9-754">Provedores</span><span class="sxs-lookup"><span data-stu-id="85ef9-754">Providers</span></span>

<span data-ttu-id="85ef9-755">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85ef9-755">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="85ef9-756">Provedor</span><span class="sxs-lookup"><span data-stu-id="85ef9-756">Provider</span></span> | <span data-ttu-id="85ef9-757">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="85ef9-757">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="85ef9-758">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="85ef9-758">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="85ef9-759">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-759">Azure Key Vault</span></span> |
| <span data-ttu-id="85ef9-760">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="85ef9-760">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="85ef9-761">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-761">Azure App Configuration</span></span> |
| [<span data-ttu-id="85ef9-762">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-762">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="85ef9-763">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-763">Command-line parameters</span></span> |
| [<span data-ttu-id="85ef9-764">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="85ef9-764">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="85ef9-765">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="85ef9-765">Custom source</span></span> |
| [<span data-ttu-id="85ef9-766">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-766">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="85ef9-767">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-767">Environment variables</span></span> |
| [<span data-ttu-id="85ef9-768">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="85ef9-768">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="85ef9-769">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="85ef9-769">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="85ef9-770">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="85ef9-770">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="85ef9-771">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="85ef9-771">Directory files</span></span> |
| [<span data-ttu-id="85ef9-772">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="85ef9-772">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="85ef9-773">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="85ef9-773">In-memory collections</span></span> |
| <span data-ttu-id="85ef9-774">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="85ef9-774">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="85ef9-775">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="85ef9-775">File in the user profile directory</span></span> |

<span data-ttu-id="85ef9-776">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-776">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="85ef9-777">Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza.</span><span class="sxs-lookup"><span data-stu-id="85ef9-777">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="85ef9-778">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="85ef9-778">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="85ef9-779">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="85ef9-779">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="85ef9-780">Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="85ef9-780">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="85ef9-781">Cofre da Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-781">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="85ef9-782">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="85ef9-782">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="85ef9-783">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-783">Environment variables</span></span>
1. <span data-ttu-id="85ef9-784">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-784">Command-line arguments</span></span>

<span data-ttu-id="85ef9-785">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="85ef9-785">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="85ef9-786">A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-786">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="85ef9-787">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-787">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="85ef9-788">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="85ef9-788">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="85ef9-789">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-789">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="85ef9-790">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="85ef9-790">ConfigureAppConfiguration</span></span>

<span data-ttu-id="85ef9-791">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-791">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="85ef9-792">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-792">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="85ef9-793">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="85ef9-793">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="85ef9-794">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="85ef9-794">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="85ef9-795">Para remover os provedores adicionados por `CreateDefaultBuilder`, chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="85ef9-795">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="85ef9-796">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="85ef9-796">Consume configuration during app startup</span></span>

<span data-ttu-id="85ef9-797">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-797">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="85ef9-798">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="85ef9-798">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="85ef9-799">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="85ef9-799">Command-line Configuration Provider</span></span>

<span data-ttu-id="85ef9-800">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-800">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="85ef9-801">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-801">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-802">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-802">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="85ef9-803">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-803">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="85ef9-804">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="85ef9-804">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="85ef9-805">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-805">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="85ef9-806">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-806">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="85ef9-807">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-807">Environment variables.</span></span>

<span data-ttu-id="85ef9-808">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="85ef9-808">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="85ef9-809">Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="85ef9-809">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="85ef9-810">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="85ef9-810">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="85ef9-811">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-811">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="85ef9-812">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-812">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="85ef9-813">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="85ef9-813">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="85ef9-814">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="85ef9-814">**Example**</span></span>

<span data-ttu-id="85ef9-815">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-815">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="85ef9-816">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="85ef9-816">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="85ef9-817">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-817">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="85ef9-818">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-818">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="85ef9-819">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-819">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="85ef9-820">Argumentos</span><span class="sxs-lookup"><span data-stu-id="85ef9-820">Arguments</span></span>

<span data-ttu-id="85ef9-821">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="85ef9-821">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="85ef9-822">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="85ef9-822">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="85ef9-823">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-823">Key prefix</span></span>               | <span data-ttu-id="85ef9-824">Exemplo</span><span class="sxs-lookup"><span data-stu-id="85ef9-824">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="85ef9-825">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="85ef9-825">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="85ef9-826">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="85ef9-826">Two dashes (`--`)</span></span>        | <span data-ttu-id="85ef9-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="85ef9-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="85ef9-828">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="85ef9-828">Forward slash (`/`)</span></span>      | <span data-ttu-id="85ef9-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="85ef9-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="85ef9-830">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="85ef9-830">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="85ef9-831">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-831">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="85ef9-832">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="85ef9-832">Switch mappings</span></span>

<span data-ttu-id="85ef9-833">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="85ef9-833">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="85ef9-834">Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, forneça um dicionário de substituições de switch para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-834">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="85ef9-835">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="85ef9-835">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="85ef9-836">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-836">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="85ef9-837">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="85ef9-837">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="85ef9-838">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="85ef9-838">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="85ef9-839">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="85ef9-839">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="85ef9-840">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-840">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="85ef9-841">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="85ef9-841">Create a switch mappings dictionary.</span></span> <span data-ttu-id="85ef9-842">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="85ef9-842">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="85ef9-843">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="85ef9-843">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="85ef9-844">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="85ef9-844">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="85ef9-845">A chamada `CreateDefaultBuilder` do método `AddCommandLine` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-845">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="85ef9-846">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `ConfigurationBuilder` do método `AddCommandLine` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="85ef9-846">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="85ef9-847">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="85ef9-847">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="85ef9-848">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-848">Key</span></span>       | <span data-ttu-id="85ef9-849">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-849">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="85ef9-850">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="85ef9-850">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="85ef9-851">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="85ef9-851">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="85ef9-852">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-852">Key</span></span>               | <span data-ttu-id="85ef9-853">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-853">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="85ef9-854">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-854">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="85ef9-855">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-855">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="85ef9-856">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-856">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="85ef9-857">[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-857">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="85ef9-858">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="85ef9-858">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="85ef9-859">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-859">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="85ef9-860">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-860">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="85ef9-861">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="85ef9-861">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="85ef9-862">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-862">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="85ef9-863">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-863">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="85ef9-864">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-864">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="85ef9-865">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="85ef9-865">Command-line arguments.</span></span>

<span data-ttu-id="85ef9-866">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-866">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="85ef9-867">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-867">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="85ef9-868">Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:</span><span class="sxs-lookup"><span data-stu-id="85ef9-868">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="85ef9-869">Chame `AddEnvironmentVariables` último para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.</span><span class="sxs-lookup"><span data-stu-id="85ef9-869">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="85ef9-870">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="85ef9-870">**Example**</span></span>

<span data-ttu-id="85ef9-871">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-871">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="85ef9-872">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-872">Run the sample app.</span></span> <span data-ttu-id="85ef9-873">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-873">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="85ef9-874">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-874">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="85ef9-875">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-875">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="85ef9-876">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-876">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="85ef9-877">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-877">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="85ef9-878">Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="85ef9-878">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="85ef9-879">Prefixos</span><span class="sxs-lookup"><span data-stu-id="85ef9-879">Prefixes</span></span>

<span data-ttu-id="85ef9-880">As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-880">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="85ef9-881">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-881">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="85ef9-882">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-882">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="85ef9-883">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-883">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="85ef9-884">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-884">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="85ef9-885">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="85ef9-885">**Connection string prefixes**</span></span>

<span data-ttu-id="85ef9-886">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-886">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="85ef9-887">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-887">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="85ef9-888">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="85ef9-888">Connection string prefix</span></span> | <span data-ttu-id="85ef9-889">Provedor</span><span class="sxs-lookup"><span data-stu-id="85ef9-889">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="85ef9-890">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="85ef9-890">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="85ef9-891">MySQL</span><span class="sxs-lookup"><span data-stu-id="85ef9-891">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="85ef9-892">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="85ef9-892">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="85ef9-893">SQL Server</span><span class="sxs-lookup"><span data-stu-id="85ef9-893">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="85ef9-894">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="85ef9-894">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="85ef9-895">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="85ef9-895">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="85ef9-896">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="85ef9-896">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="85ef9-897">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="85ef9-897">Environment variable key</span></span> | <span data-ttu-id="85ef9-898">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="85ef9-898">Converted configuration key</span></span> | <span data-ttu-id="85ef9-899">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="85ef9-899">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="85ef9-900">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="85ef9-900">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="85ef9-901">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-901">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="85ef9-902">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="85ef9-902">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="85ef9-903">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-903">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="85ef9-904">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="85ef9-904">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="85ef9-905">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-905">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="85ef9-906">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="85ef9-906">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="85ef9-907">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="85ef9-907">**Example**</span></span>

<span data-ttu-id="85ef9-908">Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-908">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="85ef9-909">Nome &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="85ef9-909">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="85ef9-910">Valor &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="85ef9-910">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="85ef9-911">Se `IConfiguration` for injetada e atribuída a um campo chamado `_config`, leia o valor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-911">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="85ef9-912">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="85ef9-912">File Configuration Provider</span></span>

<span data-ttu-id="85ef9-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="85ef9-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="85ef9-914">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-914">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="85ef9-915">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="85ef9-915">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="85ef9-916">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="85ef9-916">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="85ef9-917">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="85ef9-917">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="85ef9-918">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="85ef9-918">INI Configuration Provider</span></span>

<span data-ttu-id="85ef9-919">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-919">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="85ef9-920">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-920">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-921">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="85ef9-921">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="85ef9-922">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="85ef9-922">Overloads permit specifying:</span></span>

* <span data-ttu-id="85ef9-923">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="85ef9-923">Whether the file is optional.</span></span>
* <span data-ttu-id="85ef9-924">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-924">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="85ef9-925">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-925">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="85ef9-926">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="85ef9-926">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="85ef9-927">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="85ef9-927">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="85ef9-928">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-928">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="85ef9-929">section0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-929">section0:key0</span></span>
* <span data-ttu-id="85ef9-930">section0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-930">section0:key1</span></span>
* <span data-ttu-id="85ef9-931">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="85ef9-931">section1:subsection:key</span></span>
* <span data-ttu-id="85ef9-932">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="85ef9-932">section2:subsection0:key</span></span>
* <span data-ttu-id="85ef9-933">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="85ef9-933">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="85ef9-934">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="85ef9-934">JSON Configuration Provider</span></span>

<span data-ttu-id="85ef9-935">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-935">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="85ef9-936">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-936">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-937">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="85ef9-937">Overloads permit specifying:</span></span>

* <span data-ttu-id="85ef9-938">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="85ef9-938">Whether the file is optional.</span></span>
* <span data-ttu-id="85ef9-939">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-939">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="85ef9-940">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-940">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="85ef9-941">`AddJsonFile` é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-941">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="85ef9-942">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="85ef9-942">The method is called to load configuration from:</span></span>

* <span data-ttu-id="85ef9-943">*appSettings. json* &ndash; este arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="85ef9-943">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="85ef9-944">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-944">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="85ef9-945">*appSettings. {Environment}. JSON* &ndash; a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="85ef9-945">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="85ef9-946">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="85ef9-946">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="85ef9-947">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="85ef9-947">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="85ef9-948">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85ef9-948">Environment variables.</span></span>
* <span data-ttu-id="85ef9-949">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-949">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="85ef9-950">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="85ef9-950">Command-line arguments.</span></span>

<span data-ttu-id="85ef9-951">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="85ef9-951">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="85ef9-952">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-952">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="85ef9-953">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-953">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="85ef9-954">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="85ef9-954">**Example**</span></span>

<span data-ttu-id="85ef9-955">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-955">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="85ef9-956">A primeira chamada para `AddJsonFile` carrega a configuração de *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-956">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="85ef9-957">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-957">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="85ef9-958">Para *appSettings. Development. JSON* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="85ef9-958">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="85ef9-959">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-959">Run the sample app.</span></span> <span data-ttu-id="85ef9-960">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-960">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="85ef9-961">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-961">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="85ef9-962">O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="85ef9-962">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="85ef9-963">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="85ef9-963">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="85ef9-964">Abra o arquivo *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="85ef9-964">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="85ef9-965">No perfil de `ConfigurationSample`, altere o valor da variável de ambiente `ASPNETCORE_ENVIRONMENT` para `Production`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-965">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="85ef9-966">Salve o arquivo e execute o aplicativo com `dotnet run` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="85ef9-966">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="85ef9-967">As configurações em *appSettings. O Development. JSON* não substitui mais as configurações em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-967">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="85ef9-968">O nível de log para a `Logging:LogLevel:Default` de chave é `Warning`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-968">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="85ef9-969">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="85ef9-969">XML Configuration Provider</span></span>

<span data-ttu-id="85ef9-970">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="85ef9-970">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="85ef9-971">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-971">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-972">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="85ef9-972">Overloads permit specifying:</span></span>

* <span data-ttu-id="85ef9-973">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="85ef9-973">Whether the file is optional.</span></span>
* <span data-ttu-id="85ef9-974">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-974">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="85ef9-975">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-975">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="85ef9-976">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-976">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="85ef9-977">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-977">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="85ef9-978">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="85ef9-978">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="85ef9-979">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="85ef9-979">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="85ef9-980">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-980">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="85ef9-981">section0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-981">section0:key0</span></span>
* <span data-ttu-id="85ef9-982">section0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-982">section0:key1</span></span>
* <span data-ttu-id="85ef9-983">section1:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-983">section1:key0</span></span>
* <span data-ttu-id="85ef9-984">section1:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-984">section1:key1</span></span>

<span data-ttu-id="85ef9-985">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="85ef9-985">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="85ef9-986">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-986">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="85ef9-987">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-987">section:section0:key:key0</span></span>
* <span data-ttu-id="85ef9-988">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-988">section:section0:key:key1</span></span>
* <span data-ttu-id="85ef9-989">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-989">section:section1:key:key0</span></span>
* <span data-ttu-id="85ef9-990">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-990">section:section1:key:key1</span></span>

<span data-ttu-id="85ef9-991">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="85ef9-991">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="85ef9-992">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-992">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="85ef9-993">key:attribute</span><span class="sxs-lookup"><span data-stu-id="85ef9-993">key:attribute</span></span>
* <span data-ttu-id="85ef9-994">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="85ef9-994">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="85ef9-995">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="85ef9-995">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="85ef9-996">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-996">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="85ef9-997">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-997">The key is the file name.</span></span> <span data-ttu-id="85ef9-998">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-998">The value contains the file's contents.</span></span> <span data-ttu-id="85ef9-999">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="85ef9-999">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="85ef9-1000">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1000">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="85ef9-1001">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1001">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="85ef9-1002">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1002">Overloads permit specifying:</span></span>

* <span data-ttu-id="85ef9-1003">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1003">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="85ef9-1004">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1004">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="85ef9-1005">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1005">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="85ef9-1006">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1006">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="85ef9-1007">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1007">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="85ef9-1008">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="85ef9-1008">Memory Configuration Provider</span></span>

<span data-ttu-id="85ef9-1009">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1009">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="85ef9-1010">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1010">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="85ef9-1011">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1011">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="85ef9-1012">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1012">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="85ef9-1013">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1013">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="85ef9-1014">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1014">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="85ef9-1015">GetValue</span><span class="sxs-lookup"><span data-stu-id="85ef9-1015">GetValue</span></span>

<span data-ttu-id="85ef9-1016">[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1016">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="85ef9-1017">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1017">An overload accepts a default value.</span></span>

<span data-ttu-id="85ef9-1018">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1018">The following example:</span></span>

* <span data-ttu-id="85ef9-1019">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1019">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="85ef9-1020">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1020">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="85ef9-1021">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1021">Types the value as an `int`.</span></span>
* <span data-ttu-id="85ef9-1022">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1022">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="85ef9-1023">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="85ef9-1023">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="85ef9-1024">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1024">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="85ef9-1025">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1025">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="85ef9-1026">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1026">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="85ef9-1027">section0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1027">section0:key0</span></span>
* <span data-ttu-id="85ef9-1028">section0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1028">section0:key1</span></span>
* <span data-ttu-id="85ef9-1029">section1:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1029">section1:key0</span></span>
* <span data-ttu-id="85ef9-1030">section1:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1030">section1:key1</span></span>
* <span data-ttu-id="85ef9-1031">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1031">section2:subsection0:key0</span></span>
* <span data-ttu-id="85ef9-1032">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1032">section2:subsection0:key1</span></span>
* <span data-ttu-id="85ef9-1033">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1033">section2:subsection1:key0</span></span>
* <span data-ttu-id="85ef9-1034">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1034">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="85ef9-1035">GetSection</span><span class="sxs-lookup"><span data-stu-id="85ef9-1035">GetSection</span></span>

<span data-ttu-id="85ef9-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="85ef9-1037">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1037">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="85ef9-1038">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1038">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="85ef9-1039">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1039">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="85ef9-1040">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1040">`GetSection` never returns `null`.</span></span> <span data-ttu-id="85ef9-1041">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1041">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="85ef9-1042">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1042">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="85ef9-1043"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1043">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="85ef9-1044">GetChildren</span><span class="sxs-lookup"><span data-stu-id="85ef9-1044">GetChildren</span></span>

<span data-ttu-id="85ef9-1045">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1045">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="85ef9-1046">Exists</span><span class="sxs-lookup"><span data-stu-id="85ef9-1046">Exists</span></span>

<span data-ttu-id="85ef9-1047">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1047">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="85ef9-1048">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1048">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="85ef9-1049">Associar a uma classe</span><span class="sxs-lookup"><span data-stu-id="85ef9-1049">Bind to a class</span></span>

<span data-ttu-id="85ef9-1050">A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1050">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="85ef9-1051">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1051">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="85ef9-1052">Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="85ef9-1052">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="85ef9-1053">O associador associa valores a todas as propriedades públicas de leitura/gravação do tipo fornecido.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1053">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="85ef9-1054">Os campos **não** estão associados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1054">Fields are **not** bound.</span></span>

<span data-ttu-id="85ef9-1055">O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="85ef9-1055">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="85ef9-1056">A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1056">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="85ef9-1057">Os seguintes pares chave-valor de configuração são criados:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1057">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="85ef9-1058">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-1058">Key</span></span>                   | <span data-ttu-id="85ef9-1059">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-1059">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="85ef9-1060">starship:name</span><span class="sxs-lookup"><span data-stu-id="85ef9-1060">starship:name</span></span>         | <span data-ttu-id="85ef9-1061">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="85ef9-1061">USS Enterprise</span></span>                                    |
| <span data-ttu-id="85ef9-1062">starship:registry</span><span class="sxs-lookup"><span data-stu-id="85ef9-1062">starship:registry</span></span>     | <span data-ttu-id="85ef9-1063">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="85ef9-1063">NCC-1701</span></span>                                          |
| <span data-ttu-id="85ef9-1064">starship:class</span><span class="sxs-lookup"><span data-stu-id="85ef9-1064">starship:class</span></span>        | <span data-ttu-id="85ef9-1065">Constituição</span><span class="sxs-lookup"><span data-stu-id="85ef9-1065">Constitution</span></span>                                      |
| <span data-ttu-id="85ef9-1066">starship:length</span><span class="sxs-lookup"><span data-stu-id="85ef9-1066">starship:length</span></span>       | <span data-ttu-id="85ef9-1067">304,8</span><span class="sxs-lookup"><span data-stu-id="85ef9-1067">304.8</span></span>                                             |
| <span data-ttu-id="85ef9-1068">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="85ef9-1068">starship:commissioned</span></span> | <span data-ttu-id="85ef9-1069">Falso</span><span class="sxs-lookup"><span data-stu-id="85ef9-1069">False</span></span>                                             |
| <span data-ttu-id="85ef9-1070">marca</span><span class="sxs-lookup"><span data-stu-id="85ef9-1070">trademark</span></span>             | <span data-ttu-id="85ef9-1071">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="85ef9-1071">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="85ef9-1072">O aplicativo de exemplo chama `GetSection` com a chave `starship`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1072">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="85ef9-1073">Os pares chave-valor `starship` são isolados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1073">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="85ef9-1074">O método `Bind` é chamado na subseção passando uma instância da classe `Starship`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1074">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="85ef9-1075">Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1075">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="85ef9-1076">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="85ef9-1076">Bind to an object graph</span></span>

<span data-ttu-id="85ef9-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="85ef9-1078">Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1078">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="85ef9-1079">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="85ef9-1079">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="85ef9-1080">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1080">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="85ef9-1081">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1081">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="85ef9-1082">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1082">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="85ef9-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="85ef9-1084">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1084">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="85ef9-1085">O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1085">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="85ef9-1086">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="85ef9-1086">Bind an array to a class</span></span>

<span data-ttu-id="85ef9-1087">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="85ef9-1087">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="85ef9-1088">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1088">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="85ef9-1089">Qualquer formato de matriz que expõe um segmento de chave numérico (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar a matriz a uma matriz de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1089">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="85ef9-1090">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1090">Binding is provided by convention.</span></span> <span data-ttu-id="85ef9-1091">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1091">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="85ef9-1092">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="85ef9-1092">**In-memory array processing**</span></span>

<span data-ttu-id="85ef9-1093">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1093">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="85ef9-1094">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-1094">Key</span></span>             | <span data-ttu-id="85ef9-1095">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-1095">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="85ef9-1096">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1096">array:entries:0</span></span> | <span data-ttu-id="85ef9-1097">value0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1097">value0</span></span> |
| <span data-ttu-id="85ef9-1098">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1098">array:entries:1</span></span> | <span data-ttu-id="85ef9-1099">value1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1099">value1</span></span> |
| <span data-ttu-id="85ef9-1100">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="85ef9-1100">array:entries:2</span></span> | <span data-ttu-id="85ef9-1101">value2</span><span class="sxs-lookup"><span data-stu-id="85ef9-1101">value2</span></span> |
| <span data-ttu-id="85ef9-1102">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="85ef9-1102">array:entries:4</span></span> | <span data-ttu-id="85ef9-1103">value4</span><span class="sxs-lookup"><span data-stu-id="85ef9-1103">value4</span></span> |
| <span data-ttu-id="85ef9-1104">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="85ef9-1104">array:entries:5</span></span> | <span data-ttu-id="85ef9-1105">value5</span><span class="sxs-lookup"><span data-stu-id="85ef9-1105">value5</span></span> |

<span data-ttu-id="85ef9-1106">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1106">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="85ef9-1107">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1107">The array skips a value for index &num;3.</span></span> <span data-ttu-id="85ef9-1108">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1108">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="85ef9-1109">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1109">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="85ef9-1110">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1110">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="85ef9-1111">A sintaxe [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1111">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="85ef9-1112">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1112">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="85ef9-1113">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="85ef9-1113">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="85ef9-1114">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-1114">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="85ef9-1115">0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1115">0</span></span>                            | <span data-ttu-id="85ef9-1116">value0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1116">value0</span></span>                       |
| <span data-ttu-id="85ef9-1117">1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1117">1</span></span>                            | <span data-ttu-id="85ef9-1118">value1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1118">value1</span></span>                       |
| <span data-ttu-id="85ef9-1119">2</span><span class="sxs-lookup"><span data-stu-id="85ef9-1119">2</span></span>                            | <span data-ttu-id="85ef9-1120">value2</span><span class="sxs-lookup"><span data-stu-id="85ef9-1120">value2</span></span>                       |
| <span data-ttu-id="85ef9-1121">3</span><span class="sxs-lookup"><span data-stu-id="85ef9-1121">3</span></span>                            | <span data-ttu-id="85ef9-1122">value4</span><span class="sxs-lookup"><span data-stu-id="85ef9-1122">value4</span></span>                       |
| <span data-ttu-id="85ef9-1123">4</span><span class="sxs-lookup"><span data-stu-id="85ef9-1123">4</span></span>                            | <span data-ttu-id="85ef9-1124">value5</span><span class="sxs-lookup"><span data-stu-id="85ef9-1124">value5</span></span>                       |

<span data-ttu-id="85ef9-1125">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1125">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="85ef9-1126">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1126">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="85ef9-1127">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1127">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="85ef9-1128">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1128">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="85ef9-1129">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1129">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="85ef9-1130">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1130">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="85ef9-1131">Em `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1131">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="85ef9-1132">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1132">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="85ef9-1133">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-1133">Key</span></span>             | <span data-ttu-id="85ef9-1134">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-1134">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="85ef9-1135">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="85ef9-1135">array:entries:3</span></span> | <span data-ttu-id="85ef9-1136">value3</span><span class="sxs-lookup"><span data-stu-id="85ef9-1136">value3</span></span> |

<span data-ttu-id="85ef9-1137">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1137">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="85ef9-1138">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="85ef9-1138">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="85ef9-1139">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-1139">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="85ef9-1140">0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1140">0</span></span>                            | <span data-ttu-id="85ef9-1141">value0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1141">value0</span></span>                       |
| <span data-ttu-id="85ef9-1142">1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1142">1</span></span>                            | <span data-ttu-id="85ef9-1143">value1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1143">value1</span></span>                       |
| <span data-ttu-id="85ef9-1144">2</span><span class="sxs-lookup"><span data-stu-id="85ef9-1144">2</span></span>                            | <span data-ttu-id="85ef9-1145">value2</span><span class="sxs-lookup"><span data-stu-id="85ef9-1145">value2</span></span>                       |
| <span data-ttu-id="85ef9-1146">3</span><span class="sxs-lookup"><span data-stu-id="85ef9-1146">3</span></span>                            | <span data-ttu-id="85ef9-1147">value3</span><span class="sxs-lookup"><span data-stu-id="85ef9-1147">value3</span></span>                       |
| <span data-ttu-id="85ef9-1148">4</span><span class="sxs-lookup"><span data-stu-id="85ef9-1148">4</span></span>                            | <span data-ttu-id="85ef9-1149">value4</span><span class="sxs-lookup"><span data-stu-id="85ef9-1149">value4</span></span>                       |
| <span data-ttu-id="85ef9-1150">5</span><span class="sxs-lookup"><span data-stu-id="85ef9-1150">5</span></span>                            | <span data-ttu-id="85ef9-1151">value5</span><span class="sxs-lookup"><span data-stu-id="85ef9-1151">value5</span></span>                       |

<span data-ttu-id="85ef9-1152">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="85ef9-1152">**JSON array processing**</span></span>

<span data-ttu-id="85ef9-1153">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1153">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="85ef9-1154">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1154">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="85ef9-1155">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1155">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="85ef9-1156">Chave</span><span class="sxs-lookup"><span data-stu-id="85ef9-1156">Key</span></span>                     | <span data-ttu-id="85ef9-1157">Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-1157">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="85ef9-1158">json_array:key</span><span class="sxs-lookup"><span data-stu-id="85ef9-1158">json_array:key</span></span>          | <span data-ttu-id="85ef9-1159">valueA</span><span class="sxs-lookup"><span data-stu-id="85ef9-1159">valueA</span></span> |
| <span data-ttu-id="85ef9-1160">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1160">json_array:subsection:0</span></span> | <span data-ttu-id="85ef9-1161">valueB</span><span class="sxs-lookup"><span data-stu-id="85ef9-1161">valueB</span></span> |
| <span data-ttu-id="85ef9-1162">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1162">json_array:subsection:1</span></span> | <span data-ttu-id="85ef9-1163">valueC</span><span class="sxs-lookup"><span data-stu-id="85ef9-1163">valueC</span></span> |
| <span data-ttu-id="85ef9-1164">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="85ef9-1164">json_array:subsection:2</span></span> | <span data-ttu-id="85ef9-1165">valueD</span><span class="sxs-lookup"><span data-stu-id="85ef9-1165">valueD</span></span> |

<span data-ttu-id="85ef9-1166">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1166">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="85ef9-1167">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1167">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="85ef9-1168">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1168">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="85ef9-1169">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="85ef9-1169">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="85ef9-1170">`JsonArrayExample.Subsection` Valor</span><span class="sxs-lookup"><span data-stu-id="85ef9-1170">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="85ef9-1171">0</span><span class="sxs-lookup"><span data-stu-id="85ef9-1171">0</span></span>                                   | <span data-ttu-id="85ef9-1172">valueB</span><span class="sxs-lookup"><span data-stu-id="85ef9-1172">valueB</span></span>                              |
| <span data-ttu-id="85ef9-1173">1</span><span class="sxs-lookup"><span data-stu-id="85ef9-1173">1</span></span>                                   | <span data-ttu-id="85ef9-1174">valueC</span><span class="sxs-lookup"><span data-stu-id="85ef9-1174">valueC</span></span>                              |
| <span data-ttu-id="85ef9-1175">2</span><span class="sxs-lookup"><span data-stu-id="85ef9-1175">2</span></span>                                   | <span data-ttu-id="85ef9-1176">valueD</span><span class="sxs-lookup"><span data-stu-id="85ef9-1176">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="85ef9-1177">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="85ef9-1177">Custom configuration provider</span></span>

<span data-ttu-id="85ef9-1178">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="85ef9-1178">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="85ef9-1179">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1179">The provider has the following characteristics:</span></span>

* <span data-ttu-id="85ef9-1180">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1180">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="85ef9-1181">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1181">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="85ef9-1182">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1182">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="85ef9-1183">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1183">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="85ef9-1184">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1184">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="85ef9-1185">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1185">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="85ef9-1186">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1186">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="85ef9-1187">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1187">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="85ef9-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="85ef9-1189">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1189">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="85ef9-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="85ef9-1191">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1191">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="85ef9-1192">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1192">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="85ef9-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="85ef9-1194">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1194">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="85ef9-1195">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1195">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="85ef9-1196">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1196">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="85ef9-1197">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="85ef9-1197">Access configuration during startup</span></span>

<span data-ttu-id="85ef9-1198">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1198">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="85ef9-1199">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1199">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="85ef9-1200">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="85ef9-1200">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="85ef9-1201">Acessar a configuração em uma página do Razor Pages ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="85ef9-1201">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="85ef9-1202">Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1202">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="85ef9-1203">Em uma página do Razor:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1203">In a Razor Pages page:</span></span>

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

<span data-ttu-id="85ef9-1204">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="85ef9-1204">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="85ef9-1205">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="85ef9-1205">Add configuration from an external assembly</span></span>

<span data-ttu-id="85ef9-1206">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1206">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="85ef9-1207">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="85ef9-1207">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="85ef9-1208">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="85ef9-1208">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
