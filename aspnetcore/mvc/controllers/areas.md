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
# <a name="areas-in-aspnet-core"></a>Áreas no ASP.NET Core

Por [Dhananjay Kumar](https://twitter.com/debug_mode) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Áreas são um recurso do ASP.NET MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace separado (para roteamento) e uma estrutura de pastas (para exibições). O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area`, a `controller` e `action`.

As áreas fornecem uma maneira de particionar um aplicativo Web ASP.NET Core MVC grande em agrupamentos funcionais menores. Uma área é efetivamente uma estrutura MVC dentro de um aplicativo. Em um projeto MVC, componentes lógicos como Modelo, Controlador e Exibição são mantidos em pastas diferentes e o MVC usa convenções de nomenclatura para criar a relação entre esses componentes. Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade. Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa, etc. Cada uma dessas unidades têm suas próprias exibições de componente lógico, controladores e modelos. Nesse cenário, você pode usar Áreas para particionar fisicamente os componentes de negócios no mesmo projeto.

Uma área pode ser definida como as menores unidades funcionais em um projeto do ASP.NET Core MVC com seu próprio conjunto de controladores, exibições e modelos.

Considere o uso de Áreas em um projeto MVC quando:

* O aplicativo é composto por vários componentes funcionais de alto nível que devem ser separados logicamente

* Você deseja particionar o projeto MVC para que cada área funcional possa ser trabalhada de forma independente

Recursos de área:

* Um aplicativo ASP.NET Core MVC pode ter qualquer quantidade de áreas.

* Cada área tem seus próprios controladores, modelos e exibições.

* As áreas permitem organizar projetos MVC grandes em vários componentes de alto nível que podem ser trabalhados de forma independente.

* As áreas dão suporte a vários controladores com o mesmo nome, desde que eles tenham *áreas* diferentes.

Vamos dar uma olhada em um exemplo para ilustrar como as Áreas são criadas e usadas. Digamos que você tenha um aplicativo de loja que tenha dois agrupamentos distintos de controladores e exibições: Produtos e Serviços. Uma estrutura de pastas comum para isso usando áreas do MVC tem a aparência mostrada abaixo:

* Nome do projeto
  * Áreas
    * Produtos
      * Controladores
        * HomeController.cs
        * ManageController.cs
      * Exibições
        * Home
          * Index.cshtml
        * Gerenciar
          * Index.cshtml
    * Serviços
      * Controladores
        * HomeController.cs
      * Exibições
        * Home
          * Index.cshtml

Quando o MVC tenta renderizar uma exibição em uma Área, por padrão, ele tenta procurar nos seguintes locais:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

Esses são os locais padrão que podem ser alterados por meio do `AreaViewLocationFormats` no `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.

Por exemplo, no código abaixo, em vez de ter o nome da pasta como 'Areas', ele foi alterado para 'Categories'.

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

Uma coisa importante a ser observada é que a estrutura da pasta *Views* é a única que é considerada importante aqui e, o conteúdo do restante das pastas como *Controllers* e *Models* **não** importa. Por exemplo, você não precisa ter uma pasta *Controllers* e *Models*. Isso funciona porque o conteúdo de *Controllers* e *Models* é apenas um código que é compilado em uma .dll, enquanto o conteúdo de *Views* não é, até que uma solicitação para essa exibição seja feita.

Depois de definir a hierarquia de pastas, você precisa informar ao MVC que cada controlador está associado a uma área. Faça isso decorando o nome do controlador com o atributo `[Area]`.

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

Configure uma definição de rota que funciona com as áreas recém-criadas. O artigo [Rota para ações do controlador](routing.md) apresenta detalhes de como criar definições de rota, incluindo o uso de rotas convencionais versus rotas de atributo. Neste exemplo, usaremos uma rota convencional. Para fazer isso, abra o arquivo *Startup.cs* e modifique-o adicionando a definição de rota nomeada `areaRoute` abaixo.

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

Navegando para `http://<yourApp>/products`, o método de ação `Index` do `HomeController` na área `Products` será invocado.

## <a name="link-generation"></a>Geração de link

* Geração de links em uma ação dentro de um controlador baseado em área para outra ação no mesmo controlador.

  Digamos que o caminho da solicitação atual seja semelhante a `/Products/Home/Create`

  Sintaxe de HtmlHelper: `@Html.ActionLink("Go to Product's Home Page", "Index")`

  Sintaxe de TagHelper: `<a asp-action="Index">Go to Product's Home Page</a>`

  Observe que não é necessário fornecer os valores 'area' e 'controller' aqui, pois já estão disponíveis no contexto da solicitação atual. Esses tipos de valores são chamados valores `ambient`.

* Geração de links em uma ação dentro de um controlador baseado em área para outra ação em outro controlador

  Digamos que o caminho da solicitação atual seja semelhante a `/Products/Home/Create`

  Sintaxe de HtmlHelper: `@Html.ActionLink("Go to Manage Products Home Page", "Index", "Manage")`

  Sintaxe de TagHelper: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`

  Observe que aqui o valor de ambiente de uma "area" é usado, mas o valor de 'controller' é especificado de forma explícita acima.

* Geração de links em uma ação dentro de um controlador baseado em área para outra ação em outro controlador e outra área.

  Digamos que o caminho da solicitação atual seja semelhante a `/Products/Home/Create`

  Sintaxe de HtmlHelper: `@Html.ActionLink("Go to Services Home Page", "Index", "Home", new { area = "Services" })`

  Sintaxe de TagHelper: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services Home Page</a>`

  Observe que aqui nenhum valor de ambiente é usado.

* Geração de links em uma ação dentro de um controlador baseado em área para outra ação em outro controlador e **não** em uma área.

  Sintaxe de HtmlHelper: `@Html.ActionLink("Go to Manage Products  Home Page", "Index", "Home", new { area = "" })`

  Sintaxe de TagHelper: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`

  Como queremos gerar links para uma ação do controlador não baseada em área, esvaziamos o valor de ambiente para 'area' aqui.

## <a name="publishing-areas"></a>Publicando áreas

Todos os arquivos `*.cshtml` e `wwwroot/**` são publicados na saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` é incluído no arquivo *.csproj*.
