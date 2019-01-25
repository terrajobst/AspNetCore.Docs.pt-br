---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Tutorial: Bate-papo em tempo real com SignalR 2 e MVC 5 | Microsoft Docs'
author: bradygaster
description: Este tutorial mostra como usar o ASP.NET SignalR 2 para criar um aplicativo de bate-papo em tempo real. Adicionar o SignalR para um aplicativo MVC 5.
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 1b02aecc68a93dbd6373ca5304530e76c9d0b6b5
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836994"
---
# <a name="tutorial-real-time-chat-with-signalr-2-and-mvc-5"></a><span data-ttu-id="7b0a5-104">Tutorial: Bate-papo em tempo real com SignalR 2 e MVC 5</span><span class="sxs-lookup"><span data-stu-id="7b0a5-104">Tutorial: Real-time chat with SignalR 2 and MVC 5</span></span>

<span data-ttu-id="7b0a5-105">Este tutorial mostra como usar o ASP.NET SignalR 2 para criar um aplicativo de bate-papo em tempo real.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-105">This tutorial shows how to use ASP.NET SignalR 2 to create a real-time chat application.</span></span> <span data-ttu-id="7b0a5-106">Adicionar o SignalR para um aplicativo MVC 5 e criar uma exibição de bate-papo para enviar e exibir as mensagens.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-106">You add SignalR to an MVC 5 application and create a chat view to send and display messages.</span></span>

<span data-ttu-id="7b0a5-107">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="7b0a5-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7b0a5-108">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="7b0a5-108">Set up the project</span></span>
> * <span data-ttu-id="7b0a5-109">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="7b0a5-109">Run the sample</span></span>
> * <span data-ttu-id="7b0a5-110">Examinar o código</span><span class="sxs-lookup"><span data-stu-id="7b0a5-110">Examine the code</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="7b0a5-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7b0a5-111">Prerequisites</span></span>

* <span data-ttu-id="7b0a5-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) com o **ASP.NET e desenvolvimento web** carga de trabalho.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="7b0a5-113">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="7b0a5-113">Set up the Project</span></span>

<span data-ttu-id="7b0a5-114">Esta seção mostra como usar o Visual Studio 2017 e SignalR 2 para criar um aplicativo ASP.NET MVC 5 vazio, adicione a biblioteca SignalR e criar o aplicativo de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-114">This section shows how to use Visual Studio 2017 and SignalR 2 to create an empty ASP.NET MVC 5 application, add the SignalR library, and create the chat application.</span></span>

1. <span data-ttu-id="7b0a5-115">No Visual Studio, crie um aplicativo c# ASP.NET que tem como alvo o .NET Framework 4.5, nomeie-o SignalRChat e clique em Okey.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-115">In Visual Studio, create a C# ASP.NET application that targets .NET Framework 4.5, name it SignalRChat, and click OK.</span></span>

    ![Criar web](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

1. <span data-ttu-id="7b0a5-117">Na **novo aplicativo de Web do ASP.NET - SignalRMvcChat**, selecione **MVC** e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-117">In **New ASP.NET Web Application - SignalRMvcChat**, select **MVC** and then select **Change Authentication**.</span></span>

1. <span data-ttu-id="7b0a5-118">Na **alterar autenticação**, selecione **sem autenticação** e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-118">In **Change Authentication**, select **No Authentication** and click **OK**.</span></span>

    ![Selecione sem autenticação](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)

1. <span data-ttu-id="7b0a5-120">Na **novo aplicativo de Web do ASP.NET - SignalRMvcChat**, selecione **Okey**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-120">In **New ASP.NET Web Application - SignalRMvcChat**, select **OK**.</span></span>

1. <span data-ttu-id="7b0a5-121">Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-121">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="7b0a5-122">Na **Adicionar Novo Item – SignalRChat**, selecione **instalado** > **Visual C#**   >  **Web**  >  **SignalR** e, em seguida, selecione **classe de Hub do SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-122">In **Add New Item - SignalRChat**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="7b0a5-123">Nomeie a classe *ChatHub* e adicioná-lo ao projeto.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-123">Name the class *ChatHub* and add it to the project.</span></span>

    <span data-ttu-id="7b0a5-124">Esta etapa cria o *ChatHub.cs* arquivo de classe e adiciona um conjunto de arquivos de script e referências de assembly que dão suporte ao SignalR ao projeto.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-124">This step creates the *ChatHub.cs* class file and adds a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="7b0a5-125">Substitua o código no novo *ChatHub.cs* arquivo de classe com este código:</span><span class="sxs-lookup"><span data-stu-id="7b0a5-125">Replace the code in the new *ChatHub.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]

1. <span data-ttu-id="7b0a5-126">Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-126">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="7b0a5-127">Nomeie a nova classe *inicialização* e adicioná-lo ao projeto.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-127">Name the new class *Startup* and add it to the project.</span></span>

1. <span data-ttu-id="7b0a5-128">Substitua o código na *Startup.cs* arquivo de classe com este código:</span><span class="sxs-lookup"><span data-stu-id="7b0a5-128">Replace the code in the *Startup.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]

