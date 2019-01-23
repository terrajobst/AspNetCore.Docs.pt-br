---
uid: mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-cs
title: Exibições do ASP.NET MVC visão geral (c#) | Microsoft Docs
author: StephenWalther
description: O que é um modo de exibição de MVC do ASP.NET e como ela difere de uma página HTML? Neste tutorial, Stephen Walther apresenta exibições e demonstra como você pode t...
ms.author: riande
ms.date: 02/16/2008
ms.assetid: 152ab1e5-aec2-4ea7-b8cc-27a24dd9acb8
msc.legacyurl: /mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-cs
msc.type: authoredcontent
ms.openlocfilehash: a8e64a99549584f150d64d909ac97210257b1147
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444123"
---
<a name="aspnet-mvc-views-overview-c"></a>Exibições do ASP.NET MVC visão geral (c#)
====================
por [Stephen Walther](https://github.com/StephenWalther)

> O que é um modo de exibição de MVC do ASP.NET e como ela difere de uma página HTML? Neste tutorial, Stephen Walther apresenta a modos de exibição e demonstra como você pode tirar proveito dos dados de exibição e auxiliares HTML dentro de um modo de exibição.


O objetivo deste tutorial é fornecer uma breve introdução ao ASP.NET MVC exibições, exibir dados e auxiliares HTML. No final deste tutorial, você deve compreender como criar novas exibições, passar dados de um controlador para um modo de exibição e usar os auxiliares HTML para gerar o conteúdo em uma exibição.

## <a name="understanding-views"></a>Noções básicas sobre exibições

Para o ASP.NET ou páginas ASP, ASP.NET MVC não inclui tudo o que corresponde diretamente a uma página. Em um aplicativo ASP.NET MVC, não há uma página em disco que corresponde ao caminho na URL que você digita na barra de endereços do navegador. A coisa mais próxima a uma página em um aplicativo ASP.NET MVC é algo chamado de um *exibição*.

Em um aplicativo ASP.NET MVC, as solicitações recebidas do navegador são mapeadas para ações do controlador. Uma ação do controlador pode retornar uma exibição. No entanto, uma ação do controlador pode executar algum outro tipo de ação como redirecionando você para outra ação do controlador.

Listagem 1 contém um controlador simples chamado HomeController. O HomeController expõe duas ações do controlador chamadas Index () e Details().

**Listagem 1 - HomeController.cs**

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample1.cs)]

Você pode invocar a primeira ação, a ação Index (), digitando a seguinte URL na barra de endereços do navegador:

/ Home/Index

Você pode invocar a segunda ação, a ação Details(), digitando este endereço no seu navegador:

/ Home/detalhes

A ação Index () retorna uma exibição. A maioria das ações que você cria retornará os modos de exibição. No entanto, uma ação pode retornar outros tipos de resultados de ação. Por exemplo, a ação de Details() retorna um RedirectToActionResult que redireciona a solicitação de entrada para a ação Index ().

A ação Index () contém a única linha de código a seguir:

View();

Esta linha de código retorna uma exibição que deve estar localizada no seguinte caminho em seu servidor web:

\Views\Home\Index.aspx

O caminho para o modo de exibição é inferido do nome do controlador e o nome da ação de controlador.

Se você preferir, você pode ser explícito sobre o modo de exibição. A linha de código a seguir retorna um modo de exibição chamado Fred:

Modo de exibição (Vinicius);

Quando esta linha de código é executada, uma exibição é retornada do caminho a seguir:

\Views\Home\Fred.aspx

> [!NOTE] 
> 
> Se você planeja criar testes de unidade para seu aplicativo ASP.NET MVC é uma boa ideia para ser explícito sobre nomes de exibição. Dessa forma, você pode criar um teste de unidade para verificar se o modo de exibição esperado foi retornado por uma ação do controlador.


## <a name="adding-content-to-a-view"></a>Adicionando conteúdo a um modo de exibição

Um modo de exibição é um padrão de (documento HTML que pode conter scripts X). Você pode usar scripts para adicionar conteúdo dinâmico a um modo de exibição.

Por exemplo, o modo de exibição na listagem 2 exibe a data e hora atuais.

**Listing 2 - \Views\Home\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample2.aspx)]

Observe que o corpo da página HTML na listagem 2 contém o script a seguir:

&lt;% Response.Write(DateTime.Now);%&gt;

Você usa os delimitadores de script &lt;% e %&gt; para marcar o início e término de um script. Esse script é escrito em c#. Ele exibe a data e hora atuais, chamando o método Response para processar o conteúdo para o navegador. Os delimitadores de script &lt;% e %&gt; pode ser usado para executar uma ou mais instruções.

Uma vez que você chama Response com tanta frequência, Microsoft fornece um atalho para chamar o método Response. O modo de exibição na listagem 3 usa os delimitadores &lt;% = e %&gt; como um atalho para a chamada de Response.

**Listagem 3 - Views\Home\Index2.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample3.aspx)]

Você pode usar qualquer linguagem .NET para gerar o conteúdo dinâmico em uma exibição. Normalmente, você usará o Visual Basic .NET ou C# para gravar seus controladores e modos de exibição.

