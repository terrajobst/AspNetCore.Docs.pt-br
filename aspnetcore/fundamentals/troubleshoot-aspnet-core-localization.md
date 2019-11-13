---
title: Solucionar problemas de Localização no ASP.NET Core
author: hishamco
description: Saiba como diagnosticar problemas com a localização em aplicativos do ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963342"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="a0d09-103">Solucionar problemas de Localização no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0d09-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="a0d09-104">Por [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="a0d09-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="a0d09-105">Este artigo contém instruções sobre como diagnosticar problemas de localização em aplicativos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0d09-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="a0d09-106">Problemas de configuração da localização</span><span class="sxs-lookup"><span data-stu-id="a0d09-106">Localization configuration issues</span></span>

<span data-ttu-id="a0d09-107">**Ordem do middleware de localização**</span><span class="sxs-lookup"><span data-stu-id="a0d09-107">**Localization middleware order**</span></span>  
<span data-ttu-id="a0d09-108">O aplicativo pode não localizar porque o middleware de localização não está ordenado conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="a0d09-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="a0d09-109">Para resolver esse problema, verifique se o middleware de localização foi registrado antes do middleware do MVC.</span><span class="sxs-lookup"><span data-stu-id="a0d09-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="a0d09-110">Caso contrário, o middleware de localização não será aplicado.</span><span class="sxs-lookup"><span data-stu-id="a0d09-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="a0d09-111">**Caminho dos recursos de localização não encontrado**</span><span class="sxs-lookup"><span data-stu-id="a0d09-111">**Localization resources path not found**</span></span>

<span data-ttu-id="a0d09-112">**As culturas compatíveis em RequestCultureProvider não correspondem ao registrado uma vez**</span><span class="sxs-lookup"><span data-stu-id="a0d09-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="a0d09-113">Problemas de nomenclatura do arquivo de recurso</span><span class="sxs-lookup"><span data-stu-id="a0d09-113">Resource file naming issues</span></span>

<span data-ttu-id="a0d09-114">O ASP.NET Core tem regras e diretrizes predefinidas para a nomenclatura de arquivos de recurso de localização, que são descritas em detalhes [aqui](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="a0d09-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="a0d09-115">Recursos ausentes</span><span class="sxs-lookup"><span data-stu-id="a0d09-115">Missing resources</span></span>

<span data-ttu-id="a0d09-116">As causas comuns para não localizar recursos incluem:</span><span class="sxs-lookup"><span data-stu-id="a0d09-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="a0d09-117">Nomes de recursos digitados incorretamente no arquivo `resx` ou na solicitação do localizador.</span><span class="sxs-lookup"><span data-stu-id="a0d09-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="a0d09-118">O recurso está ausente do `resx` para alguns idiomas, mas presente em outros.</span><span class="sxs-lookup"><span data-stu-id="a0d09-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="a0d09-119">Se ainda houver problemas, verifique as mensagens de log de localização (que estão no nível de log `Debug`) para saber mais detalhes sobre os recursos ausentes.</span><span class="sxs-lookup"><span data-stu-id="a0d09-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="a0d09-120">_**Dica:** Ao usar `CookieRequestCultureProvider`, verifique se aspas simples não são usadas com as culturas dentro do valor do cookie de localização. Por exemplo, `c='en-UK'|uic='en-US'` é um valor de cookie inválido, enquanto `c=en-UK|uic=en-US` é válido._</span><span class="sxs-lookup"><span data-stu-id="a0d09-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="a0d09-121">Problemas de bibliotecas de recursos e classes</span><span class="sxs-lookup"><span data-stu-id="a0d09-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="a0d09-122">Por padrão, o ASP.NET Core oferece uma maneira de permitir que as bibliotecas de classes localizem seus arquivos de recurso por meio de [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="a0d09-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="a0d09-123">Problemas comuns com bibliotecas de classes incluem:</span><span class="sxs-lookup"><span data-stu-id="a0d09-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="a0d09-124">Ausência do `ResourceLocationAttribute` em uma biblioteca de classes evitará que `ResourceManagerStringLocalizerFactory` descubra os recursos.</span><span class="sxs-lookup"><span data-stu-id="a0d09-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="a0d09-125">Nomenclatura do arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="a0d09-125">Resource file naming.</span></span> <span data-ttu-id="a0d09-126">Para saber mais, confira a seção [Problemas de nomenclatura do arquivo de recurso](#resource-file-naming-issues).</span><span class="sxs-lookup"><span data-stu-id="a0d09-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="a0d09-127">Altere o namespace raiz da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="a0d09-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="a0d09-128">Para saber mais, confira a seção [Problemas do namespace raiz](#root-namespace-issues).</span><span class="sxs-lookup"><span data-stu-id="a0d09-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="a0d09-129">CustomRequestCultureProvider não funciona conforme o esperado</span><span class="sxs-lookup"><span data-stu-id="a0d09-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="a0d09-130">A classe `RequestLocalizationOptions` tem três provedores padrão:</span><span class="sxs-lookup"><span data-stu-id="a0d09-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="a0d09-131">O [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) permite personalizar como a cultura de localização é fornecida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0d09-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="a0d09-132">O `CustomRequestCultureProvider` é usado quando os provedores padrão não atendem aos seus requisitos.</span><span class="sxs-lookup"><span data-stu-id="a0d09-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="a0d09-133">Um motivo comum para o provedor personalizado não funcionar corretamente é não ser o primeiro provedor na lista `RequestCultureProviders`.</span><span class="sxs-lookup"><span data-stu-id="a0d09-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="a0d09-134">Para resolver esse problema:</span><span class="sxs-lookup"><span data-stu-id="a0d09-134">To resolve this issue:</span></span>

- <span data-ttu-id="a0d09-135">Insira o provedor personalizado na posição 0 da lista `RequestCultureProviders`, desta maneira:</span><span class="sxs-lookup"><span data-stu-id="a0d09-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="a0d09-136">Use o método de extensão `AddInitialRequestCultureProvider` para definir o provedor personalizado como provedor inicial.</span><span class="sxs-lookup"><span data-stu-id="a0d09-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="a0d09-137">Problemas do namespace raiz</span><span class="sxs-lookup"><span data-stu-id="a0d09-137">Root Namespace issues</span></span>

<span data-ttu-id="a0d09-138">Quando o namespace raiz de um assembly for diferente do nome do assembly, a localização não funcionará por padrão.</span><span class="sxs-lookup"><span data-stu-id="a0d09-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="a0d09-139">Para evitar esse problema, use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), que é descrito em detalhes [aqui](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span><span class="sxs-lookup"><span data-stu-id="a0d09-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="a0d09-140">Isso pode ocorrer quando o nome de um projeto não é um identificador .NET válido.</span><span class="sxs-lookup"><span data-stu-id="a0d09-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="a0d09-141">Por exemplo, `my-project-name.csproj` usará o namespace raiz `my_project_name` e o nome do assembly `my-project-name` levando a esse erro.</span><span class="sxs-lookup"><span data-stu-id="a0d09-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="a0d09-142">Recursos e ação de build</span><span class="sxs-lookup"><span data-stu-id="a0d09-142">Resources & Build Action</span></span>

<span data-ttu-id="a0d09-143">Se você usa arquivos de recurso para localização, é importante que eles tenham uma ação de build correta.</span><span class="sxs-lookup"><span data-stu-id="a0d09-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="a0d09-144">Eles precisam ser um **Recurso inserido**; caso contrário, o `ResourceStringLocalizer` não conseguirá localizar os recursos.</span><span class="sxs-lookup"><span data-stu-id="a0d09-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
