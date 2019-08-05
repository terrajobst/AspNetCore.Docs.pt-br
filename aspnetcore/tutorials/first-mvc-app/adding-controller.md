---
title: Adicionar um controlador a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Saiba como adicionar um controlador a um aplicativo ASP.NET Core MVC simples.
ms.author: riande
ms.date: 02/28/2017
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: ab97b875956ec262623ed9862ace6a930331d80d
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68682318"
---
# <a name="add-a-controller-to-an-aspnet-core-mvc-app"></a>Adicionar um controlador a um aplicativo ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

O padrão de arquitetura MVC (Model-View-Controller) separa um aplicativo em três componentes principais: **M**odel, **V**iew e **C**ontroller. O padrão MVC ajuda a criar aplicativos que são mais testáveis e fáceis de atualizar comparado aos aplicativos monolíticos tradicionais. Os aplicativos baseados no MVC contêm:

* **M**odelos: classes que representam os dados do aplicativo. As classes de modelo usam a lógica de validação para impor regras de negócio aos dados. Normalmente, os objetos de modelo recuperam e armazenam o estado do modelo em um banco de dados. Neste tutorial, um modelo `Movie` recupera dados de filmes de um banco de dados, fornece-os para a exibição ou atualiza-os. O dados atualizados são gravados em um banco de dados.

* **E**xibições: são os componentes que exibem a interface do usuário do aplicativo. Em geral, essa interface do usuário exibe os dados de modelo.

* **C**ontroladores: classes que manipulam as solicitações do navegador. Elas recuperam dados de modelo e chamam modelos de exibição que retornam uma resposta. Em um aplicativo MVC, a exibição mostra apenas informações; o controlador manipula e responde à entrada e à interação do usuário. Por exemplo, o controlador manipula os dados de rota e os valores de cadeia de consulta e passa esses valores para o modelo. O modelo pode usar esses valores para consultar o banco de dados. Por exemplo, `https://localhost:5001/Home/Privacy` tem dados de rota de `Home` (o controlador) e `Privacy` (o método de ação a ser chamado no controlador principal). `https://localhost:5001/Movies/Edit/5` é uma solicitação para editar o filme com ID=5 usando o controlador do filme. Os dados de rota são explicados posteriormente no tutorial.

O padrão MVC ajuda a criar aplicativos que separam os diferentes aspectos do aplicativo (lógica de entrada, lógica de negócios e lógica da interface do usuário), ao mesmo tempo que fornece um acoplamento flexível entre esses elementos. O padrão especifica o local em que cada tipo de lógica deve estar localizado no aplicativo. A lógica da interface do usuário pertence à exibição. A lógica de entrada pertence ao controlador. A lógica de negócios pertence ao modelo. Essa separação ajuda a gerenciar a complexidade ao criar um aplicativo, porque permite que você trabalhe em um aspecto da implementação por vez, sem afetar o código de outro. Por exemplo, você pode trabalhar no código de exibição sem depender do código da lógica de negócios.

Abrangemos esses conceitos nesta série de tutoriais e mostraremos como usá-los para criar um aplicativo de filme. O projeto MVC contém pastas para os *Controladores* e as *Exibições*.

## <a name="add-a-controller"></a>Adicionar um controlador

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores > Adicionar > Controlador**
  ![Menu de Contexto](adding-controller/_static/add_controller.png)

* Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC – Vazio**

  ![Adicionar o controlador MVC e nomeá-lo](adding-controller/_static/ac.png)

