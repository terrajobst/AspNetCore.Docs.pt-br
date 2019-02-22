---
title: Áreas no ASP.NET Core
author: rick-anderson
description: Saiba por que as áreas são um recurso do ASP.NET MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace (para roteamento) e uma estrutura de pasta (para exibições) separados.
ms.author: riande
ms.date: 02/14/2017
uid: mvc/controllers/areas
ms.openlocfilehash: 19e818fa198936ea1bee0da8039e88a3c0abbf6b
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410606"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="3ea4c-103">Áreas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ea4c-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="3ea4c-104">Por [Dhananjay Kumar](https://twitter.com/debug_mode) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3ea4c-104">By [Dhananjay Kumar](https://twitter.com/debug_mode)  and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3ea4c-105">Áreas são um recurso do ASP.NET MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace separado (para roteamento) e uma estrutura de pastas (para exibições).</span><span class="sxs-lookup"><span data-stu-id="3ea4c-105">Areas are an ASP.NET MVC feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="3ea4c-106">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area`, a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action`.</span></span>

<span data-ttu-id="3ea4c-107">As áreas fornecem uma maneira de particionar um aplicativo Web ASP.NET Core MVC grande em agrupamentos funcionais menores.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-107">Areas provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="3ea4c-108">Uma área é efetivamente uma estrutura MVC dentro de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-108">An area is effectively an MVC structure inside an application.</span></span> <span data-ttu-id="3ea4c-109">Em um projeto MVC, componentes lógicos como Modelo, Controlador e Exibição são mantidos em pastas diferentes e o MVC usa convenções de nomenclatura para criar a relação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-109">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="3ea4c-110">Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-110">For a large app, it may be advantageous to partition the  app into separate high level areas of functionality.</span></span> <span data-ttu-id="3ea4c-111">Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa, etc. Cada uma dessas unidades têm suas próprias exibições de componente lógico, controladores e modelos.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-111">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span> <span data-ttu-id="3ea4c-112">Nesse cenário, você pode usar Áreas para particionar fisicamente os componentes de negócios no mesmo projeto.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-112">In this scenario, you can use Areas to physically partition the business components in the same project.</span></span>

<span data-ttu-id="3ea4c-113">Uma área pode ser definida como as menores unidades funcionais em um projeto do ASP.NET Core MVC com seu próprio conjunto de controladores, exibições e modelos.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-113">An area can be defined as smaller functional units in an ASP.NET Core MVC project with its own set of controllers, views, and models.</span></span>

<span data-ttu-id="3ea4c-114">Considere o uso de Áreas em um projeto MVC quando:</span><span class="sxs-lookup"><span data-stu-id="3ea4c-114">Consider using Areas in an MVC project when:</span></span>

* <span data-ttu-id="3ea4c-115">O aplicativo é composto por vários componentes funcionais de alto nível que devem ser separados logicamente</span><span class="sxs-lookup"><span data-stu-id="3ea4c-115">Your application is made of multiple high-level functional components that should be logically separated</span></span>

* <span data-ttu-id="3ea4c-116">Você deseja particionar o projeto MVC para que cada área funcional possa ser trabalhada de forma independente</span><span class="sxs-lookup"><span data-stu-id="3ea4c-116">You want to partition your MVC project so that each functional area can be worked on independently</span></span>

<span data-ttu-id="3ea4c-117">Recursos de área:</span><span class="sxs-lookup"><span data-stu-id="3ea4c-117">Area features:</span></span>

* <span data-ttu-id="3ea4c-118">Um aplicativo ASP.NET Core MVC pode ter qualquer quantidade de áreas.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-118">An ASP.NET Core MVC app can have any number of areas.</span></span>

* <span data-ttu-id="3ea4c-119">Cada área tem seus próprios controladores, modelos e exibições.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-119">Each area has its own controllers, models, and views.</span></span>

* <span data-ttu-id="3ea4c-120">As áreas permitem organizar projetos MVC grandes em vários componentes de alto nível que podem ser trabalhados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-120">Areas allow you to organize large MVC projects into multiple high-level components that can be worked on independently.</span></span>

* <span data-ttu-id="3ea4c-121">As áreas dão suporte a vários controladores com o mesmo nome, desde que eles tenham *áreas* diferentes.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-121">Areas support multiple controllers with the same name, as long as they have different *areas*.</span></span>

<span data-ttu-id="3ea4c-122">Vamos dar uma olhada em um exemplo para ilustrar como as Áreas são criadas e usadas.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-122">Let's take a look at an example to illustrate how Areas are created and used.</span></span> <span data-ttu-id="3ea4c-123">Digamos que você tenha um aplicativo de loja que tenha dois agrupamentos distintos de controladores e exibições: Produtos e Serviços.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-123">Let's say you have a store app that has two distinct groupings of controllers and views: Products and Services.</span></span> <span data-ttu-id="3ea4c-124">Uma estrutura de pastas comum para isso usando áreas do MVC tem a aparência mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="3ea4c-124">A typical folder structure for that using MVC areas looks like below:</span></span>

* <span data-ttu-id="3ea4c-125">Nome do projeto</span><span class="sxs-lookup"><span data-stu-id="3ea4c-125">Project name</span></span>
  * <span data-ttu-id="3ea4c-126">Áreas</span><span class="sxs-lookup"><span data-stu-id="3ea4c-126">Areas</span></span>
    * <span data-ttu-id="3ea4c-127">Produtos</span><span class="sxs-lookup"><span data-stu-id="3ea4c-127">Products</span></span>
      * <span data-ttu-id="3ea4c-128">Controladores</span><span class="sxs-lookup"><span data-stu-id="3ea4c-128">Controllers</span></span>
        * <span data-ttu-id="3ea4c-129">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="3ea4c-129">HomeController.cs</span></span>
        * <span data-ttu-id="3ea4c-130">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="3ea4c-130">ManageController.cs</span></span>
      * <span data-ttu-id="3ea4c-131">Exibições</span><span class="sxs-lookup"><span data-stu-id="3ea4c-131">Views</span></span>
        * <span data-ttu-id="3ea4c-132">Home</span><span class="sxs-lookup"><span data-stu-id="3ea4c-132">Home</span></span>
          * <span data-ttu-id="3ea4c-133">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="3ea4c-133">Index.cshtml</span></span>
        * <span data-ttu-id="3ea4c-134">Gerenciar</span><span class="sxs-lookup"><span data-stu-id="3ea4c-134">Manage</span></span>
          * <span data-ttu-id="3ea4c-135">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="3ea4c-135">Index.cshtml</span></span>
    * <span data-ttu-id="3ea4c-136">Serviços</span><span class="sxs-lookup"><span data-stu-id="3ea4c-136">Services</span></span>
      * <span data-ttu-id="3ea4c-137">Controladores</span><span class="sxs-lookup"><span data-stu-id="3ea4c-137">Controllers</span></span>
        * <span data-ttu-id="3ea4c-138">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="3ea4c-138">HomeController.cs</span></span>
      * <span data-ttu-id="3ea4c-139">Exibições</span><span class="sxs-lookup"><span data-stu-id="3ea4c-139">Views</span></span>
        * <span data-ttu-id="3ea4c-140">Home</span><span class="sxs-lookup"><span data-stu-id="3ea4c-140">Home</span></span>
          * <span data-ttu-id="3ea4c-141">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="3ea4c-141">Index.cshtml</span></span>

<span data-ttu-id="3ea4c-142">Quando o MVC tenta renderizar uma exibição em uma Área, por padrão, ele tenta procurar nos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="3ea4c-142">When MVC tries to render a view in an Area, by default, it tries to look in the following locations:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="3ea4c-143">Esses são os locais padrão que podem ser alterados por meio do `AreaViewLocationFormats` no `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-143">These are the default locations which can be changed via the `AreaViewLocationFormats` on the `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.</span></span>

<span data-ttu-id="3ea4c-144">Por exemplo, no código abaixo, em vez de ter o nome da pasta como 'Areas', ele foi alterado para 'Categories'.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-144">For example, in the below code instead of having the folder name as 'Areas', it has been changed to 'Categories'.</span></span>

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

<span data-ttu-id="3ea4c-145">Uma coisa importante a ser observada é que a estrutura da pasta *Views* é a única que é considerada importante aqui e, o conteúdo do restante das pastas como *Controllers* e *Models* **não** importa.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-145">One thing to note is that the structure of the *Views* folder is the only one which is considered important here and the content of the rest of the folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="3ea4c-146">Por exemplo, você não precisa ter uma pasta *Controllers* e *Models*.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-146">For example, you need not have a *Controllers* and *Models* folder at all.</span></span> <span data-ttu-id="3ea4c-147">Isso funciona porque o conteúdo de *Controllers* e *Models* é apenas um código que é compilado em uma .dll, enquanto o conteúdo de *Views* não é, até que uma solicitação para essa exibição seja feita.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-147">This works because the content of *Controllers* and *Models* is just code which gets compiled into a .dll where as the content of the *Views* isn't until a request to that view has been made.</span></span>

<span data-ttu-id="3ea4c-148">Depois de definir a hierarquia de pastas, você precisa informar ao MVC que cada controlador está associado a uma área.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-148">Once you've defined the folder hierarchy, you need to tell MVC that each controller is associated with an area.</span></span> <span data-ttu-id="3ea4c-149">Faça isso decorando o nome do controlador com o atributo `[Area]`.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-149">You do that by decorating the controller name with the `[Area]` attribute.</span></span>

```csharp
...
   namespace MyStore.Areas.Products.Controllers
   {
       [Area("Products")]
       public class HomeController : Controller
       {
           // GET: /Products/Home/Index
           public IActionResult Index()
           {
               return View();
           }

           // GET: /Products/Home/Create
           public IActionResult Create()
           {
               return View();
           }
       }
   }
   ```

<span data-ttu-id="3ea4c-150">Configure uma definição de rota que funciona com as áreas recém-criadas.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-150">Set up a route definition that works with your newly created areas.</span></span> <span data-ttu-id="3ea4c-151">O artigo [Rota para ações do controlador](routing.md) apresenta detalhes de como criar definições de rota, incluindo o uso de rotas convencionais versus rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-151">The [Route to controller actions](routing.md) article goes into detail about how to create route definitions, including using conventional routes versus attribute routes.</span></span> <span data-ttu-id="3ea4c-152">Neste exemplo, usaremos uma rota convencional.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-152">In this example, we'll use a conventional route.</span></span> <span data-ttu-id="3ea4c-153">Para fazer isso, abra o arquivo *Startup.cs* e modifique-o adicionando a definição de rota nomeada `areaRoute` abaixo.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-153">To do so, open the *Startup.cs* file and modify it by adding the `areaRoute` named route definition below.</span></span>

```csharp
...
   app.UseMvc(routes =>
   {
     routes.MapRoute(
         name: "areaRoute",
         template: "{area:exists}/{controller=Home}/{action=Index}/{id?}");

     routes.MapRoute(
         name: "default",
         template: "{controller=Home}/{action=Index}/{id?}");
   });
   ```

<span data-ttu-id="3ea4c-154">Navegando para `http://<yourApp>/products`, o método de ação `Index` do `HomeController` na área `Products` será invocado.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-154">Browsing to `http://<yourApp>/products`, the `Index` action method of the `HomeController` in the `Products` area will be invoked.</span></span>

## <a name="link-generation"></a><span data-ttu-id="3ea4c-155">Geração de link</span><span class="sxs-lookup"><span data-stu-id="3ea4c-155">Link Generation</span></span>

* <span data-ttu-id="3ea4c-156">Geração de links em uma ação dentro de um controlador baseado em área para outra ação no mesmo controlador.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-156">Generating links from an action within an area based controller to another action within the same controller.</span></span>

  <span data-ttu-id="3ea4c-157">Digamos que o caminho da solicitação atual seja semelhante a `/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-157">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="3ea4c-158">Sintaxe de HtmlHelper: `@Html.ActionLink("Go to Product's Home Page", "Index")`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-158">HtmlHelper syntax: `@Html.ActionLink("Go to Product's Home Page", "Index")`</span></span>

  <span data-ttu-id="3ea4c-159">Sintaxe de TagHelper: `<a asp-action="Index">Go to Product's Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-159">TagHelper syntax: `<a asp-action="Index">Go to Product's Home Page</a>`</span></span>

  <span data-ttu-id="3ea4c-160">Observe que não é necessário fornecer os valores 'area' e 'controller' aqui, pois já estão disponíveis no contexto da solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-160">Note that we need not supply the 'area' and 'controller' values here as they're already available in the context of the current request.</span></span> <span data-ttu-id="3ea4c-161">Esses tipos de valores são chamados valores `ambient`.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-161">These kind of values are called `ambient` values.</span></span>

* <span data-ttu-id="3ea4c-162">Geração de links em uma ação dentro de um controlador baseado em área para outra ação em outro controlador</span><span class="sxs-lookup"><span data-stu-id="3ea4c-162">Generating links from an action within an area based controller to another action on a different controller</span></span>

  <span data-ttu-id="3ea4c-163">Digamos que o caminho da solicitação atual seja semelhante a `/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-163">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="3ea4c-164">Sintaxe de HtmlHelper: `@Html.ActionLink("Go to Manage Products Home Page", "Index", "Manage")`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-164">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products Home Page", "Index", "Manage")`</span></span>

  <span data-ttu-id="3ea4c-165">Sintaxe de TagHelper: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-165">TagHelper syntax: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span></span>

  <span data-ttu-id="3ea4c-166">Observe que aqui o valor de ambiente de uma "area" é usado, mas o valor de 'controller' é especificado de forma explícita acima.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-166">Note that here the ambient value of an 'area' is used but the 'controller' value is specified explicitly above.</span></span>

* <span data-ttu-id="3ea4c-167">Geração de links em uma ação dentro de um controlador baseado em área para outra ação em outro controlador e outra área.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-167">Generating links from an action within an area based controller to another action on a different controller and a different area.</span></span>

  <span data-ttu-id="3ea4c-168">Digamos que o caminho da solicitação atual seja semelhante a `/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-168">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="3ea4c-169">Sintaxe de HtmlHelper: `@Html.ActionLink("Go to Services Home Page", "Index", "Home", new { area = "Services" })`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-169">HtmlHelper syntax: `@Html.ActionLink("Go to Services Home Page", "Index", "Home", new { area = "Services" })`</span></span>

  <span data-ttu-id="3ea4c-170">Sintaxe de TagHelper: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-170">TagHelper syntax: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services Home Page</a>`</span></span>

  <span data-ttu-id="3ea4c-171">Observe que aqui nenhum valor de ambiente é usado.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-171">Note that here no ambient values are used.</span></span>

* <span data-ttu-id="3ea4c-172">Geração de links em uma ação dentro de um controlador baseado em área para outra ação em outro controlador e **não** em uma área.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-172">Generating links from an action within an area based controller to another action on a different controller and **not** in an area.</span></span>

  <span data-ttu-id="3ea4c-173">Sintaxe de HtmlHelper: `@Html.ActionLink("Go to Manage Products  Home Page", "Index", "Home", new { area = "" })`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-173">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products  Home Page", "Index", "Home", new { area = "" })`</span></span>

  <span data-ttu-id="3ea4c-174">Sintaxe de TagHelper: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="3ea4c-174">TagHelper syntax: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span></span>

  <span data-ttu-id="3ea4c-175">Como queremos gerar links para uma ação do controlador não baseada em área, esvaziamos o valor de ambiente para 'area' aqui.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-175">Since we want to generate links to a non-area based controller action, we empty the ambient value for 'area' here.</span></span>

## <a name="publishing-areas"></a><span data-ttu-id="3ea4c-176">Publicando áreas</span><span class="sxs-lookup"><span data-stu-id="3ea4c-176">Publishing Areas</span></span>

<span data-ttu-id="3ea4c-177">Todos os arquivos `*.cshtml` e `wwwroot/**` são publicados na saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` é incluído no arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="3ea4c-177">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the *.csproj* file.</span></span>
