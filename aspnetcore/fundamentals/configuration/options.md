---
title: Padrão de opções no ASP.NET Core
author: guardrex
description: Descubra como usar o padrão de opções para representar grupos de configurações relacionadas em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 753afb9def90fd35122182f260f1dd0ce8d2830a
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288951"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="aa189-103">Padrão de opções no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa189-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="aa189-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aa189-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa189-105">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="aa189-106">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="aa189-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="aa189-107">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="aa189-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="aa189-108">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="aa189-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="aa189-109">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="aa189-110">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="aa189-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="aa189-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa189-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aa189-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="aa189-112">Prerequisites</span></span>

<span data-ttu-id="aa189-113">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="aa189-113">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="aa189-114">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="aa189-114">Options interfaces</span></span>

<span data-ttu-id="aa189-115">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="aa189-116">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="aa189-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="aa189-117">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="aa189-117">Change notifications</span></span>
* [<span data-ttu-id="aa189-118">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="aa189-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="aa189-119">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="aa189-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="aa189-120">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="aa189-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="aa189-121">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="aa189-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="aa189-122">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="aa189-123">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="aa189-124">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="aa189-125">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="aa189-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="aa189-126">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="aa189-127">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="aa189-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="aa189-128">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="aa189-129">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="aa189-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="aa189-130">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="aa189-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="aa189-131">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="aa189-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="aa189-132">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="aa189-133">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="aa189-134">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="aa189-135">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="aa189-135">General options configuration</span></span>

<span data-ttu-id="aa189-136">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="aa189-137">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="aa189-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="aa189-138">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="aa189-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="aa189-139">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="aa189-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="aa189-140">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="aa189-141">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="aa189-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="aa189-142">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="aa189-143">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="aa189-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="aa189-144">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="aa189-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="aa189-145">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="aa189-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="aa189-146">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="aa189-147">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="aa189-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="aa189-148">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="aa189-149">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-149">Use a delegate to set options values.</span></span> <span data-ttu-id="aa189-150">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="aa189-151">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="aa189-152">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="aa189-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="aa189-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="aa189-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="aa189-154">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="aa189-155">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="aa189-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="aa189-156">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="aa189-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="aa189-157">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="aa189-158">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="aa189-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="aa189-159">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="aa189-160">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="aa189-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="aa189-161">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="aa189-161">Suboptions configuration</span></span>

<span data-ttu-id="aa189-162">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="aa189-163">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aa189-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="aa189-164">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="aa189-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="aa189-165">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração do formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="aa189-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="aa189-166">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aa189-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="aa189-167">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="aa189-168">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="aa189-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="aa189-169">O método de extensão `GetSection` exige o pacote NuGet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="aa189-169">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="aa189-170">Se o aplicativo usa o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), o pacote é automaticamente incluído.</span><span class="sxs-lookup"><span data-stu-id="aa189-170">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="aa189-171">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="aa189-171">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="aa189-172">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-172">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="aa189-173">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-173">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="aa189-174">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="aa189-174">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="aa189-175">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="aa189-175">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="aa189-176">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-176">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="aa189-177">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-177">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="aa189-178">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="aa189-178">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="aa189-179">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="aa189-179">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="aa189-181">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="aa189-181">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="aa189-182">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-182">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="aa189-183">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="aa189-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="aa189-184">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="aa189-184">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="aa189-185">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="aa189-185">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="aa189-186">Várias solicitações ao servidor retornam valores de constante fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="aa189-186">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="aa189-187">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="aa189-187">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="aa189-188">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="aa189-188">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="aa189-189">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aa189-189">Save the *appsettings.json* file.</span></span> <span data-ttu-id="aa189-190">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="aa189-190">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="aa189-191">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="aa189-191">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="aa189-192">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-192">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="aa189-193">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-193">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="aa189-194">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="aa189-194">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="aa189-195">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-195">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="aa189-196">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="aa189-196">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="aa189-197">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aa189-197">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="aa189-198">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="aa189-198">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="aa189-199">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="aa189-199">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="aa189-200">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-200">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="aa189-201">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="aa189-201">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="aa189-202">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-202">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="aa189-203">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="aa189-203">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="aa189-204">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="aa189-204">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="aa189-205">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="aa189-205">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="aa189-206">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-206">All options are named instances.</span></span> <span data-ttu-id="aa189-207">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="aa189-207">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="aa189-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="aa189-209">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="aa189-209">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="aa189-210">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="aa189-210">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="aa189-211">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="aa189-211">OptionsBuilder API</span></span>

<span data-ttu-id="aa189-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="aa189-213">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="aa189-213">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="aa189-214">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="aa189-214">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="aa189-215">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="aa189-215">Use DI services to configure options</span></span>

<span data-ttu-id="aa189-216">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="aa189-216">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="aa189-217">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="aa189-217">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="aa189-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="aa189-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="aa189-219">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-219">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="aa189-220">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="aa189-220">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="aa189-221">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="aa189-221">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="aa189-222">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="aa189-222">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="aa189-223">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="aa189-223">Options validation</span></span>

<span data-ttu-id="aa189-224">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="aa189-224">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="aa189-225">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="aa189-225">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="aa189-226">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="aa189-226">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="aa189-227">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="aa189-227">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="aa189-228">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-228">Validation runs when the options instance is created.</span></span> <span data-ttu-id="aa189-229">A instância de opções tem garantia de passar pela validação, na primeira vez em que for acessada.</span><span class="sxs-lookup"><span data-stu-id="aa189-229">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aa189-230">A validação não protege contra modificações de opções, depois que as opções são inicialmente configuradas e validadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-230">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="aa189-231">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="aa189-231">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="aa189-232">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="aa189-232">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="aa189-233">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="aa189-233">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="aa189-234">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="aa189-234">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="aa189-235">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="aa189-235">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="aa189-236">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-236">A specific named options instance.</span></span>
* <span data-ttu-id="aa189-237">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="aa189-237">All options when `name` is `null`.</span></span>

<span data-ttu-id="aa189-238">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="aa189-238">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="aa189-239">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="aa189-239">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="aa189-240">O `Microsoft.Extensions.Options.DataAnnotations` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="aa189-240">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
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

<span data-ttu-id="aa189-241">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="aa189-241">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="aa189-242">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="aa189-242">Options post-configuration</span></span>

<span data-ttu-id="aa189-243">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-243">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="aa189-244">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="aa189-244">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="aa189-245">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="aa189-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="aa189-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="aa189-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="aa189-247">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="aa189-247">Accessing options during startup</span></span>

<span data-ttu-id="aa189-248"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="aa189-248"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="aa189-249">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="aa189-249">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aa189-250">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-250">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="aa189-251">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-251">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="aa189-252">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="aa189-252">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="aa189-253">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="aa189-253">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="aa189-254">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="aa189-254">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="aa189-255">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-255">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="aa189-256">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="aa189-256">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="aa189-257">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa189-257">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aa189-258">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="aa189-258">Prerequisites</span></span>

<span data-ttu-id="aa189-259">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="aa189-259">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="aa189-260">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="aa189-260">Options interfaces</span></span>

<span data-ttu-id="aa189-261">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="aa189-262">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="aa189-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="aa189-263">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="aa189-263">Change notifications</span></span>
* [<span data-ttu-id="aa189-264">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="aa189-264">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="aa189-265">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="aa189-265">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="aa189-266">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="aa189-266">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="aa189-267">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="aa189-267">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="aa189-268">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="aa189-269">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-269">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="aa189-270">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-270">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="aa189-271">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="aa189-271">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="aa189-272">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="aa189-273">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="aa189-273">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="aa189-274">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-274">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="aa189-275">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="aa189-275">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="aa189-276">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="aa189-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="aa189-277">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="aa189-277">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="aa189-278">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-278"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="aa189-279">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-279">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="aa189-280">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-280">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="aa189-281">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="aa189-281">General options configuration</span></span>

<span data-ttu-id="aa189-282">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-282">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="aa189-283">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="aa189-283">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="aa189-284">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="aa189-284">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="aa189-285">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="aa189-285">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="aa189-286">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-286">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="aa189-287">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="aa189-287">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="aa189-288">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-288">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="aa189-289">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="aa189-289">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="aa189-290">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="aa189-290">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="aa189-291">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="aa189-291">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="aa189-292">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-292">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="aa189-293">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="aa189-293">Configure simple options with a delegate</span></span>

<span data-ttu-id="aa189-294">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-294">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="aa189-295">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-295">Use a delegate to set options values.</span></span> <span data-ttu-id="aa189-296">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-296">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="aa189-297">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-297">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="aa189-298">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="aa189-298">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="aa189-299">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="aa189-299">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="aa189-300">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-300">You can add multiple configuration providers.</span></span> <span data-ttu-id="aa189-301">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="aa189-301">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="aa189-302">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="aa189-302">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="aa189-303">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-303">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="aa189-304">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="aa189-304">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="aa189-305">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-305">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="aa189-306">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="aa189-306">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="aa189-307">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="aa189-307">Suboptions configuration</span></span>

<span data-ttu-id="aa189-308">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-308">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="aa189-309">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aa189-309">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="aa189-310">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="aa189-310">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="aa189-311">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração do formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="aa189-311">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="aa189-312">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aa189-312">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="aa189-313">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-313">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="aa189-314">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="aa189-314">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="aa189-315">O método de extensão `GetSection` exige o pacote NuGet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="aa189-315">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="aa189-316">Se o aplicativo usa o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), o pacote é automaticamente incluído.</span><span class="sxs-lookup"><span data-stu-id="aa189-316">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="aa189-317">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="aa189-317">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="aa189-318">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-318">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="aa189-319">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-319">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="aa189-320">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="aa189-320">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="aa189-321">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="aa189-321">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="aa189-322">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-322">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="aa189-323">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-323">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="aa189-324">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="aa189-324">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="aa189-325">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="aa189-325">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="aa189-327">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="aa189-327">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="aa189-328">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-328">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="aa189-329">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="aa189-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="aa189-330">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="aa189-330">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="aa189-331">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="aa189-331">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="aa189-332">Várias solicitações ao servidor retornam valores de constante fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="aa189-332">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="aa189-333">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="aa189-333">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="aa189-334">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="aa189-334">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="aa189-335">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aa189-335">Save the *appsettings.json* file.</span></span> <span data-ttu-id="aa189-336">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="aa189-336">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="aa189-337">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="aa189-337">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="aa189-338">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-338">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="aa189-339">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-339">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="aa189-340">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="aa189-340">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="aa189-341">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-341">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="aa189-342">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="aa189-342">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="aa189-343">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aa189-343">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="aa189-344">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="aa189-344">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="aa189-345">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="aa189-345">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="aa189-346">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-346">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="aa189-347">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="aa189-347">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="aa189-348">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-348">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="aa189-349">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="aa189-349">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="aa189-350">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="aa189-350">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="aa189-351">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="aa189-351">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="aa189-352">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-352">All options are named instances.</span></span> <span data-ttu-id="aa189-353">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="aa189-353">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="aa189-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="aa189-355">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="aa189-355">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="aa189-356">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="aa189-356">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="aa189-357">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="aa189-357">OptionsBuilder API</span></span>

<span data-ttu-id="aa189-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="aa189-359">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="aa189-359">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="aa189-360">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="aa189-360">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="aa189-361">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="aa189-361">Use DI services to configure options</span></span>

<span data-ttu-id="aa189-362">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="aa189-362">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="aa189-363">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="aa189-363">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="aa189-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="aa189-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="aa189-365">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-365">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="aa189-366">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="aa189-366">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="aa189-367">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="aa189-367">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="aa189-368">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="aa189-368">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="aa189-369">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="aa189-369">Options validation</span></span>

<span data-ttu-id="aa189-370">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="aa189-370">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="aa189-371">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="aa189-371">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="aa189-372">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="aa189-372">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="aa189-373">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="aa189-373">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="aa189-374">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-374">Validation runs when the options instance is created.</span></span> <span data-ttu-id="aa189-375">A instância de opções tem garantia de passar pela validação, na primeira vez em que for acessada.</span><span class="sxs-lookup"><span data-stu-id="aa189-375">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aa189-376">A validação não protege contra modificações de opções, depois que as opções são inicialmente configuradas e validadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-376">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="aa189-377">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="aa189-377">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="aa189-378">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="aa189-378">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="aa189-379">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="aa189-379">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="aa189-380">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="aa189-380">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="aa189-381">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="aa189-381">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="aa189-382">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-382">A specific named options instance.</span></span>
* <span data-ttu-id="aa189-383">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="aa189-383">All options when `name` is `null`.</span></span>

<span data-ttu-id="aa189-384">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="aa189-384">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="aa189-385">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="aa189-385">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="aa189-386">O `Microsoft.Extensions.Options.DataAnnotations` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="aa189-386">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

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

<span data-ttu-id="aa189-387">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="aa189-387">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="aa189-388">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="aa189-388">Options post-configuration</span></span>

<span data-ttu-id="aa189-389">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-389">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="aa189-390">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="aa189-390">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="aa189-391">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="aa189-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="aa189-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="aa189-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="aa189-393">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="aa189-393">Accessing options during startup</span></span>

<span data-ttu-id="aa189-394"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="aa189-394"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="aa189-395">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="aa189-395">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aa189-396">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-396">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="aa189-397">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-397">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="aa189-398">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="aa189-398">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="aa189-399">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="aa189-399">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="aa189-400">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="aa189-400">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="aa189-401">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-401">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="aa189-402">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="aa189-402">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="aa189-403">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa189-403">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aa189-404">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="aa189-404">Prerequisites</span></span>

<span data-ttu-id="aa189-405">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="aa189-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="aa189-406">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="aa189-406">Options interfaces</span></span>

<span data-ttu-id="aa189-407">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="aa189-408">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="aa189-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="aa189-409">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="aa189-409">Change notifications</span></span>
* [<span data-ttu-id="aa189-410">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="aa189-410">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="aa189-411">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="aa189-411">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="aa189-412">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="aa189-412">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="aa189-413">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="aa189-413">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="aa189-414">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="aa189-415">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-415">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="aa189-416">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-416">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="aa189-417">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="aa189-417">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="aa189-418">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="aa189-419">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="aa189-419">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="aa189-420">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-420">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="aa189-421">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="aa189-421">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="aa189-422">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="aa189-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="aa189-423">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="aa189-423">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="aa189-424">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-424"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="aa189-425">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-425">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="aa189-426">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-426">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="aa189-427">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="aa189-427">General options configuration</span></span>

<span data-ttu-id="aa189-428">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-428">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="aa189-429">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="aa189-429">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="aa189-430">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="aa189-430">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="aa189-431">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="aa189-431">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="aa189-432">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-432">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="aa189-433">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="aa189-433">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="aa189-434">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-434">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="aa189-435">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="aa189-435">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="aa189-436">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="aa189-436">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="aa189-437">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="aa189-437">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="aa189-438">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-438">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="aa189-439">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="aa189-439">Configure simple options with a delegate</span></span>

<span data-ttu-id="aa189-440">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-440">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="aa189-441">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="aa189-441">Use a delegate to set options values.</span></span> <span data-ttu-id="aa189-442">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-442">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="aa189-443">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-443">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="aa189-444">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="aa189-444">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="aa189-445">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="aa189-445">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="aa189-446">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-446">You can add multiple configuration providers.</span></span> <span data-ttu-id="aa189-447">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="aa189-447">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="aa189-448">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="aa189-448">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="aa189-449">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-449">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="aa189-450">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="aa189-450">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="aa189-451">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="aa189-451">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="aa189-452">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="aa189-452">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="aa189-453">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="aa189-453">Suboptions configuration</span></span>

<span data-ttu-id="aa189-454">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-454">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="aa189-455">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aa189-455">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="aa189-456">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="aa189-456">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="aa189-457">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração do formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="aa189-457">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="aa189-458">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aa189-458">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="aa189-459">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-459">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="aa189-460">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="aa189-460">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="aa189-461">O método de extensão `GetSection` exige o pacote NuGet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="aa189-461">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="aa189-462">Se o aplicativo usa o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), o pacote é automaticamente incluído.</span><span class="sxs-lookup"><span data-stu-id="aa189-462">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="aa189-463">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="aa189-463">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="aa189-464">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-464">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="aa189-465">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-465">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="aa189-466">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="aa189-466">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="aa189-467">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="aa189-467">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="aa189-468">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-468">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="aa189-469">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-469">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="aa189-470">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="aa189-470">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="aa189-471">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="aa189-471">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="aa189-473">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="aa189-473">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="aa189-474">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-474">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="aa189-475">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="aa189-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="aa189-476">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="aa189-476">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="aa189-477">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="aa189-477">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="aa189-478">Várias solicitações ao servidor retornam valores de constante fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="aa189-478">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="aa189-479">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="aa189-479">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="aa189-480">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="aa189-480">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="aa189-481">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aa189-481">Save the *appsettings.json* file.</span></span> <span data-ttu-id="aa189-482">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="aa189-482">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="aa189-483">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="aa189-483">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="aa189-484">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="aa189-484">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="aa189-485">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-485">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="aa189-486">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="aa189-486">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="aa189-487">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="aa189-487">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="aa189-488">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="aa189-488">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="aa189-489">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="aa189-489">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="aa189-490">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="aa189-490">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="aa189-491">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="aa189-491">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="aa189-492">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-492">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="aa189-493">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="aa189-493">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="aa189-494">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="aa189-494">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="aa189-495">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="aa189-495">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="aa189-496">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="aa189-496">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="aa189-497">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="aa189-497">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="aa189-498">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="aa189-498">All options are named instances.</span></span> <span data-ttu-id="aa189-499">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="aa189-499">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="aa189-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="aa189-501">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="aa189-501">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="aa189-502">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="aa189-502">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="aa189-503">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="aa189-503">OptionsBuilder API</span></span>

<span data-ttu-id="aa189-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa189-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="aa189-505">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="aa189-505">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="aa189-506">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="aa189-506">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="aa189-507">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="aa189-507">Use DI services to configure options</span></span>

<span data-ttu-id="aa189-508">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="aa189-508">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="aa189-509">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="aa189-509">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="aa189-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="aa189-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="aa189-511">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-511">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="aa189-512">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="aa189-512">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="aa189-513">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="aa189-513">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="aa189-514">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="aa189-514">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="aa189-515">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="aa189-515">Options post-configuration</span></span>

<span data-ttu-id="aa189-516">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="aa189-516">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="aa189-517">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="aa189-517">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="aa189-518">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="aa189-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="aa189-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="aa189-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="aa189-520">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="aa189-520">Accessing options during startup</span></span>

<span data-ttu-id="aa189-521"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="aa189-521"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="aa189-522">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="aa189-522">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aa189-523">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="aa189-523">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="aa189-524">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="aa189-524">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
