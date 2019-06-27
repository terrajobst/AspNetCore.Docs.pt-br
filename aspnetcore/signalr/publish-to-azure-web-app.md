---
title: Publicar um ASP.NET Core SignalR aplicativo no serviço de aplicativo do Azure
author: bradygaster
description: Saiba como publicar um aplicativo do SignalR do ASP.NET Core no serviço de aplicativo do Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406109"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a><span data-ttu-id="99cb5-103">Publicar um ASP.NET Core SignalR aplicativo no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="99cb5-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="99cb5-104">Por [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="99cb5-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="99cb5-105">[O serviço de aplicativo do Azure](/azure/app-service/app-service-web-overview) é um [a computação em nuvem do Microsoft](https://azure.microsoft.com/) plataforma de serviço para hospedar aplicativos web, incluindo o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99cb5-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="99cb5-106">Este artigo refere-se a publicação de um aplicativo de SignalR do ASP.NET Core no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="99cb5-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="99cb5-107">Para obter mais informações, consulte [serviço SignalR para o Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="99cb5-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="99cb5-108">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="99cb5-108">Publish the app</span></span>

<span data-ttu-id="99cb5-109">Este artigo aborda a publicação usando as ferramentas do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="99cb5-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="99cb5-110">Os usuários do Visual Studio Code podem usar [CLI do Azure](/cli/azure) comandos para publicar aplicativos no Azure.</span><span class="sxs-lookup"><span data-stu-id="99cb5-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="99cb5-111">Para obter mais informações, consulte [publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="99cb5-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="99cb5-112">Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções**, e selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="99cb5-113">Confirme **serviço de aplicativo** e **criar novo** selecionados no **escolher um destino de publicação** caixa de diálogo.</span><span class="sxs-lookup"><span data-stu-id="99cb5-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="99cb5-114">Selecione **criar perfil** da **publicar** botão soltar para baixo.</span><span class="sxs-lookup"><span data-stu-id="99cb5-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="99cb5-115">Insira as informações descritas na tabela a seguir na **criar serviço de aplicativo** caixa de diálogo e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="99cb5-116">Item</span><span class="sxs-lookup"><span data-stu-id="99cb5-116">Item</span></span>               | <span data-ttu-id="99cb5-117">Descrição</span><span class="sxs-lookup"><span data-stu-id="99cb5-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="99cb5-118">**Nome**</span><span class="sxs-lookup"><span data-stu-id="99cb5-118">**Name**</span></span>           | <span data-ttu-id="99cb5-119">Nome exclusivo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="99cb5-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="99cb5-120">**Assinatura**</span><span class="sxs-lookup"><span data-stu-id="99cb5-120">**Subscription**</span></span>   | <span data-ttu-id="99cb5-121">Assinatura do Azure que o aplicativo usa.</span><span class="sxs-lookup"><span data-stu-id="99cb5-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="99cb5-122">**Grupo de recursos**</span><span class="sxs-lookup"><span data-stu-id="99cb5-122">**Resource Group**</span></span> | <span data-ttu-id="99cb5-123">Grupo de recursos relacionados ao qual pertence o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="99cb5-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="99cb5-124">**Plano de hospedagem**</span><span class="sxs-lookup"><span data-stu-id="99cb5-124">**Hosting Plan**</span></span>   | <span data-ttu-id="99cb5-125">Plano de preços para o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="99cb5-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="99cb5-126">Selecione o **serviço do Azure SignalR** na **dependências** > **Add** lista suspensa:</span><span class="sxs-lookup"><span data-stu-id="99cb5-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Área de dependências, mostrando a seleção de serviço do Azure SignalR na lista suspensa Add](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="99cb5-128">No **serviço do Azure SignalR** caixa de diálogo, selecione **criar uma nova instância de serviço do Azure SignalR**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="99cb5-129">Fornecer um **nome**, **grupo de recursos**, e **local**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="99cb5-130">Volte para o **serviço do Azure SignalR** caixa de diálogo e selecione **Add**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="99cb5-131">Visual Studio conclui as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="99cb5-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="99cb5-132">Cria um perfil de publicação que contém as configurações de publicação.</span><span class="sxs-lookup"><span data-stu-id="99cb5-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="99cb5-133">Cria uma *aplicativo Web do Azure* com os detalhes fornecidos.</span><span class="sxs-lookup"><span data-stu-id="99cb5-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="99cb5-134">Publica o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="99cb5-134">Publishes the app.</span></span>
* <span data-ttu-id="99cb5-135">Inicia um navegador, que carrega o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="99cb5-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="99cb5-136">O formato da URL do aplicativo é `{APP SERVICE NAME}.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="99cb5-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="99cb5-137">Por exemplo, um aplicativo chamado `SignalRChatApp` tem uma URL de `https://signalrchatapp.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="99cb5-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="99cb5-138">Se um HTTP *502.2 - Gateway incorreto* erro ocorre ao implantar um aplicativo que tem como alvo uma versão do .NET Core da visualização, consulte [versão de visualização de implantar o ASP.NET Core no serviço de aplicativo do Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) resolvê-lo.</span><span class="sxs-lookup"><span data-stu-id="99cb5-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="99cb5-139">Configurar o aplicativo no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="99cb5-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="99cb5-140">*Esta seção aplica-se somente para aplicativos que não usam o serviço do Azure SignalR.*</span><span class="sxs-lookup"><span data-stu-id="99cb5-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="99cb5-141">Se o aplicativo usa o serviço do Azure SignalR, o serviço de aplicativo não exige a configuração de afinidade do roteamento ARR (Application Request) e Web Sockets descritos nesta seção.</span><span class="sxs-lookup"><span data-stu-id="99cb5-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="99cb5-142">Os clientes se conectam seus soquetes da Web ao serviço Azure SignalR, não diretamente para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="99cb5-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="99cb5-143">Para aplicativos hospedados sem o serviço do Azure SignalR, habilite:</span><span class="sxs-lookup"><span data-stu-id="99cb5-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="99cb5-144">[Afinidade ARR](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) para rotear solicitações de um usuário de volta para a mesma instância do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="99cb5-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="99cb5-145">A configuração padrão é **em**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-145">The default setting is **On**.</span></span>
* <span data-ttu-id="99cb5-146">[Web Sockets](xref:fundamentals/websockets) para permitir que o transporte de soquetes da Web para a função.</span><span class="sxs-lookup"><span data-stu-id="99cb5-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="99cb5-147">A configuração padrão é **desativar**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="99cb5-148">No portal do Azure, navegue até o aplicativo web no **serviços de aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="99cb5-149">Abra **Configuration** > **configurações gerais**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="99cb5-150">Definir **Web sockets** à **em**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="99cb5-151">Verifique **afinidade ARR** é definido como **em**.</span><span class="sxs-lookup"><span data-stu-id="99cb5-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="99cb5-152">Limita o plano do serviço de aplicativo</span><span class="sxs-lookup"><span data-stu-id="99cb5-152">App Service Plan limits</span></span>

<span data-ttu-id="99cb5-153">Soquetes da Web e outros transportes são limitados com base no plano de serviço de aplicativo selecionado.</span><span class="sxs-lookup"><span data-stu-id="99cb5-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="99cb5-154">Para obter mais informações, consulte o *serviços de nuvem do Azure limita* e *limites do serviço de aplicativo* seções o [assinatura do Azure e limites de serviço, cotas e restrições](/azure/azure-subscription-service-limits#app-service-limits) artigo.</span><span class="sxs-lookup"><span data-stu-id="99cb5-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99cb5-155">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="99cb5-155">Additional resources</span></span>

* [<span data-ttu-id="99cb5-156">O que é o serviço do Azure SignalR?</span><span class="sxs-lookup"><span data-stu-id="99cb5-156">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="99cb5-157">Publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando</span><span class="sxs-lookup"><span data-stu-id="99cb5-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="99cb5-158">Hospedar e implantar aplicativos de visualização do ASP.NET Core no Azure</span><span class="sxs-lookup"><span data-stu-id="99cb5-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
