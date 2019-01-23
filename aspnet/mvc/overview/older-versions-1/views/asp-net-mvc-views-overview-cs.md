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
<a name="aspnet-mvc-views-overview-c"></a><span data-ttu-id="42798-104">Exibições do ASP.NET MVC visão geral (c#)</span><span class="sxs-lookup"><span data-stu-id="42798-104">ASP.NET MVC Views Overview (C#)</span></span>
====================
<span data-ttu-id="42798-105">por [Stephen Walther](https://github.com/StephenWalther)</span><span class="sxs-lookup"><span data-stu-id="42798-105">by [Stephen Walther](https://github.com/StephenWalther)</span></span>

> <span data-ttu-id="42798-106">O que é um modo de exibição de MVC do ASP.NET e como ela difere de uma página HTML?</span><span class="sxs-lookup"><span data-stu-id="42798-106">What is an ASP.NET MVC View and how does it differ from a HTML page?</span></span> <span data-ttu-id="42798-107">Neste tutorial, Stephen Walther apresenta a modos de exibição e demonstra como você pode tirar proveito dos dados de exibição e auxiliares HTML dentro de um modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-107">In this tutorial, Stephen Walther introduces you to Views and demonstrates how you can take advantage of View Data and HTML Helpers within a View.</span></span>


<span data-ttu-id="42798-108">O objetivo deste tutorial é fornecer uma breve introdução ao ASP.NET MVC exibições, exibir dados e auxiliares HTML.</span><span class="sxs-lookup"><span data-stu-id="42798-108">The purpose of this tutorial is to provide you with a brief introduction to ASP.NET MVC views, view data, and HTML Helpers.</span></span> <span data-ttu-id="42798-109">No final deste tutorial, você deve compreender como criar novas exibições, passar dados de um controlador para um modo de exibição e usar os auxiliares HTML para gerar o conteúdo em uma exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-109">By the end of this tutorial, you should understand how to create new views, pass data from a controller to a view, and use HTML Helpers to generate content in a view.</span></span>

## <a name="understanding-views"></a><span data-ttu-id="42798-110">Noções básicas sobre exibições</span><span class="sxs-lookup"><span data-stu-id="42798-110">Understanding Views</span></span>

<span data-ttu-id="42798-111">Para o ASP.NET ou páginas ASP, ASP.NET MVC não inclui tudo o que corresponde diretamente a uma página.</span><span class="sxs-lookup"><span data-stu-id="42798-111">For ASP.NET or Active Server Pages, ASP.NET MVC does not include anything that directly corresponds to a page.</span></span> <span data-ttu-id="42798-112">Em um aplicativo ASP.NET MVC, não há uma página em disco que corresponde ao caminho na URL que você digita na barra de endereços do navegador.</span><span class="sxs-lookup"><span data-stu-id="42798-112">In an ASP.NET MVC application, there is not a page on disk that corresponds to the path in the URL that you type into the address bar of your browser.</span></span> <span data-ttu-id="42798-113">A coisa mais próxima a uma página em um aplicativo ASP.NET MVC é algo chamado de um *exibição*.</span><span class="sxs-lookup"><span data-stu-id="42798-113">The closest thing to a page in an ASP.NET MVC application is something called a *view*.</span></span>

<span data-ttu-id="42798-114">Em um aplicativo ASP.NET MVC, as solicitações recebidas do navegador são mapeadas para ações do controlador.</span><span class="sxs-lookup"><span data-stu-id="42798-114">In an ASP.NET MVC application, incoming browser requests are mapped to controller actions.</span></span> <span data-ttu-id="42798-115">Uma ação do controlador pode retornar uma exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-115">A controller action might return a view.</span></span> <span data-ttu-id="42798-116">No entanto, uma ação do controlador pode executar algum outro tipo de ação como redirecionando você para outra ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="42798-116">However, a controller action might perform some other type of action such as redirecting you to another controller action.</span></span>

<span data-ttu-id="42798-117">Listagem 1 contém um controlador simples chamado HomeController.</span><span class="sxs-lookup"><span data-stu-id="42798-117">Listing 1 contains a simple controller named the HomeController.</span></span> <span data-ttu-id="42798-118">O HomeController expõe duas ações do controlador chamadas Index () e Details().</span><span class="sxs-lookup"><span data-stu-id="42798-118">The HomeController exposes two controller actions named Index() and Details().</span></span>

<span data-ttu-id="42798-119">**Listagem 1 - HomeController.cs**</span><span class="sxs-lookup"><span data-stu-id="42798-119">**Listing 1 - HomeController.cs**</span></span>

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample1.cs)]

