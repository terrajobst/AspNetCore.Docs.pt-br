---
title: Implantação contínua no Azure com o Visual Studio e o GIT com o ASP.NET Core
author: rick-anderson
description: Saiba como criar um aplicativo Web ASP.NET Core usando o Visual Studio e implantá-lo no Serviço de Aplicativo do Azure, usando o Git para implantação contínua.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3b344505739bb4292ed1683c73ff314b6e4e01e9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890091"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="a7fd3-103">Implantação contínua no Azure com o Visual Studio e o GIT com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7fd3-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="a7fd3-104">Por [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="a7fd3-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="a7fd3-105">Este tutorial mostra como criar um aplicativo Web ASP.NET Core usando o Visual Studio e implantá-lo por meio do Visual Studio no Serviço de Aplicativo do Azure usando a implantação contínua.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="a7fd3-106">Consulte também [Criar seu primeiro pipeline com o Azure Pipelines](/azure/devops/pipelines/get-started-yaml), que mostra como configurar um fluxo de trabalho de CD (entrega contínua) para o [Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-overview) usando o Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="a7fd3-107">O Azure Pipelines (um serviço do Azure DevOps Services) simplifica a configuração de um pipeline de implantação robusta para publicar atualizações para aplicativos hospedados no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="a7fd3-108">O pipeline pode ser configurado no portal do Azure para criar, executar testes, implantar em um slot de preparo e, em seguida, implantar na produção.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="a7fd3-109">Para concluir este tutorial, você precisa de uma conta do Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="a7fd3-110">Para obter uma conta, [ative os benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) ou [inscreva-se em uma avaliação gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="a7fd3-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a7fd3-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="a7fd3-111">Prerequisites</span></span>

<span data-ttu-id="a7fd3-112">Este tutorial pressupõe que o seguinte software está instalado:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="a7fd3-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a7fd3-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="a7fd3-114">[Git](https://git-scm.com/downloads) para Windows</span><span class="sxs-lookup"><span data-stu-id="a7fd3-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="a7fd3-115">Criar um aplicativo Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7fd3-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="a7fd3-116">Inicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="a7fd3-117">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="a7fd3-118">Selecione o modelo de projeto **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="a7fd3-119">Ele será exibido em **Instalado** > **Modelos** > **Visual C#** > **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="a7fd3-120">Nomeie o projeto `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="a7fd3-121">Selecione a opção **Criar novo repositório GIT** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Caixa de diálogo Novo Projeto](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="a7fd3-123">Na caixa de diálogo **Novo Projeto ASP.NET Core**, selecione o modelo **Vazio** do ASP.NET Core e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Caixa de diálogo Novo projeto ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="a7fd3-125">A versão mais recente do .NET Core é a 2.0.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="a7fd3-126">Executando o aplicativo Web localmente</span><span class="sxs-lookup"><span data-stu-id="a7fd3-126">Running the web app locally</span></span>

1. <span data-ttu-id="a7fd3-127">Depois que o Visual Studio concluir a criação do aplicativo, execute o aplicativo selecionando **Depurar** > **Iniciar Depuração**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="a7fd3-128">Como alternativa, pressione **F5**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="a7fd3-129">Talvez seja necessário alguns instantes para inicializar o Visual Studio e o novo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="a7fd3-130">Quando isso for concluído, o navegador mostrará o aplicativo em execução.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-130">Once it's complete, the browser shows the running app.</span></span>

   ![Janela do navegador mostrando o aplicativo em execução que exibe “Olá, Mundo!”](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="a7fd3-132">Depois de examinar o aplicativo Web em execução, feche o navegador e selecione o ícone “Parar Depuração” na barra de ferramentas do Visual Studio para interromper o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="a7fd3-133">Criar um aplicativo Web no Portal do Azure</span><span class="sxs-lookup"><span data-stu-id="a7fd3-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="a7fd3-134">As etapas a seguir criam um aplicativo Web no portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="a7fd3-135">Faça logon no [portal do Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="a7fd3-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="a7fd3-136">Selecione **NOVO** na parte superior esquerda da interface do portal.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="a7fd3-137">Selecione **Web + Celular** > **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Portal do Microsoft Azure: Botão Novo: Web + Celular em Marketplace: Botão Aplicativo Web em Aplicativos em Destaque](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="a7fd3-139">Na folha **Aplicativo Web**, insira um valor exclusivo para o **Nome do Serviço de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Folha Aplicativo Web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="a7fd3-141">O **Nome do Serviço de Aplicativo** precisa ser exclusivo.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="a7fd3-142">O portal impõe essa regra quando o nome é fornecido.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="a7fd3-143">Se você fornecer outro valor, você precisará substituí-lo para cada ocorrência do **SampleWebAppDemo** neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="a7fd3-144">Também na folha **Aplicativo Web**, selecione um **Plano do Serviço de Aplicativo/Localização** existente ou crie um novo.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="a7fd3-145">Se você criar um novo plano, selecione o tipo de preço, o local e outras opções.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="a7fd3-146">Para obter mais informações sobre os planos do Serviço de Aplicativo, veja [Visão geral detalhada dos planos do Serviço de Aplicativo do Azure](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="a7fd3-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="a7fd3-147">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-147">Select **Create**.</span></span> <span data-ttu-id="a7fd3-148">O Azure provisionará e iniciará o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-148">Azure will provision and start the web app.</span></span>

   ![Portal do Azure: folha Informações Gerais da Demonstração de Aplicativo Web de Exemplo 01](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="a7fd3-150">Habilitar a publicação do Git no novo aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="a7fd3-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="a7fd3-151">O GIT é um sistema de controle de versão distribuída que pode ser usado para implantar um aplicativo Web do Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="a7fd3-152">O código do aplicativo Web é armazenado em um repositório GIT local e implantado no Azure por push para um repositório remoto.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="a7fd3-153">Faça logon no [portal do Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="a7fd3-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="a7fd3-154">Selecione **Serviços de Aplicativos** para exibir uma lista de serviços de aplicativos associados à assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="a7fd3-155">Selecione o aplicativo Web criado na seção anterior deste tutorial.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="a7fd3-156">Na folha **Implantação**, selecione **Opções de implantação** > **Escolher Origem** > **Repositório Git Local**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Folha Configurações: Folha Origem da implantação: Escolha a folha de origem](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="a7fd3-158">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-158">Select **OK**.</span></span>

1. <span data-ttu-id="a7fd3-159">Caso você não tenha configurado anteriormente as credenciais de implantação para publicar um aplicativo Web ou outro aplicativo do Serviço de Aplicativo, configure-as agora:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="a7fd3-160">Selecione **Configurações** > **Credenciais de implantação**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="a7fd3-161">A folha **Definir credenciais de implantação** é exibida.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="a7fd3-162">Crie um nome de usuário e uma senha.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-162">Create a user name and password.</span></span> <span data-ttu-id="a7fd3-163">Salve a senha para uso posterior ao configurar o GIT.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="a7fd3-164">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-164">Select **Save**.</span></span>

1. <span data-ttu-id="a7fd3-165">Na folha **Aplicativo Web**, selecione **Configurações** > **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="a7fd3-166">A URL do repositório GIT remoto no qual você implantará será mostrada em **URL do GIT**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="a7fd3-167">Copie o valor **URL do GIT** para uso posterior no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Portal do Azure: folha Propriedades do aplicativo](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="a7fd3-169">Publicar o aplicativo Web no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="a7fd3-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="a7fd3-170">Nesta seção, você criará um repositório GIT local usando o Visual Studio e efetuará push desse repositório para o Azure para implantar o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="a7fd3-171">As etapas envolvidas incluem as seguintes:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-171">The steps involved include the following:</span></span>

* <span data-ttu-id="a7fd3-172">Adicione a configuração do repositório remoto usando o valor de URL do GIT, de modo que você possa implantar seu repositório local no Azure.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="a7fd3-173">Confirme as alterações do projeto.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-173">Commit project changes.</span></span>
* <span data-ttu-id="a7fd3-174">Envie as alterações do projeto por push do repositório local para o repositório remoto no Azure.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="a7fd3-175">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **“SampleWebAppDemo” da Solução** e selecione **Confirmar**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="a7fd3-176">O **Team Explorer** é exibido.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-176">The **Team Explorer** is displayed.</span></span>

   ![Guia Conectar do Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="a7fd3-178">No **Team Explorer**, selecione a **Página Inicial** (ícone da página inicial) > **Configurações** > **Configurações do Repositório**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="a7fd3-179">Na seção **Remotos** das **Configurações do Repositório**, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="a7fd3-180">A caixa de diálogo **Adicionar Remoto** é exibida.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="a7fd3-181">Defina o **Nome** do remoto como **Azure-SampleApp**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="a7fd3-182">Defina o valor de **Buscar** como a **URL do GIT** copiada do Azure anteriormente neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="a7fd3-183">Observe que essa é a URL que termina com **.git**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Caixa de diálogo Editar Remoto](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="a7fd3-185">Como alternativa, especifique o repositório remoto na **Janela Comando** abrindo a **Janela Comando**, alterando para o diretório do projeto e inserindo o comando.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="a7fd3-186">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="a7fd3-187">Selecione a **Página Inicial** (ícone da página inicial) > **Configurações** > **Configurações Globais**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="a7fd3-188">Confirme se o nome e o endereço de email estão definidos.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="a7fd3-189">Se necessário, selecione **Atualizar**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="a7fd3-190">Selecione **Página Inicial** > **Alterações** para retornar à exibição **Alterações**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="a7fd3-191">Escreva uma mensagem de confirmação, como **Push Inicial nº 1** e selecione **Confirmar**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="a7fd3-192">Essa ação cria uma *confirmação* localmente.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-192">This action creates a *commit* locally.</span></span>

   ![Guia Conectar do Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="a7fd3-194">Como alternativa, confirme as alterações na **Janela Comando** abrindo a **Janela Comando**, alterando para o diretório do projeto e inserindo os comandos do GIT.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="a7fd3-195">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="a7fd3-196">Selecione **Página Inicial** > **Sincronização** > **Ações** > **Abrir Prompt de Comando**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="a7fd3-197">O prompt de comando se abre no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="a7fd3-198">Insira o seguinte comando na janela de comando:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="a7fd3-199">Insira a senha das **credenciais de implantação** do Azure criada anteriormente no Azure.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="a7fd3-200">Esse comando inicia o processo de envio por push dos arquivos de projeto locais para o Azure.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="a7fd3-201">A saída do comando acima termina com uma mensagem informando que a implantação foi bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="a7fd3-202">Se a colaboração no projeto é necessária, considere a possibilidade de enviar por push para o [GitHub](https://github.com) antes de enviar por push para o Azure.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="a7fd3-203">Verificar a implantação ativa</span><span class="sxs-lookup"><span data-stu-id="a7fd3-203">Verify the Active Deployment</span></span>

<span data-ttu-id="a7fd3-204">Verifique se a transferência do aplicativo Web do ambiente local para o Azure é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="a7fd3-205">No [portal do Azure](https://portal.azure.com), selecione o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="a7fd3-206">Selecione **Implantação** > **Opções de implantação**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-206">Select **Deployment** > **Deployment options**.</span></span>

![Portal do Azure: Folha Configurações: folha Implantações mostrando a implantação bem-sucedida](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="a7fd3-208">Executar o aplicativo no Azure</span><span class="sxs-lookup"><span data-stu-id="a7fd3-208">Run the app in Azure</span></span>

<span data-ttu-id="a7fd3-209">Agora que o aplicativo Web é implantado no Azure, execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="a7fd3-210">Isso pode ser feito de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="a7fd3-211">No portal do Azure, localize a folha do aplicativo Web desse aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="a7fd3-212">Selecione **Procurar** para exibir o aplicativo no navegador padrão.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="a7fd3-213">Abra um navegador e insira a URL para o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="a7fd3-214">Exemplo: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="a7fd3-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="a7fd3-215">Atualize o aplicativo Web e publique-o novamente</span><span class="sxs-lookup"><span data-stu-id="a7fd3-215">Update the web app and republish</span></span>

<span data-ttu-id="a7fd3-216">Depois de fazer alterações ao código local, republique:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="a7fd3-217">No **Gerenciador de Soluções** do Visual Studio, abra o arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="a7fd3-218">No método `Configure`, modifique o método `Response.WriteAsync` para que ele seja exibido da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="a7fd3-219">Salve as alterações em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="a7fd3-220">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **“SampleWebAppDemo” da Solução** e selecione **Confirmar**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="a7fd3-221">O **Team Explorer** é exibido.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="a7fd3-222">Escreva uma mensagem de confirmação, tal como `Update #2`.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="a7fd3-223">Pressione o botão **Confirmar** para confirmar as alterações do projeto.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="a7fd3-224">Selecione **Página Inicial** > **Sincronização** > **Ações** > **Enviar por Push**.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="a7fd3-225">Como alternativa, envie as alterações por push da **Janela Comando** abrindo a **Janela Comando**, alterando para o diretório do projeto e inserindo um comando do GIT.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="a7fd3-226">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="a7fd3-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="a7fd3-227">Exibir o aplicativo Web atualizado no Azure</span><span class="sxs-lookup"><span data-stu-id="a7fd3-227">View the updated web app in Azure</span></span>

<span data-ttu-id="a7fd3-228">Exiba o aplicativo Web atualizado selecionando **Procurar** na folha do aplicativo Web no portal do Azure ou abrindo um navegador e inserindo a URL do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a7fd3-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="a7fd3-229">Exemplo: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="a7fd3-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7fd3-230">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a7fd3-230">Additional resources</span></span>

* [<span data-ttu-id="a7fd3-231">Criar seu primeiro pipeline com o Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="a7fd3-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="a7fd3-232">Kudu do projeto</span><span class="sxs-lookup"><span data-stu-id="a7fd3-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
