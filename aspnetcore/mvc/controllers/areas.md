---
title: Áreas no ASP.NET Core
author: rick-anderson
description: Saiba por que as áreas são um recurso do ASP.NET MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace (para roteamento) e uma estrutura de pasta (para exibições) separados.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 41f7bdd6dbb3e33f843cb2a765dd30f98c81ce21
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665399"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="d669b-103">Áreas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d669b-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="d669b-104">Por [Dhananjay Kumar](https://twitter.com/debug_mode) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d669b-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d669b-105">As áreas são um recurso ASP.NET usado para organizar a funcionalidade relacionada em um grupo como separado:</span><span class="sxs-lookup"><span data-stu-id="d669b-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="d669b-106">Namespace para roteamento.</span><span class="sxs-lookup"><span data-stu-id="d669b-106">Namespace for routing.</span></span>
* <span data-ttu-id="d669b-107">Estrutura de pastas para exibições e Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d669b-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="d669b-108">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area`, a `controller` e `action` ou a uma Razor Page, `page`.</span><span class="sxs-lookup"><span data-stu-id="d669b-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="d669b-109">As áreas proporcionam uma maneira de particionar um aplicativo Web do ASP.NET Core em grupos funcionais menores, cada um com seu próprio conjunto de Razor Pages, controladores, exibições e modelos.</span><span class="sxs-lookup"><span data-stu-id="d669b-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="d669b-110">Uma área é efetivamente uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="d669b-111">Em um projeto Web do ASP.NET Core, os componentes lógicos como páginas, modelo, controlador e modo de exibição são mantidos em pastas diferentes.</span><span class="sxs-lookup"><span data-stu-id="d669b-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="d669b-112">O runtime do ASP.NET Core usa as convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="d669b-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="d669b-113">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="d669b-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="d669b-114">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d669b-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="d669b-115">Cada uma dessas unidades tem sua própria área para conter exibições, controladores, Razor Pages e modelos.</span><span class="sxs-lookup"><span data-stu-id="d669b-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="d669b-116">Considere o uso de Áreas em um projeto quando:</span><span class="sxs-lookup"><span data-stu-id="d669b-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="d669b-117">O aplicativo é composto por vários componentes funcionais de alto nível que podem ser separados logicamente.</span><span class="sxs-lookup"><span data-stu-id="d669b-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="d669b-118">Você deseja particionar o aplicativo para que cada área funcional possa ser trabalhada de forma independente.</span><span class="sxs-lookup"><span data-stu-id="d669b-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="d669b-119">[Exibir ou baixar um código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d669b-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="d669b-120">O exemplo de download fornece um aplicativo básico para áreas de teste.</span><span class="sxs-lookup"><span data-stu-id="d669b-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="d669b-121">Se você estiver usando o Razor Pages, confira [Áreas com Razor Pages](#areas-with-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="d669b-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="d669b-122">Áreas para os controladores com modos de exibição</span><span class="sxs-lookup"><span data-stu-id="d669b-122">Areas for controllers with views</span></span>

<span data-ttu-id="d669b-123">Um aplicativo Web do ASP.NET Core típico usando áreas, controladores e exibições contém o seguinte:</span><span class="sxs-lookup"><span data-stu-id="d669b-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="d669b-124">Uma [estrutura de pastas da área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="d669b-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="d669b-125">Controladores com o atributo [`[Area]`](#attribute) para associar o controlador à área:</span><span class="sxs-lookup"><span data-stu-id="d669b-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="d669b-126">A [rota de área adicionada à inicialização](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="d669b-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="d669b-127">Estrutura de pastas da área</span><span class="sxs-lookup"><span data-stu-id="d669b-127">Area folder structure</span></span>

<span data-ttu-id="d669b-128">Considere um aplicativo que tem dois grupos lógicos, *Produtos* e *Serviços*.</span><span class="sxs-lookup"><span data-stu-id="d669b-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="d669b-129">Usando áreas, a estrutura de pastas seria semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="d669b-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="d669b-130">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="d669b-130">Project name</span></span>
  * <span data-ttu-id="d669b-131">Áreas</span><span class="sxs-lookup"><span data-stu-id="d669b-131">Areas</span></span>
    * <span data-ttu-id="d669b-132">Produtos</span><span class="sxs-lookup"><span data-stu-id="d669b-132">Products</span></span>
      * <span data-ttu-id="d669b-133">Controladores</span><span class="sxs-lookup"><span data-stu-id="d669b-133">Controllers</span></span>
        * <span data-ttu-id="d669b-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d669b-134">HomeController.cs</span></span>
        * <span data-ttu-id="d669b-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="d669b-135">ManageController.cs</span></span>
      * <span data-ttu-id="d669b-136">Exibições</span><span class="sxs-lookup"><span data-stu-id="d669b-136">Views</span></span>
        * <span data-ttu-id="d669b-137">Home</span><span class="sxs-lookup"><span data-stu-id="d669b-137">Home</span></span>
          * <span data-ttu-id="d669b-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-138">Index.cshtml</span></span>
        * <span data-ttu-id="d669b-139">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="d669b-139">Manage</span></span>
          * <span data-ttu-id="d669b-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-140">Index.cshtml</span></span>
          * <span data-ttu-id="d669b-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-141">About.cshtml</span></span>
    * <span data-ttu-id="d669b-142">Serviços</span><span class="sxs-lookup"><span data-stu-id="d669b-142">Services</span></span>
      * <span data-ttu-id="d669b-143">Controladores</span><span class="sxs-lookup"><span data-stu-id="d669b-143">Controllers</span></span>
        * <span data-ttu-id="d669b-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d669b-144">HomeController.cs</span></span>
      * <span data-ttu-id="d669b-145">Exibições</span><span class="sxs-lookup"><span data-stu-id="d669b-145">Views</span></span>
        * <span data-ttu-id="d669b-146">Home</span><span class="sxs-lookup"><span data-stu-id="d669b-146">Home</span></span>
          * <span data-ttu-id="d669b-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-147">Index.cshtml</span></span>

<span data-ttu-id="d669b-148">Enquanto o layout anterior é típico ao usar áreas, somente os arquivos de exibição são necessários para usar essa estrutura de pastas.</span><span class="sxs-lookup"><span data-stu-id="d669b-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="d669b-149">Pesquisas de descoberta de exibição para um arquivo de exibição de área correspondente, na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="d669b-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="d669b-150">Associar o controlador a uma área</span><span class="sxs-lookup"><span data-stu-id="d669b-150">Associate the controller with an Area</span></span>

<span data-ttu-id="d669b-151">Controladores de área são designados com o atributo [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute):</span><span class="sxs-lookup"><span data-stu-id="d669b-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="d669b-152">Adicionar rota de área</span><span class="sxs-lookup"><span data-stu-id="d669b-152">Add Area route</span></span>

<span data-ttu-id="d669b-153">As rotas de área normalmente usam [Roteamento convencional](xref:mvc/controllers/routing#cr) em vez de [Roteamento de atributos](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="d669b-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="d669b-154">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="d669b-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="d669b-155">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="d669b-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="d669b-156">`{area:...}` pode ser usado como um token em modelos de rota, se o espaço de URL é uniforme entre todas as áreas:</span><span class="sxs-lookup"><span data-stu-id="d669b-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="d669b-157">No código anterior, `exists` aplica uma restrição de que a rota deve corresponder a uma área.</span><span class="sxs-lookup"><span data-stu-id="d669b-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="d669b-158">Usando `{area:...}` com `MapControllerRoute`:</span><span class="sxs-lookup"><span data-stu-id="d669b-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="d669b-159">É o mecanismo menos complicado para adicionar roteamento a áreas.</span><span class="sxs-lookup"><span data-stu-id="d669b-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="d669b-160">Corresponde a todos os controladores com o atributo `[Area("Area name")]`.</span><span class="sxs-lookup"><span data-stu-id="d669b-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="d669b-161">O código a seguir usa <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> para criar duas rotas de área nomeadas:</span><span class="sxs-lookup"><span data-stu-id="d669b-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="d669b-162">Para obter mais informações, veja [Roteamento de área](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="d669b-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="d669b-163">Geração de links com áreas do MVC</span><span class="sxs-lookup"><span data-stu-id="d669b-163">Link generation with MVC areas</span></span>

<span data-ttu-id="d669b-164">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) mostra geração de link com a área especificada:</span><span class="sxs-lookup"><span data-stu-id="d669b-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d669b-165">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém:</span><span class="sxs-lookup"><span data-stu-id="d669b-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="d669b-166">Os links anteriores.</span><span class="sxs-lookup"><span data-stu-id="d669b-166">The preceding links.</span></span>
* <span data-ttu-id="d669b-167">Links semelhantes aos anteriores, exceto `area` não são especificados.</span><span class="sxs-lookup"><span data-stu-id="d669b-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="d669b-168">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="d669b-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d669b-169">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área e no mesmo controlador.</span><span class="sxs-lookup"><span data-stu-id="d669b-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="d669b-170">Quando a área ou o controlador não for especificado, o roteamento dependerá dos valores do [ambiente](xref:mvc/controllers/routing#ambient).</span><span class="sxs-lookup"><span data-stu-id="d669b-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="d669b-171">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="d669b-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d669b-172">Em muitos casos para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos com a marcação que não especifica a área.</span><span class="sxs-lookup"><span data-stu-id="d669b-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="d669b-173">Para obter mais informações, veja [Roteamento para ações do controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="d669b-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="d669b-174">Layout compartilhado para áreas usando o arquivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="d669b-175">Para compartilhar um layout comum para todo o aplicativo, mantenha o *_ViewStart. cshtml* na [pasta raiz do aplicativo](#arf).</span><span class="sxs-lookup"><span data-stu-id="d669b-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="d669b-176">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="d669b-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="d669b-177">Pasta raiz do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d669b-177">Application root folder</span></span>

<span data-ttu-id="d669b-178">A pasta raiz do aplicativo é a pasta que contém *Startup.cs* no aplicativo Web criado com os modelos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d669b-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="d669b-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="d669b-180">*/Views/_ViewImports. cshtml*, para MVC e */pages/_ViewImports. cshtml* para Razor pages, não é importado para modos de exibição em áreas.</span><span class="sxs-lookup"><span data-stu-id="d669b-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="d669b-181">Use uma das abordagens a seguir para fornecer importações de exibição a todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="d669b-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="d669b-182">Adicione *_ViewImports. cshtml* à [pasta raiz do aplicativo](#arf).</span><span class="sxs-lookup"><span data-stu-id="d669b-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="d669b-183">Um *_ViewImports. cshtml* na pasta raiz do aplicativo será aplicado a todas as exibições no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="d669b-184">Copie o arquivo *_ViewImports. cshtml* para a pasta de exibição apropriada em áreas.</span><span class="sxs-lookup"><span data-stu-id="d669b-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="d669b-185">O arquivo *_ViewImports. cshtml* normalmente contém instruções de [marcação de auxiliares de marca](xref:mvc/views/tag-helpers/intro) , `@using`e `@inject`.</span><span class="sxs-lookup"><span data-stu-id="d669b-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="d669b-186">Para obter mais informações, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="d669b-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="d669b-187">Alterar a pasta de área padrão em que as exibições são armazenadas</span><span class="sxs-lookup"><span data-stu-id="d669b-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="d669b-188">O código a seguir altera a pasta da área padrão de `"Areas"` para `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="d669b-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="d669b-189">Áreas com Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d669b-189">Areas with Razor Pages</span></span>