* Na **caixa de diálogo Adicionar Controlador MVC Vazio**, insira **HelloWorldController** e selecione **ADICIONAR**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Selecione o ícone **EXPLORER** e, em seguida, pressione Control (clique com o botão direito do mouse) **Controladores > Novo Arquivo** e nomeie o novo arquivo *HelloWorldController.cs*.

  ![Menu contextual](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores > Adicionar > Novo Arquivo**.
![Menu contextual](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Selecione **ASP.NET Core** e **Classe do Controlador MVC**.

Nomeie o controlador **HelloWorldController**.

![Adicionar o controlador MVC e nomeá-lo](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Substitua o conteúdo de *Controllers/HelloWorldController.cs* pelo seguinte:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Cada método `public` em um controlador pode ser chamado como um ponto de extremidade HTTP. Na amostra acima, ambos os métodos retornam uma cadeia de caracteres. Observe os comentários que precedem cada método.

Um ponto de extremidade HTTP é uma URL direcionável no aplicativo Web, como `https://localhost:5001/HelloWorld`, e combina o protocolo usado `HTTPS`, o local de rede do servidor Web (incluindo a porta TCP) `localhost:5001` e o URI de destino `HelloWorld`.

O primeiro comentário indica que este é um método [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) invocado por meio do acréscimo de `/HelloWorld/` à URL base. O primeiro comentário especifica um método [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) invocado por meio do acréscimo de `/HelloWorld/Welcome/` à URL base. Mais adiante no tutorial, o mecanismo de scaffolding será usado para gerar métodos `HTTP POST` que atualizam dados.

Execute o aplicativo no modo sem depuração e acrescente “HelloWorld” ao caminho na barra de endereços. O método `Index` retorna uma cadeia de caracteres.

![Janela do navegador mostrando a resposta do aplicativo Esta é minha ação padrão](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

O MVC invoca as classes do controlador (e os métodos de ação dentro delas), dependendo da URL de entrada. A [lógica de roteamento de URL](xref:mvc/controllers/routing) padrão usada pelo MVC usa um formato como este para determinar o código a ser invocado:

`/[Controller]/[ActionName]/[Parameters]`

O formato de roteamento é definido no método `Configure` no arquivo *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Quando você acessa o aplicativo e não fornece nenhum segmento de URL, ele usa como padrão o controlador “Home” e o método “Index” especificado na linha do modelo realçada acima.

O primeiro segmento de URL determina a classe do controlador a ser executada. Portanto, o `localhost:xxxx/HelloWorld` mapeia para a classe **HelloWorld**Controller. A segunda parte do segmento de URL determina o método de ação na classe. Portanto, `localhost:xxxx/HelloWorld/Index` fará com que o método `Index` da classe `HelloWorldController` seja executado. Observe que você precisou apenas navegar para `localhost:xxxx/HelloWorld` e o método `Index` foi chamado por padrão. Isso ocorre porque `Index` é o método padrão que será chamado em um controlador se não houver um nome de método explicitamente especificado. A terceira parte do segmento de URL (`id`) refere-se aos dados de rota. Os dados de rota são explicados posteriormente no tutorial.

Navegue para `https://localhost:xxxx/HelloWorld/Welcome`. O método `Welcome` é executado e retorna a cadeia de caracteres `This is the Welcome action method...`. Para essa URL, o controlador é `HelloWorld` e `Welcome` é o método de ação. Você ainda não usou a parte `[Parameters]` da URL.

![Janela do navegador mostrando a resposta do aplicativo Este é o método de ação Boas-vindas](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Modifique o código para passar algumas informações de parâmetro da URL para o controlador. Por exemplo, `/HelloWorld/Welcome?name=Rick&numtimes=4`. Altere o método `Welcome` para incluir dois parâmetros, conforme mostrado no código a seguir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

O código anterior:

* Usa o recurso de parâmetro opcional do C# para indicar que o parâmetro `numTimes` usa 1 como padrão se nenhum valor é passado para esse parâmetro. <!-- remove for simplified -->
* Usa `HtmlEncoder.Default.Encode` para proteger o aplicativo contra entrada mal-intencionada (ou seja, JavaScript).
* Usa [Cadeias de caracteres interpoladas](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) em `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Execute o aplicativo e navegue até:

   `https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

(Substitua xxxx pelo número da porta.) Você pode tentar valores diferentes para `name` e `numtimes` na URL. O sistema de [model binding](xref:mvc/models/model-binding) do MVC mapeia automaticamente os parâmetros nomeados da cadeia de consulta na barra de endereços para os parâmetros no método. Consulte [Model binding](xref:mvc/models/model-binding) para obter mais informações.

![Janela do navegador mostrando a resposta do aplicativo Olá, Ricardo, NumTimes é: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na imagem acima, o segmento de URL (`Parameters`) não é usado e os parâmetros `name` e `numTimes` são transmitidos como [cadeias de consulta](https://wikipedia.org/wiki/Query_string). O `?` (ponto de interrogação) na URL acima é um separador seguido pelas cadeias de consulta. O caractere `&` separa as cadeias de consulta.

Substitua o método `Welcome` pelo seguinte código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Execute o aplicativo e insira a seguinte URL: `https://localhost:xxx/HelloWorld/Welcome/3?name=Rick`

Agora, o terceiro segmento de URL correspondeu ao parâmetro de rota `id`. O método `Welcome` contém um parâmetro `id` que correspondeu ao modelo de URL no método `MapControllerRoute`. O `?` à direita (em `id?`) indica que o parâmetro `id` é opcional.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Nestes exemplos, o controlador tem feito a parte "VC" do MVC – ou seja, o trabalho de **V**iew e de **C**ontroller. O controlador retorna o HTML diretamente. Em geral, você não deseja que os controladores retornem HTML diretamente, pois isso é muito difícil de codificar e manter. Em vez disso, normalmente, você usa um arquivo de modelo de exibição do Razor separado para gerar a resposta HTML. Faça isso no próximo tutorial.

> [!div class="step-by-step"]
> [Anterior](start-mvc.md)
> [Próximo](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O padrão de arquitetura MVC (Model-View-Controller) separa um aplicativo em três componentes principais: **M**odel, **V**iew e **C**ontroller. O padrão MVC ajuda a criar aplicativos que são mais testáveis e fáceis de atualizar comparado aos aplicativos monolíticos tradicionais. Os aplicativos baseados no MVC contêm:

* **M**odelos: classes que representam os dados do aplicativo. As classes de modelo usam a lógica de validação para impor regras de negócio aos dados. Normalmente, os objetos de modelo recuperam e armazenam o estado do modelo em um banco de dados. Neste tutorial, um modelo `Movie` recupera dados de filmes de um banco de dados, fornece-os para a exibição ou atualiza-os. O dados atualizados são gravados em um banco de dados.

* **E**xibições: são os componentes que exibem a interface do usuário do aplicativo. Em geral, essa interface do usuário exibe os dados de modelo.

* **C**ontroladores: classes que manipulam as solicitações do navegador. Elas recuperam dados de modelo e chamam modelos de exibição que retornam uma resposta. Em um aplicativo MVC, a exibição mostra apenas informações; o controlador manipula e responde à entrada e à interação do usuário. Por exemplo, o controlador manipula os dados de rota e os valores de cadeia de consulta e passa esses valores para o modelo. O modelo pode usar esses valores para consultar o banco de dados. Por exemplo, `https://localhost:5001/Home/About` tem dados de rota de `Home` (o controlador) e `About` (o método de ação a ser chamado no controlador principal). `https://localhost:5001/Movies/Edit/5` é uma solicitação para editar o filme com ID=5 usando o controlador do filme. Os dados de rota são explicados posteriormente no tutorial.

O padrão MVC ajuda a criar aplicativos que separam os diferentes aspectos do aplicativo (lógica de entrada, lógica de negócios e lógica da interface do usuário), ao mesmo tempo que fornece um acoplamento flexível entre esses elementos. O padrão especifica o local em que cada tipo de lógica deve estar localizado no aplicativo. A lógica da interface do usuário pertence à exibição. A lógica de entrada pertence ao controlador. A lógica de negócios pertence ao modelo. Essa separação ajuda a gerenciar a complexidade ao criar um aplicativo, porque permite que você trabalhe em um aspecto da implementação por vez, sem afetar o código de outro. Por exemplo, você pode trabalhar no código de exibição sem depender do código da lógica de negócios.

Abrangemos esses conceitos nesta série de tutoriais e mostraremos como usá-los para criar um aplicativo de filme. O projeto MVC contém pastas para os *Controladores* e as *Exibições*.

## <a name="add-a-controller"></a>Adicionar um controlador

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores > Adicionar > Controlador**
  ![Menu de Contexto](adding-controller/_static/add_controller.png)

* Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC – Vazio**

  ![Adicionar o controlador MVC e nomeá-lo](adding-controller/_static/ac.png)

* Na **caixa de diálogo Adicionar Controlador MVC Vazio**, insira **HelloWorldController** e selecione **ADICIONAR**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Selecione o ícone **EXPLORER** e, em seguida, pressione Control (clique com o botão direito do mouse) **Controladores > Novo Arquivo** e nomeie o novo arquivo *HelloWorldController.cs*.

  ![Menu contextual](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores > Adicionar > Novo Arquivo**.
![Menu contextual](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Selecione **ASP.NET Core** e **Classe do Controlador MVC**.

Nomeie o controlador **HelloWorldController**.

![Adicionar o controlador MVC e nomeá-lo](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Substitua o conteúdo de *Controllers/HelloWorldController.cs* pelo seguinte:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Cada método `public` em um controlador pode ser chamado como um ponto de extremidade HTTP. Na amostra acima, ambos os métodos retornam uma cadeia de caracteres. Observe os comentários que precedem cada método.

Um ponto de extremidade HTTP é uma URL direcionável no aplicativo Web, como `https://localhost:5001/HelloWorld`, e combina o protocolo usado `HTTPS`, o local de rede do servidor Web (incluindo a porta TCP) `localhost:5001` e o URI de destino `HelloWorld`.

O primeiro comentário indica que este é um método [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) invocado por meio do acréscimo de `/HelloWorld/` à URL base. O primeiro comentário especifica um método [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) invocado por meio do acréscimo de `/HelloWorld/Welcome/` à URL base. Mais adiante no tutorial, o mecanismo de scaffolding será usado para gerar métodos `HTTP POST` que atualizam dados.

Execute o aplicativo no modo sem depuração e acrescente “HelloWorld” ao caminho na barra de endereços. O método `Index` retorna uma cadeia de caracteres.

![Janela do navegador mostrando a resposta do aplicativo Esta é minha ação padrão](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

O MVC invoca as classes do controlador (e os métodos de ação dentro delas), dependendo da URL de entrada. A [lógica de roteamento de URL](xref:mvc/controllers/routing) padrão usada pelo MVC usa um formato como este para determinar o código a ser invocado:

`/[Controller]/[ActionName]/[Parameters]`

O formato de roteamento é definido no método `Configure` no arquivo *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Quando você acessa o aplicativo e não fornece nenhum segmento de URL, ele usa como padrão o controlador “Home” e o método “Index” especificado na linha do modelo realçada acima.

O primeiro segmento de URL determina a classe do controlador a ser executada. Portanto, `localhost:xxxx/HelloWorld` é mapeado para a classe `HelloWorldController`. A segunda parte do segmento de URL determina o método de ação na classe. Portanto, `localhost:xxxx/HelloWorld/Index` fará com que o método `Index` da classe `HelloWorldController` seja executado. Observe que você precisou apenas navegar para `localhost:xxxx/HelloWorld` e o método `Index` foi chamado por padrão. Isso ocorre porque `Index` é o método padrão que será chamado em um controlador se um nome de método não for especificado explicitamente. A terceira parte do segmento de URL (`id`) refere-se aos dados de rota. Os dados de rota são explicados posteriormente no tutorial.

Navegue para `https://localhost:xxxx/HelloWorld/Welcome`. O método `Welcome` é executado e retorna a cadeia de caracteres `This is the Welcome action method...`. Para essa URL, o controlador é `HelloWorld` e `Welcome` é o método de ação. Você ainda não usou a parte `[Parameters]` da URL.

![Janela do navegador mostrando a resposta do aplicativo Este é o método de ação Boas-vindas](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Modifique o código para passar algumas informações de parâmetro da URL para o controlador. Por exemplo, `/HelloWorld/Welcome?name=Rick&numtimes=4`. Altere o método `Welcome` para incluir dois parâmetros, conforme mostrado no código a seguir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

O código anterior:

* Usa o recurso de parâmetro opcional do C# para indicar que o parâmetro `numTimes` usa 1 como padrão se nenhum valor é passado para esse parâmetro. <!-- remove for simplified -->
* Usa `HtmlEncoder.Default.Encode` para proteger o aplicativo contra entrada mal-intencionada (ou seja, JavaScript).
* Usa [Cadeias de caracteres interpoladas](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) em `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Execute o aplicativo e navegue até:

   `https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

(Substitua xxxx pelo número da porta.) Você pode tentar valores diferentes para `name` e `numtimes` na URL. O sistema de [model binding](xref:mvc/models/model-binding) do MVC mapeia automaticamente os parâmetros nomeados da cadeia de consulta na barra de endereços para os parâmetros no método. Consulte [Model binding](xref:mvc/models/model-binding) para obter mais informações.

![Janela do navegador mostrando a resposta do aplicativo Olá, Ricardo, NumTimes é: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na imagem acima, o segmento de URL (`Parameters`) não é usado e os parâmetros `name` e `numTimes` são transmitidos como [cadeias de consulta](https://wikipedia.org/wiki/Query_string). O `?` (ponto de interrogação) na URL acima é um separador seguido pelas cadeias de consulta. O caractere `&` separa as cadeias de consulta.

Substitua o método `Welcome` pelo seguinte código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Execute o aplicativo e insira a seguinte URL: `https://localhost:xxx/HelloWorld/Welcome/3?name=Rick`

Agora, o terceiro segmento de URL correspondeu ao parâmetro de rota `id`. O método `Welcome` contém um parâmetro `id` que correspondeu ao modelo de URL no método `MapRoute`. O `?` à direita (em `id?`) indica que o parâmetro `id` é opcional.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

Nestes exemplos, o controlador faz a parte “VC” do MVC – ou seja, o trabalho da exibição e do controlador. O controlador retorna o HTML diretamente. Em geral, você não deseja que os controladores retornem HTML diretamente, pois isso é muito difícil de codificar e manter. Em vez disso, normalmente, você usa um arquivo de modelo de exibição do Razor separado para ajudar a gerar a resposta HTML. Faça isso no próximo tutorial.

> [!div class="step-by-step"]
> [Anterior](start-mvc.md)
> [Próximo](adding-view.md)

::: moniker-end
