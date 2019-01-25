---
uid: signalr/overview/releases/upgrading-signalr-1x-projects-to-20
title: Atualizando projetos do SignalR 1.x para a versão 2 | Microsoft Docs
author: bradygaster
description: Este tópico descreve como atualizar um projeto existente do SignalR 1.x para o SignalR 2. x e como solucionar problemas que podem surgir durante o processo de atualização...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: adcfef99-9bc5-489d-a91b-9b7c2bc35e04
msc.legacyurl: /signalr/overview/releases/upgrading-signalr-1x-projects-to-20
msc.type: authoredcontent
ms.openlocfilehash: a1cb4903f3cdeef70ffd0f624a3a2170f641a395
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837332"
---
<a name="upgrading-signalr-1x-projects-to-version-2"></a><span data-ttu-id="dbac5-103">Atualizando projetos do SignalR 1.x para a versão 2</span><span class="sxs-lookup"><span data-stu-id="dbac5-103">Upgrading SignalR 1.x Projects to version 2</span></span>
====================
<span data-ttu-id="dbac5-104">por [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="dbac5-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="dbac5-105">Este tópico descreve como atualizar um projeto existente do SignalR 1.x para o SignalR 2. x e como solucionar problemas que podem surgir durante o processo de atualização.</span><span class="sxs-lookup"><span data-stu-id="dbac5-105">This topic describes how to upgrade an existing SignalR 1.x project to SignalR 2.x, and how to troubleshoot issues that may arise during the upgrade process.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="dbac5-106">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="dbac5-106">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="dbac5-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="dbac5-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="dbac5-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="dbac5-108">.NET 4.5</span></span>
> - <span data-ttu-id="dbac5-109">Versões 1 e 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="dbac5-109">SignalR versions 1 and 2</span></span>
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="dbac5-110">Usando o Visual Studio 2012 com este tutorial</span><span class="sxs-lookup"><span data-stu-id="dbac5-110">Using Visual Studio 2012 with this tutorial</span></span>
>
>
> <span data-ttu-id="dbac5-111">Para usar o Visual Studio 2012 com este tutorial, faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="dbac5-111">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
>
> - <span data-ttu-id="dbac5-112">Atualização de seu [Gerenciador de pacotes](http://docs.nuget.org/docs/start-here/installing-nuget) para a versão mais recente.</span><span class="sxs-lookup"><span data-stu-id="dbac5-112">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="dbac5-113">Instalar o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="dbac5-113">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="dbac5-114">No Web Platform Installer, procure e instale **ASP.NET e Web Tools 2013.1 para Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="dbac5-114">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="dbac5-115">Isso irá instalar os modelos do Visual Studio para classes do SignalR, como **Hub**.</span><span class="sxs-lookup"><span data-stu-id="dbac5-115">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="dbac5-116">Alguns modelos (como **classe de inicialização OWIN**) não está disponível; nesses casos, use um arquivo de classe em vez disso.</span><span class="sxs-lookup"><span data-stu-id="dbac5-116">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="dbac5-117">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="dbac5-117">Questions and comments</span></span>
>
> <span data-ttu-id="dbac5-118">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="dbac5-118">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="dbac5-119">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="dbac5-119">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="dbac5-120">SignalR 2 oferece uma experiência de desenvolvimento consistente entre as plataformas de servidor usando [OWIN](http://owin.org).</span><span class="sxs-lookup"><span data-stu-id="dbac5-120">SignalR 2 offers a consistent development experience across server platforms using [OWIN](http://owin.org).</span></span> <span data-ttu-id="dbac5-121">Este artigo descreve as etapas que são necessárias para atualizar um aplicativo do 1.x SignalR para a versão 2.</span><span class="sxs-lookup"><span data-stu-id="dbac5-121">This article describes the few steps that are needed to update a SignalR 1.x application to version 2.</span></span>

<span data-ttu-id="dbac5-122">Enquanto ela é incentivada a atualizar aplicativos para o SignalR 2, o SignalR 1.x ainda ter suporte.</span><span class="sxs-lookup"><span data-stu-id="dbac5-122">While it is encouraged to upgrade applications to SignalR 2, SignalR 1.x will still be supported.</span></span>

<span data-ttu-id="dbac5-123">Este tutorial descreve como atualizar um aplicativo hospedado na web para o SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="dbac5-123">This tutorial describes how to upgrade a web-hosted application to SignalR 2.</span></span> <span data-ttu-id="dbac5-124">Agora há suporte para aplicativos auto-hospedados (aqueles que hospedam um servidor em um aplicativo de console, o serviço do Windows ou outro processo) em SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="dbac5-124">Self-hosted applications (those that host a server in a console application, Windows service, or other process) are now supported under SignalR 2.</span></span> <span data-ttu-id="dbac5-125">Para obter informações sobre como começar a criar um aplicativo hospedado internamente com SignalR 2, consulte [Tutorial: Auto-hospedar SignalR](../deployment/tutorial-signalr-self-host.md).</span><span class="sxs-lookup"><span data-stu-id="dbac5-125">For information on how to get started creating a self-hosted application with SignalR 2, see [Tutorial: SignalR Self-Host](../deployment/tutorial-signalr-self-host.md).</span></span>

## <a name="contents"></a><span data-ttu-id="dbac5-126">Conteúdo</span><span class="sxs-lookup"><span data-stu-id="dbac5-126">Contents</span></span>

<span data-ttu-id="dbac5-127">As seções a seguir descrevem as tarefas envolvidas com a atualização de projetos do SignalR e como solucionar problemas que podem surgir.</span><span class="sxs-lookup"><span data-stu-id="dbac5-127">The following sections describe tasks involved with upgrading SignalR projects, and how to troubleshoot issues that may arise.</span></span>

- [<span data-ttu-id="dbac5-128">Exemplo: Atualizando o tutorial de Introdução ao SignalR 2</span><span class="sxs-lookup"><span data-stu-id="dbac5-128">Example: Upgrading the Getting Started tutorial to SignalR 2</span></span>](#example)
- [<span data-ttu-id="dbac5-129">Solução de problemas de erros encontrados durante a atualização</span><span class="sxs-lookup"><span data-stu-id="dbac5-129">Troubleshooting errors encountered during upgrading</span></span>](#troubleshooting)

<a id="example"></a>

## <a name="example-upgrading-the-getting-started-tutorial-application-to-signalr-2"></a><span data-ttu-id="dbac5-130">Exemplo: Atualizar o aplicativo de tutorial de Introdução ao SignalR 2</span><span class="sxs-lookup"><span data-stu-id="dbac5-130">Example: Upgrading the Getting Started tutorial application to SignalR 2</span></span>

<span data-ttu-id="dbac5-131">Nesta seção, você atualizará o aplicativo criado na [versão do SignalR 1.x do Tutorial de Introdução](../older-versions/index.md) para usar o SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="dbac5-131">In this section, you'll update the application created in the [SignalR 1.x version of the Getting Started Tutorial](../older-versions/index.md) to use SignalR 2.</span></span>

1. <span data-ttu-id="dbac5-132">Depois que você concluiu o tutorial de Introdução, clique com botão direito no projeto e selecione **propriedades**.</span><span class="sxs-lookup"><span data-stu-id="dbac5-132">Once you've finished the Getting Started tutorial, right-click on the project, and select **Properties**.</span></span> <span data-ttu-id="dbac5-133">Verifique se que o **estrutura de destino** é definido como **.NET Framework 4.5.**</span><span class="sxs-lookup"><span data-stu-id="dbac5-133">Verify that the **Target framework** is set to **.NET Framework 4.5.**</span></span>
2. <span data-ttu-id="dbac5-134">Abra o Console do Gerenciador de pacotes.</span><span class="sxs-lookup"><span data-stu-id="dbac5-134">Open the Package Manager Console.</span></span> <span data-ttu-id="dbac5-135">Remover o SignalR 1.x do projeto usando o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="dbac5-135">Remove SignalR 1.x from the project using the following command:</span></span>

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample1.ps1)]
3. <span data-ttu-id="dbac5-136">Instale o SignalR 2 usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="dbac5-136">Install SignalR 2 using the following command:</span></span>

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample2.ps1)]
4. <span data-ttu-id="dbac5-137">Na página HTML, atualize a referência de script para o SignalR corresponder à versão do script agora está incluído no projeto.</span><span class="sxs-lookup"><span data-stu-id="dbac5-137">In the HTML page, update the script reference for SignalR to match the version of the script now included in the project.</span></span>

    [!code-html[Main](upgrading-signalr-1x-projects-to-20/samples/sample3.html)]
5. <span data-ttu-id="dbac5-138">Na classe de aplicativo global, remova a chamada para MapHubs.</span><span class="sxs-lookup"><span data-stu-id="dbac5-138">In the global application class, remove the call to MapHubs.</span></span>

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample4.cs)]
6. <span data-ttu-id="dbac5-139">A solução com o botão direito e selecione **Add**, **Novo Item...** . Na caixa de diálogo, selecione **classe de inicialização Owin**.</span><span class="sxs-lookup"><span data-stu-id="dbac5-139">Right-click the solution, and select **Add**, **New Item...**. In the dialog, select **Owin Startup Class**.</span></span> <span data-ttu-id="dbac5-140">Nomeie a nova classe **Startup.cs**.</span><span class="sxs-lookup"><span data-stu-id="dbac5-140">Name the new class **Startup.cs**.</span></span>

    ![](upgrading-signalr-1x-projects-to-20/_static/image1.png)
