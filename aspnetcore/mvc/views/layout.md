---
title: Layout no ASP.NET Core
author: ardalis
description: Saiba como usar layouts comuns, compartilhar diretivas e executar um código comum antes de renderizar exibições em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/18/2018
uid: mvc/views/layout
ms.openlocfilehash: 1bd225c804b333efea834a46b7d9ba46b1bb69d8
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410567"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="f25d0-103">Layout no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f25d0-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="f25d0-104">Por [Steve Smith](https://ardalis.com/) e [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="f25d0-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="f25d0-105">Páginas e exibições com frequência compartilham elementos visuais e programáticos.</span><span class="sxs-lookup"><span data-stu-id="f25d0-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="f25d0-106">Este artigo demonstra como:</span><span class="sxs-lookup"><span data-stu-id="f25d0-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="f25d0-107">Usar layouts comuns.</span><span class="sxs-lookup"><span data-stu-id="f25d0-107">Use common layouts.</span></span>
* <span data-ttu-id="f25d0-108">Compartilhar diretivas.</span><span class="sxs-lookup"><span data-stu-id="f25d0-108">Share directives.</span></span>
* <span data-ttu-id="f25d0-109">Executar o código comum antes de renderizar páginas ou modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="f25d0-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="f25d0-110">Este documento discute os layouts para as duas abordagens diferentes ao ASP.NET Core MVC: Razor Pages e controladores com exibições.</span><span class="sxs-lookup"><span data-stu-id="f25d0-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="f25d0-111">Para este tópico, as diferenças são mínimas:</span><span class="sxs-lookup"><span data-stu-id="f25d0-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="f25d0-112">Razor Pages estão na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f25d0-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="f25d0-113">Controladores com exibições usam uma pasta *Views* pasta exibições.</span><span class="sxs-lookup"><span data-stu-id="f25d0-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="f25d0-114">O que é um layout</span><span class="sxs-lookup"><span data-stu-id="f25d0-114">What is a Layout</span></span>

<span data-ttu-id="f25d0-115">A maioria dos aplicativos Web tem um layout comum que fornece aos usuários uma experiência consistente durante sua navegação de uma página a outra.</span><span class="sxs-lookup"><span data-stu-id="f25d0-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="f25d0-116">O layout normalmente inclui elementos comuns de interface do usuário, como o cabeçalho do aplicativo, elementos de menu ou de navegação e rodapé.</span><span class="sxs-lookup"><span data-stu-id="f25d0-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Exemplo de layout da página](layout/_static/page-layout.png)

<span data-ttu-id="f25d0-118">Estruturas HTML comuns, como scripts e folhas de estilo, também são usadas com frequência por muitas páginas em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f25d0-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="f25d0-119">Todos esses elementos compartilhados podem ser definidos em um arquivo de *layout*, que pode então ser referenciado por qualquer exibição usada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f25d0-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="f25d0-120">Os layouts reduzem o código duplicado em exibições.</span><span class="sxs-lookup"><span data-stu-id="f25d0-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="f25d0-121">Por convenção, o layout padrão de um aplicativo ASP.NET Core é chamado *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f25d0-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="f25d0-122">O arquivo de layout para novos projetos do ASP.NET Core criados com os modelos:</span><span class="sxs-lookup"><span data-stu-id="f25d0-122">The layout file for new ASP.NET Core projects created with the templates:</span></span>

