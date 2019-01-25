---
title: Publicar um ASP.NET Core SignalR aplicativo ao aplicativo Web do Azure
author: bradygaster
description: Publicar um ASP.NET Core SignalR aplicativo ao aplicativo Web do Azure
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/20/2018
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 66fa855590c49c4284e4b42cae57f3d4d81dd0fc
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837670"
---
# <a name="publish-an-aspnet-core-signalr-app-to-an-azure-web-app"></a><span data-ttu-id="0a969-103">Publicar um ASP.NET Core aplicativo SignalR em um aplicativo Web do Azure</span><span class="sxs-lookup"><span data-stu-id="0a969-103">Publish an ASP.NET Core SignalR app to an Azure Web App</span></span>

<span data-ttu-id="0a969-104">[Aplicativo Web do Azure](/azure/app-service/app-service-web-overview) é um [a computação em nuvem do Microsoft](https://azure.microsoft.com/) plataforma de serviço para hospedar aplicativos web, incluindo o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a969-104">[Azure Web App](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="0a969-105">Este artigo refere-se a publicação de um aplicativo de SignalR do ASP.NET Core no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0a969-105">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="0a969-106">Visite [serviço SignalR para o Azure](https://azure.microsoft.com/en-gb/services/signalr-service?) para obter mais informações sobre como usar o SignalR no Azure.</span><span class="sxs-lookup"><span data-stu-id="0a969-106">Visit [SignalR service for Azure](https://azure.microsoft.com/en-gb/services/signalr-service?) for more information about using SignalR on Azure.</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="0a969-107">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0a969-107">Publish the app</span></span>

<span data-ttu-id="0a969-108">Visual Studio fornece ferramentas internas para publicação de um aplicativo da Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="0a969-108">Visual Studio provides built-in tools for publishing to an Azure Web App.</span></span> <span data-ttu-id="0a969-109">Usuário do Visual Studio Code pode usar [CLI do Azure](/cli/azure) comandos para publicar aplicativos no Azure.</span><span class="sxs-lookup"><span data-stu-id="0a969-109">Visual Studio Code user can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="0a969-110">Este artigo aborda a publicação usando as ferramentas do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0a969-110">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="0a969-111">Para publicar um aplicativo usando a CLI do Azure, consulte [publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="0a969-111">To publish an app using Azure CLI, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

<span data-ttu-id="0a969-112">Clique com botão direito no projeto no **Gerenciador de soluções** e selecione **publicar**.</span><span class="sxs-lookup"><span data-stu-id="0a969-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span> <span data-ttu-id="0a969-113">Confirme **criar novo** check-in a **escolher um destino de publicação** caixa de diálogo e selecione **publicar**.</span><span class="sxs-lookup"><span data-stu-id="0a969-113">Confirm that **Create new** is checked in the **Pick a publish target** dialog, and select **Publish**.</span></span>

![Escolha o destino de publicação](publish-to-azure-web-app/_static/pick-publish-target-dialog.png)

<span data-ttu-id="0a969-115">Insira as seguintes informações na **criar serviço de aplicativo** caixa de diálogo e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="0a969-115">Enter the following information in the **Create App Service** dialog and select **Create**.</span></span>

| <span data-ttu-id="0a969-116">Item</span><span class="sxs-lookup"><span data-stu-id="0a969-116">Item</span></span> | <span data-ttu-id="0a969-117">Descrição</span><span class="sxs-lookup"><span data-stu-id="0a969-117">Description</span></span> |
| ---- | ----------- |
| <span data-ttu-id="0a969-118">**Nome do aplicativo**</span><span class="sxs-lookup"><span data-stu-id="0a969-118">**App name**</span></span> | <span data-ttu-id="0a969-119">Um nome exclusivo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0a969-119">A unique name of the app.</span></span> |
| <span data-ttu-id="0a969-120">**Assinatura**</span><span class="sxs-lookup"><span data-stu-id="0a969-120">**Subscription**</span></span> | <span data-ttu-id="0a969-121">A assinatura do Azure que o aplicativo usa.</span><span class="sxs-lookup"><span data-stu-id="0a969-121">The Azure subscription that the app uses.</span></span> |
| <span data-ttu-id="0a969-122">**Grupo de recursos**</span><span class="sxs-lookup"><span data-stu-id="0a969-122">**Resource Group**</span></span> | <span data-ttu-id="0a969-123">O grupo de recursos relacionados ao qual pertence o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0a969-123">The group of related resources to which the app belongs.</span></span>  |
| <span data-ttu-id="0a969-124">**Plano de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="0a969-124">**Hosting Plan**</span></span> | <span data-ttu-id="0a969-125">O plano de preços para o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="0a969-125">The pricing plan for the web app.</span></span> |

![Criar serviço de aplicativo](publish-to-azure-web-app/_static/create-app-service-dialog.png)

<span data-ttu-id="0a969-127">Visual Studio conclui as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="0a969-127">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="0a969-128">Cria um perfil de publicação que contém as configurações de publicação.</span><span class="sxs-lookup"><span data-stu-id="0a969-128">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="0a969-129">Cria ou usa um existente *aplicativo Web do Azure* com os detalhes fornecidos.</span><span class="sxs-lookup"><span data-stu-id="0a969-129">Creates or uses an existing *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="0a969-130">Publica o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0a969-130">Publishes the app.</span></span>
* <span data-ttu-id="0a969-131">Inicia um navegador, com o aplicativo web publicado carregado.</span><span class="sxs-lookup"><span data-stu-id="0a969-131">Launches a browser, with the published web app loaded.</span></span>

<span data-ttu-id="0a969-132">Observe o formato da URL para o aplicativo é *amp;#42;.azurewebsites.NET {nome do aplicativo} .net*.</span><span class="sxs-lookup"><span data-stu-id="0a969-132">Notice the format of the URL for the app is *{app name}.azurewebsites.net*.</span></span> <span data-ttu-id="0a969-133">Por exemplo, um aplicativo chamado `SignalRChattR` tem uma URL que se parece com `https://signalrchattr.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="0a969-133">For example, an app named `SignalRChattR` has a URL that looks like `https://signalrchattr.azurewebsites.net`.</span></span>

<span data-ttu-id="0a969-134">Se ocorrer um erro de HTTP 502.2, consulte [versão de visualização de implantar o ASP.NET Core no serviço de aplicativo do Azure](xref:host-and-deploy/azure-apps/index) resolvê-lo.</span><span class="sxs-lookup"><span data-stu-id="0a969-134">If an HTTP 502.2 error occurs, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index) to resolve it.</span></span>

