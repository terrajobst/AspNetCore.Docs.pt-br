---
uid: signalr/overview/deployment/using-signalr-with-azure-web-sites
title: Usando o SignalR com aplicativos Web no serviço de aplicativo do Azure | Microsoft Docs
author: bradygaster
description: Este documento descreve como configurar um aplicativo de SignalR é executado no Microsoft Azure. Versões de software usado no tutorial, o Visual Studio 2013 ou Vis....
ms.author: bradyg
ms.date: 07/01/2015
ms.assetid: 2a7517a0-b88c-4162-ade3-9bf6ca7062fd
msc.legacyurl: /signalr/overview/deployment/using-signalr-with-azure-web-sites
msc.type: authoredcontent
ms.openlocfilehash: 13eb5d29a2c40f52aed4b569ec8695f014a05f03
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837696"
---
<a name="using-signalr-with-web-apps-in-azure-app-service"></a><span data-ttu-id="dbb4e-104">Usando o SignalR com aplicativos Web no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="dbb4e-104">Using SignalR with Web Apps in Azure App Service</span></span>
====================
<span data-ttu-id="dbb4e-105">por [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="dbb4e-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="dbb4e-106">Este documento descreve como configurar um aplicativo de SignalR é executado no Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-106">This document describes how to configure a SignalR application that runs on Microsoft Azure.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="dbb4e-107">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="dbb4e-107">Software versions used in the tutorial</span></span>
>
>
> - <span data-ttu-id="dbb4e-108">[Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013) ou o Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="dbb4e-108">[Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013) or Visual Studio 2012</span></span>
> - <span data-ttu-id="dbb4e-109">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="dbb4e-109">.NET 4.5</span></span>
> - <span data-ttu-id="dbb4e-110">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="dbb4e-110">SignalR version 2</span></span>
> - <span data-ttu-id="dbb4e-111">SDK 2.3 do Azure para Visual Studio 2013 ou 2012</span><span class="sxs-lookup"><span data-stu-id="dbb4e-111">Azure SDK 2.3 for Visual Studio 2013 or 2012</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="dbb4e-112">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="dbb4e-112">Questions and comments</span></span>
>
> <span data-ttu-id="dbb4e-113">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-113">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="dbb4e-114">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR), [StackOverflow.com](http://stackoverflow.com/), ou o [fóruns do Microsoft Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).</span><span class="sxs-lookup"><span data-stu-id="dbb4e-114">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR), [StackOverflow.com](http://stackoverflow.com/), or the [Microsoft Azure forums](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).</span></span>


## <a name="table-of-contents"></a><span data-ttu-id="dbb4e-115">Sumário</span><span class="sxs-lookup"><span data-stu-id="dbb4e-115">Table of Contents</span></span>

- [<span data-ttu-id="dbb4e-116">Introdução</span><span class="sxs-lookup"><span data-stu-id="dbb4e-116">Introduction</span></span>](#introduction)
- [<span data-ttu-id="dbb4e-117">Implantando um aplicativo Web do SignalR para o serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="dbb4e-117">Deploying a SignalR Web App to Azure App Service</span></span>](#deploying)
- [<span data-ttu-id="dbb4e-118">Habilitar WebSockets no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="dbb4e-118">Enabling WebSockets on Azure App Service</span></span>](#websocket)
- [<span data-ttu-id="dbb4e-119">Usando o Backplane de Cache Redis do Azure</span><span class="sxs-lookup"><span data-stu-id="dbb4e-119">Using the Azure Redis Cache Backplane</span></span>](#backplane)
- [<span data-ttu-id="dbb4e-120">Próximas Etapas</span><span class="sxs-lookup"><span data-stu-id="dbb4e-120">Next Steps</span></span>](#nextsteps)

<a id="introduction"></a>
## <a name="introduction"></a><span data-ttu-id="dbb4e-121">Introdução</span><span class="sxs-lookup"><span data-stu-id="dbb4e-121">Introduction</span></span>

<span data-ttu-id="dbb4e-122">SignalR do ASP.NET pode ser usado para colocar um novo nível de interatividade entre os servidores e web ou de clientes .NET.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-122">ASP.NET SignalR can be used to bring a new level of interactivity between servers and web or .NET clients.</span></span> <span data-ttu-id="dbb4e-123">Quando hospedado no Azure, aplicativos do SignalR podem tirar proveito de altamente disponível, escalonável, e o ambiente de alto desempenho em execução na nuvem fornece.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-123">When hosted in Azure, SignalR applications can take advantage of the highly available, scalable, and performant environment that running in the cloud provides.</span></span>

<a id="deploying"></a>
## <a name="deploying-a-signalr-web-app-to-azure-app-service"></a><span data-ttu-id="dbb4e-124">Implantando um aplicativo Web do SignalR para o serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="dbb4e-124">Deploying a SignalR Web App to Azure App Service</span></span>

<span data-ttu-id="dbb4e-125">SignalR não adiciona qualquer complicação específica para implantar um aplicativo do Azure versus implantando em um servidor local.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-125">SignalR doesn't add any particular complications to deploying an application to Azure versus deploying to an on-premises server.</span></span> <span data-ttu-id="dbb4e-126">Um aplicativo que usa o SignalR pode ser hospedado no Azure sem qualquer alteração na configuração ou outras configurações (embora para suporte de WebSockets, consulte [habilitar WebSockets no serviço de aplicativo do Azure](#websocket) abaixo.) Para este tutorial, você implantará o aplicativo criado na [Tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md) para o Azure.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-126">An application that uses SignalR can be hosted in Azure without any changes in configuration or other settings (though for WebSockets support, see [Enabling WebSockets on Azure App Service](#websocket) below.) For this tutorial, you'll deploy the application created in the [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md) to Azure.</span></span>

<span data-ttu-id="dbb4e-127">**Pré-requisitos**</span><span class="sxs-lookup"><span data-stu-id="dbb4e-127">**Prerequisites**</span></span>

- <span data-ttu-id="dbb4e-128">Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-128">Visual Studio 2013.</span></span> <span data-ttu-id="dbb4e-129">Se você não tiver o Visual Studio, Visual Studio 2013 Express para Web está incluído na instalação do SDK do Azure.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-129">If you don't have Visual Studio, Visual Studio 2013 Express for Web is included in the install of the Azure SDK.</span></span>
- <span data-ttu-id="dbb4e-130">[SDK 2.3 do Azure para Visual Studio 2013](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) ou [SDK 2.3 do Azure para Visual Studio 2012](https://go.microsoft.com/fwlink/p/?linkid=323511).</span><span class="sxs-lookup"><span data-stu-id="dbb4e-130">[Azure SDK 2.3 for Visual Studio 2013](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) or [Azure SDK 2.3 for Visual Studio 2012](https://go.microsoft.com/fwlink/p/?linkid=323511).</span></span>
- <span data-ttu-id="dbb4e-131">Para concluir este tutorial, você precisará de uma assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-131">To complete this tutorial, you will need an Azure subscription.</span></span> <span data-ttu-id="dbb4e-132">Você pode [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), ou [Inscreva-se para uma assinatura de avaliação](https://azure.microsoft.com/pricing/free-trial/).</span><span class="sxs-lookup"><span data-stu-id="dbb4e-132">You can [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), or [sign up for a trial subscription](https://azure.microsoft.com/pricing/free-trial/).</span></span>

### <a name="deploying-a-signalr-web-app-to-azure"></a><span data-ttu-id="dbb4e-133">Implantar um aplicativo web de SignalR no Azure</span><span class="sxs-lookup"><span data-stu-id="dbb4e-133">Deploying a SignalR web app to Azure</span></span>

1. <span data-ttu-id="dbb4e-134">Conclua o [Tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md), ou baixe o projeto concluído na [Galeria de códigos](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span><span class="sxs-lookup"><span data-stu-id="dbb4e-134">Complete the [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md), or download the finished project from [Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span></span>
2. <span data-ttu-id="dbb4e-135">No Visual Studio, selecione **construir**, **publicar SignalR bate-papo**.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-135">In Visual Studio, select **Build**, **Publish SignalR Chat**.</span></span>
3. <span data-ttu-id="dbb4e-136">Na caixa de diálogo "Publicar na Web", selecione "Windows Azure Web Sites".</span><span class="sxs-lookup"><span data-stu-id="dbb4e-136">In the "Publish Web" dialog, select "Windows Azure Web Sites".</span></span>

    ![Selecione os Sites do Azure](using-signalr-with-azure-web-sites/_static/image1.png)
4. <span data-ttu-id="dbb4e-138">Se você não estiver conectado à sua conta da Microsoft, clique em **entrar...**  na caixa de diálogo "Selecionar Site existente" e de entrada.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-138">If you aren't signed in to your Microsoft account, click **Sign In...** in the "Select Existing Web Site" dialog, and sign in.</span></span>

    ![Selecione o Site da Web existente](using-signalr-with-azure-web-sites/_static/image2.png)    ![Entrar no Azure](using-signalr-with-azure-web-sites/_static/image3.png)
5. <span data-ttu-id="dbb4e-141">Na caixa de diálogo "Selecionar Site existente", clique em **New**.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-141">In the "Select Existing Web Site" dialog, click **New**.</span></span>

    ![Novo Site](using-signalr-with-azure-web-sites/_static/image4.png)
6. <span data-ttu-id="dbb4e-143">Na caixa de diálogo "Criar site no Windows Azure", insira um nome exclusivo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-143">In the "Create site on Windows Azure" dialog, enter a unique app name.</span></span> <span data-ttu-id="dbb4e-144">Selecione a região mais próxima a você no menu suspenso de região.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-144">Select the region closest to you in the Region dropdown.</span></span> <span data-ttu-id="dbb4e-145">Clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-145">Click **Create**.</span></span>

    ![Criar site no Azure](using-signalr-with-azure-web-sites/_static/image5.png)
7. <span data-ttu-id="dbb4e-147">Na caixa de diálogo "Publicar na Web", clique em **publicar**.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-147">In the "Publish Web" dialog, click **Publish**.</span></span>

    ![Publicar site](using-signalr-with-azure-web-sites/_static/image6.png)
8. <span data-ttu-id="dbb4e-149">Quando o aplicativo tiver concluído a publicação, o aplicativo de SignalR Chat hospedado em aplicativos de Web do serviço de aplicativo do Azure será aberto em um navegador.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-149">When the app has completed publishing, the SignalR Chat application hosted in Azure App Service Web Apps will open in a browser.</span></span>

    ![Abrir em um navegador de site](using-signalr-with-azure-web-sites/_static/image7.png)

<a id="websocket"></a>
### <a name="enabling-websockets-on-azure-app-service-web-apps"></a><span data-ttu-id="dbb4e-151">Habilitar WebSockets em aplicativos Web do serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="dbb4e-151">Enabling WebSockets on Azure App Service Web Apps</span></span>

<span data-ttu-id="dbb4e-152">O WebSocket precisa ser habilitado explicitamente em seu aplicativo web a ser usado em um aplicativo do SignalR; Caso contrário, outros protocolos serão usados (consulte [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports) para obter detalhes).</span><span class="sxs-lookup"><span data-stu-id="dbb4e-152">WebSockets needs to be explicitly enabled in your web app to be used in a SignalR application; otherwise, other protocols will be used (See [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports) for details).</span></span>

<span data-ttu-id="dbb4e-153">Para usar o WebSockets em aplicativos de Web do serviço de aplicativo do Azure, você precisa habilitá-la na seção de configuração do aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-153">In order to use WebSockets on Azure App Service Web Apps, enable it in the configuration section of the web app.</span></span> <span data-ttu-id="dbb4e-154">Para fazer isso, abra seu aplicativo web na [Portal de gerenciamento](https://manage.windowsazure.com/)e selecione Configure.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-154">To do this, open your web app in the [Azure Management Portal](https://manage.windowsazure.com/), and select Configure.</span></span>

![Guia Configurar](using-signalr-with-azure-web-sites/_static/image8.png)

<span data-ttu-id="dbb4e-156">Na parte superior da página de configuração, certifique-se de que o .NET 4.5 é usado para seu aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-156">At the top of the configuration page, ensure that .NET 4.5 is used for your web app.</span></span>

![Configuração do .NET framework versão 4.5](using-signalr-with-azure-web-sites/_static/image9.png)

<span data-ttu-id="dbb4e-158">Na página de configuração, nos **WebSockets** configuração, selecione **em**.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-158">On the configuration page, in the **WebSockets** setting, select **On**.</span></span>

![Configuração de WebSockets: On](using-signalr-with-azure-web-sites/_static/image10.png)

<span data-ttu-id="dbb4e-160">Na parte inferior da página de configuração, selecione **salvar** para salvar suas alterações.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-160">At the bottom of the Configuration page, select **Save** to save your changes.</span></span>

![Salvar configurações](using-signalr-with-azure-web-sites/_static/image11.png)

<a id="backplane"></a>
## <a name="using-the-azure-redis-cache-backplane"></a><span data-ttu-id="dbb4e-162">Usando o Backplane de Cache Redis do Azure</span><span class="sxs-lookup"><span data-stu-id="dbb4e-162">Using the Azure Redis Cache Backplane</span></span>

<span data-ttu-id="dbb4e-163">Se você usar várias instâncias do aplicativo web e os usuários dessas instâncias precisam interagir entre si (de modo que, por exemplo, as mensagens de bate-papo criadas em uma instância podem alcançar os usuários conectados a outras instâncias), o [Cache Redis do Azure backplane](../performance/scaleout-with-redis.md) devem ser implementados em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dbb4e-163">If you use multiple instances for your web app, and the users of those instances need to interact with one another (so that, for instance, chat messages created in one instance can reach the users connected to other instances), the [Azure Redis Cache backplane](../performance/scaleout-with-redis.md) must be implemented in your application.</span></span>

<a id="nextsteps"></a>
## <a name="next-steps"></a><span data-ttu-id="dbb4e-164">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="dbb4e-164">Next Steps</span></span>

<span data-ttu-id="dbb4e-165">Para obter mais informações sobre aplicativos Web no serviço de aplicativo do Azure, consulte [visão geral de aplicativos Web](https://azure.microsoft.com/documentation/articles/app-service-web-overview/).</span><span class="sxs-lookup"><span data-stu-id="dbb4e-165">For more information on Web Apps in Azure App Service, see [Web Apps overview](https://azure.microsoft.com/documentation/articles/app-service-web-overview/).</span></span>