1. <span data-ttu-id="7b0a5-129">Na **Gerenciador de soluções**, selecione **controladores** > **HomeController.cs**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-129">In **Solution Explorer**, select **Controllers** > **HomeController.cs**.</span></span>

1. <span data-ttu-id="7b0a5-130">Adicione este método para o *HomeController.cs*.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-130">Add this method to the *HomeController.cs*.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]

    <span data-ttu-id="7b0a5-131">Esse método retorna o **bate-papo** exibição que você cria em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-131">This method returns the **Chat** view that you create in a later step.</span></span>

1. <span data-ttu-id="7b0a5-132">Na **Gerenciador de soluções**, clique com botão direito **exibições** > **página inicial**e selecione **adicionar**  >    **Modo de exibição**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-132">In **Solution Explorer**, right-click **Views** > **Home**, and select **Add** >  **View**.</span></span>

1. <span data-ttu-id="7b0a5-133">Na **adicionar exibição**, nomeie a nova exibição **bate-papo** e selecione **adicionar**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-133">In **Add View**, name the new view **Chat** and select **Add**.</span></span>

1. <span data-ttu-id="7b0a5-134">Substitua o conteúdo do **Chat.cshtml** com este código:</span><span class="sxs-lookup"><span data-stu-id="7b0a5-134">Replace the contents of **Chat.cshtml** with this code:</span></span>

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]

1. <span data-ttu-id="7b0a5-135">Na **Gerenciador de soluções**, expanda **Scripts**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-135">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="7b0a5-136">Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-136">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="7b0a5-137">O Gerenciador de pacotes pode ter instalado uma versão mais recente dos scripts do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-137">The package manager may have installed a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="7b0a5-138">Verifique se as referências de script no bloco de código correspondem às versões dos arquivos de script no projeto.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-138">Check that the script references in the code block correspond to the versions of the script files in the project.</span></span>

    <span data-ttu-id="7b0a5-139">Referências de script de bloco de código original:</span><span class="sxs-lookup"><span data-stu-id="7b0a5-139">Script references from the original code block:</span></span>

    ```cshtml
    <!--Script references. -->
    <!--The jQuery library is required and is referenced by default in _Layout.cshtml. -->
    <!--Reference the SignalR library. -->
    <script src="~/Scripts/jquery.signalR-2.1.0.min.js"></script>
    ```

1. <span data-ttu-id="7b0a5-140">Se eles não corresponderem, atualize o *. cshtml* arquivo.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-140">If they don't match, update the *.cshtml* file.</span></span>

1. <span data-ttu-id="7b0a5-141">Na barra de menus, selecione **arquivo** > **Salvar tudo**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-141">From the menu bar, select **File** > **Save All**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="7b0a5-142">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="7b0a5-142">Run the Sample</span></span>

1. <span data-ttu-id="7b0a5-143">Na barra de ferramentas, ative **depuração de Script** e, em seguida, selecione o botão Reproduzir para executar o exemplo no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-143">In the toolbar, turn on **Script Debugging** and then select the play button to run the sample in Debug mode.</span></span>

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)

1. <span data-ttu-id="7b0a5-145">Quando o navegador for aberta, insira um nome para sua identidade de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-145">When the browser opens, enter a name for your chat identity.</span></span>

1. <span data-ttu-id="7b0a5-146">Copie a URL do navegador, abra dois outros navegadores e cole as URLs em barras de endereço.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-146">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="7b0a5-147">Em cada navegador, insira um nome exclusivo.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-147">In each browser, enter a unique name.</span></span>

