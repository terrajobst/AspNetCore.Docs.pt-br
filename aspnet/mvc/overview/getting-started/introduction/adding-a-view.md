---
title: Adicionando uma exibição a um aplicativo MVC
author: Rick-Anderson
description: Adicionando uma exibição a um aplicativo MVC
ms.author: riande
ms.date: 09/1721/2017
uid: mvc/overview/getting-started/introduction/adding-a-view
ms.openlocfilehash: 47447c82506cc0eb4dafabe272b3204f76a2edd7
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098656"
---
<a name="adding-a-view"></a>Adicionando uma exibição
====================
por [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

Nesta seção você irá modificar o `HelloWorldController` classe para usar a exibição de arquivos de modelo para corretamente encapsulam o processo de geração de respostas HTML para um cliente. 

Você criará um arquivo de modelo de exibição usando o [mecanismo de exibição Razor](../../../../web-pages/overview/getting-started/introducing-razor-syntax-c.md). Modelos de exibição baseado no Razor têm uma *. cshtml* extensão de arquivo e fornecem uma maneira elegante de criar o HTML de saída usando a linguagem c#. Razor minimiza o número de caracteres e pressionamentos de teclas necessários ao escrever um modelo de exibição e permite um rápido, fluido fluxo de trabalho de codificação.

Atualmente, o método `Index` retorna uma cadeia de caracteres com uma mensagem que é embutida em código na classe do controlador. Alterar o `Index` método para chamar os controladores [exibição](/dotnet/api/microsoft.aspnetcore.mvc.controller.view#Microsoft_AspNetCore_Mvc_Controller_View) método, conforme mostrado no código a seguir:

[!code-csharp[Main](adding-a-view/samples/sample1.cs?highlight=1,3)]

O `Index` método acima usa um modelo de exibição para gerar uma resposta HTML para o navegador. Métodos do controlador (também conhecido como [métodos de ação](http://rachelappel.com/asp.net-mvc-actionresults-explained)), como o `Index` método acima, geralmente retornam um [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx) (ou uma classe derivada de [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx)), não os tipos primitivos como cadeia de caracteres.

Clique com botão direito do *Views\HelloWorld* pasta e clique em **Add**, em seguida, clique em **página de exibição MVC 5 com Layout (Razor)**.
  
![](adding-a-view/_static/image1.png)   
  
No **especificar nome para o Item** caixa de diálogo, digite *índice*e, em seguida, clique em **Okey**.  
  
![](adding-a-view/_static/image2.png)  
  
No **selecionar uma página de Layout** caixa de diálogo, aceite o padrão  **\_layout. cshtml** e clique em **Okey**.  
  
![](adding-a-view/_static/image3.png)  
  
Na caixa de diálogo acima, o *Views\Shared* pasta está selecionada no painel esquerdo. Se você tivesse um arquivo de layout personalizado em outra pasta, você pode selecionar a ele. Vamos falar sobre o arquivo de layout posteriormente no tutorial

O *MvcMovie\Views\HelloWorld\Index.cshtml* arquivo é criado.

![](adding-a-view/_static/image4.png)

Adicione a seguinte marcação realçada.

[!code-cshtml[Main](adding-a-view/samples/sample2.cshtml?highlight=4-11)]

Clique com botão direito do *index. cshtml* do arquivo e selecione **exibir no navegador**.

![PI](adding-a-view/_static/image5.png)

Você pode também clique com botão direito do *index. cshtml* do arquivo e selecione **exibir no Page Inspector.** Consulte a [tutorial do Inspetor de página](../../views/using-page-inspector-in-aspnet-mvc.md) para obter mais informações.

Como alternativa, execute o aplicativo e navegue até a `HelloWorld` controlador (`http://localhost:xxxx/HelloWorld`). O `Index` método no seu controlador de não fazer muito trabalho; ele simplesmente executou a instrução `return View()`, qual especificado que o método deve usar um arquivo de modelo de exibição para renderizar uma resposta ao navegador. Porque você não especificar explicitamente o nome do arquivo de modelo de exibição para usar, ASP.NET MVC assumiu como padrão usando o *index. cshtml* arquivo de exibição a *\Views\HelloWorld* pasta. A imagem abaixo mostra a cadeia de caracteres &quot;Olá de nosso modelo de exibição!&quot; embutido em código no modo de exibição.

![](adding-a-view/_static/image6.png)

Parece muito bom. No entanto, observe que a barra de título do navegador mostra &quot;Index - meu aplicativo do ASP.NET "e o link grande na parte superior da página diz:"Nome do aplicativo". Dependendo de como pequenos, você faz a janela do navegador, talvez seja necessário clicar nas três barras no canto superior direito para ver o para o **página inicial**, **sobre**, **contato**, **Registrar** e **fazer logon no** links.

## <a name="changing-views-and-layout-pages"></a>Alterando exibições e páginas de Layout

Primeiro, você deseja alterar o &quot;nome do aplicativo&quot; link na parte superior da página. Se o texto é comum a todas as páginas. Na verdade, ele é implementado em apenas um lugar no projeto, mesmo que ela apareça em todas as páginas no aplicativo. Vá para o */Views/Shared* pasta **Gerenciador de soluções** e abra o  *\_layout. cshtml* arquivo. Esse arquivo é chamado um *página de layout* e ele está na pasta compartilhada que usam todas as outras páginas.

![_LayoutCshtml](adding-a-view/_static/image7.png)

Modelos de layout permitem que você especifique o layout de contêiner HTML do seu site em um só lugar e, em seguida, aplicá-la em várias páginas em seu site. Localize a linha `@RenderBody()`. `RenderBody` é um espaço reservado em que todas as páginas específicas à exibição criadas são mostradas, &quot;encapsuladas&quot; na página de layout. Por exemplo, se você selecionar o **sobre** link, o *Views\Home\About.cshtml* exibição é renderizada dentro de `RenderBody` método.

Altere o conteúdo do elemento de título. Alterar o [ActionLink](https://msdn.microsoft.com/library/dd504972(v=vs.108).aspx) no modelo de layout de &quot;nome do aplicativo&quot; para &quot;filme MVC&quot; e do controlador do `Home` para `Movies`. O arquivo de layout completa é mostrado abaixo:

[!code-cshtml[Main](adding-a-view/samples/sample3.cshtml?highlight=6,20)]

Execute o aplicativo e observe que agora você verá &quot;filme MVC &quot;. Clique o **sobre** link e você verá como essa página mostra &quot;filme MVC&quot;também. Fomos capazes de fazer a alteração uma vez no modelo de layout e ter todas as páginas no site de refletir o novo título.

![](adding-a-view/_static/image8.png)

Quando é criado pela primeira vez o *Views\HelloWorld\Index.cshtml* arquivo, ele continha o código a seguir:

[!code-cshtml[Main](adding-a-view/samples/sample4.cshtml)]

O código do Razor acima é definir explicitamente a página de layout. Examine os *modos de exibição\\viewstart* arquivo, ele contém a marcação do Razor mesma exata. O *[modos de exibição\\viewstart](https://weblogs.asp.net/scottgu/archive/2010/10/22/asp-net-mvc-3-layouts.aspx)* arquivo define o layout comum que todas as exibições usarão, portanto você pode comentar ou remova esse código do *Views\HelloWorld\ Index. cshtml* arquivo.

[!code-cshtml[Main](adding-a-view/samples/sample5.cshtml?highlight=1-3)]

Use a propriedade `Layout` para definir outra exibição de layout ou defina-a como `null` para que nenhum arquivo de layout seja usado.

Agora, vamos alterar o título da exibição índice.

Abra *MvcMovie\Views\HelloWorld\Index.cshtml*. Há dois locais para fazer uma alteração: primeiro, o texto que aparece no título do navegador e, em seguida, no cabeçalho secundário (o `<h2>` elemento). Você os tornará ligeiramente diferentes para que possa ver qual parte do código altera qual parte do aplicativo.

[!code-cshtml[Main](adding-a-view/samples/sample6.cshtml?highlight=2,5)]

Para indicar o título HTML para exibir o código acima define um `Title` propriedade do `ViewBag` objeto (que está na *index. cshtml* modelo de exibição). Observe que o modelo de layout ( *Views\Shared\\layout. cshtml* ) usa esse valor no `<title>` elemento como parte do `<head>` seção do HTML que modificamos anteriormente.

[!code-cshtml[Main](adding-a-view/samples/sample7.cshtml?highlight=6)]

Usando este `ViewBag` abordagem, você pode facilmente passar outros parâmetros entre seu modelo de exibição e seu arquivo de layout.

Execute o aplicativo. Observe que o título do navegador, o cabeçalho primário e os títulos secundários foram alterados. (Se as alterações não forem exibidas no navegador, talvez o conteúdo armazenado em cache esteja sendo exibido. Pressione Ctrl+F5 no navegador para forçar a resposta do servidor a ser carregada.) O título do navegador é criado com o `ViewBag.Title` definimos na *index. cshtml* exibir modelo e adicional &quot;-aplicativo de filme&quot; adicionados no arquivo de layout.

Observe também como o conteúdo na *index. cshtml* modelo de exibição foi mesclado com a  *\_layout. cshtml* modelo de exibição e uma única resposta HTML foi enviada ao navegador. Os modelos de layout facilitam realmente a realização de alterações que se aplicam a todas as páginas do aplicativo.

![](adding-a-view/_static/image9.png)

Nosso pouco &quot;dados&quot; (nesse caso, o &quot;Olá de nosso modelo de exibição!&quot; mensagem) é embutido no código, no entanto. O aplicativo MVC tem um &quot;V&quot; (exibição) e você tem um &quot;C&quot; (controlador), mas nenhum &quot;M&quot; (modelo) ainda. Em breve, vamos examinar como criar um banco de dados e recuperação de dados de modelo dele.

## <a name="passing-data-from-the-controller-to-the-view"></a>Passando dados do controlador para a exibição

Antes de ir para um banco de dados e falar sobre modelos, no entanto, vamos primeiro falar sobre como passar informações do controlador para um modo de exibição. As classes do controlador são invocadas em resposta a uma solicitação de URL de entrada. Uma classe de controlador é onde você escreve o código que manipula o entrada navegador solicita, recupera dados de um banco de dados e, por fim, decide qual tipo de resposta será enviada ao navegador. Modelos de exibição, em seguida, podem ser usados em um controlador para gerar e formatar uma resposta HTML para o navegador.

Os controladores são responsáveis por fornecer quaisquer dados ou objetos são necessários para que um modelo de exibição renderizar uma resposta ao navegador. Uma prática recomendada: **Um modelo de exibição nunca deve executar lógica de negócios ou interagir diretamente com um banco de dados**. Em vez disso, um modelo de exibição deve trabalhar somente com os dados que são fornecidos a ela pelo controlador. Manter isso &quot;separação de preocupações&quot; ajuda a manter seu código limpo, testável e mais sustentável.

No momento, o `Welcome` método de ação de `HelloWorldController` classe usa um `name` e um `numTimes` parâmetro e, em seguida, gera os valores diretamente para o navegador. Em vez de fazer com que o controlador renderize a resposta como uma cadeia de caracteres, vamos alterar o controlador para usar um modelo de exibição em vez disso. O modelo de exibição gerará uma resposta dinâmica, o que significa que você precisa passar bits de dados apropriados do controlador para a exibição para gerar a resposta. Você pode fazer isso fazendo com que o controlador coloque os dados dinâmicos (parâmetros) que o modelo de exibição precisa em um `ViewBag` objeto que pode acessar o modelo de exibição.

Volte para o *HelloWorldController.cs* de arquivo e altere o `Welcome` para adicionar um `Message` e `NumTimes` valor para o `ViewBag` objeto. `ViewBag` é um objeto dinâmico, o que significa que você pode colocar tudo que quiser a ele. o `ViewBag` objeto não tem nenhuma propriedade definida até que você insira algo dentro dele. O [sistema de associação de modelos do ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx) mapeia automaticamente os parâmetros nomeados (`name` e `numTimes`) da cadeia de consulta na barra de endereços para os parâmetros no método. O arquivo *HelloWorldController.cs* completo tem esta aparência:

[!code-csharp[Main](adding-a-view/samples/sample8.cs)]

Agora o `ViewBag` objeto contém dados que serão passados para o modo de exibição automaticamente. Em seguida, você precisa de um modelo de exibição bem-vindo! No **construir** menu, selecione **compilar solução** (ou Ctrl + Shift + B) para garantir que o projeto é compilado. Clique com botão direito do *Views\HelloWorld* pasta e clique em **Add**, em seguida, clique em **página de exibição MVC 5 com Layout (Razor)**.
  
![](adding-a-view/_static/image10.png)   
  
No **especificar nome para o Item** caixa de diálogo, digite *boas-vindas*e, em seguida, clique em **Okey**.   
  
No **selecionar uma página de Layout** caixa de diálogo, aceite o padrão  **\_layout. cshtml** e clique em **Okey**.  
  
![](adding-a-view/_static/image11.png)   

O *MvcMovie\Views\HelloWorld\Welcome.cshtml* arquivo é criado.

Substitua a marcação na *Welcome* arquivo. Você criará um loop que diz &quot;Hello&quot; quantas vezes o usuário disser que deveria. A conclusão *Welcome* arquivo é mostrado abaixo.

[!code-cshtml[Main](adding-a-view/samples/sample9.cshtml)]

Execute o aplicativo e navegue até a URL a seguir:

`http://localhost:xx/HelloWorld/Welcome?name=Scott&numtimes=4`

Agora os dados são obtidos da URL e passados para o controlador usando o [associador de modelo](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx). O controlador empacota os dados em um `ViewBag` objeto e passa esse objeto para o modo de exibição. O modo de exibição, em seguida, exibe os dados como HTML para o usuário.

![](adding-a-view/_static/image12.png)

No exemplo acima, usamos um `ViewBag` objeto para passar dados do controlador para um modo de exibição. Mais adiante no tutorial, usaremos um modelo de exibição para passar dados de um controlador para uma exibição. A abordagem de modelo de exibição para passar dados é geralmente a preferida em relação à abordagem de recipiente de propriedades de exibição. Consulte a entrada de blog [V fortemente tipados exibições dinâmicas](https://blogs.msdn.com/b/rickandy/archive/2011/01/28/dynamic-v-strongly-typed-views.aspx) para obter mais informações. 

Bem, isso foi uma espécie de uma &quot;M&quot; de modelo, mas não o tipo de banco de dados. Vamos ver o que aprendemos e criar um banco de dados de filmes.

> [!div class="step-by-step"]
> [Anterior](adding-a-controller.md)
> [Próximo](adding-a-model.md)
