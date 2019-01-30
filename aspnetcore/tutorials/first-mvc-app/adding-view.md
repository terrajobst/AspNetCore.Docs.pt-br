---
title: Adicionar uma exibição a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Adicionando uma exibição a um aplicativo ASP.NET Core MVC simples
ms.author: riande
ms.date: 03/04/2017
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: f241a19c8821019f327fb160f01fe01eca53c5d0
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836891"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>Adicionar uma exibição a um aplicativo ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Nesta seção, você modifica a classe `HelloWorldController` para que ela use os arquivos de exibição do [Razor](xref:mvc/views/razor) para encapsular corretamente o processo de geração de respostas HTML para um cliente.

Crie um arquivo de modelo de exibição usando o Razor. Os modelos de exibição baseados no Razor têm uma extensão de arquivo *.cshtml*. Eles fornecem uma maneira elegante de criar a saída HTML com o C#.

Atualmente, o método `Index` retorna uma cadeia de caracteres com uma mensagem que é embutida em código na classe do controlador. Na classe `HelloWorldController`, substitua o método `Index` pelo seguinte código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

O código anterior chama o método <xref:Microsoft.AspNetCore.Mvc.Controller.View*> do controlador. Ele usa um modelo de exibição para gerar uma resposta HTML. Métodos do controlador (também conhecidos como *métodos de ação*), como o método `Index` acima, geralmente retornam um <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou uma classe derivada de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), não um tipo como `string`.

## <a name="add-a-view"></a>Adicionar uma exibição

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.

* Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Item**.

* Na caixa de diálogo **Adicionar Novo Item – MvcMovie**

  * Na caixa de pesquisa no canto superior direito, insira *exibição*

  * Selecione **Exibição Razor**

  * Mantenha o valor da caixa **Nome**, *Index.cshtml*.

  * Selecione **Adicionar**

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Adicione uma exibição `Index` ao `HelloWorldController`.

* Adicione uma nova pasta chamada *Views/HelloWorld*.
* Adicione um novo arquivo à pasta *Views/HelloWorld* chamada *Index.cshtml*.

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse na pasta *Exibições* e, em seguida, **Adicionar > Nova Pasta** e nomeie a pasta *HelloWorld*.
* Clique com o botão direito do mouse na pasta *Views/HelloWorld* e, em seguida, clique em **Adicionar > Novo Arquivo**.
* Na caixa de diálogo **Novo Arquivo**:

  * Selecione **Web** no painel esquerdo.
  * Selecione **Arquivo HTML vazio** no painel central.
  * Digite *Index.cshtml* na caixa **Nome**.
  * Selecione **Novo**.

![Caixa de diálogo Adicionar Novo Item](adding-view/_static/add_view.png)

---  
<!-- End of VS tabs -->

Substitua o conteúdo do arquivo de exibição *Views/HelloWorld/Index.cshtml* do Razor pelo seguinte:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Navegue para `https://localhost:xxxx/HelloWorld`. O método `Index` no `HelloWorldController` não fez muita coisa: ele executou a instrução `return View();`, que especificou que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta para o navegador. Como você não especificou explicitamente o nome do arquivo do modelo de exibição, o MVC usou como padrão o arquivo de exibição *Index.cshtml* na pasta */Views/HelloWorld*. A imagem abaixo mostra a cadeia de caracteres “Olá de nosso modelo de exibição!” embutida em código na exibição.