<span data-ttu-id="d669b-190">As áreas com Razor Pages exigem uma pasta `Areas/<area name>/Pages` na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="d669b-191">A seguinte estrutura de pasta é usada com o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span><span class="sxs-lookup"><span data-stu-id="d669b-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="d669b-192">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="d669b-192">Project name</span></span>
  * <span data-ttu-id="d669b-193">Áreas</span><span class="sxs-lookup"><span data-stu-id="d669b-193">Areas</span></span>
    * <span data-ttu-id="d669b-194">Produtos</span><span class="sxs-lookup"><span data-stu-id="d669b-194">Products</span></span>
      * <span data-ttu-id="d669b-195">Páginas</span><span class="sxs-lookup"><span data-stu-id="d669b-195">Pages</span></span>
        * <span data-ttu-id="d669b-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="d669b-196">_ViewImports</span></span>
        * <span data-ttu-id="d669b-197">Sobre</span><span class="sxs-lookup"><span data-stu-id="d669b-197">About</span></span>
        * <span data-ttu-id="d669b-198">Index</span><span class="sxs-lookup"><span data-stu-id="d669b-198">Index</span></span>
    * <span data-ttu-id="d669b-199">Serviços</span><span class="sxs-lookup"><span data-stu-id="d669b-199">Services</span></span>
      * <span data-ttu-id="d669b-200">Páginas</span><span class="sxs-lookup"><span data-stu-id="d669b-200">Pages</span></span>
        * <span data-ttu-id="d669b-201">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="d669b-201">Manage</span></span>
          * <span data-ttu-id="d669b-202">Sobre</span><span class="sxs-lookup"><span data-stu-id="d669b-202">About</span></span>
          * <span data-ttu-id="d669b-203">Index</span><span class="sxs-lookup"><span data-stu-id="d669b-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="d669b-204">Geração de links com áreas e Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d669b-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="d669b-205">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra a geração de links com a área especificada (por exemplo, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="d669b-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d669b-206">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="d669b-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="d669b-207">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="d669b-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d669b-208">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área.</span><span class="sxs-lookup"><span data-stu-id="d669b-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="d669b-209">Quando a área não for especificada, o roteamento dependerá dos valores do *ambiente*.</span><span class="sxs-lookup"><span data-stu-id="d669b-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="d669b-210">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="d669b-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d669b-211">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="d669b-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="d669b-212">Por exemplo, considere os links gerados a partir do código a seguir:</span><span class="sxs-lookup"><span data-stu-id="d669b-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="d669b-213">Para o código anterior:</span><span class="sxs-lookup"><span data-stu-id="d669b-213">For the preceding code:</span></span>

