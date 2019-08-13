---
title: Extensibilidade de localização
author: hishamco
description: Saiba como estender as APIs de localização em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
uid: fundamentals/localization-extensibility
ms.openlocfilehash: 92fe954ea6bf5d0a8f9f62f4da696d197c51af04
ms.sourcegitcommit: 4fe3ae892f54dc540859bff78741a28c2daa9a38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2019
ms.locfileid: "68776751"
---
# <a name="localization-extensibility"></a>Extensibilidade de localização

Por [Hisham Bin Ateya](https://github.com/hishamco)

Este artigo:

* Lista os pontos de extensibilidade nas APIs de localização.
* Fornece instruções sobre como estender a localização de aplicativos ASP.NET Core.

## <a name="extensible-points-in-localization-apis"></a>Pontos extensíveis em APIs de localização

As APIs de localização ASP.NET Core foram criadas para serem extensíveis. A extensibilidade permite que os desenvolvedores personalizem a localização de acordo com suas necessidades. Por exemplo, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) tem um `POStringLocalizer`. `POStringLocalizer` descreve em detalhes o uso da [Localização de objeto portátil](xref:fundamentals/portable-object-localization) para usar arquivos `PO` para armazenar recursos de localização.

Este artigo lista os dois pontos de extensibilidade principais que as APIs de localização fornecem: 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Provedores de cultura de localização

As APIs de localização ASP.NET Core têm quatro provedores padrão que podem determinar a cultura atual de uma solicitação em execução:

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Os provedores anteriores são descritos em detalhes na documentação sobre [Middleware de localização](xref:fundamentals/localization). Se os provedores padrão não atenderem às suas necessidades, crie um provedor personalizado usando uma das seguintes abordagens:

### <a name="use-customrequestcultureprovider"></a>Usar CustomRequestCultureProvider

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> fornece um <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> personalizado que usa um delegado simples para determinar a cultura de localização atual:

::: moniker range=">= aspnetcore-2.2"

```csharp
options.AddInitialRequestCultureProvider(
    new CustomRequestCultureProvider(async context =>
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

::: moniker range="< aspnetcore-2.2"

```csharp
options.RequestCultureProviders.Insert(0, 
    new CustomRequestCultureProvider(async context =>
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

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>Usar uma nova implementação de RequestCultureProvider

Uma nova implementação de <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> pode ser criada para determinar as informações de cultura da solicitação de uma fonte personalizada. Por exemplo, a origem personalizada pode ser um arquivo de configuração ou um banco de dados.

O exemplo a seguir mostra `AppSettingsRequestCultureProvider`, que estende o <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> para determinar as informações de cultura da solicitação de *appsettings.json*:

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

## <a name="localization-resources"></a>Recursos de localização

A localização do ASP.NET Core fornece <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> é uma implementação de <xref:Microsoft.Extensions.Localization.IStringLocalizer> que usa `resx` para armazenar recursos de localização.

Você não fica limitado a usar arquivos `resx`. Implementando `IStringLocalized`, qualquer fonte de dados pode ser usada.

Os seguintes projetos de exemplo implementam <xref:Microsoft.Extensions.Localization.IStringLocalizer>: 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
