---
title: Configuração no ASP.NET Core
author: guardrex
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2019
uid: fundamentals/configuration/index
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="c19d6-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c19d6-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="c19d6-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c19d6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c19d6-105">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="c19d6-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="c19d6-106">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="c19d6-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c19d6-107">Azure Key Vault</span></span>
* <span data-ttu-id="c19d6-108">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="c19d6-108">Command-line arguments</span></span>
* <span data-ttu-id="c19d6-109">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="c19d6-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="c19d6-110">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="c19d6-110">Directory files</span></span>
* <span data-ttu-id="c19d6-111">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="c19d6-111">Environment variables</span></span>
* <span data-ttu-id="c19d6-112">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="c19d6-112">In-memory .NET objects</span></span>
* <span data-ttu-id="c19d6-113">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="c19d6-113">Settings files</span></span>

<span data-ttu-id="c19d6-114">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="c19d6-114">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="c19d6-115">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="c19d6-115">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="c19d6-116">Para saber mais sobre como usar o padrão de opções, confira <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-116">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="c19d6-117">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c19d6-117">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c19d6-118">Esses três pacotes estão incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-118">These three packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="host-vs-app-configuration"></a><span data-ttu-id="c19d6-119">Configuração do host versus aplicativo</span><span class="sxs-lookup"><span data-stu-id="c19d6-119">Host vs. app configuration</span></span>

<span data-ttu-id="c19d6-120">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-120">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="c19d6-121">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-121">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="c19d6-122">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="c19d6-122">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="c19d6-123">Os pares chave-valor de configuração do host se tornam parte da configuração global do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-123">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="c19d6-124">Para saber mais sobre como os provedores de configuração são usados quando o host for criado e como as fontes de configuração afetam o host e a configuração, veja [O host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="c19d6-124">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see [The host](xref:fundamentals/index#host).</span></span>

## <a name="default-configuration"></a><span data-ttu-id="c19d6-125">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="c19d6-125">Default configuration</span></span>

<span data-ttu-id="c19d6-126">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="c19d6-126">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="c19d6-127"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="c19d6-127"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

* <span data-ttu-id="c19d6-128">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="c19d6-128">Host configuration is provided from:</span></span>
  * <span data-ttu-id="c19d6-129">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c19d6-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="c19d6-130">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-130">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="c19d6-131">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c19d6-131">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="c19d6-132">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="c19d6-132">App configuration is provided from:</span></span>
  * <span data-ttu-id="c19d6-133">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c19d6-133">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="c19d6-134">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c19d6-134">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="c19d6-135">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="c19d6-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="c19d6-136">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c19d6-136">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="c19d6-137">Se um prefixo personalizado for usado (por exemplo, `PREFIX_` com `.AddEnvironmentVariables(prefix: "PREFIX_")`), o prefixo será removido durante o carregamento dos pares de chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-137">If a custom prefix is used (for example, `PREFIX_` with `.AddEnvironmentVariables(prefix: "PREFIX_")`), the prefix is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="c19d6-138">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c19d6-138">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="c19d6-139">Os provedores de configuração são explicados posteriormente neste tópico.</span><span class="sxs-lookup"><span data-stu-id="c19d6-139">The configuration providers are explained later in this topic.</span></span> <span data-ttu-id="c19d6-140">Para obter mais informações sobre o host e <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, consulte <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-140">For more information on the host and <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="security"></a><span data-ttu-id="c19d6-141">Segurança</span><span class="sxs-lookup"><span data-stu-id="c19d6-141">Security</span></span>

<span data-ttu-id="c19d6-142">Adote as melhores práticas a seguir:</span><span class="sxs-lookup"><span data-stu-id="c19d6-142">Adopt the following best practices:</span></span>

* <span data-ttu-id="c19d6-143">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="c19d6-143">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="c19d6-144">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c19d6-144">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="c19d6-145">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="c19d6-145">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="c19d6-146">Saiba mais sobre [como usar vários ambientes](xref:fundamentals/environments) e gerenciar o [armazenamento seguro de segredos de aplicativo em desenvolvimento com o Gerenciador de Segredo](xref:security/app-secrets) (inclui recomendações sobre como usar variáveis de ambiente para armazenar dados confidenciais).</span><span class="sxs-lookup"><span data-stu-id="c19d6-146">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="c19d6-147">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="c19d6-147">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="c19d6-148">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="c19d6-148">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="c19d6-149">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) é uma opção para o armazenamento seguro de segredos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-149">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="c19d6-150">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-150">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="c19d6-151">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="c19d6-151">Hierarchical configuration data</span></span>

<span data-ttu-id="c19d6-152">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-152">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="c19d6-153">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="c19d6-153">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="c19d6-154">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-154">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="c19d6-155">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="c19d6-155">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="c19d6-156">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-156">section0:key0</span></span>
* <span data-ttu-id="c19d6-157">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-157">section0:key1</span></span>
* <span data-ttu-id="c19d6-158">section1:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-158">section1:key0</span></span>
* <span data-ttu-id="c19d6-159">section1:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-159">section1:key1</span></span>

<span data-ttu-id="c19d6-160">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-160"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="c19d6-161">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="c19d6-161">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span> <span data-ttu-id="c19d6-162">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-162">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="conventions"></a><span data-ttu-id="c19d6-163">Convenções</span><span class="sxs-lookup"><span data-stu-id="c19d6-163">Conventions</span></span>

