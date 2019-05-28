---
title: Configuração no ASP.NET Core
author: guardrex
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/24/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 3f7588f9ba18e300f5947e8bb0daf2e72d580a94
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223159"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="f2e31-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f2e31-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="f2e31-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f2e31-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f2e31-105">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="f2e31-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="f2e31-106">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="f2e31-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="f2e31-107">Azure Key Vault</span></span>
* <span data-ttu-id="f2e31-108">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="f2e31-108">Command-line arguments</span></span>
* <span data-ttu-id="f2e31-109">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="f2e31-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="f2e31-110">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="f2e31-110">Directory files</span></span>
* <span data-ttu-id="f2e31-111">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="f2e31-111">Environment variables</span></span>
* <span data-ttu-id="f2e31-112">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="f2e31-112">In-memory .NET objects</span></span>
* <span data-ttu-id="f2e31-113">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="f2e31-113">Settings files</span></span>

<span data-ttu-id="f2e31-114">Pacotes de configuração para cenários comuns de provedor de configuração estão incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-114">Configuration packages for common configuration provider scenarios are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f2e31-115">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="f2e31-115">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="f2e31-116">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="f2e31-116">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="f2e31-117">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="f2e31-117">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="f2e31-118">Para saber mais sobre como usar o padrão de opções, confira <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-118">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="f2e31-119">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f2e31-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-vs-app-configuration"></a><span data-ttu-id="f2e31-120">Configuração do host versus aplicativo</span><span class="sxs-lookup"><span data-stu-id="f2e31-120">Host vs. app configuration</span></span>

<span data-ttu-id="f2e31-121">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-121">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="f2e31-122">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-122">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="f2e31-123">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="f2e31-123">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="f2e31-124">Os pares chave-valor de configuração do host se tornam parte da configuração global do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-124">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="f2e31-125">Para saber mais sobre como os provedores de configuração são usados quando o host for criado e como as fontes de configuração afetam o host e a configuração, veja [O host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="f2e31-125">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see [The host](xref:fundamentals/index#host).</span></span>

## <a name="default-configuration"></a><span data-ttu-id="f2e31-126">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="f2e31-126">Default configuration</span></span>

<span data-ttu-id="f2e31-127">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="f2e31-127">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="f2e31-128"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="f2e31-128"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

* <span data-ttu-id="f2e31-129">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="f2e31-129">Host configuration is provided from:</span></span>
  * <span data-ttu-id="f2e31-130">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f2e31-130">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="f2e31-131">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-131">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="f2e31-132">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f2e31-132">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="f2e31-133">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="f2e31-133">App configuration is provided from:</span></span>
  * <span data-ttu-id="f2e31-134">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f2e31-134">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="f2e31-135">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f2e31-135">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="f2e31-136">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="f2e31-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="f2e31-137">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f2e31-137">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="f2e31-138">Se um prefixo personalizado for usado (por exemplo, `PREFIX_` com `.AddEnvironmentVariables(prefix: "PREFIX_")`), o prefixo será removido durante o carregamento dos pares de chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-138">If a custom prefix is used (for example, `PREFIX_` with `.AddEnvironmentVariables(prefix: "PREFIX_")`), the prefix is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="f2e31-139">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f2e31-139">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="f2e31-140">Os provedores de configuração são explicados posteriormente neste tópico.</span><span class="sxs-lookup"><span data-stu-id="f2e31-140">The configuration providers are explained later in this topic.</span></span> <span data-ttu-id="f2e31-141">Para obter mais informações sobre o host e <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, consulte <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-141">For more information on the host and <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="security"></a><span data-ttu-id="f2e31-142">Segurança</span><span class="sxs-lookup"><span data-stu-id="f2e31-142">Security</span></span>

<span data-ttu-id="f2e31-143">Adote as melhores práticas a seguir:</span><span class="sxs-lookup"><span data-stu-id="f2e31-143">Adopt the following best practices:</span></span>

* <span data-ttu-id="f2e31-144">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="f2e31-144">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="f2e31-145">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f2e31-145">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="f2e31-146">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="f2e31-146">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="f2e31-147">Saiba mais sobre [como usar vários ambientes](xref:fundamentals/environments) e gerenciar o [armazenamento seguro de segredos de aplicativo em desenvolvimento com o Gerenciador de Segredo](xref:security/app-secrets) (inclui recomendações sobre como usar variáveis de ambiente para armazenar dados confidenciais).</span><span class="sxs-lookup"><span data-stu-id="f2e31-147">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="f2e31-148">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="f2e31-148">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="f2e31-149">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="f2e31-149">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="f2e31-150">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) é uma opção para o armazenamento seguro de segredos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-150">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="f2e31-151">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-151">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="f2e31-152">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="f2e31-152">Hierarchical configuration data</span></span>

<span data-ttu-id="f2e31-153">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-153">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="f2e31-154">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="f2e31-154">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="f2e31-155">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-155">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="f2e31-156">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="f2e31-156">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="f2e31-157">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-157">section0:key0</span></span>
* <span data-ttu-id="f2e31-158">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-158">section0:key1</span></span>
* <span data-ttu-id="f2e31-159">section1:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-159">section1:key0</span></span>
* <span data-ttu-id="f2e31-160">section1:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-160">section1:key1</span></span>

<span data-ttu-id="f2e31-161">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-161"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="f2e31-162">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="f2e31-162">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span> <span data-ttu-id="f2e31-163">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-163">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="conventions"></a><span data-ttu-id="f2e31-164">Convenções</span><span class="sxs-lookup"><span data-stu-id="f2e31-164">Conventions</span></span>