<span data-ttu-id="42798-120">Você pode invocar a primeira ação, a ação Index (), digitando a seguinte URL na barra de endereços do navegador:</span><span class="sxs-lookup"><span data-stu-id="42798-120">You can invoke the first action, the Index() action, by typing the following URL into your browser address bar:</span></span>

<span data-ttu-id="42798-121">/ Home/Index</span><span class="sxs-lookup"><span data-stu-id="42798-121">/Home/Index</span></span>

<span data-ttu-id="42798-122">Você pode invocar a segunda ação, a ação Details(), digitando este endereço no seu navegador:</span><span class="sxs-lookup"><span data-stu-id="42798-122">You can invoke the second action, the Details() action, by typing this address into your browser:</span></span>

<span data-ttu-id="42798-123">/ Home/detalhes</span><span class="sxs-lookup"><span data-stu-id="42798-123">/Home/Details</span></span>

<span data-ttu-id="42798-124">A ação Index () retorna uma exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-124">The Index() action returns a view.</span></span> <span data-ttu-id="42798-125">A maioria das ações que você cria retornará os modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-125">Most actions that you create will return views.</span></span> <span data-ttu-id="42798-126">No entanto, uma ação pode retornar outros tipos de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="42798-126">However, an action can return other types of action results.</span></span> <span data-ttu-id="42798-127">Por exemplo, a ação de Details() retorna um RedirectToActionResult que redireciona a solicitação de entrada para a ação Index ().</span><span class="sxs-lookup"><span data-stu-id="42798-127">For example, the Details() action returns a RedirectToActionResult that redirects incoming request to the Index() action.</span></span>

<span data-ttu-id="42798-128">A ação Index () contém a única linha de código a seguir:</span><span class="sxs-lookup"><span data-stu-id="42798-128">The Index() action contains the following single line of code:</span></span>

<span data-ttu-id="42798-129">View();</span><span class="sxs-lookup"><span data-stu-id="42798-129">View();</span></span>

<span data-ttu-id="42798-130">Esta linha de código retorna uma exibição que deve estar localizada no seguinte caminho em seu servidor web:</span><span class="sxs-lookup"><span data-stu-id="42798-130">This line of code returns a view that must be located at the following path on your web server:</span></span>

<span data-ttu-id="42798-131">\Views\Home\Index.aspx</span><span class="sxs-lookup"><span data-stu-id="42798-131">\Views\Home\Index.aspx</span></span>

<span data-ttu-id="42798-132">O caminho para o modo de exibição é inferido do nome do controlador e o nome da ação de controlador.</span><span class="sxs-lookup"><span data-stu-id="42798-132">The path to the view is inferred from the name of the controller and the name of the controller action.</span></span>

<span data-ttu-id="42798-133">Se você preferir, você pode ser explícito sobre o modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-133">If you prefer, you can be explicit about the view.</span></span> <span data-ttu-id="42798-134">A linha de código a seguir retorna um modo de exibição chamado Fred:</span><span class="sxs-lookup"><span data-stu-id="42798-134">The following line of code returns a view named Fred :</span></span>

<span data-ttu-id="42798-135">Modo de exibição (Vinicius);</span><span class="sxs-lookup"><span data-stu-id="42798-135">View( Fred );</span></span>

<span data-ttu-id="42798-136">Quando esta linha de código é executada, uma exibição é retornada do caminho a seguir:</span><span class="sxs-lookup"><span data-stu-id="42798-136">When this line of code is executed, a view is returned from the following path:</span></span>

<span data-ttu-id="42798-137">\Views\Home\Fred.aspx</span><span class="sxs-lookup"><span data-stu-id="42798-137">\Views\Home\Fred.aspx</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="42798-138">Se você planeja criar testes de unidade para seu aplicativo ASP.NET MVC é uma boa ideia para ser explícito sobre nomes de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-138">If you plan to create unit tests for your ASP.NET MVC application then it is a good idea to be explicit about view names.</span></span> <span data-ttu-id="42798-139">Dessa forma, você pode criar um teste de unidade para verificar se o modo de exibição esperado foi retornado por uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="42798-139">That way, you can create a unit test to verify that the expected view was returned by a controller action.</span></span>


