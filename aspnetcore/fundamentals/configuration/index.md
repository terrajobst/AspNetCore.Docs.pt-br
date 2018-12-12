---
title: Configuração no ASP.NET Core
author: guardrex
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2018
uid: fundamentals/configuration/index
ms.openlocfilehash: 6f0378ffc4f9a1efa95c8f70d70e7799abef130b
ms.sourcegitcommit: 1872d2e6f299093c78a6795a486929ffb0bbffff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53216892"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="b0b36-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0b36-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="b0b36-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b0b36-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b0b36-105">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="b0b36-106">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="b0b36-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0b36-107">Azure Key Vault</span></span>
* <span data-ttu-id="b0b36-108">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-108">Command-line arguments</span></span>
* <span data-ttu-id="b0b36-109">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="b0b36-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b0b36-110">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="b0b36-110">Directory files</span></span>
* <span data-ttu-id="b0b36-111">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-111">Environment variables</span></span>
* <span data-ttu-id="b0b36-112">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-112">In-memory .NET objects</span></span>
* <span data-ttu-id="b0b36-113">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="b0b36-113">Settings files</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

* <span data-ttu-id="b0b36-114">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0b36-114">Azure Key Vault</span></span>
* <span data-ttu-id="b0b36-115">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-115">Command-line arguments</span></span>
* <span data-ttu-id="b0b36-116">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="b0b36-116">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b0b36-117">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-117">Environment variables</span></span>
* <span data-ttu-id="b0b36-118">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-118">In-memory .NET objects</span></span>
* <span data-ttu-id="b0b36-119">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="b0b36-119">Settings files</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.0"

* <span data-ttu-id="b0b36-120">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-120">Command-line arguments</span></span>
* <span data-ttu-id="b0b36-121">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="b0b36-121">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b0b36-122">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-122">Environment variables</span></span>
* <span data-ttu-id="b0b36-123">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-123">In-memory .NET objects</span></span>
* <span data-ttu-id="b0b36-124">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="b0b36-124">Settings files</span></span>

::: moniker-end

<span data-ttu-id="b0b36-125">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="b0b36-125">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="b0b36-126">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0b36-126">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="b0b36-127">Para saber mais sobre como usar o padrão de opções, confira <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-127">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b0b36-128">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0b36-128">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b0b36-129">Os exemplos apresentados neste tópico dependem do seguinte:</span><span class="sxs-lookup"><span data-stu-id="b0b36-129">The examples provided in this topic rely upon:</span></span>

* <span data-ttu-id="b0b36-130">Definição do caminho de base do aplicativo com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-130">Setting the base path of the app with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0b36-131">`SetBasePath` é disponibilizado para um aplicativo fazendo referência ao pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/).</span><span class="sxs-lookup"><span data-stu-id="b0b36-131">`SetBasePath` is made available to an app by referencing the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package.</span></span>
* <span data-ttu-id="b0b36-132">Resolução de seções dos arquivos de configuração com <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-132">Resolving sections of configuration files with <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>.</span></span> <span data-ttu-id="b0b36-133">`GetSection` é disponibilizado a um aplicativo fazendo referência ao pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/).</span><span class="sxs-lookup"><span data-stu-id="b0b36-133">`GetSection` is made available to an app by referencing the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package.</span></span>
* <span data-ttu-id="b0b36-134">Configuração de associação a classes .NET com <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> e [Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*).</span><span class="sxs-lookup"><span data-stu-id="b0b36-134">Binding configuration to .NET classes with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> and [Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*).</span></span> <span data-ttu-id="b0b36-135">`Bind` e `Get<T>` são disponibilizados a um aplicativo fazendo referência ao pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/).</span><span class="sxs-lookup"><span data-stu-id="b0b36-135">`Bind` and `Get<T>` are made available to an app by referencing the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package.</span></span> <span data-ttu-id="b0b36-136">`Get<T>` está disponível no ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="b0b36-136">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0b36-137">Esses três pacotes estão incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b0b36-137">These three packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0b36-138">Esses três pacotes estão incluídos no [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="b0b36-138">These three packages are included in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

## <a name="host-vs-app-configuration"></a><span data-ttu-id="b0b36-139">Configuração do host versus aplicativo</span><span class="sxs-lookup"><span data-stu-id="b0b36-139">Host vs. app configuration</span></span>

<span data-ttu-id="b0b36-140">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-140">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b0b36-141">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-141">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b0b36-142">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="b0b36-142">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b0b36-143">Os pares chave-valor de configuração do host se tornam parte da configuração global do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-143">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="b0b36-144">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/host/index>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-144">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/host/index>.</span></span>

## <a name="security"></a><span data-ttu-id="b0b36-145">Segurança</span><span class="sxs-lookup"><span data-stu-id="b0b36-145">Security</span></span>

<span data-ttu-id="b0b36-146">Adote as melhores práticas a seguir:</span><span class="sxs-lookup"><span data-stu-id="b0b36-146">Adopt the following best practices:</span></span>

* <span data-ttu-id="b0b36-147">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="b0b36-147">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="b0b36-148">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b0b36-148">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b0b36-149">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="b0b36-149">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b0b36-150">Saiba mais sobre [como usar vários ambientes](xref:fundamentals/environments) e gerenciar o [armazenamento seguro de segredos de aplicativo em desenvolvimento com o Gerenciador de Segredo](xref:security/app-secrets) (inclui recomendações sobre como usar variáveis de ambiente para armazenar dados confidenciais).</span><span class="sxs-lookup"><span data-stu-id="b0b36-150">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="b0b36-151">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="b0b36-151">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="b0b36-152">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="b0b36-152">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b0b36-153">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) é uma opção para o armazenamento seguro de segredos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-153">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="b0b36-154">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-154">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b0b36-155">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="b0b36-155">Hierarchical configuration data</span></span>

<span data-ttu-id="b0b36-156">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-156">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b0b36-157">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="b0b36-157">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="b0b36-158">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-158">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="b0b36-159">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="b0b36-159">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="b0b36-160">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-160">section0:key0</span></span>
* <span data-ttu-id="b0b36-161">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-161">section0:key1</span></span>
* <span data-ttu-id="b0b36-162">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-162">section1:key0</span></span>
* <span data-ttu-id="b0b36-163">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-163">section1:key1</span></span>

<span data-ttu-id="b0b36-164">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-164"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b0b36-165">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="b0b36-165">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="b0b36-166">Convenções</span><span class="sxs-lookup"><span data-stu-id="b0b36-166">Conventions</span></span>