<span data-ttu-id="f2e31-165">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-165">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="f2e31-166">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="f2e31-166">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="f2e31-167">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-167">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="f2e31-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f2e31-169"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetado em um <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> do Razor Pages para obter a configuração para a classe:</span><span class="sxs-lookup"><span data-stu-id="f2e31-169"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

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

<span data-ttu-id="f2e31-170">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="f2e31-170">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="f2e31-171">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="f2e31-171">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="f2e31-172">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="f2e31-172">Keys are case-insensitive.</span></span> <span data-ttu-id="f2e31-173">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="f2e31-173">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="f2e31-174">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-174">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="f2e31-175">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="f2e31-175">Hierarchical keys</span></span>
  * <span data-ttu-id="f2e31-176">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="f2e31-176">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="f2e31-177">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="f2e31-177">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="f2e31-178">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="f2e31-178">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="f2e31-179">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="f2e31-179">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="f2e31-180">Você deve fornecer o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-180">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="f2e31-181">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-181">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="f2e31-182">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="f2e31-182">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="f2e31-183">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="f2e31-183">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="f2e31-184">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="f2e31-184">Values are strings.</span></span>
* <span data-ttu-id="f2e31-185">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="f2e31-185">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="f2e31-186">Provedores</span><span class="sxs-lookup"><span data-stu-id="f2e31-186">Providers</span></span>

