---
title: Metapacote Microsoft.AspNetCore.All para ASP.NET Core 2.0
author: Rick-Anderson
description: O metapacote Microsoft.AspNetCore.All não é recomendado para o ASP.NET Core 2.1 e posterior.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: e47f583d0fa75bdeb26b669303747a70619117c1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663145"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="49b53-103">Metapacote Microsoft.AspNetCore.All para ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="49b53-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49b53-104">O metapacote `Microsoft.AspNetCore.All` não está incluído no ASP.NET Core 3,0 e posterior.</span><span class="sxs-lookup"><span data-stu-id="49b53-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="49b53-105">Saiba mais neste [tópico do GitHub](https://github.com/aspnet/Announcements/issues/314).</span><span class="sxs-lookup"><span data-stu-id="49b53-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="49b53-106">Recomendamos que os aplicativos direcionados ao ASP.NET Core 2.1 e posterior usem o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) em vez desse pacote.</span><span class="sxs-lookup"><span data-stu-id="49b53-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="49b53-107">Veja [Migração do Microsoft.AspNetCore.All para Microsoft.AspNetCore.App](#migrate) neste artigo.</span><span class="sxs-lookup"><span data-stu-id="49b53-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="49b53-108">Este recurso exige o ASP.NET Core 2.x direcionado ao .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="49b53-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="49b53-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) é um metapacote que faz referência a uma estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="49b53-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="49b53-110">Uma *estrutura compartilhada* é um conjunto de assemblies (arquivos *.dll*) que não estão em pastas do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="49b53-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="49b53-111">A estrutura compartilhada deve ser instalada no computador para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="49b53-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="49b53-112">Saiba mais em [A estrutura compartilhada](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="49b53-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="49b53-113">A estrutura compartilhada a que `Microsoft.AspNetCore.All` se refere inclui:</span><span class="sxs-lookup"><span data-stu-id="49b53-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="49b53-114">Todos os pacotes com suporte da equipe do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49b53-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="49b53-115">Todos os pacotes com suporte pelo Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="49b53-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="49b53-116">Dependências internas e de terceiros usadas por ASP.NET Core e pelo Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="49b53-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="49b53-117">Todos os recursos do ASP.NET Core 2.x e do Entity Framework Core 2.x são incluídos no pacote `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="49b53-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="49b53-118">Os modelos de projeto padrão direcionados para ASP.NET Core 2.0 usam este pacote.</span><span class="sxs-lookup"><span data-stu-id="49b53-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="49b53-119">O número de versão do metapacote `Microsoft.AspNetCore.All` representa a versão mínima do ASP.NET Core e a versão do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="49b53-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="49b53-120">O seguinte arquivo *.csproj* referencia os metapacotes `Microsoft.AspNetCore.All` para o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="49b53-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="49b53-121">Controle de versão implícita</span><span class="sxs-lookup"><span data-stu-id="49b53-121">Implicit versioning</span></span>

<span data-ttu-id="49b53-122">No ASP.NET Core 2.1 ou posterior, você pode especificar a referência de pacote `Microsoft.AspNetCore.All` sem uma versão.</span><span class="sxs-lookup"><span data-stu-id="49b53-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="49b53-123">Quando a versão não for especificada, uma versão implícita será especificada pelo SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="49b53-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="49b53-124">Recomendamos que você conte com a versão implícita especificada pelo SDK, e não defina explicitamente o número de versão na referência de pacote.</span><span class="sxs-lookup"><span data-stu-id="49b53-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="49b53-125">Caso tenha dúvidas sobre essa abordagem, deixe um comentário no GitHub na [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430) (Discussão sobre a versão implícita do Microsoft.AspNetCore.App).</span><span class="sxs-lookup"><span data-stu-id="49b53-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="49b53-126">A versão implícita é definida como `major.minor.0` para aplicativos portátil.</span><span class="sxs-lookup"><span data-stu-id="49b53-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="49b53-127">O mecanismo de roll forward da estrutura compartilhada executará o aplicativo na versão compatível mais recente entre as estruturas compartilhadas instaladas.</span><span class="sxs-lookup"><span data-stu-id="49b53-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="49b53-128">Para garantir que a mesma versão seja usada no desenvolvimento, no teste e na produção, certifique-se de que a mesma versão da estrutura compartilhada seja instalada em todos os ambientes.</span><span class="sxs-lookup"><span data-stu-id="49b53-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="49b53-129">Para aplicativos autossuficientes, o número de versão implícita é definido como o `major.minor.patch` da estrutura compartilhada agrupada no SDK instalado.</span><span class="sxs-lookup"><span data-stu-id="49b53-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="49b53-130">A especificação de um número de versão na referência de pacote `Microsoft.AspNetCore.All`**não** assegura que a versão da estrutura compartilhada será escolhida.</span><span class="sxs-lookup"><span data-stu-id="49b53-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="49b53-131">Por exemplo, suponha que a versão "2.1.1" foi especificada, mas "2.1.3" está instalada.</span><span class="sxs-lookup"><span data-stu-id="49b53-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="49b53-132">Nesse caso, o aplicativo usará "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="49b53-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="49b53-133">Embora não seja recomendado, você pode desabilitar o roll forward (patch e/ou secundária).</span><span class="sxs-lookup"><span data-stu-id="49b53-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="49b53-134">Para obter mais informações sobre como efetuar roll forward do host dotnet e como configurar seu comportamento, veja [Efetuar roll forward do host dotnet](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="49b53-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="49b53-135">O SDK do projeto precisa ser definido como `Microsoft.NET.Sdk.Web` no arquivo de projeto para usar a versão implícita do `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="49b53-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="49b53-136">Quando o SDK `Microsoft.NET.Sdk` for especificado (`<Project Sdk="Microsoft.NET.Sdk">` na parte superior do arquivo de projeto), o seguinte aviso será gerado:</span><span class="sxs-lookup"><span data-stu-id="49b53-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="49b53-137">*Aviso NU1604: dependência do projeto Microsoft. AspNetCore. All não contém um limite inferior inclusivo. Inclua um limite inferior na versão de dependência para garantir resultados de restauração consistentes.*</span><span class="sxs-lookup"><span data-stu-id="49b53-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="49b53-138">Esse é um problema conhecido com o SDK do .NET Core 2.1 e será corrigido no SDK do .NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="49b53-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="49b53-139">Migração do Microsoft.AspNetCore.All para Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="49b53-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="49b53-140">Os pacotes a seguir estão incluídos no `Microsoft.AspNetCore.All`, mas não no pacote `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="49b53-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

<span data-ttu-id="49b53-141">Para mover de `Microsoft.AspNetCore.All` para `Microsoft.AspNetCore.App`, se seu aplicativo usa APIs dos pacotes acima ou de pacotes trazidos por eles, adicione referências a eles em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="49b53-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="49b53-142">Todas as dependências dos pacotes anteriores que, de outra forma, não são dependências de `Microsoft.AspNetCore.App` não são incluídas implicitamente.</span><span class="sxs-lookup"><span data-stu-id="49b53-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="49b53-143">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="49b53-143">For example:</span></span>

* <span data-ttu-id="49b53-144">`StackExchange.Redis` como uma dependência de `Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="49b53-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="49b53-145">`Microsoft.ApplicationInsights` como uma dependência de `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="49b53-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="49b53-146">Atualizar o ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="49b53-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="49b53-147">É recomendável migrar para o metapacote `Microsoft.AspNetCore.App` para a versão 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="49b53-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="49b53-148">Para continuar usando o metapacote `Microsoft.AspNetCore.All` e certificar-se de que a versão de patch mais recente foi implantada:</span><span class="sxs-lookup"><span data-stu-id="49b53-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="49b53-149">Em computadores de desenvolvimento e em servidores de build: instale o [SDK do .NET Core](https://www.microsoft.com/net/download) mais recente.</span><span class="sxs-lookup"><span data-stu-id="49b53-149">On development machines and build servers: Install the latest [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="49b53-150">Nos servidores de implantação: instale o [runtime do .NET Core](https://www.microsoft.com/net/download) mais recente.</span><span class="sxs-lookup"><span data-stu-id="49b53-150">On deployment servers: Install the latest [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>
 <span data-ttu-id="49b53-151">Seu aplicativo efetuará roll forward para a versão instalada mais recente em uma reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="49b53-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