<span data-ttu-id="c19d6-164">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-164">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="c19d6-165">Os Provedores de Configuração de Arquivo têm a capacidade de recarregar a configuração quando um arquivo de configurações subjacente é alterado após a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-165">File Configuration Providers have the ability to reload configuration when an underlying settings file is changed after app startup.</span></span> <span data-ttu-id="c19d6-166">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="c19d6-166">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="c19d6-167"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-167"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c19d6-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetado em um <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> do Razor Pages para obter a configuração para a classe:</span><span class="sxs-lookup"><span data-stu-id="c19d6-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
// using Microsoft.Extensions.Configuration;

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

<span data-ttu-id="c19d6-169">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="c19d6-169">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="c19d6-170">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="c19d6-170">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="c19d6-171">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="c19d6-171">Keys are case-insensitive.</span></span> <span data-ttu-id="c19d6-172">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="c19d6-172">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="c19d6-173">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-173">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="c19d6-174">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="c19d6-174">Hierarchical keys</span></span>
  * <span data-ttu-id="c19d6-175">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="c19d6-175">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="c19d6-176">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="c19d6-176">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="c19d6-177">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="c19d6-177">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="c19d6-178">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="c19d6-178">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="c19d6-179">Você deve fornecer o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-179">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="c19d6-180">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-180">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="c19d6-181">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="c19d6-181">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="c19d6-182">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="c19d6-182">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="c19d6-183">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="c19d6-183">Values are strings.</span></span>
* <span data-ttu-id="c19d6-184">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="c19d6-184">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="c19d6-185">Provedores</span><span class="sxs-lookup"><span data-stu-id="c19d6-185">Providers</span></span>