* <span data-ttu-id="f25d0-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f25d0-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![pasta de páginas no gerenciador de soluções](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="f25d0-125">Controlador com exibições: *Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f25d0-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

 ![pasta de exibições no gerenciador de soluções](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="f25d0-127">O layout define um modelo de nível superior para exibições no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f25d0-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="f25d0-128">Os aplicativos não exigem um layout.</span><span class="sxs-lookup"><span data-stu-id="f25d0-128">Apps don't require a layout.</span></span> <span data-ttu-id="f25d0-129">Os aplicativos podem definir mais de um layout, com diferentes exibições especificando diferentes layouts.</span><span class="sxs-lookup"><span data-stu-id="f25d0-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="f25d0-130">O código a seguir mostra o arquivo de layout para um modelo de projeto criado com um controlador e exibições:</span><span class="sxs-lookup"><span data-stu-id="f25d0-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-html[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="f25d0-131">Especificando um layout</span><span class="sxs-lookup"><span data-stu-id="f25d0-131">Specifying a Layout</span></span>

<span data-ttu-id="f25d0-132">As exibições do Razor têm uma propriedade `Layout`.</span><span class="sxs-lookup"><span data-stu-id="f25d0-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="f25d0-133">As exibições individuais especificam um layout com a configuração dessa propriedade:</span><span class="sxs-lookup"><span data-stu-id="f25d0-133">Individual views specify a layout by setting this property:</span></span>

[!code-html[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="f25d0-134">O layout especificado pode usar um caminho completo (por exemplo, */Pages/Shared/_Layout.cshtml* ou */Views/Shared/_Layout.cshtml*) ou um nome parcial (exemplo: `_Layout`).</span><span class="sxs-lookup"><span data-stu-id="f25d0-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="f25d0-135">Quando um nome parcial for fornecido, o mecanismo de exibição do Razor pesquisará o arquivo de layout usando seu processo de descoberta padrão.</span><span class="sxs-lookup"><span data-stu-id="f25d0-135">When a partial name is provided, the Razor view engine will search for the layout file using its standard discovery process.</span></span> <span data-ttu-id="f25d0-136">A pasta em que o método do manipulador (ou controlador) existe é pesquisada primeiro, seguida pela pasta *Shared*.</span><span class="sxs-lookup"><span data-stu-id="f25d0-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="f25d0-137">Esse processo de descoberta é idêntico àquele usado para descobrir [exibições parciais](partial.md).</span><span class="sxs-lookup"><span data-stu-id="f25d0-137">This discovery process is identical to the one used to discover [partial views](partial.md).</span></span>

<span data-ttu-id="f25d0-138">Por padrão, todo layout precisa chamar `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="f25d0-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="f25d0-139">Sempre que a chamada a `RenderBody` for feita, o conteúdo da exibição será renderizado.</span><span class="sxs-lookup"><span data-stu-id="f25d0-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>

### <a name="sections"></a><span data-ttu-id="f25d0-140">Seções</span><span class="sxs-lookup"><span data-stu-id="f25d0-140">Sections</span></span>

<span data-ttu-id="f25d0-141">Um layout, opcionalmente, pode referenciar uma ou mais *seções*, chamando `RenderSection`.</span><span class="sxs-lookup"><span data-stu-id="f25d0-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="f25d0-142">As seções fornecem uma maneira de organizar o local em que determinados elementos da página devem ser colocados.</span><span class="sxs-lookup"><span data-stu-id="f25d0-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="f25d0-143">Cada chamada a `RenderSection` pode especificar se essa seção é obrigatória ou opcional:</span><span class="sxs-lookup"><span data-stu-id="f25d0-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
@section Scripts {
    @RenderSection("Scripts", required: false)
}
```

<span data-ttu-id="f25d0-144">Se uma seção obrigatória não for encontrada, uma exceção será gerada.</span><span class="sxs-lookup"><span data-stu-id="f25d0-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="f25d0-145">As exibições individuais especificam o conteúdo a ser renderizado em uma seção usando a sintaxe Razor `@section`.</span><span class="sxs-lookup"><span data-stu-id="f25d0-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="f25d0-146">Se uma página ou exibição definir uma seção, ela precisará ser renderizada (ou ocorrerá um erro).</span><span class="sxs-lookup"><span data-stu-id="f25d0-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="f25d0-147">Uma definição `@section` de exemplo na exibição do Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="f25d0-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
}
```

<span data-ttu-id="f25d0-148">No código anterior, *scripts/main.js* é adicionado à seção `scripts` em uma página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="f25d0-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="f25d0-149">Outras páginas ou exibições no mesmo aplicativo podem não exigir esse script e não definirão uma seção de scripts.</span><span class="sxs-lookup"><span data-stu-id="f25d0-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="f25d0-150">A marcação a seguir usa o [Auxiliar de Marca Parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) para renderizar *_ValidationScriptsPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f25d0-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="f25d0-151">A marcação anterior foi gerada pela [Identidade de scaffolding](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="f25d0-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="f25d0-152">As seções definidas em uma página ou exibição estão disponíveis apenas em sua página de layout imediata.</span><span class="sxs-lookup"><span data-stu-id="f25d0-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="f25d0-153">Elas não podem ser referenciadas em parciais, componentes de exibição ou outras partes do sistema de exibição.</span><span class="sxs-lookup"><span data-stu-id="f25d0-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="f25d0-154">Ignorando seções</span><span class="sxs-lookup"><span data-stu-id="f25d0-154">Ignoring sections</span></span>

<span data-ttu-id="f25d0-155">Por padrão, o corpo e todas as seções de uma página de conteúdo precisam ser renderizados pela página de layout.</span><span class="sxs-lookup"><span data-stu-id="f25d0-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="f25d0-156">O mecanismo de exibição do Razor impõe isso acompanhando se o corpo e cada seção foram renderizados.</span><span class="sxs-lookup"><span data-stu-id="f25d0-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="f25d0-157">Para instruir o mecanismo de exibição a ignorar o corpo ou as seções, chame os métodos `IgnoreBody` e `IgnoreSection`.</span><span class="sxs-lookup"><span data-stu-id="f25d0-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="f25d0-158">O corpo e cada seção em uma página do Razor precisam ser renderizados ou ignorados.</span><span class="sxs-lookup"><span data-stu-id="f25d0-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="f25d0-159">Importando diretivas compartilhadas</span><span class="sxs-lookup"><span data-stu-id="f25d0-159">Importing Shared Directives</span></span>

<span data-ttu-id="f25d0-160">Exibições e páginas podem usar diretivas do Razor para importar namespaces e usar [injeção de dependência](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="f25d0-160">Views and pages can use Razor directives to importing namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="f25d0-161">Diretivas compartilhadas por diversas exibições podem ser especificadas em um arquivo *_ViewImports.cshtml* comum.</span><span class="sxs-lookup"><span data-stu-id="f25d0-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f25d0-162">O arquivo `_ViewImports` dá suporte às seguintes diretivas:</span><span class="sxs-lookup"><span data-stu-id="f25d0-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="f25d0-163">O arquivo não dá suporte a outros recursos do Razor, como funções e definições de seção.</span><span class="sxs-lookup"><span data-stu-id="f25d0-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="f25d0-164">Um arquivo `_ViewImports.cshtml` de exemplo:</span><span class="sxs-lookup"><span data-stu-id="f25d0-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-html[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="f25d0-165">O arquivo *_ViewImports.cshtml* para um aplicativo ASP.NET Core MVC normalmente é colocado na pasta *Pages* (ou *Views*).</span><span class="sxs-lookup"><span data-stu-id="f25d0-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="f25d0-166">Um arquivo *_ViewImports.cshtml* pode ser colocado em qualquer pasta, caso em que só será aplicado a páginas ou exibições nessa pasta e em suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="f25d0-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="f25d0-167">Arquivos `_ViewImports` são processados começando no nível raiz e, em seguida, para cada pasta até o local da página ou da exibição em si.</span><span class="sxs-lookup"><span data-stu-id="f25d0-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="f25d0-168">As configurações `_ViewImports` especificadas no nível raiz podem ser substituídas no nível da pasta.</span><span class="sxs-lookup"><span data-stu-id="f25d0-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="f25d0-169">Por exemplo, suponha:</span><span class="sxs-lookup"><span data-stu-id="f25d0-169">For example, suppose:</span></span>

* <span data-ttu-id="f25d0-170">O arquivo *_ViewImports.cshtml* no nível de raiz inclui `@model MyModel1` e `@addTagHelper *, MyTagHelper1`.</span><span class="sxs-lookup"><span data-stu-id="f25d0-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="f25d0-171">Um arquivo *_ViewImports.cshtml* de subpasta inclui `@model MyModel2` e `@addTagHelper *, MyTagHelper2`.</span><span class="sxs-lookup"><span data-stu-id="f25d0-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="f25d0-172">Páginas e exibições na subpasta terão acesso a Auxiliares de Marca e ao modelo `MyModel2`.</span><span class="sxs-lookup"><span data-stu-id="f25d0-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="f25d0-173">Se vários arquivos *_ViewImports.cshtml* forem encontrados na hierarquia de arquivos, o comportamento combinado das diretivas será:</span><span class="sxs-lookup"><span data-stu-id="f25d0-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="f25d0-174">`@addTagHelper`, `@removeTagHelper`: todos são executados, em ordem</span><span class="sxs-lookup"><span data-stu-id="f25d0-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="f25d0-175">`@tagHelperPrefix`: o mais próximo à exibição substitui todos os outros</span><span class="sxs-lookup"><span data-stu-id="f25d0-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="f25d0-176">`@model`: o mais próximo à exibição substitui todos os outros</span><span class="sxs-lookup"><span data-stu-id="f25d0-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="f25d0-177">`@inherits`: o mais próximo à exibição substitui todos os outros</span><span class="sxs-lookup"><span data-stu-id="f25d0-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="f25d0-178">`@using`: todos são incluídos; duplicatas são ignoradas</span><span class="sxs-lookup"><span data-stu-id="f25d0-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="f25d0-179">`@inject`: para cada propriedade, o mais próximo à exibição substitui todos os outros com o mesmo nome de propriedade</span><span class="sxs-lookup"><span data-stu-id="f25d0-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="f25d0-180">Executando o código antes de cada exibição</span><span class="sxs-lookup"><span data-stu-id="f25d0-180">Running Code Before Each View</span></span>

<span data-ttu-id="f25d0-181">Código que precisa ser executado antes de cada exibição ou página precisa ser colocada no arquivo *_ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f25d0-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="f25d0-182">Por convenção, o arquivo *_ViewStart.cshtml* está localizado na pasta *Pages* (ou *Views*).</span><span class="sxs-lookup"><span data-stu-id="f25d0-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="f25d0-183">As instruções listadas em *_ViewStart.cshtml* são executadas antes de cada exibição completa (não layouts nem exibições parciais).</span><span class="sxs-lookup"><span data-stu-id="f25d0-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="f25d0-184">Como [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* é hierárquico.</span><span class="sxs-lookup"><span data-stu-id="f25d0-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="f25d0-185">Se um arquivo *_ViewStart.cshtml* for definido na pasta de exibições ou páginas, ele será executado depois daquele definido na raiz da pasta *Pages* (ou *Views*) (se houver).</span><span class="sxs-lookup"><span data-stu-id="f25d0-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="f25d0-186">Um arquivo *_ViewStart.cshtml* de amostra:</span><span class="sxs-lookup"><span data-stu-id="f25d0-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-html[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="f25d0-187">O arquivo acima especifica que todas as exibições usarão o layout *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f25d0-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="f25d0-188">*_ViewStart.cshtml* é *_ViewImports.cshtml* normalmente **não** são colocados na pasta */Pages/Shared* (ou */Views/Shared*).</span><span class="sxs-lookup"><span data-stu-id="f25d0-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="f25d0-189">As versões no nível do aplicativo desses arquivos devem ser colocadas diretamente na pasta */Pages* (ou */Views*).</span><span class="sxs-lookup"><span data-stu-id="f25d0-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