1. <span data-ttu-id="7b0a5-148">Agora, adicione um comentário e selecione **enviar**.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-148">Now, add a comment and select **Send**.</span></span> <span data-ttu-id="7b0a5-149">Repita que em outros navegadores.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-149">Repeat that in the other browsers.</span></span> <span data-ttu-id="7b0a5-150">Os comentários são exibidos em tempo real.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-150">The comments appear in real time.</span></span>

    > [!NOTE]
    > <span data-ttu-id="7b0a5-151">Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-151">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="7b0a5-152">O hub transmite seus comentários para todos os usuários atuais.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-152">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="7b0a5-153">Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-153">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="7b0a5-154">Veja como o aplicativo de bate-papo é executado em três diferentes navegadores.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-154">See how the chat application runs in three different browsers.</span></span> <span data-ttu-id="7b0a5-155">Quando o Tom, Anand e Susan enviam mensagens, todos os navegadores são atualizados em tempo real:</span><span class="sxs-lookup"><span data-stu-id="7b0a5-155">When Tom, Anand, and Susan send messages, all browsers update in real time:</span></span>

    ![Todos os três navegadores exibem ao mesmo histórico de bate-papo](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

1. <span data-ttu-id="7b0a5-157">Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-157">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="7b0a5-158">Há um arquivo de script denominado *hubs* que a biblioteca SignalR gera em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-158">There's a script file named *hubs* that the SignalR library generates at runtime.</span></span> <span data-ttu-id="7b0a5-159">Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-159">This file manages the communication between jQuery script and server-side code.</span></span>

    ![script de hubs gerado automaticamente no nó documentos de Script](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)

## <a name="examine-the-code"></a><span data-ttu-id="7b0a5-161">Examinar o código</span><span class="sxs-lookup"><span data-stu-id="7b0a5-161">Examine the Code</span></span>

<span data-ttu-id="7b0a5-162">O aplicativo de chat SignalR demonstra duas tarefas básicas de desenvolvimento de SignalR.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-162">The SignalR chat application demonstrates two basic SignalR development tasks.</span></span> <span data-ttu-id="7b0a5-163">Ele mostra como criar um hub.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-163">It shows you how to create a hub.</span></span> <span data-ttu-id="7b0a5-164">O servidor usa esse hub de que o objeto principal de coordenação.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-164">The server uses that hub as the main coordination object.</span></span> <span data-ttu-id="7b0a5-165">O hub usa a biblioteca jQuery SignalR para enviar e receber mensagens.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-165">The hub uses the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs-in-the-chathubcs"></a><span data-ttu-id="7b0a5-166">Hubs de SignalR no ChatHub.cs</span><span class="sxs-lookup"><span data-stu-id="7b0a5-166">SignalR Hubs in the ChatHub.cs</span></span>

<span data-ttu-id="7b0a5-167">No exemplo de código, o `ChatHub` classe deriva de `Microsoft.AspNet.SignalR.Hub` classe.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-167">In the code sample, the `ChatHub` class derives from the `Microsoft.AspNet.SignalR.Hub` class.</span></span> <span data-ttu-id="7b0a5-168">Derivando a `Hub` classe é uma maneira útil para criar um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-168">Deriving from the `Hub` class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="7b0a5-169">Você pode criar métodos públicos em sua classe de hub e, em seguida, acessar esses métodos chamando-as de scripts em uma página da web.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-169">You can create public methods on your hub class and then access those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="7b0a5-170">No código de bate-papo, os clientes chamam o `ChatHub.Send` método para enviar uma nova mensagem.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-170">In the chat code, clients call the `ChatHub.Send` method to send a new message.</span></span> <span data-ttu-id="7b0a5-171">O hub por sua vez envia a mensagem a todos os clientes chamando `Clients.All.addNewMessageToPage`.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-171">The hub in turn sends the message to all clients by calling `Clients.All.addNewMessageToPage`.</span></span>

<span data-ttu-id="7b0a5-172">O `Send` método demonstra vários conceitos de hub:</span><span class="sxs-lookup"><span data-stu-id="7b0a5-172">The `Send` method demonstrates several hub concepts:</span></span>

* <span data-ttu-id="7b0a5-173">Declare métodos públicos em um hub para que os clientes possam chamá-los.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-173">Declare public methods on a hub so that clients can call them.</span></span>

* <span data-ttu-id="7b0a5-174">Use o `Microsoft.AspNet.SignalR.Hub.Clients` propriedade dinâmica para se comunicar com todos os clientes conectados a esse hub.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-174">Use the `Microsoft.AspNet.SignalR.Hub.Clients` dynamic property to communicate with all clients connected to this hub.</span></span>

* <span data-ttu-id="7b0a5-175">Chamar uma função no cliente (como o `addNewMessageToPage` função) para atualizar os clientes.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-175">Call a function on the client (like the `addNewMessageToPage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery-chatcshtml"></a><span data-ttu-id="7b0a5-176">O SignalR e jQuery Chat.cshtml</span><span class="sxs-lookup"><span data-stu-id="7b0a5-176">SignalR and jQuery Chat.cshtml</span></span>

<span data-ttu-id="7b0a5-177">O *Chat.cshtml* arquivo de exibição no código de exemplo mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-177">The *Chat.cshtml* view file in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span>  <span data-ttu-id="7b0a5-178">O código realiza várias tarefas importantes.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-178">The code carries out many important tasks.</span></span> <span data-ttu-id="7b0a5-179">Cria uma referência para o proxy gerado automaticamente para o hub, ele declara uma função que o servidor pode chamar para enviar por push o conteúdo para clientes, e ele inicia uma conexão para enviar mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-179">It creates a reference to the autogenerated proxy for the hub, declares a function that the server can call to push content to clients, and it starts a connection to send messages to the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="7b0a5-180">No JavaScript, a referência para a classe de servidor e seus membros está em camelCase.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-180">In JavaScript, the reference to the server class and its members is in camelCase.</span></span> <span data-ttu-id="7b0a5-181">As referências de exemplo de código a C# `ChatHub` classe no JavaScript, enquanto `chatHub`.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-181">The code sample references the C# `ChatHub` class in JavaScript as `chatHub`.</span></span>

<span data-ttu-id="7b0a5-182">Este bloco de código, você cria uma função de retorno de chamada no script.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-182">In this code block, you create a callback function in the script.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

<span data-ttu-id="7b0a5-183">A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-183">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="7b0a5-184">A chamada opcional para o `htmlEncode` função mostra uma maneira para HTML codificar o conteúdo da mensagem antes de exibi-la na página.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-184">The optional call to the `htmlEncode` function shows a way to HTML encode the message content before displaying it in the page.</span></span> <span data-ttu-id="7b0a5-185">É uma maneira de evitar a injeção de script.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-185">It's a way to prevent script injection.</span></span>

<span data-ttu-id="7b0a5-186">Esse código abre uma conexão com o hub.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-186">This code opens a connection with the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

> [!NOTE]
> <span data-ttu-id="7b0a5-187">Essa abordagem garante que você estabeleça uma conexão antes de ser executado o manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-187">This approach ensures that you establish a connection before the event handler executes.</span></span>

<span data-ttu-id="7b0a5-188">O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-188">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the Chat page.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="7b0a5-189">Obter o código</span><span class="sxs-lookup"><span data-stu-id="7b0a5-189">Get the code</span></span>

[<span data-ttu-id="7b0a5-190">Baixe o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="7b0a5-190">Download Completed Project</span></span>](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

## <a name="additional-resources"></a><span data-ttu-id="7b0a5-191">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7b0a5-191">Additional resources</span></span>

<span data-ttu-id="7b0a5-192">Para obter mais informações sobre o SignalR, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="7b0a5-192">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="7b0a5-193">Projeto de SignalR</span><span class="sxs-lookup"><span data-stu-id="7b0a5-193">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="7b0a5-194">GitHub do SignalR e exemplos</span><span class="sxs-lookup"><span data-stu-id="7b0a5-194">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="7b0a5-195">Wiki do SignalR</span><span class="sxs-lookup"><span data-stu-id="7b0a5-195">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="7b0a5-196">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="7b0a5-196">Next steps</span></span>

<span data-ttu-id="7b0a5-197">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="7b0a5-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7b0a5-198">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="7b0a5-198">Set up the project</span></span>
> * <span data-ttu-id="7b0a5-199">Executar a amostra</span><span class="sxs-lookup"><span data-stu-id="7b0a5-199">Ran the sample</span></span>
> * <span data-ttu-id="7b0a5-200">Examinado o código</span><span class="sxs-lookup"><span data-stu-id="7b0a5-200">Examined the code</span></span>

<span data-ttu-id="7b0a5-201">Avance para o próximo artigo para saber como criar um aplicativo web que usa o ASP.NET SignalR 2 para fornecer a funcionalidade de mensagens de alta frequência.</span><span class="sxs-lookup"><span data-stu-id="7b0a5-201">Advance to the next article to learn how to create a web application that uses ASP.NET SignalR 2 to provide high-frequency messaging functionality.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="7b0a5-202">Aplicativo Web com alta frequência de mensagens</span><span class="sxs-lookup"><span data-stu-id="7b0a5-202">Web app with high-frequency messaging</span></span>](tutorial-high-frequency-realtime-with-signalr.md)