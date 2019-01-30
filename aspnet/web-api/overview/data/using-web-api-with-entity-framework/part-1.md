---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: Usando a API Web 2 com o Entity Framework 6 | Microsoft Docs
author: MikeWasson
description: Este tutorial ensinará os fundamentos da criação de um aplicativo web com uma API Web ASP.NET de back-end. O tutorial usa o Entity Framework 6 para o layout de dados...
ms.author: riande
ms.date: 01/17/2019
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: 266c808e3525787181038d2de473194989039e02
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236517"
---
<a name="using-web-api-2-with-entity-framework-6"></a><span data-ttu-id="57c01-104">Usando a API Web 2 com o Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="57c01-104">Using Web API 2 with Entity Framework 6</span></span>
====================

[<span data-ttu-id="57c01-105">Baixe o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="57c01-105">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

> <span data-ttu-id="57c01-106">Este tutorial ensina as Noções básicas da criação de um aplicativo web com uma API Web ASP.NET de back-end.</span><span class="sxs-lookup"><span data-stu-id="57c01-106">This tutorial teaches you the basics of creating a web application with an ASP.NET Web API back end.</span></span> <span data-ttu-id="57c01-107">O tutorial usa o Entity Framework 6 para a camada de dados e o Knockout. js para o aplicativo de JavaScript do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="57c01-107">The tutorial uses Entity Framework 6 for the data layer, and Knockout.js for the client-side JavaScript application.</span></span> <span data-ttu-id="57c01-108">O tutorial também mostra como implantar o aplicativo em aplicativos de Web do serviço de aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="57c01-108">The tutorial also shows how to deploy the app to Azure App Service Web Apps.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="57c01-109">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="57c01-109">Software versions used in the tutorial</span></span>
>
> - <span data-ttu-id="57c01-110">API Web 2.1</span><span class="sxs-lookup"><span data-stu-id="57c01-110">Web API 2.1</span></span>
> - <span data-ttu-id="57c01-111">Visual Studio 2017 (Baixe o Visual Studio 2017 [aqui](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))</span><span class="sxs-lookup"><span data-stu-id="57c01-111">Visual Studio 2017 (download Visual Studio 2017 [here](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))</span></span>
> - <span data-ttu-id="57c01-112">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="57c01-112">Entity Framework 6</span></span>
> - <span data-ttu-id="57c01-113">.NET 4.7</span><span class="sxs-lookup"><span data-stu-id="57c01-113">.NET 4.7</span></span>
> - <span data-ttu-id="57c01-114">[Knockout.js](http://knockoutjs.com/) 3.1</span><span class="sxs-lookup"><span data-stu-id="57c01-114">[Knockout.js](http://knockoutjs.com/) 3.1</span></span>

<span data-ttu-id="57c01-115">Este tutorial usa a API Web ASP.NET 2 com o Entity Framework 6 para criar um aplicativo web que manipula um banco de dados de back-end.</span><span class="sxs-lookup"><span data-stu-id="57c01-115">This tutorial uses ASP.NET Web API 2 with Entity Framework 6 to create a web application that manipulates a back-end database.</span></span> <span data-ttu-id="57c01-116">Aqui está uma captura de tela do aplicativo que você criará.</span><span class="sxs-lookup"><span data-stu-id="57c01-116">Here is a screen shot of the application that you will create.</span></span>

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

<span data-ttu-id="57c01-117">O aplicativo usa um design de aplicativo de página única (SPA).</span><span class="sxs-lookup"><span data-stu-id="57c01-117">The app uses a single-page application (SPA) design.</span></span> <span data-ttu-id="57c01-118">"Aplicativo de página única" é o termo geral para um aplicativo web que carrega uma página HTML única e, em seguida, atualiza a página dinamicamente, em vez de carregar novas páginas.</span><span class="sxs-lookup"><span data-stu-id="57c01-118">"Single-page application" is the general term for a web application that loads a single HTML page and then updates the page dynamically, instead of loading new pages.</span></span> <span data-ttu-id="57c01-119">Após o carregamento de página inicial, o aplicativo se comunica com o servidor por meio de solicitações AJAX.</span><span class="sxs-lookup"><span data-stu-id="57c01-119">After the initial page load, the app talks with the server through AJAX requests.</span></span> <span data-ttu-id="57c01-120">O AJAX solicita dados JSON retornados, o que o aplicativo usa para atualizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="57c01-120">The AJAX requests return JSON data, which the app uses to update the UI.</span></span>

<span data-ttu-id="57c01-121">AJAX não é novo, mas hoje, há estruturas de JavaScript que facilitam criar e manter um grande aplicativo SPA sofisticado.</span><span class="sxs-lookup"><span data-stu-id="57c01-121">AJAX isn't new, but today there are JavaScript frameworks that make it easier to build and maintain a large sophisticated SPA application.</span></span> <span data-ttu-id="57c01-122">Este tutorial usa [Knockout. js](http://knockoutjs.com/), mas você pode usar qualquer estrutura de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="57c01-122">This tutorial uses [Knockout.js](http://knockoutjs.com/), but you can use any JavaScript client framework.</span></span>

<span data-ttu-id="57c01-123">Aqui estão os principais blocos de construção para este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="57c01-123">Here are the main building blocks for this app:</span></span>

- <span data-ttu-id="57c01-124">ASP.NET MVC cria a página HTML.</span><span class="sxs-lookup"><span data-stu-id="57c01-124">ASP.NET MVC creates the HTML page.</span></span>
- <span data-ttu-id="57c01-125">API Web ASP.NET manipula as solicitações AJAX e retorna dados JSON.</span><span class="sxs-lookup"><span data-stu-id="57c01-125">ASP.NET Web API handles the AJAX requests and returns JSON data.</span></span>
- <span data-ttu-id="57c01-126">Knockout. js-associa dados os elementos HTML para os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="57c01-126">Knockout.js data-binds the HTML elements to the JSON data.</span></span>
- <span data-ttu-id="57c01-127">Entity Framework se comunica com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="57c01-127">Entity Framework talks to the database.</span></span>

## <a name="see-this-app-running-on-azure"></a><span data-ttu-id="57c01-128">Consulte esse aplicativo em execução no Azure</span><span class="sxs-lookup"><span data-stu-id="57c01-128">See this app running on Azure</span></span>

<span data-ttu-id="57c01-129">Você gostaria de ver o site concluído em execução como um aplicativo web ao vivo?</span><span class="sxs-lookup"><span data-stu-id="57c01-129">Would you like to see the finished site running as a live web app?</span></span> <span data-ttu-id="57c01-130">Você pode implantar uma versão completa do aplicativo para sua conta do Azure, selecionando o botão a seguir.</span><span class="sxs-lookup"><span data-stu-id="57c01-130">You can deploy a complete version of the app to your Azure account by selecting the following button.</span></span>

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

<span data-ttu-id="57c01-131">Você precisa de uma conta do Azure para implantar essa solução no Azure.</span><span class="sxs-lookup"><span data-stu-id="57c01-131">You need an Azure account to deploy this solution to Azure.</span></span> <span data-ttu-id="57c01-132">Se você ainda não tiver uma conta, você tem as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="57c01-132">If you do not already have an account, you have the following options:</span></span>

- <span data-ttu-id="57c01-133">[Abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) -obtenha créditos você pode usar para experimentar serviços pagos do Azure e até mesmo após eles serem utilizados, você pode manter a conta e utilizar os serviços do Azure gratuitos.</span><span class="sxs-lookup"><span data-stu-id="57c01-133">[Open an Azure account for free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) - You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services.</span></span>
- <span data-ttu-id="57c01-134">[Ativar benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) -sua assinatura do MSDN concede créditos todos os meses que você pode usar para serviços pagos do Azure.</span><span class="sxs-lookup"><span data-stu-id="57c01-134">[Activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) - Your MSDN subscription gives you credits every month that you can use for paid Azure services.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="57c01-135">Criar o projeto</span><span class="sxs-lookup"><span data-stu-id="57c01-135">Create the project</span></span>

<span data-ttu-id="57c01-136">Abra o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="57c01-136">Open Visual Studio.</span></span> <span data-ttu-id="57c01-137">Dos **arquivo** menu, selecione **New**, em seguida, selecione **projeto**.</span><span class="sxs-lookup"><span data-stu-id="57c01-137">From the **File** menu, select **New**, then select **Project**.</span></span> <span data-ttu-id="57c01-138">(Ou selecione **novo projeto** na página inicial.)</span><span class="sxs-lookup"><span data-stu-id="57c01-138">(Or select **New Project** on the Start page.)</span></span>

<span data-ttu-id="57c01-139">No **novo projeto** caixa de diálogo, selecione **Web** no painel esquerdo e **aplicativo Web ASP.NET (.NET Framework)** no painel central.</span><span class="sxs-lookup"><span data-stu-id="57c01-139">In the **New Project** dialog, select **Web** in the left pane and **ASP.NET Web Application (.NET Framework)** in the middle pane.</span></span> <span data-ttu-id="57c01-140">Nomeie o projeto **BookService** e selecione **Okey**.</span><span class="sxs-lookup"><span data-stu-id="57c01-140">Name the project **BookService** and select **OK**.</span></span>

[![](part-1/_static/image11.png)](part-1/_static/image11.png)

<span data-ttu-id="57c01-141">No **novo projeto ASP.NET** caixa de diálogo, selecione o **API da Web** modelo.</span><span class="sxs-lookup"><span data-stu-id="57c01-141">In the **New ASP.NET Project** dialog, select the **Web API** template.</span></span>

[![](part-1/_static/image12.png)](part-1/_static/image12.png)


<span data-ttu-id="57c01-142">Selecione **OK** para criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="57c01-142">Select **OK** to create the project.</span></span>

## <a name="configure-azure-settings-optional"></a><span data-ttu-id="57c01-143">Definir configurações do Azure (opcional)</span><span class="sxs-lookup"><span data-stu-id="57c01-143">Configure Azure settings (optional)</span></span>

<span data-ttu-id="57c01-144">Depois de criar o projeto, você pode optar por implantar aplicativos de Web do serviço de aplicativo do Azure a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="57c01-144">After you create the project, you can choose to deploy to Azure App Service Web Apps at any time.</span></span> 

1. <span data-ttu-id="57c01-145">No Gerenciador de soluções, clique com botão direito no seu projeto e selecione **publicar**.</span><span class="sxs-lookup"><span data-stu-id="57c01-145">In Solution Explorer, right-click on your project and select **Publish**.</span></span>

2. <span data-ttu-id="57c01-146">Na janela que aparece, selecione **iniciar**.</span><span class="sxs-lookup"><span data-stu-id="57c01-146">In the window that appears, select **Start**.</span></span> <span data-ttu-id="57c01-147">O **escolher um destino de publicação** caixa de diálogo é exibida.</span><span class="sxs-lookup"><span data-stu-id="57c01-147">The **Pick a publish target** dialog box appears.</span></span>

   [![](part-1/_static/image14.png)](part-1/_static/image14.png)

3. <span data-ttu-id="57c01-148">Selecione **Criar Perfil**.</span><span class="sxs-lookup"><span data-stu-id="57c01-148">Select **Create Profile**.</span></span> <span data-ttu-id="57c01-149">A caixa de diálogo **Criar Serviço de Aplicativo** é exibida.</span><span class="sxs-lookup"><span data-stu-id="57c01-149">The **Create App Service** dialog box appears.</span></span>

   [![](part-1/_static/image15.png)](part-1/_static/image15.png)

   <span data-ttu-id="57c01-150">Aceite os padrões ou inserir valores diferentes para o nome do aplicativo, grupo de recursos, hospedagem de plano, a assinatura do Azure e a região geográfica.</span><span class="sxs-lookup"><span data-stu-id="57c01-150">Accept the defaults, or enter different values for the application name, resource group, hosting plan, Azure subscription, and geographical region.</span></span> 

4. <span data-ttu-id="57c01-151">Selecione **criar um banco de dados SQL**.</span><span class="sxs-lookup"><span data-stu-id="57c01-151">Select **Create a SQL database**.</span></span> <span data-ttu-id="57c01-152">O **configurar o SQL Server** caixa de diálogo é exibida.</span><span class="sxs-lookup"><span data-stu-id="57c01-152">The **Configure SQL Server** dialog box appears.</span></span> 

   [![](part-1/_static/image16.png)](part-1/_static/image16.png)

   <span data-ttu-id="57c01-153">Aceite os padrões ou inserir valores diferentes.</span><span class="sxs-lookup"><span data-stu-id="57c01-153">Accept the defaults or enter different values.</span></span> <span data-ttu-id="57c01-154">Insira um **Adminstrator Username** e **senha de administrador** para seu novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="57c01-154">Enter an **Adminstrator Username** and **Administrator Password** for your new database.</span></span> <span data-ttu-id="57c01-155">Selecione **Okey** quando você terminar.</span><span class="sxs-lookup"><span data-stu-id="57c01-155">Select **OK** when you're done.</span></span> <span data-ttu-id="57c01-156">O **criar serviço de aplicativo** página será exibida novamente.</span><span class="sxs-lookup"><span data-stu-id="57c01-156">The **Create App Service** page reappears.</span></span>

5. <span data-ttu-id="57c01-157">Selecione **criar** para criar seu perfil.</span><span class="sxs-lookup"><span data-stu-id="57c01-157">Select **Create** to create your profile.</span></span> <span data-ttu-id="57c01-158">Uma mensagem é exibida no canto inferior direito, indicando que a implantação está em andamento.</span><span class="sxs-lookup"><span data-stu-id="57c01-158">A message appears in the lower-right corner indicating that deployment is in progress.</span></span> <span data-ttu-id="57c01-159">Após um curto tempo, o **publicar** janela será exibida novamente.</span><span class="sxs-lookup"><span data-stu-id="57c01-159">After a short while, the **Publish** window reappears.</span></span>

    [![](part-1/_static/image17.png)](part-1/_static/image17.png)
   
    <span data-ttu-id="57c01-160">O perfil que você criou para implantar o aplicativo agora está disponível.</span><span class="sxs-lookup"><span data-stu-id="57c01-160">The profile you created to deploy the app is now available.</span></span> 


> [!div class="step-by-step"]
> [<span data-ttu-id="57c01-161">Avançar</span><span class="sxs-lookup"><span data-stu-id="57c01-161">Next</span></span>](part-2.md)
