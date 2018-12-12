---
title: Introdução às Páginas do Razor no ASP.NET Core
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 05/30/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ca5b134aaa0a9218bc3b0466c3448bd41e8cef8b
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450730"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="5dfd0-104">Tutorial: introdução ao Razor Pages no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5dfd0-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="5dfd0-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5dfd0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="5dfd0-106">Recomendamos que você siga a versão ASP.NET Core 2.1 deste tutorial.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-106">We recommend you follow the ASP.NET Core 2.1 version of this tutorial.</span></span> <span data-ttu-id="5dfd0-107">É **muito** mais fácil de seguir e cobre muito mais recursos.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-107">It's **much** easier to follow and covers more features.</span></span> <span data-ttu-id="5dfd0-108">Selecione **ASP.NET Core 2.1** no seletor de versão.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-108">Select **ASP.NET Core 2.1** in the version selector.</span></span>

![Seletor de versão no sumário](razor-pages-start/_static/v21.png)

::: moniker-end

<span data-ttu-id="5dfd0-110">Este tutorial ensina as noções básicas de criação de um aplicativo Web de Páginas do Razor do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-110">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span> <span data-ttu-id="5dfd0-111">As Páginas do Razor são a maneira recomendada para criar a interface do usuário para aplicativos Web no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-111">Razor Pages is the recommended way to build UI for web apps in ASP.NET Core.</span></span>

<span data-ttu-id="5dfd0-112">Há três versões deste tutorial:</span><span class="sxs-lookup"><span data-stu-id="5dfd0-112">There are three versions of this tutorial:</span></span>