## <a name="configure-signalr-web-app"></a><span data-ttu-id="0a969-135">Configurar o aplicativo web de SignalR</span><span class="sxs-lookup"><span data-stu-id="0a969-135">Configure SignalR web app</span></span>

<span data-ttu-id="0a969-136">Aplicativos do ASP.NET SignalR Core que são publicados como um aplicativo Web deve ter [afinidade ARR](https://en.wikipedia.org/wiki/Application_Request_Routing) habilitado.</span><span class="sxs-lookup"><span data-stu-id="0a969-136">ASP.NET Core SignalR apps that are published as an Azure Web App must have [ARR Affinity](https://en.wikipedia.org/wiki/Application_Request_Routing) enabled.</span></span> <span data-ttu-id="0a969-137">[WebSockets](xref:fundamentals/websockets) deve ser habilitada, para permitir que o transporte de WebSockets para a função.</span><span class="sxs-lookup"><span data-stu-id="0a969-137">[WebSockets](xref:fundamentals/websockets) should be enabled, to allow the WebSockets transport to function.</span></span>

<span data-ttu-id="0a969-138">No portal do Azure, navegue até **configurações do aplicativo** para seu aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="0a969-138">In the Azure portal, navigate to **App Settings** for your web app.</span></span> <span data-ttu-id="0a969-139">Definir **WebSockets** à **na**e verifique se **afinidade ARR** é **em**.</span><span class="sxs-lookup"><span data-stu-id="0a969-139">Set **WebSockets** to **On**, and verify **ARR Affinity** is **On**.</span></span>

![Configurações do aplicativo Web do Azure no portal do Azure](publish-to-azure-web-app/_static/azure-web-app-settings.png)

 <span data-ttu-id="0a969-141">WebSockets e outros transportes [são limitados com base no plano do serviço de aplicativo](/azure/azure-subscription-service-limits#app-service-limits).</span><span class="sxs-lookup"><span data-stu-id="0a969-141">WebSockets and other transports [are limited based on the App Service Plan](/azure/azure-subscription-service-limits#app-service-limits).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0a969-142">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="0a969-142">Related resources</span></span>

* [<span data-ttu-id="0a969-143">Publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando</span><span class="sxs-lookup"><span data-stu-id="0a969-143">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="0a969-144">Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a969-144">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>](xref:tutorials/publish-to-azure-webapp-using-vs)
* [<span data-ttu-id="0a969-145">Hospedar e implantar aplicativos de visualização do ASP.NET Core no Azure</span><span class="sxs-lookup"><span data-stu-id="0a969-145">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
