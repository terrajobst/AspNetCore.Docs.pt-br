---
title: Usar assemblies de inicialização de hospedagem no ASP.NET Core
author: guardrex
description: Descubra como aprimorar um aplicativo ASP.NET Core por meio de um assembly externo usando uma implementação de IHostingStartup.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc,seodec18
ms.date: 11/22/2018
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 6c38242afee46b80bafcba47a8f77e2c05f6537e
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121720"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="c1768-103">Usar assemblies de inicialização de hospedagem no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1768-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="c1768-104">Por [Luke Latham](https://github.com/guardrex) e [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="c1768-104">By [Luke Latham](https://github.com/guardrex) and [Pavel Krymets](https://github.com/pakrym)</span></span>

<span data-ttu-id="c1768-105">Uma implementação [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (inicialização de hospedagem) adiciona melhorias a um aplicativo durante a inicialização de um assembly externo.</span><span class="sxs-lookup"><span data-stu-id="c1768-105">An [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="c1768-106">Por exemplo, uma biblioteca externa pode usar uma implementação de inicialização de hospedagem para fornecer serviços ou provedores de configuração adicionais a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span> <span data-ttu-id="c1768-107">`IHostingStartup` *está disponível no ASP.NET Core 2.0 ou posterior.*</span><span class="sxs-lookup"><span data-stu-id="c1768-107">`IHostingStartup` *is available in ASP.NET Core 2.0 or later.*</span></span>

<span data-ttu-id="c1768-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1768-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="c1768-109">Atributo HostingStartup</span><span class="sxs-lookup"><span data-stu-id="c1768-109">HostingStartup attribute</span></span>

<span data-ttu-id="c1768-110">Um atributo [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) indica a presença de um assembly de inicialização de hospedagem para ativar em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c1768-110">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="c1768-111">O assembly de entrada ou o assembly que contém a classe `Startup` é automaticamente examinado para o atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-111">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="c1768-112">A lista de assemblies a ser pesquisada para os atributos `HostingStartup` é carregada no tempo de execução da configuração em [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span><span class="sxs-lookup"><span data-stu-id="c1768-112">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span></span> <span data-ttu-id="c1768-113">A lista de assemblies para excluir da descoberta é carregada de [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span><span class="sxs-lookup"><span data-stu-id="c1768-113">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span></span> <span data-ttu-id="c1768-114">Para obter mais informações, consulte [Host da Web: assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies) e [Host da Web: assemblies de exclusão da inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="c1768-114">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="c1768-115">No exemplo a seguir, o namespace do assembly de inicialização de hospedagem é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="c1768-115">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="c1768-116">A classe que contém o código de inicialização de hospedagem é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="c1768-116">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="c1768-117">O atributo `HostingStartup` normalmente está localizado no arquivo de classe de implementação `IHostingStartup` do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-117">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="c1768-118">Descobrir assemblies de inicialização de hospedagem carregados</span><span class="sxs-lookup"><span data-stu-id="c1768-118">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="c1768-119">Para descobrir os assemblies de inicialização de hospedagem carregados, habilite o registro em log e verifique os logs do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-119">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="c1768-120">Erros que ocorrem quando os assemblies carregados são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="c1768-120">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="c1768-121">Os assemblies de inicialização de hospedagem carregados são registrados em log no nível Depuração e todos os erros são registrados.</span><span class="sxs-lookup"><span data-stu-id="c1768-121">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="c1768-122">Desabilitar o carregamento automático de assemblies de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="c1768-122">Disable automatic loading of hosting startup assemblies</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c1768-123">Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="c1768-123">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="c1768-124">Para impedir o carregamento de todos os assemblies de inicialização de hospedagem, defina o seguinte para `true` ou `1`:</span><span class="sxs-lookup"><span data-stu-id="c1768-124">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="c1768-125">Configuração do host [Impedir inicialização de hospedagem](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="c1768-125">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="c1768-126">A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="c1768-126">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="c1768-127">Para evitar o carregamento de assemblies específicos de inicialização de hospedagem, defina uma das opções a seguir como uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para excluir na inicialização:</span><span class="sxs-lookup"><span data-stu-id="c1768-127">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="c1768-128">Configuração do host [Assemblies de exclusão de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="c1768-128">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="c1768-129">A variável de ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="c1768-129">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c1768-130">Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, defina um dos seguintes como `true` ou `1`:</span><span class="sxs-lookup"><span data-stu-id="c1768-130">To disable automatic loading of hosting startup assemblies, set one of the following to `true` or `1`:</span></span>

* <span data-ttu-id="c1768-131">Configuração do host [Impedir inicialização de hospedagem](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="c1768-131">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
* <span data-ttu-id="c1768-132">A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="c1768-132">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

::: moniker-end

<span data-ttu-id="c1768-133">Se a configuração do host e a variável de ambiente estiverem definidas, a configuração do host controlará o comportamento.</span><span class="sxs-lookup"><span data-stu-id="c1768-133">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="c1768-134">A desabilitação de assemblies de inicialização de hospedagem usando a configuração do host ou a variável de ambiente desabilita o assembly globalmente e poderá desabilitar várias características de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-134">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="c1768-135">Projeto</span><span class="sxs-lookup"><span data-stu-id="c1768-135">Project</span></span>

<span data-ttu-id="c1768-136">Crie uma inicialização de hospedagem com qualquer um dos seguintes tipos de projeto:</span><span class="sxs-lookup"><span data-stu-id="c1768-136">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="c1768-137">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="c1768-137">Class library</span></span>](#class-library)
* [<span data-ttu-id="c1768-138">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="c1768-138">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="c1768-139">Biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="c1768-139">Class library</span></span>

<span data-ttu-id="c1768-140">Uma melhoria da inicialização de hospedagem pode ser fornecida em uma biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="c1768-140">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="c1768-141">A biblioteca contém um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-141">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="c1768-142">O [código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclui um aplicativo Razor Pages, *HostingStartupApp* e uma biblioteca de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="c1768-142">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="c1768-143">A biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="c1768-143">The class library:</span></span>

* <span data-ttu-id="c1768-144">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-144">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="c1768-145">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço à configuração do aplicativo usando o provedor de configuração na memória ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span><span class="sxs-lookup"><span data-stu-id="c1768-145">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span></span>
* <span data-ttu-id="c1768-146">Inclui um atributo `HostingStartup` que identifica o namespace e a classe de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-146">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="c1768-147">O método [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) da classe `ServiceKeyInjection` usa um [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) para adicionar melhorias a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-147">The `ServiceKeyInjection` class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="c1768-148">`IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo tempo de execução antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-148">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

<span data-ttu-id="c1768-149">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="c1768-149">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="c1768-150">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem da biblioteca de classes:</span><span class="sxs-lookup"><span data-stu-id="c1768-150">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="c1768-151">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c1768-151">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="c1768-152">O [código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) também inclui um projeto de pacote do NuGet que fornece uma inicialização de hospedagem separada, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="c1768-152">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="c1768-153">O pacote tem as mesmas características da biblioteca de classes descrita anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c1768-153">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="c1768-154">O pacote:</span><span class="sxs-lookup"><span data-stu-id="c1768-154">The package:</span></span>

* <span data-ttu-id="c1768-155">Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-155">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="c1768-156">`ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço para a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-156">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="c1768-157">Inclui um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-157">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="c1768-158">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="c1768-158">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="c1768-159">A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem do pacote:</span><span class="sxs-lookup"><span data-stu-id="c1768-159">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="c1768-160">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c1768-160">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="c1768-161">Aplicativo de console sem um ponto de entrada</span><span class="sxs-lookup"><span data-stu-id="c1768-161">Console app without an entry point</span></span>

<span data-ttu-id="c1768-162">*Essa abordagem só está disponível para aplicativos .NET Core, não para .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="c1768-162">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="c1768-163">Uma melhoria de inicialização de hospedagem dinâmica que não requer uma referência de tempo de compilação para a ativação pode ser fornecida em um aplicativo de console sem um ponto de entrada que contenha um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-163">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="c1768-164">Publicar o aplicativo de console produz um assembly de inicialização de hospedagem que pode ser consumido do repositório de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c1768-164">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="c1768-165">Um aplicativo de console sem um ponto de entrada é usado nesse processo porque:</span><span class="sxs-lookup"><span data-stu-id="c1768-165">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="c1768-166">Um arquivo de dependências é necessário para consumir a inicialização de hospedagem no assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-166">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="c1768-167">Um arquivo de dependências é um ativo de aplicativo executável que é produzido ao publicar um aplicativo, não uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="c1768-167">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="c1768-168">Uma biblioteca não pode ser adicionada diretamente ao [repositório de pacotes de tempo de execução](/dotnet/core/deploying/runtime-store), o que exige um projeto executável que tem como alvo o tempo de execução compartilhado.</span><span class="sxs-lookup"><span data-stu-id="c1768-168">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="c1768-169">Na criação de uma inicialização de hospedagem dinâmica:</span><span class="sxs-lookup"><span data-stu-id="c1768-169">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="c1768-170">Um assembly de inicialização de hospedagem é criado no aplicativo de console sem um ponto de entrada que:</span><span class="sxs-lookup"><span data-stu-id="c1768-170">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="c1768-171">Inclui uma classe que contém a implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-171">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="c1768-172">Inclui um atributo [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) para identificar a classe de implementação de `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-172">Includes a [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="c1768-173">O aplicativo de console é publicado para obter as dependências da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-173">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="c1768-174">Uma consequência de publicar o aplicativo de console é que as dependências não utilizadas são cortadas do arquivo de dependências.</span><span class="sxs-lookup"><span data-stu-id="c1768-174">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="c1768-175">O arquivo de dependências é modificado para definir o local de tempo de execução do assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-175">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="c1768-176">O assembly de inicialização de hospedagem e seu arquivo de dependências são colocados no repositório do pacote de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c1768-176">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="c1768-177">Para descobrir o assembly de inicialização de hospedagem e seu arquivo de dependências, eles são listados em um par de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="c1768-177">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="c1768-178">O assembly de aplicativo do console referencia o pacote [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="c1768-178">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="c1768-179">Um atributo [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) identifica uma classe como uma implementação de `IHostingStartup` para o carregamento e a execução durante a criação do [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span><span class="sxs-lookup"><span data-stu-id="c1768-179">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span></span> <span data-ttu-id="c1768-180">No seguinte exemplo, o namespace é `StartupEnhancement` e a classe é `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="c1768-180">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="c1768-181">Uma classe implementa `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-181">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="c1768-182">O método [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) da classe usa um [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) para adicionar melhorias a um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-182">The class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="c1768-183">`IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo tempo de execução antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-183">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="c1768-184">Ao criar um projeto `IHostingStartup`, o arquivo de dependências (*\*.deps.json*) define o local `runtime` do assembly como a pasta *bin*:</span><span class="sxs-lookup"><span data-stu-id="c1768-184">When building an `IHostingStartup` project, the dependencies file (*\*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="c1768-185">Apenas uma parte do arquivo é mostrada.</span><span class="sxs-lookup"><span data-stu-id="c1768-185">Only part of the file is shown.</span></span> <span data-ttu-id="c1768-186">O nome do assembly no exemplo é `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="c1768-186">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="c1768-187">Especificar o assembly de inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="c1768-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="c1768-188">Para uma biblioteca de classes ou inicialização de hospedagem fornecida pelo aplicativo de console, especifique o nome do assembly de inicialização de hospedagem na variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="c1768-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="c1768-189">A variável de ambiente é uma lista de assemblies delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="c1768-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="c1768-190">Apenas assemblies de inicialização de hospedagem são examinados quanto ao atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="c1768-191">Para o aplicativo de exemplo, *HostingStartupApp*, para descobrir as inicializações de hospedagem descritas anteriormente, a variável de ambiente é definida como o seguinte valor:</span><span class="sxs-lookup"><span data-stu-id="c1768-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="c1768-192">Um assembly de inicialização de hospedagem também pode ser definido usando a configuração do host [Assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="c1768-192">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="c1768-193">Quando há vários assemblies de inicialização de hospedagem, os métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) são executados na ordem em que os assemblies são listados.</span><span class="sxs-lookup"><span data-stu-id="c1768-193">When multiple hosting startup assembles are present, their [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="c1768-194">Ativação</span><span class="sxs-lookup"><span data-stu-id="c1768-194">Activation</span></span>

<span data-ttu-id="c1768-195">As opções para ativação da inicialização de hospedagem são:</span><span class="sxs-lookup"><span data-stu-id="c1768-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="c1768-196">[Repositório de tempo de execução](#runtime-store) &ndash; A ativação não requer uma referência de tempo de compilação para a ativação.</span><span class="sxs-lookup"><span data-stu-id="c1768-196">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="c1768-197">O aplicativo de exemplo coloca os arquivos de dependências e o assembly de inicialização de hospedagem em uma pasta, *implantação*, para facilitar a implantação da inicialização de hospedagem em um ambiente multicomputador.</span><span class="sxs-lookup"><span data-stu-id="c1768-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="c1768-198">A pasta *implantação* também inclui um script do PowerShell que cria ou modifica variáveis de ambiente no sistema de implantação para habilitar a inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="c1768-199">Referência de tempo de compilação necessária para a ativação</span><span class="sxs-lookup"><span data-stu-id="c1768-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="c1768-200">Pacote do NuGet</span><span class="sxs-lookup"><span data-stu-id="c1768-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="c1768-201">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="c1768-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="c1768-202">Repositório de tempo de execução</span><span class="sxs-lookup"><span data-stu-id="c1768-202">Runtime store</span></span>

<span data-ttu-id="c1768-203">A implementação de inicialização de hospedagem é colocada no [repositório de tempo de execução](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="c1768-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="c1768-204">Uma referência de tempo de compilação para o assembly não é exigida pelo aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="c1768-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="c1768-205">Depois que a inicialização de hospedagem é compilada, um repositório de tempo de execução é gerado, usando o arquivo de projeto do manifesto e o comando do [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="c1768-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```console
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="c1768-206">No aplicativo de exemplo (projeto *RuntimeStore*) é usado o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c1768-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

``` console
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="c1768-207">Para o tempo de execução descobrir o repositório de tempo de execução, o local do repositório de tempo de execução é adicionado à variável de ambiente `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="c1768-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="c1768-208">**Modificar e colocar o arquivo de dependências da inicialização de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="c1768-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="c1768-209">Para ativar o aprimoramento sem uma referência de pacote ao aprimoramento, especifique as dependências adicionais do tempo de execução com `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="c1768-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="c1768-210">`additionalDeps` permite que você:</span><span class="sxs-lookup"><span data-stu-id="c1768-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="c1768-211">Amplie o grafo de biblioteca do aplicativo, fornecendo um conjunto de arquivos *\*.deps.json* adicionais a serem mesclados com o arquivo *\*.deps.json* próprio do aplicativo na inicialização.</span><span class="sxs-lookup"><span data-stu-id="c1768-211">Extend the app's library graph by providing a set of additional *\*.deps.json* files to merge with the app's own *\*.deps.json* file on startup.</span></span>
* <span data-ttu-id="c1768-212">Torne o assembly de inicialização de hospedagem detectável e carregável.</span><span class="sxs-lookup"><span data-stu-id="c1768-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="c1768-213">A abordagem recomendada para gerar o arquivo de dependências adicionais é:</span><span class="sxs-lookup"><span data-stu-id="c1768-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="c1768-214">Executar o `dotnet publish` no arquivo de manifesto do repositório de tempo de execução mencionado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="c1768-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="c1768-215">Remover a referência do manifesto das bibliotecas e a seção `runtime` resultantes do arquivo *\*deps.json*.</span><span class="sxs-lookup"><span data-stu-id="c1768-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *\*deps.json* file.</span></span>

<span data-ttu-id="c1768-216">No projeto de exemplo, a propriedade `store.manifest/1.0.0` é removida das seções `targets` e `libraries`:</span><span class="sxs-lookup"><span data-stu-id="c1768-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="c1768-217">Coloque o arquivo *\*.deps.json* no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="c1768-217">Place the *\*.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="c1768-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Local adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="c1768-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="c1768-219">`{SHARED FRAMEWORK NAME}` &ndash; Estrutura compartilhada necessária para esse arquivo de dependências adicionais.</span><span class="sxs-lookup"><span data-stu-id="c1768-219">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="c1768-220">`{SHARED FRAMEWORK VERSION}` &ndash; Versão mínima de estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="c1768-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="c1768-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; Nome do assembly do aprimoramento.</span><span class="sxs-lookup"><span data-stu-id="c1768-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="c1768-222">No aplicativo de exemplo (projeto *RuntimeStore*), o arquivo de dependências adicionais é colocado no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="c1768-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="c1768-223">Para o tempo de execução descobrir o local do repositório de tempo de execução, o local do arquivo de dependências adicionais é adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="c1768-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="c1768-224">No aplicativo de exemplo (projeto *RuntimeStore*), crie o repositório de tempo de execução e gere o arquivo de dependências adicionais usando um script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="c1768-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="c1768-225">Para obter exemplos de como definir variáveis de ambiente para vários sistemas operacionais, confira [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="c1768-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="c1768-226">**Implantação**</span><span class="sxs-lookup"><span data-stu-id="c1768-226">**Deployment**</span></span>

<span data-ttu-id="c1768-227">Para facilitar a implantação de uma inicialização de hospedagem em um ambiente multicomputador, o aplicativo de exemplo cria uma pasta *implantação* na saída publicada que contém:</span><span class="sxs-lookup"><span data-stu-id="c1768-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="c1768-228">O repositório de tempo de execução de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="c1768-229">O arquivo de dependências de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="c1768-230">Um script do PowerShell que cria ou modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` para dar suporte à ativação da inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="c1768-231">Execute o script de um prompt de comando do PowerShell administrativo no sistema de implantação.</span><span class="sxs-lookup"><span data-stu-id="c1768-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="c1768-232">Pacote NuGet</span><span class="sxs-lookup"><span data-stu-id="c1768-232">NuGet package</span></span>

<span data-ttu-id="c1768-233">Uma melhoria da inicialização de hospedagem pode ser fornecida em pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="c1768-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="c1768-234">O pacote tem um atributo `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c1768-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="c1768-235">Os tipos de inicialização de hospedagem fornecidos pelo pacote são disponibilizados para o aplicativo usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="c1768-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="c1768-236">O arquivo de projeto do aplicativo aprimorado faz uma referência de pacote para a inicialização de hospedagem no arquivo de projeto do aplicativo (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="c1768-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="c1768-237">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*\*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="c1768-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="c1768-238">Essa abordagem se aplica a um pacote de assembly de inicialização de hospedagem publicado para [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="c1768-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="c1768-239">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de tempo de execução](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="c1768-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="c1768-240">Para obter mais informações sobre pacotes do NuGet e o repositório de tempo de execução, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="c1768-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="c1768-241">Como criar um pacote do NuGet com ferramentas de plataforma cruzada</span><span class="sxs-lookup"><span data-stu-id="c1768-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="c1768-242">Publicando pacotes</span><span class="sxs-lookup"><span data-stu-id="c1768-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="c1768-243">Repositório de pacote de tempo de execução</span><span class="sxs-lookup"><span data-stu-id="c1768-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="c1768-244">Pasta Lixeira do projeto</span><span class="sxs-lookup"><span data-stu-id="c1768-244">Project bin folder</span></span>

<span data-ttu-id="c1768-245">Uma melhoria da inicialização de hospedagem pode ser fornecida por um assemby implantado na *lixeira* no aplicativo aprimorado.</span><span class="sxs-lookup"><span data-stu-id="c1768-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="c1768-246">Os tipos de inicialização de hospedagem fornecidos pelo assembly são disponibilizados para o aplicativo usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="c1768-246">The hosting startup types provided by the assembly are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="c1768-247">O arquivo de projeto do aplicativo aprimorado faz uma referência de assembly para a inicialização de hospedagem (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="c1768-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="c1768-248">Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo (*\*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="c1768-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="c1768-249">Essa abordagem é aplicável quando o cenário de implantação faz chamadas para mover o assembly compilado da biblioteca de inicialização de hospedagem (arquivo DLL) para o projeto consumidor ou para um local acessível pelo projeto consumidor e uma referência de tempo de compilação é feita para o assembly de inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-249">This approach applies when the deployment scenario calls for moving the compiled hosting startup library's assembly (DLL file) to the consuming project or to a location accessible by the consuming project and a compile-time reference is made to the hosting startup's assembly.</span></span>
* <span data-ttu-id="c1768-250">O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de tempo de execução](#runtime-store) (sem uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="c1768-250">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

## <a name="sample-code"></a><span data-ttu-id="c1768-251">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="c1768-251">Sample code</span></span>

<span data-ttu-id="c1768-252">O [código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample)) demonstra cenários de implementação de inicialização de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="c1768-252">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="c1768-253">Dois assemblies de inicialização de hospedagem (bibliotecas de classes) definem um par chave-valor de configuração na memória cada:</span><span class="sxs-lookup"><span data-stu-id="c1768-253">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="c1768-254">Pacote do NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="c1768-254">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="c1768-255">Biblioteca de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="c1768-255">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="c1768-256">Uma inicialização de hospedagem é ativada de um assembly implantado pelo repositório de tempo de execução (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="c1768-256">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="c1768-257">O assembly adiciona dois middlewares ao aplicativo na inicialização que fornecem informações de diagnóstico sobre:</span><span class="sxs-lookup"><span data-stu-id="c1768-257">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="c1768-258">Serviços registrados</span><span class="sxs-lookup"><span data-stu-id="c1768-258">Registered services</span></span>
  * <span data-ttu-id="c1768-259">Endereço (esquema, host, base do caminho, caminho, cadeia de caracteres de consulta)</span><span class="sxs-lookup"><span data-stu-id="c1768-259">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="c1768-260">Conexão (IP remoto, porta remota, IP local, porta local, certificado do cliente)</span><span class="sxs-lookup"><span data-stu-id="c1768-260">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="c1768-261">Cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="c1768-261">Request headers</span></span>
  * <span data-ttu-id="c1768-262">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="c1768-262">Environment variables</span></span>

<span data-ttu-id="c1768-263">Para executar a amostra:</span><span class="sxs-lookup"><span data-stu-id="c1768-263">To run the sample:</span></span>

<span data-ttu-id="c1768-264">**Ativação de um pacote do NuGet**</span><span class="sxs-lookup"><span data-stu-id="c1768-264">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="c1768-265">Compile o pacote *HostingStartupPackage* com o comando [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="c1768-265">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="c1768-266">Adicione o nome do assembly do pacote do *HostingStartupPackage* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="c1768-266">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="c1768-267">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-267">Compile and run the app.</span></span> <span data-ttu-id="c1768-268">Uma referência de pacote está presente no aplicativo aprimorado (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="c1768-268">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="c1768-269">Um `<PropertyGroup>` no arquivo de projeto do aplicativo especifica a saída do projeto de pacote (*../HostingStartupPackage/bin/Debug*) como uma origem de pacote.</span><span class="sxs-lookup"><span data-stu-id="c1768-269">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="c1768-270">Isso permite que o aplicativo use o pacote sem carregar o pacote para [nuget.org](https://www.nuget.org/). Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="c1768-270">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```
1. <span data-ttu-id="c1768-271">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` do pacote.</span><span class="sxs-lookup"><span data-stu-id="c1768-271">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="c1768-272">Se você fizer alterações no projeto *HostingStartupPackage* e recompilá-lo, limpe os caches de pacote do NuGet locais para garantir que o *HostingStartupApp* receba o pacote atualizado e não um pacote obsoleto do cache local.</span><span class="sxs-lookup"><span data-stu-id="c1768-272">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="c1768-273">Para limpar os caches locais do NuGet, execute o seguinte comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="c1768-273">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```console
dotnet nuget locals all --clear
```

<span data-ttu-id="c1768-274">**Ativação de uma biblioteca de classes**</span><span class="sxs-lookup"><span data-stu-id="c1768-274">**Activation from a class library**</span></span>

1. <span data-ttu-id="c1768-275">Compile a biblioteca de classes *HostingStartupLibrary* com o comando [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="c1768-275">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="c1768-276">Adicione nome do assembly da biblioteca de classes do *HostingStartupLibrary* à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="c1768-276">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="c1768-277">A pasta *lixeira* implanta o assembly da biblioteca de classes para o aplicativo ao copiar o arquivo *HostingStartupLibrary.dll* da saída compilada da biblioteca de classes para a pasta *lixeira/Depurar* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-277">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="c1768-278">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-278">Compile and run the app.</span></span> <span data-ttu-id="c1768-279">Um `<ItemGroup>` do arquivo de projeto do aplicativo referencia o assembly da biblioteca de classes (*.\lixeira\Depurar\netcoreapp2.1\HostingStartupLibrary.dll*) (uma referência de tempo de compilação).</span><span class="sxs-lookup"><span data-stu-id="c1768-279">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="c1768-280">Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="c1768-280">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```
1. <span data-ttu-id="c1768-281">Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="c1768-281">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="c1768-282">**Ativação de um assembly implantado pelo repositório de tempo de execução**</span><span class="sxs-lookup"><span data-stu-id="c1768-282">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="c1768-283">O projeto *StartupDiagnostics* usa o [PowerShell](/powershell/scripting/powershell-scripting) para modificar seu arquivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="c1768-283">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="c1768-284">O PowerShell é instalado por padrão em um sistema operacional Windows começando no Windows 7 SP1 e no Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="c1768-284">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="c1768-285">Para obter o PowerShell em outras plataformas, confira [Instalando o Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span><span class="sxs-lookup"><span data-stu-id="c1768-285">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="c1768-286">Compilar o projeto *StartupDiagnostics*.</span><span class="sxs-lookup"><span data-stu-id="c1768-286">Build the *StartupDiagnostics* project.</span></span> <span data-ttu-id="c1768-287">Depois que o projeto é compilado, um destino de compilação no arquivo de projeto automaticamente:</span><span class="sxs-lookup"><span data-stu-id="c1768-287">After the project is built, a build target in the project file automatically:</span></span>
   * <span data-ttu-id="c1768-288">Dispara o script do PowerShell para modificar o arquivo *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="c1768-288">Triggers the PowerShell script to modify the *StartupDiagnostics.deps.json* file.</span></span>
   * <span data-ttu-id="c1768-289">Move o arquivo *StartupDiagnostics.deps.json* para a pasta *additionalDeps* do perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="c1768-289">Moves the *StartupDiagnostics.deps.json* file to the user profile's *additionalDeps* folder.</span></span>
1. <span data-ttu-id="c1768-290">Execute o comando `dotnet store` em um prompt de comando no diretório de inicialização de hospedagem para armazenar o assembly e suas dependências no repositório de tempo de execução do perfil do usuário:</span><span class="sxs-lookup"><span data-stu-id="c1768-290">Execute the `dotnet store` command at a command prompt in the hosting startup's directory to store the assembly and its dependencies in the user profile's runtime store:</span></span>

   ```console
   dotnet store --manifest StartupDiagnostics.csproj --runtime <RID>
   ```
   <span data-ttu-id="c1768-291">Para Windows, o comando usa o `win7-x64` [RID (identificador de tempo de execução)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="c1768-291">For Windows, the command uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="c1768-292">Ao fornecer a inicialização de hospedagem para um tempo de execução diferente, substitua o RID correto.</span><span class="sxs-lookup"><span data-stu-id="c1768-292">When providing the hosting startup for a different runtime, substitute the correct RID.</span></span>
1. <span data-ttu-id="c1768-293">Defina as variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="c1768-293">Set the environment variables:</span></span>
   * <span data-ttu-id="c1768-294">Adicione o nome do assembly de *StartupDiagnostics* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="c1768-294">Add the assembly name of *StartupDiagnostics* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="c1768-295">No Windows, defina a variável de ambiente `DOTNET_ADDITIONAL_DEPS` como `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\`.</span><span class="sxs-lookup"><span data-stu-id="c1768-295">On Windows, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\`.</span></span> <span data-ttu-id="c1768-296">No macOS/Linux, defina a variável de ambiente `DOTNET_ADDITIONAL_DEPS` como `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/`, em que `<USER>` é o perfil do usuário que contém a inicialização de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c1768-296">On macOS/Linux, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/`, where `<USER>` is the user profile that contains the hosting startup.</span></span>
1. <span data-ttu-id="c1768-297">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="c1768-297">Run the sample app.</span></span>
1. <span data-ttu-id="c1768-298">Solicite o ponto de extremidade `/services` para ver os serviços registrados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1768-298">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="c1768-299">Solicite o ponto de extremidade `/diag` para ver as informações de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="c1768-299">Request the `/diag` endpoint to see the diagnostic information.</span></span>