## <a name="adding-content-to-a-view"></a><span data-ttu-id="42798-140">Adicionando conteúdo a um modo de exibição</span><span class="sxs-lookup"><span data-stu-id="42798-140">Adding Content to a View</span></span>

<span data-ttu-id="42798-141">Um modo de exibição é um padrão de (documento HTML que pode conter scripts X).</span><span class="sxs-lookup"><span data-stu-id="42798-141">A view is a standard (X)HTML document that can contain scripts.</span></span> <span data-ttu-id="42798-142">Você pode usar scripts para adicionar conteúdo dinâmico a um modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-142">You use scripts to add dynamic content to a view.</span></span>

<span data-ttu-id="42798-143">Por exemplo, o modo de exibição na listagem 2 exibe a data e hora atuais.</span><span class="sxs-lookup"><span data-stu-id="42798-143">For example, the view in Listing 2 displays the current date and time.</span></span>

<span data-ttu-id="42798-144">**Listing 2 - \Views\Home\Index.aspx**</span><span class="sxs-lookup"><span data-stu-id="42798-144">**Listing 2 - \Views\Home\Index.aspx**</span></span>

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample2.aspx)]

<span data-ttu-id="42798-145">Observe que o corpo da página HTML na listagem 2 contém o script a seguir:</span><span class="sxs-lookup"><span data-stu-id="42798-145">Notice that the body of the HTML page in Listing 2 contains the following script:</span></span>

<span data-ttu-id="42798-146">&lt;% Response.Write(DateTime.Now);%&gt;</span><span class="sxs-lookup"><span data-stu-id="42798-146">&lt;% Response.Write(DateTime.Now);%&gt;</span></span>

<span data-ttu-id="42798-147">Você usa os delimitadores de script &lt;% e %&gt; para marcar o início e término de um script.</span><span class="sxs-lookup"><span data-stu-id="42798-147">You use the script delimiters &lt;% and %&gt; to mark the beginning and end of a script.</span></span> <span data-ttu-id="42798-148">Esse script é escrito em c#.</span><span class="sxs-lookup"><span data-stu-id="42798-148">This script is written in C#.</span></span> <span data-ttu-id="42798-149">Ele exibe a data e hora atuais, chamando o método Response para processar o conteúdo para o navegador.</span><span class="sxs-lookup"><span data-stu-id="42798-149">It displays the current date and time by calling the Response.Write() method to render content to the browser.</span></span> <span data-ttu-id="42798-150">Os delimitadores de script &lt;% e %&gt; pode ser usado para executar uma ou mais instruções.</span><span class="sxs-lookup"><span data-stu-id="42798-150">The script delimiters &lt;% and %&gt; can be used to execute one or more statements.</span></span>

<span data-ttu-id="42798-151">Uma vez que você chama Response com tanta frequência, Microsoft fornece um atalho para chamar o método Response.</span><span class="sxs-lookup"><span data-stu-id="42798-151">Since you call Response.Write() so often, Microsoft provides you with a shortcut for calling the Response.Write() method.</span></span> <span data-ttu-id="42798-152">O modo de exibição na listagem 3 usa os delimitadores &lt;% = e %&gt; como um atalho para a chamada de Response.</span><span class="sxs-lookup"><span data-stu-id="42798-152">The view in Listing 3 uses the delimiters &lt;%= and %&gt; as a shortcut for calling Response.Write().</span></span>

<span data-ttu-id="42798-153">**Listagem 3 - Views\Home\Index2.aspx**</span><span class="sxs-lookup"><span data-stu-id="42798-153">**Listing 3 - Views\Home\Index2.aspx**</span></span>

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample3.aspx)]

<span data-ttu-id="42798-154">Você pode usar qualquer linguagem .NET para gerar o conteúdo dinâmico em uma exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-154">You can use any .NET language to generate dynamic content in a view.</span></span> <span data-ttu-id="42798-155">Normalmente, você usará o Visual Basic .NET ou C# para gravar seus controladores e modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-155">Normally, you'll use either Visual Basic .NET or C# to write your controllers and views.</span></span>

## <a name="using-html-helpers-to-generate-view-content"></a><span data-ttu-id="42798-156">Usando os auxiliares HTML para gerar o conteúdo da exibição</span><span class="sxs-lookup"><span data-stu-id="42798-156">Using HTML Helpers to Generate View Content</span></span>

