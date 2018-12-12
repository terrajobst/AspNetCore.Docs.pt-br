---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr
title: 'Tutorial: Introdução ao SignalR 1.x | Microsoft Docs'
author: pfletcher
description: Use o ASP.NET SignalR para criar um aplicativo de bate-papo em tempo real em uma página HTML.
ms.author: riande
ms.date: 02/18/2013
ms.assetid: fdc3599a-5217-44c1-951f-0eec9812dce7
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 720a4879f5fbe3c0c2b4c7809cb94c22547329c3
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287345"
---
<a name="tutorial-getting-started-with-signalr-1x"></a><span data-ttu-id="3f84f-103">Tutorial: Introdução ao SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="3f84f-103">Tutorial: Getting Started with SignalR 1.x</span></span>
====================
<span data-ttu-id="3f84f-104">por [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span><span class="sxs-lookup"><span data-stu-id="3f84f-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="3f84f-105">Este tutorial mostra como usar o SignalR para criar um aplicativo de chat em tempo real.</span><span class="sxs-lookup"><span data-stu-id="3f84f-105">This tutorial shows how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="3f84f-106">Você adicionar o SignalR para um aplicativo de web ASP.NET vazio e criar uma página HTML para enviar e exibir as mensagens.</span><span class="sxs-lookup"><span data-stu-id="3f84f-106">You will add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span>


## <a name="overview"></a><span data-ttu-id="3f84f-107">Visão geral</span><span class="sxs-lookup"><span data-stu-id="3f84f-107">Overview</span></span>

<span data-ttu-id="3f84f-108">Este tutorial apresenta o desenvolvimento do SignalR, mostrando como criar um aplicativo de bate-papo simples baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="3f84f-108">This tutorial introduces SignalR development by showing how to build a simple browser-based chat application.</span></span> <span data-ttu-id="3f84f-109">Você será adicionar a biblioteca do SignalR para um aplicativo de web ASP.NET vazio, crie uma classe de hub para enviar mensagens para os clientes e criar uma página HTML que permite aos usuários enviar e receber mensagens de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="3f84f-109">You will add the SignalR library to an empty ASP.NET web application, create a hub class for sending messages to clients, and create an HTML page that lets users send and receive chat messages.</span></span> <span data-ttu-id="3f84f-110">Para obter um tutorial semelhante que mostra como criar um aplicativo de bate-papo no MVC 4 usando uma exibição do MVC, consulte [Introdução ao SignalR e MVC 4](index.md).</span><span class="sxs-lookup"><span data-stu-id="3f84f-110">For a similar tutorial that shows how to create a chat application in MVC 4 using an MVC view, see [Getting Started with SignalR and MVC 4](index.md).</span></span>

> [!NOTE]
> <span data-ttu-id="3f84f-111">Este tutorial usa a versão de lançamento (1.x) do SignalR.</span><span class="sxs-lookup"><span data-stu-id="3f84f-111">This tutorial uses the release (1.x) version of SignalR.</span></span> <span data-ttu-id="3f84f-112">Para obter detalhes sobre as alterações entre o SignalR 1.x e 2.0, consulte [atualizando SignalR 1.x projetos](../releases/upgrading-signalr-1x-projects-to-20.md).</span><span class="sxs-lookup"><span data-stu-id="3f84f-112">For details on changes between SignalR 1.x and 2.0, see [Upgrading SignalR 1.x Projects](../releases/upgrading-signalr-1x-projects-to-20.md).</span></span>

<span data-ttu-id="3f84f-113">O SignalR é uma biblioteca .NET de código-fonte aberto para a criação de aplicativos da web que exigem interação do usuário em tempo real ou atualizações de dados em tempo real.</span><span class="sxs-lookup"><span data-stu-id="3f84f-113">SignalR is an open-source .NET library for building web applications that require live user interaction or real-time data updates.</span></span> <span data-ttu-id="3f84f-114">Exemplos incluem aplicativos sociais, jogos multiusuários, clima de notícias e colaboração de negócios ou aplicativos financeiros de atualização.</span><span class="sxs-lookup"><span data-stu-id="3f84f-114">Examples include social applications, multiuser games, business collaboration, and news, weather, or financial update applications.</span></span> <span data-ttu-id="3f84f-115">Eles são chamados de aplicativos em tempo real.</span><span class="sxs-lookup"><span data-stu-id="3f84f-115">These are often called real-time applications.</span></span>

<span data-ttu-id="3f84f-116">O SignalR simplifica o processo de criação de aplicativos em tempo real.</span><span class="sxs-lookup"><span data-stu-id="3f84f-116">SignalR simplifies the process of building real-time applications.</span></span> <span data-ttu-id="3f84f-117">Ele inclui uma biblioteca de servidor ASP.NET e uma biblioteca de cliente JavaScript para torná-lo mais fácil de gerenciar conexões de cliente-servidor e enviar por push atualizações de conteúdo aos clientes.</span><span class="sxs-lookup"><span data-stu-id="3f84f-117">It includes an ASP.NET server library and a JavaScript client library to make it easier to manage client-server connections and push content updates to clients.</span></span> <span data-ttu-id="3f84f-118">Você pode adicionar a biblioteca SignalR para um aplicativo ASP.NET existente para obter a funcionalidade em tempo real.</span><span class="sxs-lookup"><span data-stu-id="3f84f-118">You can add the SignalR library to an existing ASP.NET application to gain real-time functionality.</span></span>

<span data-ttu-id="3f84f-119">O tutorial demonstra as seguintes tarefas de desenvolvimento do SignalR:</span><span class="sxs-lookup"><span data-stu-id="3f84f-119">The tutorial demonstrates the following SignalR development tasks:</span></span>

- <span data-ttu-id="3f84f-120">Adicionar a biblioteca do SignalR para um aplicativo web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="3f84f-120">Adding the SignalR library to an ASP.NET web application.</span></span>
- <span data-ttu-id="3f84f-121">Criando uma classe de hub para enviar por push o conteúdo aos clientes.</span><span class="sxs-lookup"><span data-stu-id="3f84f-121">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="3f84f-122">Usando a biblioteca jQuery SignalR em uma página da web para enviar mensagens e exibir as atualizações do hub.</span><span class="sxs-lookup"><span data-stu-id="3f84f-122">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="3f84f-123">A captura de tela a seguir mostra o aplicativo de bate-papo em execução em um navegador.</span><span class="sxs-lookup"><span data-stu-id="3f84f-123">The following screen shot shows the chat application running in a browser.</span></span> <span data-ttu-id="3f84f-124">Cada novo usuário pode postar comentários e ver os comentários adicionados depois que o usuário se associe o bate-papo.</span><span class="sxs-lookup"><span data-stu-id="3f84f-124">Each new user can post comments and see comments added after the user joins the chat.</span></span>

![Instâncias de bate-papo](tutorial-getting-started-with-signalr/_static/image1.png)

<span data-ttu-id="3f84f-126">Seções:</span><span class="sxs-lookup"><span data-stu-id="3f84f-126">Sections:</span></span>

- [<span data-ttu-id="3f84f-127">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="3f84f-127">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="3f84f-128">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="3f84f-128">Run the Sample</span></span>](#run)
- [<span data-ttu-id="3f84f-129">Examinar o código</span><span class="sxs-lookup"><span data-stu-id="3f84f-129">Examine the Code</span></span>](#code)
- [<span data-ttu-id="3f84f-130">Próximas Etapas</span><span class="sxs-lookup"><span data-stu-id="3f84f-130">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="3f84f-131">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="3f84f-131">Set up the Project</span></span>

<span data-ttu-id="3f84f-132">Esta seção mostra como criar um aplicativo web ASP.NET vazio, adicione o SignalR e criar o aplicativo de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="3f84f-132">This section shows how to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

<span data-ttu-id="3f84f-133">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="3f84f-133">Prerequisites:</span></span>

- <span data-ttu-id="3f84f-134">Visual Studio 2010 SP1 ou 2012.</span><span class="sxs-lookup"><span data-stu-id="3f84f-134">Visual Studio 2010 SP1 or 2012.</span></span> <span data-ttu-id="3f84f-135">Se você não tiver o Visual Studio, consulte [Downloads do ASP.NET](https://www.asp.net/downloads) para obter o Visual Studio 2012 Express ferramenta de desenvolvimento gratuita.</span><span class="sxs-lookup"><span data-stu-id="3f84f-135">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2012 Express Development Tool.</span></span>
- <span data-ttu-id="3f84f-136">[Microsoft ASP.NET e Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941).</span><span class="sxs-lookup"><span data-stu-id="3f84f-136">[Microsoft ASP.NET and Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941).</span></span> <span data-ttu-id="3f84f-137">Para o Visual Studio 2012, o instalador adiciona novos recursos do ASP.NET, incluindo modelos de SignalR ao Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3f84f-137">For Visual Studio 2012, this installer adds new ASP.NET features including SignalR templates to Visual Studio.</span></span> <span data-ttu-id="3f84f-138">Para o Visual Studio 2010 SP1, um instalador não está disponível, mas você pode concluir o tutorial instalando o pacote NuGet do SignalR, conforme descrito nas etapas de configuração.</span><span class="sxs-lookup"><span data-stu-id="3f84f-138">For Visual Studio 2010 SP1, an installer is not available but you can complete the tutorial by installing the SignalR NuGet package as described in the setup steps.</span></span>

<span data-ttu-id="3f84f-139">As etapas a seguir usam o Visual Studio 2012 para criar um aplicativo Web vazio do ASP.NET e adicionar a biblioteca SignalR:</span><span class="sxs-lookup"><span data-stu-id="3f84f-139">The following steps use Visual Studio 2012 to create an ASP.NET Empty Web Application and add the SignalR library:</span></span>

1. <span data-ttu-id="3f84f-140">No Visual Studio, crie um aplicativo Web vazio do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="3f84f-140">In Visual Studio create an ASP.NET Empty Web Application.</span></span>

    ![Criar web vazio](tutorial-getting-started-with-signalr/_static/image2.png)
2. <span data-ttu-id="3f84f-142">Abra o **Package Manager Console** selecionando **ferramentas | Gerenciador de pacotes NuGet | Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-142">Open the **Package Manager Console** by selecting **Tools | NuGet Package Manager | Package Manager Console**.</span></span> <span data-ttu-id="3f84f-143">Digite o seguinte comando na janela de console:</span><span class="sxs-lookup"><span data-stu-id="3f84f-143">Enter the following command into the console window:</span></span>

    `Install-Package Microsoft.AspNet.SignalR -Version 1.1.3`

    <span data-ttu-id="3f84f-144">Este comando instala a versão mais recente do SignalR 1.x.</span><span class="sxs-lookup"><span data-stu-id="3f84f-144">This command installs the latest version of SignalR 1.x.</span></span>
3. <span data-ttu-id="3f84f-145">Na **Gerenciador de soluções**, clique com botão direito no projeto, selecione **adicionar | Classe**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-145">In **Solution Explorer**, right-click the project, select **Add | Class**.</span></span> <span data-ttu-id="3f84f-146">Nomeie a nova classe **ChatHub**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-146">Name the new class **ChatHub**.</span></span>
4. <span data-ttu-id="3f84f-147">Na **Gerenciador de soluções** expanda o nó de Scripts.</span><span class="sxs-lookup"><span data-stu-id="3f84f-147">In **Solution Explorer** expand the Scripts node.</span></span> <span data-ttu-id="3f84f-148">Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.</span><span class="sxs-lookup"><span data-stu-id="3f84f-148">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    ![Referências da biblioteca](tutorial-getting-started-with-signalr/_static/image3.png)
5. <span data-ttu-id="3f84f-150">Substitua o código na **ChatHub** classe pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3f84f-150">Replace the code in the **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. <span data-ttu-id="3f84f-151">Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-151">In **Solution Explorer**, right-click the project, then click **Add | New Item**.</span></span> <span data-ttu-id="3f84f-152">No **Adicionar Novo Item** caixa de diálogo, selecione **classe de aplicativo Global** e clique em **adicionar**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-152">In the **Add New Item** dialog, select **Global Application Class** and click **Add**.</span></span>

    ![Adicionar global](tutorial-getting-started-with-signalr/_static/image4.png)
7. <span data-ttu-id="3f84f-154">Adicione o seguinte `using` instruções após fornecido `using` instruções na classe Global.asax.cs.</span><span class="sxs-lookup"><span data-stu-id="3f84f-154">Add the following `using` statements after the provided `using` statements in the Global.asax.cs class.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. <span data-ttu-id="3f84f-155">Adicione a seguinte linha de código no `Application_Start` método da classe Global para registrar a rota padrão para hubs do SignalR.</span><span class="sxs-lookup"><span data-stu-id="3f84f-155">Add the following line of code in the `Application_Start` method of the Global class to register the default route for SignalR hubs.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample3.cs)]
9. <span data-ttu-id="3f84f-156">Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-156">In **Solution Explorer**, right-click the project, then click **Add | New Item**.</span></span> <span data-ttu-id="3f84f-157">No **Adicionar Novo Item** caixa de diálogo, selecione Página Html e clique em **Add**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-157">In the **Add New Item** dialog, select Html Page and click **Add**.</span></span>
10. <span data-ttu-id="3f84f-158">Na **Gerenciador de soluções**, a página HTML que você acabou de criar com o botão direito e clique em **definir como página inicial**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-158">In **Solution Explorer**, right-click the HTML page you just created and click **Set as Start Page**.</span></span>
11. <span data-ttu-id="3f84f-159">Substitua o código padrão na página HTML com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3f84f-159">Replace the default code in the HTML page with the following code.</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample4.html)]
12. <span data-ttu-id="3f84f-160">**Salvar tudo** para o projeto.</span><span class="sxs-lookup"><span data-stu-id="3f84f-160">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="3f84f-161">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="3f84f-161">Run the Sample</span></span>

1. <span data-ttu-id="3f84f-162">Pressione F5 para executar o projeto no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="3f84f-162">Press F5 to run the project in debug mode.</span></span> <span data-ttu-id="3f84f-163">A página HTML carrega em uma instância do navegador e solicitará um nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="3f84f-163">The HTML page loads in a browser instance and prompts for a user name.</span></span>

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr/_static/image5.png)
2. <span data-ttu-id="3f84f-165">Insira um nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="3f84f-165">Enter a user name.</span></span>
3. <span data-ttu-id="3f84f-166">Copie a URL da linha de endereço do navegador e usá-lo para abrir duas ou mais instâncias de navegador.</span><span class="sxs-lookup"><span data-stu-id="3f84f-166">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="3f84f-167">Em cada instância do navegador, insira um nome de usuário exclusivo.</span><span class="sxs-lookup"><span data-stu-id="3f84f-167">In each browser instance, enter a unique user name.</span></span>
4. <span data-ttu-id="3f84f-168">Em cada instância do navegador, adicione um comentário e clique em **enviar**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-168">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="3f84f-169">Os comentários devem exibir em todas as instâncias do navegador.</span><span class="sxs-lookup"><span data-stu-id="3f84f-169">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="3f84f-170">Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor.</span><span class="sxs-lookup"><span data-stu-id="3f84f-170">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="3f84f-171">O hub transmite seus comentários para todos os usuários atuais.</span><span class="sxs-lookup"><span data-stu-id="3f84f-171">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="3f84f-172">Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.</span><span class="sxs-lookup"><span data-stu-id="3f84f-172">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="3f84f-173">A captura de tela a seguir mostra o aplicativo de bate-papo em execução em três instâncias do navegador, que são atualizados quando uma instância envia uma mensagem:</span><span class="sxs-lookup"><span data-stu-id="3f84f-173">The following screen shot shows the chat application running in three browser instances, all of which are updated when one instance sends a message:</span></span>

    ![Navegadores de bate-papo](tutorial-getting-started-with-signalr/_static/image6.png)
5. <span data-ttu-id="3f84f-175">Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução.</span><span class="sxs-lookup"><span data-stu-id="3f84f-175">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="3f84f-176">Há um arquivo de script denominado **hubs** que a biblioteca SignalR gera dinamicamente em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="3f84f-176">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="3f84f-177">Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="3f84f-177">This file manages the communication between jQuery script and server-side code.</span></span>

    ![Script gerado hub](tutorial-getting-started-with-signalr/_static/image7.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="3f84f-179">Examinar o código</span><span class="sxs-lookup"><span data-stu-id="3f84f-179">Examine the Code</span></span>

<span data-ttu-id="3f84f-180">O aplicativo de chat SignalR demonstra duas tarefas de desenvolvimento básicas do SignalR: criar um hub de que o objeto de coordenação principal no servidor e usando a biblioteca jQuery SignalR para enviar e receber mensagens.</span><span class="sxs-lookup"><span data-stu-id="3f84f-180">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="3f84f-181">Hubs de SignalR</span><span class="sxs-lookup"><span data-stu-id="3f84f-181">SignalR Hubs</span></span>

<span data-ttu-id="3f84f-182">No exemplo de código a **ChatHub** classe deriva a **ASPNET** classe.</span><span class="sxs-lookup"><span data-stu-id="3f84f-182">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="3f84f-183">Derivando de **Hub** classe é uma maneira útil para criar um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="3f84f-183">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="3f84f-184">Você pode criar métodos públicos em sua classe de hub e, em seguida, acessar esses métodos chamando-as de scripts do jQuery em uma página da web.</span><span class="sxs-lookup"><span data-stu-id="3f84f-184">You can create public methods on your hub class and then access those methods by calling them from jQuery scripts in a web page.</span></span>

<span data-ttu-id="3f84f-185">No código de bate-papo, os clientes chamam a **ChatHub.Send** método para enviar uma nova mensagem.</span><span class="sxs-lookup"><span data-stu-id="3f84f-185">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="3f84f-186">O hub por sua vez envia a mensagem a todos os clientes chamando **Clients.All.broadcastMessage**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-186">The hub in turn sends the message to all clients by calling **Clients.All.broadcastMessage**.</span></span>

<span data-ttu-id="3f84f-187">O **enviar** método demonstra vários conceitos de hub:</span><span class="sxs-lookup"><span data-stu-id="3f84f-187">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="3f84f-188">Declare métodos públicos em um hub para que os clientes possam chamá-los.</span><span class="sxs-lookup"><span data-stu-id="3f84f-188">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="3f84f-189">Use o **Microsoft.AspNet.SignalR.Hub.Clients** propriedade dinâmica para acessar todos os clientes conectados a esse hub.</span><span class="sxs-lookup"><span data-stu-id="3f84f-189">Use the **Microsoft.AspNet.SignalR.Hub.Clients** dynamic property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="3f84f-190">Chamar uma função do jQuery no cliente (como o `broadcastMessage` função) para atualizar os clientes.</span><span class="sxs-lookup"><span data-stu-id="3f84f-190">Call a jQuery function on the client (such as the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="3f84f-191">O SignalR e jQuery</span><span class="sxs-lookup"><span data-stu-id="3f84f-191">SignalR and jQuery</span></span>

<span data-ttu-id="3f84f-192">A página HTML no código de exemplo mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR.</span><span class="sxs-lookup"><span data-stu-id="3f84f-192">The HTML page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="3f84f-193">As tarefas essenciais no código estão declarando um proxy para o hub, declarar uma função que o servidor pode chamar para enviar conteúdo para clientes e iniciando uma conexão para enviar mensagens para o hub de referência.</span><span class="sxs-lookup"><span data-stu-id="3f84f-193">The essential tasks in the code are declaring a proxy to reference the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="3f84f-194">O código a seguir declara um proxy para um hub.</span><span class="sxs-lookup"><span data-stu-id="3f84f-194">The following code declares a proxy for a hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="3f84f-195">No jQuery a referência para a classe de servidor e seus membros é em minúsculas concatenadas.</span><span class="sxs-lookup"><span data-stu-id="3f84f-195">In jQuery the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="3f84f-196">O exemplo de código faz referência de c# **ChatHub** classe no jQuery como **chatHub**.</span><span class="sxs-lookup"><span data-stu-id="3f84f-196">The code sample references the C# **ChatHub** class in jQuery as **chatHub**.</span></span>


<span data-ttu-id="3f84f-197">O código a seguir é como criar uma função de retorno de chamada no script.</span><span class="sxs-lookup"><span data-stu-id="3f84f-197">The following code is how you create a callback function in the script.</span></span> <span data-ttu-id="3f84f-198">A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente.</span><span class="sxs-lookup"><span data-stu-id="3f84f-198">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="3f84f-199">As duas linhas em que o HTML codificar o conteúdo antes de exibi-los são opcionais e mostram uma maneira simples de evitar a injeção de script.</span><span class="sxs-lookup"><span data-stu-id="3f84f-199">The two lines that HTML encode the content before displaying it are optional and show a simple way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample7.html)]

<span data-ttu-id="3f84f-200">O código a seguir mostra como abrir uma conexão com o hub.</span><span class="sxs-lookup"><span data-stu-id="3f84f-200">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="3f84f-201">O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página HTML.</span><span class="sxs-lookup"><span data-stu-id="3f84f-201">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

> [!NOTE]
> <span data-ttu-id="3f84f-202">Essa abordagem garante que a conexão seja estabelecida antes de ser executado o manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="3f84f-202">This approach insures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="3f84f-203">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3f84f-203">Next Steps</span></span>

<span data-ttu-id="3f84f-204">Você aprendeu que o SignalR é uma estrutura para a criação de aplicativos web em tempo real.</span><span class="sxs-lookup"><span data-stu-id="3f84f-204">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="3f84f-205">Você também aprendeu várias tarefas de desenvolvimento do SignalR: como adicionar o SignalR para um aplicativo ASP.NET, como criar uma classe de hub e como enviar e receber mensagens do hub.</span><span class="sxs-lookup"><span data-stu-id="3f84f-205">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="3f84f-206">Você pode tornar o aplicativo de exemplo neste tutorial ou outros aplicativos do SignalR disponíveis na Internet por implantá-los para um provedor de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="3f84f-206">You can make the sample application in this tutorial or other SignalR applications available over the Internet by deploying them to a hosting provider.</span></span> <span data-ttu-id="3f84f-207">A Microsoft oferece a hospedagem de web gratuita para até 10 sites em um livre [conta de avaliação do Windows Azure](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604).</span><span class="sxs-lookup"><span data-stu-id="3f84f-207">Microsoft offers free web hosting for up to 10 web sites in a free [Windows Azure trial account](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604).</span></span> <span data-ttu-id="3f84f-208">Para obter instruções sobre como implantar o aplicativo de exemplo SignalR, consulte [publicar o SignalR obtendo introdução de exemplo como um Site do Windows Azure](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx).</span><span class="sxs-lookup"><span data-stu-id="3f84f-208">For a walkthrough on how to deploy the sample SignalR application, see [Publish the SignalR Getting Started Sample as a Windows Azure Web Site](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx).</span></span> <span data-ttu-id="3f84f-209">Para obter informações detalhadas sobre como implantar um projeto de web do Visual Studio para um Site do Windows Azure, consulte [Implantando um aplicativo ASP.NET a um Site do Windows Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="3f84f-209">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Deploying an ASP.NET Application to a Windows Azure Web Site](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).</span></span> <span data-ttu-id="3f84f-210">(Observação: O transporte de WebSocket atualmente não há suporte para o Windows Azure Web Sites.</span><span class="sxs-lookup"><span data-stu-id="3f84f-210">(Note: The WebSocket transport is not currently supported for Windows Azure Web Sites.</span></span> <span data-ttu-id="3f84f-211">O transporte de WebSocket quando não estiver disponível, o SignalR usa os outros transportes disponíveis, conforme descrito na seção de transportes a [Introdução ao SignalR tópico](index.md).)</span><span class="sxs-lookup"><span data-stu-id="3f84f-211">When WebSocket transport is not available, SignalR uses the other available transports as described in the Transports section of the [Introduction to SignalR topic](index.md).)</span></span>

<span data-ttu-id="3f84f-212">Para aprender os conceitos mais avançados de desenvolvimentos do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:</span><span class="sxs-lookup"><span data-stu-id="3f84f-212">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources:</span></span>

- [<span data-ttu-id="3f84f-213">Projeto de SignalR</span><span class="sxs-lookup"><span data-stu-id="3f84f-213">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="3f84f-214">Github do SignalR e exemplos</span><span class="sxs-lookup"><span data-stu-id="3f84f-214">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="3f84f-215">Wiki do SignalR</span><span class="sxs-lookup"><span data-stu-id="3f84f-215">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)
