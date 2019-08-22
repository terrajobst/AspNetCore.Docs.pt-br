---
title: Padrão de opções no ASP.NET Core
author: guardrex
description: Descubra como usar o padrão de opções para representar grupos de configurações relacionadas em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 22dde4b05ea20fedb696c6a4b144755a957e8c0d
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886392"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="b0298-103">Padrão de opções no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0298-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="b0298-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b0298-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0298-105">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="b0298-106">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="b0298-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="b0298-107">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="b0298-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="b0298-108">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="b0298-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="b0298-109">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="b0298-110">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="b0298-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="b0298-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0298-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b0298-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="b0298-112">Prerequisites</span></span>

<span data-ttu-id="b0298-113">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="b0298-113">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="b0298-114">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="b0298-114">Options interfaces</span></span>

<span data-ttu-id="b0298-115">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="b0298-116">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="b0298-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="b0298-117">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="b0298-117">Change notifications</span></span>
* [<span data-ttu-id="b0298-118">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="b0298-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="b0298-119">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="b0298-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="b0298-120">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="b0298-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="b0298-121">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="b0298-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="b0298-122">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="b0298-123">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="b0298-124">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="b0298-125">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="b0298-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="b0298-126">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="b0298-127">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="b0298-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="b0298-128">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="b0298-129">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="b0298-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="b0298-130">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0298-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="b0298-131">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="b0298-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="b0298-132">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="b0298-133">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="b0298-134">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="b0298-135">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="b0298-135">General options configuration</span></span>

<span data-ttu-id="b0298-136">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="b0298-137">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="b0298-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="b0298-138">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="b0298-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="b0298-139">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="b0298-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="b0298-140">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="b0298-141">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="b0298-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="b0298-142">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="b0298-143">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="b0298-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="b0298-144">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="b0298-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="b0298-145">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="b0298-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="b0298-146">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="b0298-147">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="b0298-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="b0298-148">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="b0298-149">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-149">Use a delegate to set options values.</span></span> <span data-ttu-id="b0298-150">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="b0298-151">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b0298-152">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="b0298-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="b0298-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0298-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="b0298-154">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="b0298-155">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="b0298-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="b0298-156">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b0298-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="b0298-157">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="b0298-158">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="b0298-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="b0298-159">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="b0298-160">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="b0298-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="b0298-161">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="b0298-161">Suboptions configuration</span></span>

<span data-ttu-id="b0298-162">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="b0298-163">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0298-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="b0298-164">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="b0298-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="b0298-165">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração do formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="b0298-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="b0298-166">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0298-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="b0298-167">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b0298-168">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b0298-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="b0298-169">O método de extensão `GetSection` exige o pacote NuGet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="b0298-169">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="b0298-170">Se o aplicativo usa o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), o pacote é automaticamente incluído.</span><span class="sxs-lookup"><span data-stu-id="b0298-170">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="b0298-171">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="b0298-171">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="b0298-172">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-172">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="b0298-173">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-173">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="b0298-174">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="b0298-174">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="b0298-175">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="b0298-175">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="b0298-176">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-176">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="b0298-177">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-177">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="b0298-178">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="b0298-178">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="b0298-179">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="b0298-179">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="b0298-181">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="b0298-181">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="b0298-182">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-182">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="b0298-183">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="b0298-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="b0298-184">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0298-184">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="b0298-185">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="b0298-185">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="b0298-186">Várias solicitações ao servidor retornam valores de constante fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="b0298-186">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="b0298-187">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b0298-187">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="b0298-188">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="b0298-188">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="b0298-189">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0298-189">Save the *appsettings.json* file.</span></span> <span data-ttu-id="b0298-190">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="b0298-190">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="b0298-191">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="b0298-191">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="b0298-192">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-192">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="b0298-193">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-193">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="b0298-194">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="b0298-194">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="b0298-195">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-195">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="b0298-196">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="b0298-196">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="b0298-197">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0298-197">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="b0298-198">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="b0298-198">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="b0298-199">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="b0298-199">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="b0298-200">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-200">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="b0298-201">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="b0298-201">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="b0298-202">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-202">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="b0298-203">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="b0298-203">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="b0298-204">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0298-204">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="b0298-205">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="b0298-205">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="b0298-206">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-206">All options are named instances.</span></span> <span data-ttu-id="b0298-207">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="b0298-207">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="b0298-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="b0298-209">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="b0298-209">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="b0298-210">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="b0298-210">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="b0298-211">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="b0298-211">OptionsBuilder API</span></span>

<span data-ttu-id="b0298-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="b0298-213">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="b0298-213">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="b0298-214">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0298-214">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="b0298-215">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="b0298-215">Use DI services to configure options</span></span>

<span data-ttu-id="b0298-216">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="b0298-216">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="b0298-217">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="b0298-217">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="b0298-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="b0298-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="b0298-219">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-219">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="b0298-220">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="b0298-220">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="b0298-221">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="b0298-221">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="b0298-222">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="b0298-222">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="b0298-223">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="b0298-223">Options validation</span></span>

<span data-ttu-id="b0298-224">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="b0298-224">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="b0298-225">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="b0298-225">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="b0298-226">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="b0298-226">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="b0298-227">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="b0298-227">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="b0298-228">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-228">Validation runs when the options instance is created.</span></span> <span data-ttu-id="b0298-229">A instância de opções tem garantia de passar pela validação, na primeira vez em que for acessada.</span><span class="sxs-lookup"><span data-stu-id="b0298-229">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b0298-230">A validação não protege contra modificações de opções, depois que as opções são inicialmente configuradas e validadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-230">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="b0298-231">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="b0298-231">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="b0298-232">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="b0298-232">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="b0298-233">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="b0298-233">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="b0298-234">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="b0298-234">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="b0298-235">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="b0298-235">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="b0298-236">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-236">A specific named options instance.</span></span>
* <span data-ttu-id="b0298-237">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="b0298-237">All options when `name` is `null`.</span></span>

<span data-ttu-id="b0298-238">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="b0298-238">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="b0298-239">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="b0298-239">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="b0298-240">O `Microsoft.Extensions.Options.DataAnnotations` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="b0298-240">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="b0298-241">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="b0298-241">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="b0298-242">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="b0298-242">Options post-configuration</span></span>

<span data-ttu-id="b0298-243">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-243">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="b0298-244">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="b0298-244">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b0298-245">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="b0298-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b0298-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="b0298-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="b0298-247">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="b0298-247">Accessing options during startup</span></span>

<span data-ttu-id="b0298-248"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="b0298-248"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="b0298-249">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b0298-249">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b0298-250">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-250">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b0298-251">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-251">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="b0298-252">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="b0298-252">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="b0298-253">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="b0298-253">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="b0298-254">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="b0298-254">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="b0298-255">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-255">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="b0298-256">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="b0298-256">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="b0298-257">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0298-257">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b0298-258">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="b0298-258">Prerequisites</span></span>

<span data-ttu-id="b0298-259">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="b0298-259">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="b0298-260">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="b0298-260">Options interfaces</span></span>

<span data-ttu-id="b0298-261">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="b0298-262">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="b0298-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="b0298-263">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="b0298-263">Change notifications</span></span>
* [<span data-ttu-id="b0298-264">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="b0298-264">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="b0298-265">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="b0298-265">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="b0298-266">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="b0298-266">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="b0298-267">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="b0298-267">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="b0298-268">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="b0298-269">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-269">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="b0298-270">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-270">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="b0298-271">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="b0298-271">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="b0298-272">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="b0298-273">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="b0298-273">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="b0298-274">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-274">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="b0298-275">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="b0298-275">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="b0298-276">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0298-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="b0298-277">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="b0298-277">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="b0298-278">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-278"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="b0298-279">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-279">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="b0298-280">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-280">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="b0298-281">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="b0298-281">General options configuration</span></span>

<span data-ttu-id="b0298-282">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-282">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="b0298-283">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="b0298-283">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="b0298-284">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="b0298-284">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="b0298-285">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="b0298-285">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="b0298-286">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-286">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="b0298-287">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="b0298-287">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="b0298-288">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-288">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="b0298-289">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="b0298-289">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="b0298-290">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="b0298-290">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="b0298-291">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="b0298-291">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="b0298-292">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-292">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="b0298-293">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="b0298-293">Configure simple options with a delegate</span></span>

<span data-ttu-id="b0298-294">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-294">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="b0298-295">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-295">Use a delegate to set options values.</span></span> <span data-ttu-id="b0298-296">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-296">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="b0298-297">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-297">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b0298-298">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="b0298-298">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="b0298-299">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0298-299">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="b0298-300">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-300">You can add multiple configuration providers.</span></span> <span data-ttu-id="b0298-301">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="b0298-301">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="b0298-302">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b0298-302">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="b0298-303">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-303">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="b0298-304">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="b0298-304">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="b0298-305">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-305">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="b0298-306">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="b0298-306">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="b0298-307">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="b0298-307">Suboptions configuration</span></span>

<span data-ttu-id="b0298-308">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-308">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="b0298-309">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0298-309">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="b0298-310">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="b0298-310">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="b0298-311">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração do formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="b0298-311">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="b0298-312">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0298-312">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="b0298-313">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-313">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b0298-314">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b0298-314">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="b0298-315">O método de extensão `GetSection` exige o pacote NuGet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="b0298-315">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="b0298-316">Se o aplicativo usa o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), o pacote é automaticamente incluído.</span><span class="sxs-lookup"><span data-stu-id="b0298-316">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="b0298-317">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="b0298-317">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="b0298-318">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-318">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="b0298-319">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-319">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="b0298-320">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="b0298-320">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="b0298-321">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="b0298-321">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="b0298-322">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-322">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="b0298-323">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-323">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="b0298-324">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="b0298-324">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="b0298-325">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="b0298-325">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="b0298-327">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="b0298-327">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="b0298-328">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-328">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="b0298-329">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="b0298-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="b0298-330">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0298-330">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="b0298-331">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="b0298-331">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="b0298-332">Várias solicitações ao servidor retornam valores de constante fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="b0298-332">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="b0298-333">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b0298-333">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="b0298-334">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="b0298-334">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="b0298-335">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0298-335">Save the *appsettings.json* file.</span></span> <span data-ttu-id="b0298-336">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="b0298-336">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="b0298-337">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="b0298-337">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="b0298-338">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-338">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="b0298-339">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-339">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="b0298-340">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="b0298-340">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="b0298-341">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-341">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="b0298-342">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="b0298-342">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="b0298-343">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0298-343">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="b0298-344">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="b0298-344">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="b0298-345">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="b0298-345">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="b0298-346">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-346">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="b0298-347">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="b0298-347">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="b0298-348">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-348">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="b0298-349">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="b0298-349">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="b0298-350">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0298-350">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="b0298-351">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="b0298-351">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="b0298-352">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-352">All options are named instances.</span></span> <span data-ttu-id="b0298-353">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="b0298-353">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="b0298-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="b0298-355">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="b0298-355">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="b0298-356">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="b0298-356">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="b0298-357">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="b0298-357">OptionsBuilder API</span></span>

<span data-ttu-id="b0298-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="b0298-359">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="b0298-359">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="b0298-360">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0298-360">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="b0298-361">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="b0298-361">Use DI services to configure options</span></span>

<span data-ttu-id="b0298-362">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="b0298-362">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="b0298-363">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="b0298-363">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="b0298-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="b0298-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="b0298-365">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-365">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="b0298-366">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="b0298-366">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="b0298-367">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="b0298-367">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="b0298-368">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="b0298-368">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="b0298-369">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="b0298-369">Options validation</span></span>

<span data-ttu-id="b0298-370">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="b0298-370">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="b0298-371">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="b0298-371">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="b0298-372">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="b0298-372">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="b0298-373">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="b0298-373">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="b0298-374">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-374">Validation runs when the options instance is created.</span></span> <span data-ttu-id="b0298-375">A instância de opções tem garantia de passar pela validação, na primeira vez em que for acessada.</span><span class="sxs-lookup"><span data-stu-id="b0298-375">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b0298-376">A validação não protege contra modificações de opções, depois que as opções são inicialmente configuradas e validadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-376">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="b0298-377">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="b0298-377">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="b0298-378">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="b0298-378">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="b0298-379">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="b0298-379">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="b0298-380">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="b0298-380">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="b0298-381">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="b0298-381">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="b0298-382">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-382">A specific named options instance.</span></span>
* <span data-ttu-id="b0298-383">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="b0298-383">All options when `name` is `null`.</span></span>

<span data-ttu-id="b0298-384">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="b0298-384">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="b0298-385">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="b0298-385">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="b0298-386">O `Microsoft.Extensions.Options.DataAnnotations` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="b0298-386">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="b0298-387">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="b0298-387">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="b0298-388">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="b0298-388">Options post-configuration</span></span>

<span data-ttu-id="b0298-389">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-389">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="b0298-390">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="b0298-390">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b0298-391">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="b0298-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b0298-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="b0298-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="b0298-393">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="b0298-393">Accessing options during startup</span></span>

<span data-ttu-id="b0298-394"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="b0298-394"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="b0298-395">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b0298-395">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b0298-396">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-396">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b0298-397">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-397">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="b0298-398">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="b0298-398">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="b0298-399">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="b0298-399">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="b0298-400">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="b0298-400">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="b0298-401">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-401">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="b0298-402">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="b0298-402">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="b0298-403">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0298-403">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b0298-404">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="b0298-404">Prerequisites</span></span>

<span data-ttu-id="b0298-405">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="b0298-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="b0298-406">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="b0298-406">Options interfaces</span></span>

<span data-ttu-id="b0298-407">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="b0298-408">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="b0298-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="b0298-409">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="b0298-409">Change notifications</span></span>
* [<span data-ttu-id="b0298-410">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="b0298-410">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="b0298-411">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="b0298-411">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="b0298-412">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="b0298-412">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="b0298-413">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="b0298-413">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="b0298-414">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="b0298-415">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-415">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="b0298-416">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-416">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="b0298-417">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="b0298-417">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="b0298-418">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="b0298-419">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="b0298-419">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="b0298-420">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-420">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="b0298-421">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="b0298-421">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="b0298-422">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0298-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="b0298-423">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="b0298-423">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="b0298-424">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-424"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="b0298-425">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-425">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="b0298-426">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-426">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="b0298-427">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="b0298-427">General options configuration</span></span>

<span data-ttu-id="b0298-428">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-428">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="b0298-429">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="b0298-429">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="b0298-430">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="b0298-430">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="b0298-431">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="b0298-431">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="b0298-432">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-432">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="b0298-433">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="b0298-433">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="b0298-434">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-434">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="b0298-435">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="b0298-435">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="b0298-436">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="b0298-436">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="b0298-437">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="b0298-437">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="b0298-438">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-438">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="b0298-439">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="b0298-439">Configure simple options with a delegate</span></span>

<span data-ttu-id="b0298-440">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-440">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="b0298-441">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="b0298-441">Use a delegate to set options values.</span></span> <span data-ttu-id="b0298-442">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-442">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="b0298-443">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-443">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b0298-444">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="b0298-444">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="b0298-445">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0298-445">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="b0298-446">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-446">You can add multiple configuration providers.</span></span> <span data-ttu-id="b0298-447">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="b0298-447">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="b0298-448">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b0298-448">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="b0298-449">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-449">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="b0298-450">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="b0298-450">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="b0298-451">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0298-451">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="b0298-452">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="b0298-452">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="b0298-453">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="b0298-453">Suboptions configuration</span></span>

<span data-ttu-id="b0298-454">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-454">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="b0298-455">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0298-455">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="b0298-456">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="b0298-456">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="b0298-457">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração do formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="b0298-457">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="b0298-458">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0298-458">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="b0298-459">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-459">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b0298-460">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b0298-460">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="b0298-461">O método de extensão `GetSection` exige o pacote NuGet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="b0298-461">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="b0298-462">Se o aplicativo usa o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), o pacote é automaticamente incluído.</span><span class="sxs-lookup"><span data-stu-id="b0298-462">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="b0298-463">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="b0298-463">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="b0298-464">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-464">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="b0298-465">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-465">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="b0298-466">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="b0298-466">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="b0298-467">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="b0298-467">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="b0298-468">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-468">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="b0298-469">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-469">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="b0298-470">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="b0298-470">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="b0298-471">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="b0298-471">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="b0298-473">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="b0298-473">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="b0298-474">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-474">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="b0298-475">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="b0298-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="b0298-476">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0298-476">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="b0298-477">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="b0298-477">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="b0298-478">Várias solicitações ao servidor retornam valores de constante fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="b0298-478">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="b0298-479">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b0298-479">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="b0298-480">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="b0298-480">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="b0298-481">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0298-481">Save the *appsettings.json* file.</span></span> <span data-ttu-id="b0298-482">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="b0298-482">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="b0298-483">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="b0298-483">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="b0298-484">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="b0298-484">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="b0298-485">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-485">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="b0298-486">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="b0298-486">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="b0298-487">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b0298-487">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="b0298-488">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="b0298-488">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="b0298-489">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0298-489">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="b0298-490">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="b0298-490">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="b0298-491">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="b0298-491">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="b0298-492">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-492">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="b0298-493">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="b0298-493">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="b0298-494">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="b0298-494">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="b0298-495">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="b0298-495">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="b0298-496">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0298-496">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="b0298-497">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="b0298-497">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="b0298-498">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="b0298-498">All options are named instances.</span></span> <span data-ttu-id="b0298-499">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="b0298-499">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="b0298-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="b0298-501">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="b0298-501">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="b0298-502">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="b0298-502">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="b0298-503">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="b0298-503">OptionsBuilder API</span></span>

<span data-ttu-id="b0298-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="b0298-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="b0298-505">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="b0298-505">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="b0298-506">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0298-506">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="b0298-507">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="b0298-507">Use DI services to configure options</span></span>

<span data-ttu-id="b0298-508">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="b0298-508">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="b0298-509">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="b0298-509">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="b0298-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="b0298-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="b0298-511">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-511">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="b0298-512">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="b0298-512">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="b0298-513">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="b0298-513">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="b0298-514">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="b0298-514">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="b0298-515">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="b0298-515">Options post-configuration</span></span>

<span data-ttu-id="b0298-516">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="b0298-516">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="b0298-517">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="b0298-517">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b0298-518">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="b0298-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="b0298-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="b0298-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="b0298-520">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="b0298-520">Accessing options during startup</span></span>

<span data-ttu-id="b0298-521"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="b0298-521"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="b0298-522">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b0298-522">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b0298-523">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="b0298-523">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b0298-524">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b0298-524">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