<span data-ttu-id="c19d6-186">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c19d6-186">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="c19d6-187">Provider</span><span class="sxs-lookup"><span data-stu-id="c19d6-187">Provider</span></span> | <span data-ttu-id="c19d6-188">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="c19d6-188">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="c19d6-189">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="c19d6-189">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="c19d6-190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c19d6-190">Azure Key Vault</span></span> |
| [<span data-ttu-id="c19d6-191">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="c19d6-191">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="c19d6-192">Parâmetros de linha de comando</span><span class="sxs-lookup"><span data-stu-id="c19d6-192">Command-line parameters</span></span> |
| [<span data-ttu-id="c19d6-193">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="c19d6-193">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="c19d6-194">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="c19d6-194">Custom source</span></span> |
| [<span data-ttu-id="c19d6-195">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="c19d6-195">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="c19d6-196">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="c19d6-196">Environment variables</span></span> |
| [<span data-ttu-id="c19d6-197">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="c19d6-197">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="c19d6-198">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="c19d6-198">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="c19d6-199">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="c19d6-199">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="c19d6-200">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="c19d6-200">Directory files</span></span> |
| [<span data-ttu-id="c19d6-201">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="c19d6-201">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="c19d6-202">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="c19d6-202">In-memory collections</span></span> |
| <span data-ttu-id="c19d6-203">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="c19d6-203">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="c19d6-204">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="c19d6-204">File in the user profile directory</span></span> |

<span data-ttu-id="c19d6-205">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-205">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="c19d6-206">Os provedores de configuração descritos neste tópico estão descritos em ordem alfabética, não na ordem na qual seu código pode organizá-los.</span><span class="sxs-lookup"><span data-stu-id="c19d6-206">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="c19d6-207">Organize os provedores de configuração em seu código para atender às suas prioridades para as fontes de configuração subjacentes.</span><span class="sxs-lookup"><span data-stu-id="c19d6-207">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="c19d6-208">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="c19d6-208">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="c19d6-209">Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="c19d6-209">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="c19d6-210">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c19d6-210">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="c19d6-211">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="c19d6-211">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="c19d6-212">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="c19d6-212">Environment variables</span></span>
1. <span data-ttu-id="c19d6-213">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="c19d6-213">Command-line arguments</span></span>

<span data-ttu-id="c19d6-214">É comum posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="c19d6-214">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="c19d6-215">Essa sequência de provedores é aplicada quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-215">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="c19d6-216">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="c19d6-216">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

## <a name="configureappconfiguration"></a><span data-ttu-id="c19d6-217">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="c19d6-217">ConfigureAppConfiguration</span></span>

<span data-ttu-id="c19d6-218">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="c19d6-218">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=19)]

<span data-ttu-id="c19d6-219">a configuração fornecida para o aplicativo no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-219">Configuration supplied to the app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c19d6-220">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="c19d6-220">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="c19d6-221">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="c19d6-221">Command-line Configuration Provider</span></span>

<span data-ttu-id="c19d6-222">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c19d6-222">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="c19d6-223">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-223">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c19d6-224">`AddCommandLine` é chamado automaticamente quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-224">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="c19d6-225">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="c19d6-225">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="c19d6-226">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="c19d6-226">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="c19d6-227">Configuração opcional de *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="c19d6-227">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="c19d6-228">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="c19d6-228">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="c19d6-229">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="c19d6-229">Environment variables.</span></span>

<span data-ttu-id="c19d6-230">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="c19d6-230">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="c19d6-231">Os argumentos de linha de comando passados em tempo de execução substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="c19d6-231">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="c19d6-232">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="c19d6-232">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="c19d6-233">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-233">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="c19d6-234">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-234">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="c19d6-235">`AddCommandLine` já foi chamado por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-235">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c19d6-236">Se precisar oferecer configuração de aplicativo e ainda poder substituir essa configuração por argumentos de linha de comando, chame os provedores adicionais do aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> e chame `AddCommandLine` por fim.</span><span class="sxs-lookup"><span data-stu-id="c19d6-236">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

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

<span data-ttu-id="c19d6-237">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-237">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="c19d6-238">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="c19d6-238">**Example**</span></span>

<span data-ttu-id="c19d6-239">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-239">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="c19d6-240">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="c19d6-240">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="c19d6-241">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-241">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="c19d6-242">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-242">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="c19d6-243">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-243">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="c19d6-244">Arguments</span><span class="sxs-lookup"><span data-stu-id="c19d6-244">Arguments</span></span>

<span data-ttu-id="c19d6-245">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="c19d6-245">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="c19d6-246">O valor pode ser nulo se um sinal de igual for usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="c19d6-246">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="c19d6-247">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="c19d6-247">Key prefix</span></span>               | <span data-ttu-id="c19d6-248">Exemplo</span><span class="sxs-lookup"><span data-stu-id="c19d6-248">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="c19d6-249">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="c19d6-249">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="c19d6-250">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="c19d6-250">Two dashes (`--`)</span></span>        | <span data-ttu-id="c19d6-251">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="c19d6-251">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="c19d6-252">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="c19d6-252">Forward slash (`/`)</span></span>      | <span data-ttu-id="c19d6-253">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="c19d6-253">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="c19d6-254">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="c19d6-254">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="c19d6-255">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="c19d6-255">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="c19d6-256">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="c19d6-256">Switch mappings</span></span>

<span data-ttu-id="c19d6-257">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="c19d6-257">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="c19d6-258">Quando você cria manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, pode fornecer um dicionário de substituições de opções para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-258">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="c19d6-259">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="c19d6-259">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="c19d6-260">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-260">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="c19d6-261">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="c19d6-261">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="c19d6-262">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="c19d6-262">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="c19d6-263">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="c19d6-263">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="c19d6-264">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="c19d6-264">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="c19d6-265">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c19d6-265">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="c19d6-266">Conforme mostrado no exemplo anterior, a chamada para `CreateDefaultBuilder` não deve passar argumentos ao usar mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="c19d6-266">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="c19d6-267">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-267">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c19d6-268">Se os argumentos incluírem uma opção mapeada e forem passados para `CreateDefaultBuilder`, o provedor `AddCommandLine` não inicializará com um <xref:System.FormatException>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-268">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="c19d6-269">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="c19d6-269">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="c19d6-270">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="c19d6-270">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="c19d6-271">Chave</span><span class="sxs-lookup"><span data-stu-id="c19d6-271">Key</span></span>       | <span data-ttu-id="c19d6-272">Valor</span><span class="sxs-lookup"><span data-stu-id="c19d6-272">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="c19d6-273">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="c19d6-273">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="c19d6-274">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="c19d6-274">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="c19d6-275">Chave</span><span class="sxs-lookup"><span data-stu-id="c19d6-275">Key</span></span>               | <span data-ttu-id="c19d6-276">Valor</span><span class="sxs-lookup"><span data-stu-id="c19d6-276">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="c19d6-277">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="c19d6-277">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="c19d6-278">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c19d6-278">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="c19d6-279">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-279">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c19d6-280">Ao trabalhar com chaves hierárquicas nas variáveis de ambiente, talvez um separador de dois-pontos (`:`) não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="c19d6-280">When working with hierarchical keys in environment variables, a colon separator (`:`) may not work on all platforms.</span></span> <span data-ttu-id="c19d6-281">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é substituído por dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="c19d6-281">A double underscore (`__`) is supported by all platforms and is replaced by a colon.</span></span>

<span data-ttu-id="c19d6-282">O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) permite que você defina variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o Provedor de Configuração de Variáveis de Ambiente.</span><span class="sxs-lookup"><span data-stu-id="c19d6-282">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="c19d6-283">Para obter mais informações, confira [Aplicativos Azure: Substituir a configuração do aplicativo no Portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="c19d6-283">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="c19d6-284">`AddEnvironmentVariables` é chamado automaticamente para as variáveis de ambiente prefixadas com `ASPNETCORE_` quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-284">`AddEnvironmentVariables` is automatically called for environment variables prefixed with `ASPNETCORE_` when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="c19d6-285">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="c19d6-285">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="c19d6-286">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="c19d6-286">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="c19d6-287">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-287">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="c19d6-288">Configuração opcional de *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="c19d6-288">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="c19d6-289">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="c19d6-289">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="c19d6-290">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="c19d6-290">Command-line arguments.</span></span>

<span data-ttu-id="c19d6-291">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="c19d6-291">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="c19d6-292">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em tempo de execução para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="c19d6-292">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="c19d6-293">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-293">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="c19d6-294">Se precisar fornecer configuração do aplicativo com base em variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>e chame `AddEnvironmentVariables` com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-294">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

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

<span data-ttu-id="c19d6-295">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-295">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="c19d6-296">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="c19d6-296">**Example**</span></span>

<span data-ttu-id="c19d6-297">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-297">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="c19d6-298">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-298">Run the sample app.</span></span> <span data-ttu-id="c19d6-299">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-299">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="c19d6-300">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-300">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="c19d6-301">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="c19d6-301">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="c19d6-302">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente para mostrar as que começam com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="c19d6-302">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="c19d6-303">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="c19d6-303">ASPNETCORE_</span></span>
* <span data-ttu-id="c19d6-304">urls</span><span class="sxs-lookup"><span data-stu-id="c19d6-304">urls</span></span>
* <span data-ttu-id="c19d6-305">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="c19d6-305">Logging</span></span>
* <span data-ttu-id="c19d6-306">AMBIENTE</span><span class="sxs-lookup"><span data-stu-id="c19d6-306">ENVIRONMENT</span></span>
* <span data-ttu-id="c19d6-307">contentRoot</span><span class="sxs-lookup"><span data-stu-id="c19d6-307">contentRoot</span></span>
* <span data-ttu-id="c19d6-308">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="c19d6-308">AllowedHosts</span></span>
* <span data-ttu-id="c19d6-309">applicationName</span><span class="sxs-lookup"><span data-stu-id="c19d6-309">applicationName</span></span>
* <span data-ttu-id="c19d6-310">CommandLine</span><span class="sxs-lookup"><span data-stu-id="c19d6-310">CommandLine</span></span>

<span data-ttu-id="c19d6-311">Se você quiser expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *Pages/Index.cshtml.cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="c19d6-311">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="c19d6-312">Prefixos</span><span class="sxs-lookup"><span data-stu-id="c19d6-312">Prefixes</span></span>

<span data-ttu-id="c19d6-313">Variáveis de ambiente carregadas na configuração do aplicativo são filtradas quando você fornece um prefixo para o método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-313">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="c19d6-314">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-314">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="c19d6-315">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-315">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="c19d6-316">O método de conveniência estático `CreateDefaultBuilder` cria um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> para estabelecer o host do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-316">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="c19d6-317">Quando `WebHostBuilder` é criado, ele encontra a configuração de seu host nas variáveis de ambiente prefixadas com `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-317">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

<span data-ttu-id="c19d6-318">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="c19d6-318">**Connection string prefixes**</span></span>

<span data-ttu-id="c19d6-319">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-319">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="c19d6-320">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-320">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="c19d6-321">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="c19d6-321">Connection string prefix</span></span> | <span data-ttu-id="c19d6-322">Provider</span><span class="sxs-lookup"><span data-stu-id="c19d6-322">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="c19d6-323">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="c19d6-323">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="c19d6-324">MySQL</span><span class="sxs-lookup"><span data-stu-id="c19d6-324">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="c19d6-325">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="c19d6-325">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="c19d6-326">SQL Server</span><span class="sxs-lookup"><span data-stu-id="c19d6-326">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="c19d6-327">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="c19d6-327">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="c19d6-328">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="c19d6-328">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="c19d6-329">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="c19d6-329">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="c19d6-330">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="c19d6-330">Environment variable key</span></span> | <span data-ttu-id="c19d6-331">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="c19d6-331">Converted configuration key</span></span> | <span data-ttu-id="c19d6-332">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="c19d6-332">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="c19d6-333">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="c19d6-333">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="c19d6-334">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="c19d6-334">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="c19d6-335">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="c19d6-335">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="c19d6-336">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="c19d6-336">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="c19d6-337">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="c19d6-337">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="c19d6-338">Chave: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="c19d6-338">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="c19d6-339">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="c19d6-339">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="c19d6-340">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="c19d6-340">File Configuration Provider</span></span>

<span data-ttu-id="c19d6-341"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="c19d6-341"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="c19d6-342">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="c19d6-342">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="c19d6-343">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="c19d6-343">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="c19d6-344">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="c19d6-344">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="c19d6-345">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="c19d6-345">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="c19d6-346">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="c19d6-346">INI Configuration Provider</span></span>

<span data-ttu-id="c19d6-347">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c19d6-347">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="c19d6-348">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-348">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c19d6-349">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="c19d6-349">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="c19d6-350">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="c19d6-350">Overloads permit specifying:</span></span>

* <span data-ttu-id="c19d6-351">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="c19d6-351">Whether the file is optional.</span></span>
* <span data-ttu-id="c19d6-352">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-352">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="c19d6-353">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-353">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="c19d6-354">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c19d6-354">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="c19d6-355">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-355">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="c19d6-356">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-356">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-357">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-357">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="c19d6-358">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-358">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="c19d6-359">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-359">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-360">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="c19d6-360">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="c19d6-361">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="c19d6-361">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c19d6-362">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-362">section0:key0</span></span>
* <span data-ttu-id="c19d6-363">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-363">section0:key1</span></span>
* <span data-ttu-id="c19d6-364">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="c19d6-364">section1:subsection:key</span></span>
* <span data-ttu-id="c19d6-365">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="c19d6-365">section2:subsection0:key</span></span>
* <span data-ttu-id="c19d6-366">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="c19d6-366">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="c19d6-367">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="c19d6-367">JSON Configuration Provider</span></span>

<span data-ttu-id="c19d6-368">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c19d6-368">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="c19d6-369">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-369">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c19d6-370">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="c19d6-370">Overloads permit specifying:</span></span>

* <span data-ttu-id="c19d6-371">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="c19d6-371">Whether the file is optional.</span></span>
* <span data-ttu-id="c19d6-372">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-372">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="c19d6-373">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-373">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>



<span data-ttu-id="c19d6-374">`AddJsonFile` é chamado automaticamente duas vezes quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-374">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="c19d6-375">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="c19d6-375">The method is called to load configuration from:</span></span>

* <span data-ttu-id="c19d6-376">*appsettings.json* &ndash; Esse arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="c19d6-376">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="c19d6-377">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c19d6-377">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="c19d6-378">*appsettings.{Environment}.json* &ndash; A versão de ambiente do arquivo é carregada com base em [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="c19d6-378">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="c19d6-379">Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="c19d6-379">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="c19d6-380">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="c19d6-380">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="c19d6-381">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="c19d6-381">Environment variables.</span></span>
* <span data-ttu-id="c19d6-382">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="c19d6-382">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="c19d6-383">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="c19d6-383">Command-line arguments.</span></span>

<span data-ttu-id="c19d6-384">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="c19d6-384">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="c19d6-385">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="c19d6-385">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="c19d6-386">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="c19d6-386">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

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

<span data-ttu-id="c19d6-387">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-387">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="c19d6-388">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-388">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-389">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-389">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="c19d6-390">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-390">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="c19d6-391">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-391">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-392">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="c19d6-392">**Example**</span></span>

<span data-ttu-id="c19d6-393">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-393">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="c19d6-394">A configuração é carregada de *appsettings.json* e de *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="c19d6-394">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="c19d6-395">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-395">Run the sample app.</span></span> <span data-ttu-id="c19d6-396">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-396">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="c19d6-397">Observe que a saída contém pares chave-valor para a configuração mostrada na tabela de acordo com o ambiente.</span><span class="sxs-lookup"><span data-stu-id="c19d6-397">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="c19d6-398">Chaves de configuração de registro usam os dois-pontos (`:`) como um separador hierárquico.</span><span class="sxs-lookup"><span data-stu-id="c19d6-398">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="c19d6-399">Chave</span><span class="sxs-lookup"><span data-stu-id="c19d6-399">Key</span></span>                        | <span data-ttu-id="c19d6-400">Valor de Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="c19d6-400">Development Value</span></span> | <span data-ttu-id="c19d6-401">Valor de Produção</span><span class="sxs-lookup"><span data-stu-id="c19d6-401">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="c19d6-402">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="c19d6-402">Logging:LogLevel:System</span></span>    | <span data-ttu-id="c19d6-403">Informações</span><span class="sxs-lookup"><span data-stu-id="c19d6-403">Information</span></span>       | <span data-ttu-id="c19d6-404">Informações</span><span class="sxs-lookup"><span data-stu-id="c19d6-404">Information</span></span>      |
| <span data-ttu-id="c19d6-405">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="c19d6-405">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="c19d6-406">Informações</span><span class="sxs-lookup"><span data-stu-id="c19d6-406">Information</span></span>       | <span data-ttu-id="c19d6-407">Informações</span><span class="sxs-lookup"><span data-stu-id="c19d6-407">Information</span></span>      |
| <span data-ttu-id="c19d6-408">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="c19d6-408">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="c19d6-409">Depurar</span><span class="sxs-lookup"><span data-stu-id="c19d6-409">Debug</span></span>             | <span data-ttu-id="c19d6-410">Erro</span><span class="sxs-lookup"><span data-stu-id="c19d6-410">Error</span></span>            |
| <span data-ttu-id="c19d6-411">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="c19d6-411">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="c19d6-412">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="c19d6-412">XML Configuration Provider</span></span>

<span data-ttu-id="c19d6-413">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c19d6-413">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="c19d6-414">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-414">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c19d6-415">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="c19d6-415">Overloads permit specifying:</span></span>

* <span data-ttu-id="c19d6-416">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="c19d6-416">Whether the file is optional.</span></span>
* <span data-ttu-id="c19d6-417">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-417">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="c19d6-418">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-418">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="c19d6-419">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="c19d6-419">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="c19d6-420">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-420">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="c19d6-421">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c19d6-421">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="c19d6-422">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-422">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="c19d6-423">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-423">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-424">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-424">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="c19d6-425">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-425">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="c19d6-426">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-426">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-427">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="c19d6-427">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="c19d6-428">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="c19d6-428">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c19d6-429">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-429">section0:key0</span></span>
* <span data-ttu-id="c19d6-430">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-430">section0:key1</span></span>
* <span data-ttu-id="c19d6-431">section1:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-431">section1:key0</span></span>
* <span data-ttu-id="c19d6-432">section1:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-432">section1:key1</span></span>

<span data-ttu-id="c19d6-433">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="c19d6-433">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="c19d6-434">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="c19d6-434">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c19d6-435">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-435">section:section0:key:key0</span></span>
* <span data-ttu-id="c19d6-436">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-436">section:section0:key:key1</span></span>
* <span data-ttu-id="c19d6-437">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-437">section:section1:key:key0</span></span>
* <span data-ttu-id="c19d6-438">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-438">section:section1:key:key1</span></span>

<span data-ttu-id="c19d6-439">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="c19d6-439">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="c19d6-440">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="c19d6-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c19d6-441">key:attribute</span><span class="sxs-lookup"><span data-stu-id="c19d6-441">key:attribute</span></span>
* <span data-ttu-id="c19d6-442">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="c19d6-442">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="c19d6-443">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="c19d6-443">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="c19d6-444">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-444">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="c19d6-445">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-445">The key is the file name.</span></span> <span data-ttu-id="c19d6-446">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-446">The value contains the file's contents.</span></span> <span data-ttu-id="c19d6-447">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="c19d6-447">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="c19d6-448">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-448">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="c19d6-449">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="c19d6-449">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="c19d6-450">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="c19d6-450">Overloads permit specifying:</span></span>

* <span data-ttu-id="c19d6-451">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="c19d6-451">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="c19d6-452">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="c19d6-452">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="c19d6-453">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-453">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="c19d6-454">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-454">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="c19d6-455">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c19d6-455">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="c19d6-456">O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-456">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="c19d6-457">`SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-457">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-458">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-458">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

## <a name="memory-configuration-provider"></a><span data-ttu-id="c19d6-459">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="c19d6-459">Memory Configuration Provider</span></span>

<span data-ttu-id="c19d6-460">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="c19d6-461">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c19d6-462">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="c19d6-463">Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c19d6-463">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="c19d6-464">Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-464">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

## <a name="getvalue"></a><span data-ttu-id="c19d6-465">GetValue</span><span class="sxs-lookup"><span data-stu-id="c19d6-465">GetValue</span></span>

<span data-ttu-id="c19d6-466">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um valor de configuração com uma chave especificada e o converte para o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-466">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="c19d6-467">Uma sobrecarga permite que você forneça um valor padrão se a chave não for encontrada.</span><span class="sxs-lookup"><span data-stu-id="c19d6-467">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="c19d6-468">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="c19d6-468">The following example:</span></span>

* <span data-ttu-id="c19d6-469">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-469">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="c19d6-470">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-470">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="c19d6-471">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-471">Types the value as an `int`.</span></span>
* <span data-ttu-id="c19d6-472">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="c19d6-472">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
// using Microsoft.Extensions.Configuration;

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="c19d6-473">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="c19d6-473">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="c19d6-474">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="c19d6-474">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="c19d6-475">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="c19d6-475">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="c19d6-476">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-476">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="c19d6-477">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-477">section0:key0</span></span>
* <span data-ttu-id="c19d6-478">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-478">section0:key1</span></span>
* <span data-ttu-id="c19d6-479">section1:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-479">section1:key0</span></span>
* <span data-ttu-id="c19d6-480">section1:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-480">section1:key1</span></span>
* <span data-ttu-id="c19d6-481">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-481">section2:subsection0:key0</span></span>
* <span data-ttu-id="c19d6-482">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-482">section2:subsection0:key1</span></span>
* <span data-ttu-id="c19d6-483">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="c19d6-483">section2:subsection1:key0</span></span>
* <span data-ttu-id="c19d6-484">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="c19d6-484">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="c19d6-485">GetSection</span><span class="sxs-lookup"><span data-stu-id="c19d6-485">GetSection</span></span>

<span data-ttu-id="c19d6-486">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="c19d6-486">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span> <span data-ttu-id="c19d6-487">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-487">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-488">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="c19d6-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="c19d6-489">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="c19d6-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="c19d6-490">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="c19d6-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="c19d6-491">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="c19d6-492">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="c19d6-493">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="c19d6-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="c19d6-494"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="c19d6-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="c19d6-495">GetChildren</span><span class="sxs-lookup"><span data-stu-id="c19d6-495">GetChildren</span></span>

<span data-ttu-id="c19d6-496">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="c19d6-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="c19d6-497">Exists</span><span class="sxs-lookup"><span data-stu-id="c19d6-497">Exists</span></span>

<span data-ttu-id="c19d6-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="c19d6-499">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="c19d6-500">Associar a uma classe</span><span class="sxs-lookup"><span data-stu-id="c19d6-500">Bind to a class</span></span>

<span data-ttu-id="c19d6-501">A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*.</span><span class="sxs-lookup"><span data-stu-id="c19d6-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="c19d6-502">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="c19d6-503">Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="c19d6-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="c19d6-504">`Bind` está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-504">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-505">O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="c19d6-505">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="c19d6-506">A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-506">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="c19d6-507">Os seguintes pares chave-valor de configuração são criados:</span><span class="sxs-lookup"><span data-stu-id="c19d6-507">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="c19d6-508">Chave</span><span class="sxs-lookup"><span data-stu-id="c19d6-508">Key</span></span>                   | <span data-ttu-id="c19d6-509">Valor</span><span class="sxs-lookup"><span data-stu-id="c19d6-509">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="c19d6-510">starship:name</span><span class="sxs-lookup"><span data-stu-id="c19d6-510">starship:name</span></span>         | <span data-ttu-id="c19d6-511">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="c19d6-511">USS Enterprise</span></span>                                    |
| <span data-ttu-id="c19d6-512">starship:registry</span><span class="sxs-lookup"><span data-stu-id="c19d6-512">starship:registry</span></span>     | <span data-ttu-id="c19d6-513">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="c19d6-513">NCC-1701</span></span>                                          |
| <span data-ttu-id="c19d6-514">starship:class</span><span class="sxs-lookup"><span data-stu-id="c19d6-514">starship:class</span></span>        | <span data-ttu-id="c19d6-515">Constituição</span><span class="sxs-lookup"><span data-stu-id="c19d6-515">Constitution</span></span>                                      |
| <span data-ttu-id="c19d6-516">starship:length</span><span class="sxs-lookup"><span data-stu-id="c19d6-516">starship:length</span></span>       | <span data-ttu-id="c19d6-517">304,8</span><span class="sxs-lookup"><span data-stu-id="c19d6-517">304.8</span></span>                                             |
| <span data-ttu-id="c19d6-518">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="c19d6-518">starship:commissioned</span></span> | <span data-ttu-id="c19d6-519">False</span><span class="sxs-lookup"><span data-stu-id="c19d6-519">False</span></span>                                             |
| <span data-ttu-id="c19d6-520">marca</span><span class="sxs-lookup"><span data-stu-id="c19d6-520">trademark</span></span>             | <span data-ttu-id="c19d6-521">Paramount Pictures Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="c19d6-521">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="c19d6-522">O aplicativo de exemplo chama `GetSection` com a chave `starship`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-522">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="c19d6-523">Os pares chave-valor `starship` são isolados.</span><span class="sxs-lookup"><span data-stu-id="c19d6-523">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="c19d6-524">O método `Bind` é chamado na subseção passando uma instância da classe `Starship`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-524">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="c19d6-525">Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="c19d6-525">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

<span data-ttu-id="c19d6-526">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-526">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="c19d6-527">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="c19d6-527">Bind to an object graph</span></span>

<span data-ttu-id="c19d6-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="c19d6-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="c19d6-529">`Bind` está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-529">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-530">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="c19d6-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="c19d6-531">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="c19d6-532">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="c19d6-533">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="c19d6-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="c19d6-534">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-534">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="c19d6-535">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="c19d6-536">O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:</span><span class="sxs-lookup"><span data-stu-id="c19d6-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

<span data-ttu-id="c19d6-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="c19d6-538">`Get<T>` está disponível no ASP.NET Core 1.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="c19d6-538">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c19d6-539">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-539">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="c19d6-540">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="c19d6-540">Bind an array to a class</span></span>

<span data-ttu-id="c19d6-541">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="c19d6-541">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="c19d6-542">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-542">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="c19d6-543">Qualquer formato de matriz que exponha um segmento de chave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar matrizes a uma matriz de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="c19d6-543">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span> <span data-ttu-id="c19d6-544">\`Bind\`\` está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-544">\`Bind\`\` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

> [!NOTE]
> <span data-ttu-id="c19d6-545">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="c19d6-545">Binding is provided by convention.</span></span> <span data-ttu-id="c19d6-546">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="c19d6-546">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="c19d6-547">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="c19d6-547">**In-memory array processing**</span></span>

<span data-ttu-id="c19d6-548">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="c19d6-548">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="c19d6-549">Chave</span><span class="sxs-lookup"><span data-stu-id="c19d6-549">Key</span></span>             | <span data-ttu-id="c19d6-550">Valor</span><span class="sxs-lookup"><span data-stu-id="c19d6-550">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="c19d6-551">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="c19d6-551">array:entries:0</span></span> | <span data-ttu-id="c19d6-552">value0</span><span class="sxs-lookup"><span data-stu-id="c19d6-552">value0</span></span> |
| <span data-ttu-id="c19d6-553">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="c19d6-553">array:entries:1</span></span> | <span data-ttu-id="c19d6-554">value1</span><span class="sxs-lookup"><span data-stu-id="c19d6-554">value1</span></span> |
| <span data-ttu-id="c19d6-555">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="c19d6-555">array:entries:2</span></span> | <span data-ttu-id="c19d6-556">value2</span><span class="sxs-lookup"><span data-stu-id="c19d6-556">value2</span></span> |
| <span data-ttu-id="c19d6-557">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="c19d6-557">array:entries:4</span></span> | <span data-ttu-id="c19d6-558">value4</span><span class="sxs-lookup"><span data-stu-id="c19d6-558">value4</span></span> |
| <span data-ttu-id="c19d6-559">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="c19d6-559">array:entries:5</span></span> | <span data-ttu-id="c19d6-560">value5</span><span class="sxs-lookup"><span data-stu-id="c19d6-560">value5</span></span> |

<span data-ttu-id="c19d6-561">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="c19d6-561">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=3-10,22)]

<span data-ttu-id="c19d6-562">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="c19d6-562">The array skips a value for index &num;3.</span></span> <span data-ttu-id="c19d6-563">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="c19d6-563">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="c19d6-564">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="c19d6-564">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="c19d6-565">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="c19d6-565">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="c19d6-566">`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c19d6-566">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c19d6-567">A sintaxe [ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="c19d6-567">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="c19d6-568">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-568">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="c19d6-569">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="c19d6-569">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="c19d6-570">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="c19d6-570">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="c19d6-571">0</span><span class="sxs-lookup"><span data-stu-id="c19d6-571">0</span></span>                            | <span data-ttu-id="c19d6-572">value0</span><span class="sxs-lookup"><span data-stu-id="c19d6-572">value0</span></span>                       |
| <span data-ttu-id="c19d6-573">1</span><span class="sxs-lookup"><span data-stu-id="c19d6-573">1</span></span>                            | <span data-ttu-id="c19d6-574">value1</span><span class="sxs-lookup"><span data-stu-id="c19d6-574">value1</span></span>                       |
| <span data-ttu-id="c19d6-575">2</span><span class="sxs-lookup"><span data-stu-id="c19d6-575">2</span></span>                            | <span data-ttu-id="c19d6-576">value2</span><span class="sxs-lookup"><span data-stu-id="c19d6-576">value2</span></span>                       |
| <span data-ttu-id="c19d6-577">3</span><span class="sxs-lookup"><span data-stu-id="c19d6-577">3</span></span>                            | <span data-ttu-id="c19d6-578">value4</span><span class="sxs-lookup"><span data-stu-id="c19d6-578">value4</span></span>                       |
| <span data-ttu-id="c19d6-579">4</span><span class="sxs-lookup"><span data-stu-id="c19d6-579">4</span></span>                            | <span data-ttu-id="c19d6-580">value5</span><span class="sxs-lookup"><span data-stu-id="c19d6-580">value5</span></span>                       |

<span data-ttu-id="c19d6-581">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-581">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="c19d6-582">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="c19d6-582">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="c19d6-583">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="c19d6-583">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="c19d6-584">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-584">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="c19d6-585">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="c19d6-585">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="c19d6-586">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="c19d6-586">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="c19d6-587">No <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="c19d6-587">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="c19d6-588">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-588">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="c19d6-589">Chave</span><span class="sxs-lookup"><span data-stu-id="c19d6-589">Key</span></span>             | <span data-ttu-id="c19d6-590">Valor</span><span class="sxs-lookup"><span data-stu-id="c19d6-590">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="c19d6-591">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="c19d6-591">array:entries:3</span></span> | <span data-ttu-id="c19d6-592">value3</span><span class="sxs-lookup"><span data-stu-id="c19d6-592">value3</span></span> |

<span data-ttu-id="c19d6-593">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="c19d6-593">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="c19d6-594">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="c19d6-594">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="c19d6-595">Valor `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="c19d6-595">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="c19d6-596">0</span><span class="sxs-lookup"><span data-stu-id="c19d6-596">0</span></span>                            | <span data-ttu-id="c19d6-597">value0</span><span class="sxs-lookup"><span data-stu-id="c19d6-597">value0</span></span>                       |
| <span data-ttu-id="c19d6-598">1</span><span class="sxs-lookup"><span data-stu-id="c19d6-598">1</span></span>                            | <span data-ttu-id="c19d6-599">value1</span><span class="sxs-lookup"><span data-stu-id="c19d6-599">value1</span></span>                       |
| <span data-ttu-id="c19d6-600">2</span><span class="sxs-lookup"><span data-stu-id="c19d6-600">2</span></span>                            | <span data-ttu-id="c19d6-601">value2</span><span class="sxs-lookup"><span data-stu-id="c19d6-601">value2</span></span>                       |
| <span data-ttu-id="c19d6-602">3</span><span class="sxs-lookup"><span data-stu-id="c19d6-602">3</span></span>                            | <span data-ttu-id="c19d6-603">value3</span><span class="sxs-lookup"><span data-stu-id="c19d6-603">value3</span></span>                       |
| <span data-ttu-id="c19d6-604">4</span><span class="sxs-lookup"><span data-stu-id="c19d6-604">4</span></span>                            | <span data-ttu-id="c19d6-605">value4</span><span class="sxs-lookup"><span data-stu-id="c19d6-605">value4</span></span>                       |
| <span data-ttu-id="c19d6-606">5</span><span class="sxs-lookup"><span data-stu-id="c19d6-606">5</span></span>                            | <span data-ttu-id="c19d6-607">value5</span><span class="sxs-lookup"><span data-stu-id="c19d6-607">value5</span></span>                       |

<span data-ttu-id="c19d6-608">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="c19d6-608">**JSON array processing**</span></span>

<span data-ttu-id="c19d6-609">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="c19d6-609">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="c19d6-610">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="c19d6-610">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="c19d6-611">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="c19d6-611">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="c19d6-612">Chave</span><span class="sxs-lookup"><span data-stu-id="c19d6-612">Key</span></span>                     | <span data-ttu-id="c19d6-613">Valor</span><span class="sxs-lookup"><span data-stu-id="c19d6-613">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="c19d6-614">json_array:key</span><span class="sxs-lookup"><span data-stu-id="c19d6-614">json_array:key</span></span>          | <span data-ttu-id="c19d6-615">valueA</span><span class="sxs-lookup"><span data-stu-id="c19d6-615">valueA</span></span> |
| <span data-ttu-id="c19d6-616">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="c19d6-616">json_array:subsection:0</span></span> | <span data-ttu-id="c19d6-617">valueB</span><span class="sxs-lookup"><span data-stu-id="c19d6-617">valueB</span></span> |
| <span data-ttu-id="c19d6-618">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="c19d6-618">json_array:subsection:1</span></span> | <span data-ttu-id="c19d6-619">valueC</span><span class="sxs-lookup"><span data-stu-id="c19d6-619">valueC</span></span> |
| <span data-ttu-id="c19d6-620">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="c19d6-620">json_array:subsection:2</span></span> | <span data-ttu-id="c19d6-621">valueD</span><span class="sxs-lookup"><span data-stu-id="c19d6-621">valueD</span></span> |

<span data-ttu-id="c19d6-622">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="c19d6-622">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="c19d6-623">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-623">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="c19d6-624">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-624">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="c19d6-625">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="c19d6-625">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="c19d6-626">Valor `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="c19d6-626">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="c19d6-627">0</span><span class="sxs-lookup"><span data-stu-id="c19d6-627">0</span></span>                                   | <span data-ttu-id="c19d6-628">valueB</span><span class="sxs-lookup"><span data-stu-id="c19d6-628">valueB</span></span>                              |
| <span data-ttu-id="c19d6-629">1</span><span class="sxs-lookup"><span data-stu-id="c19d6-629">1</span></span>                                   | <span data-ttu-id="c19d6-630">valueC</span><span class="sxs-lookup"><span data-stu-id="c19d6-630">valueC</span></span>                              |
| <span data-ttu-id="c19d6-631">2</span><span class="sxs-lookup"><span data-stu-id="c19d6-631">2</span></span>                                   | <span data-ttu-id="c19d6-632">valueD</span><span class="sxs-lookup"><span data-stu-id="c19d6-632">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="c19d6-633">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="c19d6-633">Custom configuration provider</span></span>

<span data-ttu-id="c19d6-634">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="c19d6-634">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="c19d6-635">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="c19d6-635">The provider has the following characteristics:</span></span>

* <span data-ttu-id="c19d6-636">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-636">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="c19d6-637">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="c19d6-637">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="c19d6-638">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="c19d6-638">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="c19d6-639">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="c19d6-639">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="c19d6-640">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-640">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="c19d6-641">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c19d6-641">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="c19d6-642">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="c19d6-642">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="c19d6-643">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="c19d6-643">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="c19d6-644">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="c19d6-644">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="c19d6-645">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-645">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="c19d6-646">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="c19d6-646">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="c19d6-647">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-647">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="c19d6-648">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="c19d6-648">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="c19d6-649">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="c19d6-649">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="c19d6-650">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-650">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="c19d6-651">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="c19d6-651">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="c19d6-652">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c19d6-652">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=26)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="c19d6-653">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="c19d6-653">Access configuration during startup</span></span>

<span data-ttu-id="c19d6-654">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c19d6-654">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c19d6-655">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="c19d6-655">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="c19d6-656">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, confira [Inicialização do aplicativo: Métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="c19d6-656">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="c19d6-657">Acessar a configuração em uma página do Razor Pages ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="c19d6-657">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="c19d6-658">Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.</span><span class="sxs-lookup"><span data-stu-id="c19d6-658">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="c19d6-659">Em uma página do Razor:</span><span class="sxs-lookup"><span data-stu-id="c19d6-659">In a Razor Pages page:</span></span>

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

<span data-ttu-id="c19d6-660">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="c19d6-660">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="c19d6-661">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="c19d6-661">Add configuration from an external assembly</span></span>

<span data-ttu-id="c19d6-662">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c19d6-662">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="c19d6-663">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c19d6-663">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c19d6-664">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c19d6-664">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* [<span data-ttu-id="c19d6-665">Detalhes da configuração da Microsoft</span><span class="sxs-lookup"><span data-stu-id="c19d6-665">Deep Dive into Microsoft Configuration</span></span>](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)
