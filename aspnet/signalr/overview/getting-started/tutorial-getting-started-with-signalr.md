---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 'Tutorial: Introdução ao SignalR 2 | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como usar o SignalR para criar um aplicativo de chat em tempo real. Você irá adicionar o SignalR para um aplicativo de web ASP.NET vazio e criar um pa HTML...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 3b06e7d0a602e061112adbceba92276f836f6311
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287332"
---
<a name="tutorial-getting-started-with-signalr-2"></a><span data-ttu-id="97738-104">Tutorial: Introdução ao SignalR 2</span><span class="sxs-lookup"><span data-stu-id="97738-104">Tutorial: Getting Started with SignalR 2</span></span>
====================
<span data-ttu-id="97738-105">por [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="97738-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[<span data-ttu-id="97738-106">Baixe o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="97738-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)

> <span data-ttu-id="97738-107">Este tutorial mostra como usar o SignalR para criar um aplicativo de chat em tempo real.</span><span class="sxs-lookup"><span data-stu-id="97738-107">This tutorial shows how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="97738-108">Você adicionar o SignalR para um aplicativo de web ASP.NET vazio e criar uma página HTML para enviar e exibir as mensagens.</span><span class="sxs-lookup"><span data-stu-id="97738-108">You will add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span> 
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="97738-109">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="97738-109">Software versions used in the tutorial</span></span>
> 
> 
> - [<span data-ttu-id="97738-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="97738-110">Visual Studio 2013</span></span>](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - <span data-ttu-id="97738-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="97738-111">.NET 4.5</span></span>
> - <span data-ttu-id="97738-112">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="97738-112">SignalR version 2</span></span>
>   
> 
> 
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="97738-113">Usando o Visual Studio 2012 com este tutorial</span><span class="sxs-lookup"><span data-stu-id="97738-113">Using Visual Studio 2012 with this tutorial</span></span>
> 
> 
> <span data-ttu-id="97738-114">Para usar o Visual Studio 2012 com este tutorial, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="97738-114">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
> 
> - <span data-ttu-id="97738-115">Atualização de seu [Gerenciador de pacotes](http://docs.nuget.org/docs/start-here/installing-nuget) para a versão mais recente.</span><span class="sxs-lookup"><span data-stu-id="97738-115">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="97738-116">Instalar o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="97738-116">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="97738-117">No Web Platform Installer, procure e instale **ASP.NET e Web Tools 2013.1 para Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="97738-117">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="97738-118">Isso irá instalar os modelos do Visual Studio para classes do SignalR, como **Hub**.</span><span class="sxs-lookup"><span data-stu-id="97738-118">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="97738-119">Alguns modelos (como **classe de inicialização OWIN**) não está disponível; nesses casos, use um arquivo de classe em vez disso.</span><span class="sxs-lookup"><span data-stu-id="97738-119">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
> 
> 
> ## <a name="tutorial-versions"></a><span data-ttu-id="97738-120">Versões de tutoriais</span><span class="sxs-lookup"><span data-stu-id="97738-120">Tutorial versions</span></span>
> 
> <span data-ttu-id="97738-121">Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="97738-121">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
> 
> ## <a name="questions-and-comments"></a><span data-ttu-id="97738-122">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="97738-122">Questions and comments</span></span>
> 
> <span data-ttu-id="97738-123">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="97738-123">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="97738-124">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="97738-124">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="97738-125">Visão geral</span><span class="sxs-lookup"><span data-stu-id="97738-125">Overview</span></span>

<span data-ttu-id="97738-126">Este tutorial apresenta o desenvolvimento do SignalR, mostrando como criar um aplicativo de bate-papo simples baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="97738-126">This tutorial introduces SignalR development by showing how to build a simple browser-based chat application.</span></span> <span data-ttu-id="97738-127">Você será adicionar a biblioteca do SignalR para um aplicativo de web ASP.NET vazio, crie uma classe de hub para enviar mensagens para os clientes e criar uma página HTML que permite aos usuários enviar e receber mensagens de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="97738-127">You will add the SignalR library to an empty ASP.NET web application, create a hub class for sending messages to clients, and create an HTML page that lets users send and receive chat messages.</span></span> <span data-ttu-id="97738-128">Para obter um tutorial semelhante que mostra como criar um aplicativo de bate-papo no MVC 5 usando uma exibição do MVC, consulte [Introdução ao SignalR 2 e MVC 5](tutorial-getting-started-with-signalr-and-mvc.md).</span><span class="sxs-lookup"><span data-stu-id="97738-128">For a similar tutorial that shows how to create a chat application in MVC 5 using an MVC view, see [Getting Started with SignalR 2 and MVC 5](tutorial-getting-started-with-signalr-and-mvc.md).</span></span>

> [!NOTE]
> <span data-ttu-id="97738-129">Este tutorial demonstra como criar aplicativos do SignalR na versão 2.</span><span class="sxs-lookup"><span data-stu-id="97738-129">This tutorial demonstrates how to create SignalR applications in version 2.</span></span> <span data-ttu-id="97738-130">Para obter detalhes sobre as alterações entre o SignalR 1.x e 2, consulte [SignalR Atualizando projetos do 1.x](../releases/upgrading-signalr-1x-projects-to-20.md) e [notas de versão do Visual Studio 2013](../../../visual-studio/overview/2013/release-notes.md#TOC13).</span><span class="sxs-lookup"><span data-stu-id="97738-130">For details on changes between SignalR 1.x and 2, see [Upgrading SignalR 1.x Projects](../releases/upgrading-signalr-1x-projects-to-20.md) and [Visual Studio 2013 Release Notes](../../../visual-studio/overview/2013/release-notes.md#TOC13).</span></span>

<span data-ttu-id="97738-131">O SignalR é uma biblioteca .NET de código-fonte aberto para a criação de aplicativos da web que exigem interação do usuário em tempo real ou atualizações de dados em tempo real.</span><span class="sxs-lookup"><span data-stu-id="97738-131">SignalR is an open-source .NET library for building web applications that require live user interaction or real-time data updates.</span></span> <span data-ttu-id="97738-132">Exemplos incluem aplicativos sociais, jogos multiusuários, clima de notícias e colaboração de negócios ou aplicativos financeiros de atualização.</span><span class="sxs-lookup"><span data-stu-id="97738-132">Examples include social applications, multiuser games, business collaboration, and news, weather, or financial update applications.</span></span> <span data-ttu-id="97738-133">Eles são chamados de aplicativos em tempo real.</span><span class="sxs-lookup"><span data-stu-id="97738-133">These are often called real-time applications.</span></span>

<span data-ttu-id="97738-134">O SignalR simplifica o processo de criação de aplicativos em tempo real.</span><span class="sxs-lookup"><span data-stu-id="97738-134">SignalR simplifies the process of building real-time applications.</span></span> <span data-ttu-id="97738-135">Ele inclui uma biblioteca de servidor ASP.NET e uma biblioteca de cliente JavaScript para torná-lo mais fácil de gerenciar conexões de cliente-servidor e enviar por push atualizações de conteúdo aos clientes.</span><span class="sxs-lookup"><span data-stu-id="97738-135">It includes an ASP.NET server library and a JavaScript client library to make it easier to manage client-server connections and push content updates to clients.</span></span> <span data-ttu-id="97738-136">Você pode adicionar a biblioteca SignalR para um aplicativo ASP.NET existente para obter a funcionalidade em tempo real.</span><span class="sxs-lookup"><span data-stu-id="97738-136">You can add the SignalR library to an existing ASP.NET application to gain real-time functionality.</span></span>

<span data-ttu-id="97738-137">O tutorial demonstra as seguintes tarefas de desenvolvimento do SignalR:</span><span class="sxs-lookup"><span data-stu-id="97738-137">The tutorial demonstrates the following SignalR development tasks:</span></span>

- <span data-ttu-id="97738-138">Adicionar a biblioteca do SignalR para um aplicativo web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="97738-138">Adding the SignalR library to an ASP.NET web application.</span></span>
- <span data-ttu-id="97738-139">Criando uma classe de hub para enviar por push o conteúdo aos clientes.</span><span class="sxs-lookup"><span data-stu-id="97738-139">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="97738-140">Criando uma classe de inicialização OWIN para configurar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97738-140">Creating an OWIN startup class to configure the application.</span></span>
- <span data-ttu-id="97738-141">Usando a biblioteca jQuery SignalR em uma página da web para enviar mensagens e exibir as atualizações do hub.</span><span class="sxs-lookup"><span data-stu-id="97738-141">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="97738-142">A captura de tela a seguir mostra o aplicativo de bate-papo em execução em um navegador.</span><span class="sxs-lookup"><span data-stu-id="97738-142">The following screen shot shows the chat application running in a browser.</span></span> <span data-ttu-id="97738-143">Cada novo usuário pode postar comentários e ver os comentários adicionados depois que o usuário se associe o bate-papo.</span><span class="sxs-lookup"><span data-stu-id="97738-143">Each new user can post comments and see comments added after the user joins the chat.</span></span>

![Instâncias de bate-papo](tutorial-getting-started-with-signalr/_static/image1.png)

<span data-ttu-id="97738-145">Seções:</span><span class="sxs-lookup"><span data-stu-id="97738-145">Sections:</span></span>

- [<span data-ttu-id="97738-146">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="97738-146">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="97738-147">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="97738-147">Run the Sample</span></span>](#run)
- [<span data-ttu-id="97738-148">Examinar o código</span><span class="sxs-lookup"><span data-stu-id="97738-148">Examine the Code</span></span>](#code)
- [<span data-ttu-id="97738-149">Próximas Etapas</span><span class="sxs-lookup"><span data-stu-id="97738-149">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="97738-150">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="97738-150">Set up the Project</span></span>

<span data-ttu-id="97738-151">Esta seção mostra como usar o Visual Studio 2013 e a versão 2 do SignalR para criar um aplicativo web ASP.NET vazio, adicione o SignalR e criar o aplicativo de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="97738-151">This section shows how to use Visual Studio 2013 and SignalR version 2 to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

<span data-ttu-id="97738-152">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="97738-152">Prerequisites:</span></span>

- <span data-ttu-id="97738-153">Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="97738-153">Visual Studio 2013.</span></span> <span data-ttu-id="97738-154">Se você não tiver o Visual Studio, consulte [Downloads do ASP.NET](https://www.asp.net/downloads) para obter o Visual Studio 2013 Express ferramenta de desenvolvimento gratuita.</span><span class="sxs-lookup"><span data-stu-id="97738-154">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2013 Express Development Tool.</span></span>

<span data-ttu-id="97738-155">As etapas a seguir usam o Visual Studio 2013 para criar um aplicativo Web vazio do ASP.NET e adicionar a biblioteca SignalR:</span><span class="sxs-lookup"><span data-stu-id="97738-155">The following steps use Visual Studio 2013 to create an ASP.NET Empty Web Application and add the SignalR library:</span></span>

1. <span data-ttu-id="97738-156">No Visual Studio, crie um aplicativo Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="97738-156">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Criar web](tutorial-getting-started-with-signalr/_static/image2.png)
2. <span data-ttu-id="97738-158">No **novo projeto ASP.NET** janela, deixe **vazia** selecionado e clique em **criar projeto**.</span><span class="sxs-lookup"><span data-stu-id="97738-158">In the **New ASP.NET Project** window, leave **Empty** selected and click **Create Project**.</span></span>

    ![Criar web vazio](tutorial-getting-started-with-signalr/_static/image3.png)
3. <span data-ttu-id="97738-160">Na **Gerenciador de soluções**, clique com botão direito no projeto, selecione **adicionar | Classe de Hub do SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="97738-160">In **Solution Explorer**, right-click the project, select **Add | SignalR Hub Class (v2)**.</span></span> <span data-ttu-id="97738-161">Nomeie a classe **ChatHub.cs** e adicioná-lo ao projeto.</span><span class="sxs-lookup"><span data-stu-id="97738-161">Name the class **ChatHub.cs** and add it to the project.</span></span> <span data-ttu-id="97738-162">Esta etapa cria o **ChatHub** de classe e o adiciona ao projeto de um conjunto de arquivos de script e referências de assembly que dão suporte ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="97738-162">This step creates the **ChatHub** class and adds to the project a set of script files and assembly references that support SignalR.</span></span>

    > [!NOTE]
    > <span data-ttu-id="97738-163">Você também pode adicionar o SignalR para um projeto, abrindo o **Ferramentas > Gerenciador de pacotes NuGet > Console do Gerenciador de pacotes** e executando um comando:</span><span class="sxs-lookup"><span data-stu-id="97738-163">You can also add SignalR to a project by opening the **Tools > NuGet Package Manager > Package Manager Console** and running a command:</span></span>

    `install-package Microsoft.AspNet.SignalR`

    <span data-ttu-id="97738-164">Se você usar o console para adicionar o SignalR, crie a classe de hub do SignalR como uma etapa separada depois de adicionar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="97738-164">If you use the console to add SignalR, create the SignalR hub class as a separate step after you add SignalR.</span></span>

    > [!NOTE]
    > <span data-ttu-id="97738-165">Se você estiver usando o Visual Studio 2012, o **classe de Hub do SignalR (v2)** modelo não estará disponível.</span><span class="sxs-lookup"><span data-stu-id="97738-165">If you are using Visual Studio 2012, the **SignalR Hub Class (v2)** template will not be available.</span></span> <span data-ttu-id="97738-166">Você pode adicionar uma sem formatação **classe** chamado `ChatHub` em vez disso.</span><span class="sxs-lookup"><span data-stu-id="97738-166">You can add a plain **Class** called `ChatHub` instead.</span></span>
4. <span data-ttu-id="97738-167">Na **Gerenciador de soluções**, expanda o nó de Scripts.</span><span class="sxs-lookup"><span data-stu-id="97738-167">In **Solution Explorer**, expand the Scripts node.</span></span> <span data-ttu-id="97738-168">Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.</span><span class="sxs-lookup"><span data-stu-id="97738-168">Script libraries for jQuery and SignalR are visible in the project.</span></span>
5. <span data-ttu-id="97738-169">Substitua o código no novo **ChatHub** classe pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="97738-169">Replace the code in the new **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. <span data-ttu-id="97738-170">Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Classe de inicialização OWIN**.</span><span class="sxs-lookup"><span data-stu-id="97738-170">In **Solution Explorer**, right-click the project, then click **Add | OWIN Startup Class**.</span></span> <span data-ttu-id="97738-171">Nomeie a nova classe `Startup` e clique em Okey.</span><span class="sxs-lookup"><span data-stu-id="97738-171">Name the new class `Startup` and click OK.</span></span>

    > [!NOTE]
    > <span data-ttu-id="97738-172">Se você estiver usando o Visual Studio 2012, o **classe de inicialização OWIN** modelo não estará disponível.</span><span class="sxs-lookup"><span data-stu-id="97738-172">If you are using Visual Studio 2012, the **OWIN Startup Class** template will not be available.</span></span> <span data-ttu-id="97738-173">Você pode adicionar uma sem formatação **classe** chamado `Startup` em vez disso.</span><span class="sxs-lookup"><span data-stu-id="97738-173">You can add a plain **Class** called `Startup` instead.</span></span>
7. <span data-ttu-id="97738-174">Altere o conteúdo da nova classe de inicialização para o seguinte.</span><span class="sxs-lookup"><span data-stu-id="97738-174">Change the contents of the new Startup class to the following.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. <span data-ttu-id="97738-175">Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Página HTML**.</span><span class="sxs-lookup"><span data-stu-id="97738-175">In **Solution Explorer**, right-click the project, then click **Add | HTML Page**.</span></span> <span data-ttu-id="97738-176">Nomeie a nova página `index.html`.</span><span class="sxs-lookup"><span data-stu-id="97738-176">Name the new page `index.html`.</span></span>
    >[!NOTE]
    ><span data-ttu-id="97738-177">Talvez você precise alterar os números de versão para as referências às bibliotecas do JQuery e SignalR</span><span class="sxs-lookup"><span data-stu-id="97738-177">You might need to change the version numbers for the references to JQuery and SignalR libraries</span></span>
9. <span data-ttu-id="97738-178">Na **Gerenciador de soluções**, a página HTML que você acabou de criar com o botão direito e clique em **definir como página inicial**.</span><span class="sxs-lookup"><span data-stu-id="97738-178">In **Solution Explorer**, right-click the HTML page you just created and click **Set as Start Page**.</span></span>
10. <span data-ttu-id="97738-179">Substitua o código padrão na página HTML com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="97738-179">Replace the default code in the HTML page with the following code.</span></span>

    > [!NOTE]
    > <span data-ttu-id="97738-180">Uma versão mais recente dos scripts SignalR pode ser instalada pelo Gerenciador de pacote.</span><span class="sxs-lookup"><span data-stu-id="97738-180">A later version of the SignalR scripts may be installed by the package manager.</span></span> <span data-ttu-id="97738-181">Verifique se que as referências de script abaixo correspondem às versões dos arquivos de script no projeto (que será diferente se você tiver adicionado o SignalR usando o NuGet em vez de adicionar um hub.)</span><span class="sxs-lookup"><span data-stu-id="97738-181">Verify that the script references below correspond to the versions of the script files in the project (they will be different if you added SignalR using NuGet rather than adding a hub.)</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]
11. <span data-ttu-id="97738-182">**Salvar tudo** para o projeto.</span><span class="sxs-lookup"><span data-stu-id="97738-182">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="97738-183">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="97738-183">Run the Sample</span></span>

1. <span data-ttu-id="97738-184">Pressione F5 para executar o projeto no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="97738-184">Press F5 to run the project in debug mode.</span></span> <span data-ttu-id="97738-185">A página HTML carrega em uma instância do navegador e solicitará um nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="97738-185">The HTML page loads in a browser instance and prompts for a user name.</span></span>

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr/_static/image4.png)
2. <span data-ttu-id="97738-187">Insira um nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="97738-187">Enter a user name.</span></span>
3. <span data-ttu-id="97738-188">Copie a URL da linha de endereço do navegador e usá-lo para abrir duas ou mais instâncias de navegador.</span><span class="sxs-lookup"><span data-stu-id="97738-188">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="97738-189">Em cada instância do navegador, insira um nome de usuário exclusivo.</span><span class="sxs-lookup"><span data-stu-id="97738-189">In each browser instance, enter a unique user name.</span></span>
4. <span data-ttu-id="97738-190">Em cada instância do navegador, adicione um comentário e clique em **enviar**.</span><span class="sxs-lookup"><span data-stu-id="97738-190">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="97738-191">Os comentários devem exibir em todas as instâncias do navegador.</span><span class="sxs-lookup"><span data-stu-id="97738-191">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="97738-192">Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor.</span><span class="sxs-lookup"><span data-stu-id="97738-192">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="97738-193">O hub transmite seus comentários para todos os usuários atuais.</span><span class="sxs-lookup"><span data-stu-id="97738-193">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="97738-194">Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.</span><span class="sxs-lookup"><span data-stu-id="97738-194">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="97738-195">A captura de tela a seguir mostra o aplicativo de bate-papo em execução em três instâncias do navegador, que são atualizados quando uma instância envia uma mensagem:</span><span class="sxs-lookup"><span data-stu-id="97738-195">The following screen shot shows the chat application running in three browser instances, all of which are updated when one instance sends a message:</span></span>

    ![Navegadores de bate-papo](tutorial-getting-started-with-signalr/_static/image5.png)
5. <span data-ttu-id="97738-197">Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução.</span><span class="sxs-lookup"><span data-stu-id="97738-197">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="97738-198">Há um arquivo de script denominado **hubs** que a biblioteca SignalR gera dinamicamente em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="97738-198">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="97738-199">Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="97738-199">This file manages the communication between jQuery script and server-side code.</span></span>

    ![](tutorial-getting-started-with-signalr/_static/image6.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="97738-200">Examinar o código</span><span class="sxs-lookup"><span data-stu-id="97738-200">Examine the Code</span></span>

<span data-ttu-id="97738-201">O aplicativo de chat SignalR demonstra duas tarefas de desenvolvimento básicas do SignalR: criar um hub de que o objeto de coordenação principal no servidor e usando a biblioteca jQuery SignalR para enviar e receber mensagens.</span><span class="sxs-lookup"><span data-stu-id="97738-201">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="97738-202">Hubs de SignalR</span><span class="sxs-lookup"><span data-stu-id="97738-202">SignalR Hubs</span></span>

<span data-ttu-id="97738-203">No exemplo de código a **ChatHub** classe deriva a **ASPNET** classe.</span><span class="sxs-lookup"><span data-stu-id="97738-203">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="97738-204">Derivando de **Hub** classe é uma maneira útil para criar um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="97738-204">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="97738-205">Você pode criar métodos públicos em sua classe de hub e, em seguida, acessar esses métodos chamando-as de scripts em uma página da web.</span><span class="sxs-lookup"><span data-stu-id="97738-205">You can create public methods on your hub class and then access those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="97738-206">No código de bate-papo, os clientes chamam a **ChatHub.Send** método para enviar uma nova mensagem.</span><span class="sxs-lookup"><span data-stu-id="97738-206">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="97738-207">O hub por sua vez envia a mensagem a todos os clientes chamando **Clients.All.broadcastMessage**.</span><span class="sxs-lookup"><span data-stu-id="97738-207">The hub in turn sends the message to all clients by calling **Clients.All.broadcastMessage**.</span></span>

<span data-ttu-id="97738-208">O **enviar** método demonstra vários conceitos de hub:</span><span class="sxs-lookup"><span data-stu-id="97738-208">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="97738-209">Declare métodos públicos em um hub para que os clientes possam chamá-los.</span><span class="sxs-lookup"><span data-stu-id="97738-209">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="97738-210">Use o **Microsoft.AspNet.SignalR.Hub.Clients** propriedade dinâmica para acessar todos os clientes conectados a esse hub.</span><span class="sxs-lookup"><span data-stu-id="97738-210">Use the **Microsoft.AspNet.SignalR.Hub.Clients** dynamic property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="97738-211">Chamar uma função no cliente (como o `broadcastMessage` função) para atualizar os clientes.</span><span class="sxs-lookup"><span data-stu-id="97738-211">Call a function on the client (such as the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="97738-212">O SignalR e jQuery</span><span class="sxs-lookup"><span data-stu-id="97738-212">SignalR and jQuery</span></span>

<span data-ttu-id="97738-213">A página HTML no código de exemplo mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR.</span><span class="sxs-lookup"><span data-stu-id="97738-213">The HTML page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="97738-214">As tarefas essenciais no código estão declarando um proxy para o hub, declarar uma função que o servidor pode chamar para enviar conteúdo para clientes e iniciando uma conexão para enviar mensagens para o hub de referência.</span><span class="sxs-lookup"><span data-stu-id="97738-214">The essential tasks in the code are declaring a proxy to reference the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="97738-215">O código a seguir declara uma referência a um proxy de hub.</span><span class="sxs-lookup"><span data-stu-id="97738-215">The following code declares a reference to a hub proxy.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> <span data-ttu-id="97738-216">Em JavaScript a referência para a classe de servidor e seus membros é em minúsculas concatenadas.</span><span class="sxs-lookup"><span data-stu-id="97738-216">In JavaScript the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="97738-217">O exemplo de código faz referência de c# **ChatHub** classe no JavaScript, enquanto **chatHub**.</span><span class="sxs-lookup"><span data-stu-id="97738-217">The code sample references the C# **ChatHub** class in JavaScript as **chatHub**.</span></span>


<span data-ttu-id="97738-218">O código a seguir é como criar uma função de retorno de chamada no script.</span><span class="sxs-lookup"><span data-stu-id="97738-218">The following code is how you create a callback function in the script.</span></span> <span data-ttu-id="97738-219">A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente.</span><span class="sxs-lookup"><span data-stu-id="97738-219">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="97738-220">As duas linhas em que o HTML codificar o conteúdo antes de exibi-los são opcionais e mostram uma maneira simples de evitar a injeção de script.</span><span class="sxs-lookup"><span data-stu-id="97738-220">The two lines that HTML encode the content before displaying it are optional and show a simple way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

<span data-ttu-id="97738-221">O código a seguir mostra como abrir uma conexão com o hub.</span><span class="sxs-lookup"><span data-stu-id="97738-221">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="97738-222">O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página HTML.</span><span class="sxs-lookup"><span data-stu-id="97738-222">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

> [!NOTE]
> <span data-ttu-id="97738-223">Essa abordagem garante que a conexão seja estabelecida antes de ser executado o manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="97738-223">This approach ensures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="97738-224">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="97738-224">Next Steps</span></span>

<span data-ttu-id="97738-225">Você aprendeu que o SignalR é uma estrutura para a criação de aplicativos web em tempo real.</span><span class="sxs-lookup"><span data-stu-id="97738-225">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="97738-226">Você também aprendeu várias tarefas de desenvolvimento do SignalR: como adicionar o SignalR para um aplicativo ASP.NET, como criar uma classe de hub e como enviar e receber mensagens do hub.</span><span class="sxs-lookup"><span data-stu-id="97738-226">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="97738-227">Para obter instruções sobre como implantar o aplicativo de exemplo de SignalR no Azure, consulte [usando o SignalR com aplicativos Web no serviço de aplicativo do Azure](../deployment/using-signalr-with-azure-web-sites.md).</span><span class="sxs-lookup"><span data-stu-id="97738-227">For a walkthrough on how to deploy the sample SignalR application to Azure, see [Using SignalR with Web Apps in Azure App Service](../deployment/using-signalr-with-azure-web-sites.md).</span></span> <span data-ttu-id="97738-228">Para obter informações detalhadas sobre como implantar um projeto de web do Visual Studio para um Site do Windows Azure, consulte [criar um aplicativo web ASP.NET no serviço de aplicativo do Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span><span class="sxs-lookup"><span data-stu-id="97738-228">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Create an ASP.NET web app in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span></span>

<span data-ttu-id="97738-229">Para aprender os conceitos mais avançados de desenvolvimentos do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:</span><span class="sxs-lookup"><span data-stu-id="97738-229">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources:</span></span>

- [<span data-ttu-id="97738-230">Projeto de SignalR</span><span class="sxs-lookup"><span data-stu-id="97738-230">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="97738-231">Github do SignalR e exemplos</span><span class="sxs-lookup"><span data-stu-id="97738-231">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="97738-232">Wiki do SignalR</span><span class="sxs-lookup"><span data-stu-id="97738-232">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)