<span data-ttu-id="42798-157">Para tornar mais fácil de adicionar conteúdo a um modo de exibição, você pode tirar proveito de algo chamado de um *auxiliar HTML*.</span><span class="sxs-lookup"><span data-stu-id="42798-157">To make it easier to add content to a view, you can take advantage of something called an *HTML Helper*.</span></span> <span data-ttu-id="42798-158">Um auxiliar de HTML, normalmente, é um método que gera uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="42798-158">An HTML Helper, typically, is a method that generates a string.</span></span> <span data-ttu-id="42798-159">Você pode usar os auxiliares HTML para gerar elementos HTML padrão, como caixas de texto, links, listas suspensas e caixas de listagem.</span><span class="sxs-lookup"><span data-stu-id="42798-159">You can use HTML Helpers to generate standard HTML elements such as textboxes, links, dropdown lists, and list boxes.</span></span>

<span data-ttu-id="42798-160">Por exemplo, a exibição na listagem 4 tira proveito dos três auxiliares de HTML – auxiliares BeginForm(), TextBox() e Password() – para gerar um logon do formam (veja a Figura 1).</span><span class="sxs-lookup"><span data-stu-id="42798-160">For example, the view in Listing 4 takes advantage of three HTML Helpers -- the BeginForm(), the TextBox() and Password() helpers -- to generate a Login form (see Figure 1).</span></span>

<span data-ttu-id="42798-161">**Listing 4 -- \Views\Home\Login.aspx**</span><span class="sxs-lookup"><span data-stu-id="42798-161">**Listing 4 -- \Views\Home\Login.aspx**</span></span>

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample4.aspx)]


<span data-ttu-id="42798-162">[![A caixa de diálogo Novo projeto](asp-net-mvc-views-overview-cs/_static/image1.jpg)](asp-net-mvc-views-overview-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="42798-162">[![The New Project dialog box](asp-net-mvc-views-overview-cs/_static/image1.jpg)](asp-net-mvc-views-overview-cs/_static/image1.png)</span></span>

<span data-ttu-id="42798-163">**Figura 01**: Um formulário de logon padrão ([clique para exibir a imagem em tamanho normal](asp-net-mvc-views-overview-cs/_static/image2.png))</span><span class="sxs-lookup"><span data-stu-id="42798-163">**Figure 01**: A standard Login form ([Click to view full-size image](asp-net-mvc-views-overview-cs/_static/image2.png))</span></span>


<span data-ttu-id="42798-164">Todos os métodos auxiliares HTML são chamados na propriedade Html da exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-164">All of the HTML Helpers methods are called on the Html property of the view.</span></span> <span data-ttu-id="42798-165">Por exemplo, você deve processar uma caixa de texto chamando o método Html.TextBox().</span><span class="sxs-lookup"><span data-stu-id="42798-165">For example, you render a TextBox by calling the Html.TextBox() method.</span></span>

<span data-ttu-id="42798-166">Observe que você usa os delimitadores de script &lt;% = e %&gt; ao chamar o Html.TextBox() e Html.Password() auxiliares.</span><span class="sxs-lookup"><span data-stu-id="42798-166">Notice that you use the script delimiters &lt;%= and %&gt; when calling both the Html.TextBox() and Html.Password() helpers.</span></span> <span data-ttu-id="42798-167">Esses auxiliares simplesmente retornam uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="42798-167">These helpers simply return a string.</span></span> <span data-ttu-id="42798-168">Você precisa chamar Response para renderizar a cadeia de caracteres para o navegador.</span><span class="sxs-lookup"><span data-stu-id="42798-168">You need to call Response.Write() in order to render the string to the browser.</span></span>

<span data-ttu-id="42798-169">Usar métodos auxiliares HTML é opcional.</span><span class="sxs-lookup"><span data-stu-id="42798-169">Using HTML Helper methods is optional.</span></span> <span data-ttu-id="42798-170">Eles tornam sua vida mais fácil, reduzindo a quantidade de HTML e script que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="42798-170">They make your life easier by reducing the amount of HTML and script that you need to write.</span></span> <span data-ttu-id="42798-171">O modo de exibição na listagem 5 apresenta o mesmo formato exato que o modo de exibição na listagem 4 sem uso de auxiliares de HTML.</span><span class="sxs-lookup"><span data-stu-id="42798-171">The view in Listing 5 renders the exact same form as the view in Listing 4 without using HTML Helpers.</span></span>

<span data-ttu-id="42798-172">**Listing 5 -- \Views\Home\Login.aspx**</span><span class="sxs-lookup"><span data-stu-id="42798-172">**Listing 5 -- \Views\Home\Login.aspx**</span></span>

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample5.aspx)]