<span data-ttu-id="f2e31-187">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f2e31-187">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="f2e31-188">Provider</span><span class="sxs-lookup"><span data-stu-id="f2e31-188">Provider</span></span> | <span data-ttu-id="f2e31-189">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="f2e31-189">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="f2e31-190">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="f2e31-190">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="f2e31-191">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="f2e31-191">Azure Key Vault</span></span> |
| [<span data-ttu-id="f2e31-192">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="f2e31-192">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="f2e31-193">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="f2e31-193">Command-line parameters</span></span> |
| [<span data-ttu-id="f2e31-194">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="f2e31-194">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="f2e31-195">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="f2e31-195">Custom source</span></span> |
| [<span data-ttu-id="f2e31-196">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="f2e31-196">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="f2e31-197">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="f2e31-197">Environment variables</span></span> |
| [<span data-ttu-id="f2e31-198">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="f2e31-198">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="f2e31-199">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="f2e31-199">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="f2e31-200">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="f2e31-200">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="f2e31-201">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="f2e31-201">Directory files</span></span> |
| [<span data-ttu-id="f2e31-202">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="f2e31-202">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="f2e31-203">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="f2e31-203">In-memory collections</span></span> |
| <span data-ttu-id="f2e31-204">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="f2e31-204">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="f2e31-205">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="f2e31-205">File in the user profile directory</span></span> |

<span data-ttu-id="f2e31-206">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-206">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="f2e31-207">Os provedores de configuração descritos neste tópico estão descritos em ordem alfabética, não na ordem na qual seu código pode organizá-los.</span><span class="sxs-lookup"><span data-stu-id="f2e31-207">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="f2e31-208">Organize os provedores de configuração em seu código para atender às suas prioridades para as fontes de configuração subjacentes.</span><span class="sxs-lookup"><span data-stu-id="f2e31-208">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="f2e31-209">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="f2e31-209">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="f2e31-210">Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="f2e31-210">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="f2e31-211">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="f2e31-211">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="f2e31-212">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="f2e31-212">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="f2e31-213">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="f2e31-213">Environment variables</span></span>
1. <span data-ttu-id="f2e31-214">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="f2e31-214">Command-line arguments</span></span>

<span data-ttu-id="f2e31-215">É comum posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="f2e31-215">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="f2e31-216">Essa sequência de provedores é aplicada quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-216">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="f2e31-217">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="f2e31-217">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

## <a name="configureappconfiguration"></a><span data-ttu-id="f2e31-218">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="f2e31-218">ConfigureAppConfiguration</span></span>

<span data-ttu-id="f2e31-219">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="f2e31-219">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

<span data-ttu-id="f2e31-220">a configuração fornecida para o aplicativo no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-220">Configuration supplied to the app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f2e31-221">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="f2e31-221">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="f2e31-222">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="f2e31-222">Command-line Configuration Provider</span></span>

<span data-ttu-id="f2e31-223">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="f2e31-223">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="f2e31-224">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-224">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f2e31-225">`AddCommandLine` é chamado automaticamente quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-225">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="f2e31-226">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="f2e31-226">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="f2e31-227">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="f2e31-227">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="f2e31-228">Configuração opcional de *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="f2e31-228">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="f2e31-229">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="f2e31-229">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="f2e31-230">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="f2e31-230">Environment variables.</span></span>

<span data-ttu-id="f2e31-231">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="f2e31-231">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="f2e31-232">Os argumentos de linha de comando passados em tempo de execução substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="f2e31-232">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="f2e31-233">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="f2e31-233">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="f2e31-234">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-234">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="f2e31-235">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-235">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="f2e31-236">`AddCommandLine` já foi chamado por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-236">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f2e31-237">Se precisar oferecer configuração de aplicativo e ainda poder substituir essa configuração por argumentos de linha de comando, chame os provedores adicionais do aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> e chame `AddCommandLine` por fim.</span><span class="sxs-lookup"><span data-stu-id="f2e31-237">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call other providers here and call AddCommandLine last.
                config.AddCommandLine(args);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f2e31-238">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-238">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    // Call additional providers here as needed.
    // Call AddCommandLine last to allow arguments to override other configuration.
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f2e31-239">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="f2e31-239">**Example**</span></span>

<span data-ttu-id="f2e31-240">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-240">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="f2e31-241">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="f2e31-241">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="f2e31-242">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-242">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="f2e31-243">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-243">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="f2e31-244">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-244">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="f2e31-245">Arguments</span><span class="sxs-lookup"><span data-stu-id="f2e31-245">Arguments</span></span>

<span data-ttu-id="f2e31-246">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="f2e31-246">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="f2e31-247">O valor pode ser nulo se um sinal de igual for usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="f2e31-247">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="f2e31-248">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="f2e31-248">Key prefix</span></span>               | <span data-ttu-id="f2e31-249">Exemplo</span><span class="sxs-lookup"><span data-stu-id="f2e31-249">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="f2e31-250">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="f2e31-250">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="f2e31-251">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="f2e31-251">Two dashes (`--`)</span></span>        | <span data-ttu-id="f2e31-252">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="f2e31-252">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="f2e31-253">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="f2e31-253">Forward slash (`/`)</span></span>      | <span data-ttu-id="f2e31-254">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="f2e31-254">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="f2e31-255">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="f2e31-255">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="f2e31-256">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="f2e31-256">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="f2e31-257">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="f2e31-257">Switch mappings</span></span>

<span data-ttu-id="f2e31-258">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="f2e31-258">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="f2e31-259">Quando você cria manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, pode fornecer um dicionário de substituições de opções para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-259">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="f2e31-260">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="f2e31-260">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="f2e31-261">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-261">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="f2e31-262">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="f2e31-262">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="f2e31-263">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="f2e31-263">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="f2e31-264">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="f2e31-264">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="f2e31-265">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="f2e31-265">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="f2e31-266">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f2e31-266">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _switchMappings = 
        new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    // Do not pass the args to CreateDefaultBuilder
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder()
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddCommandLine(args, _switchMappings);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f2e31-267">Conforme mostrado no exemplo anterior, a chamada para `CreateDefaultBuilder` não deve passar argumentos ao usar mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="f2e31-267">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="f2e31-268">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-268">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f2e31-269">Se os argumentos incluírem uma opção mapeada e forem passados para `CreateDefaultBuilder`, o provedor `AddCommandLine` não inicializará com um <xref:System.FormatException>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-269">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="f2e31-270">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="f2e31-270">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="f2e31-271">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f2e31-271">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="f2e31-272">Chave</span><span class="sxs-lookup"><span data-stu-id="f2e31-272">Key</span></span>       | <span data-ttu-id="f2e31-273">Valor</span><span class="sxs-lookup"><span data-stu-id="f2e31-273">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="f2e31-274">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="f2e31-274">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="f2e31-275">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f2e31-275">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="f2e31-276">Chave</span><span class="sxs-lookup"><span data-stu-id="f2e31-276">Key</span></span>               | <span data-ttu-id="f2e31-277">Valor</span><span class="sxs-lookup"><span data-stu-id="f2e31-277">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="f2e31-278">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="f2e31-278">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="f2e31-279">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="f2e31-279">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="f2e31-280">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-280">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="f2e31-281">O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) permite que você defina variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o Provedor de Configuração de Variáveis de Ambiente.</span><span class="sxs-lookup"><span data-stu-id="f2e31-281">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="f2e31-282">Para obter mais informações, confira [Aplicativos Azure: Substituir a configuração do aplicativo no Portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="f2e31-282">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="f2e31-283">`AddEnvironmentVariables` é chamado automaticamente para as variáveis de ambiente prefixadas com `ASPNETCORE_` quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-283">`AddEnvironmentVariables` is automatically called for environment variables prefixed with `ASPNETCORE_` when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="f2e31-284">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="f2e31-284">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="f2e31-285">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="f2e31-285">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="f2e31-286">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-286">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="f2e31-287">Configuração opcional de *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="f2e31-287">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="f2e31-288">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="f2e31-288">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="f2e31-289">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="f2e31-289">Command-line arguments.</span></span>

<span data-ttu-id="f2e31-290">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="f2e31-290">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="f2e31-291">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em tempo de execução para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="f2e31-291">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="f2e31-292">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-292">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="f2e31-293">Se precisar fornecer configuração do aplicativo com base em variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>e chame `AddEnvironmentVariables` com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-293">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call additional providers here as needed.
                // Call AddEnvironmentVariables last if you need to allow
                // environment variables to override values from other 
                // providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f2e31-294">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-294">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f2e31-295">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="f2e31-295">**Example**</span></span>

<span data-ttu-id="f2e31-296">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-296">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="f2e31-297">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-297">Run the sample app.</span></span> <span data-ttu-id="f2e31-298">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-298">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="f2e31-299">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-299">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="f2e31-300">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="f2e31-300">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="f2e31-301">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente para mostrar as que começam com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f2e31-301">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="f2e31-302">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="f2e31-302">ASPNETCORE_</span></span>
* <span data-ttu-id="f2e31-303">urls</span><span class="sxs-lookup"><span data-stu-id="f2e31-303">urls</span></span>
* <span data-ttu-id="f2e31-304">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="f2e31-304">Logging</span></span>
* <span data-ttu-id="f2e31-305">AMBIENTE</span><span class="sxs-lookup"><span data-stu-id="f2e31-305">ENVIRONMENT</span></span>
* <span data-ttu-id="f2e31-306">contentRoot</span><span class="sxs-lookup"><span data-stu-id="f2e31-306">contentRoot</span></span>
* <span data-ttu-id="f2e31-307">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="f2e31-307">AllowedHosts</span></span>
* <span data-ttu-id="f2e31-308">applicationName</span><span class="sxs-lookup"><span data-stu-id="f2e31-308">applicationName</span></span>
* <span data-ttu-id="f2e31-309">CommandLine</span><span class="sxs-lookup"><span data-stu-id="f2e31-309">CommandLine</span></span>

<span data-ttu-id="f2e31-310">Se você quiser expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *Pages/Index.cshtml.cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f2e31-310">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="f2e31-311">Prefixos</span><span class="sxs-lookup"><span data-stu-id="f2e31-311">Prefixes</span></span>

<span data-ttu-id="f2e31-312">Variáveis de ambiente carregadas na configuração do aplicativo são filtradas quando você fornece um prefixo para o método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-312">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="f2e31-313">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-313">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="f2e31-314">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-314">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="f2e31-315">O método de conveniência estático `CreateDefaultBuilder` cria um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> para estabelecer o host do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-315">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="f2e31-316">Quando `WebHostBuilder` é criado, ele encontra a configuração de seu host nas variáveis de ambiente prefixadas com `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-316">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

<span data-ttu-id="f2e31-317">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="f2e31-317">**Connection string prefixes**</span></span>

<span data-ttu-id="f2e31-318">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-318">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="f2e31-319">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-319">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="f2e31-320">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="f2e31-320">Connection string prefix</span></span> | <span data-ttu-id="f2e31-321">Provider</span><span class="sxs-lookup"><span data-stu-id="f2e31-321">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="f2e31-322">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="f2e31-322">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="f2e31-323">MySQL</span><span class="sxs-lookup"><span data-stu-id="f2e31-323">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="f2e31-324">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="f2e31-324">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="f2e31-325">SQL Server</span><span class="sxs-lookup"><span data-stu-id="f2e31-325">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="f2e31-326">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="f2e31-326">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="f2e31-327">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="f2e31-327">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="f2e31-328">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="f2e31-328">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="f2e31-329">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="f2e31-329">Environment variable key</span></span> | <span data-ttu-id="f2e31-330">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="f2e31-330">Converted configuration key</span></span> | <span data-ttu-id="f2e31-331">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="f2e31-331">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="f2e31-332">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="f2e31-332">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="f2e31-333">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f2e31-333">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="f2e31-334">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="f2e31-334">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="f2e31-335">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f2e31-335">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="f2e31-336">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="f2e31-336">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="f2e31-337">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f2e31-337">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="f2e31-338">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="f2e31-338">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="f2e31-339">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="f2e31-339">File Configuration Provider</span></span>

<span data-ttu-id="f2e31-340"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="f2e31-340"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="f2e31-341">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="f2e31-341">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="f2e31-342">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="f2e31-342">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="f2e31-343">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="f2e31-343">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="f2e31-344">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="f2e31-344">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="f2e31-345">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="f2e31-345">INI Configuration Provider</span></span>

<span data-ttu-id="f2e31-346">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="f2e31-346">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="f2e31-347">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-347">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f2e31-348">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="f2e31-348">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="f2e31-349">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="f2e31-349">Overloads permit specifying:</span></span>

* <span data-ttu-id="f2e31-350">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="f2e31-350">Whether the file is optional.</span></span>
* <span data-ttu-id="f2e31-351">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-351">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="f2e31-352">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-352">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="f2e31-353">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f2e31-353">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddIniFile(
                    "config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f2e31-354">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-354">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f2e31-355">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-355">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-356">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-356">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddIniFile("config.ini", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f2e31-357">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-357">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f2e31-358">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-358">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-359">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="f2e31-359">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="f2e31-360">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="f2e31-360">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f2e31-361">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-361">section0:key0</span></span>
* <span data-ttu-id="f2e31-362">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-362">section0:key1</span></span>
* <span data-ttu-id="f2e31-363">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="f2e31-363">section1:subsection:key</span></span>
* <span data-ttu-id="f2e31-364">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="f2e31-364">section2:subsection0:key</span></span>
* <span data-ttu-id="f2e31-365">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="f2e31-365">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="f2e31-366">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="f2e31-366">JSON Configuration Provider</span></span>

<span data-ttu-id="f2e31-367">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="f2e31-367">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="f2e31-368">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-368">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f2e31-369">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="f2e31-369">Overloads permit specifying:</span></span>

* <span data-ttu-id="f2e31-370">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="f2e31-370">Whether the file is optional.</span></span>
* <span data-ttu-id="f2e31-371">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-371">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="f2e31-372">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-372">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="f2e31-373">`AddJsonFile` é chamado automaticamente duas vezes quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-373">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="f2e31-374">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="f2e31-374">The method is called to load configuration from:</span></span>

* <span data-ttu-id="f2e31-375">*appsettings.json* &ndash; Esse arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="f2e31-375">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="f2e31-376">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f2e31-376">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="f2e31-377">*appsettings.{Environment}.json* &ndash; A versão de ambiente do arquivo é carregada com base em [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="f2e31-377">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="f2e31-378">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="f2e31-378">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="f2e31-379">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="f2e31-379">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="f2e31-380">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="f2e31-380">Environment variables.</span></span>
* <span data-ttu-id="f2e31-381">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="f2e31-381">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="f2e31-382">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="f2e31-382">Command-line arguments.</span></span>

<span data-ttu-id="f2e31-383">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="f2e31-383">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="f2e31-384">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="f2e31-384">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="f2e31-385">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="f2e31-385">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddJsonFile(
                    "config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f2e31-386">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-386">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f2e31-387">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-387">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-388">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-388">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("config.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f2e31-389">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-389">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f2e31-390">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-390">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-391">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="f2e31-391">**Example**</span></span>

<span data-ttu-id="f2e31-392">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-392">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="f2e31-393">A configuração é carregada de *appsettings.json* e de *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="f2e31-393">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="f2e31-394">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-394">Run the sample app.</span></span> <span data-ttu-id="f2e31-395">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-395">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="f2e31-396">Observe que a saída contém pares chave-valor para a configuração mostrada na tabela de acordo com o ambiente.</span><span class="sxs-lookup"><span data-stu-id="f2e31-396">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="f2e31-397">Chaves de configuração de registro usam os dois-pontos (`:`) como um separador hierárquico.</span><span class="sxs-lookup"><span data-stu-id="f2e31-397">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="f2e31-398">Chave</span><span class="sxs-lookup"><span data-stu-id="f2e31-398">Key</span></span>                        | <span data-ttu-id="f2e31-399">Valor de Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="f2e31-399">Development Value</span></span> | <span data-ttu-id="f2e31-400">Valor de Produção</span><span class="sxs-lookup"><span data-stu-id="f2e31-400">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="f2e31-401">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="f2e31-401">Logging:LogLevel:System</span></span>    | <span data-ttu-id="f2e31-402">Informações</span><span class="sxs-lookup"><span data-stu-id="f2e31-402">Information</span></span>       | <span data-ttu-id="f2e31-403">Informações</span><span class="sxs-lookup"><span data-stu-id="f2e31-403">Information</span></span>      |
| <span data-ttu-id="f2e31-404">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="f2e31-404">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="f2e31-405">Informações</span><span class="sxs-lookup"><span data-stu-id="f2e31-405">Information</span></span>       | <span data-ttu-id="f2e31-406">Informações</span><span class="sxs-lookup"><span data-stu-id="f2e31-406">Information</span></span>      |
| <span data-ttu-id="f2e31-407">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="f2e31-407">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="f2e31-408">Depurar</span><span class="sxs-lookup"><span data-stu-id="f2e31-408">Debug</span></span>             | <span data-ttu-id="f2e31-409">Erro</span><span class="sxs-lookup"><span data-stu-id="f2e31-409">Error</span></span>            |
| <span data-ttu-id="f2e31-410">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="f2e31-410">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="f2e31-411">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="f2e31-411">XML Configuration Provider</span></span>

<span data-ttu-id="f2e31-412">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="f2e31-412">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="f2e31-413">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-413">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f2e31-414">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="f2e31-414">Overloads permit specifying:</span></span>

* <span data-ttu-id="f2e31-415">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="f2e31-415">Whether the file is optional.</span></span>
* <span data-ttu-id="f2e31-416">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-416">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="f2e31-417">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-417">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="f2e31-418">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="f2e31-418">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="f2e31-419">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-419">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="f2e31-420">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f2e31-420">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddXmlFile(
                    "config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f2e31-421">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-421">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f2e31-422">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-422">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-423">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-423">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f2e31-424">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-424">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f2e31-425">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-425">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-426">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="f2e31-426">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="f2e31-427">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="f2e31-427">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f2e31-428">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-428">section0:key0</span></span>
* <span data-ttu-id="f2e31-429">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-429">section0:key1</span></span>
* <span data-ttu-id="f2e31-430">section1:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-430">section1:key0</span></span>
* <span data-ttu-id="f2e31-431">section1:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-431">section1:key1</span></span>

<span data-ttu-id="f2e31-432">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="f2e31-432">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="f2e31-433">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="f2e31-433">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f2e31-434">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-434">section:section0:key:key0</span></span>
* <span data-ttu-id="f2e31-435">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-435">section:section0:key:key1</span></span>
* <span data-ttu-id="f2e31-436">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-436">section:section1:key:key0</span></span>
* <span data-ttu-id="f2e31-437">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-437">section:section1:key:key1</span></span>

<span data-ttu-id="f2e31-438">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="f2e31-438">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="f2e31-439">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="f2e31-439">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f2e31-440">key:attribute</span><span class="sxs-lookup"><span data-stu-id="f2e31-440">key:attribute</span></span>
* <span data-ttu-id="f2e31-441">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="f2e31-441">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="f2e31-442">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="f2e31-442">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="f2e31-443">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-443">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="f2e31-444">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-444">The key is the file name.</span></span> <span data-ttu-id="f2e31-445">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-445">The value contains the file's contents.</span></span> <span data-ttu-id="f2e31-446">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="f2e31-446">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="f2e31-447">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-447">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="f2e31-448">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="f2e31-448">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="f2e31-449">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="f2e31-449">Overloads permit specifying:</span></span>

* <span data-ttu-id="f2e31-450">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="f2e31-450">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="f2e31-451">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="f2e31-451">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="f2e31-452">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-452">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="f2e31-453">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-453">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="f2e31-454">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f2e31-454">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                var path = Path.Combine(
                    Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f2e31-455">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-455">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f2e31-456">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-456">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-457">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-457">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
var config = new ConfigurationBuilder()
    .AddKeyPerFile(directoryPath: path, optional: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="f2e31-458">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="f2e31-458">Memory Configuration Provider</span></span>

<span data-ttu-id="f2e31-459">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-459">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="f2e31-460">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-460">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f2e31-461">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-461">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="f2e31-462">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f2e31-462">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _dict = 
        new Dictionary<string, string>
        {
            {"MemoryCollectionKey1", "value1"},
            {"MemoryCollectionKey2", "value2"}
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddInMemoryCollection(_dict);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f2e31-463">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-463">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

var config = new ConfigurationBuilder()
    .AddInMemoryCollection(dict)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="getvalue"></a><span data-ttu-id="f2e31-464">GetValue</span><span class="sxs-lookup"><span data-stu-id="f2e31-464">GetValue</span></span>

<span data-ttu-id="f2e31-465">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um valor de configuração com uma chave especificada e o converte para o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-465">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="f2e31-466">Uma sobrecarga permite que você forneça um valor padrão se a chave não for encontrada.</span><span class="sxs-lookup"><span data-stu-id="f2e31-466">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="f2e31-467">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="f2e31-467">The following example:</span></span>

* <span data-ttu-id="f2e31-468">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-468">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="f2e31-469">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-469">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="f2e31-470">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-470">Types the value as an `int`.</span></span>
* <span data-ttu-id="f2e31-471">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="f2e31-471">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="f2e31-472">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="f2e31-472">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="f2e31-473">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="f2e31-473">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="f2e31-474">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="f2e31-474">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="f2e31-475">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-475">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="f2e31-476">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-476">section0:key0</span></span>
* <span data-ttu-id="f2e31-477">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-477">section0:key1</span></span>
* <span data-ttu-id="f2e31-478">section1:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-478">section1:key0</span></span>
* <span data-ttu-id="f2e31-479">section1:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-479">section1:key1</span></span>
* <span data-ttu-id="f2e31-480">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-480">section2:subsection0:key0</span></span>
* <span data-ttu-id="f2e31-481">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-481">section2:subsection0:key1</span></span>
* <span data-ttu-id="f2e31-482">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="f2e31-482">section2:subsection1:key0</span></span>
* <span data-ttu-id="f2e31-483">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="f2e31-483">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="f2e31-484">GetSection</span><span class="sxs-lookup"><span data-stu-id="f2e31-484">GetSection</span></span>

<span data-ttu-id="f2e31-485">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="f2e31-485">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span> <span data-ttu-id="f2e31-486">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-486">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-487">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="f2e31-487">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="f2e31-488">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="f2e31-488">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="f2e31-489">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="f2e31-489">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="f2e31-490">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-490">`GetSection` never returns `null`.</span></span> <span data-ttu-id="f2e31-491">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-491">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="f2e31-492">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="f2e31-492">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="f2e31-493"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="f2e31-493">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="f2e31-494">GetChildren</span><span class="sxs-lookup"><span data-stu-id="f2e31-494">GetChildren</span></span>

<span data-ttu-id="f2e31-495">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="f2e31-495">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="f2e31-496">Exists</span><span class="sxs-lookup"><span data-stu-id="f2e31-496">Exists</span></span>

<span data-ttu-id="f2e31-497">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-497">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="f2e31-498">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-498">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="f2e31-499">Associar a uma classe</span><span class="sxs-lookup"><span data-stu-id="f2e31-499">Bind to a class</span></span>

<span data-ttu-id="f2e31-500">A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*.</span><span class="sxs-lookup"><span data-stu-id="f2e31-500">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="f2e31-501">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="f2e31-502">Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="f2e31-502">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="f2e31-503">`Bind` está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-503">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-504">O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="f2e31-504">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="f2e31-505">A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-505">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="f2e31-506">Os seguintes pares chave-valor de configuração são criados:</span><span class="sxs-lookup"><span data-stu-id="f2e31-506">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="f2e31-507">Chave</span><span class="sxs-lookup"><span data-stu-id="f2e31-507">Key</span></span>                   | <span data-ttu-id="f2e31-508">Valor</span><span class="sxs-lookup"><span data-stu-id="f2e31-508">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="f2e31-509">starship:name</span><span class="sxs-lookup"><span data-stu-id="f2e31-509">starship:name</span></span>         | <span data-ttu-id="f2e31-510">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="f2e31-510">USS Enterprise</span></span>                                    |
| <span data-ttu-id="f2e31-511">starship:registry</span><span class="sxs-lookup"><span data-stu-id="f2e31-511">starship:registry</span></span>     | <span data-ttu-id="f2e31-512">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="f2e31-512">NCC-1701</span></span>                                          |
| <span data-ttu-id="f2e31-513">starship:class</span><span class="sxs-lookup"><span data-stu-id="f2e31-513">starship:class</span></span>        | <span data-ttu-id="f2e31-514">Constituição</span><span class="sxs-lookup"><span data-stu-id="f2e31-514">Constitution</span></span>                                      |
| <span data-ttu-id="f2e31-515">starship:length</span><span class="sxs-lookup"><span data-stu-id="f2e31-515">starship:length</span></span>       | <span data-ttu-id="f2e31-516">304,8</span><span class="sxs-lookup"><span data-stu-id="f2e31-516">304.8</span></span>                                             |
| <span data-ttu-id="f2e31-517">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="f2e31-517">starship:commissioned</span></span> | <span data-ttu-id="f2e31-518">False</span><span class="sxs-lookup"><span data-stu-id="f2e31-518">False</span></span>                                             |
| <span data-ttu-id="f2e31-519">marca</span><span class="sxs-lookup"><span data-stu-id="f2e31-519">trademark</span></span>             | <span data-ttu-id="f2e31-520">Paramount Pictures Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="f2e31-520">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="f2e31-521">O aplicativo de exemplo chama `GetSection` com a chave `starship`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-521">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="f2e31-522">Os pares chave-valor `starship` são isolados.</span><span class="sxs-lookup"><span data-stu-id="f2e31-522">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="f2e31-523">O método `Bind` é chamado na subseção passando uma instância da classe `Starship`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-523">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="f2e31-524">Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="f2e31-524">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

<span data-ttu-id="f2e31-525">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-525">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="f2e31-526">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="f2e31-526">Bind to an object graph</span></span>

<span data-ttu-id="f2e31-527"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="f2e31-527"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="f2e31-528">`Bind` está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-528">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-529">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="f2e31-529">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="f2e31-530">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-530">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="f2e31-531">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-531">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="f2e31-532">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="f2e31-532">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="f2e31-533">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-533">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="f2e31-534">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-534">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="f2e31-535">O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:</span><span class="sxs-lookup"><span data-stu-id="f2e31-535">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

<span data-ttu-id="f2e31-536"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-536"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f2e31-537">`Get<T>` está disponível no ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="f2e31-537">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="f2e31-538">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-538">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="f2e31-539">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="f2e31-539">Bind an array to a class</span></span>

<span data-ttu-id="f2e31-540">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="f2e31-540">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="f2e31-541">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-541">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="f2e31-542">Qualquer formato de matriz que exponha um segmento de chave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar matrizes a uma matriz de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="f2e31-542">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span> <span data-ttu-id="f2e31-543">\`Bind\`\` está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-543">\`Bind\`\` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

> [!NOTE]
> <span data-ttu-id="f2e31-544">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="f2e31-544">Binding is provided by convention.</span></span> <span data-ttu-id="f2e31-545">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="f2e31-545">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="f2e31-546">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="f2e31-546">**In-memory array processing**</span></span>

<span data-ttu-id="f2e31-547">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f2e31-547">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="f2e31-548">Chave</span><span class="sxs-lookup"><span data-stu-id="f2e31-548">Key</span></span>             | <span data-ttu-id="f2e31-549">Valor</span><span class="sxs-lookup"><span data-stu-id="f2e31-549">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="f2e31-550">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="f2e31-550">array:entries:0</span></span> | <span data-ttu-id="f2e31-551">value0</span><span class="sxs-lookup"><span data-stu-id="f2e31-551">value0</span></span> |
| <span data-ttu-id="f2e31-552">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="f2e31-552">array:entries:1</span></span> | <span data-ttu-id="f2e31-553">value1</span><span class="sxs-lookup"><span data-stu-id="f2e31-553">value1</span></span> |
| <span data-ttu-id="f2e31-554">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="f2e31-554">array:entries:2</span></span> | <span data-ttu-id="f2e31-555">value2</span><span class="sxs-lookup"><span data-stu-id="f2e31-555">value2</span></span> |
| <span data-ttu-id="f2e31-556">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="f2e31-556">array:entries:4</span></span> | <span data-ttu-id="f2e31-557">value4</span><span class="sxs-lookup"><span data-stu-id="f2e31-557">value4</span></span> |
| <span data-ttu-id="f2e31-558">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="f2e31-558">array:entries:5</span></span> | <span data-ttu-id="f2e31-559">value5</span><span class="sxs-lookup"><span data-stu-id="f2e31-559">value5</span></span> |

<span data-ttu-id="f2e31-560">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="f2e31-560">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

<span data-ttu-id="f2e31-561">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="f2e31-561">The array skips a value for index &num;3.</span></span> <span data-ttu-id="f2e31-562">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="f2e31-562">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="f2e31-563">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="f2e31-563">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="f2e31-564">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="f2e31-564">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="f2e31-565">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2e31-565">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2e31-566">A sintaxe [ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="f2e31-566">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="f2e31-567">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-567">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="f2e31-568">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="f2e31-568">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="f2e31-569">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="f2e31-569">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="f2e31-570">0</span><span class="sxs-lookup"><span data-stu-id="f2e31-570">0</span></span>                            | <span data-ttu-id="f2e31-571">value0</span><span class="sxs-lookup"><span data-stu-id="f2e31-571">value0</span></span>                       |
| <span data-ttu-id="f2e31-572">1</span><span class="sxs-lookup"><span data-stu-id="f2e31-572">1</span></span>                            | <span data-ttu-id="f2e31-573">value1</span><span class="sxs-lookup"><span data-stu-id="f2e31-573">value1</span></span>                       |
| <span data-ttu-id="f2e31-574">2</span><span class="sxs-lookup"><span data-stu-id="f2e31-574">2</span></span>                            | <span data-ttu-id="f2e31-575">value2</span><span class="sxs-lookup"><span data-stu-id="f2e31-575">value2</span></span>                       |
| <span data-ttu-id="f2e31-576">3</span><span class="sxs-lookup"><span data-stu-id="f2e31-576">3</span></span>                            | <span data-ttu-id="f2e31-577">value4</span><span class="sxs-lookup"><span data-stu-id="f2e31-577">value4</span></span>                       |
| <span data-ttu-id="f2e31-578">4</span><span class="sxs-lookup"><span data-stu-id="f2e31-578">4</span></span>                            | <span data-ttu-id="f2e31-579">value5</span><span class="sxs-lookup"><span data-stu-id="f2e31-579">value5</span></span>                       |

<span data-ttu-id="f2e31-580">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-580">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="f2e31-581">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="f2e31-581">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="f2e31-582">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="f2e31-582">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="f2e31-583">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-583">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="f2e31-584">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="f2e31-584">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="f2e31-585">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="f2e31-585">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="f2e31-586">No <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="f2e31-586">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="f2e31-587">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-587">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="f2e31-588">Chave</span><span class="sxs-lookup"><span data-stu-id="f2e31-588">Key</span></span>             | <span data-ttu-id="f2e31-589">Valor</span><span class="sxs-lookup"><span data-stu-id="f2e31-589">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="f2e31-590">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="f2e31-590">array:entries:3</span></span> | <span data-ttu-id="f2e31-591">value3</span><span class="sxs-lookup"><span data-stu-id="f2e31-591">value3</span></span> |

<span data-ttu-id="f2e31-592">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="f2e31-592">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="f2e31-593">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="f2e31-593">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="f2e31-594">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="f2e31-594">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="f2e31-595">0</span><span class="sxs-lookup"><span data-stu-id="f2e31-595">0</span></span>                            | <span data-ttu-id="f2e31-596">value0</span><span class="sxs-lookup"><span data-stu-id="f2e31-596">value0</span></span>                       |
| <span data-ttu-id="f2e31-597">1</span><span class="sxs-lookup"><span data-stu-id="f2e31-597">1</span></span>                            | <span data-ttu-id="f2e31-598">value1</span><span class="sxs-lookup"><span data-stu-id="f2e31-598">value1</span></span>                       |
| <span data-ttu-id="f2e31-599">2</span><span class="sxs-lookup"><span data-stu-id="f2e31-599">2</span></span>                            | <span data-ttu-id="f2e31-600">value2</span><span class="sxs-lookup"><span data-stu-id="f2e31-600">value2</span></span>                       |
| <span data-ttu-id="f2e31-601">3</span><span class="sxs-lookup"><span data-stu-id="f2e31-601">3</span></span>                            | <span data-ttu-id="f2e31-602">value3</span><span class="sxs-lookup"><span data-stu-id="f2e31-602">value3</span></span>                       |
| <span data-ttu-id="f2e31-603">4</span><span class="sxs-lookup"><span data-stu-id="f2e31-603">4</span></span>                            | <span data-ttu-id="f2e31-604">value4</span><span class="sxs-lookup"><span data-stu-id="f2e31-604">value4</span></span>                       |
| <span data-ttu-id="f2e31-605">5</span><span class="sxs-lookup"><span data-stu-id="f2e31-605">5</span></span>                            | <span data-ttu-id="f2e31-606">value5</span><span class="sxs-lookup"><span data-stu-id="f2e31-606">value5</span></span>                       |

<span data-ttu-id="f2e31-607">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="f2e31-607">**JSON array processing**</span></span>

<span data-ttu-id="f2e31-608">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="f2e31-608">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="f2e31-609">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="f2e31-609">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="f2e31-610">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="f2e31-610">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="f2e31-611">Chave</span><span class="sxs-lookup"><span data-stu-id="f2e31-611">Key</span></span>                     | <span data-ttu-id="f2e31-612">Valor</span><span class="sxs-lookup"><span data-stu-id="f2e31-612">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="f2e31-613">json_array:key</span><span class="sxs-lookup"><span data-stu-id="f2e31-613">json_array:key</span></span>          | <span data-ttu-id="f2e31-614">valueA</span><span class="sxs-lookup"><span data-stu-id="f2e31-614">valueA</span></span> |
| <span data-ttu-id="f2e31-615">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="f2e31-615">json_array:subsection:0</span></span> | <span data-ttu-id="f2e31-616">valueB</span><span class="sxs-lookup"><span data-stu-id="f2e31-616">valueB</span></span> |
| <span data-ttu-id="f2e31-617">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="f2e31-617">json_array:subsection:1</span></span> | <span data-ttu-id="f2e31-618">valueC</span><span class="sxs-lookup"><span data-stu-id="f2e31-618">valueC</span></span> |
| <span data-ttu-id="f2e31-619">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="f2e31-619">json_array:subsection:2</span></span> | <span data-ttu-id="f2e31-620">valueD</span><span class="sxs-lookup"><span data-stu-id="f2e31-620">valueD</span></span> |

<span data-ttu-id="f2e31-621">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="f2e31-621">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="f2e31-622">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-622">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="f2e31-623">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-623">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="f2e31-624">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="f2e31-624">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="f2e31-625">Valor `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="f2e31-625">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="f2e31-626">0</span><span class="sxs-lookup"><span data-stu-id="f2e31-626">0</span></span>                                   | <span data-ttu-id="f2e31-627">valueB</span><span class="sxs-lookup"><span data-stu-id="f2e31-627">valueB</span></span>                              |
| <span data-ttu-id="f2e31-628">1</span><span class="sxs-lookup"><span data-stu-id="f2e31-628">1</span></span>                                   | <span data-ttu-id="f2e31-629">valueC</span><span class="sxs-lookup"><span data-stu-id="f2e31-629">valueC</span></span>                              |
| <span data-ttu-id="f2e31-630">2</span><span class="sxs-lookup"><span data-stu-id="f2e31-630">2</span></span>                                   | <span data-ttu-id="f2e31-631">valueD</span><span class="sxs-lookup"><span data-stu-id="f2e31-631">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="f2e31-632">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="f2e31-632">Custom configuration provider</span></span>

<span data-ttu-id="f2e31-633">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="f2e31-633">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="f2e31-634">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="f2e31-634">The provider has the following characteristics:</span></span>

* <span data-ttu-id="f2e31-635">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-635">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="f2e31-636">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="f2e31-636">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="f2e31-637">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="f2e31-637">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="f2e31-638">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="f2e31-638">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="f2e31-639">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-639">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="f2e31-640">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f2e31-640">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="f2e31-641">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="f2e31-641">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="f2e31-642">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="f2e31-642">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="f2e31-643">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="f2e31-643">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="f2e31-644">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-644">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="f2e31-645">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="f2e31-645">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="f2e31-646">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-646">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="f2e31-647">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="f2e31-647">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="f2e31-648">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="f2e31-648">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="f2e31-649">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-649">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="f2e31-650">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="f2e31-650">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="f2e31-651">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f2e31-651">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="f2e31-652">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="f2e31-652">Access configuration during startup</span></span>

<span data-ttu-id="f2e31-653">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f2e31-653">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f2e31-654">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="f2e31-654">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="f2e31-655">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, confira [Inicialização do aplicativo: Métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="f2e31-655">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="f2e31-656">Acessar a configuração em uma página do Razor Pages ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="f2e31-656">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="f2e31-657">Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.</span><span class="sxs-lookup"><span data-stu-id="f2e31-657">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="f2e31-658">Em uma página do Razor:</span><span class="sxs-lookup"><span data-stu-id="f2e31-658">In a Razor Pages page:</span></span>

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

<span data-ttu-id="f2e31-659">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="f2e31-659">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="f2e31-660">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="f2e31-660">Add configuration from an external assembly</span></span>

<span data-ttu-id="f2e31-661">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2e31-661">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="f2e31-662">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="f2e31-662">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2e31-663">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f2e31-663">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* [<span data-ttu-id="f2e31-664">Detalhes da configuração da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f2e31-664">Deep Dive into Microsoft Configuration</span></span>](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)