## <a name="using-html-helpers-to-generate-view-content"></a>Usando os auxiliares HTML para gerar o conteúdo da exibição

Para tornar mais fácil de adicionar conteúdo a um modo de exibição, você pode tirar proveito de algo chamado de um *auxiliar HTML*. Um auxiliar de HTML, normalmente, é um método que gera uma cadeia de caracteres. Você pode usar os auxiliares HTML para gerar elementos HTML padrão, como caixas de texto, links, listas suspensas e caixas de listagem.

Por exemplo, a exibição na listagem 4 tira proveito dos três auxiliares de HTML – auxiliares BeginForm(), TextBox() e Password() – para gerar um logon do formam (veja a Figura 1).

**Listing 4 -- \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample4.aspx)]


[![A caixa de diálogo Novo projeto](asp-net-mvc-views-overview-cs/_static/image1.jpg)](asp-net-mvc-views-overview-cs/_static/image1.png)

**Figura 01**: Um formulário de logon padrão ([clique para exibir a imagem em tamanho normal](asp-net-mvc-views-overview-cs/_static/image2.png))


Todos os métodos auxiliares HTML são chamados na propriedade Html da exibição. Por exemplo, você deve processar uma caixa de texto chamando o método Html.TextBox().

Observe que você usa os delimitadores de script &lt;% = e %&gt; ao chamar o Html.TextBox() e Html.Password() auxiliares. Esses auxiliares simplesmente retornam uma cadeia de caracteres. Você precisa chamar Response para renderizar a cadeia de caracteres para o navegador.

Usar métodos auxiliares HTML é opcional. Eles tornam sua vida mais fácil, reduzindo a quantidade de HTML e script que você precisa escrever. O modo de exibição na listagem 5 apresenta o mesmo formato exato que o modo de exibição na listagem 4 sem uso de auxiliares de HTML.

**Listing 5 -- \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample5.aspx)]

Você também tem a opção de criar seu próprio auxiliares HTML. Por exemplo, você pode criar um método auxiliar GridView() que exibe um conjunto de registros do banco de dados em uma tabela HTML automaticamente. Podemos explorar esse tópico no tutorial **criação de auxiliares de HTML personalizado**.

## <a name="using-view-data-to-pass-data-to-a-view"></a>Usando dados de exibição para passar dados para um modo de exibição

Você pode usar dados de exibição para passar dados de um controlador para um modo de exibição. Pense em dados de exibição, como um pacote que você envia por meio de email. Todos os dados passados de um controlador para um modo de exibição devem ser enviados usando este pacote. Por exemplo, o controlador na listagem 6 adiciona uma mensagem para exibir os dados.

**Listagem 6 - ProductController.cs**

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample6.cs)]

O controlador de propriedade ViewData representa uma coleção de pares nome-valor. Na listagem 6, o método Index () adiciona um item para a coleta de dados de exibição denominada mensagem com o valor Olá, mundo!. Quando o modo de exibição é retornado pelo método Index (), os dados de exibição são passados para o modo de exibição automaticamente.

A exibição na listagem 7 recupera a mensagem dos dados da exibição e processa a mensagem para o navegador.

**Listagem 7 – \Views\Product\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample7.aspx)]

Observe que o modo de exibição aproveita o método auxiliar de HTML Html.Encode() ao renderizar a mensagem. O auxiliar de HTML Html.Encode() codifica caracteres especiais, como &lt; e &gt; em caracteres que são seguros exibir em uma página da web. Sempre que você processar o conteúdo que um usuário envia para um site, você deve codificar o conteúdo para evitar ataques de injeção de JavaScript.

(Porque criamos a mensagem por conta própria no ProductController, não precisamos codificar a mensagem. No entanto, é um hábito de sempre chamar o método Html.Encode() ao exibir o conteúdo recuperado de exibir dados em uma exibição.)

Na listagem 7, tiramos proveito dos dados de exibição para passar uma mensagem de cadeia de caracteres simples de um controlador para um modo de exibição. Você também pode usar dados de exibição para passar outros tipos de dados, como uma coleção de registros de banco de dados, de um controlador para um modo de exibição. Por exemplo, se você quiser exibir o conteúdo da tabela de banco de dados de produtos em uma exibição, em seguida, você passaria a coleção de banco de dados registra no modo de exibição dados.

Você também tem a opção de passar dados de exibição fortemente tipada de um controlador para um modo de exibição. Podemos explorar esse tópico no tutorial **Noções básicas sobre fortemente tipados exibir dados e modos de exibição**.

## <a name="summary"></a>Resumo

Este tutorial forneceu uma breve introdução ao ASP.NET MVC exibições, exibir dados e auxiliares HTML. Na primeira seção, você aprendeu a adicionar novos modos de exibição ao seu projeto. Você aprendeu que você deve adicionar um modo de exibição para a pasta correta para chamá-lo em um controlador específico. Em seguida, discutimos o tópico de auxiliares de HTML. Você aprendeu como auxiliares HTML permitem gerar facilmente o conteúdo HTML padrão. Por fim, você aprendeu como tirar proveito dos dados de exibição para passar dados de um controlador para um modo de exibição.

> [!div class="step-by-step"]
> [Avançar](creating-custom-html-helpers-cs.md)