<span data-ttu-id="42798-173">Você também tem a opção de criar seu próprio auxiliares HTML.</span><span class="sxs-lookup"><span data-stu-id="42798-173">You also have the option of creating your own HTML Helpers.</span></span> <span data-ttu-id="42798-174">Por exemplo, você pode criar um método auxiliar GridView() que exibe um conjunto de registros do banco de dados em uma tabela HTML automaticamente.</span><span class="sxs-lookup"><span data-stu-id="42798-174">For example, you can create a GridView() helper method that displays a set of database records in an HTML table automatically.</span></span> <span data-ttu-id="42798-175">Podemos explorar esse tópico no tutorial **criação de auxiliares de HTML personalizado**.</span><span class="sxs-lookup"><span data-stu-id="42798-175">We explore this topic in the tutorial **Creating Custom HTML Helpers**.</span></span>

## <a name="using-view-data-to-pass-data-to-a-view"></a><span data-ttu-id="42798-176">Usando dados de exibição para passar dados para um modo de exibição</span><span class="sxs-lookup"><span data-stu-id="42798-176">Using View Data to Pass Data to a View</span></span>

<span data-ttu-id="42798-177">Você pode usar dados de exibição para passar dados de um controlador para um modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-177">You use view data to pass data from a controller to a view.</span></span> <span data-ttu-id="42798-178">Pense em dados de exibição, como um pacote que você envia por meio de email.</span><span class="sxs-lookup"><span data-stu-id="42798-178">Think of view data like a package that you send through the mail.</span></span> <span data-ttu-id="42798-179">Todos os dados passados de um controlador para um modo de exibição devem ser enviados usando este pacote.</span><span class="sxs-lookup"><span data-stu-id="42798-179">All data passed from a controller to a view must be sent using this package.</span></span> <span data-ttu-id="42798-180">Por exemplo, o controlador na listagem 6 adiciona uma mensagem para exibir os dados.</span><span class="sxs-lookup"><span data-stu-id="42798-180">For example, the controller in Listing 6 adds a message to view data.</span></span>

<span data-ttu-id="42798-181">**Listagem 6 - ProductController.cs**</span><span class="sxs-lookup"><span data-stu-id="42798-181">**Listing 6 - ProductController.cs**</span></span>

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample6.cs)]

<span data-ttu-id="42798-182">O controlador de propriedade ViewData representa uma coleção de pares nome-valor.</span><span class="sxs-lookup"><span data-stu-id="42798-182">The controller ViewData property represents a collection of name and value pairs.</span></span> <span data-ttu-id="42798-183">Na listagem 6, o método Index () adiciona um item para a coleta de dados de exibição denominada mensagem com o valor Olá, mundo!.</span><span class="sxs-lookup"><span data-stu-id="42798-183">In Listing 6, the Index() method adds an item to the view data collection named message with the value Hello World!.</span></span> <span data-ttu-id="42798-184">Quando o modo de exibição é retornado pelo método Index (), os dados de exibição são passados para o modo de exibição automaticamente.</span><span class="sxs-lookup"><span data-stu-id="42798-184">When the view is returned by the Index() method, the view data is passed to the view automatically.</span></span>

<span data-ttu-id="42798-185">A exibição na listagem 7 recupera a mensagem dos dados da exibição e processa a mensagem para o navegador.</span><span class="sxs-lookup"><span data-stu-id="42798-185">The view in Listing 7 retrieves the message from the view data and renders the message to the browser.</span></span>

<span data-ttu-id="42798-186">**Listagem 7 – \Views\Product\Index.aspx**</span><span class="sxs-lookup"><span data-stu-id="42798-186">**Listing 7 -- \Views\Product\Index.aspx**</span></span>

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample7.aspx)]

<span data-ttu-id="42798-187">Observe que o modo de exibição aproveita o método auxiliar de HTML Html.Encode() ao renderizar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="42798-187">Notice that the view takes advantage of the Html.Encode() HTML Helper method when rendering the message.</span></span> <span data-ttu-id="42798-188">O auxiliar de HTML Html.Encode() codifica caracteres especiais, como &lt; e &gt; em caracteres que são seguros exibir em uma página da web.</span><span class="sxs-lookup"><span data-stu-id="42798-188">The Html.Encode() HTML Helper encodes special characters such as &lt; and &gt; into characters that are safe to display in a web page.</span></span> <span data-ttu-id="42798-189">Sempre que você processar o conteúdo que um usuário envia para um site, você deve codificar o conteúdo para evitar ataques de injeção de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="42798-189">Whenever you render content that a user submits to a website, you should encode the content to prevent JavaScript injection attacks.</span></span>

