---
title: Extensibilidade de localização
author: hishamco
description: Saiba como estender as APIs de localização em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
uid: fundamentals/localization-extensibility
ms.openlocfilehash: dfa2efe78b2e1e118e6b3f09bfc41f3330e1d721
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288947"
---
# <a name="localization-extensibility"></a><span data-ttu-id="513be-103">Extensibilidade de localização</span><span class="sxs-lookup"><span data-stu-id="513be-103">Localization Extensibility</span></span>

<span data-ttu-id="513be-104">Por [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="513be-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="513be-105">Este artigo:</span><span class="sxs-lookup"><span data-stu-id="513be-105">This article:</span></span>

* <span data-ttu-id="513be-106">Lista os pontos de extensibilidade nas APIs de localização.</span><span class="sxs-lookup"><span data-stu-id="513be-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="513be-107">Fornece instruções sobre como estender a localização de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="513be-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="513be-108">Pontos extensíveis em APIs de localização</span><span class="sxs-lookup"><span data-stu-id="513be-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="513be-109">As APIs de localização ASP.NET Core foram criadas para serem extensíveis.</span><span class="sxs-lookup"><span data-stu-id="513be-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="513be-110">A extensibilidade permite que os desenvolvedores personalizem a localização de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="513be-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="513be-111">Por exemplo, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) tem um `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="513be-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="513be-112">`POStringLocalizer` descreve em detalhes o uso da [Localização de objeto portátil](xref:fundamentals/portable-object-localization) para usar arquivos `PO` para armazenar recursos de localização.</span><span class="sxs-lookup"><span data-stu-id="513be-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="513be-113">Este artigo lista os dois pontos de extensibilidade principais que as APIs de localização fornecem:</span><span class="sxs-lookup"><span data-stu-id="513be-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="513be-114">Provedores de cultura de localização</span><span class="sxs-lookup"><span data-stu-id="513be-114">Localization Culture Providers</span></span>

<span data-ttu-id="513be-115">As APIs de localização ASP.NET Core têm quatro provedores padrão que podem determinar a cultura atual de uma solicitação em execução:</span><span class="sxs-lookup"><span data-stu-id="513be-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="513be-116">Os provedores anteriores são descritos em detalhes na documentação sobre [Middleware de localização](xref:fundamentals/localization).</span><span class="sxs-lookup"><span data-stu-id="513be-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="513be-117">Se os provedores padrão não atenderem às suas necessidades, crie um provedor personalizado usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="513be-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="513be-118">Usar CustomRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="513be-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="513be-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> fornece um <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> personalizado que usa um delegado simples para determinar a cultura de localização atual:</span><span class="sxs-lookup"><span data-stu-id="513be-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="513be-120">Usar uma nova implementação de RequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="513be-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="513be-121">Uma nova implementação de <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> pode ser criada para determinar as informações de cultura da solicitação de uma fonte personalizada.</span><span class="sxs-lookup"><span data-stu-id="513be-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="513be-122">Por exemplo, a origem personalizada pode ser um arquivo de configuração ou um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="513be-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="513be-123">O exemplo a seguir mostra `AppSettingsRequestCultureProvider`, que estende o <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> para determinar as informações de cultura da solicitação de *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="513be-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a><span data-ttu-id="513be-124">Recursos de localização</span><span class="sxs-lookup"><span data-stu-id="513be-124">Localization resources</span></span>

<span data-ttu-id="513be-125">A localização do ASP.NET Core fornece <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span><span class="sxs-lookup"><span data-stu-id="513be-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="513be-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> é uma implementação de <xref:Microsoft.Extensions.Localization.IStringLocalizer> que usa `resx` para armazenar recursos de localização.</span><span class="sxs-lookup"><span data-stu-id="513be-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="513be-127">Você não fica limitado a usar arquivos `resx`.</span><span class="sxs-lookup"><span data-stu-id="513be-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="513be-128">Implementando `IStringLocalized`, qualquer fonte de dados pode ser usada.</span><span class="sxs-lookup"><span data-stu-id="513be-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="513be-129">Os seguintes projetos de exemplo implementam <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span><span class="sxs-lookup"><span data-stu-id="513be-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="513be-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="513be-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="513be-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="513be-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="513be-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="513be-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
