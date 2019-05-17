---
title: Áreas no ASP.NET Core
author: rick-anderson
description: Saiba por que as áreas são um recurso do ASP.NET MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace (para roteamento) e uma estrutura de pasta (para exibições) separados.
ms.author: riande
ms.date: 05/06/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 35c7682861f7392b0bcda7326e4d7f5ccc356bda
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212591"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="97746-103">Áreas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="97746-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="97746-104">Por [Dhananjay Kumar](https://twitter.com/debug_mode) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="97746-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="97746-105">Áreas são um recurso do ASP.NET usado para organizar funcionalidades relacionadas em um grupo como um namespace separado (para roteamento) e uma estrutura de pastas (para exibições).</span><span class="sxs-lookup"><span data-stu-id="97746-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="97746-106">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area`, a `controller` e `action` ou a uma Razor Page, `page`.</span><span class="sxs-lookup"><span data-stu-id="97746-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="97746-107">As áreas proporcionam uma maneira de particionar um aplicativo Web do ASP.NET Core em grupos funcionais menores, cada um com seu próprio conjunto de Razor Pages, controladores, exibições e modelos.</span><span class="sxs-lookup"><span data-stu-id="97746-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="97746-108">Uma área é efetivamente uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97746-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="97746-109">Em um projeto Web do ASP.NET Core, os componentes lógicos como páginas, modelo, controlador e modo de exibição são mantidos em pastas diferentes.</span><span class="sxs-lookup"><span data-stu-id="97746-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="97746-110">O tempo de execução do ASP.NET Core usa as convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="97746-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="97746-111">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="97746-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="97746-112">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa.</span><span class="sxs-lookup"><span data-stu-id="97746-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="97746-113">Cada uma dessas unidades tem sua própria área para conter exibições, controladores, Razor Pages e modelos.</span><span class="sxs-lookup"><span data-stu-id="97746-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="97746-114">Considere o uso de Áreas em um projeto quando:</span><span class="sxs-lookup"><span data-stu-id="97746-114">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="97746-115">O aplicativo é composto por vários componentes funcionais de alto nível que podem ser separados logicamente.</span><span class="sxs-lookup"><span data-stu-id="97746-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="97746-116">Você deseja particionar o aplicativo para que cada área funcional possa ser trabalhada de forma independente.</span><span class="sxs-lookup"><span data-stu-id="97746-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="97746-117">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="97746-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="97746-118">O exemplo de download fornece um aplicativo básico para áreas de teste.</span><span class="sxs-lookup"><span data-stu-id="97746-118">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="97746-119">Se você estiver usando o Razor Pages, confira [Áreas com Razor Pages](#areas-with-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="97746-119">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="97746-120">Áreas para os controladores com modos de exibição</span><span class="sxs-lookup"><span data-stu-id="97746-120">Areas for controllers with views</span></span>

<span data-ttu-id="97746-121">Um aplicativo Web do ASP.NET Core típico usando áreas, controladores e exibições contém o seguinte:</span><span class="sxs-lookup"><span data-stu-id="97746-121">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="97746-122">Uma [estrutura de pastas da área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="97746-122">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="97746-123">Controladores decorados com o atributo [&lbrack;Area&rbrack;](#attribute) para associar o controlador à área:</span><span class="sxs-lookup"><span data-stu-id="97746-123">Controllers decorated with the [&lbrack;Area&rbrack;](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="97746-124">A [rota de área adicionada à inicialização](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="97746-124">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="97746-125">Estrutura de pastas da área</span><span class="sxs-lookup"><span data-stu-id="97746-125">Area folder structure</span></span>

<span data-ttu-id="97746-126">Considere um aplicativo que tem dois grupos lógicos, *Produtos* e *Serviços*.</span><span class="sxs-lookup"><span data-stu-id="97746-126">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="97746-127">Usando áreas, a estrutura de pastas seria semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="97746-127">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="97746-128">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="97746-128">Project name</span></span>
  * <span data-ttu-id="97746-129">Áreas</span><span class="sxs-lookup"><span data-stu-id="97746-129">Areas</span></span>
    * <span data-ttu-id="97746-130">Produtos</span><span class="sxs-lookup"><span data-stu-id="97746-130">Products</span></span>
      * <span data-ttu-id="97746-131">Controladores</span><span class="sxs-lookup"><span data-stu-id="97746-131">Controllers</span></span>
        * <span data-ttu-id="97746-132">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="97746-132">HomeController.cs</span></span>
        * <span data-ttu-id="97746-133">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="97746-133">ManageController.cs</span></span>
      * <span data-ttu-id="97746-134">Exibições</span><span class="sxs-lookup"><span data-stu-id="97746-134">Views</span></span>
        * <span data-ttu-id="97746-135">Home</span><span class="sxs-lookup"><span data-stu-id="97746-135">Home</span></span>
          * <span data-ttu-id="97746-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="97746-136">Index.cshtml</span></span>
        * <span data-ttu-id="97746-137">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="97746-137">Manage</span></span>
          * <span data-ttu-id="97746-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="97746-138">Index.cshtml</span></span>
          * <span data-ttu-id="97746-139">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="97746-139">About.cshtml</span></span>
    * <span data-ttu-id="97746-140">Serviços</span><span class="sxs-lookup"><span data-stu-id="97746-140">Services</span></span>
      * <span data-ttu-id="97746-141">Controladores</span><span class="sxs-lookup"><span data-stu-id="97746-141">Controllers</span></span>
        * <span data-ttu-id="97746-142">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="97746-142">HomeController.cs</span></span>
      * <span data-ttu-id="97746-143">Exibições</span><span class="sxs-lookup"><span data-stu-id="97746-143">Views</span></span>
        * <span data-ttu-id="97746-144">Home</span><span class="sxs-lookup"><span data-stu-id="97746-144">Home</span></span>
          * <span data-ttu-id="97746-145">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="97746-145">Index.cshtml</span></span>

<span data-ttu-id="97746-146">Enquanto o layout anterior é típico ao usar áreas, somente os arquivos de exibição são necessários para usar essa estrutura de pastas.</span><span class="sxs-lookup"><span data-stu-id="97746-146">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="97746-147">Pesquisas de descoberta de exibição para um arquivo de exibição de área correspondente, na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="97746-147">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="97746-148">A localização de pastas de não exibição, assim como *Controladores* e *Modelos*, **não** importa.</span><span class="sxs-lookup"><span data-stu-id="97746-148">The location of non-view folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="97746-149">Por exemplo, as pastas *Controladores* e *Modelos* não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="97746-149">For example, the *Controllers* and *Models* folder are not required.</span></span> <span data-ttu-id="97746-150">O conteúdo de *Controladores* e *Modelos* é o código que é compilado em um arquivo .dll.</span><span class="sxs-lookup"><span data-stu-id="97746-150">The content of *Controllers* and *Models* is code which gets compiled into a .dll.</span></span> <span data-ttu-id="97746-151">O conteúdo das *Exibições* não é compilado até que seja feita uma solicitação para essa exibição.</span><span class="sxs-lookup"><span data-stu-id="97746-151">The content of the *Views* isn't compiled until a request to that view has been made.</span></span>

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="97746-152">Associar o controlador a uma área</span><span class="sxs-lookup"><span data-stu-id="97746-152">Associate the controller with an Area</span></span>

<span data-ttu-id="97746-153">Controladores de área são designados com o atributo [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute):</span><span class="sxs-lookup"><span data-stu-id="97746-153">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="97746-154">Adicionar rota de área</span><span class="sxs-lookup"><span data-stu-id="97746-154">Add Area route</span></span>

<span data-ttu-id="97746-155">Rotas de área normalmente usam o roteamento convencional, em vez de roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="97746-155">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="97746-156">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="97746-156">Conventional routing is order-dependent.</span></span> <span data-ttu-id="97746-157">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="97746-157">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="97746-158">`{area:...}` pode ser usado como um token em modelos de rota, se o espaço de URL é uniforme entre todas as áreas:</span><span class="sxs-lookup"><span data-stu-id="97746-158">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="97746-159">No código anterior, `exists` aplica uma restrição de que a rota deve corresponder a uma área.</span><span class="sxs-lookup"><span data-stu-id="97746-159">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="97746-160">Usar `{area:...}` é o mecanismo menos complicado para a adição de roteamento para áreas.</span><span class="sxs-lookup"><span data-stu-id="97746-160">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="97746-161">O código a seguir usa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> para criar duas rotas de área nomeadas:</span><span class="sxs-lookup"><span data-stu-id="97746-161">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="97746-162">Ao usar `MapAreaRoute` com o ASP.NET Core 2.2, veja [este problema do GitHub](https://github.com/aspnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="97746-162">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="97746-163">Para obter mais informações, veja [Roteamento de área](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="97746-163">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="97746-164">Geração de links com áreas do MVC</span><span class="sxs-lookup"><span data-stu-id="97746-164">Link generation with MVC areas</span></span>

<span data-ttu-id="97746-165">O código a seguir do [download do exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) mostra geração de link com a área especificada:</span><span class="sxs-lookup"><span data-stu-id="97746-165">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="97746-166">Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97746-166">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="97746-167">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="97746-167">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="97746-168">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="97746-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="97746-169">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área e no mesmo controlador.</span><span class="sxs-lookup"><span data-stu-id="97746-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="97746-170">Quando a área ou o controlador não for especificado, o roteamento dependerá dos valores do *ambiente*.</span><span class="sxs-lookup"><span data-stu-id="97746-170">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="97746-171">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="97746-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="97746-172">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="97746-172">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="97746-173">Para obter mais informações, veja [Roteamento para ações do controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="97746-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a><span data-ttu-id="97746-174">Layout compartilhado para áreas usando o arquivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="97746-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="97746-175">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97746-175">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="97746-176">Alterar a pasta de área padrão em que as exibições são armazenadas</span><span class="sxs-lookup"><span data-stu-id="97746-176">Change default area folder where views are stored</span></span>

<span data-ttu-id="97746-177">O código a seguir altera a pasta da área padrão de `"Areas"` para `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="97746-177">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="97746-178">Áreas com Razor Pages</span><span class="sxs-lookup"><span data-stu-id="97746-178">Areas with Razor Pages</span></span>

<span data-ttu-id="97746-179">As Áreas com Razor Pages exigem e a pasta *Areas/&lt;nome da área&gt;/Pages* na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97746-179">Areas with Razor Pages require and *Areas/&lt;area name&gt;/Pages* folder in the root of the app.</span></span> <span data-ttu-id="97746-180">A seguinte estrutura de pasta é usada com o [download de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)</span><span class="sxs-lookup"><span data-stu-id="97746-180">The following folder structure is used with the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)</span></span>

* <span data-ttu-id="97746-181">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="97746-181">Project name</span></span>
  * <span data-ttu-id="97746-182">Áreas</span><span class="sxs-lookup"><span data-stu-id="97746-182">Areas</span></span>
    * <span data-ttu-id="97746-183">Produtos</span><span class="sxs-lookup"><span data-stu-id="97746-183">Products</span></span>
      * <span data-ttu-id="97746-184">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="97746-184">Pages</span></span>
        * <span data-ttu-id="97746-185">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="97746-185">_ViewImports</span></span>
        * <span data-ttu-id="97746-186">Sobre o</span><span class="sxs-lookup"><span data-stu-id="97746-186">About</span></span>
        * <span data-ttu-id="97746-187">Índice</span><span class="sxs-lookup"><span data-stu-id="97746-187">Index</span></span>
    * <span data-ttu-id="97746-188">Serviços</span><span class="sxs-lookup"><span data-stu-id="97746-188">Services</span></span>
      * <span data-ttu-id="97746-189">Pages (Páginas)</span><span class="sxs-lookup"><span data-stu-id="97746-189">Pages</span></span>
        * <span data-ttu-id="97746-190">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="97746-190">Manage</span></span>
          * <span data-ttu-id="97746-191">Sobre o</span><span class="sxs-lookup"><span data-stu-id="97746-191">About</span></span>
          * <span data-ttu-id="97746-192">Índice</span><span class="sxs-lookup"><span data-stu-id="97746-192">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="97746-193">Geração de links com áreas e Razor Pages</span><span class="sxs-lookup"><span data-stu-id="97746-193">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="97746-194">O código a seguir do [download do exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra a geração de links com a área especificada (por exemplo, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="97746-194">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="97746-195">Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97746-195">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="97746-196">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="97746-196">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="97746-197">A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="97746-197">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="97746-198">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área.</span><span class="sxs-lookup"><span data-stu-id="97746-198">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="97746-199">Quando a área não for especificada, o roteamento dependerá dos valores do *ambiente*.</span><span class="sxs-lookup"><span data-stu-id="97746-199">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="97746-200">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="97746-200">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="97746-201">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="97746-201">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="97746-202">Por exemplo, considere os links gerados a partir do código a seguir:</span><span class="sxs-lookup"><span data-stu-id="97746-202">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="97746-203">Para o código anterior:</span><span class="sxs-lookup"><span data-stu-id="97746-203">For the preceding code:</span></span>

* <span data-ttu-id="97746-204">O link gerado de `<a asp-page="/Manage/About">` só estará correto quando a última solicitação tiver sido para uma página na área `Services`.</span><span class="sxs-lookup"><span data-stu-id="97746-204">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="97746-205">Por exemplo, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="97746-205">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="97746-206">O link gerado a partir `<a asp-page="/About">` só estará correto quando a última solicitação tiver sido para uma página na área `/Home`.</span><span class="sxs-lookup"><span data-stu-id="97746-206">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="97746-207">O código vem do [download de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="97746-207">The code is from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-viewimports-file"></a><span data-ttu-id="97746-208">Importar o namespace e os Auxiliares de marca com o arquivo _ViewImports</span><span class="sxs-lookup"><span data-stu-id="97746-208">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="97746-209">Um arquivo *_ViewImports* pode ser adicionado a cada pasta *Páginas* da área para importar o namespace e os Auxiliares de marcação para cada Página do Razor na pasta.</span><span class="sxs-lookup"><span data-stu-id="97746-209">A *_ViewImports* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="97746-210">Considere a área *Serviços* do código de exemplo, que não contém um arquivo *_ViewImports*.</span><span class="sxs-lookup"><span data-stu-id="97746-210">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports* file.</span></span> <span data-ttu-id="97746-211">A marcação a seguir mostra a Página do Razor */Services/Manage/About*:</span><span class="sxs-lookup"><span data-stu-id="97746-211">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="97746-212">Na marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="97746-212">In the preceding markup:</span></span>

* <span data-ttu-id="97746-213">O nome de domínio totalmente qualificado deve ser usado para especificar o modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="97746-213">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="97746-214">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="97746-214">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="97746-215">No download de exemplo, a área Produtos contém o seguinte arquivo *_ViewImports*:</span><span class="sxs-lookup"><span data-stu-id="97746-215">In the sample download, the Products area contains the following *_ViewImports* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="97746-216">A marcação a seguir mostra a Página do Razor */Products/About*:</span><span class="sxs-lookup"><span data-stu-id="97746-216">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="97746-217">No arquivo anterior, o namespace e a diretiva `@addTagHelper` são importados para o arquivo por meio do arquivo *Areas/Products/Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="97746-217">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="97746-218">Para saber mais, confira [Gerenciar o escopo do Auxiliar de Marcação](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importar diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="97746-218">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="97746-219">Layout compartilhado para Áreas do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="97746-219">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="97746-220">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97746-220">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="97746-221">Publicando áreas</span><span class="sxs-lookup"><span data-stu-id="97746-221">Publishing Areas</span></span>

<span data-ttu-id="97746-222">Todos os arquivos `*.cshtml` e `wwwroot/**` são publicados na saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` é incluído no arquivo .csproj\*.</span><span class="sxs-lookup"><span data-stu-id="97746-222">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the.csproj\* file.</span></span>