![Janela do navegador](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Alterar exibições e páginas de layout

Selecione os links de menu (**MvcMovie**, **Página Inicial** e **Privacidade**). Cada página mostra o mesmo layout de menu. O layout de menu é implementado no arquivo *Views/Shared/_Layout.cshtml*. Abra o arquivo *Views/Shared/_Layout.cshtml*.

Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site. Localize a linha `@RenderBody()`. `RenderBody` é um espaço reservado em que todas as páginas específicas à exibição criadas são mostradas, *encapsuladas* na página de layout. Por exemplo, se você selecionar o link **Privacidade**, a exibição **Views/Home/Privacy.cshtml** será renderizada dentro do método `RenderBody`.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Alterar o título, o rodapé e o link de menu no arquivo de layout

* Nos elementos de título e de rodapé, altere `MvcMovie` para `Movie App`.
* Altere o elemento de âncora `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` para `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

A seguinte marcação mostra as alterações realçadas:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24)]

Na marcação anterior, o `asp-area` [atributo do Auxiliar de Marca de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) foi omitido porque este aplicativo não está usando [Áreas](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Observação**: O controlador `Movies` não foi implementado. Neste ponto, o link `Movie App` não está funcionando.

Salve suas alterações e selecione o link **Privacidade**. Observe como o título na guia do navegador agora exibe **Política de Privacidade – Aplicativo de filme**, em vez de **Política de Privacidade – Filme MVC**:

![Guia Privacidade](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Selecione o link **Página Inicial** e observe que o texto do título e de âncora também exibem **Aplicativo de Filme**. Conseguimos fazer a alteração uma vez no modelo de layout e fazer com que todas as páginas no site refletissem o novo texto do link e o novo título.

Examine o arquivo *Views/_ViewStart.cshtml*:

```HTML
@{
    Layout = "_Layout";
}
```

O arquivo *Views/_ViewStart.cshtml* mostra o arquivo *Views/Shared/_Layout.cshtml* em cada exibição. A propriedade `Layout` pode ser usada para definir outra exibição de layout ou defina-a como `null` para que nenhum arquivo de layout seja usado.

Altere o título e o elemento `<h2>` do arquivo de exibição *Views/HelloWorld/Index.cshtml*:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

O título e o elemento `<h2>` são ligeiramente diferentes para que possa ver qual parte do código altera a exibição.

`ViewData["Title"] = "Movie List";` no código acima define a propriedade `Title` do dicionário `ViewData` como “Lista de Filmes”. A propriedade `Title` é usada no elemento HTML `<title>` na página de layout:

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

Salve as alterações e navegue para `https://localhost:xxxx/HelloWorld`. Observe que o título do navegador, o cabeçalho primário e os títulos secundários foram alterados. (Se as alterações não forem exibidas no navegador, talvez o conteúdo armazenado em cache esteja sendo exibido. Pressione Ctrl+F5 no navegador para forçar a resposta do servidor a ser carregada.) O título do navegador é criado com `ViewData["Title"]` que definimos no modelo de exibição *Index.cshtml* e o “– Aplicativo de Filme” adicional adicionado no arquivo de layout.

Observe também como o conteúdo no modelo de exibição *Index.cshtml* foi mesclado com o modelo de exibição *Views/Shared/_Layout.cshtml* e uma única resposta HTML foi enviada para o navegador. Os modelos de layout facilitam realmente a realização de alterações que se aplicam a todas as páginas do aplicativo. Para saber mais, consulte [Layout](xref:mvc/views/layout).

![Exibição de Lista de Filmes](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Apesar disso, nossos poucos “dados” (nesse caso, a mensagem “Olá de nosso modelo de exibição!”) são embutidos em código. O aplicativo MVC tem um “V” (exibição) e você tem um “C” (controlador), mas ainda nenhum “M” (modelo).

## <a name="passing-data-from-the-controller-to-the-view"></a>Passando dados do controlador para a exibição

As ações do controlador são invocadas em resposta a uma solicitação de URL de entrada. Uma classe de controlador é o local em que o código é escrito e que manipula as solicitações recebidas do navegador. O controlador recupera dados de uma fonte de dados e decide qual tipo de resposta será enviada novamente para o navegador. Modelos de exibição podem ser usados em um controlador para gerar e formatar uma resposta HTML para o navegador.

Os controladores são responsáveis por fornecer os dados necessários para que um modelo de exibição renderize uma resposta. Uma melhor prática: modelos de exibição **não** devem executar a lógica de negócios nem interagir diretamente com um banco de dados. Em vez disso, um modelo de exibição deve funcionar somente com os dados fornecidos pelo controlador. Manter essa “separação de preocupações” ajuda a manter o código limpo, testável e com capacidade de manutenção.

Atualmente, o método `Welcome` na classe `HelloWorldController` usa um parâmetro `name` e um `ID` e, em seguida, gera os valores diretamente no navegador. Em vez de fazer com que o controlador renderize a resposta como uma cadeia de caracteres, altere o controlador para que ele use um modelo de exibição. O modelo de exibição gera uma resposta dinâmica, o que significa que é necessário passar bits de dados apropriados do controlador para a exibição para gerar a resposta. Faça isso fazendo com que o controlador coloque os dados dinâmicos (parâmetros) que o modelo de exibição precisa em um dicionário `ViewData` que pode ser acessado em seguida pelo modelo de exibição.

Em *HelloWorldController.cs*, altere o método `Welcome` para adicionar um valor `Message` e `NumTimes` ao dicionário `ViewData`. O dicionário `ViewData` é um objeto dinâmico, o que significa que qualquer tipo pode ser usado. O objeto `ViewData` não tem nenhuma propriedade definida até que você insira algo nele. O sistema de [model binding](xref:mvc/models/model-binding) MVC mapeia automaticamente os parâmetros nomeados (`name` e `numTimes`) da cadeia de consulta na barra de endereços para os parâmetros no método. O arquivo *HelloWorldController.cs* completo tem esta aparência:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

O objeto de dicionário `ViewData` contém dados que serão passados para a exibição. 

Crie um modelo de exibição Boas-vindas chamado *Views/HelloWorld/Welcome.cshtml*.

Você criará um loop no modelo de exibição *Welcome.cshtml* que exibe “Olá” `NumTimes`. Substitua o conteúdo de *Views/HelloWorld/Welcome.cshtml* pelo seguinte:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Salve as alterações e navegue para a seguinte URL:

`https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

Os dados são obtidos da URL e passados para o controlador usando o [associador de modelo MVC](xref:mvc/models/model-binding). O controlador empacota os dados em um dicionário `ViewData` e passa esse objeto para a exibição. Em seguida, a exibição renderiza os dados como HTML para o navegador.

![Exibição de privacidade que mostra um rótulo Boas-vindas e a frase Olá, Ricardo mostrada quatro vezes](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

No exemplo acima, o dicionário `ViewData` foi usado para passar dados do controlador para uma exibição. Mais adiante no tutorial, um modelo de exibição será usado para passar dados de um controlador para uma exibição. A abordagem de modelo de exibição para passar dados é geralmente a preferida em relação à abordagem do dicionário `ViewData`. Para obter mais informações, confira [Quando usar ViewBag, ViewData ou TempData ](http://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).

No próximo tutorial, será criado um banco de dados de filmes.

> [!div class="step-by-step"]
> [Anterior](adding-controller.md)
> [Próximo](adding-model.md)
