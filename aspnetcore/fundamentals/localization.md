---
title: Globalização e localização no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core fornece serviços e middleware para localização de conteúdo em diferentes idiomas e culturas.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: 645f680436336acbe1d5c2854a242527c9b4b9cb
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717397"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="3bc91-103">Globalização e localização no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bc91-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="3bc91-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="3bc91-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="3bc91-105">A criação de um site multilíngue com o ASP.NET Core permitirá que seu site alcance um público maior.</span><span class="sxs-lookup"><span data-stu-id="3bc91-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="3bc91-106">O ASP.NET Core fornece serviços e middleware para localização em diferentes idiomas e culturas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="3bc91-107">A internacionalização envolve [Globalização](/dotnet/api/system.globalization) e [Localização](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="3bc91-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="3bc91-108">Globalização é o processo de criação de aplicativos que dão suporte a diferentes culturas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="3bc91-109">A globalização adiciona suporte para entrada, exibição e saída de um conjunto definido de scripts de idiomas relacionados a áreas geográficas específicas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="3bc91-110">Localização é o processo de adaptar um aplicativo globalizado, que você já processou para possibilidade de localização, a determinada cultura/localidade.</span><span class="sxs-lookup"><span data-stu-id="3bc91-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="3bc91-111">Para obter mais informações, consulte **Termos de globalização e localização** próximo ao final deste documento.</span><span class="sxs-lookup"><span data-stu-id="3bc91-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="3bc91-112">A localização de aplicativos envolve o seguinte:</span><span class="sxs-lookup"><span data-stu-id="3bc91-112">App localization involves the following:</span></span>

1. <span data-ttu-id="3bc91-113">Tornar o conteúdo do aplicativo localizável</span><span class="sxs-lookup"><span data-stu-id="3bc91-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="3bc91-114">Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte</span><span class="sxs-lookup"><span data-stu-id="3bc91-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="3bc91-115">Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="3bc91-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="3bc91-116">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3bc91-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="3bc91-117">Tornar o conteúdo do aplicativo localizável</span><span class="sxs-lookup"><span data-stu-id="3bc91-117">Make the app's content localizable</span></span>

<span data-ttu-id="3bc91-118">Introduzidos no ASP.NET Core `IStringLocalizer` e `IStringLocalizer<T>` foram projetados para melhorar a produtividade ao desenvolver aplicativos localizados.</span><span class="sxs-lookup"><span data-stu-id="3bc91-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="3bc91-119">`IStringLocalizer` usa o [ResourceManager](/dotnet/api/system.resources.resourcemanager) e o [ResourceReader](/dotnet/api/system.resources.resourcereader) para fornecer recursos específicos a uma cultura em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="3bc91-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="3bc91-120">A interface simples tem um indexador e um `IEnumerable` para retornar cadeias de caracteres localizadas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="3bc91-121">`IStringLocalizer` não exige o armazenamento das cadeias de caracteres de idioma padrão em um arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="3bc91-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="3bc91-122">Você pode desenvolver um aplicativo direcionado à localização e não precisa criar arquivos de recurso no início do desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3bc91-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="3bc91-123">O código abaixo mostra como encapsular a cadeia de caracteres "About Title" para localização.</span><span class="sxs-lookup"><span data-stu-id="3bc91-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="3bc91-124">No código acima, a implementação `IStringLocalizer<T>` é obtida da [Injeção de Dependência](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="3bc91-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="3bc91-125">Se o valor localizado de "About Title" não é encontrado, a chave do indexador é retornada, ou seja, a cadeia de caracteres "About Title".</span><span class="sxs-lookup"><span data-stu-id="3bc91-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="3bc91-126">Deixe as cadeias de caracteres literais de idioma padrão no aplicativo e encapsule-as no localizador, de modo que você possa se concentrar no desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bc91-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="3bc91-127">Você pode desenvolve seu aplicativo com o idioma padrão e prepará-lo para a etapa de localização sem primeiro criar um arquivo de recurso padrão.</span><span class="sxs-lookup"><span data-stu-id="3bc91-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="3bc91-128">Como alternativa, você pode usar a abordagem tradicional e fornecer uma chave para recuperar a cadeia de caracteres de idioma padrão.</span><span class="sxs-lookup"><span data-stu-id="3bc91-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="3bc91-129">Para muitos desenvolvedores, o novo fluxo de trabalho de não ter um arquivo *.resx* de idioma padrão e simplesmente encapsular os literais de cadeia de caracteres pode reduzir a sobrecarga de localizar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bc91-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="3bc91-130">Outros desenvolvedores preferirão o fluxo de trabalho tradicional, pois ele pode facilitar o trabalho com literais de cadeia de caracteres mais longas e a atualização de cadeias de caracteres localizadas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="3bc91-131">Use a implementação `IHtmlLocalizer<T>` para recursos que contêm HTML.</span><span class="sxs-lookup"><span data-stu-id="3bc91-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="3bc91-132">O `IHtmlLocalizer` codifica em HTML os argumentos que são formatados na cadeia de caracteres de recurso, mas não codifica em HTML a cadeia de caracteres de recurso em si.</span><span class="sxs-lookup"><span data-stu-id="3bc91-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="3bc91-133">Na amostra realçada abaixo, apenas o valor do parâmetro `name` é codificado em HTML.</span><span class="sxs-lookup"><span data-stu-id="3bc91-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="3bc91-134">**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.</span><span class="sxs-lookup"><span data-stu-id="3bc91-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="3bc91-135">No nível mais baixo, você pode obter `IStringLocalizerFactory` com a [Injeção de Dependência](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="3bc91-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="3bc91-136">O código acima demonstra cada um dos dois métodos Create de alocador.</span><span class="sxs-lookup"><span data-stu-id="3bc91-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="3bc91-137">Você pode particionar as cadeias de caracteres localizadas por controlador, área ou ter apenas um contêiner.</span><span class="sxs-lookup"><span data-stu-id="3bc91-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="3bc91-138">No aplicativo de exemplo, uma classe fictícia chamada `SharedResource` é usada para recursos compartilhados.</span><span class="sxs-lookup"><span data-stu-id="3bc91-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="3bc91-139">Alguns desenvolvedores usam a classe `Startup` para conter cadeias de caracteres globais ou compartilhadas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="3bc91-140">Na amostra abaixo, os localizadores `InfoController` e `SharedResource` são usados:</span><span class="sxs-lookup"><span data-stu-id="3bc91-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="3bc91-141">Localização de exibição</span><span class="sxs-lookup"><span data-stu-id="3bc91-141">View localization</span></span>

<span data-ttu-id="3bc91-142">O serviço `IViewLocalizer` fornece cadeias de caracteres localizadas para uma [exibição](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="3bc91-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="3bc91-143">A classe `ViewLocalizer` implementa essa interface e encontra o local do recurso no caminho do arquivo de exibição.</span><span class="sxs-lookup"><span data-stu-id="3bc91-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="3bc91-144">O seguinte código mostra como usar a implementação padrão de `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="3bc91-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="3bc91-145">A implementação padrão de `IViewLocalizer` encontra o arquivo de recurso com base no nome de arquivo da exibição.</span><span class="sxs-lookup"><span data-stu-id="3bc91-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="3bc91-146">Não há nenhuma opção para usar um arquivo de recurso compartilhado global.</span><span class="sxs-lookup"><span data-stu-id="3bc91-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="3bc91-147">`ViewLocalizer` implementa o localizador usando `IHtmlLocalizer` e, portanto, o Razor não codifica em HTML a cadeia de caracteres localizada.</span><span class="sxs-lookup"><span data-stu-id="3bc91-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="3bc91-148">Parametrize cadeias de recurso e o `IViewLocalizer` codificará em HTML os parâmetros, mas não a cadeia de caracteres de recurso.</span><span class="sxs-lookup"><span data-stu-id="3bc91-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="3bc91-149">Considere a seguinte marcação do Razor:</span><span class="sxs-lookup"><span data-stu-id="3bc91-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="3bc91-150">Um arquivo de recurso em francês pode conter o seguinte:</span><span class="sxs-lookup"><span data-stu-id="3bc91-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="3bc91-151">Chave</span><span class="sxs-lookup"><span data-stu-id="3bc91-151">Key</span></span> | <span data-ttu-id="3bc91-152">Value</span><span class="sxs-lookup"><span data-stu-id="3bc91-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="3bc91-153">A exibição renderizada contém a marcação HTML do arquivo de recurso.</span><span class="sxs-lookup"><span data-stu-id="3bc91-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="3bc91-154">**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.</span><span class="sxs-lookup"><span data-stu-id="3bc91-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="3bc91-155">Para usar um arquivo de recurso compartilhado em uma exibição, injete `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="3bc91-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="3bc91-156">Localização de DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="3bc91-156">DataAnnotations localization</span></span>

<span data-ttu-id="3bc91-157">As mensagens de erro de DataAnnotations são localizadas com `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="3bc91-158">Usando a opção `ResourcesPath = "Resources"`, as mensagens de erro em `RegisterViewModel` podem ser armazenadas em um dos seguintes caminhos:</span><span class="sxs-lookup"><span data-stu-id="3bc91-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="3bc91-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3bc91-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="3bc91-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3bc91-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="3bc91-161">No ASP.NET Core MVC 1.1.0 e superior, atributos que não sejam de validação são localizados.</span><span class="sxs-lookup"><span data-stu-id="3bc91-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="3bc91-162">O ASP.NET Core MVC 1.0 **não** pesquisa cadeias de caracteres localizadas para atributos que não sejam de validação.</span><span class="sxs-lookup"><span data-stu-id="3bc91-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="3bc91-163">Usando uma cadeia de caracteres de recurso para várias classes</span><span class="sxs-lookup"><span data-stu-id="3bc91-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="3bc91-164">O seguinte código mostra como usar uma cadeia de caracteres de recurso para atributos de validação com várias classes:</span><span class="sxs-lookup"><span data-stu-id="3bc91-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="3bc91-165">No código anterior, `SharedResource` é a classe correspondente ao resx em que as mensagens de validação são armazenadas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="3bc91-166">Com essa abordagem, DataAnnotations usará apenas `SharedResource`, em vez de o recurso para cada classe.</span><span class="sxs-lookup"><span data-stu-id="3bc91-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="3bc91-167">Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte</span><span class="sxs-lookup"><span data-stu-id="3bc91-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="3bc91-168">SupportedCultures e SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="3bc91-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="3bc91-169">O ASP.NET Core permite que você especifique dois valores de cultura, `SupportedCultures` e `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="3bc91-170">O objeto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) para `SupportedCultures` determina os resultados das funções dependentes de cultura, como data, hora, número e formatação de moeda.</span><span class="sxs-lookup"><span data-stu-id="3bc91-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="3bc91-171">`SupportedCultures` também determina a ordem de classificação de texto, convenções de uso de maiúsculas e comparações de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="3bc91-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="3bc91-172">Consulte [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) para obter mais informações sobre como o servidor obtém a Cultura.</span><span class="sxs-lookup"><span data-stu-id="3bc91-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="3bc91-173">O `SupportedUICultures` determina quais cadeias de caracteres traduzidas (de arquivos *.resx*) são pesquisadas pelo [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="3bc91-173">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="3bc91-174">O `ResourceManager` apenas pesquisa cadeias de caracteres específicas a uma cultura determinadas por `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="3bc91-175">Cada thread no .NET tem objetos `CurrentCulture` e `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="3bc91-176">O ASP.NET Core inspeciona esses valores durante a renderização de funções dependentes de cultura.</span><span class="sxs-lookup"><span data-stu-id="3bc91-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="3bc91-177">Por exemplo, se a cultura do thread atual estiver definida como "en-US" (inglês, Estados Unidos), `DateTime.Now.ToLongDateString()` exibirá "Thursday, February 18, 2016", mas se `CurrentCulture` estiver definida como "es-ES" (espanhol, Espanha), o resultado será "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="3bc91-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="3bc91-178">Arquivos de recurso</span><span class="sxs-lookup"><span data-stu-id="3bc91-178">Resource files</span></span>

<span data-ttu-id="3bc91-179">Um arquivo de recurso é um mecanismo útil para separar cadeias de caracteres localizáveis do código.</span><span class="sxs-lookup"><span data-stu-id="3bc91-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="3bc91-180">Cadeias de caracteres traduzidas para o idioma não padrão são arquivos de recurso *.resx* isolados.</span><span class="sxs-lookup"><span data-stu-id="3bc91-180">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="3bc91-181">Por exemplo, talvez você queira criar um arquivo de recurso em espanhol chamado *Welcome.es.resx* contendo cadeias de caracteres traduzidas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="3bc91-182">"es" são o código de idioma para o espanhol.</span><span class="sxs-lookup"><span data-stu-id="3bc91-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="3bc91-183">Para criar esse arquivo de recurso no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3bc91-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="3bc91-184">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta que conterá o arquivo de recurso > **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="3bc91-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Menu contextual aninhado: no Gerenciador de Soluções, um menu contextual é aberto para Recursos.](localization/_static/newi.png)

2. <span data-ttu-id="3bc91-187">Na caixa **Pesquisar modelos instalados**, insira "recurso" e nomeie o arquivo.</span><span class="sxs-lookup"><span data-stu-id="3bc91-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Caixa de diálogo Adicionar Novo Item](localization/_static/res.png)

3. <span data-ttu-id="3bc91-189">Insira o valor da chave (cadeia de caracteres nativa) na coluna **Nome** e a cadeia de caracteres traduzida na coluna **Valor**.</span><span class="sxs-lookup"><span data-stu-id="3bc91-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Arquivo Welcome.es.resx (o arquivo de recurso Welcome em espanhol) com a palavra Hello na coluna Nome e a palavra Hola (Olá, em espanhol) na coluna valor](localization/_static/hola.png)

    <span data-ttu-id="3bc91-191">O Visual Studio mostra o arquivo *Welcome.es.resx*.</span><span class="sxs-lookup"><span data-stu-id="3bc91-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Gerenciador de Soluções mostrando o arquivo de recurso Welcome em espanhol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="3bc91-193">Nomenclatura do arquivo de recurso</span><span class="sxs-lookup"><span data-stu-id="3bc91-193">Resource file naming</span></span>

<span data-ttu-id="3bc91-194">Os recursos são nomeados com o nome completo do tipo de sua classe menos o nome do assembly.</span><span class="sxs-lookup"><span data-stu-id="3bc91-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="3bc91-195">Por exemplo, um recurso em francês em um projeto cujo assembly principal é `LocalizationWebsite.Web.dll` para a classe `LocalizationWebsite.Web.Startup` será nomeado *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="3bc91-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="3bc91-196">Um recurso para a classe `LocalizationWebsite.Web.Controllers.HomeController` será nomeado *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="3bc91-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3bc91-197">Se o namespace da classe de destino não for o mesmo que o nome do assembly, você precisará do nome completo do tipo.</span><span class="sxs-lookup"><span data-stu-id="3bc91-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="3bc91-198">Por exemplo, no projeto de exemplo, um recurso para o tipo `ExtraNamespace.Tools` será nomeado *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="3bc91-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="3bc91-199">No projeto de exemplo, o método `ConfigureServices` define o `ResourcesPath` como "Resources", de modo que o caminho relativo do projeto para o arquivo de recurso em francês do controlador principal seja *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="3bc91-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3bc91-200">Como alternativa, você pode usar pastas para organizar arquivos de recurso.</span><span class="sxs-lookup"><span data-stu-id="3bc91-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="3bc91-201">Para o controlador principal, o caminho será *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="3bc91-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="3bc91-202">Se você não usar a opção `ResourcesPath`, o arquivo *.resx* entrará no diretório base do projeto.</span><span class="sxs-lookup"><span data-stu-id="3bc91-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="3bc91-203">O arquivo de recurso para `HomeController` será nomeado *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="3bc91-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3bc91-204">A opção de usar a convenção de nomenclatura de ponto ou caminho depende de como você deseja organizar os arquivos de recurso.</span><span class="sxs-lookup"><span data-stu-id="3bc91-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="3bc91-205">Nome do recurso</span><span class="sxs-lookup"><span data-stu-id="3bc91-205">Resource name</span></span> | <span data-ttu-id="3bc91-206">Nomenclatura de ponto ou caminho</span><span class="sxs-lookup"><span data-stu-id="3bc91-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="3bc91-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3bc91-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="3bc91-208">Ponto</span><span class="sxs-lookup"><span data-stu-id="3bc91-208">Dot</span></span>  |
| <span data-ttu-id="3bc91-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3bc91-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="3bc91-210">Caminho</span><span class="sxs-lookup"><span data-stu-id="3bc91-210">Path</span></span> |
|    |     |

<span data-ttu-id="3bc91-211">Os arquivos de recurso que usam `@inject IViewLocalizer` em exibições do Razor seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="3bc91-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="3bc91-212">O arquivo de recurso de uma exibição pode ser nomeado usando a nomenclatura de ponto ou de caminho.</span><span class="sxs-lookup"><span data-stu-id="3bc91-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="3bc91-213">Os arquivos de recurso da exibição do Razor simulam o caminho de seu arquivo de exibição associado.</span><span class="sxs-lookup"><span data-stu-id="3bc91-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="3bc91-214">Supondo que definimos o `ResourcesPath` como "Resources", o arquivo de recurso em francês associado à exibição *Views/Home/About.cshtml* pode ser um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="3bc91-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="3bc91-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3bc91-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="3bc91-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3bc91-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="3bc91-217">Se você não usar a opção `ResourcesPath`, o arquivo *.resx* de uma exibição estará localizado na mesma pasta da exibição.</span><span class="sxs-lookup"><span data-stu-id="3bc91-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="3bc91-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="3bc91-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="3bc91-219">O atributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) fornece o namespace raiz de um assembly quando o namespace raiz de um assembly é diferente do nome do assembly.</span><span class="sxs-lookup"><span data-stu-id="3bc91-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="3bc91-220">Isso pode ocorrer quando o nome de um projeto não é um identificador .NET válido.</span><span class="sxs-lookup"><span data-stu-id="3bc91-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="3bc91-221">Por exemplo, `my-project-name.csproj` usará o namespace raiz `my_project_name` e o nome do assembly `my-project-name` levando a esse erro.</span><span class="sxs-lookup"><span data-stu-id="3bc91-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="3bc91-222">Se o namespace raiz de um assembly é diferente do nome do assembly:</span><span class="sxs-lookup"><span data-stu-id="3bc91-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="3bc91-223">A localização não funciona por padrão.</span><span class="sxs-lookup"><span data-stu-id="3bc91-223">Localization does not work by default.</span></span>
* <span data-ttu-id="3bc91-224">A localização falha devido à maneira como os recursos são pesquisados dentro do assembly.</span><span class="sxs-lookup"><span data-stu-id="3bc91-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="3bc91-225">`RootNamespace` é um valor de tempo de build que não está disponível para o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="3bc91-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="3bc91-226">Se o `RootNamespace` é diferente de `AssemblyName`, inclua o seguinte no *AssemblyInfo.cs* (com valores de parâmetro substituídos pelos valores reais):</span><span class="sxs-lookup"><span data-stu-id="3bc91-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="3bc91-227">O código anterior permite a resolução bem-sucedida de arquivos resx.</span><span class="sxs-lookup"><span data-stu-id="3bc91-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="3bc91-228">Comportamento de fallback da cultura</span><span class="sxs-lookup"><span data-stu-id="3bc91-228">Culture fallback behavior</span></span>

<span data-ttu-id="3bc91-229">Ao procurar por um recurso, a localização participa do "fallback de cultura".</span><span class="sxs-lookup"><span data-stu-id="3bc91-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="3bc91-230">Começando com a cultura solicitada, se ela não for encontrada, ela será revertida para a cultura pai dessa cultura.</span><span class="sxs-lookup"><span data-stu-id="3bc91-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="3bc91-231">Além disso, a propriedade [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) representa a cultura pai.</span><span class="sxs-lookup"><span data-stu-id="3bc91-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="3bc91-232">Isso normalmente (mas nem sempre) significa a remoção do significante do ISO.</span><span class="sxs-lookup"><span data-stu-id="3bc91-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="3bc91-233">Por exemplo, o dialeto do espanhol falado no México é "es-MX".</span><span class="sxs-lookup"><span data-stu-id="3bc91-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="3bc91-234">Ele tem o pai "es", ou seja, espanhol, não específico de nenhum país.</span><span class="sxs-lookup"><span data-stu-id="3bc91-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="3bc91-235">Imagine que seu site receba uma solicitação de um recurso “Boas-vindas" usando a cultura"fr-CA".</span><span class="sxs-lookup"><span data-stu-id="3bc91-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="3bc91-236">O sistema de localização procura os seguintes recursos, em ordem, e seleciona a primeira correspondência:</span><span class="sxs-lookup"><span data-stu-id="3bc91-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="3bc91-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="3bc91-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="3bc91-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3bc91-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="3bc91-239">*Welcome.resx* (se o `NeutralResourcesLanguage` for "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="3bc91-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="3bc91-240">Por exemplo, se você remover o designador de cultura ".fr" e tiver a cultura definida como francês, o arquivo de recurso padrão será lido e as cadeias de caracteres serão localizadas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="3bc91-241">O Gerenciador de Recursos designa um padrão ou um recurso de fallback para quando nenhuma opção atende à cultura solicitada.</span><span class="sxs-lookup"><span data-stu-id="3bc91-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="3bc91-242">Se você deseja apenas retornar a chave quando um recurso está ausente para a cultura solicitada, não deve ter um arquivo de recurso padrão.</span><span class="sxs-lookup"><span data-stu-id="3bc91-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="3bc91-243">Gerar arquivos de recurso com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bc91-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="3bc91-244">Se você criar um arquivo de recurso no Visual Studio sem uma cultura no nome do arquivo (por exemplo, *Welcome.resx*), o Visual Studio criará uma classe do C# com uma propriedade para cada cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="3bc91-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="3bc91-245">Geralmente isso não é o desejado com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bc91-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="3bc91-246">Normalmente você não tem um arquivo de recurso *.resx* padrão (um arquivo *.resx* sem o nome de cultura).</span><span class="sxs-lookup"><span data-stu-id="3bc91-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="3bc91-247">Sugerimos que você crie o arquivo *.resx* com um nome de cultura (por exemplo *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="3bc91-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="3bc91-248">Quando você criar um arquivo *.resx* com um nome de cultura, o Visual Studio não gerará o arquivo de classe.</span><span class="sxs-lookup"><span data-stu-id="3bc91-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="3bc91-249">Adicionar outras culturas</span><span class="sxs-lookup"><span data-stu-id="3bc91-249">Add other cultures</span></span>

<span data-ttu-id="3bc91-250">Cada combinação de idioma e cultura (que não seja o idioma padrão) exige um arquivo de recurso exclusivo.</span><span class="sxs-lookup"><span data-stu-id="3bc91-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="3bc91-251">Crie arquivos de recurso para diferentes culturas e localidades criando novos arquivos de recurso, nos quais os códigos ISO e de idioma fazem parte do nome do arquivo (por exemplo, **en-us**, **fr-ca** e **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="3bc91-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="3bc91-252">Esses códigos ISO são colocados entre o nome do arquivo e a extensão de arquivo *.resx*, como em *Welcome.es-MX.resx* (espanhol/México).</span><span class="sxs-lookup"><span data-stu-id="3bc91-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="3bc91-253">Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação</span><span class="sxs-lookup"><span data-stu-id="3bc91-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="3bc91-254">Configurar a localização</span><span class="sxs-lookup"><span data-stu-id="3bc91-254">Configure localization</span></span>

<span data-ttu-id="3bc91-255">A localização é configurada no método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3bc91-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="3bc91-256">`AddLocalization` Adiciona os serviços de localização ao contêiner de serviços.</span><span class="sxs-lookup"><span data-stu-id="3bc91-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="3bc91-257">O código acima também define o caminho de recursos como "Resources".</span><span class="sxs-lookup"><span data-stu-id="3bc91-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="3bc91-258">`AddViewLocalization` Adiciona suporte para os arquivos de exibição localizados.</span><span class="sxs-lookup"><span data-stu-id="3bc91-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="3bc91-259">Nesta amostra, a localização de exibição se baseia no sufixo do arquivo de exibição.</span><span class="sxs-lookup"><span data-stu-id="3bc91-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="3bc91-260">Por exemplo, "fr" no arquivo *Index.fr.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3bc91-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="3bc91-261">`AddDataAnnotationsLocalization` Adiciona suporte para as mensagens de validação `DataAnnotations` localizadas por meio de abstrações `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="3bc91-262">Middleware de localização</span><span class="sxs-lookup"><span data-stu-id="3bc91-262">Localization middleware</span></span>

<span data-ttu-id="3bc91-263">A cultura atual em uma solicitação é definida no [Middleware](xref:fundamentals/middleware/index) de localização.</span><span class="sxs-lookup"><span data-stu-id="3bc91-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="3bc91-264">O middleware de localização é habilitado no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="3bc91-265">O middleware de localização precisa ser configurado antes de qualquer middleware que possa verificar a cultura de solicitação (por exemplo, `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="3bc91-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]

<span data-ttu-id="3bc91-266">`UseRequestLocalization` inicializa um objeto `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="3bc91-267">Em cada solicitação, a lista de `RequestCultureProvider` em `RequestLocalizationOptions` é enumerada e o primeiro provedor que pode determinar com êxito a cultura de solicitação é usado.</span><span class="sxs-lookup"><span data-stu-id="3bc91-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="3bc91-268">Os provedores padrão são obtidos da classe `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="3bc91-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="3bc91-269">A lista padrão é apresentada do mais específico ao menos específico.</span><span class="sxs-lookup"><span data-stu-id="3bc91-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="3bc91-270">Mais adiante neste artigo, veremos como você pode alterar a ordem e até mesmo adicionar um provedor de cultura personalizado.</span><span class="sxs-lookup"><span data-stu-id="3bc91-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="3bc91-271">Se nenhum dos provedores pode determinar a cultura de solicitação, o `DefaultRequestCulture` é usado.</span><span class="sxs-lookup"><span data-stu-id="3bc91-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="3bc91-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3bc91-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="3bc91-273">Alguns aplicativos usarão uma cadeia de caracteres de consulta para definir a [cultura e a cultura da interface do usuário](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="3bc91-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="3bc91-274">Para aplicativos que usam a abordagem do cabeçalho Accept-Language ou do cookie, a adição de uma cadeia de caracteres de consulta à URL é útil para depurar e testar o código.</span><span class="sxs-lookup"><span data-stu-id="3bc91-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="3bc91-275">Por padrão, o `QueryStringRequestCultureProvider` é registrado como o primeiro provedor de localização na lista `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="3bc91-276">Passe os parâmetros `culture` e `ui-culture` da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3bc91-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="3bc91-277">O seguinte exemplo define a cultura específica (idioma e região) como espanhol/México:</span><span class="sxs-lookup"><span data-stu-id="3bc91-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="3bc91-278">Se você passar somente uma das duas (`culture` ou `ui-culture`), o provedor da cadeia de caracteres de consulta definirá os dois valores usando aquela que foi passada.</span><span class="sxs-lookup"><span data-stu-id="3bc91-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="3bc91-279">Por exemplo, a definição apenas da cultura definirá a `Culture` e a `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="3bc91-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="3bc91-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3bc91-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="3bc91-281">Em geral, aplicativos de produção fornecerão um mecanismo para definir a cultura com o cookie de cultura do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bc91-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="3bc91-282">Use o método `MakeCookieValue` para criar um cookie.</span><span class="sxs-lookup"><span data-stu-id="3bc91-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="3bc91-283">O `CookieRequestCultureProvider` `DefaultCookieName` retorna o nome padrão do cookie usado para acompanhar as informações de cultura preferencial do usuário.</span><span class="sxs-lookup"><span data-stu-id="3bc91-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="3bc91-284">O nome padrão do cookie é `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="3bc91-285">O formato do cookie é `c=%LANGCODE%|uic=%LANGCODE%`, em que `c` é `Culture` e `uic` é `UICulture`, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3bc91-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="3bc91-286">Se você especificar apenas as informações de cultura e a cultura da interface do usuário, a cultura especificada será usada para as informações de cultura e para a cultura da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="3bc91-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="3bc91-287">O cabeçalho HTTP Accept-Language</span><span class="sxs-lookup"><span data-stu-id="3bc91-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="3bc91-288">O [cabeçalho Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) é configurável na maioria dos navegadores e originalmente foi criado para especificar o idioma do usuário.</span><span class="sxs-lookup"><span data-stu-id="3bc91-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="3bc91-289">Essa configuração indica que o navegador foi definido para enviar ou herdou do sistema operacional subjacente.</span><span class="sxs-lookup"><span data-stu-id="3bc91-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="3bc91-290">O cabeçalho HTTP Accept-Language de uma solicitação do navegador não é uma maneira infalível de detectar o idioma preferencial do usuário (consulte [Definindo preferências de idioma em um navegador](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="3bc91-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="3bc91-291">Um aplicativo de produção deve incluir uma maneira para que um usuário personalize sua opção de cultura.</span><span class="sxs-lookup"><span data-stu-id="3bc91-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="3bc91-292">Definir o cabeçalho HTTP Accept-Language no IE</span><span class="sxs-lookup"><span data-stu-id="3bc91-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="3bc91-293">No ícone de engrenagem, toque em **Opções da Internet**.</span><span class="sxs-lookup"><span data-stu-id="3bc91-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="3bc91-294">Toque em **Idiomas**.</span><span class="sxs-lookup"><span data-stu-id="3bc91-294">Tap **Languages**.</span></span>

    ![Opções da Internet](localization/_static/lang.png)

3. <span data-ttu-id="3bc91-296">Toque em **Definir Preferências de Idioma**.</span><span class="sxs-lookup"><span data-stu-id="3bc91-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="3bc91-297">Toque em **Adicionar um idioma**.</span><span class="sxs-lookup"><span data-stu-id="3bc91-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="3bc91-298">Adicione o idioma.</span><span class="sxs-lookup"><span data-stu-id="3bc91-298">Add the language.</span></span>

6. <span data-ttu-id="3bc91-299">Toque no idioma e, em seguida, em **Mover Para Cima**.</span><span class="sxs-lookup"><span data-stu-id="3bc91-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range=">= aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="3bc91-300">O cabeçalho HTTP do idioma do conteúdo</span><span class="sxs-lookup"><span data-stu-id="3bc91-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="3bc91-301">O cabeçalho da entidade de [linguagem de conteúdo](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="3bc91-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="3bc91-302">É usado para descrever os idiomas destinados ao público.</span><span class="sxs-lookup"><span data-stu-id="3bc91-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="3bc91-303">Permite que um usuário diferencie de acordo com a linguagem preferencial dos usuários.</span><span class="sxs-lookup"><span data-stu-id="3bc91-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="3bc91-304">Os cabeçalhos de entidade são usados tanto em solicitações HTTP quanto em respostas.</span><span class="sxs-lookup"><span data-stu-id="3bc91-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="3bc91-305">O cabeçalho `Content-Language` pode ser adicionado definindo a propriedade `ApplyCurrentCultureToResponseHeaders`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="3bc91-306">Adicionando o cabeçalho de `Content-Language`:</span><span class="sxs-lookup"><span data-stu-id="3bc91-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="3bc91-307">Permite que o RequestLocalizationMiddleware defina o cabeçalho de `Content-Language` com o `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="3bc91-308">Elimina a necessidade de definir o cabeçalho de resposta `Content-Language` explicitamente.</span><span class="sxs-lookup"><span data-stu-id="3bc91-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="3bc91-309">Usar um provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="3bc91-309">Use a custom provider</span></span>

<span data-ttu-id="3bc91-310">Suponha que você deseje permitir que os clientes armazenem seus idiomas e culturas nos bancos de dados.</span><span class="sxs-lookup"><span data-stu-id="3bc91-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="3bc91-311">Você pode escrever um provedor para pesquisar esses valores para o usuário.</span><span class="sxs-lookup"><span data-stu-id="3bc91-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="3bc91-312">O seguinte código mostra como adicionar um provedor personalizado:</span><span class="sxs-lookup"><span data-stu-id="3bc91-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

<span data-ttu-id="3bc91-313">Use `RequestLocalizationOptions` para adicionar ou remover provedores de localização.</span><span class="sxs-lookup"><span data-stu-id="3bc91-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="3bc91-314">Definir a cultura de forma programática</span><span class="sxs-lookup"><span data-stu-id="3bc91-314">Set the culture programmatically</span></span>

<span data-ttu-id="3bc91-315">Este projeto de exemplo **Localization.StarterWeb** no [GitHub](https://github.com/aspnet/entropy) contém a interface do usuário para definir a `Culture`.</span><span class="sxs-lookup"><span data-stu-id="3bc91-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="3bc91-316">O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* permite que você selecione a cultura na lista de culturas compatíveis:</span><span class="sxs-lookup"><span data-stu-id="3bc91-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="3bc91-317">O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* é adicionado à seção `footer` do arquivo de layout para que ele fique disponível para todos os exibições:</span><span class="sxs-lookup"><span data-stu-id="3bc91-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="3bc91-318">O método `SetLanguage` define o cookie de cultura.</span><span class="sxs-lookup"><span data-stu-id="3bc91-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="3bc91-319">Não é possível conectar o *_SelectLanguagePartial.cshtml* ao código de exemplo para este projeto.</span><span class="sxs-lookup"><span data-stu-id="3bc91-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="3bc91-320">O projeto **Localization.StarterWeb** no [GitHub](https://github.com/aspnet/entropy) contém o código para o fluxo do `RequestLocalizationOptions` para uma parcial do Razor por meio do contêiner de [Injeção de Dependência](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="3bc91-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="3bc91-321">Dados de rota de associação de modelo e cadeias de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="3bc91-321">Model binding route data and query strings</span></span>

<span data-ttu-id="3bc91-322">Consulte [comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="3bc91-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="3bc91-323">Termos de globalização e localização</span><span class="sxs-lookup"><span data-stu-id="3bc91-323">Globalization and localization terms</span></span>

<span data-ttu-id="3bc91-324">O processo de localização do aplicativo também exige uma compreensão básica dos conjuntos de caracteres relevantes geralmente usados no desenvolvimento moderno de software e uma compreensão dos problemas associados a eles.</span><span class="sxs-lookup"><span data-stu-id="3bc91-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="3bc91-325">Embora todos os computadores armazenem texto como números (códigos), diferentes sistemas armazenam o mesmo texto usando números diferentes.</span><span class="sxs-lookup"><span data-stu-id="3bc91-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="3bc91-326">O processo de localização se refere à tradução da interface do usuário do aplicativo para uma cultura/localidade específica.</span><span class="sxs-lookup"><span data-stu-id="3bc91-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="3bc91-327">[Possibilidade de localização](/dotnet/standard/globalization-localization/localizability-review) é um processo intermediário usado para verificar se um aplicativo globalizado está pronto para localização.</span><span class="sxs-lookup"><span data-stu-id="3bc91-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="3bc91-328">O formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) para o nome de cultura é `<languagecode2>-<country/regioncode2>`, em que `<languagecode2>` é o código de idioma e `<country/regioncode2>` é o código de subcultura.</span><span class="sxs-lookup"><span data-stu-id="3bc91-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="3bc91-329">Por exemplo, `es-CL` para Espanhol (Chile), `en-US` para inglês (Estados Unidos) e `en-AU` para inglês (Austrália).</span><span class="sxs-lookup"><span data-stu-id="3bc91-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="3bc91-330">O [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) é uma combinação de um código de cultura de duas letras minúsculas ISO 639 associado a um idioma e um código de subcultura de duas letras maiúsculas ISO 3166 associado a um país ou uma região.</span><span class="sxs-lookup"><span data-stu-id="3bc91-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="3bc91-331">Consulte [Nome da cultura de idioma](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="3bc91-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="3bc91-332">Muitas vezes, internacionalização é abreviada como "I18N".</span><span class="sxs-lookup"><span data-stu-id="3bc91-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="3bc91-333">A abreviação usa a primeira e última letras e o número de letras entre elas e, portanto, 18 significa o número de letras entre o primeiro "I" e o último "N".</span><span class="sxs-lookup"><span data-stu-id="3bc91-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="3bc91-334">O mesmo se aplica a Globalização (G11N) e Localização (L10N).</span><span class="sxs-lookup"><span data-stu-id="3bc91-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="3bc91-335">Termos:</span><span class="sxs-lookup"><span data-stu-id="3bc91-335">Terms:</span></span>

* <span data-ttu-id="3bc91-336">Globalização (G11N): o processo de fazer com que um aplicativo dê suporte a diferentes idiomas e regiões.</span><span class="sxs-lookup"><span data-stu-id="3bc91-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="3bc91-337">Localização (L10N): o processo de personalizar um aplicativo para determinado idioma e região.</span><span class="sxs-lookup"><span data-stu-id="3bc91-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="3bc91-338">Internacionalização (I18N): descreve a globalização e a localização.</span><span class="sxs-lookup"><span data-stu-id="3bc91-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="3bc91-339">Cultura: um idioma e, opcionalmente, uma região.</span><span class="sxs-lookup"><span data-stu-id="3bc91-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="3bc91-340">Cultura neutra: uma cultura que tem um idioma especificado, mas não uma região.</span><span class="sxs-lookup"><span data-stu-id="3bc91-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="3bc91-341">(por exemplo, "en", "es")</span><span class="sxs-lookup"><span data-stu-id="3bc91-341">(for example "en", "es")</span></span>
* <span data-ttu-id="3bc91-342">Cultura específica: uma cultura que tem um idioma e uma região especificados.</span><span class="sxs-lookup"><span data-stu-id="3bc91-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="3bc91-343">(por exemplo, "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="3bc91-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="3bc91-344">Cultura pai: a cultura neutra que contém uma cultura específica.</span><span class="sxs-lookup"><span data-stu-id="3bc91-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="3bc91-345">(por exemplo, "en" é a cultura pai de "en-US" e "en-GB")</span><span class="sxs-lookup"><span data-stu-id="3bc91-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="3bc91-346">Localidade: uma localidade é o mesmo que uma cultura.</span><span class="sxs-lookup"><span data-stu-id="3bc91-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3bc91-347">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3bc91-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="3bc91-348">O [projeto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usado no artigo.</span><span class="sxs-lookup"><span data-stu-id="3bc91-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="3bc91-349">Globalizando e localizando aplicativos do .NET</span><span class="sxs-lookup"><span data-stu-id="3bc91-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="3bc91-350">Recursos em arquivos .resx</span><span class="sxs-lookup"><span data-stu-id="3bc91-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="3bc91-351">Kit de Ferramentas de Aplicativo Multilíngue da Microsoft</span><span class="sxs-lookup"><span data-stu-id="3bc91-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="3bc91-352">Localização e genéricos</span><span class="sxs-lookup"><span data-stu-id="3bc91-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)
