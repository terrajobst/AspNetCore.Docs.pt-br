---
title: Configurar a localização de objeto portátil no ASP.NET Core
author: sebastienros
description: Este artigo apresenta arquivos de Objeto Portátil e descreve as etapas para usá-los em um aplicativo ASP.NET Core com a estrutura Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 6ec7afc59d6dfd2629a3d6d83ae619575397a9df
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884371"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="73ba3-103">Configurar a localização de objeto portátil no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73ba3-103">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="73ba3-104">Por [Sébastien Ros](https://github.com/sebastienros) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="73ba3-104">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="73ba3-105">Este artigo explica as etapas para usar arquivos PO (Objeto Portátil) em um aplicativo ASP.NET Core com a estrutura [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="73ba3-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="73ba3-106">**Observação:** Orchard Core não é um produto da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="73ba3-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="73ba3-107">Consequentemente, a Microsoft não fornece suporte para esse recurso.</span><span class="sxs-lookup"><span data-stu-id="73ba3-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="73ba3-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73ba3-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="73ba3-109">O que é um arquivo PO?</span><span class="sxs-lookup"><span data-stu-id="73ba3-109">What is a PO file?</span></span>

<span data-ttu-id="73ba3-110">Os arquivos PO são distribuídos como arquivos de texto que contém cadeias de caracteres traduzidas em determinado idioma.</span><span class="sxs-lookup"><span data-stu-id="73ba3-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="73ba3-111">Algumas vantagens do uso de arquivos PO em vez de arquivos *.resx* incluem:</span><span class="sxs-lookup"><span data-stu-id="73ba3-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="73ba3-112">Os arquivos PO dão suporte à pluralização, ao contrário dos arquivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="73ba3-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="73ba3-113">Os arquivos PO não são compilados como os arquivos *.resx*.</span><span class="sxs-lookup"><span data-stu-id="73ba3-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="73ba3-114">Dessa forma, não são necessárias ferramentas especializadas nem etapas de build.</span><span class="sxs-lookup"><span data-stu-id="73ba3-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="73ba3-115">Os arquivos PO funcionam bem com ferramentas de colaboração de edição online.</span><span class="sxs-lookup"><span data-stu-id="73ba3-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="73ba3-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="73ba3-116">Example</span></span>

<span data-ttu-id="73ba3-117">Este é um arquivo PO de exemplo que contém a tradução de duas cadeias de caracteres em francês, incluindo uma com sua forma plural:</span><span class="sxs-lookup"><span data-stu-id="73ba3-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="73ba3-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="73ba3-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="73ba3-119">Este exemplo usa a seguinte sintaxe:</span><span class="sxs-lookup"><span data-stu-id="73ba3-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="73ba3-120">`#:`: um comentário que indica o contexto da cadeia de caracteres a ser traduzida.</span><span class="sxs-lookup"><span data-stu-id="73ba3-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="73ba3-121">A mesma cadeia de caracteres pode ser traduzida de modo diferente, dependendo do local em que ela está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="73ba3-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="73ba3-122">`msgid`: a cadeia de caracteres não traduzida.</span><span class="sxs-lookup"><span data-stu-id="73ba3-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="73ba3-123">`msgstr`: a cadeia de caracteres traduzida.</span><span class="sxs-lookup"><span data-stu-id="73ba3-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="73ba3-124">No caso de suporte à pluralização, entradas adicionais podem ser definidas.</span><span class="sxs-lookup"><span data-stu-id="73ba3-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="73ba3-125">`msgid_plural`: a cadeia de caracteres no plural não traduzida.</span><span class="sxs-lookup"><span data-stu-id="73ba3-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="73ba3-126">`msgstr[0]`: a cadeia de caracteres traduzida para o caso 0.</span><span class="sxs-lookup"><span data-stu-id="73ba3-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="73ba3-127">`msgstr[N]`: a cadeia de caracteres traduzida para o caso N.</span><span class="sxs-lookup"><span data-stu-id="73ba3-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="73ba3-128">A especificação de arquivo PO pode ser encontrada [aqui](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="73ba3-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="73ba3-129">Configurando o suporte a arquivos PO no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73ba3-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="73ba3-130">Este exemplo se baseia em um aplicativo ASP.NET Core MVC gerado com base em um modelo de projeto do Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="73ba3-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="73ba3-131">Referenciando o pacote</span><span class="sxs-lookup"><span data-stu-id="73ba3-131">Referencing the package</span></span>

<span data-ttu-id="73ba3-132">Adicione uma referência ao pacote NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="73ba3-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="73ba3-133">Ele está disponível em [MyGet](https://www.myget.org/) na fonte do pacote a seguir: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="73ba3-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="73ba3-134">O arquivo *.csproj* agora contém uma linha semelhante à seguinte (o número de versão pode variar):</span><span class="sxs-lookup"><span data-stu-id="73ba3-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="73ba3-135">Registrando o serviço</span><span class="sxs-lookup"><span data-stu-id="73ba3-135">Registering the service</span></span>

<span data-ttu-id="73ba3-136">Adicione os serviços necessários ao método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="73ba3-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="73ba3-137">Adicione o middleware necessário ao método `Configure` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="73ba3-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="73ba3-138">Adicione o código a seguir à exibição do Razor de sua escolha.</span><span class="sxs-lookup"><span data-stu-id="73ba3-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="73ba3-139">*About.cshtml* é usado neste exemplo.</span><span class="sxs-lookup"><span data-stu-id="73ba3-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="73ba3-140">Uma instância `IViewLocalizer` é injetada e usada para traduzir o texto “Olá, Mundo!”.</span><span class="sxs-lookup"><span data-stu-id="73ba3-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="73ba3-141">Criando um arquivo PO</span><span class="sxs-lookup"><span data-stu-id="73ba3-141">Creating a PO file</span></span>

<span data-ttu-id="73ba3-142">Crie um arquivo chamado *\<culture code>.po* na pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="73ba3-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="73ba3-143">Neste exemplo, o nome do arquivo é *fr.po* porque o idioma francês é usado:</span><span class="sxs-lookup"><span data-stu-id="73ba3-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

<span data-ttu-id="73ba3-144">Esse arquivo armazena a cadeia de caracteres a ser traduzida e a cadeia de caracteres traduzida do francês.</span><span class="sxs-lookup"><span data-stu-id="73ba3-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="73ba3-145">As traduções são revertidas para a cultura pai, se necessário.</span><span class="sxs-lookup"><span data-stu-id="73ba3-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="73ba3-146">Neste exemplo, o arquivo *fr.po* é usado se a cultura solicitada é `fr-FR` ou `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="73ba3-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="73ba3-147">Testando o aplicativo</span><span class="sxs-lookup"><span data-stu-id="73ba3-147">Testing the application</span></span>

<span data-ttu-id="73ba3-148">Execute o aplicativo e navegue para a URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="73ba3-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="73ba3-149">O texto **Olá, Mundo!**</span><span class="sxs-lookup"><span data-stu-id="73ba3-149">The text **Hello world!**</span></span> <span data-ttu-id="73ba3-150">é exibido.</span><span class="sxs-lookup"><span data-stu-id="73ba3-150">is displayed.</span></span>

<span data-ttu-id="73ba3-151">Navegue para a URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="73ba3-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="73ba3-152">O texto **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="73ba3-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="73ba3-153">é exibido.</span><span class="sxs-lookup"><span data-stu-id="73ba3-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="73ba3-154">Pluralização</span><span class="sxs-lookup"><span data-stu-id="73ba3-154">Pluralization</span></span>

<span data-ttu-id="73ba3-155">Os arquivos PO dão suporte a formas de pluralização, que são úteis quando a mesma cadeia de caracteres precisa ser traduzida de modo diferente de acordo com uma cardinalidade.</span><span class="sxs-lookup"><span data-stu-id="73ba3-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="73ba3-156">Essa tarefa torna-se complicada pelo fato de que cada idioma define regras personalizadas para selecionar qual cadeia de caracteres será usada de acordo com a cardinalidade.</span><span class="sxs-lookup"><span data-stu-id="73ba3-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="73ba3-157">O pacote de Localização do Orchard fornece uma API para invocar essas diferentes formas plurais automaticamente.</span><span class="sxs-lookup"><span data-stu-id="73ba3-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="73ba3-158">Criando arquivos PO de pluralização</span><span class="sxs-lookup"><span data-stu-id="73ba3-158">Creating pluralization PO files</span></span>

<span data-ttu-id="73ba3-159">Adicione o seguinte conteúdo ao arquivo *fr.po* mencionado anteriormente:</span><span class="sxs-lookup"><span data-stu-id="73ba3-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="73ba3-160">Consulte [O que é um arquivo PO?](#what-is-a-po-file) para obter uma explicação do que representa cada entrada neste exemplo.</span><span class="sxs-lookup"><span data-stu-id="73ba3-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="73ba3-161">Adicionando um idioma usando diferentes formas de pluralização</span><span class="sxs-lookup"><span data-stu-id="73ba3-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="73ba3-162">Cadeias de caracteres em inglês e francês foram usadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="73ba3-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="73ba3-163">O inglês e o francês têm apenas duas formas de pluralização e compartilham as mesmas regras de forma, o que significa que uma cardinalidade de um é mapeada para a primeira forma plural.</span><span class="sxs-lookup"><span data-stu-id="73ba3-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="73ba3-164">Qualquer outra cardinalidade é mapeada para a segunda forma plural.</span><span class="sxs-lookup"><span data-stu-id="73ba3-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="73ba3-165">Nem todos os idiomas compartilham as mesmas regras.</span><span class="sxs-lookup"><span data-stu-id="73ba3-165">Not all languages share the same rules.</span></span> <span data-ttu-id="73ba3-166">Isso é ilustrado com o idioma tcheco, que tem três formas plurais.</span><span class="sxs-lookup"><span data-stu-id="73ba3-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="73ba3-167">Crie o arquivo `cs.po` da seguinte maneira e observe como a pluralização precisa de três traduções diferentes:</span><span class="sxs-lookup"><span data-stu-id="73ba3-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/POLocalization/cs.po)]

<span data-ttu-id="73ba3-168">Para aceitar localizações para o tcheco, adicione `"cs"` à lista de culturas com suporte no método `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="73ba3-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="73ba3-169">Edite o arquivo *Views/Home/About.cshtml* para renderizar cadeias de caracteres localizadas no plural para várias cardinalidades:</span><span class="sxs-lookup"><span data-stu-id="73ba3-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="73ba3-170">**Observação:** em um cenário do mundo real, uma variável é usada para representar a contagem.</span><span class="sxs-lookup"><span data-stu-id="73ba3-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="73ba3-171">Aqui, repetimos o mesmo código com três valores diferentes para expor um caso muito específico.</span><span class="sxs-lookup"><span data-stu-id="73ba3-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="73ba3-172">Ao mudar as culturas, o seguinte é observado:</span><span class="sxs-lookup"><span data-stu-id="73ba3-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="73ba3-173">Para `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="73ba3-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="73ba3-174">Para `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="73ba3-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="73ba3-175">Para `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="73ba3-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="73ba3-176">Observe que, para a cultura do tcheco, as três traduções são diferentes.</span><span class="sxs-lookup"><span data-stu-id="73ba3-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="73ba3-177">As culturas do francês e do inglês compartilham a mesma construção para as duas últimas cadeias de caracteres traduzidas.</span><span class="sxs-lookup"><span data-stu-id="73ba3-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="73ba3-178">Tarefas avançadas</span><span class="sxs-lookup"><span data-stu-id="73ba3-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="73ba3-179">Contextualizando cadeias de caracteres</span><span class="sxs-lookup"><span data-stu-id="73ba3-179">Contextualizing strings</span></span>

<span data-ttu-id="73ba3-180">Os aplicativos costumam conter as cadeias de caracteres a serem traduzidas em vários locais.</span><span class="sxs-lookup"><span data-stu-id="73ba3-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="73ba3-181">A mesma cadeia de caracteres pode ter uma tradução diferente em determinados locais em um aplicativo (exibições do Razor ou arquivos de classe).</span><span class="sxs-lookup"><span data-stu-id="73ba3-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="73ba3-182">Um arquivo PO dá suporte à noção de um contexto de arquivo, que pode ser usado para categorizar a cadeia de caracteres que está sendo representada.</span><span class="sxs-lookup"><span data-stu-id="73ba3-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="73ba3-183">Usando um contexto de arquivo, uma cadeia de caracteres pode ser traduzida de forma diferente, dependendo do contexto de arquivo (ou de sua ausência).</span><span class="sxs-lookup"><span data-stu-id="73ba3-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="73ba3-184">Os serviços de localização de PO usam o nome da classe completa ou da exibição que é usado ao traduzir uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="73ba3-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="73ba3-185">Isso é feito definindo o valor na entrada `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="73ba3-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="73ba3-186">Considere uma adição mínima ao exemplo anterior de *fr.po*.</span><span class="sxs-lookup"><span data-stu-id="73ba3-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="73ba3-187">Uma exibição do Razor localizada em *Views/Home/About.cshtml* pode ser definida com o contexto de arquivo definindo o valor da entrada `msgctxt` reservada:</span><span class="sxs-lookup"><span data-stu-id="73ba3-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="73ba3-188">Com o `msgctxt` definido assim, a tradução de texto ocorre durante a navegação para `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="73ba3-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="73ba3-189">A tradução não ocorre durante a navegação para `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="73ba3-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="73ba3-190">Quando não é encontrada a correspondência de nenhuma entrada específica com um contexto de arquivo fornecido, o mecanismo de fallback do Orchard Core procura um arquivo PO apropriado sem contexto.</span><span class="sxs-lookup"><span data-stu-id="73ba3-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="73ba3-191">Supondo que não haja nenhum contexto de arquivo específico definido para *Views/Home/Contact.cshtml*, a navegação para `/Home/Contact?culture=fr-FR` carrega um arquivo PO, como:</span><span class="sxs-lookup"><span data-stu-id="73ba3-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="73ba3-192">Alterando o local dos arquivos PO</span><span class="sxs-lookup"><span data-stu-id="73ba3-192">Changing the location of PO files</span></span>

<span data-ttu-id="73ba3-193">A localização padrão dos arquivos PO pode ser alterada em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="73ba3-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="73ba3-194">Neste exemplo, os arquivos PO são carregados da pasta *Localization*.</span><span class="sxs-lookup"><span data-stu-id="73ba3-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="73ba3-195">Implementando uma lógica personalizada para encontrar arquivos de localização</span><span class="sxs-lookup"><span data-stu-id="73ba3-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="73ba3-196">Quando uma lógica mais complexa é necessária para localizar arquivos PO, a interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` pode ser implementada e registrada como um serviço.</span><span class="sxs-lookup"><span data-stu-id="73ba3-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="73ba3-197">Isso é útil quando arquivos PO podem ser armazenados em locais variáveis ou quando os arquivos precisam ser encontrados em uma hierarquia de pastas.</span><span class="sxs-lookup"><span data-stu-id="73ba3-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="73ba3-198">Usando um idioma pluralizado padrão diferente</span><span class="sxs-lookup"><span data-stu-id="73ba3-198">Using a different default pluralized language</span></span>

<span data-ttu-id="73ba3-199">O pacote inclui um método de extensão `Plural` específico a duas formas plurais.</span><span class="sxs-lookup"><span data-stu-id="73ba3-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="73ba3-200">Para idiomas que exigem mais formas plurais, crie um método de extensão.</span><span class="sxs-lookup"><span data-stu-id="73ba3-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="73ba3-201">Com um método de extensão, você não precisará fornecer nenhum arquivo de localização para o idioma padrão – as cadeias de caracteres originais já estão disponíveis diretamente no código.</span><span class="sxs-lookup"><span data-stu-id="73ba3-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="73ba3-202">Use a sobrecarga `Plural(int count, string[] pluralForms, params object[] arguments)` mais genérica que aceita uma matriz de cadeia de caracteres de traduções.</span><span class="sxs-lookup"><span data-stu-id="73ba3-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>
