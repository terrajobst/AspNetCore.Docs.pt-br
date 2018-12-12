---
uid: signalr/overview/deployment/tutorial-signalr-self-host
title: 'Tutorial: Auto-hospedar SignalR | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como criar um servidor de SignalR 2 auto-hospedado e como conectá-lo com um cliente JavaScript. Versões de software usadas no tutorial V...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 400db427-27af-4f2f-abf0-5486d5e024b5
msc.legacyurl: /signalr/overview/deployment/tutorial-signalr-self-host
msc.type: authoredcontent
ms.openlocfilehash: 48cb3d4d71c33ac3382b2b35b5a19fa1c4958874
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287397"
---
<a name="tutorial-signalr-self-host"></a><span data-ttu-id="7ff3e-104">Tutorial: Auto-hospedar SignalR</span><span class="sxs-lookup"><span data-stu-id="7ff3e-104">Tutorial: SignalR Self-Host</span></span>
====================
<span data-ttu-id="7ff3e-105">por [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="7ff3e-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[<span data-ttu-id="7ff3e-106">Baixe o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="7ff3e-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-Self-Host-Sample-6da0f383)

> <span data-ttu-id="7ff3e-107">Este tutorial mostra como criar um servidor de SignalR 2 auto-hospedado e como conectá-lo com um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-107">This tutorial shows how to create a self-hosted SignalR 2 server, and how to connect to it with a JavaScript client.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="7ff3e-108">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="7ff3e-108">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="7ff3e-109">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="7ff3e-109">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="7ff3e-110">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="7ff3e-110">.NET 4.5</span></span>
> - <span data-ttu-id="7ff3e-111">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="7ff3e-111">SignalR version 2</span></span>
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="7ff3e-112">Usando o Visual Studio 2012 com este tutorial</span><span class="sxs-lookup"><span data-stu-id="7ff3e-112">Using Visual Studio 2012 with this tutorial</span></span>
>
>
> <span data-ttu-id="7ff3e-113">Para usar o Visual Studio 2012 com este tutorial, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="7ff3e-113">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
>
> - <span data-ttu-id="7ff3e-114">Atualização de seu [Gerenciador de pacotes](http://docs.nuget.org/docs/start-here/installing-nuget) para a versão mais recente.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-114">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="7ff3e-115">Instalar o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="7ff3e-115">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="7ff3e-116">No Web Platform Installer, procure e instale **ASP.NET e Web Tools 2013.1 para Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-116">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="7ff3e-117">Isso irá instalar os modelos do Visual Studio para classes do SignalR, como **Hub**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-117">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="7ff3e-118">Alguns modelos (como **classe de inicialização OWIN**) não está disponível; nesses casos, use um arquivo de classe em vez disso.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-118">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="7ff3e-119">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="7ff3e-119">Questions and comments</span></span>
>
> <span data-ttu-id="7ff3e-120">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-120">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="7ff3e-121">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="7ff3e-121">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="7ff3e-122">Visão geral</span><span class="sxs-lookup"><span data-stu-id="7ff3e-122">Overview</span></span>

<span data-ttu-id="7ff3e-123">Geralmente, um servidor do SignalR está hospedado em um aplicativo ASP.NET no IIS, mas também pode ser auto-hospedado (como em um aplicativo de console ou o serviço do Windows) usando a biblioteca de hospedar internamente.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-123">A SignalR server is usually hosted in an ASP.NET application in IIS, but it can also be self-hosted (such as in a console application or Windows service) using the self-host library.</span></span> <span data-ttu-id="7ff3e-124">Essa biblioteca, como todas as 2 SignalR, baseia-se em OWIN ([Open Web Interface para .NET](http://owin.org)).</span><span class="sxs-lookup"><span data-stu-id="7ff3e-124">This library, like all of SignalR 2, is built on OWIN ([Open Web Interface for .NET](http://owin.org)).</span></span> <span data-ttu-id="7ff3e-125">OWIN define uma abstração entre servidores de web do .NET e aplicativos da web.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-125">OWIN defines an abstraction between .NET web servers and web applications.</span></span> <span data-ttu-id="7ff3e-126">OWIN separa o aplicativo web do servidor, o que torna o OWIN ideal para auto-hospedagem em um aplicativo web em seu próprio processo, fora do IIS.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-126">OWIN decouples the web application from the server, which makes OWIN ideal for self-hosting a web application in your own process, outside of IIS.</span></span>

<span data-ttu-id="7ff3e-127">Motivos para não hospedar no IIS:</span><span class="sxs-lookup"><span data-stu-id="7ff3e-127">Reasons for not hosting in IIS include:</span></span>

- <span data-ttu-id="7ff3e-128">Ambientes em que o IIS não está disponível ou desejáveis, como um farm de servidores existente sem o IIS.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-128">Environments where IIS is not available or desirable, such as an existing server farm without IIS.</span></span>
- <span data-ttu-id="7ff3e-129">A sobrecarga de desempenho do IIS deve ser evitado.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-129">The performance overhead of IIS needs to be avoided.</span></span>
- <span data-ttu-id="7ff3e-130">Funcionalidade do SignalR deve ser adicionado a um aplicativo de instância que é executado em um serviço do Windows, a função de trabalho do Azure ou outro processo.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-130">SignalR functionality is to be added to an exising application that runs in a Windows Service, Azure worker role, or other process.</span></span>

<span data-ttu-id="7ff3e-131">Se uma solução está sendo desenvolvida como hospedar internamente por motivos de desempenho, recomenda-se também teste o aplicativo hospedado no IIS para determinar o benefício de desempenho.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-131">If a solution is being developed as self-host for performance reasons, it's recommended to also test the application hosted in IIS to determine the performance benefit.</span></span>

<span data-ttu-id="7ff3e-132">Este tutorial contém as seções a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ff3e-132">This tutorial contains the following sections:</span></span>

- [<span data-ttu-id="7ff3e-133">Criar o servidor</span><span class="sxs-lookup"><span data-stu-id="7ff3e-133">Creating the server</span></span>](#server)
- [<span data-ttu-id="7ff3e-134">Acessando o servidor com um cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="7ff3e-134">Accessing the server with a JavaScript client</span></span>](#js)

<a id="server"></a>

## <a name="creating-the-server"></a><span data-ttu-id="7ff3e-135">Criar o servidor</span><span class="sxs-lookup"><span data-stu-id="7ff3e-135">Creating the server</span></span>

<span data-ttu-id="7ff3e-136">Neste tutorial, você criará um servidor que está hospedado em um aplicativo de console, mas o servidor pode ser hospedado em qualquer tipo de processo, como um serviço do Windows ou uma função de trabalho do Azure.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-136">In this tutorial, you'll create a server that's hosted in a console application, but the server can be hosted in any sort of process, such as a Windows service or Azure worker role.</span></span> <span data-ttu-id="7ff3e-137">Para o código de exemplo para hospedar um servidor do SignalR em um serviço do Windows, consulte [Self-Hosting SignalR em um serviço Windows](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).</span><span class="sxs-lookup"><span data-stu-id="7ff3e-137">For sample code for hosting a SignalR server in a Windows Service, see [Self-Hosting SignalR in a Windows Service](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).</span></span>

1. <span data-ttu-id="7ff3e-138">Abra o Visual Studio 2013 com privilégios de administrador.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-138">Open Visual Studio 2013 with administrator privileges.</span></span> <span data-ttu-id="7ff3e-139">Selecione **arquivo**, **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-139">Select **File**, **New Project**.</span></span> <span data-ttu-id="7ff3e-140">Selecione **Windows** sob o **Visual c#** nó no **modelos** painel e selecione o **aplicativo de Console** modelo.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-140">Select **Windows** under the **Visual C#** node in the **Templates** pane, and select the **Console Application** template.</span></span> <span data-ttu-id="7ff3e-141">Nomeie o novo projeto "SignalRSelfHost" e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-141">Name the new project "SignalRSelfHost" and click **OK**.</span></span>

    ![](tutorial-signalr-self-host/_static/image1.png)
2. <span data-ttu-id="7ff3e-142">Abra o console de Gerenciador de pacotes do NuGet, selecionando **ferramentas** > **Gerenciador de pacotes NuGet** > **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-142">Open the NuGet package manager console by selecting **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>
3. <span data-ttu-id="7ff3e-143">No console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7ff3e-143">In the package manager console, enter the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample1.ps1)]

    <span data-ttu-id="7ff3e-144">Este comando adiciona as bibliotecas do SignalR 2 Self ao projeto.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-144">This command adds the SignalR 2 Self-Host libraries to the project.</span></span>
4. <span data-ttu-id="7ff3e-145">No console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7ff3e-145">In the package manager console, enter the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample2.ps1)]

    <span data-ttu-id="7ff3e-146">Este comando adiciona a biblioteca owin ao projeto.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-146">This command adds the Microsoft.Owin.Cors library to the project.</span></span> <span data-ttu-id="7ff3e-147">Essa biblioteca será usada para suporte de domínio cruzado, que é necessário para aplicativos que hospedam o SignalR e um cliente da página da web em domínios diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-147">This library will be used for cross-domain support, which is required for applications that host SignalR and a web page client in different domains.</span></span> <span data-ttu-id="7ff3e-148">Uma vez que você irá hospedando o servidor do SignalR e o cliente da web em portas diferentes, isso significa que esse domínio cruzado deve ser habilitado para a comunicação entre esses componentes.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-148">Since you'll be hosting the SignalR server and the web client on different ports, this means that cross-domain must be enabled for communication between these components.</span></span>
5. <span data-ttu-id="7ff3e-149">Substitua o conteúdo de Program.cs pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-149">Replace the contents of Program.cs with the following code.</span></span>

    [!code-csharp[Main](tutorial-signalr-self-host/samples/sample3.cs)]

    <span data-ttu-id="7ff3e-150">O código acima inclui três classes:</span><span class="sxs-lookup"><span data-stu-id="7ff3e-150">The above code includes three classes:</span></span>

    - <span data-ttu-id="7ff3e-151">**Programa**, incluindo o **Main** definindo o caminho principal de execução do método.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-151">**Program**, including the **Main** method defining the primary path of execution.</span></span> <span data-ttu-id="7ff3e-152">Nesse método, um aplicativo web do tipo **inicialização** é iniciado na URL especificada (`http://localhost:8080`).</span><span class="sxs-lookup"><span data-stu-id="7ff3e-152">In this method, a web application of type **Startup** is started at the specified URL (`http://localhost:8080`).</span></span> <span data-ttu-id="7ff3e-153">Se segurança for necessária no ponto de extremidade, o SSL pode ser implementado.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-153">If security is required on the endpoint, SSL can be implemented.</span></span> <span data-ttu-id="7ff3e-154">Consulte [como: Configurar uma porta com um certificado SSL](https://msdn.microsoft.com/library/ms733791.aspx) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-154">See [How to: Configure a Port with an SSL Certificate](https://msdn.microsoft.com/library/ms733791.aspx) for more information.</span></span>
    - <span data-ttu-id="7ff3e-155">**Inicialização**, a classe que contém a configuração para o servidor do SignalR (a única configuração que usa este tutorial é a chamada para `UseCors`) e a chamada para `MapSignalR`, que cria as rotas para todos os objetos Hub no projeto.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-155">**Startup**, the class containing the configuration for the SignalR server (the only configuration this tutorial uses is the call to `UseCors`), and the call to `MapSignalR`, which creates routes for any Hub objects in the project.</span></span>
    - <span data-ttu-id="7ff3e-156">**MyHub**, a classe SignalR Hub que o aplicativo será fornecer aos clientes.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-156">**MyHub**, the SignalR Hub class that the application will provide to clients.</span></span> <span data-ttu-id="7ff3e-157">Essa classe tem um único método, **enviar**, que os clientes chamarão para difundir uma mensagem para todos os outros clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-157">This class has a single method, **Send**, that clients will call to broadcast a message to all other connected clients.</span></span>
6. <span data-ttu-id="7ff3e-158">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-158">Compile and run the application.</span></span> <span data-ttu-id="7ff3e-159">O endereço que o servidor está em execução deve mostrar em uma janela do console.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-159">The address that the server is running should show in a console window.</span></span>

    ![](tutorial-signalr-self-host/_static/image2.png)
7. <span data-ttu-id="7ff3e-160">Se a execução falhará com a exceção `System.Reflection.TargetInvocationException was unhandled`, você precisará reiniciar o Visual Studio com privilégios de administrador.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-160">If execution fails with the exception `System.Reflection.TargetInvocationException was unhandled`, you will need to restart Visual Studio with administrator privileges.</span></span>
8. <span data-ttu-id="7ff3e-161">Pare o aplicativo antes de prosseguir para a próxima seção.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-161">Stop the application before proceeding to the next section.</span></span>

<a id="js"></a>

## <a name="accessing-the-server-with-a-javascript-client"></a><span data-ttu-id="7ff3e-162">Acessando o servidor com um cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="7ff3e-162">Accessing the server with a JavaScript client</span></span>

<span data-ttu-id="7ff3e-163">Nesta seção, você usará o mesmo cliente JavaScript do [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="7ff3e-163">In this section, you'll use the same JavaScript client from the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md).</span></span> <span data-ttu-id="7ff3e-164">Podemos apenas fazer uma modificação para o cliente, que é definir explicitamente a URL do hub.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-164">We'll only make one modification to the client, which is to explicitly define the hub URL.</span></span> <span data-ttu-id="7ff3e-165">Com um aplicativo hospedado internamente, o servidor pode não ser necessariamente o mesmo endereço que a URL de conexão (devido a proxies reversos e balanceadores de carga), portanto, a URL precisa ser definida explicitamente.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-165">With a self-hosted application, the server may not necessarily be at the same address as the connection URL (due to reverse proxies and load balancers), so the URL needs to be defined explicitly.</span></span>

1. <span data-ttu-id="7ff3e-166">Na **Gerenciador de soluções**, clique com botão direito na solução e selecione **Add**, **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-166">In **Solution Explorer**, right-click on the solution and select **Add**, **New Project**.</span></span> <span data-ttu-id="7ff3e-167">Selecione o **Web** nó e selecione o **aplicativo Web ASP.NET** modelo.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-167">Select the **Web** node, and select the **ASP.NET Web Application** template.</span></span> <span data-ttu-id="7ff3e-168">Nomeie o projeto "JavascriptClient" e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-168">Name the project "JavascriptClient" and click **OK**.</span></span>

    ![](tutorial-signalr-self-host/_static/image3.png)
2. <span data-ttu-id="7ff3e-169">Selecione o **vazio** modelo e deixe as opções restantes não selecionadas.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-169">Select the **Empty** template, and leave the remaining options unselected.</span></span> <span data-ttu-id="7ff3e-170">Selecione **criar projeto**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-170">Select **Create Project**.</span></span>

    ![](tutorial-signalr-self-host/_static/image4.png)
3. <span data-ttu-id="7ff3e-171">No console do Gerenciador de pacotes, selecione o projeto de "JavascriptClient" na **projeto padrão** lista suspensa e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7ff3e-171">In the package manager console, select the "JavascriptClient" project in the **Default project** drop-down, and execute the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample4.ps1)]

    <span data-ttu-id="7ff3e-172">Esse comando instala as bibliotecas de SignalR e o JQuery que você precisará no cliente.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-172">This command installs the SignalR and JQuery libraries that you'll need in the client.</span></span>
4. <span data-ttu-id="7ff3e-173">Clique com botão direito no seu projeto e selecione **Add**, **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-173">Right-click on your project and select **Add**, **New Item**.</span></span> <span data-ttu-id="7ff3e-174">Selecione o **Web** nó e selecionar HTML Page.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-174">Select the **Web** node, and select HTML Page.</span></span> <span data-ttu-id="7ff3e-175">Nomeie a página **default. HTML**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-175">Name the page **Default.html**.</span></span>

    ![](tutorial-signalr-self-host/_static/image5.png)
5. <span data-ttu-id="7ff3e-176">Substitua o conteúdo da nova página HTML com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-176">Replace the contents of the new HTML page with the following code.</span></span> <span data-ttu-id="7ff3e-177">Verifique se as referências de script correspondem os scripts na pasta Scripts do projeto.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-177">Verify that the script references here match the scripts in the Scripts folder of the project.</span></span>

    [!code-html[Main](tutorial-signalr-self-host/samples/sample5.html?highlight=31-32)]

    <span data-ttu-id="7ff3e-178">O código a seguir (realçado em exemplo de código acima) é a adição feitas para o cliente usado no tutorial de Introdução ao uso (além de atualizar o código para o SignalR versão 2 beta).</span><span class="sxs-lookup"><span data-stu-id="7ff3e-178">The following code (highlighted in the code sample above) is the addition that you've made to the client used in the Getting Stared tutorial (in addition to upgrading the code to SignalR version 2 beta).</span></span> <span data-ttu-id="7ff3e-179">Esta linha de código define explicitamente a URL de conexão base para o SignalR no servidor.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-179">This line of code explicitly sets the base connection URL for SignalR on the server.</span></span>

    [!code-javascript[Main](tutorial-signalr-self-host/samples/sample6.js)]
6. <span data-ttu-id="7ff3e-180">Clique com botão direito na solução e, em seguida, selecione **definir projetos de inicialização...** . Selecione o **vários projetos de inicialização** botão de opção e, em seguida, defina ambos os projetos **ação** para **iniciar**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-180">Right-click on the solution, and select **Set Startup Projects...**. Select the **Multiple startup projects** radio button, and set both projects' **Action** to **Start**.</span></span>

    ![](tutorial-signalr-self-host/_static/image6.png)
7. <span data-ttu-id="7ff3e-181">Clique com botão direito em "Default" e selecione **definir como página inicial**.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-181">Right-click on "Default.html" and select **Set As Start Page**.</span></span>
8. <span data-ttu-id="7ff3e-182">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-182">Run the application.</span></span> <span data-ttu-id="7ff3e-183">O servidor e a página serão iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-183">The server and page will launch.</span></span> <span data-ttu-id="7ff3e-184">Talvez seja necessário recarregar a página da web (ou selecione **continuar** no depurador) se a página for carregada antes que o servidor é iniciado.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-184">You may need to reload the web page (or select **Continue** in the debugger) if the page loads before the server is started.</span></span>
9. <span data-ttu-id="7ff3e-185">No navegador, forneça um nome de usuário quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-185">In the browser, provide a username when prompted.</span></span> <span data-ttu-id="7ff3e-186">Copie a URL da página para outra janela ou guia do navegador e forneça um nome de usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-186">Copy the page's URL into another browser tab or window and provide a different username.</span></span> <span data-ttu-id="7ff3e-187">Você poderá enviar mensagens de navegador de um painel para outro, como no tutorial de Introdução.</span><span class="sxs-lookup"><span data-stu-id="7ff3e-187">You will be able to send messages from one browser pane to the other, as in the Getting Started tutorial.</span></span>
