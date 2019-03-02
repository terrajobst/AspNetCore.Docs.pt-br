---
title: Áreas no ASP.NET Core
author: rick-anderson
description: Saiba por que as áreas são um recurso do ASP.NET MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace (para roteamento) e uma estrutura de pasta (para exibições) separados.
ms.author: riande
ms.date: 02/14/2019
uid: mvc/controllers/areas
ms.openlocfilehash: c21eed04ea68512515da262b6b6895dc1a821039
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833521"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="b96c6-103">Áreas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b96c6-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="b96c6-104">Por [Dhananjay Kumar](https://twitter.com/debug_mode) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b96c6-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b96c6-105">Áreas são um recurso do ASP.NET usado para organizar funcionalidades relacionadas em um grupo como um namespace separado (para roteamento) e uma estrutura de pastas (para exibições).</span><span class="sxs-lookup"><span data-stu-id="b96c6-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="b96c6-106">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area`, a `controller` e `action` ou a uma Razor Page, `page`.</span><span class="sxs-lookup"><span data-stu-id="b96c6-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="b96c6-107">As áreas proporcionam uma maneira de particionar um aplicativo Web do ASP.NET Core em grupos funcionais menores, cada um com seu próprio conjunto de Razor Pages, controladores, exibições e modelos.</span><span class="sxs-lookup"><span data-stu-id="b96c6-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="b96c6-108">Uma área é efetivamente uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b96c6-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="b96c6-109">Em um projeto Web do ASP.NET Core, os componentes lógicos como páginas, modelo, controlador e modo de exibição são mantidos em pastas diferentes.</span><span class="sxs-lookup"><span data-stu-id="b96c6-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="b96c6-110">O tempo de execução do ASP.NET Core usa as convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="b96c6-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="b96c6-111">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="b96c6-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="b96c6-112">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa.</span><span class="sxs-lookup"><span data-stu-id="b96c6-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="b96c6-113">Cada uma dessas unidades tem sua própria área para conter exibições, controladores, Razor Pages e modelos.</span><span class="sxs-lookup"><span data-stu-id="b96c6-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="b96c6-114">Considere o uso de Áreas em um projeto quando:</span><span class="sxs-lookup"><span data-stu-id="b96c6-114">Consider using Areas in an project when:</span></span>

* <span data-ttu-id="b96c6-115">O aplicativo é composto por vários componentes funcionais de alto nível que podem ser separados logicamente.</span><span class="sxs-lookup"><span data-stu-id="b96c6-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="b96c6-116">Você deseja particionar o aplicativo para que cada área funcional possa ser trabalhada de forma independente.</span><span class="sxs-lookup"><span data-stu-id="b96c6-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="b96c6-117">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b96c6-117">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="b96c6-118">O exemplo de download fornece um aplicativo básico para áreas de teste.</span><span class="sxs-lookup"><span data-stu-id="b96c6-118">The download sample provides a basic app for testing areas.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="b96c6-119">Áreas para os controladores com modos de exibição</span><span class="sxs-lookup"><span data-stu-id="b96c6-119">Areas for controllers with views</span></span>