<span data-ttu-id="b0b36-167">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-167">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="b0b36-168">Os Provedores de Configuração de Arquivo têm a capacidade de recarregar a configuração quando um arquivo de configurações subjacente é alterado após a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-168">File Configuration Providers have the ability to reload configuration when an underlying settings file is changed after app startup.</span></span> <span data-ttu-id="b0b36-169">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="b0b36-169">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b0b36-170"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-170"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [Dependency Injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b0b36-171">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="b0b36-171">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="b0b36-172">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="b0b36-172">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="b0b36-173">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b0b36-173">Keys are case-insensitive.</span></span> <span data-ttu-id="b0b36-174">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="b0b36-174">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b0b36-175">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-175">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="b0b36-176">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="b0b36-176">Hierarchical keys</span></span>
  * <span data-ttu-id="b0b36-177">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="b0b36-177">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b0b36-178">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="b0b36-178">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b0b36-179">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="b0b36-179">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="b0b36-180">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="b0b36-180">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b0b36-181">Você deve fornecer o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-181">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b0b36-182">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-182">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b0b36-183">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="b0b36-183">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="b0b36-184">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="b0b36-184">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="b0b36-185">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="b0b36-185">Values are strings.</span></span>
* <span data-ttu-id="b0b36-186">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="b0b36-186">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="b0b36-187">Provedores</span><span class="sxs-lookup"><span data-stu-id="b0b36-187">Providers</span></span>

<span data-ttu-id="b0b36-188">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0b36-188">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

::: moniker range=">= aspnetcore-2.1"

| <span data-ttu-id="b0b36-189">Provider</span><span class="sxs-lookup"><span data-stu-id="b0b36-189">Provider</span></span> | <span data-ttu-id="b0b36-190">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="b0b36-190">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b0b36-191">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="b0b36-191">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="b0b36-192">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0b36-192">Azure Key Vault</span></span> |
| [<span data-ttu-id="b0b36-193">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-193">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b0b36-194">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-194">Command-line parameters</span></span> |
| [<span data-ttu-id="b0b36-195">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="b0b36-195">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b0b36-196">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="b0b36-196">Custom source</span></span> |
| [<span data-ttu-id="b0b36-197">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-197">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b0b36-198">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-198">Environment variables</span></span> |
| [<span data-ttu-id="b0b36-199">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="b0b36-199">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b0b36-200">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b0b36-200">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b0b36-201">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="b0b36-201">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b0b36-202">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="b0b36-202">Directory files</span></span> |
| [<span data-ttu-id="b0b36-203">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-203">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b0b36-204">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-204">In-memory collections</span></span> |
| <span data-ttu-id="b0b36-205">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="b0b36-205">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b0b36-206">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="b0b36-206">File in the user profile directory</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

| <span data-ttu-id="b0b36-207">Provider</span><span class="sxs-lookup"><span data-stu-id="b0b36-207">Provider</span></span> | <span data-ttu-id="b0b36-208">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="b0b36-208">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b0b36-209">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="b0b36-209">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="b0b36-210">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0b36-210">Azure Key Vault</span></span> |
| [<span data-ttu-id="b0b36-211">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-211">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b0b36-212">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-212">Command-line parameters</span></span> |
| [<span data-ttu-id="b0b36-213">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="b0b36-213">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b0b36-214">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="b0b36-214">Custom source</span></span> |
| [<span data-ttu-id="b0b36-215">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-215">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b0b36-216">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-216">Environment variables</span></span> |
| [<span data-ttu-id="b0b36-217">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="b0b36-217">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b0b36-218">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b0b36-218">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b0b36-219">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-219">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b0b36-220">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-220">In-memory collections</span></span> |
| <span data-ttu-id="b0b36-221">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="b0b36-221">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b0b36-222">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="b0b36-222">File in the user profile directory</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-1.0"

| <span data-ttu-id="b0b36-223">Provider</span><span class="sxs-lookup"><span data-stu-id="b0b36-223">Provider</span></span> | <span data-ttu-id="b0b36-224">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="b0b36-224">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="b0b36-225">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-225">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b0b36-226">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-226">Command-line parameters</span></span> |
| [<span data-ttu-id="b0b36-227">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="b0b36-227">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b0b36-228">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="b0b36-228">Custom source</span></span> |
| [<span data-ttu-id="b0b36-229">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-229">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b0b36-230">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-230">Environment variables</span></span> |
| [<span data-ttu-id="b0b36-231">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="b0b36-231">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b0b36-232">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b0b36-232">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b0b36-233">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-233">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b0b36-234">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-234">In-memory collections</span></span> |
| <span data-ttu-id="b0b36-235">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="b0b36-235">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b0b36-236">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="b0b36-236">File in the user profile directory</span></span> |

::: moniker-end

<span data-ttu-id="b0b36-237">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-237">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="b0b36-238">Os provedores de configuração descritos neste tópico estão descritos em ordem alfabética, não na ordem na qual seu código pode organizá-los.</span><span class="sxs-lookup"><span data-stu-id="b0b36-238">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="b0b36-239">Organize os provedores de configuração em seu código para atender às suas prioridades para as fontes de configuração subjacentes.</span><span class="sxs-lookup"><span data-stu-id="b0b36-239">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="b0b36-240">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="b0b36-240">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="b0b36-241">Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="b0b36-241">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="b0b36-242">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0b36-242">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="b0b36-243">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="b0b36-243">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="b0b36-244">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-244">Environment variables</span></span>
1. <span data-ttu-id="b0b36-245">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-245">Command-line arguments</span></span>

<span data-ttu-id="b0b36-246">É comum posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="b0b36-246">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-247">Essa sequência de provedores é aplicada quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-247">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b0b36-248">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b0b36-248">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-249">Essa sequência de provedores pode ser criada para o aplicativo (não o host) com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> e uma chamada para seu método <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder.Build*> em `Startup`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-249">This sequence of providers can be created for the app (not the host) with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> and a call to its <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder.Build*> method in `Startup`:</span></span>

```csharp
public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
            reloadOnChange: true);

    var appAssembly = Assembly.Load(new AssemblyName(env.ApplicationName));

    if (appAssembly != null)
    {
        builder.AddUserSecrets(appAssembly, optional: true);
    }

    builder.AddEnvironmentVariables();

    Configuration = builder.Build();
}

public IConfiguration Configuration { get; }

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IConfiguration>(Configuration);
}
```

<span data-ttu-id="b0b36-250">No exemplo anterior, o nome do ambiente (`env.EnvironmentName`) e o nome do assembly de aplicativo (`env.ApplicationName`) são fornecidos pelo <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-250">In the preceding example, the environment name (`env.EnvironmentName`) and app assembly name (`env.ApplicationName`) are provided by the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>.</span></span> <span data-ttu-id="b0b36-251">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-251">For more information, see <xref:fundamentals/environments>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="configureappconfiguration"></a><span data-ttu-id="b0b36-252">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="b0b36-252">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b0b36-253">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o Host da Web para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="b0b36-253">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the Web Host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=19)]

::: moniker-end

## <a name="command-line-configuration-provider"></a><span data-ttu-id="b0b36-254">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="b0b36-254">Command-line Configuration Provider</span></span>

<span data-ttu-id="b0b36-255">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b0b36-255">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="b0b36-256">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-256">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-257">`AddCommandLine` é chamado automaticamente quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-257">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b0b36-258">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b0b36-258">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b0b36-259">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="b0b36-259">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b0b36-260">Configuração opcional de *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-260">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="b0b36-261">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="b0b36-261">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b0b36-262">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="b0b36-262">Environment variables.</span></span>

<span data-ttu-id="b0b36-263">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="b0b36-263">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="b0b36-264">Os argumentos de linha de comando passados em tempo de execução substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="b0b36-264">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="b0b36-265">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="b0b36-265">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="b0b36-266">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-266">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0b36-267">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-267">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b0b36-268">`AddCommandLine` já foi chamado por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-268">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b0b36-269">Se precisar oferecer configuração de aplicativo e ainda poder substituir essa configuração por argumentos de linha de comando, chame os provedores adicionais do aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> e chame `AddCommandLine` por fim.</span><span class="sxs-lookup"><span data-stu-id="b0b36-269">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

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

<span data-ttu-id="b0b36-270">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-270">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0b36-271">Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-271">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method.</span></span>

<span data-ttu-id="b0b36-272">`AddCommandLine` já foi chamado por `CreateDefaultBuilder` quando `UseConfiguration` é chamado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-272">`AddCommandLine` has already been called by `CreateDefaultBuilder` when `UseConfiguration` is called.</span></span> <span data-ttu-id="b0b36-273">Se precisar oferecer configuração de aplicativo e ainda poder substituir essa configuração por argumentos de linha de comando, chame os provedores adicionais do aplicativo em um `ConfigurationBuilder` e chame `AddCommandLine` por fim.</span><span class="sxs-lookup"><span data-stu-id="b0b36-273">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers on a `ConfigurationBuilder` and call `AddCommandLine` last.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            // Call other providers here and call AddCommandLine last.
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0b36-274">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-274">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-275">Para ativar a configuração de linha de comando, chame o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-275">To activate command-line configuration, call the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0b36-276">Chamar o provedor por último permite que os argumentos de linha de comando passados em tempo de execução substituam a configuração definida por outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-276">Call the provider last to allow the command-line arguments passed at runtime to override configuration set by other configuration providers.</span></span>

<span data-ttu-id="b0b36-277">Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b0b36-277">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

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

<span data-ttu-id="b0b36-278">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="b0b36-278">**Example**</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-279">O aplicativo de exemplo 2.x aproveita a vantagem do método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-279">The 2.x sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-280">O aplicativo de exemplo 1.x chama <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> em um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-280">The 1.x sample app calls <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> on a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

::: moniker-end

1. <span data-ttu-id="b0b36-281">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="b0b36-281">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="b0b36-282">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-282">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="b0b36-283">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-283">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b0b36-284">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-284">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="b0b36-285">Arguments</span><span class="sxs-lookup"><span data-stu-id="b0b36-285">Arguments</span></span>

<span data-ttu-id="b0b36-286">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="b0b36-286">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="b0b36-287">O valor pode ser nulo se um sinal de igual for usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="b0b36-287">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="b0b36-288">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="b0b36-288">Key prefix</span></span>               | <span data-ttu-id="b0b36-289">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b0b36-289">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="b0b36-290">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="b0b36-290">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="b0b36-291">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="b0b36-291">Two dashes (`--`)</span></span>        | <span data-ttu-id="b0b36-292">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="b0b36-292">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="b0b36-293">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="b0b36-293">Forward slash (`/`)</span></span>      | <span data-ttu-id="b0b36-294">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="b0b36-294">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="b0b36-295">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="b0b36-295">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="b0b36-296">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="b0b36-296">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="b0b36-297">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="b0b36-297">Switch mappings</span></span>

<span data-ttu-id="b0b36-298">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="b0b36-298">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="b0b36-299">Quando você cria manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, pode fornecer um dicionário de substituições de opções para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-299">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b0b36-300">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="b0b36-300">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b0b36-301">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-301">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b0b36-302">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="b0b36-302">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b0b36-303">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="b0b36-303">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b0b36-304">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="b0b36-304">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="b0b36-305">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="b0b36-305">The switch mappings dictionary must not contain duplicate keys.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0b36-306">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b0b36-306">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="b0b36-307">Conforme mostrado no exemplo anterior, a chamada para `CreateDefaultBuilder` não deve passar argumentos ao usar mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="b0b36-307">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="b0b36-308">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-308">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b0b36-309">Se os argumentos incluírem uma opção mapeada e forem passados para `CreateDefaultBuilder`, o provedor `AddCommandLine` não inicializará com um <xref:System.FormatException>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-309">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="b0b36-310">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="b0b36-310">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var switchMappings = new Dictionary<string, string>
            {
                { "-CLKey1", "CommandLineKey1" },
                { "-CLKey2", "CommandLineKey2" }
            };

        var config = new ConfigurationBuilder()
            .AddCommandLine(args, switchMappings)
            .Build();

        // Do not pass the args to CreateDefaultBuilder
        return WebHost.CreateDefaultBuilder()
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0b36-311">Conforme mostrado no exemplo anterior, a chamada para `CreateDefaultBuilder` não deve passar argumentos ao usar mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="b0b36-311">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="b0b36-312">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-312">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b0b36-313">Se os argumentos incluírem uma opção mapeada e forem passados para `CreateDefaultBuilder`, o provedor `AddCommandLine` não inicializará com um <xref:System.FormatException>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-313">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="b0b36-314">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="b0b36-314">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
public static void Main(string[] args)
{
    var switchMappings = new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    var config = new ConfigurationBuilder()
        .AddCommandLine(args, switchMappings)
        .Build();

    var host = new WebHostBuilder()
        .UseConfiguration(config)
        .UseKestrel()
        .UseStartup<Startup>()
        .Start();

    using (host)
    {
        Console.ReadLine();
    }
}
```

::: moniker-end

<span data-ttu-id="b0b36-315">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="b0b36-315">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="b0b36-316">Chave</span><span class="sxs-lookup"><span data-stu-id="b0b36-316">Key</span></span>       | <span data-ttu-id="b0b36-317">Valor</span><span class="sxs-lookup"><span data-stu-id="b0b36-317">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="b0b36-318">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="b0b36-318">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="b0b36-319">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="b0b36-319">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="b0b36-320">Chave</span><span class="sxs-lookup"><span data-stu-id="b0b36-320">Key</span></span>               | <span data-ttu-id="b0b36-321">Valor</span><span class="sxs-lookup"><span data-stu-id="b0b36-321">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="b0b36-322">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-322">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="b0b36-323">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b0b36-323">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="b0b36-324">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-324">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0b36-325">Ao trabalhar com chaves hierárquicas nas variáveis de ambiente, talvez um separador de dois-pontos (`:`) não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="b0b36-325">When working with hierarchical keys in environment variables, a colon separator (`:`) may not work on all platforms.</span></span> <span data-ttu-id="b0b36-326">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é substituído por dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="b0b36-326">A double underscore (`__`) is supported by all platforms and is replaced by a colon.</span></span>

<span data-ttu-id="b0b36-327">O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) permite que você defina variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o Provedor de Configuração de Variáveis de Ambiente.</span><span class="sxs-lookup"><span data-stu-id="b0b36-327">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="b0b36-328">Para obter mais informações, confira [Aplicativos Azure: Substituir a configuração do aplicativo no Portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="b0b36-328">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-329">`AddEnvironmentVariables` é chamado automaticamente para as variáveis de ambiente prefixadas com `ASPNETCORE_` quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-329">`AddEnvironmentVariables` is automatically called for environment variables prefixed with `ASPNETCORE_` when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="b0b36-330">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b0b36-330">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b0b36-331">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="b0b36-331">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b0b36-332">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-332">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="b0b36-333">Configuração opcional de *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-333">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="b0b36-334">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="b0b36-334">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b0b36-335">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="b0b36-335">Command-line arguments.</span></span>

<span data-ttu-id="b0b36-336">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-336">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="b0b36-337">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em tempo de execução para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-337">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0b36-338">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-338">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b0b36-339">Se precisar fornecer configuração do aplicativo com base em variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>e chame `AddEnvironmentVariables` com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-339">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

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
                // Call AddEnvironmentVariables last if you need to allow environment
                // variables to override values from other providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0b36-340">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-340">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0b36-341">Chame o método de extensão `AddEnvironmentVariables` em uma instância de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-341">Call the `AddEnvironmentVariables` extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b0b36-342">Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-342">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method.</span></span>

<span data-ttu-id="b0b36-343">Se precisar fornecer configuração do aplicativo com base em variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>e chame `AddEnvironmentVariables` com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-343">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            // Call additional providers here as needed.
            // Call AddEnvironmentVariables last if you need to allow environment
            // variables to override values from other providers.
            .AddEnvironmentVariables(prefix: "PREFIX_")
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0b36-344">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-344">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-345">Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b0b36-345">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b0b36-346">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="b0b36-346">**Example**</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-347">O aplicativo de exemplo 2.x aproveita a vantagem do método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-347">The 2.x sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-348">O aplicativo de exemplo 1.x chama `AddEnvironmentVariables` em um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-348">The 1.x sample app calls `AddEnvironmentVariables` on a `ConfigurationBuilder`.</span></span>

::: moniker-end

1. <span data-ttu-id="b0b36-349">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-349">Run the sample app.</span></span> <span data-ttu-id="b0b36-350">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-350">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b0b36-351">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-351">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="b0b36-352">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="b0b36-352">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="b0b36-353">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente para mostrar as que começam com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b0b36-353">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="b0b36-354">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="b0b36-354">ASPNETCORE_</span></span>
* <span data-ttu-id="b0b36-355">urls</span><span class="sxs-lookup"><span data-stu-id="b0b36-355">urls</span></span>
* <span data-ttu-id="b0b36-356">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="b0b36-356">Logging</span></span>
* <span data-ttu-id="b0b36-357">AMBIENTE</span><span class="sxs-lookup"><span data-stu-id="b0b36-357">ENVIRONMENT</span></span>
* <span data-ttu-id="b0b36-358">contentRoot</span><span class="sxs-lookup"><span data-stu-id="b0b36-358">contentRoot</span></span>
* <span data-ttu-id="b0b36-359">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="b0b36-359">AllowedHosts</span></span>
* <span data-ttu-id="b0b36-360">applicationName</span><span class="sxs-lookup"><span data-stu-id="b0b36-360">applicationName</span></span>
* <span data-ttu-id="b0b36-361">CommandLine</span><span class="sxs-lookup"><span data-stu-id="b0b36-361">CommandLine</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-362">Se você quiser expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *Pages/Index.cshtml.cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b0b36-362">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-363">Se você quiser expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *Controllers/HomeController.cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b0b36-363">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Controllers/HomeController.cs* to the following:</span></span>

::: moniker-end

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="b0b36-364">Prefixos</span><span class="sxs-lookup"><span data-stu-id="b0b36-364">Prefixes</span></span>

<span data-ttu-id="b0b36-365">Variáveis de ambiente carregadas na configuração do aplicativo são filtradas quando você fornece um prefixo para o método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-365">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="b0b36-366">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-366">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="b0b36-367">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-367">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-368">O método de conveniência estático `CreateDefaultBuilder` cria um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> para estabelecer o host do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-368">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="b0b36-369">Quando `WebHostBuilder` é criado, ele encontra a configuração de seu host nas variáveis de ambiente prefixadas com `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-369">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

::: moniker-end

<span data-ttu-id="b0b36-370">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="b0b36-370">**Connection string prefixes**</span></span>

<span data-ttu-id="b0b36-371">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-371">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b0b36-372">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-372">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b0b36-373">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="b0b36-373">Connection string prefix</span></span> | <span data-ttu-id="b0b36-374">Provider</span><span class="sxs-lookup"><span data-stu-id="b0b36-374">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b0b36-375">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="b0b36-375">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b0b36-376">MySQL</span><span class="sxs-lookup"><span data-stu-id="b0b36-376">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b0b36-377">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="b0b36-377">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b0b36-378">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b0b36-378">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b0b36-379">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="b0b36-379">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b0b36-380">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="b0b36-380">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b0b36-381">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="b0b36-381">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b0b36-382">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="b0b36-382">Environment variable key</span></span> | <span data-ttu-id="b0b36-383">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="b0b36-383">Converted configuration key</span></span> | <span data-ttu-id="b0b36-384">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="b0b36-384">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b0b36-385">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="b0b36-385">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b0b36-386">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-386">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b0b36-387">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b0b36-387">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b0b36-388">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-388">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b0b36-389">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b0b36-389">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b0b36-390">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-390">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b0b36-391">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b0b36-391">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="b0b36-392">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="b0b36-392">File Configuration Provider</span></span>

<span data-ttu-id="b0b36-393"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="b0b36-393"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b0b36-394">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="b0b36-394">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="b0b36-395">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="b0b36-395">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b0b36-396">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="b0b36-396">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="b0b36-397">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="b0b36-397">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b0b36-398">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="b0b36-398">INI Configuration Provider</span></span>

<span data-ttu-id="b0b36-399">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b0b36-399">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b0b36-400">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-400">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0b36-401">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="b0b36-401">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="b0b36-402">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="b0b36-402">Overloads permit specifying:</span></span>

* <span data-ttu-id="b0b36-403">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="b0b36-403">Whether the file is optional.</span></span>
* <span data-ttu-id="b0b36-404">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-404">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b0b36-405">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-405">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0b36-406">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b0b36-406">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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
                config.AddIniFile("config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0b36-407">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-407">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0b36-408">Ao chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-408">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddIniFile("config.ini", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0b36-409">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-409">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-410">Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b0b36-410">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

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

<span data-ttu-id="b0b36-411">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="b0b36-411">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="b0b36-412">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-412">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b0b36-413">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-413">section0:key0</span></span>
* <span data-ttu-id="b0b36-414">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-414">section0:key1</span></span>
* <span data-ttu-id="b0b36-415">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="b0b36-415">section1:subsection:key</span></span>
* <span data-ttu-id="b0b36-416">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="b0b36-416">section2:subsection0:key</span></span>
* <span data-ttu-id="b0b36-417">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="b0b36-417">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="b0b36-418">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="b0b36-418">JSON Configuration Provider</span></span>

<span data-ttu-id="b0b36-419">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b0b36-419">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="b0b36-420">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-420">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0b36-421">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="b0b36-421">Overloads permit specifying:</span></span>

* <span data-ttu-id="b0b36-422">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="b0b36-422">Whether the file is optional.</span></span>
* <span data-ttu-id="b0b36-423">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-423">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b0b36-424">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-424">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-425">`AddJsonFile` é chamado automaticamente duas vezes quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-425">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b0b36-426">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="b0b36-426">The method is called to load configuration from:</span></span>

* <span data-ttu-id="b0b36-427">*appsettings.json* &ndash; Esse arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="b0b36-427">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="b0b36-428">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-428">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="b0b36-429">*appsettings.{Environment}.json* &ndash; A versão de ambiente do arquivo é carregada com base em [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b0b36-429">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="b0b36-430">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b0b36-430">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b0b36-431">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="b0b36-431">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b0b36-432">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="b0b36-432">Environment variables.</span></span>
* <span data-ttu-id="b0b36-433">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="b0b36-433">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b0b36-434">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="b0b36-434">Command-line arguments.</span></span>

<span data-ttu-id="b0b36-435">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="b0b36-435">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="b0b36-436">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-436">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0b36-437">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="b0b36-437">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

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
                config.AddJsonFile("config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0b36-438">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-438">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0b36-439">Você pode chamar diretamente o método de extensão `AddJsonFile` em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-439">You can also directly call the `AddJsonFile` extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0b36-440">Ao chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-440">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("config.json", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0b36-441">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-441">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-442">Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b0b36-442">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

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

<span data-ttu-id="b0b36-443">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="b0b36-443">**Example**</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-444">O aplicativo de exemplo 2.x aproveita a vantagem do método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-444">The 2.x sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="b0b36-445">A configuração é carregada de *appsettings.json* e de *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-445">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-446">O aplicativo de exemplo 1.x chama `AddJsonFile` duas vezes em um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-446">The 1.x sample app calls `AddJsonFile` twice on a `ConfigurationBuilder`.</span></span> <span data-ttu-id="b0b36-447">A configuração é carregada de *appsettings.json* e de *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-447">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

::: moniker-end

1. <span data-ttu-id="b0b36-448">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-448">Run the sample app.</span></span> <span data-ttu-id="b0b36-449">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-449">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b0b36-450">Observe que a saída contém pares chave-valor para a configuração mostrada na tabela de acordo com o ambiente.</span><span class="sxs-lookup"><span data-stu-id="b0b36-450">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="b0b36-451">Chaves de configuração de registro usam os dois-pontos (`:`) como um separador hierárquico.</span><span class="sxs-lookup"><span data-stu-id="b0b36-451">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="b0b36-452">Chave</span><span class="sxs-lookup"><span data-stu-id="b0b36-452">Key</span></span>                        | <span data-ttu-id="b0b36-453">Valor de Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="b0b36-453">Development Value</span></span> | <span data-ttu-id="b0b36-454">Valor de Produção</span><span class="sxs-lookup"><span data-stu-id="b0b36-454">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="b0b36-455">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="b0b36-455">Logging:LogLevel:System</span></span>    | <span data-ttu-id="b0b36-456">Informações</span><span class="sxs-lookup"><span data-stu-id="b0b36-456">Information</span></span>       | <span data-ttu-id="b0b36-457">Informações</span><span class="sxs-lookup"><span data-stu-id="b0b36-457">Information</span></span>      |
| <span data-ttu-id="b0b36-458">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="b0b36-458">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="b0b36-459">Informações</span><span class="sxs-lookup"><span data-stu-id="b0b36-459">Information</span></span>       | <span data-ttu-id="b0b36-460">Informações</span><span class="sxs-lookup"><span data-stu-id="b0b36-460">Information</span></span>      |
| <span data-ttu-id="b0b36-461">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="b0b36-461">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="b0b36-462">Depurar</span><span class="sxs-lookup"><span data-stu-id="b0b36-462">Debug</span></span>             | <span data-ttu-id="b0b36-463">Erro</span><span class="sxs-lookup"><span data-stu-id="b0b36-463">Error</span></span>            |
| <span data-ttu-id="b0b36-464">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="b0b36-464">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="b0b36-465">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="b0b36-465">XML Configuration Provider</span></span>

<span data-ttu-id="b0b36-466">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b0b36-466">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b0b36-467">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-467">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0b36-468">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="b0b36-468">Overloads permit specifying:</span></span>

* <span data-ttu-id="b0b36-469">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="b0b36-469">Whether the file is optional.</span></span>
* <span data-ttu-id="b0b36-470">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-470">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b0b36-471">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-471">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b0b36-472">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="b0b36-472">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="b0b36-473">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-473">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0b36-474">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b0b36-474">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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
                config.AddXmlFile("config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0b36-475">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-475">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0b36-476">Ao chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-476">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0b36-477">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-477">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-478">Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b0b36-478">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

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

<span data-ttu-id="b0b36-479">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="b0b36-479">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="b0b36-480">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-480">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b0b36-481">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-481">section0:key0</span></span>
* <span data-ttu-id="b0b36-482">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-482">section0:key1</span></span>
* <span data-ttu-id="b0b36-483">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-483">section1:key0</span></span>
* <span data-ttu-id="b0b36-484">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-484">section1:key1</span></span>

<span data-ttu-id="b0b36-485">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="b0b36-485">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="b0b36-486">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-486">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b0b36-487">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-487">section:section0:key:key0</span></span>
* <span data-ttu-id="b0b36-488">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-488">section:section0:key:key1</span></span>
* <span data-ttu-id="b0b36-489">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-489">section:section1:key:key0</span></span>
* <span data-ttu-id="b0b36-490">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-490">section:section1:key:key1</span></span>

<span data-ttu-id="b0b36-491">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="b0b36-491">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b0b36-492">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-492">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b0b36-493">key:attribute</span><span class="sxs-lookup"><span data-stu-id="b0b36-493">key:attribute</span></span>
* <span data-ttu-id="b0b36-494">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="b0b36-494">section:key:attribute</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b0b36-495">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="b0b36-495">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="b0b36-496">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-496">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b0b36-497">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-497">The key is the file name.</span></span> <span data-ttu-id="b0b36-498">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-498">The value contains the file's contents.</span></span> <span data-ttu-id="b0b36-499">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="b0b36-499">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b0b36-500">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-500">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b0b36-501">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="b0b36-501">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b0b36-502">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="b0b36-502">Overloads permit specifying:</span></span>

* <span data-ttu-id="b0b36-503">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="b0b36-503">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b0b36-504">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="b0b36-504">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b0b36-505">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-505">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="b0b36-506">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-506">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="b0b36-507">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b0b36-507">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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
                var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0b36-508">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-508">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

::: moniker-end

## <a name="memory-configuration-provider"></a><span data-ttu-id="b0b36-509">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="b0b36-509">Memory Configuration Provider</span></span>

<span data-ttu-id="b0b36-510">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-510">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b0b36-511">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-511">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0b36-512">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-512">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0b36-513">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b0b36-513">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="b0b36-514">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-514">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0b36-515">Ao chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-515">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

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
}
```

<span data-ttu-id="b0b36-516">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-516">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-517">Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b0b36-517">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

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

## <a name="getvalue"></a><span data-ttu-id="b0b36-518">GetValue</span><span class="sxs-lookup"><span data-stu-id="b0b36-518">GetValue</span></span>

<span data-ttu-id="b0b36-519">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um valor de configuração com uma chave especificada e o converte para o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-519">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="b0b36-520">Uma sobrecarga permite que você forneça um valor padrão se a chave não for encontrada.</span><span class="sxs-lookup"><span data-stu-id="b0b36-520">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="b0b36-521">O exemplo a seguir extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`, digita o valor como um `int` e armazena o valor na variável `intValue`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-521">The following example extracts the string value from configuration with the key `NumberKey`, types the value as an `int`, and stores the value in the variable `intValue`.</span></span> <span data-ttu-id="b0b36-522">Se `NumberKey` não for encontrado em chaves de configuração, `intValue` recebe o valor padrão de `99`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-522">If `NumberKey` isn't found in the configuration keys, `intValue` receives the default value of `99`:</span></span>

```csharp
var intValue = config.GetValue<int>("NumberKey", 99);
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b0b36-523">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="b0b36-523">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b0b36-524">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="b0b36-524">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="b0b36-525">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="b0b36-525">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="b0b36-526">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-526">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="b0b36-527">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-527">section0:key0</span></span>
* <span data-ttu-id="b0b36-528">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-528">section0:key1</span></span>
* <span data-ttu-id="b0b36-529">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-529">section1:key0</span></span>
* <span data-ttu-id="b0b36-530">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-530">section1:key1</span></span>
* <span data-ttu-id="b0b36-531">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-531">section2:subsection0:key0</span></span>
* <span data-ttu-id="b0b36-532">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-532">section2:subsection0:key1</span></span>
* <span data-ttu-id="b0b36-533">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="b0b36-533">section2:subsection1:key0</span></span>
* <span data-ttu-id="b0b36-534">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="b0b36-534">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="b0b36-535">GetSection</span><span class="sxs-lookup"><span data-stu-id="b0b36-535">GetSection</span></span>

<span data-ttu-id="b0b36-536">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="b0b36-536">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="b0b36-537">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="b0b36-537">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="b0b36-538">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="b0b36-538">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="b0b36-539">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-539">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b0b36-540">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-540">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

### <a name="getchildren"></a><span data-ttu-id="b0b36-541">GetChildren</span><span class="sxs-lookup"><span data-stu-id="b0b36-541">GetChildren</span></span>

<span data-ttu-id="b0b36-542">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="b0b36-542">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

::: moniker range=">= aspnetcore-2.0"

### <a name="exists"></a><span data-ttu-id="b0b36-543">Exists</span><span class="sxs-lookup"><span data-stu-id="b0b36-543">Exists</span></span>

<span data-ttu-id="b0b36-544">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-544">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="b0b36-545">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-545">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

::: moniker-end

## <a name="bind-to-a-class"></a><span data-ttu-id="b0b36-546">Associar a uma classe</span><span class="sxs-lookup"><span data-stu-id="b0b36-546">Bind to a class</span></span>

<span data-ttu-id="b0b36-547">A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*.</span><span class="sxs-lookup"><span data-stu-id="b0b36-547">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="b0b36-548">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-548">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b0b36-549">Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="b0b36-549">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="b0b36-550">O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0b36-550">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0b36-551">A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-551">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="b0b36-552">Os seguintes pares chave-valor de configuração são criados:</span><span class="sxs-lookup"><span data-stu-id="b0b36-552">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="b0b36-553">Chave</span><span class="sxs-lookup"><span data-stu-id="b0b36-553">Key</span></span>                   | <span data-ttu-id="b0b36-554">Valor</span><span class="sxs-lookup"><span data-stu-id="b0b36-554">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="b0b36-555">starship:name</span><span class="sxs-lookup"><span data-stu-id="b0b36-555">starship:name</span></span>         | <span data-ttu-id="b0b36-556">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="b0b36-556">USS Enterprise</span></span>                                    |
| <span data-ttu-id="b0b36-557">starship:registry</span><span class="sxs-lookup"><span data-stu-id="b0b36-557">starship:registry</span></span>     | <span data-ttu-id="b0b36-558">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="b0b36-558">NCC-1701</span></span>                                          |
| <span data-ttu-id="b0b36-559">starship:class</span><span class="sxs-lookup"><span data-stu-id="b0b36-559">starship:class</span></span>        | <span data-ttu-id="b0b36-560">Constituição</span><span class="sxs-lookup"><span data-stu-id="b0b36-560">Constitution</span></span>                                      |
| <span data-ttu-id="b0b36-561">starship:length</span><span class="sxs-lookup"><span data-stu-id="b0b36-561">starship:length</span></span>       | <span data-ttu-id="b0b36-562">304,8</span><span class="sxs-lookup"><span data-stu-id="b0b36-562">304.8</span></span>                                             |
| <span data-ttu-id="b0b36-563">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="b0b36-563">starship:commissioned</span></span> | <span data-ttu-id="b0b36-564">False</span><span class="sxs-lookup"><span data-stu-id="b0b36-564">False</span></span>                                             |
| <span data-ttu-id="b0b36-565">marca</span><span class="sxs-lookup"><span data-stu-id="b0b36-565">trademark</span></span>             | <span data-ttu-id="b0b36-566">Paramount Pictures Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="b0b36-566">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="b0b36-567">O aplicativo de exemplo chama `GetSection` com a chave `starship`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-567">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="b0b36-568">Os pares chave-valor `starship` são isolados.</span><span class="sxs-lookup"><span data-stu-id="b0b36-568">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="b0b36-569">O método `Bind` é chamado na subseção passando uma instância da classe `Starship`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-569">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="b0b36-570">Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="b0b36-570">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="b0b36-571">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="b0b36-571">Bind to an object graph</span></span>

<span data-ttu-id="b0b36-572"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="b0b36-572"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="b0b36-573">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0b36-573">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0b36-574">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-574">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-xml[](index/samples/1.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="b0b36-575">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-575">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="b0b36-576">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="b0b36-576">The bound instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
viewModel.TvShow = tvShow;
```

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="b0b36-577">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-577">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="b0b36-578">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-578">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="b0b36-579">O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:</span><span class="sxs-lookup"><span data-stu-id="b0b36-579">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b0b36-580">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="b0b36-580">Bind an array to a class</span></span>

<span data-ttu-id="b0b36-581">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="b0b36-581">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="b0b36-582">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-582">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b0b36-583">Qualquer formato de matriz que exponha um segmento de chave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar matrizes a uma matriz de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="b0b36-583">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="b0b36-584">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="b0b36-584">Binding is provided by convention.</span></span> <span data-ttu-id="b0b36-585">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="b0b36-585">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="b0b36-586">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="b0b36-586">**In-memory array processing**</span></span>

<span data-ttu-id="b0b36-587">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="b0b36-587">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="b0b36-588">Chave</span><span class="sxs-lookup"><span data-stu-id="b0b36-588">Key</span></span>             | <span data-ttu-id="b0b36-589">Valor</span><span class="sxs-lookup"><span data-stu-id="b0b36-589">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b0b36-590">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="b0b36-590">array:entries:0</span></span> | <span data-ttu-id="b0b36-591">value0</span><span class="sxs-lookup"><span data-stu-id="b0b36-591">value0</span></span> |
| <span data-ttu-id="b0b36-592">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="b0b36-592">array:entries:1</span></span> | <span data-ttu-id="b0b36-593">value1</span><span class="sxs-lookup"><span data-stu-id="b0b36-593">value1</span></span> |
| <span data-ttu-id="b0b36-594">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="b0b36-594">array:entries:2</span></span> | <span data-ttu-id="b0b36-595">value2</span><span class="sxs-lookup"><span data-stu-id="b0b36-595">value2</span></span> |
| <span data-ttu-id="b0b36-596">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="b0b36-596">array:entries:4</span></span> | <span data-ttu-id="b0b36-597">value4</span><span class="sxs-lookup"><span data-stu-id="b0b36-597">value4</span></span> |
| <span data-ttu-id="b0b36-598">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="b0b36-598">array:entries:5</span></span> | <span data-ttu-id="b0b36-599">value5</span><span class="sxs-lookup"><span data-stu-id="b0b36-599">value5</span></span> |

<span data-ttu-id="b0b36-600">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="b0b36-600">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=3-10,22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=5-12,16)]

::: moniker-end

<span data-ttu-id="b0b36-601">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="b0b36-601">The array skips a value for index &num;3.</span></span> <span data-ttu-id="b0b36-602">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="b0b36-602">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="b0b36-603">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="b0b36-603">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0b36-604">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="b0b36-604">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="b0b36-605">A sintaxe [ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="b0b36-605">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="b0b36-606">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-606">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="b0b36-607">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b0b36-607">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b0b36-608">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b0b36-608">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b0b36-609">0</span><span class="sxs-lookup"><span data-stu-id="b0b36-609">0</span></span>                            | <span data-ttu-id="b0b36-610">value0</span><span class="sxs-lookup"><span data-stu-id="b0b36-610">value0</span></span>                       |
| <span data-ttu-id="b0b36-611">1</span><span class="sxs-lookup"><span data-stu-id="b0b36-611">1</span></span>                            | <span data-ttu-id="b0b36-612">value1</span><span class="sxs-lookup"><span data-stu-id="b0b36-612">value1</span></span>                       |
| <span data-ttu-id="b0b36-613">2</span><span class="sxs-lookup"><span data-stu-id="b0b36-613">2</span></span>                            | <span data-ttu-id="b0b36-614">value2</span><span class="sxs-lookup"><span data-stu-id="b0b36-614">value2</span></span>                       |
| <span data-ttu-id="b0b36-615">3</span><span class="sxs-lookup"><span data-stu-id="b0b36-615">3</span></span>                            | <span data-ttu-id="b0b36-616">value4</span><span class="sxs-lookup"><span data-stu-id="b0b36-616">value4</span></span>                       |
| <span data-ttu-id="b0b36-617">4</span><span class="sxs-lookup"><span data-stu-id="b0b36-617">4</span></span>                            | <span data-ttu-id="b0b36-618">value5</span><span class="sxs-lookup"><span data-stu-id="b0b36-618">value5</span></span>                       |

<span data-ttu-id="b0b36-619">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-619">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b0b36-620">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="b0b36-620">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b0b36-621">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="b0b36-621">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b0b36-622">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-622">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="b0b36-623">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="b0b36-623">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="b0b36-624">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="b0b36-624">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0b36-625">No <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b0b36-625">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0b36-626">No construtor `Startup`:</span><span class="sxs-lookup"><span data-stu-id="b0b36-626">In the `Startup` constructor:</span></span>

```csharp
.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

<span data-ttu-id="b0b36-627">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-627">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="b0b36-628">Chave</span><span class="sxs-lookup"><span data-stu-id="b0b36-628">Key</span></span>             | <span data-ttu-id="b0b36-629">Valor</span><span class="sxs-lookup"><span data-stu-id="b0b36-629">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b0b36-630">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="b0b36-630">array:entries:3</span></span> | <span data-ttu-id="b0b36-631">value3</span><span class="sxs-lookup"><span data-stu-id="b0b36-631">value3</span></span> |

<span data-ttu-id="b0b36-632">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="b0b36-632">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="b0b36-633">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b0b36-633">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b0b36-634">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="b0b36-634">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b0b36-635">0</span><span class="sxs-lookup"><span data-stu-id="b0b36-635">0</span></span>                            | <span data-ttu-id="b0b36-636">value0</span><span class="sxs-lookup"><span data-stu-id="b0b36-636">value0</span></span>                       |
| <span data-ttu-id="b0b36-637">1</span><span class="sxs-lookup"><span data-stu-id="b0b36-637">1</span></span>                            | <span data-ttu-id="b0b36-638">value1</span><span class="sxs-lookup"><span data-stu-id="b0b36-638">value1</span></span>                       |
| <span data-ttu-id="b0b36-639">2</span><span class="sxs-lookup"><span data-stu-id="b0b36-639">2</span></span>                            | <span data-ttu-id="b0b36-640">value2</span><span class="sxs-lookup"><span data-stu-id="b0b36-640">value2</span></span>                       |
| <span data-ttu-id="b0b36-641">3</span><span class="sxs-lookup"><span data-stu-id="b0b36-641">3</span></span>                            | <span data-ttu-id="b0b36-642">value3</span><span class="sxs-lookup"><span data-stu-id="b0b36-642">value3</span></span>                       |
| <span data-ttu-id="b0b36-643">4</span><span class="sxs-lookup"><span data-stu-id="b0b36-643">4</span></span>                            | <span data-ttu-id="b0b36-644">value4</span><span class="sxs-lookup"><span data-stu-id="b0b36-644">value4</span></span>                       |
| <span data-ttu-id="b0b36-645">5</span><span class="sxs-lookup"><span data-stu-id="b0b36-645">5</span></span>                            | <span data-ttu-id="b0b36-646">value5</span><span class="sxs-lookup"><span data-stu-id="b0b36-646">value5</span></span>                       |

<span data-ttu-id="b0b36-647">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="b0b36-647">**JSON array processing**</span></span>

<span data-ttu-id="b0b36-648">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="b0b36-648">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="b0b36-649">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="b0b36-649">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="b0b36-650">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="b0b36-650">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="b0b36-651">Chave</span><span class="sxs-lookup"><span data-stu-id="b0b36-651">Key</span></span>                     | <span data-ttu-id="b0b36-652">Valor</span><span class="sxs-lookup"><span data-stu-id="b0b36-652">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="b0b36-653">json_array:key</span><span class="sxs-lookup"><span data-stu-id="b0b36-653">json_array:key</span></span>          | <span data-ttu-id="b0b36-654">valueA</span><span class="sxs-lookup"><span data-stu-id="b0b36-654">valueA</span></span> |
| <span data-ttu-id="b0b36-655">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="b0b36-655">json_array:subsection:0</span></span> | <span data-ttu-id="b0b36-656">valueB</span><span class="sxs-lookup"><span data-stu-id="b0b36-656">valueB</span></span> |
| <span data-ttu-id="b0b36-657">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="b0b36-657">json_array:subsection:1</span></span> | <span data-ttu-id="b0b36-658">valueC</span><span class="sxs-lookup"><span data-stu-id="b0b36-658">valueC</span></span> |
| <span data-ttu-id="b0b36-659">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="b0b36-659">json_array:subsection:2</span></span> | <span data-ttu-id="b0b36-660">valueD</span><span class="sxs-lookup"><span data-stu-id="b0b36-660">valueD</span></span> |

<span data-ttu-id="b0b36-661">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="b0b36-661">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0b36-662">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-662">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="b0b36-663">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-663">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="b0b36-664">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="b0b36-664">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="b0b36-665">Valor `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="b0b36-665">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="b0b36-666">0</span><span class="sxs-lookup"><span data-stu-id="b0b36-666">0</span></span>                                   | <span data-ttu-id="b0b36-667">valueB</span><span class="sxs-lookup"><span data-stu-id="b0b36-667">valueB</span></span>                              |
| <span data-ttu-id="b0b36-668">1</span><span class="sxs-lookup"><span data-stu-id="b0b36-668">1</span></span>                                   | <span data-ttu-id="b0b36-669">valueC</span><span class="sxs-lookup"><span data-stu-id="b0b36-669">valueC</span></span>                              |
| <span data-ttu-id="b0b36-670">2</span><span class="sxs-lookup"><span data-stu-id="b0b36-670">2</span></span>                                   | <span data-ttu-id="b0b36-671">valueD</span><span class="sxs-lookup"><span data-stu-id="b0b36-671">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="b0b36-672">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="b0b36-672">Custom configuration provider</span></span>

<span data-ttu-id="b0b36-673">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b0b36-673">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b0b36-674">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="b0b36-674">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b0b36-675">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-675">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b0b36-676">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0b36-676">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b0b36-677">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="b0b36-677">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b0b36-678">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="b0b36-678">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b0b36-679">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-679">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b0b36-680">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b0b36-680">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b0b36-681">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0b36-681">*Models/EFConfigurationValue.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0b36-682">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="b0b36-682">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b0b36-683">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0b36-683">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0b36-684">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-684">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b0b36-685">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0b36-685">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0b36-686">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-686">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b0b36-687">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="b0b36-687">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="b0b36-688">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0b36-688">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0b36-689">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-689">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b0b36-690">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0b36-690">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0b36-691">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0b36-691">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=26)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=24)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="b0b36-692">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="b0b36-692">Access configuration during startup</span></span>

<span data-ttu-id="b0b36-693">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b0b36-693">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b0b36-694">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="b0b36-694">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="b0b36-695">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, confira [Inicialização do aplicativo: Métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="b0b36-695">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="b0b36-696">Acessar a configuração em uma página do Razor Pages ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="b0b36-696">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="b0b36-697">Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.</span><span class="sxs-lookup"><span data-stu-id="b0b36-697">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="b0b36-698">Em uma página do Razor:</span><span class="sxs-lookup"><span data-stu-id="b0b36-698">In a Razor Pages page:</span></span>

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

<span data-ttu-id="b0b36-699">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="b0b36-699">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b0b36-700">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="b0b36-700">Add configuration from an external assembly</span></span>

<span data-ttu-id="b0b36-701">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0b36-701">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b0b36-702">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b0b36-702">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0b36-703">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b0b36-703">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* [<span data-ttu-id="b0b36-704">Detalhes da configuração da Microsoft</span><span class="sxs-lookup"><span data-stu-id="b0b36-704">Deep Dive into Microsoft Configuration</span></span>](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)
