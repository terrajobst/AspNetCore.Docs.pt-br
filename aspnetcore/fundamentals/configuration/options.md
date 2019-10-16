---
title: Padrão de opções no ASP.NET Core
author: guardrex
description: Descubra como usar o padrão de opções para representar grupos de configurações relacionadas em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: eb0b7f3f4596b63cf3142017c5c5fe4923aac3a4
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378738"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="3140f-103">Padrão de opções no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3140f-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="3140f-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3140f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3140f-105">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="3140f-106">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="3140f-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3140f-107">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="3140f-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3140f-108">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="3140f-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3140f-109">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3140f-110">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="3140f-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3140f-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3140f-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="3140f-112">Pacote</span><span class="sxs-lookup"><span data-stu-id="3140f-112">Package</span></span>

<span data-ttu-id="3140f-113">O pacote [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) é referenciado implicitamente em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3140f-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="3140f-114">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="3140f-114">Options interfaces</span></span>

<span data-ttu-id="3140f-115">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="3140f-116">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="3140f-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="3140f-117">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="3140f-117">Change notifications</span></span>
* [<span data-ttu-id="3140f-118">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="3140f-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="3140f-119">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="3140f-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="3140f-120">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="3140f-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="3140f-121">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="3140f-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="3140f-122">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="3140f-123">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3140f-124">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3140f-125">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="3140f-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3140f-126">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="3140f-127">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3140f-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3140f-128">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3140f-129">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="3140f-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="3140f-130">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="3140f-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3140f-131">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="3140f-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="3140f-132">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="3140f-133">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="3140f-134">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="3140f-135">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="3140f-135">General options configuration</span></span>

<span data-ttu-id="3140f-136">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="3140f-137">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="3140f-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="3140f-138">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="3140f-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="3140f-139">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3140f-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="3140f-140">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="3140f-141">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="3140f-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="3140f-142">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="3140f-143">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="3140f-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="3140f-144">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="3140f-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="3140f-145">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="3140f-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="3140f-146">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="3140f-147">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="3140f-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="3140f-148">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="3140f-149">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-149">Use a delegate to set options values.</span></span> <span data-ttu-id="3140f-150">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="3140f-151">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3140f-152">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="3140f-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="3140f-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3140f-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="3140f-154">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="3140f-155">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="3140f-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="3140f-156">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3140f-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3140f-157">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="3140f-158">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="3140f-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="3140f-159">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="3140f-160">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="3140f-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="3140f-161">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="3140f-161">Suboptions configuration</span></span>

<span data-ttu-id="3140f-162">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="3140f-163">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3140f-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="3140f-164">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="3140f-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="3140f-165">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração no formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="3140f-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="3140f-166">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3140f-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="3140f-167">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3140f-168">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3140f-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="3140f-169">O método de extensão `GetSection` exige o pacote NuGet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="3140f-169">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="3140f-170">`Microsoft.Extensions.Options.ConfigurationExtensions` é implicitamente referenciada em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3140f-170">`Microsoft.Extensions.Options.ConfigurationExtensions` is implicitly referenced in ASP.NET Core apps.</span></span>

<span data-ttu-id="3140f-171">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="3140f-171">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="3140f-172">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-172">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="3140f-173">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-173">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="3140f-174">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="3140f-174">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="3140f-175">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="3140f-175">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="3140f-176">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-176">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="3140f-177">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-177">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="3140f-178">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="3140f-178">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="3140f-179">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="3140f-179">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="3140f-181">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="3140f-181">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="3140f-182">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-182">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="3140f-183">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="3140f-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="3140f-184">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3140f-184">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="3140f-185">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="3140f-185">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="3140f-186">Várias solicitações ao servidor retornam valores constantes fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="3140f-186">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="3140f-187">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3140f-187">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="3140f-188">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="3140f-188">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="3140f-189">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3140f-189">Save the *appsettings.json* file.</span></span> <span data-ttu-id="3140f-190">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="3140f-190">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="3140f-191">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="3140f-191">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="3140f-192">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-192">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="3140f-193">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-193">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="3140f-194">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="3140f-194">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="3140f-195">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-195">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="3140f-196">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="3140f-196">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="3140f-197">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3140f-197">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="3140f-198">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="3140f-198">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="3140f-199">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3140f-199">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="3140f-200">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-200">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="3140f-201">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="3140f-201">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="3140f-202">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-202">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="3140f-203">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="3140f-203">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="3140f-204">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3140f-204">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="3140f-205">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="3140f-205">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="3140f-206">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-206">All options are named instances.</span></span> <span data-ttu-id="3140f-207">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="3140f-207">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3140f-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="3140f-209">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="3140f-209">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="3140f-210">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="3140f-210">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3140f-211">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="3140f-211">OptionsBuilder API</span></span>

<span data-ttu-id="3140f-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3140f-213">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="3140f-213">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3140f-214">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3140f-214">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="3140f-215">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="3140f-215">Use DI services to configure options</span></span>

<span data-ttu-id="3140f-216">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="3140f-216">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="3140f-217">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="3140f-217">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="3140f-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="3140f-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="3140f-219">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-219">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="3140f-220">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="3140f-220">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="3140f-221">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="3140f-221">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="3140f-222">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="3140f-222">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="3140f-223">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="3140f-223">Options validation</span></span>

<span data-ttu-id="3140f-224">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="3140f-224">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="3140f-225">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="3140f-225">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="3140f-226">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="3140f-226">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="3140f-227">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="3140f-227">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="3140f-228">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-228">Validation runs when the options instance is created.</span></span> <span data-ttu-id="3140f-229">A instância de opções tem garantia de passar pela validação, na primeira vez em que for acessada.</span><span class="sxs-lookup"><span data-stu-id="3140f-229">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3140f-230">A validação não protege contra modificações de opções, depois que as opções são inicialmente configuradas e validadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-230">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="3140f-231">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="3140f-231">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="3140f-232">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="3140f-232">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="3140f-233">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="3140f-233">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="3140f-234">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="3140f-234">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="3140f-235">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="3140f-235">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="3140f-236">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-236">A specific named options instance.</span></span>
* <span data-ttu-id="3140f-237">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="3140f-237">All options when `name` is `null`.</span></span>

<span data-ttu-id="3140f-238">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="3140f-238">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="3140f-239">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="3140f-239">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="3140f-240">`Microsoft.Extensions.Options.DataAnnotations` é implicitamente referenciada em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3140f-240">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

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

<span data-ttu-id="3140f-241">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="3140f-241">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="3140f-242">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="3140f-242">Options post-configuration</span></span>

<span data-ttu-id="3140f-243">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-243">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="3140f-244">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="3140f-244">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3140f-245">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="3140f-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3140f-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="3140f-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3140f-247">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="3140f-247">Accessing options during startup</span></span>

<span data-ttu-id="3140f-248"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="3140f-248"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3140f-249">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3140f-249">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3140f-250">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-250">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3140f-251">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-251">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="3140f-252">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="3140f-252">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3140f-253">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="3140f-253">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3140f-254">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="3140f-254">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3140f-255">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-255">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3140f-256">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="3140f-256">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3140f-257">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3140f-257">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3140f-258">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="3140f-258">Prerequisites</span></span>

<span data-ttu-id="3140f-259">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="3140f-259">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="3140f-260">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="3140f-260">Options interfaces</span></span>

<span data-ttu-id="3140f-261">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="3140f-262">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="3140f-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="3140f-263">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="3140f-263">Change notifications</span></span>
* [<span data-ttu-id="3140f-264">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="3140f-264">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="3140f-265">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="3140f-265">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="3140f-266">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="3140f-266">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="3140f-267">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="3140f-267">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="3140f-268">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="3140f-269">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-269">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3140f-270">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-270">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3140f-271">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="3140f-271">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3140f-272">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="3140f-273">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3140f-273">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3140f-274">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-274">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3140f-275">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="3140f-275">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="3140f-276">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="3140f-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3140f-277">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="3140f-277">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="3140f-278">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-278"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="3140f-279">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-279">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="3140f-280">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-280">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="3140f-281">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="3140f-281">General options configuration</span></span>

<span data-ttu-id="3140f-282">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-282">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="3140f-283">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="3140f-283">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="3140f-284">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="3140f-284">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="3140f-285">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3140f-285">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="3140f-286">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-286">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="3140f-287">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="3140f-287">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="3140f-288">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-288">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="3140f-289">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="3140f-289">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="3140f-290">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="3140f-290">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="3140f-291">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="3140f-291">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="3140f-292">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-292">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="3140f-293">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="3140f-293">Configure simple options with a delegate</span></span>

<span data-ttu-id="3140f-294">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-294">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="3140f-295">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-295">Use a delegate to set options values.</span></span> <span data-ttu-id="3140f-296">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-296">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="3140f-297">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-297">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3140f-298">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="3140f-298">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="3140f-299">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3140f-299">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="3140f-300">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-300">You can add multiple configuration providers.</span></span> <span data-ttu-id="3140f-301">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="3140f-301">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="3140f-302">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3140f-302">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3140f-303">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-303">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="3140f-304">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="3140f-304">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="3140f-305">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-305">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="3140f-306">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="3140f-306">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="3140f-307">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="3140f-307">Suboptions configuration</span></span>

<span data-ttu-id="3140f-308">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-308">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="3140f-309">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3140f-309">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="3140f-310">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="3140f-310">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="3140f-311">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração no formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="3140f-311">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="3140f-312">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3140f-312">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="3140f-313">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-313">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3140f-314">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3140f-314">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="3140f-315">O método de extensão `GetSection` exige o pacote NuGet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="3140f-315">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="3140f-316">Se o aplicativo usa o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), o pacote é automaticamente incluído.</span><span class="sxs-lookup"><span data-stu-id="3140f-316">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="3140f-317">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="3140f-317">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="3140f-318">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-318">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="3140f-319">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-319">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="3140f-320">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="3140f-320">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="3140f-321">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="3140f-321">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="3140f-322">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-322">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="3140f-323">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-323">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="3140f-324">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="3140f-324">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="3140f-325">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="3140f-325">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="3140f-327">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="3140f-327">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="3140f-328">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-328">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="3140f-329">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="3140f-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="3140f-330">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3140f-330">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="3140f-331">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="3140f-331">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="3140f-332">Várias solicitações ao servidor retornam valores constantes fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="3140f-332">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="3140f-333">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3140f-333">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="3140f-334">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="3140f-334">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="3140f-335">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3140f-335">Save the *appsettings.json* file.</span></span> <span data-ttu-id="3140f-336">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="3140f-336">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="3140f-337">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="3140f-337">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="3140f-338">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-338">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="3140f-339">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-339">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="3140f-340">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="3140f-340">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="3140f-341">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-341">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="3140f-342">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="3140f-342">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="3140f-343">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3140f-343">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="3140f-344">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="3140f-344">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="3140f-345">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3140f-345">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="3140f-346">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-346">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="3140f-347">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="3140f-347">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="3140f-348">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-348">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="3140f-349">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="3140f-349">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="3140f-350">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3140f-350">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="3140f-351">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="3140f-351">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="3140f-352">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-352">All options are named instances.</span></span> <span data-ttu-id="3140f-353">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="3140f-353">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3140f-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="3140f-355">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="3140f-355">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="3140f-356">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="3140f-356">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3140f-357">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="3140f-357">OptionsBuilder API</span></span>

<span data-ttu-id="3140f-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3140f-359">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="3140f-359">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3140f-360">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3140f-360">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="3140f-361">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="3140f-361">Use DI services to configure options</span></span>

<span data-ttu-id="3140f-362">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="3140f-362">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="3140f-363">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="3140f-363">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="3140f-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="3140f-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="3140f-365">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-365">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="3140f-366">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="3140f-366">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="3140f-367">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="3140f-367">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="3140f-368">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="3140f-368">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="3140f-369">Validação de opções</span><span class="sxs-lookup"><span data-stu-id="3140f-369">Options validation</span></span>

<span data-ttu-id="3140f-370">A validação de opções permite validar as opções depois de configurá-las.</span><span class="sxs-lookup"><span data-stu-id="3140f-370">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="3140f-371">Chame `Validate` com um método de validação que retorna `true` quando as opções são válidas, e `false` quando não são:</span><span class="sxs-lookup"><span data-stu-id="3140f-371">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="3140f-372">O exemplo anterior define a instância de opções nomeadas como `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="3140f-372">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="3140f-373">A instância de opções padrão é `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="3140f-373">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="3140f-374">A validação é executada após a criação da instância de opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-374">Validation runs when the options instance is created.</span></span> <span data-ttu-id="3140f-375">A instância de opções tem garantia de passar pela validação, na primeira vez em que for acessada.</span><span class="sxs-lookup"><span data-stu-id="3140f-375">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3140f-376">A validação não protege contra modificações de opções, depois que as opções são inicialmente configuradas e validadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-376">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="3140f-377">O método `Validate` aceita um `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="3140f-377">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="3140f-378">Para personalizar totalmente a validação, implemente `IValidateOptions<TOptions>`, que permite:</span><span class="sxs-lookup"><span data-stu-id="3140f-378">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="3140f-379">A validação de vários tipos de opções: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="3140f-379">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="3140f-380">A validação que depende de outro tipo de opção: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="3140f-380">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="3140f-381">`IValidateOptions` valida:</span><span class="sxs-lookup"><span data-stu-id="3140f-381">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="3140f-382">Uma instância específica de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-382">A specific named options instance.</span></span>
* <span data-ttu-id="3140f-383">Todas as opções quando `name` for `null`.</span><span class="sxs-lookup"><span data-stu-id="3140f-383">All options when `name` is `null`.</span></span>

<span data-ttu-id="3140f-384">Retornar um `ValidateOptionsResult` da implementação da interface:</span><span class="sxs-lookup"><span data-stu-id="3140f-384">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="3140f-385">A validação de anotação de dados está disponível no pacote [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) por meio da chamada do método <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> em `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="3140f-385">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="3140f-386">`Microsoft.Extensions.Options.DataAnnotations` está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3140f-386">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="3140f-387">A validação adiantada (fail fast na inicialização) está sendo considerada para uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="3140f-387">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="3140f-388">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="3140f-388">Options post-configuration</span></span>

<span data-ttu-id="3140f-389">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-389">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="3140f-390">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="3140f-390">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3140f-391">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="3140f-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3140f-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="3140f-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3140f-393">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="3140f-393">Accessing options during startup</span></span>

<span data-ttu-id="3140f-394"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="3140f-394"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3140f-395">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3140f-395">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3140f-396">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-396">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3140f-397">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-397">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="3140f-398">Quando as [definições de configuração](xref:fundamentals/configuration/index) são isoladas por cenário em classes separadas, o aplicativo segue dois princípios importantes de engenharia de software:</span><span class="sxs-lookup"><span data-stu-id="3140f-398">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3140f-399">O [ISP (Princípio de Segregação da Interface) ou Encapsulamento](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; os cenários (classes) que dependem das definições de configuração dependem apenas das definições de configuração usadas por eles.</span><span class="sxs-lookup"><span data-stu-id="3140f-399">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3140f-400">[Separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; As configurações para diferentes partes do aplicativo não são dependentes nem acopladas entre si.</span><span class="sxs-lookup"><span data-stu-id="3140f-400">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3140f-401">As opções também fornecem um mecanismo para validar os dados da configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-401">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3140f-402">Para obter mais configurações, consulte a seção [Validação de opções](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="3140f-402">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3140f-403">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3140f-403">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3140f-404">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="3140f-404">Prerequisites</span></span>

<span data-ttu-id="3140f-405">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="3140f-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="3140f-406">Interfaces de opções</span><span class="sxs-lookup"><span data-stu-id="3140f-406">Options interfaces</span></span>

<span data-ttu-id="3140f-407">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> é usado para recuperar as opções e gerenciar notificações de opções para instâncias de `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="3140f-408">O <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="3140f-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="3140f-409">Notificações de alteração</span><span class="sxs-lookup"><span data-stu-id="3140f-409">Change notifications</span></span>
* [<span data-ttu-id="3140f-410">Opções nomeadas</span><span class="sxs-lookup"><span data-stu-id="3140f-410">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="3140f-411">Configuração recarregável</span><span class="sxs-lookup"><span data-stu-id="3140f-411">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="3140f-412">Invalidação seletiva de opções (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="3140f-412">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="3140f-413">Os cenários de [Pós-configuração](#options-post-configuration) permitem que você defina ou altere as opções depois de todas as configurações do <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serem feitas.</span><span class="sxs-lookup"><span data-stu-id="3140f-413">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="3140f-414">O <xref:Microsoft.Extensions.Options.IOptionsFactory%601> é responsável por criar novas instâncias de opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="3140f-415">Ele tem um único método <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-415">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3140f-416">A implementação padrão usa todos os <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrados e executa todas as configurações primeiro, seguidas da pós-configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-416">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3140f-417">Ela faz distinção entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e <xref:Microsoft.Extensions.Options.IConfigureOptions%601> e chama apenas a interface apropriada.</span><span class="sxs-lookup"><span data-stu-id="3140f-417">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3140f-418">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> é usado pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para armazenar em cache as instâncias do `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="3140f-419">O <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalida as instâncias de opções no monitor, de modo que o valor seja recalculado (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3140f-419">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3140f-420">Os valores podem ser manualmente inseridos com <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-420">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3140f-421">O método <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> é usado quando todas as instâncias nomeadas devem ser recriadas sob demanda.</span><span class="sxs-lookup"><span data-stu-id="3140f-421">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="3140f-422">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é útil em cenários em que as opções devam ser novamente computadas em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="3140f-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3140f-423">Para saber mais, consulte a seção [Recarregar dados de configuração com IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="3140f-423">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="3140f-424">O <xref:Microsoft.Extensions.Options.IOptions%601> pode ser usado para dar suporte a opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-424"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="3140f-425">No entanto, o <xref:Microsoft.Extensions.Options.IOptions%601> não dá suporte a cenários anteriores do <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-425">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="3140f-426">Você pode continuar a usar o <xref:Microsoft.Extensions.Options.IOptions%601> em estruturas e bibliotecas existentes que já usam a interface do <xref:Microsoft.Extensions.Options.IOptions%601> e não exigem os cenários fornecidos pelo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-426">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="3140f-427">Configuração de opções gerais</span><span class="sxs-lookup"><span data-stu-id="3140f-427">General options configuration</span></span>

<span data-ttu-id="3140f-428">A configuração de opções gerais é demonstrada no Exemplo &num;1 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-428">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="3140f-429">Uma classe de opções deve ser não abstrata e com um construtor público sem parâmetros.</span><span class="sxs-lookup"><span data-stu-id="3140f-429">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="3140f-430">A classe a seguir, `MyOptions`, tem duas propriedades, `Option1` e `Option2`.</span><span class="sxs-lookup"><span data-stu-id="3140f-430">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="3140f-431">A configuração de valores padrão é opcional, mas o construtor de classe no exemplo a seguir define o valor padrão de `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3140f-431">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="3140f-432">`Option2` tem um valor padrão definido com a inicialização da propriedade diretamente (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-432">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="3140f-433">A classe `MyOptions` é adicionada ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associada à configuração:</span><span class="sxs-lookup"><span data-stu-id="3140f-433">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="3140f-434">O seguinte modelo de página usa a [injeção de dependência de construtor](xref:mvc/controllers/dependency-injection) com <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> para acessar as configurações (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-434">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="3140f-435">O arquivo *appsettings.json* de exemplo especifica valores para `option1` e `option2`:</span><span class="sxs-lookup"><span data-stu-id="3140f-435">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="3140f-436">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="3140f-436">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="3140f-437">Ao usar um <xref:System.Configuration.ConfigurationBuilder> personalizado para carregar opções de configuração de um arquivo de configurações, confirme se o caminho de base está corretamente configurado:</span><span class="sxs-lookup"><span data-stu-id="3140f-437">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="3140f-438">Não é necessário configurar o caminho de base explicitamente ao carregar opções de configuração do arquivo de configurações por meio do <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-438">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="3140f-439">Configurar opções simples com um delegado</span><span class="sxs-lookup"><span data-stu-id="3140f-439">Configure simple options with a delegate</span></span>

<span data-ttu-id="3140f-440">A configuração de opções simples com um delegado é demonstrada no Exemplo &num;2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-440">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="3140f-441">Use um delegado para definir valores de opções.</span><span class="sxs-lookup"><span data-stu-id="3140f-441">Use a delegate to set options values.</span></span> <span data-ttu-id="3140f-442">O aplicativo de exemplo usa a classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-442">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="3140f-443">No código a seguir, um segundo serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-443">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3140f-444">Ele usa um delegado para configurar a associação com `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="3140f-444">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="3140f-445">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3140f-445">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="3140f-446">Adicione vários provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-446">You can add multiple configuration providers.</span></span> <span data-ttu-id="3140f-447">Os provedores de configuração estão disponíveis nos pacotes do NuGet e são aplicados na ordem em que são registrados.</span><span class="sxs-lookup"><span data-stu-id="3140f-447">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="3140f-448">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3140f-448">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3140f-449">Cada chamada à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adiciona um serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-449">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="3140f-450">No exemplo anterior, os valores `Option1` e `Option2` são especificados em *appsettings.json*, mas os valores `Option1` e `Option2` são substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="3140f-450">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="3140f-451">Quando mais de um serviço de configuração é habilitado, a última fonte de configuração especificada *vence* e define o valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="3140f-451">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="3140f-452">Quando o aplicativo é executado, o método `OnGet` do modelo de página retorna uma cadeia de caracteres que mostra os valores da classe de opção:</span><span class="sxs-lookup"><span data-stu-id="3140f-452">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="3140f-453">Configuração de subopções</span><span class="sxs-lookup"><span data-stu-id="3140f-453">Suboptions configuration</span></span>

<span data-ttu-id="3140f-454">A configuração de subopções é demonstrada no Exemplo &num;3 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-454">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="3140f-455">Os aplicativos devem criar classes de opções que pertencem a grupos de cenários específicos (classes) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3140f-455">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="3140f-456">Partes do aplicativo que exigem valores de configuração devem ter acesso apenas aos valores de configuração usados por elas.</span><span class="sxs-lookup"><span data-stu-id="3140f-456">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="3140f-457">Ao associar opções à configuração, cada propriedade no tipo de opções é associada a uma chave de configuração no formato `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="3140f-457">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="3140f-458">Por exemplo, a propriedade `MyOptions.Option1` é associada à chave `Option1`, que é lida da propriedade `option1` em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3140f-458">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="3140f-459">No código a seguir, um terceiro serviço <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-459">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3140f-460">Ele associa `MySubOptions` à seção `subsection` do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3140f-460">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="3140f-461">O método de extensão `GetSection` exige o pacote NuGet [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="3140f-461">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="3140f-462">Se o aplicativo usa o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), o pacote é automaticamente incluído.</span><span class="sxs-lookup"><span data-stu-id="3140f-462">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="3140f-463">O arquivo *appsettings.json* de exemplo define um membro `subsection` com chaves para `suboption1` e `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="3140f-463">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="3140f-464">A classe `MySubOptions` define as propriedades `SubOption1` e `SubOption2`, para armazenar os valores de opções (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-464">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="3140f-465">O método `OnGet` do modelo da página retorna uma cadeia de caracteres com os valores de opções (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-465">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="3140f-466">Quando o aplicativo é executado, o método `OnGet` retorna uma cadeia de caracteres que mostra os valores da classe de subopção:</span><span class="sxs-lookup"><span data-stu-id="3140f-466">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="3140f-467">Opções fornecidas por um modelo de exibição ou com a injeção de exibição direta</span><span class="sxs-lookup"><span data-stu-id="3140f-467">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="3140f-468">As opções fornecidas por um modelo de exibição ou com a injeção de exibição direta são demonstradas no Exemplo &num;4 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-468">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="3140f-469">As opções podem ser fornecidas em um modelo de exibição ou pela injeção de <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> diretamente em uma exibição (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-469">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="3140f-470">O aplicativo de exemplo mostra como injetar `IOptionsMonitor<MyOptions>` com uma diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="3140f-470">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="3140f-471">Quando o aplicativo é executado, os valores de opções são mostrados na página renderizada:</span><span class="sxs-lookup"><span data-stu-id="3140f-471">When the app is run, the options values are shown in the rendered page:</span></span>

![Os valores de opções Option1: value1_from_json e Option2: -1 são carregados do modelo e pela injeção na exibição.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="3140f-473">Recarregar dados de configuração com IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="3140f-473">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="3140f-474">O recarregamento de dados de configuração com <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é demonstrado no Exemplo &num;5 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-474">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="3140f-475">O <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> dá suporte a opções de recarregamento com sobrecarga mínima de processamento.</span><span class="sxs-lookup"><span data-stu-id="3140f-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="3140f-476">As opções são calculadas uma vez por solicitação, quando acessadas e armazenadas em cache durante o tempo de vida da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3140f-476">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="3140f-477">O exemplo a seguir demonstra como um novo <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> é criado após a alteração de *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="3140f-477">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="3140f-478">Várias solicitações ao servidor retornam valores constantes fornecidos pelo arquivo *appsettings.json*, até que o arquivo seja alterado e a configuração seja recarregada.</span><span class="sxs-lookup"><span data-stu-id="3140f-478">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="3140f-479">A seguinte imagem mostra is valores `option1` e `option2` iniciais carregados do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3140f-479">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="3140f-480">Altere os valores no arquivo *appsettings.json* para `value1_from_json UPDATED` e `200`.</span><span class="sxs-lookup"><span data-stu-id="3140f-480">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="3140f-481">Salve o arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3140f-481">Save the *appsettings.json* file.</span></span> <span data-ttu-id="3140f-482">Atualize o navegador para ver se os valores de opções foram atualizados:</span><span class="sxs-lookup"><span data-stu-id="3140f-482">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="3140f-483">Suporte de opções nomeadas com IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="3140f-483">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="3140f-484">O suporte de opções nomeadas com <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> é demonstrado como no Exemplo &num;6 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3140f-484">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="3140f-485">O suporte de *opções nomeadas* permite que o aplicativo faça a distinção entre as configurações de opções nomeadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-485">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="3140f-486">No aplicativo de exemplo, as opções nomeadas são declaradas com [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) que chama o método de extensão [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*):</span><span class="sxs-lookup"><span data-stu-id="3140f-486">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="3140f-487">O aplicativo de exemplo acessa as opções nomeadas com <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3140f-487">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="3140f-488">Executando o aplicativo de exemplo, as opções nomeadas são retornadas:</span><span class="sxs-lookup"><span data-stu-id="3140f-488">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="3140f-489">Os valores `named_options_1` são fornecidos pela configuração, que são carregados do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3140f-489">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="3140f-490">Os valores `named_options_2` são fornecidos pelo:</span><span class="sxs-lookup"><span data-stu-id="3140f-490">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="3140f-491">Delegado `named_options_2` em `ConfigureServices` para `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3140f-491">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="3140f-492">Valor padrão para `Option2` fornecido pela classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-492">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="3140f-493">Configurar todas as opções com o método ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="3140f-493">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="3140f-494">Configure todas as instâncias de opções com o método <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="3140f-494">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="3140f-495">O código a seguir configura `Option1` para todas as instâncias de configuração com um valor comum.</span><span class="sxs-lookup"><span data-stu-id="3140f-495">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="3140f-496">Adicione o seguinte código manualmente ao método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3140f-496">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="3140f-497">A execução do aplicativo de exemplo após a adição do código produz o seguinte resultado:</span><span class="sxs-lookup"><span data-stu-id="3140f-497">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="3140f-498">Todas as opções são instâncias nomeadas.</span><span class="sxs-lookup"><span data-stu-id="3140f-498">All options are named instances.</span></span> <span data-ttu-id="3140f-499">As instâncias <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existentes são tratadas como sendo direcionadas à instância `Options.DefaultName`, que é `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="3140f-499">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3140f-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> também implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="3140f-501">A implementação padrão de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> tem lógica para usar cada um de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="3140f-501">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="3140f-502">A opção nomeada `null` é usada para direcionar todas as instâncias nomeadas, em vez de uma instância nomeada específica (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> e <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> usam essa convenção).</span><span class="sxs-lookup"><span data-stu-id="3140f-502">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3140f-503">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="3140f-503">OptionsBuilder API</span></span>

<span data-ttu-id="3140f-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> é usada para configurar instâncias `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="3140f-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3140f-505">`OptionsBuilder` simplifica a criação de opções nomeadas, pois é apenas um único parâmetro para a chamada `AddOptions<TOptions>(string optionsName)` inicial, em vez de aparecer em todas as chamadas subsequentes.</span><span class="sxs-lookup"><span data-stu-id="3140f-505">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3140f-506">A validação de opções e as sobrecargas `ConfigureOptions` que aceitam dependências de serviço só estão disponíveis por meio de `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3140f-506">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="3140f-507">Usar os serviços de injeção de dependência para configurar as opções</span><span class="sxs-lookup"><span data-stu-id="3140f-507">Use DI services to configure options</span></span>

<span data-ttu-id="3140f-508">Você pode acessar outros serviços de injeção de dependência ao configurar as opções de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="3140f-508">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="3140f-509">Passar um delegado de configuração para [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) em [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="3140f-509">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="3140f-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fornece sobrecargas de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) que permitem que você use até cinco serviços para configurar as opções:</span><span class="sxs-lookup"><span data-stu-id="3140f-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="3140f-511">Criar seu próprio tipo que implementa <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> e registrar o tipo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-511">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="3140f-512">É recomendável transmitir um delegado de configuração para [Configurar](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), já que a criação de um serviço é algo mais complexo.</span><span class="sxs-lookup"><span data-stu-id="3140f-512">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="3140f-513">Criar seu próprio tipo é equivalente ao que a estrutura faz para você quando você usa [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="3140f-513">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="3140f-514">Chamar [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registra um genérico transitório <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, que tem um construtor que aceita os tipos de serviço genérico especificados.</span><span class="sxs-lookup"><span data-stu-id="3140f-514">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="3140f-515">Pós-configuração de opções</span><span class="sxs-lookup"><span data-stu-id="3140f-515">Options post-configuration</span></span>

<span data-ttu-id="3140f-516">Defina a pós-configuração com <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="3140f-516">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="3140f-517">A pós-configuração é executada depois que toda o configuração de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> é feita:</span><span class="sxs-lookup"><span data-stu-id="3140f-517">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3140f-518">O <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> está disponível para pós-configurar opções nomeadas:</span><span class="sxs-lookup"><span data-stu-id="3140f-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3140f-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> para pós-configurar todas as instâncias de configuração:</span><span class="sxs-lookup"><span data-stu-id="3140f-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3140f-520">Acessando opções durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="3140f-520">Accessing options during startup</span></span>

<span data-ttu-id="3140f-521"><xref:Microsoft.Extensions.Options.IOptions%601> e <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podem ser usados em `Startup.Configure`, pois os serviços são criados antes da execução do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="3140f-521"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3140f-522">Não use <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3140f-522">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3140f-523">Pode haver um estado inconsistente de opções devido à ordenação dos registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="3140f-523">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3140f-524">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3140f-524">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