<span data-ttu-id="b96c6-120">Um aplicativo Web do ASP.NET Core típico usando áreas, controladores e exibições contém o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b96c6-120">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="b96c6-121">Uma [estrutura de pastas da área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="b96c6-121">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="b96c6-122">Controladores decorados com o atributo [&lbrack;Area&rbrack;](#attribute) para associar o controlador com a área: [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="b96c6-122">Controllers decorated with the [&lbrack;Area&rbrack;](#attribute) attribute to associate the controller with the area: [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]</span></span>
* <span data-ttu-id="b96c6-123">A [rota de área adicionada à inicialização](#add-area-route): [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]</span><span class="sxs-lookup"><span data-stu-id="b96c6-123">The [area route added to startup](#add-area-route): [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]</span></span>

## <a name="area-folder-structure"></a><span data-ttu-id="b96c6-124">Estrutura de pastas da área</span><span class="sxs-lookup"><span data-stu-id="b96c6-124">Area folder structure</span></span>
<span data-ttu-id="b96c6-125">Considere um aplicativo que tem dois grupos lógicos, *Produtos* e *Serviços*.</span><span class="sxs-lookup"><span data-stu-id="b96c6-125">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="b96c6-126">Usando áreas, a estrutura de pastas seria semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="b96c6-126">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="b96c6-127">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="b96c6-127">Project name</span></span>
  * <span data-ttu-id="b96c6-128">Áreas</span><span class="sxs-lookup"><span data-stu-id="b96c6-128">Areas</span></span>
    * <span data-ttu-id="b96c6-129">Produtos</span><span class="sxs-lookup"><span data-stu-id="b96c6-129">Products</span></span>
      * <span data-ttu-id="b96c6-130">Controladores</span><span class="sxs-lookup"><span data-stu-id="b96c6-130">Controllers</span></span>
        * <span data-ttu-id="b96c6-131">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="b96c6-131">HomeController.cs</span></span>
        * <span data-ttu-id="b96c6-132">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="b96c6-132">ManageController.cs</span></span>
      * <span data-ttu-id="b96c6-133">Exibições</span><span class="sxs-lookup"><span data-stu-id="b96c6-133">Views</span></span>
        * <span data-ttu-id="b96c6-134">Home</span><span class="sxs-lookup"><span data-stu-id="b96c6-134">Home</span></span>
          * <span data-ttu-id="b96c6-135">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b96c6-135">Index.cshtml</span></span>
        * <span data-ttu-id="b96c6-136">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="b96c6-136">Manage</span></span>
          * <span data-ttu-id="b96c6-137">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b96c6-137">Index.cshtml</span></span>
          * <span data-ttu-id="b96c6-138">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="b96c6-138">About.cshtml</span></span>
    * <span data-ttu-id="b96c6-139">Serviços</span><span class="sxs-lookup"><span data-stu-id="b96c6-139">Services</span></span>
      * <span data-ttu-id="b96c6-140">Controladores</span><span class="sxs-lookup"><span data-stu-id="b96c6-140">Controllers</span></span>
        * <span data-ttu-id="b96c6-141">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="b96c6-141">HomeController.cs</span></span>
      * <span data-ttu-id="b96c6-142">Exibições</span><span class="sxs-lookup"><span data-stu-id="b96c6-142">Views</span></span>
        * <span data-ttu-id="b96c6-143">Home</span><span class="sxs-lookup"><span data-stu-id="b96c6-143">Home</span></span>
          * <span data-ttu-id="b96c6-144">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b96c6-144">Index.cshtml</span></span>

<span data-ttu-id="b96c6-145">Enquanto o layout anterior é típico ao usar áreas, somente os arquivos de exibição são necessários para usar essa estrutura de pastas.</span><span class="sxs-lookup"><span data-stu-id="b96c6-145">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="b96c6-146">Pesquisas de descoberta de exibição para um arquivo de exibição de área correspondente, na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="b96c6-146">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="b96c6-147">A localização de pastas de não exibição, assim como *Controladores* e *Modelos*, **não** importa.</span><span class="sxs-lookup"><span data-stu-id="b96c6-147">The location of non-view folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="b96c6-148">Por exemplo, as pastas *Controladores* e *Modelos* não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="b96c6-148">For example, the *Controllers* and *Models* folder are not required.</span></span> <span data-ttu-id="b96c6-149">O conteúdo de *Controladores* e *Modelos* é o código que é compilado em um arquivo .dll.</span><span class="sxs-lookup"><span data-stu-id="b96c6-149">The content of *Controllers* and *Models* is code which gets compiled into a .dll.</span></span> <span data-ttu-id="b96c6-150">O conteúdo das *Exibições* não é compilado até que seja feita uma solicitação para essa exibição.</span><span class="sxs-lookup"><span data-stu-id="b96c6-150">The content of the *Views* isn't compiled until a request to that view has been made.</span></span>

<!-- TODO review:
The content of the *Views* isn't compiled until a request to that view has been made.

What about precompiled views? 
 -->
<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="b96c6-151">Associar o controlador a uma área</span><span class="sxs-lookup"><span data-stu-id="b96c6-151">Associate the controller with an Area</span></span>

<span data-ttu-id="b96c6-152">Controladores de área são designados com o atributo [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute):</span><span class="sxs-lookup"><span data-stu-id="b96c6-152">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="b96c6-153">Adicionar rota de área</span><span class="sxs-lookup"><span data-stu-id="b96c6-153">Add Area route</span></span>

<span data-ttu-id="b96c6-154">Rotas de área normalmente usam o roteamento convencional, em vez de roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="b96c6-154">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="b96c6-155">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="b96c6-155">Conventional routing is order-dependent.</span></span> <span data-ttu-id="b96c6-156">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="b96c6-156">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="b96c6-157">`{area:...}` pode ser usado como um token em modelos de rota, se o espaço de URL é uniforme entre todas as áreas:</span><span class="sxs-lookup"><span data-stu-id="b96c6-157">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="b96c6-158">No código anterior, `exists` aplica uma restrição de que a rota deve corresponder a uma área.</span><span class="sxs-lookup"><span data-stu-id="b96c6-158">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="b96c6-159">Usar `{area:...}` é o mecanismo menos complicado para a adição de roteamento para áreas.</span><span class="sxs-lookup"><span data-stu-id="b96c6-159">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="b96c6-160">O código a seguir usa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> para criar duas rotas de área nomeadas:</span><span class="sxs-lookup"><span data-stu-id="b96c6-160">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="b96c6-161">Ao usar `MapAreaRoute` com o ASP.NET Core 2.2, veja [este problema do GitHub](https://github.com/aspnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="b96c6-161">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="b96c6-162">Para obter mais informações, veja [Roteamento de área](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="b96c6-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-areas"></a><span data-ttu-id="b96c6-163">Geração de links com áreas</span><span class="sxs-lookup"><span data-stu-id="b96c6-163">Link Generation with Areas</span></span>

<span data-ttu-id="b96c6-164">O código a seguir do [download do exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) mostra geração de link com a área especificada:</span><span class="sxs-lookup"><span data-stu-id="b96c6-164">The following code from the [sample download](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="b96c6-165">Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b96c6-165">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="b96c6-166">O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área.</span><span class="sxs-lookup"><span data-stu-id="b96c6-166">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="b96c6-167">A exibição parcial é referenciada no [arquivo de layout](), portanto, todas as páginas no aplicativo exibem os links gerados.</span><span class="sxs-lookup"><span data-stu-id="b96c6-167">The partial view is referenced in the [layout file](), so every page in the app displays the generated links.</span></span> <span data-ttu-id="b96c6-168">Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área e no mesmo controlador.</span><span class="sxs-lookup"><span data-stu-id="b96c6-168">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="b96c6-169">Quando a área ou o controlador não for especificado, o roteamento dependerá dos valores do *ambiente*.</span><span class="sxs-lookup"><span data-stu-id="b96c6-169">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="b96c6-170">Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link.</span><span class="sxs-lookup"><span data-stu-id="b96c6-170">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="b96c6-171">Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.</span><span class="sxs-lookup"><span data-stu-id="b96c6-171">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="b96c6-172">Para obter mais informações, veja [Roteamento para ações do controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="b96c6-172">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a><span data-ttu-id="b96c6-173">Layout compartilhado para áreas usando o arquivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="b96c6-173">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="b96c6-174">Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b96c6-174">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

<!-- This section will be completed after https://github.com/aspnet/Docs/pull/10978 is merged.
<a name="arp"></a>

## Areas for Razor Pages
-->
<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="b96c6-175">Alterar a pasta de área padrão em que as exibições são armazenadas</span><span class="sxs-lookup"><span data-stu-id="b96c6-175">Change default area folder where views are stored</span></span>

<span data-ttu-id="b96c6-176">O código a seguir altera a pasta da área padrão de `"Areas"` para `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="b96c6-176">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<!-- TODO review - can we delete this. Areas doesn't change publishing - right? -->
### <a name="publishing-areas"></a><span data-ttu-id="b96c6-177">Publicando áreas</span><span class="sxs-lookup"><span data-stu-id="b96c6-177">Publishing Areas</span></span>

<span data-ttu-id="b96c6-178">Todos os arquivos `*.cshtml` e `wwwroot/**` são publicados na saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` é incluído no arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="b96c6-178">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the *.csproj* file.</span></span>
