---
title: Metapacote Microsoft. AspNetCore. app para ASP.NET Core
author: Rick-Anderson
description: A estrutura compartilhada Microsoft. AspNetCore. app
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 3ce74bc7329a88ffc6f77baf6b8a311c02951318
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663138"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="97136-103">Microsoft. AspNetCore. app para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="97136-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="97136-104">A ASP.NET Core estrutura compartilhada (`Microsoft.AspNetCore.App`) contém assemblies que são desenvolvidos e suportados pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="97136-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="97136-105">o `Microsoft.AspNetCore.App` é instalado quando o [SDK do .NET Core 3,0 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.0) está instalado.</span><span class="sxs-lookup"><span data-stu-id="97136-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="97136-106">A *estrutura compartilhada* é o conjunto de assemblies (arquivos *. dll* ) que estão instalados no computador e inclui um componente de tempo de execução e um pacote de direcionamento.</span><span class="sxs-lookup"><span data-stu-id="97136-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="97136-107">Saiba mais em [A estrutura compartilhada](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="97136-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="97136-108">Projetos que visam o SDK do `Microsoft.NET.Sdk.Web` referenciam implicitamente a estrutura de `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="97136-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="97136-109">Nenhuma referência adicional é necessária para esses projetos:</span><span class="sxs-lookup"><span data-stu-id="97136-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="97136-110">A estrutura compartilhada ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="97136-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="97136-111">Não inclui dependências de terceiros.</span><span class="sxs-lookup"><span data-stu-id="97136-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="97136-112">Inclui todos os pacotes com suporte pela equipe de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97136-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="97136-113">Este recurso exige o ASP.NET Core 2.x direcionado ao .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="97136-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="97136-114">O [metapacote](/dotnet/core/packages#metapackages) [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="97136-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="97136-115">Não tem dependências de terceiros, com exceção de [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) e [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="97136-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="97136-116">Essas dependências de terceiros são consideradas necessárias para garantir o funcionamento dos principais recursos das estruturas.</span><span class="sxs-lookup"><span data-stu-id="97136-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="97136-117">Inclui todos os pacotes com suporte pela equipe do ASP.NET Core, exceto aqueles que contêm dependências de terceiros (que não sejam aqueles mencionados anteriormente).</span><span class="sxs-lookup"><span data-stu-id="97136-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="97136-118">Inclui todos os pacotes com suporte pela equipe do Entity Framework Core, exceto aqueles que contêm dependências de terceiros (que não sejam aqueles mencionados anteriormente).</span><span class="sxs-lookup"><span data-stu-id="97136-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="97136-119">Todos os recursos do ASP.NET Core 2.x e do Entity Framework Core 2.x são incluídos no pacote `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="97136-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="97136-120">Os modelos de projeto padrão direcionados ASP.NET Core 2. x usam este pacote.</span><span class="sxs-lookup"><span data-stu-id="97136-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="97136-121">Recomendamos que os aplicativos que visam ASP.NET Core 2. x e Entity Framework Core 2. x usem o pacote `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="97136-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="97136-122">O número de versão do metapacote `Microsoft.AspNetCore.App` representa a versão mínima do ASP.NET Core e a versão do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="97136-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="97136-123">O uso do metapacote `Microsoft.AspNetCore.App` fornece restrições de versões que protegem seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="97136-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="97136-124">Se um pacote incluído tem uma dependência (não direta) transitiva em um pacote no `Microsoft.AspNetCore.App`, e os números de versão forem diferentes, o NuGet gera um erro.</span><span class="sxs-lookup"><span data-stu-id="97136-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="97136-125">Outros pacotes adicionados ao seu aplicativo não podem alterar a versão dos pacotes incluídos no `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="97136-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="97136-126">A consistência de versão garante uma experiência confiável.</span><span class="sxs-lookup"><span data-stu-id="97136-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="97136-127">`Microsoft.AspNetCore.App` foi projetado para evitar combinações de versão não testado de bits relacionados que estão sendo usados juntos no mesmo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97136-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="97136-128">Aplicativos que usam o metapacote `Microsoft.AspNetCore.App` aproveitam automaticamente a estrutura compartilhada do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97136-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="97136-129">Quando você usa o metapacote `Microsoft.AspNetCore.App`, **nenhum** ativo dos pacotes NuGet do ASP.NET Core referenciados é implantado com o aplicativo, porque a estrutura compartilhada do ASP.NET Core contém esses ativos.</span><span class="sxs-lookup"><span data-stu-id="97136-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="97136-130">Os ativos na estrutura compartilhada são pré-compilados para melhorar o tempo de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97136-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="97136-131">Saiba mais em [A estrutura compartilhada](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="97136-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="97136-132">O arquivo de projeto a seguir referencia o metapacote `Microsoft.AspNetCore.App` para ASP.NET Core e representa um modelo típico ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="97136-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="97136-133">A marcação anterior representa um modelo típico de ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="97136-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="97136-134">Ela não especifica um número de versão para a referência de pacote `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="97136-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="97136-135">Quando a versão não for especificada, uma versão [implícita](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) será especificada pelo SDK, ou seja, `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="97136-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="97136-136">Recomendamos que você conte com a versão implícita especificada pelo SDK, e não defina explicitamente o número de versão na referência de pacote.</span><span class="sxs-lookup"><span data-stu-id="97136-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="97136-137">Se tiver dúvidas sobre essa abordagem, deixe um comentário do GitHub na [Discussão para a versão implícita do Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="97136-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="97136-138">A versão implícita é definida como `major.minor.0` para aplicativos portátil.</span><span class="sxs-lookup"><span data-stu-id="97136-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="97136-139">O mecanismo de roll forward estrutura compartilhada executará o aplicativo na versão compatível mais recente entre as estruturas compartilhadas instaladas.</span><span class="sxs-lookup"><span data-stu-id="97136-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="97136-140">Para garantir que a mesma versão seja usada no desenvolvimento, no teste e na produção, certifique-se de que a mesma versão da estrutura compartilhada seja instalada em todos os ambientes.</span><span class="sxs-lookup"><span data-stu-id="97136-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="97136-141">Para aplicativos independentes, o número de versão implícita é definido como `major.minor.patch` da estrutura compartilhada incluída no SDK instalado.</span><span class="sxs-lookup"><span data-stu-id="97136-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="97136-142">Especificar um número de versão na referência `Microsoft.AspNetCore.App`**não** garante que a versão da estrutura compartilhada será escolhida.</span><span class="sxs-lookup"><span data-stu-id="97136-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="97136-143">Por exemplo, suponha que a versão "2.2.1" seja especificada, mas "2.2.3" esteja instalado.</span><span class="sxs-lookup"><span data-stu-id="97136-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="97136-144">Nesse caso, o aplicativo usará "2.2.3".</span><span class="sxs-lookup"><span data-stu-id="97136-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="97136-145">Embora não seja recomendado, você pode desabilitar o roll forward (patch e/ou secundária).</span><span class="sxs-lookup"><span data-stu-id="97136-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="97136-146">Para obter mais informações sobre como efetuar roll forward do host dotnet e como configurar seu comportamento, veja [Efetuar roll forward do host dotnet](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="97136-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="97136-147">`<Project Sdk` deve ser definido como `Microsoft.NET.Sdk.Web` para usar o `Microsoft.AspNetCore.App` da versão implícita.</span><span class="sxs-lookup"><span data-stu-id="97136-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="97136-148">Quando `<Project Sdk="Microsoft.NET.Sdk">` (sem o `.Web` à direita) é usado:</span><span class="sxs-lookup"><span data-stu-id="97136-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="97136-149">O aviso a seguir é gerado:</span><span class="sxs-lookup"><span data-stu-id="97136-149">The following warning is generated:</span></span>

  <span data-ttu-id="97136-150">*Aviso NU1604: dependência do projeto Microsoft. AspNetCore. app não contém um limite inferior inclusivo. Inclua um limite inferior na versão de dependência para garantir resultados de restauração consistentes.*</span><span class="sxs-lookup"><span data-stu-id="97136-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="97136-151">Esse é um problema conhecido do SDK do .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="97136-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="97136-152">Atualizar o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="97136-152">Update ASP.NET Core</span></span>

<span data-ttu-id="97136-153">O [metapacote](/dotnet/core/packages#metapackages) `Microsoft.AspNetCore.App` não é um pacote tradicional que é atualizado do NuGet.</span><span class="sxs-lookup"><span data-stu-id="97136-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="97136-154">Semelhante ao `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` representa um runtime compartilhado, que tem semântica de controle de versão especial tratada fora do NuGet.</span><span class="sxs-lookup"><span data-stu-id="97136-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="97136-155">Para obter mais informações, veja [Pacotes, metapacotes e estruturas](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="97136-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="97136-156">Para atualizar o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="97136-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="97136-157">Em computadores de desenvolvimento e servidores de compilação: baixe e instale o [SDK do .NET Core](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="97136-157">On development machines and build servers: Download and install the [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="97136-158">Nos servidores de implantação: baixe e instale o [runtime do .NET Core](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="97136-158">On deployment servers: Download and install the [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>

 <span data-ttu-id="97136-159">Os aplicativos efetuarão roll forward para a versão mais recente instalada na reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97136-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="97136-160">Não é necessário atualizar o número de versão `Microsoft.AspNetCore.App` no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="97136-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="97136-161">Para obter mais informações, consulte [Roll forward de aplicativos dependentes de estrutura](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="97136-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="97136-162">Se seu aplicativo tiver usado `Microsoft.AspNetCore.All`, veja [Migração do Microsoft.AspNetCore.All para Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="97136-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