7. <span data-ttu-id="dbac5-141">Substitua o conteúdo do Startup.cs com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="dbac5-141">Replace the contents of Startup.cs with the following code:</span></span>

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample5.cs)]

    <span data-ttu-id="dbac5-142">O atributo de assembly adiciona a classe ao processo de inicialização do Owin, que executa o `Configuration` método quando Owin é iniciado.</span><span class="sxs-lookup"><span data-stu-id="dbac5-142">The assembly attribute adds the class to Owin's startup process, which executes the `Configuration` method when Owin starts up.</span></span> <span data-ttu-id="dbac5-143">Isso por sua vez chama o `MapSignalR` método, que cria as rotas para todos os hubs de SignalR no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dbac5-143">This in turn calls the `MapSignalR` method, which creates routes for all SignalR hubs in the application.</span></span>
8. <span data-ttu-id="dbac5-144">Execute o projeto e copie a URL da página principal em outro navegador ou no painel do navegador, como antes.</span><span class="sxs-lookup"><span data-stu-id="dbac5-144">Run the project, and copy the URL of the main page into another browser or browser pane, as before.</span></span> <span data-ttu-id="dbac5-145">Cada página solicitará um nome de usuário e as mensagens enviadas de cada página devem estar visíveis em ambos os painéis do navegador.</span><span class="sxs-lookup"><span data-stu-id="dbac5-145">Each page will ask for a username, and messages sent from each page should be visible in both browser panes.</span></span>