* <span data-ttu-id="d669b-214">O link gerado de `<a asp-page="/Manage/About">` só estará correto quando a última solicitação tiver sido para uma página na área `Services`.</span><span class="sxs-lookup"><span data-stu-id="d669b-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="d669b-215">Por exemplo, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="d669b-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="d669b-216">O link gerado a partir `<a asp-page="/About">` só estará correto quando a última solicitação tiver sido para uma página na área `/Home`.</span><span class="sxs-lookup"><span data-stu-id="d669b-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="d669b-217">O código vem do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="d669b-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="d669b-218">Importar o namespace e os Auxiliares de marca com o arquivo _ViewImports</span><span class="sxs-lookup"><span data-stu-id="d669b-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="d669b-219">Um arquivo *_ViewImports.cshtml* pode ser adicionado a cada pasta *Páginas* da área para importar o namespace e os Auxiliares de Marcação para cada Razor Page na pasta.</span><span class="sxs-lookup"><span data-stu-id="d669b-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="d669b-220">Considere a área *Serviços* do código de exemplo, que não contém um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d669b-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="d669b-221">A marcação a seguir mostra a Página do Razor */Services/Manage/About*:</span><span class="sxs-lookup"><span data-stu-id="d669b-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="d669b-222">Na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="d669b-222">In the preceding markup:</span></span>

