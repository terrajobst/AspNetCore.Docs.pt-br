---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Tutorial: Introdução ao SignalR 2 e MVC 5 | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como usar o ASP.NET SignalR 2 para criar um aplicativo de bate-papo em tempo real. Você adicionará o SignalR para um aplicativo MVC 5 e criar uma exibição de bate-papo...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.openlocfilehash: 568f82daa67f33736c2bf7a45a3e1339f265c487
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287502"
---
<a name="tutorial-getting-started-with-signalr-2-and-mvc-5"></a><span data-ttu-id="d3c0e-104">Tutorial: Introdução ao SignalR 2 e MVC 5</span><span class="sxs-lookup"><span data-stu-id="d3c0e-104">Tutorial: Getting Started with SignalR 2 and MVC 5</span></span>
====================
<span data-ttu-id="d3c0e-105">por [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span><span class="sxs-lookup"><span data-stu-id="d3c0e-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[<span data-ttu-id="d3c0e-106">Baixe o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="d3c0e-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

> <span data-ttu-id="d3c0e-107">Este tutorial mostra como usar o ASP.NET SignalR 2 para criar um aplicativo de bate-papo em tempo real.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-107">This tutorial shows how to use ASP.NET SignalR 2 to create a real-time chat application.</span></span> <span data-ttu-id="d3c0e-108">Você adicionará o SignalR para um aplicativo MVC 5 e criar uma exibição de bate-papo para enviar e exibir as mensagens.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-108">You will add SignalR to an MVC 5 application and create a chat view to send and display messages.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="d3c0e-109">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="d3c0e-109">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="d3c0e-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="d3c0e-110">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="d3c0e-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="d3c0e-111">.NET 4.5</span></span>
> - <span data-ttu-id="d3c0e-112">MVC 5</span><span class="sxs-lookup"><span data-stu-id="d3c0e-112">MVC 5</span></span>
> - <span data-ttu-id="d3c0e-113">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="d3c0e-113">SignalR version 2</span></span>
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="d3c0e-114">Usando o Visual Studio 2012 com este tutorial</span><span class="sxs-lookup"><span data-stu-id="d3c0e-114">Using Visual Studio 2012 with this tutorial</span></span>
>
>
> <span data-ttu-id="d3c0e-115">Para usar o Visual Studio 2012 com este tutorial, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="d3c0e-115">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
>
> - <span data-ttu-id="d3c0e-116">Atualização de seu [Gerenciador de pacotes](http://docs.nuget.org/docs/start-here/installing-nuget) para a versão mais recente.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-116">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="d3c0e-117">Instalar o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="d3c0e-117">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="d3c0e-118">No Web Platform Installer, procure e instale **ASP.NET e Web Tools 2013.1 para Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-118">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="d3c0e-119">Isso irá instalar os modelos do Visual Studio para classes do SignalR, como **Hub**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-119">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="d3c0e-120">Alguns modelos (como **classe de inicialização OWIN**) não está disponível; nesses casos, use um arquivo de classe em vez disso.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-120">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
>
>
> ## <a name="tutorial-versions"></a><span data-ttu-id="d3c0e-121">Versões de tutoriais</span><span class="sxs-lookup"><span data-stu-id="d3c0e-121">Tutorial Versions</span></span>
>
> <span data-ttu-id="d3c0e-122">Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="d3c0e-122">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="d3c0e-123">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="d3c0e-123">Questions and comments</span></span>
>
> <span data-ttu-id="d3c0e-124">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-124">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="d3c0e-125">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="d3c0e-125">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="d3c0e-126">Visão geral</span><span class="sxs-lookup"><span data-stu-id="d3c0e-126">Overview</span></span>

<span data-ttu-id="d3c0e-127">Este tutorial apresenta o desenvolvimento de aplicativos web em tempo real com o ASP.NET SignalR 2 e o ASP.NET MVC 5.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-127">This tutorial introduces you to real-time web application development with ASP.NET SignalR 2 and ASP.NET MVC 5.</span></span> <span data-ttu-id="d3c0e-128">O tutorial usa o mesmo código de aplicativo de bate-papo, como o [tutorial de Introdução SignalR](tutorial-getting-started-with-signalr.md), mas mostra como adicioná-lo a um aplicativo MVC 5.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-128">The tutorial uses the same chat application code as the [SignalR Getting Started tutorial](tutorial-getting-started-with-signalr.md), but shows how to add it to an MVC 5 application.</span></span>

<span data-ttu-id="d3c0e-129">Neste tópico, você aprenderá as seguintes tarefas de desenvolvimento do SignalR:</span><span class="sxs-lookup"><span data-stu-id="d3c0e-129">In this topic you will learn the following SignalR development tasks:</span></span>

- <span data-ttu-id="d3c0e-130">Adicionar a biblioteca do SignalR para um aplicativo MVC 5.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-130">Adding the SignalR library to an MVC 5 application.</span></span>
- <span data-ttu-id="d3c0e-131">Criando hub e classes de inicialização OWIN para transferir conteúdo para clientes.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-131">Creating hub and OWIN startup classes to push content to clients.</span></span>
- <span data-ttu-id="d3c0e-132">Usando a biblioteca jQuery SignalR em uma página da web para enviar mensagens e exibir as atualizações do hub.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-132">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="d3c0e-133">A captura de tela a seguir mostra o aplicativo de bate-papo concluído em execução em um navegador.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-133">The following screen shot shows the completed chat application running in a browser.</span></span>

![Instâncias de bate-papo](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

<span data-ttu-id="d3c0e-135">Seções:</span><span class="sxs-lookup"><span data-stu-id="d3c0e-135">Sections:</span></span>

- [<span data-ttu-id="d3c0e-136">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="d3c0e-136">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="d3c0e-137">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="d3c0e-137">Run the Sample</span></span>](#run)
- [<span data-ttu-id="d3c0e-138">Examinar o código</span><span class="sxs-lookup"><span data-stu-id="d3c0e-138">Examine the Code</span></span>](#code)
- [<span data-ttu-id="d3c0e-139">Próximas Etapas</span><span class="sxs-lookup"><span data-stu-id="d3c0e-139">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="d3c0e-140">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="d3c0e-140">Set up the Project</span></span>

<span data-ttu-id="d3c0e-141">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="d3c0e-141">Prerequisites:</span></span>

- <span data-ttu-id="d3c0e-142">Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-142">Visual Studio 2013.</span></span> <span data-ttu-id="d3c0e-143">Se você não tiver o Visual Studio, consulte [Downloads do ASP.NET](https://www.asp.net/downloads) para obter o Visual Studio 2013 Express ferramenta de desenvolvimento gratuita.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-143">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2013 Express Development Tool.</span></span>

<span data-ttu-id="d3c0e-144">Esta seção mostra como criar um aplicativo ASP.NET MVC 5, adicione a biblioteca SignalR e criar o aplicativo de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-144">This section shows how to create an ASP.NET MVC 5 application, add the SignalR library, and create the chat application.</span></span>

1. <span data-ttu-id="d3c0e-145">No Visual Studio, crie um aplicativo c# ASP.NET que tem como alvo o .NET Framework 4.5, nomeie-o SignalRChat e clique em Okey.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-145">In Visual Studio, create a C# ASP.NET application that targets .NET Framework 4.5, name it SignalRChat, and click OK.</span></span>

    ![Criar web](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)
2. <span data-ttu-id="d3c0e-147">No `New ASP.NET Project` caixa de diálogo e selecione **MVC**e clique em **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-147">In the `New ASP.NET Project` dialog, and select **MVC**, and click **Change Authentication**.</span></span>

    ![Criar web](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)
3. <span data-ttu-id="d3c0e-149">Selecione **sem autenticação** na **alterar autenticação** caixa de diálogo e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-149">Select **No Authentication** in the **Change Authentication** dialog, and click **OK**.</span></span>

    ![Selecione sem autenticação](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

    > [!NOTE]
    > <span data-ttu-id="d3c0e-151">Se você selecionar um provedor de autenticação diferentes para seu aplicativo, uma `Startup.cs` classe será criada para você, você não precisará criar seus próprios `Startup.cs` classe na etapa 10 abaixo.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-151">If you select a different authentication provider for your application, a `Startup.cs` class will be created for you; you will not need to create your own `Startup.cs` class in step 10 below.</span></span>
4. <span data-ttu-id="d3c0e-152">Clique em **Okey** na **novo projeto ASP.NET** caixa de diálogo.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-152">Click **OK** in the **New ASP.NET Project** dialog.</span></span>
5. <span data-ttu-id="d3c0e-153">Abra o **Ferramentas > Gerenciador de pacotes NuGet > Package Manager Console** e execute o comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-153">Open the **Tools > NuGet Package Manager > Package Manager Console** and run the following command.</span></span> <span data-ttu-id="d3c0e-154">Esta etapa adiciona ao projeto de um conjunto de arquivos de script e referências de assembly que habilitar a funcionalidade do SignalR.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-154">This step adds to the project a set of script files and assembly references that enable SignalR functionality.</span></span>

    `install-package Microsoft.AspNet.SignalR`
6. <span data-ttu-id="d3c0e-155">Na **Gerenciador de soluções**, expanda a pasta de Scripts.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-155">In **Solution Explorer**, expand the Scripts folder.</span></span> <span data-ttu-id="d3c0e-156">Observe que as bibliotecas de script para o SignalR foram adicionadas ao projeto.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-156">Note that script libraries for SignalR have been added to the project.</span></span>

    ![Pasta de scripts](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)
7. <span data-ttu-id="d3c0e-158">Na **Gerenciador de soluções**, clique com botão direito no projeto, selecione **adicionar | Nova pasta**, e adicione uma nova pasta chamada **Hubs**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-158">In **Solution Explorer**, right-click the project, select **Add | New Folder**, and add a new folder named **Hubs**.</span></span>
8. <span data-ttu-id="d3c0e-159">Clique com botão direito do **Hubs** pasta, clique em **adicionar | Novo Item**, selecione o **Visual c# | Web | SignalR** nó o **instalado** painel, selecione **classe de Hub do SignalR (v2)** do painel central e criar um novo hub chamado **ChatHub.cs**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-159">Right-click the **Hubs** folder, click **Add | New Item**, select the **Visual C# | Web | SignalR** node in the **Installed** pane, select **SignalR Hub Class (v2)** from the center pane, and create a new hub named **ChatHub.cs**.</span></span> <span data-ttu-id="d3c0e-160">Você usará essa classe como um hub de servidor SignalR que envia mensagens para todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-160">You will use this class as a SignalR server hub that sends messages to all clients.</span></span>

    ![Criar um novo hub](tutorial-getting-started-with-signalr-and-mvc/_static/image6.png)
9. <span data-ttu-id="d3c0e-162">Substitua o código na **ChatHub** classe pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-162">Replace the code in the **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]
10. <span data-ttu-id="d3c0e-163">Crie uma nova classe chamada Startup.cs.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-163">Create a new class called Startup.cs.</span></span> <span data-ttu-id="d3c0e-164">Altere o conteúdo do arquivo para o seguinte.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-164">Change the contents of the file to the following.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]
11. <span data-ttu-id="d3c0e-165">Editar o `HomeController` classe encontrado no **Controllers** e adicione o seguinte método à classe.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-165">Edit the `HomeController` class found in **Controllers/HomeController.cs** and add the following method to the class.</span></span> <span data-ttu-id="d3c0e-166">Esse método retorna o **bate-papo** modo de exibição que você criará em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-166">This method returns the **Chat** view that you will create in a later step.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]
12. <span data-ttu-id="d3c0e-167">Clique com botão direito do **exibições/inicial** pasta e selecione **adicionar... | Modo de exibição**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-167">Right-click the **Views/Home** folder, and select **Add... | View**.</span></span>
13. <span data-ttu-id="d3c0e-168">No **adicionar exibição** caixa de diálogo, o nome do novo modo de exibição **bate-papo**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-168">In the **Add View** dialog, name the new view **Chat**.</span></span>

    ![Adicionar uma exibição](tutorial-getting-started-with-signalr-and-mvc/_static/image7.png)
14. <span data-ttu-id="d3c0e-170">Substitua o conteúdo do **Chat.cshtml** com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-170">Replace the contents of **Chat.cshtml** with the following code.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="d3c0e-171">Quando você adiciona o SignalR e outras bibliotecas de script ao seu projeto do Visual Studio, o Gerenciador de pacotes poderá instalar uma versão do arquivo de script SignalR é mais recente que a versão mostrada neste tópico.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-171">When you add SignalR and other script libraries to your Visual Studio project, the Package Manager might install a version of the SignalR script file that is more recent than the version shown in this topic.</span></span> <span data-ttu-id="d3c0e-172">Certifique-se de que a referência de script em seu código corresponda à versão da biblioteca de script instalada em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-172">Make sure that the script reference in your code matches the version of the script library installed in your project.</span></span>

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]
15. <span data-ttu-id="d3c0e-173">**Salvar tudo** para o projeto.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-173">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="d3c0e-174">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="d3c0e-174">Run the Sample</span></span>

1. <span data-ttu-id="d3c0e-175">Pressione F5 para executar o projeto no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-175">Press F5 to run the project in debug mode.</span></span>
2. <span data-ttu-id="d3c0e-176">Na linha de endereço do navegador, acrescente **bate-papo/home/** para a URL da página padrão para o projeto.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-176">In the browser address line, append **/home/chat** to the URL of the default page for the project.</span></span> <span data-ttu-id="d3c0e-177">Carrega a página de bate-papo em uma instância do navegador e solicitará um nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-177">The Chat page loads in a browser instance and prompts for a user name.</span></span>

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr-and-mvc/_static/image8.png)
3. <span data-ttu-id="d3c0e-179">Insira um nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-179">Enter a user name.</span></span>
4. <span data-ttu-id="d3c0e-180">Copie a URL da linha de endereço do navegador e usá-lo para abrir duas ou mais instâncias de navegador.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-180">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="d3c0e-181">Em cada instância do navegador, insira um nome de usuário exclusivo.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-181">In each browser instance, enter a unique user name.</span></span>
5. <span data-ttu-id="d3c0e-182">Em cada instância do navegador, adicione um comentário e clique em **enviar**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-182">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="d3c0e-183">Os comentários devem exibir em todas as instâncias do navegador.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-183">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="d3c0e-184">Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-184">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="d3c0e-185">O hub transmite seus comentários para todos os usuários atuais.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-185">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="d3c0e-186">Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-186">Users who join the chat later will see messages added from the time they join.</span></span>
6. <span data-ttu-id="d3c0e-187">A captura de tela a seguir mostra o aplicativo de bate-papo em execução em um navegador.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-187">The following screen shot shows the chat application running in a browser.</span></span>

    ![Navegadores de bate-papo](tutorial-getting-started-with-signalr-and-mvc/_static/image9.png)
7. <span data-ttu-id="d3c0e-189">Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-189">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="d3c0e-190">Esse nó é visível no modo de depuração, se você estiver usando o Internet Explorer como navegador.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-190">This node is visible in debug mode if you are using Internet Explorer as your browser.</span></span> <span data-ttu-id="d3c0e-191">Há um arquivo de script denominado **hubs** que a biblioteca SignalR gera dinamicamente em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-191">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="d3c0e-192">Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-192">This file manages the communication between jQuery script and server-side code.</span></span> <span data-ttu-id="d3c0e-193">Se você usar um navegador que não seja o Internet Explorer, você também pode acessar o dynamic **hubs** arquivo navegando até ele diretamente, por exemplo http://mywebsite/signalr/hubs.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-193">If you use a browser other than Internet Explorer, you can also access the dynamic **hubs** file by browsing to it directly, for example http://mywebsite/signalr/hubs.</span></span>

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="d3c0e-194">Examinar o código</span><span class="sxs-lookup"><span data-stu-id="d3c0e-194">Examine the Code</span></span>

<span data-ttu-id="d3c0e-195">O aplicativo de chat SignalR demonstra duas tarefas de desenvolvimento básicas do SignalR: criar um hub de que o objeto de coordenação principal no servidor e usando a biblioteca jQuery SignalR para enviar e receber mensagens.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-195">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="d3c0e-196">Hubs de SignalR</span><span class="sxs-lookup"><span data-stu-id="d3c0e-196">SignalR Hubs</span></span>

<span data-ttu-id="d3c0e-197">No exemplo de código a **ChatHub** classe deriva a **ASPNET** classe.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-197">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="d3c0e-198">Derivando de **Hub** classe é uma maneira útil para criar um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-198">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="d3c0e-199">Você pode criar métodos públicos em sua classe de hub e, em seguida, acessar esses métodos chamando-as de scripts em uma página da web.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-199">You can create public methods on your hub class and then access those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="d3c0e-200">No código de bate-papo, os clientes chamam a **ChatHub.Send** método para enviar uma nova mensagem.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-200">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="d3c0e-201">O hub por sua vez envia a mensagem a todos os clientes chamando **Clients.All.addNewMessageToPage**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-201">The hub in turn sends the message to all clients by calling **Clients.All.addNewMessageToPage**.</span></span>

<span data-ttu-id="d3c0e-202">O **enviar** método demonstra vários conceitos de hub:</span><span class="sxs-lookup"><span data-stu-id="d3c0e-202">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="d3c0e-203">Declare métodos públicos em um hub para que os clientes possam chamá-los.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-203">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="d3c0e-204">Use o **Microsoft.AspNet.SignalR.Hub.Clients** propriedade para acessar todos os clientes conectados a esse hub.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-204">Use the **Microsoft.AspNet.SignalR.Hub.Clients** property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="d3c0e-205">Chamar uma função no cliente (como o `addNewMessageToPage` função) para atualizar os clientes.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-205">Call a function on the client (such as the `addNewMessageToPage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="d3c0e-206">O SignalR e jQuery</span><span class="sxs-lookup"><span data-stu-id="d3c0e-206">SignalR and jQuery</span></span>

<span data-ttu-id="d3c0e-207">O **Chat.cshtml** arquivo de exibição no código de exemplo mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-207">The **Chat.cshtml** view file in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="d3c0e-208">Criando uma referência para o proxy geradas automaticamente para o hub, declarar uma função que o servidor pode chamar para enviar conteúdo para clientes e iniciando uma conexão para enviar mensagens para o hub de tarefas fundamentais no código.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-208">The essential tasks in the code are creating a reference to the auto-generated proxy for the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="d3c0e-209">O código a seguir declara uma referência a um proxy de hub.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-209">The following code declares a reference to a hub proxy.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="d3c0e-210">Em JavaScript a referência para a classe de servidor e seus membros é em minúsculas concatenadas.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-210">In JavaScript the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="d3c0e-211">O exemplo de código faz referência de c# **ChatHub** classe no JavaScript, enquanto **chatHub**.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-211">The code sample references the C# **ChatHub** class in JavaScript as **chatHub**.</span></span> <span data-ttu-id="d3c0e-212">Se você quiser fazer referência a `ChatHub` classe no jQuery com convencional Pascal casing, como você faria no c#, edite o arquivo de classe ChatHub.cs.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-212">If you want to reference the `ChatHub` class in jQuery with conventional Pascal casing as you would in C#, edit the ChatHub.cs class file.</span></span> <span data-ttu-id="d3c0e-213">Adicionar um `using` instrução para fazer referência a `Microsoft.AspNet.SignalR.Hubs` namespace.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-213">Add a `using` statement to reference the `Microsoft.AspNet.SignalR.Hubs` namespace.</span></span> <span data-ttu-id="d3c0e-214">Em seguida, adicione a `HubName` de atributo para o `ChatHub` classe, por exemplo `[HubName("ChatHub")]`.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-214">Then add the `HubName` attribute to the `ChatHub` class, for example `[HubName("ChatHub")]`.</span></span> <span data-ttu-id="d3c0e-215">Por fim, atualize sua referência de jQuery para o `ChatHub` classe.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-215">Finally, update your jQuery reference to the `ChatHub` class.</span></span>


<span data-ttu-id="d3c0e-216">O código a seguir mostra como criar uma função de retorno de chamada no script.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-216">The following code shows how to create a callback function in the script.</span></span> <span data-ttu-id="d3c0e-217">A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-217">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="d3c0e-218">A chamada opcional para o `htmlEncode` função mostra uma maneira para HTML codificar o conteúdo da mensagem antes de exibi-la na página, como uma maneira de evitar a injeção de script.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-218">The optional call to the `htmlEncode` function shows a way to HTML encode the message content before displaying it in the page, as a way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

<span data-ttu-id="d3c0e-219">O código a seguir mostra como abrir uma conexão com o hub.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-219">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="d3c0e-220">O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-220">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the Chat page.</span></span>

> [!NOTE]
> <span data-ttu-id="d3c0e-221">Essa abordagem garante que a conexão seja estabelecida antes de ser executado o manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-221">This approach ensures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="d3c0e-222">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="d3c0e-222">Next Steps</span></span>

<span data-ttu-id="d3c0e-223">Você aprendeu que o SignalR é uma estrutura para a criação de aplicativos web em tempo real.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-223">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="d3c0e-224">Você também aprendeu várias tarefas de desenvolvimento do SignalR: como adicionar o SignalR para um aplicativo ASP.NET, como criar uma classe de hub e como enviar e receber mensagens do hub.</span><span class="sxs-lookup"><span data-stu-id="d3c0e-224">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="d3c0e-225">Para obter instruções sobre como implantar o aplicativo de exemplo de SignalR no Azure, consulte [usando o SignalR com aplicativos Web no serviço de aplicativo do Azure](../deployment/using-signalr-with-azure-web-sites.md).</span><span class="sxs-lookup"><span data-stu-id="d3c0e-225">For a walkthrough on how to deploy the sample SignalR application to Azure, see [Using SignalR with Web Apps in Azure App Service](../deployment/using-signalr-with-azure-web-sites.md).</span></span> <span data-ttu-id="d3c0e-226">Para obter informações detalhadas sobre como implantar um projeto de web do Visual Studio para um Site do Windows Azure, consulte [criar um aplicativo web ASP.NET no serviço de aplicativo do Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span><span class="sxs-lookup"><span data-stu-id="d3c0e-226">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Create an ASP.NET web app in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span></span>

<span data-ttu-id="d3c0e-227">Para aprender os conceitos mais avançados de desenvolvimentos do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:</span><span class="sxs-lookup"><span data-stu-id="d3c0e-227">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources :</span></span>

- [<span data-ttu-id="d3c0e-228">Projeto de SignalR</span><span class="sxs-lookup"><span data-stu-id="d3c0e-228">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="d3c0e-229">Github do SignalR e exemplos</span><span class="sxs-lookup"><span data-stu-id="d3c0e-229">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="d3c0e-230">Wiki do SignalR</span><span class="sxs-lookup"><span data-stu-id="d3c0e-230">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)