<a id="troubleshooting"></a>

## <a name="troubleshooting-errors-encountered-during-upgrading"></a><span data-ttu-id="dbac5-146">Solução de problemas de erros encontrados durante a atualização</span><span class="sxs-lookup"><span data-stu-id="dbac5-146">Troubleshooting errors encountered during upgrading</span></span>

<span data-ttu-id="dbac5-147">Esta seção descreve problemas que podem surgir durante a atualização.</span><span class="sxs-lookup"><span data-stu-id="dbac5-147">This section describes issues that may arise during upgrading.</span></span> <span data-ttu-id="dbac5-148">Para obter uma lista mais abrangente de erros e problemas que podem ocorrer com um aplicativo do SignalR, consulte [solução de problemas do SignalR](../testing-and-debugging/troubleshooting.md).</span><span class="sxs-lookup"><span data-stu-id="dbac5-148">For a more comprehensive list of errors and issues that may occur with a SignalR application, see [SignalR Troubleshooting](../testing-and-debugging/troubleshooting.md).</span></span>

### <a name="the-call-is-ambiguous-between-the-following-methods-or-properties"></a><span data-ttu-id="dbac5-149">'A chamada é ambígua entre os seguintes métodos ou propriedades'</span><span class="sxs-lookup"><span data-stu-id="dbac5-149">'The call is ambiguous between the following methods or properties'</span></span>

<span data-ttu-id="dbac5-150">Esse erro ocorrerá se uma referência a `Microsoft.AspNet.SignalR.Owin` não é removido.</span><span class="sxs-lookup"><span data-stu-id="dbac5-150">This error will occur if a reference to `Microsoft.AspNet.SignalR.Owin` is not removed.</span></span> <span data-ttu-id="dbac5-151">Esse pacote foi preterido; a referência deve ser removida e a versão 1.x do pacote SelfHost deve ser desinstalada.</span><span class="sxs-lookup"><span data-stu-id="dbac5-151">This package is deprecated; the reference must be removed and the 1.x version of the SelfHost package must be uninstalled.</span></span>

### <a name="hub-methods-fail-silently"></a><span data-ttu-id="dbac5-152">Métodos de Hub falharem em modo silencioso</span><span class="sxs-lookup"><span data-stu-id="dbac5-152">Hub methods fail silently</span></span>

<span data-ttu-id="dbac5-153">Verifique se as referências de script no seu cliente estão atualizadas e que o `OwinStartup` de atributo para a sua classe de inicialização tem a classe correta e nomes de assembly para o seu projeto.</span><span class="sxs-lookup"><span data-stu-id="dbac5-153">Verify that the script references in your client are up to date, and that the `OwinStartup` attribute for your Startup class has the correct class and assembly names for your project.</span></span> <span data-ttu-id="dbac5-154">Além disso, tente abrir o endereço de hubs (hubs do signalr /) no seu navegador. qualquer erro que aparece se oferecerá para obter mais informações sobre o que está errado.</span><span class="sxs-lookup"><span data-stu-id="dbac5-154">Also, try opening up the hubs address (/signalr/hubs) in your browser; any error that appears will offer more information about what's going wrong.</span></span>