<span data-ttu-id="42798-190">(Porque criamos a mensagem por conta própria no ProductController, não precisamos codificar a mensagem.</span><span class="sxs-lookup"><span data-stu-id="42798-190">(Because we created the message ourselves in the ProductController, we don't really need to encode the message.</span></span> <span data-ttu-id="42798-191">No entanto, é um hábito de sempre chamar o método Html.Encode() ao exibir o conteúdo recuperado de exibir dados em uma exibição.)</span><span class="sxs-lookup"><span data-stu-id="42798-191">However, it is a good habit to always call the Html.Encode() method when displaying content retrieved from view data within a view.)</span></span>

<span data-ttu-id="42798-192">Na listagem 7, tiramos proveito dos dados de exibição para passar uma mensagem de cadeia de caracteres simples de um controlador para um modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-192">In Listing 7, we took advantage of view data to pass a simple string message from a controller to a view.</span></span> <span data-ttu-id="42798-193">Você também pode usar dados de exibição para passar outros tipos de dados, como uma coleção de registros de banco de dados, de um controlador para um modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-193">You also can use view data to pass other types of data, such as a collection of database records, from a controller to a view.</span></span> <span data-ttu-id="42798-194">Por exemplo, se você quiser exibir o conteúdo da tabela de banco de dados de produtos em uma exibição, em seguida, você passaria a coleção de banco de dados registra no modo de exibição dados.</span><span class="sxs-lookup"><span data-stu-id="42798-194">For example, if you want to display the contents of the Products database table in a view, then you would pass the collection of database records in view data.</span></span>

<span data-ttu-id="42798-195">Você também tem a opção de passar dados de exibição fortemente tipada de um controlador para um modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-195">You also have the option of passing strongly typed view data from a controller to a view.</span></span> <span data-ttu-id="42798-196">Podemos explorar esse tópico no tutorial **Noções básicas sobre fortemente tipados exibir dados e modos de exibição**.</span><span class="sxs-lookup"><span data-stu-id="42798-196">We explore this topic in the tutorial **Understanding Strongly Typed View Data and Views**.</span></span>

## <a name="summary"></a><span data-ttu-id="42798-197">Resumo</span><span class="sxs-lookup"><span data-stu-id="42798-197">Summary</span></span>

<span data-ttu-id="42798-198">Este tutorial forneceu uma breve introdução ao ASP.NET MVC exibições, exibir dados e auxiliares HTML.</span><span class="sxs-lookup"><span data-stu-id="42798-198">This tutorial provided a brief introduction to ASP.NET MVC views, view data, and HTML Helpers.</span></span> <span data-ttu-id="42798-199">Na primeira seção, você aprendeu a adicionar novos modos de exibição ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="42798-199">In the first section, you learned how to add new views to your project.</span></span> <span data-ttu-id="42798-200">Você aprendeu que você deve adicionar um modo de exibição para a pasta correta para chamá-lo em um controlador específico.</span><span class="sxs-lookup"><span data-stu-id="42798-200">You learned that you must add a view to the right folder in order to call it from a particular controller.</span></span> <span data-ttu-id="42798-201">Em seguida, discutimos o tópico de auxiliares de HTML.</span><span class="sxs-lookup"><span data-stu-id="42798-201">Next, we discussed the topic of HTML Helpers.</span></span> <span data-ttu-id="42798-202">Você aprendeu como auxiliares HTML permitem gerar facilmente o conteúdo HTML padrão.</span><span class="sxs-lookup"><span data-stu-id="42798-202">You learned how HTML Helpers enable you to easily generate standard HTML content.</span></span> <span data-ttu-id="42798-203">Por fim, você aprendeu como tirar proveito dos dados de exibição para passar dados de um controlador para um modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="42798-203">Finally, you learned how to take advantage of view data to pass data from a controller to a view.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="42798-204">Avançar</span><span class="sxs-lookup"><span data-stu-id="42798-204">Next</span></span>](creating-custom-html-helpers-cs.md)
