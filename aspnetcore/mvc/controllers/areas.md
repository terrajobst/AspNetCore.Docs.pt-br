---
title: Áreas no ASP.NET Core
author: rick-anderson
description: Saiba por que as áreas são um recurso do ASP.NET MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace (para roteamento) e uma estrutura de pasta (para exibições) separados.
ms.author: riande
ms.date: 05/10/2019
uid: mvc/controllers/areas
ms.openlocfilehash: f3a75bc307a206e43241b421f448b09011868d08
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65535966"
---
# <a name="areas-in-aspnet-core"></a>Áreas no ASP.NET Core

Por [Dhananjay Kumar](https://twitter.com/debug_mode) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Áreas são um recurso do ASP.NET usado para organizar funcionalidades relacionadas em um grupo como um namespace separado (para roteamento) e uma estrutura de pastas (para exibições). O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area`, a `controller` e `action` ou a uma Razor Page, `page`.

As áreas proporcionam uma maneira de particionar um aplicativo Web do ASP.NET Core em grupos funcionais menores, cada um com seu próprio conjunto de Razor Pages, controladores, exibições e modelos. Uma área é efetivamente uma estrutura MVC dentro de um aplicativo. Em um projeto Web do ASP.NET Core, os componentes lógicos como páginas, modelo, controlador e modo de exibição são mantidos em pastas diferentes. O tempo de execução do ASP.NET Core usa as convenções de nomenclatura para criar a relação entre esses componentes. Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade. Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa. Cada uma dessas unidades tem sua própria área para conter exibições, controladores, Razor Pages e modelos.

Considere o uso de Áreas em um projeto quando:

* O aplicativo é composto por vários componentes funcionais de alto nível que podem ser separados logicamente.
* Você deseja particionar o aplicativo para que cada área funcional possa ser trabalhada de forma independente.

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([como baixar](xref:index#how-to-download-a-sample)). O exemplo de download fornece um aplicativo básico para áreas de teste.

Se você estiver usando o Razor Pages, confira [Áreas com Razor Pages](#areas-with-razor-pages) neste documento.

## <a name="areas-for-controllers-with-views"></a>Áreas para os controladores com modos de exibição

Um aplicativo Web do ASP.NET Core típico usando áreas, controladores e exibições contém o seguinte:

* Uma [estrutura de pastas da área](#area-folder-structure).
* Controladores decorados com o atributo [&lbrack;Area&rbrack;](#attribute) para associar o controlador à área:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* A [rota de área adicionada à inicialização](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Estrutura de pastas da área

Considere um aplicativo que tem dois grupos lógicos, *Produtos* e *Serviços*. Usando áreas, a estrutura de pastas seria semelhante ao seguinte:

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
          * About.cshtml
    * Serviços
      * Controladores
        * HomeController.cs
      * Exibições
        * Home
          * Index.cshtml

Enquanto o layout anterior é típico ao usar áreas, somente os arquivos de exibição são necessários para usar essa estrutura de pastas. Pesquisas de descoberta de exibição para um arquivo de exibição de área correspondente, na seguinte ordem:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

A localização de pastas de não exibição, assim como *Controladores* e *Modelos*, **não** importa. Por exemplo, as pastas *Controladores* e *Modelos* não são necessárias. O conteúdo de *Controladores* e *Modelos* é o código que é compilado em um arquivo .dll. O conteúdo das *Exibições* não é compilado até que seja feita uma solicitação para essa exibição.

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Associar o controlador a uma área

Controladores de área são designados com o atributo [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute):

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Adicionar rota de área

Rotas de área normalmente usam o roteamento convencional, em vez de roteamento de atributo. O roteamento convencional é dependente da ordem. De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.

`{area:...}` pode ser usado como um token em modelos de rota, se o espaço de URL é uniforme entre todas as áreas:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

No código anterior, `exists` aplica uma restrição de que a rota deve corresponder a uma área. Usar `{area:...}` é o mecanismo menos complicado para a adição de roteamento para áreas.

O código a seguir usa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> para criar duas rotas de área nomeadas:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

Ao usar `MapAreaRoute` com o ASP.NET Core 2.2, veja [este problema do GitHub](https://github.com/aspnet/AspNetCore/issues/7772).

Para obter mais informações, veja [Roteamento de área](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Geração de links com áreas do MVC

O código a seguir do [download do exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) mostra geração de link com a área especificada:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.

O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área. A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados. Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área e no mesmo controlador.

Quando a área ou o controlador não for especificado, o roteamento dependerá dos valores do *ambiente*. Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link. Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos.

Para obter mais informações, veja [Roteamento para ações do controlador](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a>Layout compartilhado para áreas usando o arquivo _ViewStart.cshtml

Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Alterar a pasta de área padrão em que as exibições são armazenadas

O código a seguir altera a pasta da área padrão de `"Areas"` para `"MyAreas"`:

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Áreas com Razor Pages

As Áreas com Razor Pages exigem e a pasta *Areas/&lt;nome da área&gt;/Pages* na raiz do aplicativo. A seguinte estrutura de pasta é usada com o [download de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)

* Nome do projeto
  * Áreas
    * Produtos
      * Pages (Páginas)
        * _ViewImports
        * Sobre o
        * Índice
    * Serviços
      * Pages (Páginas)
        * Gerenciar
          * Sobre o
          * Índice

### <a name="link-generation-with-razor-pages-and-areas"></a>Geração de links com áreas e Razor Pages

O código a seguir do [download do exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra a geração de links com a área especificada (por exemplo, `asp-area="Products"`):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Os links gerados com o código anterior são válidos em qualquer lugar no aplicativo.

O download de exemplo inclui uma [exibição parcial](xref:mvc/views/partial) que contém os links anteriores e os mesmos links sem especificar a área. A exibição parcial é referenciada no [arquivo de layout](xref:mvc/views/layout), portanto, todas as páginas no aplicativo exibem os links gerados. Os links gerados sem especificar a área só são válidos quando referenciados de uma página na mesma área.

Quando a área não for especificada, o roteamento dependerá dos valores do *ambiente*. Os valores de rota atuais da solicitação atual são considerados valores de ambiente para a geração de link. Em muitos casos, para o aplicativo de exemplo, usar os valores de ambiente gera links incorretos. Por exemplo, considere os links gerados a partir do código a seguir:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Para o código anterior:

* O link gerado de `<a asp-page="/Manage/About">` só estará correto quando a última solicitação tiver sido para uma página na área `Services`. Por exemplo, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.
* O link gerado a partir `<a asp-page="/About">` só estará correto quando a última solicitação tiver sido para uma página na área `/Home`.
* O código vem do [download de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-viewimports-file"></a>Importar o namespace e os Auxiliares de marca com o arquivo _ViewImports

Um arquivo *_ViewImports.cshtml* pode ser adicionado a cada pasta *Páginas* da área para importar o namespace e os Auxiliares de Marcação para cada Razor Page na pasta.

Considere a área *Serviços* do código de exemplo, que não contém um arquivo *_ViewImports.cshtml*. A marcação a seguir mostra a Página do Razor */Services/Manage/About*:

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

Na marcação anterior:

* O nome de domínio totalmente qualificado deve ser usado para especificar o modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).
* Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são habilitados por `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

No download de exemplo, a área Produtos contém o seguinte arquivo *_ViewImports.cshtml*:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

A marcação a seguir mostra a Página do Razor */Products/About*:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

No arquivo anterior, o namespace e a diretiva `@addTagHelper` são importados para o arquivo por meio do arquivo *Areas/Products/Pages/_ViewImports.cshtml*.

Para saber mais, confira [Gerenciar o escopo do Auxiliar de Marcação](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importar diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Layout compartilhado para Áreas do Razor Pages

Para compartilhar um layout comum para o aplicativo inteiro, mova o *_ViewStart.cshtml* para a pasta raiz do aplicativo.

### <a name="publishing-areas"></a>Publicando áreas

Todos os arquivos *.cshtml e os arquivos do diretório *wwwroot* são publicados em uma saída quando `<Project Sdk="Microsoft.NET.Sdk.Web">` são incluídos no arquivo *.csproj.