* <span data-ttu-id="5dfd0-113">Windows: este tutorial</span><span class="sxs-lookup"><span data-stu-id="5dfd0-113">Windows: This tutorial</span></span>
* <span data-ttu-id="5dfd0-114">macOS: [Introdução a Razor Pages com o Visual Studio para Mac](xref:tutorials/razor-pages-mac/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="5dfd0-114">macOS: [Get started with Razor Pages with Visual Studio for Mac](xref:tutorials/razor-pages-mac/razor-pages-start)</span></span>
* <span data-ttu-id="5dfd0-115">macOS, Linux e Windows: [Introdução a Páginas Razor no ASP.NET Core no Visual Studio Code](xref:tutorials/razor-pages-vsc/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="5dfd0-115">macOS, Linux, and Windows: [Get started with ASP.NET Core Razor Pages in Visual Studio Code](xref:tutorials/razor-pages-vsc/razor-pages-start)</span></span>

<span data-ttu-id="5dfd0-116">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5dfd0-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="5dfd0-117">Estamos testando a usabilidade de uma nova estrutura proposta para o sumário do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-117">We’re testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span>  <span data-ttu-id="5dfd0-118">Se você tiver alguns minutos para experimentar um exercício de localização de sete tópicos diferentes no sumário atual ou proposto, [clique aqui para participar do estudo](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span><span class="sxs-lookup"><span data-stu-id="5dfd0-118">If you have a few minutes to try an exercise of finding 7 different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="prerequisites"></a><span data-ttu-id="5dfd0-119">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="5dfd0-119">Prerequisites</span></span>

[!INCLUDE [Prerequisites](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-razor-web-app"></a><span data-ttu-id="5dfd0-120">Criar um aplicativo Web do Razor</span><span class="sxs-lookup"><span data-stu-id="5dfd0-120">Create a Razor web app</span></span>

* <span data-ttu-id="5dfd0-121">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5dfd0-122">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-122">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="5dfd0-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="5dfd0-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar/colar código.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy/paste code.</span></span>
 <span data-ttu-id="5dfd0-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="5dfd0-125">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="5dfd0-126">Selecione **ASP.NET Core 2.1** na lista suspensa e selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-126">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

 ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.1.png)

<span data-ttu-id="5dfd0-128">O modelo do Visual Studio cria um projeto inicial:</span><span class="sxs-lookup"><span data-stu-id="5dfd0-128">The Visual Studio template creates a starter project:</span></span>

![Gerenciador de Soluções](razor-pages-start/_static/se2.1.png)

<span data-ttu-id="5dfd0-130">Pressione **F5** para executar o aplicativo no modo de depuração ou **Ctrl-F5** para executar sem anexar o depurador.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-130">Press **F5** to run the app in debug mode or **Ctrl-F5** to run without attaching the debugger.</span></span> <span data-ttu-id="5dfd0-131">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-131">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5dfd0-132">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-132">This app doesn't track personal information.</span></span> <span data-ttu-id="5dfd0-133">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5dfd0-133">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR.png)

<span data-ttu-id="5dfd0-135">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="5dfd0-135">The following image shows the app after accepting tracking:</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.1.png)

* <span data-ttu-id="5dfd0-137">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-137">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5dfd0-138">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-138">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5dfd0-139">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-139">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5dfd0-140">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-140">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="5dfd0-141">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-141">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5dfd0-142">Na imagem anterior, o número da porta é 5001.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-142">In the preceding image, the port number is 5001.</span></span> <span data-ttu-id="5dfd0-143">Quando você executar o aplicativo, verá um número de porta diferente.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-143">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5dfd0-144">Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-144">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5dfd0-145">Muitos desenvolvedores preferem usar o modo de não depuração para iniciar o aplicativo rapidamente e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-145">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

[!INCLUDE [razor-pages-start](~/includes/RP/2.1/razor-pages-start.md)]

[!INCLUDE [F7](~/includes/RP/F7.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="prerequisites"></a><span data-ttu-id="5dfd0-146">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="5dfd0-146">Prerequisites</span></span>

[!INCLUDE [Prerequisites](~/includes/net-core-prereqs-windows.md)]

## <a name="create-a-razor-web-app"></a><span data-ttu-id="5dfd0-147">Criar um aplicativo Web do Razor</span><span class="sxs-lookup"><span data-stu-id="5dfd0-147">Create a Razor web app</span></span>

* <span data-ttu-id="5dfd0-148">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-148">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5dfd0-149">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-149">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="5dfd0-150">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-150">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="5dfd0-151">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar/colar código.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-151">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy/paste code.</span></span>
  <span data-ttu-id="5dfd0-152">![novo aplicativo Web ASP.NET Core](../../razor-pages/index/_static/np.png)</span><span class="sxs-lookup"><span data-stu-id="5dfd0-152">![new ASP.NET Core Web Application](../../razor-pages/index/_static/np.png)</span></span>
* <span data-ttu-id="5dfd0-153">Selecione **ASP.NET Core 2.0** na lista suspensa e selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-153">Select **ASP.NET Core 2.0** in the dropdown, and then select **Web Application**.</span></span>

  [!INCLUDE [install 2.0](~/includes/dotnetcore-on-dotnetfx-vs.md)]

<span data-ttu-id="5dfd0-154">O modelo do Visual Studio cria um projeto inicial:</span><span class="sxs-lookup"><span data-stu-id="5dfd0-154">The Visual Studio template creates a starter project:</span></span>

![Gerenciador de Soluções](razor-pages-start/_static/se.png)

<span data-ttu-id="5dfd0-156">Pressione **F5** para executar o aplicativo no modo de depuração ou **Ctrl-F5** para executar sem anexar o depurador</span><span class="sxs-lookup"><span data-stu-id="5dfd0-156">Press **F5** to run the app in debug mode or **Ctrl-F5** to run without attaching the debugger</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home.png)

* <span data-ttu-id="5dfd0-158">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="5dfd0-159">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-159">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5dfd0-160">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5dfd0-161">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-161">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="5dfd0-162">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-162">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5dfd0-163">Na imagem anterior, o número da porta é 5000.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-163">In the preceding image, the port number is 5000.</span></span> <span data-ttu-id="5dfd0-164">Quando você executar o aplicativo, verá um número de porta diferente.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-164">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5dfd0-165">Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-165">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5dfd0-166">Muitos desenvolvedores preferem usar o modo de não depuração para iniciar o aplicativo rapidamente e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="5dfd0-166">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

[!INCLUDE [razor-pages-start](~/includes/RP/razor-pages-start.md)]

::: moniker-end

> [!div class="step-by-step"]
> [<span data-ttu-id="5dfd0-167">Próximo: adicionando um modelo</span><span class="sxs-lookup"><span data-stu-id="5dfd0-167">Next: Adding a model</span></span>](xref:tutorials/razor-pages/model)