* <span data-ttu-id="d669b-223">O nome de domínio totalmente qualificado deve ser usado para especificar o modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="d669b-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="d669b-224">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="d669b-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="d669b-225">No download de exemplo, a área Produtos contém o seguinte arquivo *_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d669b-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="d669b-226">A marcação a seguir mostra a Página do Razor */Products/About*:</span><span class="sxs-lookup"><span data-stu-id="d669b-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="d669b-227">No arquivo anterior, o namespace e a diretiva `@addTagHelper` são importados para o arquivo por meio do arquivo *Areas/Products/Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d669b-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="d669b-228">Para saber mais, confira [Gerenciar o escopo do Auxiliar de Marcação](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importar diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="d669b-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="d669b-229">Layout compartilhado para Áreas do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d669b-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="d669b-230">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="d669b-231">Publicando áreas</span><span class="sxs-lookup"><span data-stu-id="d669b-231">Publishing Areas</span></span>

<span data-ttu-id="d669b-232">Todos os arquivos \*.cshtml e os arquivos do diretório *wwwroot* são publicados em uma saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` são incluídos no arquivo \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="d669b-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d669b-233">Áreas são um recurso do ASP.NET usado para organizar funcionalidades relacionadas em um grupo como um namespace separado (para roteamento) e uma estrutura de pastas (para exibições).</span><span class="sxs-lookup"><span data-stu-id="d669b-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="d669b-234">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area`, a `controller` e `action` ou a uma Razor Page, `page`.</span><span class="sxs-lookup"><span data-stu-id="d669b-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="d669b-235">As áreas proporcionam uma maneira de particionar um aplicativo Web do ASP.NET Core em grupos funcionais menores, cada um com seu próprio conjunto de Razor Pages, controladores, exibições e modelos.</span><span class="sxs-lookup"><span data-stu-id="d669b-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="d669b-236">Uma área é efetivamente uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="d669b-237">Em um projeto Web do ASP.NET Core, os componentes lógicos como páginas, modelo, controlador e modo de exibição são mantidos em pastas diferentes.</span><span class="sxs-lookup"><span data-stu-id="d669b-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="d669b-238">O runtime do ASP.NET Core usa as convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="d669b-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="d669b-239">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="d669b-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="d669b-240">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d669b-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="d669b-241">Cada uma dessas unidades tem sua própria área para conter exibições, controladores, Razor Pages e modelos.</span><span class="sxs-lookup"><span data-stu-id="d669b-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="d669b-242">Considere o uso de Áreas em um projeto quando:</span><span class="sxs-lookup"><span data-stu-id="d669b-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="d669b-243">O aplicativo é composto por vários componentes funcionais de alto nível que podem ser separados logicamente.</span><span class="sxs-lookup"><span data-stu-id="d669b-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="d669b-244">Você deseja particionar o aplicativo para que cada área funcional possa ser trabalhada de forma independente.</span><span class="sxs-lookup"><span data-stu-id="d669b-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="d669b-245">[Exibir ou baixar um código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d669b-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="d669b-246">O exemplo de download fornece um aplicativo básico para áreas de teste.</span><span class="sxs-lookup"><span data-stu-id="d669b-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="d669b-247">Se você estiver usando o Razor Pages, confira [Áreas com Razor Pages](#areas-with-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="d669b-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="d669b-248">Áreas para os controladores com modos de exibição</span><span class="sxs-lookup"><span data-stu-id="d669b-248">Areas for controllers with views</span></span>

<span data-ttu-id="d669b-249">Um aplicativo Web do ASP.NET Core típico usando áreas, controladores e exibições contém o seguinte:</span><span class="sxs-lookup"><span data-stu-id="d669b-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="d669b-250">Uma [estrutura de pastas da área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="d669b-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="d669b-251">Controladores com o atributo [`[Area]`](#attribute) para associar o controlador à área:</span><span class="sxs-lookup"><span data-stu-id="d669b-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="d669b-252">A [rota de área adicionada à inicialização](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="d669b-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="d669b-253">Estrutura de pastas da área</span><span class="sxs-lookup"><span data-stu-id="d669b-253">Area folder structure</span></span>

<span data-ttu-id="d669b-254">Considere um aplicativo que tem dois grupos lógicos, *Produtos* e *Serviços*.</span><span class="sxs-lookup"><span data-stu-id="d669b-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="d669b-255">Usando áreas, a estrutura de pastas seria semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="d669b-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="d669b-256">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="d669b-256">Project name</span></span>
  * <span data-ttu-id="d669b-257">Áreas</span><span class="sxs-lookup"><span data-stu-id="d669b-257">Areas</span></span>
    * <span data-ttu-id="d669b-258">Produtos</span><span class="sxs-lookup"><span data-stu-id="d669b-258">Products</span></span>
      * <span data-ttu-id="d669b-259">Controladores</span><span class="sxs-lookup"><span data-stu-id="d669b-259">Controllers</span></span>
        * <span data-ttu-id="d669b-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d669b-260">HomeController.cs</span></span>
        * <span data-ttu-id="d669b-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="d669b-261">ManageController.cs</span></span>
      * <span data-ttu-id="d669b-262">Exibições</span><span class="sxs-lookup"><span data-stu-id="d669b-262">Views</span></span>
        * <span data-ttu-id="d669b-263">Home</span><span class="sxs-lookup"><span data-stu-id="d669b-263">Home</span></span>
          * <span data-ttu-id="d669b-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-264">Index.cshtml</span></span>
        * <span data-ttu-id="d669b-265">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="d669b-265">Manage</span></span>
          * <span data-ttu-id="d669b-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-266">Index.cshtml</span></span>
          * <span data-ttu-id="d669b-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-267">About.cshtml</span></span>
    * <span data-ttu-id="d669b-268">Serviços</span><span class="sxs-lookup"><span data-stu-id="d669b-268">Services</span></span>
      * <span data-ttu-id="d669b-269">Controladores</span><span class="sxs-lookup"><span data-stu-id="d669b-269">Controllers</span></span>
        * <span data-ttu-id="d669b-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d669b-270">HomeController.cs</span></span>
      * <span data-ttu-id="d669b-271">Exibições</span><span class="sxs-lookup"><span data-stu-id="d669b-271">Views</span></span>
        * <span data-ttu-id="d669b-272">Home</span><span class="sxs-lookup"><span data-stu-id="d669b-272">Home</span></span>
          * <span data-ttu-id="d669b-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-273">Index.cshtml</span></span>

<span data-ttu-id="d669b-274">Enquanto o layout anterior é típico ao usar áreas, somente os arquivos de exibição são necessários para usar essa estrutura de pastas.</span><span class="sxs-lookup"><span data-stu-id="d669b-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="d669b-275">Pesquisas de descoberta de exibição para um arquivo de exibição de área correspondente, na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="d669b-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="d669b-276">Associar o controlador a uma área</span><span class="sxs-lookup"><span data-stu-id="d669b-276">Associate the controller with an Area</span></span>

<span data-ttu-id="d669b-277">Controladores de área são designados com o atributo [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute):</span><span class="sxs-lookup"><span data-stu-id="d669b-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="d669b-278">Adicionar rota de área</span><span class="sxs-lookup"><span data-stu-id="d669b-278">Add Area route</span></span>

<span data-ttu-id="d669b-279">Rotas de área normalmente usam o roteamento convencional, em vez de roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="d669b-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="d669b-280">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="d669b-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="d669b-281">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="d669b-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="d669b-282">`{area:...}` pode ser usado como um token em modelos de rota, se o espaço de URL é uniforme entre todas as áreas:</span><span class="sxs-lookup"><span data-stu-id="d669b-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="d669b-283">No código anterior, `exists` aplica uma restrição de que a rota deve corresponder a uma área.</span><span class="sxs-lookup"><span data-stu-id="d669b-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="d669b-284">Usar `{area:...}` é o mecanismo menos complicado para a adição de roteamento para áreas.</span><span class="sxs-lookup"><span data-stu-id="d669b-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="d669b-285">O código a seguir usa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> para criar duas rotas de área nomeadas:</span><span class="sxs-lookup"><span data-stu-id="d669b-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="d669b-286">Ao usar `MapAreaRoute` com o ASP.NET Core 2.2, veja [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="d669b-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="d669b-287">Para obter mais informações, veja [Roteamento de área](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="d669b-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="d669b-288">Geração de links com áreas do MVC</span><span class="sxs-lookup"><span data-stu-id="d669b-288">Link generation with MVC areas</span></span>

<span data-ttu-id="d669b-289">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) mostra geração de link com a área especificada:</span><span class="sxs-lookup"><span data-stu-id="d669b-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d669b-290">Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="d669b-291">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="d669b-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="d669b-292">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="d669b-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d669b-293">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área e no mesmo controlador.</span><span class="sxs-lookup"><span data-stu-id="d669b-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="d669b-294">Quando a área ou o controlador não for especificado, o roteamento dependerá dos valores do *ambiente*.</span><span class="sxs-lookup"><span data-stu-id="d669b-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="d669b-295">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="d669b-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d669b-296">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="d669b-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="d669b-297">Para obter mais informações, veja [Roteamento para ações do controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="d669b-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="d669b-298">Layout compartilhado para áreas usando o arquivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="d669b-299">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="d669b-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="d669b-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="d669b-301">Em seu local padrão, */Views/_ViewImports.cshtml* não se aplica às áreas.</span><span class="sxs-lookup"><span data-stu-id="d669b-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="d669b-302">Para usar [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) comuns, `@using` ou `@inject` em sua área, um arquivo *_ViewImports.cshtml* apropriado deve se [aplicar às suas exibições de área](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="d669b-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="d669b-303">Se você quiser o mesmo comportamento em todas as suas exibições, mova */Views/_ViewImports.cshtml* para a raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="d669b-304">Alterar a pasta de área padrão em que as exibições são armazenadas</span><span class="sxs-lookup"><span data-stu-id="d669b-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="d669b-305">O código a seguir altera a pasta da área padrão de `"Areas"` para `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="d669b-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="d669b-306">Áreas com Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d669b-306">Areas with Razor Pages</span></span>

<span data-ttu-id="d669b-307">As áreas com Razor Pages exigem uma pasta `Areas/<area name>/Pages` na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="d669b-308">A seguinte estrutura de pasta é usada com o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span><span class="sxs-lookup"><span data-stu-id="d669b-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="d669b-309">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="d669b-309">Project name</span></span>
  * <span data-ttu-id="d669b-310">Áreas</span><span class="sxs-lookup"><span data-stu-id="d669b-310">Areas</span></span>
    * <span data-ttu-id="d669b-311">Produtos</span><span class="sxs-lookup"><span data-stu-id="d669b-311">Products</span></span>
      * <span data-ttu-id="d669b-312">Páginas</span><span class="sxs-lookup"><span data-stu-id="d669b-312">Pages</span></span>
        * <span data-ttu-id="d669b-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="d669b-313">_ViewImports</span></span>
        * <span data-ttu-id="d669b-314">Sobre</span><span class="sxs-lookup"><span data-stu-id="d669b-314">About</span></span>
        * <span data-ttu-id="d669b-315">Index</span><span class="sxs-lookup"><span data-stu-id="d669b-315">Index</span></span>
    * <span data-ttu-id="d669b-316">Serviços</span><span class="sxs-lookup"><span data-stu-id="d669b-316">Services</span></span>
      * <span data-ttu-id="d669b-317">Páginas</span><span class="sxs-lookup"><span data-stu-id="d669b-317">Pages</span></span>
        * <span data-ttu-id="d669b-318">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="d669b-318">Manage</span></span>
          * <span data-ttu-id="d669b-319">Sobre</span><span class="sxs-lookup"><span data-stu-id="d669b-319">About</span></span>
          * <span data-ttu-id="d669b-320">Index</span><span class="sxs-lookup"><span data-stu-id="d669b-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="d669b-321">Geração de links com áreas e Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d669b-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="d669b-322">O código a seguir do [download do exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra a geração de links com a área especificada (por exemplo, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="d669b-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d669b-323">Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="d669b-324">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="d669b-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="d669b-325">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="d669b-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d669b-326">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área.</span><span class="sxs-lookup"><span data-stu-id="d669b-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="d669b-327">Quando a área não for especificada, o roteamento dependerá dos valores do *ambiente*.</span><span class="sxs-lookup"><span data-stu-id="d669b-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="d669b-328">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="d669b-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d669b-329">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="d669b-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="d669b-330">Por exemplo, considere os links gerados a partir do código a seguir:</span><span class="sxs-lookup"><span data-stu-id="d669b-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="d669b-331">Para o código anterior:</span><span class="sxs-lookup"><span data-stu-id="d669b-331">For the preceding code:</span></span>

* <span data-ttu-id="d669b-332">O link gerado de `<a asp-page="/Manage/About">` só estará correto quando a última solicitação tiver sido para uma página na área `Services`.</span><span class="sxs-lookup"><span data-stu-id="d669b-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="d669b-333">Por exemplo, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="d669b-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="d669b-334">O link gerado a partir `<a asp-page="/About">` só estará correto quando a última solicitação tiver sido para uma página na área `/Home`.</span><span class="sxs-lookup"><span data-stu-id="d669b-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="d669b-335">O código vem do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="d669b-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="d669b-336">Importar o namespace e os Auxiliares de marca com o arquivo _ViewImports</span><span class="sxs-lookup"><span data-stu-id="d669b-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="d669b-337">Um arquivo *_ViewImports.cshtml* pode ser adicionado a cada pasta *Páginas* da área para importar o namespace e os Auxiliares de Marcação para cada Razor Page na pasta.</span><span class="sxs-lookup"><span data-stu-id="d669b-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="d669b-338">Considere a área *Serviços* do código de exemplo, que não contém um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d669b-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="d669b-339">A marcação a seguir mostra a Página do Razor */Services/Manage/About*:</span><span class="sxs-lookup"><span data-stu-id="d669b-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="d669b-340">Na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="d669b-340">In the preceding markup:</span></span>

* <span data-ttu-id="d669b-341">O nome de domínio totalmente qualificado deve ser usado para especificar o modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="d669b-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="d669b-342">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="d669b-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="d669b-343">No download de exemplo, a área Produtos contém o seguinte arquivo *_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d669b-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="d669b-344">A marcação a seguir mostra a Página do Razor */Products/About*:</span><span class="sxs-lookup"><span data-stu-id="d669b-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="d669b-345">No arquivo anterior, o namespace e a diretiva `@addTagHelper` são importados para o arquivo por meio do arquivo *Areas/Products/Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d669b-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="d669b-346">Para saber mais, confira [Gerenciar o escopo do Auxiliar de Marcação](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importar diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="d669b-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="d669b-347">Layout compartilhado para Áreas do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d669b-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="d669b-348">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d669b-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="d669b-349">Publicando áreas</span><span class="sxs-lookup"><span data-stu-id="d669b-349">Publishing Areas</span></span>

<span data-ttu-id="d669b-350">Todos os arquivos \*.cshtml e os arquivos do diretório *wwwroot* são publicados em uma saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` são incluídos no arquivo \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="d669b-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
