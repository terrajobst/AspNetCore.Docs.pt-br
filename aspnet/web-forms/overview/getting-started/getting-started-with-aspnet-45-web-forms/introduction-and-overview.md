---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
title: Introdução ao ASP.NET 4.7 do Web Forms e Visual Studio 2017 | Microsoft Docs
author: Erikre
description: Esta série de tutoriais passo a passo lhe ensinará os conceitos básicos da criação de um aplicativo de Web Forms do ASP.NET usando o Microsoft Visual Studio e o ASP.NET 4.7
ms.author: riande
ms.date: 01/09/2019
ms.assetid: 9b96eaa1-8ef0-4338-a2e8-e0f970bfaf68
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
msc.type: authoredcontent
ms.openlocfilehash: fb41ce72e9454d8d670a0b95234d2bc3f909f0ee
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341543"
---
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2017"></a><span data-ttu-id="d2afe-103">Introdução ao ASP.NET 4.5 do Web Forms e Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="d2afe-103">Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2017</span></span>
====================

<span data-ttu-id="d2afe-104">[Baixe o projeto de exemplo do Wingtip Toys (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ou [Baixe o livro eletrônico (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)</span><span class="sxs-lookup"><span data-stu-id="d2afe-104">[Download Wingtip Toys Sample Project (C#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) or [Download E-book (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)</span></span>

<span data-ttu-id="d2afe-105">Esta série de tutoriais mostra como criar um aplicativo de Web Forms do ASP.NET com o ASP.NET 4.5 e do Microsoft Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="d2afe-105">This tutorial series shows you how to build an ASP.NET Web Forms application with ASP.NET 4.5 and Microsoft Visual Studio 2017.</span></span> 

## <a name="introduction"></a><span data-ttu-id="d2afe-106">Introdução</span><span class="sxs-lookup"><span data-stu-id="d2afe-106">Introduction</span></span>

<span data-ttu-id="d2afe-107">Esta série de tutoriais orienta a criação de um aplicativo de Web Forms do ASP.NET usando o Visual Studio 2017 e o ASP.NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="d2afe-107">This tutorial series guides you through creating an ASP.NET Web Forms application using Visual Studio 2017 and ASP.NET 4.5.</span></span> <span data-ttu-id="d2afe-108">Você criará um aplicativo chamado **Wingtip Toys** – um site da web vitrine simplificada vender itens online.</span><span class="sxs-lookup"><span data-stu-id="d2afe-108">You'll create an application named **Wingtip Toys** - a simplified storefront web site selling items online.</span></span> <span data-ttu-id="d2afe-109">Durante a série, novos recursos do ASP.NET 4.5 são realçados.</span><span class="sxs-lookup"><span data-stu-id="d2afe-109">During the series, new ASP.NET 4.5 features are highlighted.</span></span>

### <a name="target-audience"></a><span data-ttu-id="d2afe-110">Público-alvo</span><span class="sxs-lookup"><span data-stu-id="d2afe-110">Target audience</span></span>

<span data-ttu-id="d2afe-111">Desenvolvedores novos no Web Forms do ASP.NET são o público-alvo desta série de tutoriais.</span><span class="sxs-lookup"><span data-stu-id="d2afe-111">Developers new to ASP.NET Web Forms are the target audience for this tutorial series.</span></span>

<span data-ttu-id="d2afe-112">Você deve ter algum conhecimento nas seguintes áreas:</span><span class="sxs-lookup"><span data-stu-id="d2afe-112">You should have some knowledge in the following areas:</span></span>

- <span data-ttu-id="d2afe-113">Programação orientada a objeto (OOP) e linguagens</span><span class="sxs-lookup"><span data-stu-id="d2afe-113">Object-oriented programming (OOP) and languages</span></span>
- <span data-ttu-id="d2afe-114">Desenvolvimento da Web (HTML, CSS e JavaScript)</span><span class="sxs-lookup"><span data-stu-id="d2afe-114">Web development (HTML, CSS, JavaScript)</span></span>
- <span data-ttu-id="d2afe-115">bancos de dados relacionais</span><span class="sxs-lookup"><span data-stu-id="d2afe-115">Relational databases</span></span>
- <span data-ttu-id="d2afe-116">Arquitetura de N camadas</span><span class="sxs-lookup"><span data-stu-id="d2afe-116">N-tier architecture</span></span>

<span data-ttu-id="d2afe-117">Para examinar essas áreas, considere a estudar o conteúdo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d2afe-117">To review these areas, consider studying the following content:</span></span>

- [<span data-ttu-id="d2afe-118">Introdução ao Visual c#</span><span class="sxs-lookup"><span data-stu-id="d2afe-118">Getting Started with Visual C#</span></span>](https://msdn.microsoft.com/library/a72418yk.aspx)
- <span data-ttu-id="d2afe-119">[Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)</span><span class="sxs-lookup"><span data-stu-id="d2afe-119">[Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)</span></span>
- [<span data-ttu-id="d2afe-120">Banco de dados relacional</span><span class="sxs-lookup"><span data-stu-id="d2afe-120">Relational database</span></span>](http://en.wikipedia.org/wiki/Relational_database)
- [<span data-ttu-id="d2afe-121">Arquitetura de várias camadas</span><span class="sxs-lookup"><span data-stu-id="d2afe-121">Multitier architecture</span></span>](http://en.wikipedia.org/wiki/Multitier_architecture)

### <a name="application-features"></a><span data-ttu-id="d2afe-122">Recursos do aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2afe-122">Application features</span></span>

<span data-ttu-id="d2afe-123">Os recursos do Web Form do ASP.NET apresentados nesta série incluem:</span><span class="sxs-lookup"><span data-stu-id="d2afe-123">The ASP.NET Web Form features presented in this series include:</span></span>

- <span data-ttu-id="d2afe-124">O projeto de aplicativo Web (não o projeto de Site da Web)</span><span class="sxs-lookup"><span data-stu-id="d2afe-124">The Web Application Project (not Web Site Project)</span></span>
- <span data-ttu-id="d2afe-125">Web Forms</span><span class="sxs-lookup"><span data-stu-id="d2afe-125">Web Forms</span></span>
- <span data-ttu-id="d2afe-126">Páginas mestras, configuração</span><span class="sxs-lookup"><span data-stu-id="d2afe-126">Master Pages, Configuration</span></span>
- <span data-ttu-id="d2afe-127">Bootstrap</span><span class="sxs-lookup"><span data-stu-id="d2afe-127">Bootstrap</span></span>
- <span data-ttu-id="d2afe-128">Entity Framework Code First, LocalDB</span><span class="sxs-lookup"><span data-stu-id="d2afe-128">Entity Framework Code First, LocalDB</span></span>
- <span data-ttu-id="d2afe-129">Validação de solicitação</span><span class="sxs-lookup"><span data-stu-id="d2afe-129">Request Validation</span></span>
- <span data-ttu-id="d2afe-130">Controles de dados fortemente tipados</span><span class="sxs-lookup"><span data-stu-id="d2afe-130">Strongly-typed Data Controls</span></span>
- <span data-ttu-id="d2afe-131">Model binding</span><span class="sxs-lookup"><span data-stu-id="d2afe-131">Model Binding</span></span>
- <span data-ttu-id="d2afe-132">Anotações de dados</span><span class="sxs-lookup"><span data-stu-id="d2afe-132">Data Annotations</span></span>
- <span data-ttu-id="d2afe-133">Provedores de valor</span><span class="sxs-lookup"><span data-stu-id="d2afe-133">Value Providers</span></span>
- <span data-ttu-id="d2afe-134">SSL e OAuth</span><span class="sxs-lookup"><span data-stu-id="d2afe-134">SSL and OAuth</span></span>
- <span data-ttu-id="d2afe-135">O ASP.NET Identity, configuração e autorização</span><span class="sxs-lookup"><span data-stu-id="d2afe-135">ASP.NET Identity, Configuration, and Authorization</span></span>
- <span data-ttu-id="d2afe-136">Validação não invasiva</span><span class="sxs-lookup"><span data-stu-id="d2afe-136">Unobtrusive Validation</span></span>
- <span data-ttu-id="d2afe-137">Roteamento</span><span class="sxs-lookup"><span data-stu-id="d2afe-137">Routing</span></span>
- <span data-ttu-id="d2afe-138">Tratamento de erros do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d2afe-138">ASP.NET Error Handling</span></span>

### <a name="application-scenarios-and-tasks"></a><span data-ttu-id="d2afe-139">Tarefas e cenários de aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2afe-139">Application scenarios and tasks</span></span>

<span data-ttu-id="d2afe-140">Tarefas de série de tutoriais incluem:</span><span class="sxs-lookup"><span data-stu-id="d2afe-140">Tutorial series tasks include:</span></span>

- <span data-ttu-id="d2afe-141">Criando, revisando e execução de um novo projeto</span><span class="sxs-lookup"><span data-stu-id="d2afe-141">Creating, reviewing, and running a new project</span></span>
- <span data-ttu-id="d2afe-142">Criando uma estrutura de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d2afe-142">Creating a database structure</span></span>
- <span data-ttu-id="d2afe-143">Inicializando e a propagação de um banco de dados</span><span class="sxs-lookup"><span data-stu-id="d2afe-143">Initializing and seeding a database</span></span>
- <span data-ttu-id="d2afe-144">Personalizando a interface do usuário com estilos, gráficos e uma página mestra</span><span class="sxs-lookup"><span data-stu-id="d2afe-144">Customizing the UI with styles, graphics, and a master page</span></span>
- <span data-ttu-id="d2afe-145">Adicionar páginas e navegação</span><span class="sxs-lookup"><span data-stu-id="d2afe-145">Adding pages and navigation</span></span>
- <span data-ttu-id="d2afe-146">Exibindo detalhes de menu e os dados de produto</span><span class="sxs-lookup"><span data-stu-id="d2afe-146">Displaying menu details and product data</span></span>
- <span data-ttu-id="d2afe-147">Criando um carrinho de compras</span><span class="sxs-lookup"><span data-stu-id="d2afe-147">Creating a shopping cart</span></span>
- <span data-ttu-id="d2afe-148">Suporte a adição de SSL e OAuth</span><span class="sxs-lookup"><span data-stu-id="d2afe-148">Adding SSL and OAuth support</span></span>
- <span data-ttu-id="d2afe-149">Adicionando um método de pagamento</span><span class="sxs-lookup"><span data-stu-id="d2afe-149">Adding a payment method</span></span>
- <span data-ttu-id="d2afe-150">Incluindo uma função de administrador e um usuário para o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d2afe-150">Including an administrator role and a user to the application</span></span>
- <span data-ttu-id="d2afe-151">Restringir o acesso a páginas específicas e pasta</span><span class="sxs-lookup"><span data-stu-id="d2afe-151">Restricting access to specific pages and folder</span></span>
- <span data-ttu-id="d2afe-152">Carregar um arquivo para o aplicativo web</span><span class="sxs-lookup"><span data-stu-id="d2afe-152">Uploading a file to the web application</span></span>
- <span data-ttu-id="d2afe-153">Implementar validação de entrada</span><span class="sxs-lookup"><span data-stu-id="d2afe-153">Implementing input validation</span></span>
- <span data-ttu-id="d2afe-154">Registrando as rotas para o aplicativo web</span><span class="sxs-lookup"><span data-stu-id="d2afe-154">Registering routes for the web application</span></span>
- <span data-ttu-id="d2afe-155">Implementando o tratamento de erros e log de erros</span><span class="sxs-lookup"><span data-stu-id="d2afe-155">Implementing error handling and error logging</span></span>

## <a name="overview"></a><span data-ttu-id="d2afe-156">Visão geral</span><span class="sxs-lookup"><span data-stu-id="d2afe-156">Overview</span></span>

<span data-ttu-id="d2afe-157">Esta série de tutoriais é pretendido para alguém que esteja familiarizado com conceitos de programação, mas é novo no Web Forms do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d2afe-157">This tutorial series is intended for someone familiar with programming concepts, but new to ASP.NET Web Forms.</span></span> <span data-ttu-id="d2afe-158">Se você já estiver familiarizado com Web Forms do ASP.NET, esta série pode ainda ajudá-lo a aprender sobre novos recursos do ASP.NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="d2afe-158">If you're already familiar with ASP.NET Web Forms, this series can still help you learn about new ASP.NET 4.5 features.</span></span> <span data-ttu-id="d2afe-159">Para quem não está familiarizado com conceitos de programação e Web Forms do ASP.NET, consulte os tutoriais de formulários da Web adicionais fornecidos na [guia de Introdução](../../../index.md) seção no site da Web do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d2afe-159">For readers unfamiliar with programming concepts and ASP.NET Web Forms, see the additional Web Forms tutorials provided in the [Getting Started](../../../index.md) section on the ASP.NET Web site.</span></span>

<span data-ttu-id="d2afe-160">O ASP.NET 4.5 fornecido nessa série de tutoriais inclui os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="d2afe-160">The ASP.NET 4.5 provided in this tutorial series includes the following features:</span></span>

- <span data-ttu-id="d2afe-161">Uma interface do usuário simple para a criação de projetos que oferece [suporte para muitas estruturas ASP.NET](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (Web Forms, MVC e API da Web).</span><span class="sxs-lookup"><span data-stu-id="d2afe-161">A simple UI for creating projects that offers [support for many ASP.NET frameworks](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (Web Forms, MVC, and Web API).</span></span>
- <span data-ttu-id="d2afe-162">[O Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), um layout, temas e estrutura de design responsivo.</span><span class="sxs-lookup"><span data-stu-id="d2afe-162">[Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), a layout, theming, and responsive design framework.</span></span>
- <span data-ttu-id="d2afe-163">[O ASP.NET Identity](../../../../identity/index.md), um novo sistema de associação do ASP.NET que funciona da mesma em todas as estruturas do ASP.NET e funciona com o software que não seja o IIS de hospedagem na web.</span><span class="sxs-lookup"><span data-stu-id="d2afe-163">[ASP.NET Identity](../../../../identity/index.md), a new ASP.NET membership system that works the same in all ASP.NET frameworks and works with web hosting software other than IIS.</span></span>
- [<span data-ttu-id="d2afe-164">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="d2afe-164">Entity Framework 6</span></span>](https://msdn.microsoft.com/data/ef.aspx)

  <span data-ttu-id="d2afe-165">Uma atualização para o Entity Framework, permitindo que você:</span><span class="sxs-lookup"><span data-stu-id="d2afe-165">An update to the Entity Framework enabling you to:</span></span>
  - <span data-ttu-id="d2afe-166">Recuperar e manipular dados como objetos fortemente tipados</span><span class="sxs-lookup"><span data-stu-id="d2afe-166">Retrieve and manipulate data as strongly-typed objects</span></span>
  - <span data-ttu-id="d2afe-167">Acessar dados de forma assíncrona</span><span class="sxs-lookup"><span data-stu-id="d2afe-167">Access data asynchronously</span></span>
  - <span data-ttu-id="d2afe-168">Tratar falhas transitórias de conexão</span><span class="sxs-lookup"><span data-stu-id="d2afe-168">Handle transient connection faults</span></span>
  - <span data-ttu-id="d2afe-169">Instruções SQL de log</span><span class="sxs-lookup"><span data-stu-id="d2afe-169">Log SQL statements</span></span>

<span data-ttu-id="d2afe-170">Para obter uma lista completa de recurso ASP.NET 4.5, consulte [ASP.NET e Web Tools para notas de versão do Visual Studio 2013](../../../../visual-studio/overview/2013/release-notes.md).</span><span class="sxs-lookup"><span data-stu-id="d2afe-170">For a complete ASP.NET 4.5 feature list, see [ASP.NET and Web Tools for Visual Studio 2013 Release Notes](../../../../visual-studio/overview/2013/release-notes.md).</span></span>

### <a name="the-wingtip-toys-sample-application"></a><span data-ttu-id="d2afe-171">O aplicativo de exemplo Wingtip Toys</span><span class="sxs-lookup"><span data-stu-id="d2afe-171">The Wingtip Toys sample application</span></span>

<span data-ttu-id="d2afe-172">São as seguintes capturas de tela do aplicativo Web Forms do ASP.NET que você criar nessa série de tutoriais.</span><span class="sxs-lookup"><span data-stu-id="d2afe-172">The following screenshots are from the ASP.NET Web Forms application that you create in this tutorial series.</span></span> <span data-ttu-id="d2afe-173">Quando você executa o aplicativo no Visual Studio, a seguinte página inicial da web é exibida.</span><span class="sxs-lookup"><span data-stu-id="d2afe-173">When you run the application in Visual Studio, the following web Home page appears.</span></span>

![A Wingtip Toys - página padrão](introduction-and-overview/_static/image1.png)

<span data-ttu-id="d2afe-175">Você pode se registrar como um novo usuário ou entrar como um usuário existente.</span><span class="sxs-lookup"><span data-stu-id="d2afe-175">You can register as a new user, or sign in as an existing user.</span></span> <span data-ttu-id="d2afe-176">Painel de navegação superior contém links para as categorias de produto e seus produtos do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d2afe-176">The top navigation has links to product categories and their products from the database.</span></span>

<span data-ttu-id="d2afe-177">Se você selecionar **produtos**, todos os produtos disponíveis são exibidos.</span><span class="sxs-lookup"><span data-stu-id="d2afe-177">If you select **Products**, all available products are displayed.</span></span> 

![A Wingtip Toys - produtos](introduction-and-overview/_static/image2.png)

<span data-ttu-id="d2afe-179">Se você selecionar um produto específico, detalhes do produto são exibidos.</span><span class="sxs-lookup"><span data-stu-id="d2afe-179">If you select a specific product, product details are displayed.</span></span>


![A Wingtip Toys - detalhes do produto](introduction-and-overview/_static/image3.png)

<span data-ttu-id="d2afe-181">Como um usuário, você pode registrar e entrar com a funcionalidade de padrão de modelo de formulários da Web.</span><span class="sxs-lookup"><span data-stu-id="d2afe-181">As a user, you can register and sign in with Web Forms template default functionality.</span></span> <span data-ttu-id="d2afe-182">Este tutorial também explica como a entrar usando uma conta existente do Gmail.</span><span class="sxs-lookup"><span data-stu-id="d2afe-182">This tutorial also explains how to sign in using an existing Gmail account.</span></span> <span data-ttu-id="d2afe-183">Além disso, você pode entrar como administrador para adicionar e remover os produtos de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d2afe-183">Additionally, you can sign in as the administrator to add and remove products from the database.</span></span>

![A Wingtip Toys - entrar](introduction-and-overview/_static/image4.png)

<span data-ttu-id="d2afe-185">Depois que você entrar como um usuário, você pode adicionar produtos ao carrinho de compras e check-out com o PayPal.</span><span class="sxs-lookup"><span data-stu-id="d2afe-185">Once you've signed in as a user, you can add products to the shopping cart and checkout with PayPal.</span></span> <span data-ttu-id="d2afe-186">O aplicativo de exemplo é projetado para trabalhar em área restrita do desenvolvedor do PayPal.</span><span class="sxs-lookup"><span data-stu-id="d2afe-186">The sample application is designed to work in PayPal's developer sandbox.</span></span> <span data-ttu-id="d2afe-187">Nenhuma transação de dinheiro real ocorre.</span><span class="sxs-lookup"><span data-stu-id="d2afe-187">No actual money transaction takes place.</span></span>

![A Wingtip Toys - carrinho de compras](introduction-and-overview/_static/image5.png)

<span data-ttu-id="d2afe-189">PayPal confirma suas informações de conta, a ordem e o pagamento.</span><span class="sxs-lookup"><span data-stu-id="d2afe-189">PayPal confirms your account, order, and payment information.</span></span>

![A Wingtip Toys - PayPal](introduction-and-overview/_static/image6.png)

<span data-ttu-id="d2afe-191">Depois de retornar do PayPal, você pode revisar e concluir o pedido.</span><span class="sxs-lookup"><span data-stu-id="d2afe-191">After returning from PayPal, you can review and complete your order.</span></span>

![A Wingtip Toys - revisão de ordem](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a><span data-ttu-id="d2afe-193">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d2afe-193">Prerequisites</span></span>

<span data-ttu-id="d2afe-194">Antes de começar, verifique se que o seguinte software está instalado no seu computador:</span><span class="sxs-lookup"><span data-stu-id="d2afe-194">Before you start, make sure the following software is installed on your computer:</span></span>

- <span data-ttu-id="d2afe-195">[Microsoft Visual Studio 2017 ou o Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="d2afe-195">[Microsoft Visual Studio 2017 or Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).</span></span>

<span data-ttu-id="d2afe-196">O .NET Framework é instalado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d2afe-196">The .NET Framework is installed automatically.</span></span>

<span data-ttu-id="d2afe-197">Esta série de tutoriais usa o Microsoft Visual Studio Community 2017.</span><span class="sxs-lookup"><span data-stu-id="d2afe-197">This tutorial series uses Microsoft Visual Studio Community 2017.</span></span> <span data-ttu-id="d2afe-198">Você pode usar qualquer um dos que ou Microsoft Visual Studio 2017 para concluir esta série de tutoriais.</span><span class="sxs-lookup"><span data-stu-id="d2afe-198">You can use either that or Microsoft Visual Studio 2017 to complete this tutorial series.</span></span>

<span data-ttu-id="d2afe-199">Observe o seguinte sobre o Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d2afe-199">Note the following about Visual Studio:</span></span>

* <span data-ttu-id="d2afe-200">Microsoft Visual Studio 2017 e Microsoft Visual Studio Community 2017 são denominados *Visual Studio* em toda esta série de tutoriais.</span><span class="sxs-lookup"><span data-stu-id="d2afe-200">Microsoft Visual Studio 2017 and Microsoft Visual Studio Community 2017 are referred to as *Visual Studio* throughout this tutorial series.</span></span>

* <span data-ttu-id="d2afe-201">Visual Studio 2017 está instalado ao lado de versões mais antigas já instaladas.</span><span class="sxs-lookup"><span data-stu-id="d2afe-201">Visual Studio 2017 is installed next to any older versions already installed.</span></span> <span data-ttu-id="d2afe-202">Sites criados em versões anteriores podem ser abertos no Visual Studio 2017 e continuam a abrir nas versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="d2afe-202">Sites created in earlier versions can be opened in Visual Studio 2017 and continue to open in previous versions.</span></span>

* <span data-ttu-id="d2afe-203">Na primeira vez que você iniciou o Visual Studio, supõe-se você selecionou o *desenvolvimento Web* configurações.</span><span class="sxs-lookup"><span data-stu-id="d2afe-203">The first time you started Visual Studio, it is assumed you selected the *Web Development* settings.</span></span> <span data-ttu-id="d2afe-204">Para obter mais informações, confira [Como: Selecione as configurações de ambiente de desenvolvimento da Web](https://msdn.microsoft.com/library/ff521558.aspx).</span><span class="sxs-lookup"><span data-stu-id="d2afe-204">For more information, see [How to: Select Web Development Environment Settings](https://msdn.microsoft.com/library/ff521558.aspx).</span></span>

<span data-ttu-id="d2afe-205">Depois de instalar os pré-requisitos, você está pronto para começar a criar o projeto Web apresentado nessa série de tutoriais.</span><span class="sxs-lookup"><span data-stu-id="d2afe-205">After installing the prerequisites, you're ready to begin creating the Web project presented in this tutorial series.</span></span>

## <a name="download-the-sample-application"></a><span data-ttu-id="d2afe-206">Baixe o aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="d2afe-206">Download the sample application</span></span>

 <span data-ttu-id="d2afe-207">Você pode baixar o applicatiion exemplo concluído na qualquer momento do site de exemplos do MSDN:</span><span class="sxs-lookup"><span data-stu-id="d2afe-207">You can download the completed sample applicatiion at anytime from the MSDN Samples site:</span></span>

<span data-ttu-id="d2afe-208">[Introdução ao ASP.NET 4.5 do Web Forms e Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (c#)</span><span class="sxs-lookup"><span data-stu-id="d2afe-208">[Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#)</span></span> 

 <span data-ttu-id="d2afe-209">Este download tem os seguintes itens:</span><span class="sxs-lookup"><span data-stu-id="d2afe-209">This download has the following items:</span></span>

- <span data-ttu-id="d2afe-210">O aplicativo de exemplo na *WingtipToys* pasta.</span><span class="sxs-lookup"><span data-stu-id="d2afe-210">The sample application in the *WingtipToys* folder.</span></span>
- <span data-ttu-id="d2afe-211">Os recursos usados para criar o aplicativo de exemplo na *ativos WingtipToys* pasta na *WingtipToys* pasta.</span><span class="sxs-lookup"><span data-stu-id="d2afe-211">The resources used to create the sample application in the *WingtipToys-Assets* folder in the *WingtipToys* folder.</span></span>

<span data-ttu-id="d2afe-212">O download é um *. zip* arquivo.</span><span class="sxs-lookup"><span data-stu-id="d2afe-212">The download is a *.zip* file.</span></span> <span data-ttu-id="d2afe-213">Para ver o projeto completo que cria esta série de tutoriais, encontre e selecione os *C#* pasta do arquivo. zip.</span><span class="sxs-lookup"><span data-stu-id="d2afe-213">To see the completed project that this tutorial series creates, find and select the *C#* folder in the .zip file.</span></span> <span data-ttu-id="d2afe-214">Salve o C# para a pasta que você pode usar para trabalhar com projetos do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d2afe-214">Save the C# folder to the folder you use to work with Visual Studio projects.</span></span> <span data-ttu-id="d2afe-215">Por padrão, a pasta de projetos do Visual Studio 2017 é:</span><span class="sxs-lookup"><span data-stu-id="d2afe-215">By default, the Visual Studio 2017 projects folder is:</span></span>

<span data-ttu-id="d2afe-216"><strong>C:\Users&#92;</strong><strong><em>&lt;username&gt;</em></strong><strong>\source\repos</strong></span><span class="sxs-lookup"><span data-stu-id="d2afe-216"><strong>C:\Users&#92;</strong><strong><em>&lt;username&gt;</em></strong><strong>\source\repos</strong></span></span>

<span data-ttu-id="d2afe-217">Renomeie o ***c#*** pasta a ser ***WingtipToys***.</span><span class="sxs-lookup"><span data-stu-id="d2afe-217">Rename the ***C#*** folder to ***WingtipToys***.</span></span>

> [!NOTE]
> <span data-ttu-id="d2afe-218">Se você já tiver uma pasta chamada *WingtipToys* na sua pasta de projetos, renomeie temporariamente essa pasta existente antes de renomear o *c#* pasta a ser *WingtipToys*.</span><span class="sxs-lookup"><span data-stu-id="d2afe-218">If you already have a folder named *WingtipToys* in your Projects folder, temporarily rename that existing folder before renaming the *C#* folder to *WingtipToys*.</span></span>

<span data-ttu-id="d2afe-219">Para executar o projeto concluído, abra o *WingtipToys* pasta e clique duas vezes o *WingtipToys.sln* arquivo.</span><span class="sxs-lookup"><span data-stu-id="d2afe-219">To run the completed project, open the *WingtipToys* folder and double-click the *WingtipToys.sln* file.</span></span> <span data-ttu-id="d2afe-220">Visual Studio 2017 abre o projeto.</span><span class="sxs-lookup"><span data-stu-id="d2afe-220">Visual Studio 2017 opens the project.</span></span> <span data-ttu-id="d2afe-221">Em seguida, clique com botão direito do *default. aspx* arquivo no **Gerenciador de soluções** e selecione **exibir no navegador**.</span><span class="sxs-lookup"><span data-stu-id="d2afe-221">Next, right-click the *Default.aspx* file in **Solution Explorer** and select **View In Browser**.</span></span>

## <a name="take-a-aspnet-web-forms-quiz-to-review-content"></a><span data-ttu-id="d2afe-222">Faça um teste de Web Forms do ASP.NET para examinar o conteúdo</span><span class="sxs-lookup"><span data-stu-id="d2afe-222">Take a ASP.NET Web Forms quiz to review content</span></span>

<span data-ttu-id="d2afe-223">Depois de concluir a série de tutoriais, faça um teste para testar seu conhecimento e reforçar os conceitos-chave.</span><span class="sxs-lookup"><span data-stu-id="d2afe-223">After completing the tutorial series, take a quiz to test your knowledge and reinforce key concepts.</span></span> <span data-ttu-id="d2afe-224">Cada pergunta fornece uma explicação e links para orientações adicionais.</span><span class="sxs-lookup"><span data-stu-id="d2afe-224">Each question provides an explanation and links to additional guidance.</span></span>

 * [<span data-ttu-id="d2afe-225">ASP.NET Web Forms do teste</span><span class="sxs-lookup"><span data-stu-id="d2afe-225">ASP.NET Web Forms Quiz</span></span>](https://blogs.msdn.microsoft.com/erikreitan/2016/01/08/asp-net-web-forms-quiz/) 

## <a name="tutorial-support-and-comments"></a><span data-ttu-id="d2afe-226">Comentários e suporte de tutorial</span><span class="sxs-lookup"><span data-stu-id="d2afe-226">Tutorial support and comments</span></span>

<span data-ttu-id="d2afe-227">Para perguntas e comentários, use a seção de p e r incluída na [Introdução ao Web Forms do ASP.NET 4.5 e Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) página de exemplo.</span><span class="sxs-lookup"><span data-stu-id="d2afe-227">For questions and comments, use the Q and A section included on the [Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) sample page.</span></span>

<span data-ttu-id="d2afe-228">Comentários sobre esta série de tutoriais são bem-vindos.</span><span class="sxs-lookup"><span data-stu-id="d2afe-228">Comments on this tutorial series are welcome.</span></span> <span data-ttu-id="d2afe-229">Quando esta série de tutoriais é atualizada, todos os esforços é feito para considerar as correções ou sugestões para melhorias.</span><span class="sxs-lookup"><span data-stu-id="d2afe-229">When this tutorial series is updated, every effort is made to consider corrections or suggestions for improvements.</span></span>

<span data-ttu-id="d2afe-230">Se ocorrer um erro, as mensagens de erro correspondente pode ser confusas, sem nenhuma boa explicação sobre como corrigi-lo.</span><span class="sxs-lookup"><span data-stu-id="d2afe-230">If an error occurs, the corresponding error messages could be confusing, with no good explanation on how to fix it.</span></span> <span data-ttu-id="d2afe-231">Para obter ajuda, você pode verificar a [fóruns do ASP.NET](https://forums.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="d2afe-231">For help, you can check the [ASP.NET forums](https://forums.asp.net/).</span></span> <span data-ttu-id="d2afe-232">Outra boa fonte é a seção p e r a [Introdução ao Web Forms do ASP.NET 4.5 e Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) página de exemplo.</span><span class="sxs-lookup"><span data-stu-id="d2afe-232">Another good source is the Q and A section in the [Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) sample page.</span></span> 

> [!div class="step-by-step"]
> [<span data-ttu-id="d2afe-233">Avançar</span><span class="sxs-lookup"><span data-stu-id="d2afe-233">Next</span></span>](create-the-project.md)
