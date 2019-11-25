---
title: Padrão de opções no ASP.NET Core
author: guardrex
description: Descubra como usar o padrão de opções para representar grupos de configurações relacionadas em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/18/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 4192bab8acef7c4f7bdf1ac481c468cd0a835420
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239793"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="47082-103">Padrão de opções no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47082-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="47082-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="47082-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47082-105">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="47082-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="47082-106">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="47082-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="47082-107">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="47082-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="47082-108">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="47082-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="47082-109">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="47082-110">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="47082-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="47082-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47082-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="47082-112">Pacote</span><span class="sxs-lookup"><span data-stu-id="47082-112">Package</span></span>

<span data-ttu-id="47082-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span><span class="sxs-lookup"><span data-stu-id="47082-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="47082-114">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="47082-114">Options interfaces</span></span>

<span data-ttu-id="47082-115">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="47082-116">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="47082-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="47082-117">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="47082-117">Change notifications</span></span>
* [<span data-ttu-id="47082-118">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="47082-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="47082-119">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="47082-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="47082-120">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="47082-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="47082-121">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="47082-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="47082-122">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="47082-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="47082-123">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="47082-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="47082-124">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="47082-125">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="47082-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="47082-126">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="47082-127">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="47082-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="47082-128">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="47082-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="47082-129">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="47082-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="47082-130">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="47082-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="47082-131">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="47082-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="47082-132">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="47082-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="47082-133">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="47082-134">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="47082-135">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="47082-135">General options configuration</span></span>

<span data-ttu-id="47082-136">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="47082-137">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="47082-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="47082-138">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="47082-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="47082-139">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="47082-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="47082-140">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="47082-141">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="47082-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="47082-142">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="47082-143">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="47082-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="47082-144">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="47082-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="47082-145">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="47082-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="47082-146">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="47082-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="47082-147">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="47082-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="47082-148">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="47082-149">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="47082-149">Use a delegate to set options values.</span></span> <span data-ttu-id="47082-150">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="47082-151">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="47082-152">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="47082-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="47082-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="47082-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="47082-154">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="47082-155">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="47082-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="47082-156">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="47082-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="47082-157">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="47082-158">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="47082-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="47082-159">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="47082-160">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="47082-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="47082-161">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="47082-161">Suboptions configuration</span></span>

<span data-ttu-id="47082-162">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="47082-163">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47082-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="47082-164">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="47082-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="47082-165">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração no formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="47082-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="47082-166">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47082-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="47082-167">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="47082-168">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47082-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="47082-169">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span><span class="sxs-lookup"><span data-stu-id="47082-169">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="47082-170">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="47082-170">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="47082-171">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-171">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="47082-172">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-172">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="47082-173">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="47082-173">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="47082-174">Options injection</span><span class="sxs-lookup"><span data-stu-id="47082-174">Options injection</span></span>

<span data-ttu-id="47082-175">Options injection is demonstrated as Example &num;4 in the sample app.</span><span class="sxs-lookup"><span data-stu-id="47082-175">Options injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="47082-176">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span><span class="sxs-lookup"><span data-stu-id="47082-176">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="47082-177">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span><span class="sxs-lookup"><span data-stu-id="47082-177">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="47082-178">A page or view model.</span><span class="sxs-lookup"><span data-stu-id="47082-178">A page or view model.</span></span>

<span data-ttu-id="47082-179">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-179">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="47082-180">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="47082-180">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="47082-181">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="47082-181">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="47082-183">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="47082-183">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="47082-184">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-184">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="47082-185">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span><span class="sxs-lookup"><span data-stu-id="47082-185">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="47082-186">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span><span class="sxs-lookup"><span data-stu-id="47082-186">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="47082-187">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span><span class="sxs-lookup"><span data-stu-id="47082-187">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="47082-188">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span><span class="sxs-lookup"><span data-stu-id="47082-188">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="47082-189">Options snapshots are designed for use with transient and scoped dependencies.</span><span class="sxs-lookup"><span data-stu-id="47082-189">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="47082-190">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="47082-190">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="47082-191">Várias solicitações ao servidor retornam valores constantes fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="47082-191">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="47082-192">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47082-192">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="47082-193">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="47082-193">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="47082-194">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47082-194">Save the *appsettings.json* file.</span></span> <span data-ttu-id="47082-195">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="47082-195">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="47082-196">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="47082-196">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="47082-197">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-197">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="47082-198">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="47082-198">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="47082-199">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="47082-199">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="47082-200">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="47082-201">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="47082-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="47082-202">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47082-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="47082-203">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="47082-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="47082-204">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="47082-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="47082-205">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="47082-206">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="47082-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="47082-207">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="47082-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="47082-208">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="47082-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="47082-209">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="47082-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="47082-210">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="47082-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="47082-211">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="47082-211">All options are named instances.</span></span> <span data-ttu-id="47082-212">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="47082-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="47082-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="47082-214">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="47082-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="47082-215">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="47082-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="47082-216">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="47082-216">OptionsBuilder API</span></span>

<span data-ttu-id="47082-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="47082-218">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="47082-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="47082-219">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="47082-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="47082-220">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="47082-220">Use DI services to configure options</span></span>

<span data-ttu-id="47082-221">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="47082-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="47082-222">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="47082-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="47082-223">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="47082-223">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="47082-224">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="47082-225">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="47082-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="47082-226">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="47082-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="47082-227">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="47082-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="47082-228">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="47082-228">Options validation</span></span>

<span data-ttu-id="47082-229">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="47082-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="47082-230">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="47082-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="47082-231">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="47082-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="47082-232">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="47082-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="47082-233">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="47082-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="47082-234">A instância de opções tem garantia de passar pela validação, na primeira vez em que for acessada.</span><span class="sxs-lookup"><span data-stu-id="47082-234">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47082-235">A validação não protege contra modificações de opções, depois que as opções são inicialmente configuradas e validadas.</span><span class="sxs-lookup"><span data-stu-id="47082-235">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="47082-236">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="47082-236">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="47082-237">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="47082-237">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="47082-238">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="47082-238">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="47082-239">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="47082-239">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="47082-240">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="47082-240">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="47082-241">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="47082-241">A specific named options instance.</span></span>
* <span data-ttu-id="47082-242">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="47082-242">All options when `name` is `null`.</span></span>

<span data-ttu-id="47082-243">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="47082-243">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="47082-244">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="47082-244">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="47082-245">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span><span class="sxs-lookup"><span data-stu-id="47082-245">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="47082-246">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="47082-246">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="47082-247">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="47082-247">Options post-configuration</span></span>

<span data-ttu-id="47082-248">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-248">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="47082-249">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="47082-249">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="47082-250">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="47082-250"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="47082-251">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="47082-251">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="47082-252">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="47082-252">Accessing options during startup</span></span>

<span data-ttu-id="47082-253"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="47082-253"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="47082-254">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="47082-254">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="47082-255">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-255">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="47082-256">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="47082-256">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="47082-257">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="47082-257">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="47082-258">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="47082-258">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="47082-259">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="47082-259">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="47082-260">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-260">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="47082-261">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="47082-261">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="47082-262">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47082-262">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="47082-263">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="47082-263">Prerequisites</span></span>

<span data-ttu-id="47082-264">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="47082-264">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="47082-265">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="47082-265">Options interfaces</span></span>

<span data-ttu-id="47082-266">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-266"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="47082-267">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="47082-267"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="47082-268">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="47082-268">Change notifications</span></span>
* [<span data-ttu-id="47082-269">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="47082-269">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="47082-270">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="47082-270">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="47082-271">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="47082-271">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="47082-272">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="47082-272">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="47082-273">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="47082-273"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="47082-274">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="47082-274">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="47082-275">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-275">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="47082-276">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="47082-276">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="47082-277">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-277"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="47082-278">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="47082-278">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="47082-279">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="47082-279">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="47082-280">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="47082-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="47082-281">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="47082-281"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="47082-282">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="47082-282">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="47082-283">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="47082-283"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="47082-284">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-284">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="47082-285">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-285">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="47082-286">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="47082-286">General options configuration</span></span>

<span data-ttu-id="47082-287">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-287">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="47082-288">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="47082-288">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="47082-289">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="47082-289">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="47082-290">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="47082-290">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="47082-291">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-291">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="47082-292">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="47082-292">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="47082-293">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-293">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="47082-294">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="47082-294">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="47082-295">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="47082-295">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="47082-296">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="47082-296">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="47082-297">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="47082-297">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="47082-298">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="47082-298">Configure simple options with a delegate</span></span>

<span data-ttu-id="47082-299">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-299">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="47082-300">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="47082-300">Use a delegate to set options values.</span></span> <span data-ttu-id="47082-301">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-301">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="47082-302">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-302">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="47082-303">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="47082-303">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="47082-304">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="47082-304">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="47082-305">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-305">You can add multiple configuration providers.</span></span> <span data-ttu-id="47082-306">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="47082-306">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="47082-307">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="47082-307">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="47082-308">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-308">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="47082-309">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="47082-309">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="47082-310">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-310">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="47082-311">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="47082-311">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="47082-312">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="47082-312">Suboptions configuration</span></span>

<span data-ttu-id="47082-313">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-313">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="47082-314">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47082-314">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="47082-315">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="47082-315">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="47082-316">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração no formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="47082-316">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="47082-317">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47082-317">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="47082-318">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-318">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="47082-319">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47082-319">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="47082-320">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span><span class="sxs-lookup"><span data-stu-id="47082-320">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="47082-321">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="47082-321">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="47082-322">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-322">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="47082-323">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-323">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="47082-324">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="47082-324">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="47082-325">Options injection</span><span class="sxs-lookup"><span data-stu-id="47082-325">Options injection</span></span>

<span data-ttu-id="47082-326">Options injection is demonstrated as Example &num;4 in the sample app.</span><span class="sxs-lookup"><span data-stu-id="47082-326">Options injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="47082-327">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span><span class="sxs-lookup"><span data-stu-id="47082-327">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="47082-328">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span><span class="sxs-lookup"><span data-stu-id="47082-328">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="47082-329">A page or view model.</span><span class="sxs-lookup"><span data-stu-id="47082-329">A page or view model.</span></span>

<span data-ttu-id="47082-330">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-330">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="47082-331">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="47082-331">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="47082-332">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="47082-332">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="47082-334">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="47082-334">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="47082-335">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-335">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="47082-336">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span><span class="sxs-lookup"><span data-stu-id="47082-336">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="47082-337">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span><span class="sxs-lookup"><span data-stu-id="47082-337">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="47082-338">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span><span class="sxs-lookup"><span data-stu-id="47082-338">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="47082-339">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span><span class="sxs-lookup"><span data-stu-id="47082-339">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="47082-340">Options snapshots are designed for use with transient and scoped dependencies.</span><span class="sxs-lookup"><span data-stu-id="47082-340">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="47082-341">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="47082-341">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="47082-342">Várias solicitações ao servidor retornam valores constantes fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="47082-342">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="47082-343">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47082-343">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="47082-344">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="47082-344">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="47082-345">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47082-345">Save the *appsettings.json* file.</span></span> <span data-ttu-id="47082-346">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="47082-346">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="47082-347">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="47082-347">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="47082-348">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-348">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="47082-349">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="47082-349">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="47082-350">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="47082-350">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="47082-351">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-351">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="47082-352">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="47082-352">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="47082-353">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47082-353">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="47082-354">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="47082-354">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="47082-355">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="47082-355">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="47082-356">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-356">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="47082-357">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="47082-357">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="47082-358">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="47082-358">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="47082-359">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="47082-359">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="47082-360">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="47082-360">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="47082-361">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="47082-361">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="47082-362">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="47082-362">All options are named instances.</span></span> <span data-ttu-id="47082-363">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="47082-363">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="47082-364"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-364"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="47082-365">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="47082-365">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="47082-366">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="47082-366">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="47082-367">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="47082-367">OptionsBuilder API</span></span>

<span data-ttu-id="47082-368"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-368"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="47082-369">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="47082-369">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="47082-370">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="47082-370">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="47082-371">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="47082-371">Use DI services to configure options</span></span>

<span data-ttu-id="47082-372">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="47082-372">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="47082-373">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="47082-373">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="47082-374">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="47082-374">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="47082-375">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-375">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="47082-376">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="47082-376">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="47082-377">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="47082-377">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="47082-378">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="47082-378">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="47082-379">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="47082-379">Options validation</span></span>

<span data-ttu-id="47082-380">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="47082-380">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="47082-381">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="47082-381">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="47082-382">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="47082-382">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="47082-383">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="47082-383">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="47082-384">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="47082-384">Validation runs when the options instance is created.</span></span> <span data-ttu-id="47082-385">A instância de opções tem garantia de passar pela validação, na primeira vez em que for acessada.</span><span class="sxs-lookup"><span data-stu-id="47082-385">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47082-386">A validação não protege contra modificações de opções, depois que as opções são inicialmente configuradas e validadas.</span><span class="sxs-lookup"><span data-stu-id="47082-386">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="47082-387">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="47082-387">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="47082-388">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="47082-388">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="47082-389">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="47082-389">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="47082-390">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="47082-390">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="47082-391">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="47082-391">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="47082-392">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="47082-392">A specific named options instance.</span></span>
* <span data-ttu-id="47082-393">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="47082-393">All options when `name` is `null`.</span></span>

<span data-ttu-id="47082-394">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="47082-394">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="47082-395">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="47082-395">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="47082-396">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="47082-396">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="47082-397">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="47082-397">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="47082-398">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="47082-398">Options post-configuration</span></span>

<span data-ttu-id="47082-399">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-399">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="47082-400">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="47082-400">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="47082-401">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="47082-401"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="47082-402">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="47082-402">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="47082-403">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="47082-403">Accessing options during startup</span></span>

<span data-ttu-id="47082-404"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="47082-404"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="47082-405">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="47082-405">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="47082-406">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-406">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="47082-407">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="47082-407">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="47082-408">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="47082-408">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="47082-409">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="47082-409">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="47082-410">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="47082-410">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="47082-411">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-411">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="47082-412">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="47082-412">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="47082-413">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47082-413">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="47082-414">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="47082-414">Prerequisites</span></span>

<span data-ttu-id="47082-415">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="47082-415">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="47082-416">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="47082-416">Options interfaces</span></span>

<span data-ttu-id="47082-417">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-417"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="47082-418">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="47082-418"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="47082-419">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="47082-419">Change notifications</span></span>
* [<span data-ttu-id="47082-420">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="47082-420">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="47082-421">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="47082-421">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="47082-422">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="47082-422">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="47082-423">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="47082-423">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="47082-424">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="47082-424"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="47082-425">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="47082-425">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="47082-426">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-426">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="47082-427">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="47082-427">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="47082-428">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-428"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="47082-429">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="47082-429">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="47082-430">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="47082-430">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="47082-431">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="47082-431">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="47082-432">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="47082-432"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="47082-433">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="47082-433">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="47082-434">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="47082-434"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="47082-435">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-435">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="47082-436">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-436">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="47082-437">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="47082-437">General options configuration</span></span>

<span data-ttu-id="47082-438">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-438">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="47082-439">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="47082-439">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="47082-440">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="47082-440">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="47082-441">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="47082-441">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="47082-442">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-442">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="47082-443">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="47082-443">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="47082-444">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-444">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="47082-445">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="47082-445">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="47082-446">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="47082-446">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="47082-447">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="47082-447">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="47082-448">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="47082-448">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="47082-449">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="47082-449">Configure simple options with a delegate</span></span>

<span data-ttu-id="47082-450">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-450">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="47082-451">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="47082-451">Use a delegate to set options values.</span></span> <span data-ttu-id="47082-452">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-452">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="47082-453">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-453">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="47082-454">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="47082-454">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="47082-455">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="47082-455">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="47082-456">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-456">You can add multiple configuration providers.</span></span> <span data-ttu-id="47082-457">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="47082-457">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="47082-458">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="47082-458">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="47082-459">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-459">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="47082-460">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="47082-460">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="47082-461">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="47082-461">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="47082-462">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="47082-462">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="47082-463">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="47082-463">Suboptions configuration</span></span>

<span data-ttu-id="47082-464">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-464">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="47082-465">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47082-465">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="47082-466">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="47082-466">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="47082-467">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração no formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="47082-467">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="47082-468">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47082-468">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="47082-469">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-469">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="47082-470">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47082-470">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="47082-471">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span><span class="sxs-lookup"><span data-stu-id="47082-471">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="47082-472">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="47082-472">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="47082-473">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-473">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="47082-474">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-474">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="47082-475">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="47082-475">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="47082-476">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="47082-476">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="47082-477">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-477">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="47082-478">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-478">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="47082-479">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="47082-479">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="47082-480">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="47082-480">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="47082-482">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="47082-482">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="47082-483">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-483">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="47082-484">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="47082-484"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="47082-485">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="47082-485">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="47082-486">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="47082-486">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="47082-487">Várias solicitações ao servidor retornam valores constantes fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="47082-487">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="47082-488">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47082-488">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="47082-489">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="47082-489">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="47082-490">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47082-490">Save the *appsettings.json* file.</span></span> <span data-ttu-id="47082-491">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="47082-491">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="47082-492">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="47082-492">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="47082-493">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="47082-493">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="47082-494">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="47082-494">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="47082-495">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="47082-495">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="47082-496">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47082-496">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="47082-497">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="47082-497">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="47082-498">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47082-498">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="47082-499">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="47082-499">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="47082-500">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="47082-500">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="47082-501">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-501">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="47082-502">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="47082-502">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="47082-503">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="47082-503">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="47082-504">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="47082-504">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="47082-505">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="47082-505">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="47082-506">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="47082-506">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="47082-507">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="47082-507">All options are named instances.</span></span> <span data-ttu-id="47082-508">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="47082-508">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="47082-509"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-509"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="47082-510">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="47082-510">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="47082-511">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="47082-511">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="47082-512">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="47082-512">OptionsBuilder API</span></span>

<span data-ttu-id="47082-513"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="47082-513"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="47082-514">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="47082-514">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="47082-515">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="47082-515">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="47082-516">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="47082-516">Use DI services to configure options</span></span>

<span data-ttu-id="47082-517">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="47082-517">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="47082-518">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="47082-518">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="47082-519">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="47082-519">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="47082-520">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-520">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="47082-521">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="47082-521">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="47082-522">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="47082-522">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="47082-523">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="47082-523">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="47082-524">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="47082-524">Options post-configuration</span></span>

<span data-ttu-id="47082-525">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="47082-525">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="47082-526">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="47082-526">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="47082-527">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="47082-527"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="47082-528">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="47082-528">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="47082-529">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="47082-529">Accessing options during startup</span></span>

<span data-ttu-id="47082-530"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="47082-530"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="47082-531">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="47082-531">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="47082-532">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="47082-532">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="47082-533">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="47082-533">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
