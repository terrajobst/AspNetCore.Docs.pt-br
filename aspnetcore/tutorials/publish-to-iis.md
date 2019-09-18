---
title: Publicar um aplicativo ASP.NET Core no IIS
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um servidor IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 4ac7b3a2f738e443263dd888f556e0aff7c8099b
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082370"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="a2e69-103">Publicar um aplicativo ASP.NET Core no IIS</span><span class="sxs-lookup"><span data-stu-id="a2e69-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="a2e69-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a2e69-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a2e69-105">Este tutorial mostra como hospedar um aplicativo ASP.NET Core em um servidor IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-105">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="a2e69-106">Este tutorial cobre os seguintes assuntos:</span><span class="sxs-lookup"><span data-stu-id="a2e69-106">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a2e69-107">Instalar o Pacote de Hospedagem do .NET Core no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="a2e69-107">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="a2e69-108">Criar um site do IIS no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-108">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="a2e69-109">Implantar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2e69-109">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a2e69-110">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="a2e69-110">Prerequisites</span></span>

* <span data-ttu-id="a2e69-111">[SDK do .NET Core](/dotnet/core/sdk) instalado no computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a2e69-111">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="a2e69-112">O Windows Server foi configurado com a função de servidor **Servidor Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="a2e69-112">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="a2e69-113">Se o servidor não estiver configurado para hospedar sites com o IIS, siga as orientações na seção *Configuração do IIS* do artigo <xref:host-and-deploy/iis/index#iis-configuration> e, em seguida, retorne a este tutorial.</span><span class="sxs-lookup"><span data-stu-id="a2e69-113">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="a2e69-114">**A configuração do IIS e a segurança do site envolvem conceitos que não são cobertos por este tutorial.**</span><span class="sxs-lookup"><span data-stu-id="a2e69-114">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="a2e69-115">Consulte as diretrizes do IIS na [documentação do IIS da Microsoft](https://www.iis.net/) e o [artigo ASP.NET Core sobre como hospedar com o IIS](xref:host-and-deploy/iis/index) antes de hospedar aplicativos de produção no IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-115">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="a2e69-116">Os cenários importantes para a hospedagem do IIS não cobertos por este tutorial incluem:</span><span class="sxs-lookup"><span data-stu-id="a2e69-116">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="a2e69-117">Criação de um hive de Registro para proteção de dados de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e69-117">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="a2e69-118">Configuração da ACL (lista de controle de acesso) do pool de aplicativos</span><span class="sxs-lookup"><span data-stu-id="a2e69-118">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="a2e69-119">Para se concentrar nos conceitos de implantação do IIS, este tutorial implanta um aplicativo sem segurança HTTPS configurada no IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-119">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="a2e69-120">Para obter mais informações sobre como hospedar um aplicativo habilitado para o protocolo HTTPS, confira os tópicos de segurança na seção [Recursos adicionais](#additional-resources) deste artigo.</span><span class="sxs-lookup"><span data-stu-id="a2e69-120">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="a2e69-121">Mais diretrizes para hospedar aplicativos ASP.NET Core são apresentadas no <xref:host-and-deploy/iis/index> artigo.</span><span class="sxs-lookup"><span data-stu-id="a2e69-121">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="a2e69-122">Instalar o pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e69-122">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="a2e69-123">Instalar o *Pacote de Hospedagem do .NET Core* no servidor IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-123">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="a2e69-124">O pacote instala o Tempo de Execução .NET Core, a Biblioteca do .NET Core e o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a2e69-124">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a2e69-125">O módulo permite que aplicativos do ASP.NET Core sejam executados por trás do IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-125">The module allows ASP.NET Core apps to run behind IIS.</span></span> <span data-ttu-id="a2e69-126">Se o sistema não tiver uma conexão com a Internet, obtenha e instale os [Pacotes redistribuíveis do Microsoft Visual C++ 2015](https://www.microsoft.com/download/details.aspx?id=53840) antes de instalar o pacote de hospedagem do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2e69-126">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Hosting Bundle.</span></span>

<span data-ttu-id="a2e69-127">Baixe o instalador usando o seguinte link:</span><span class="sxs-lookup"><span data-stu-id="a2e69-127">Download the installer using the following link:</span></span>

[<span data-ttu-id="a2e69-128">Instalador de pacote de hospedagem do .NET Core atual (download direto)</span><span class="sxs-lookup"><span data-stu-id="a2e69-128">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="a2e69-129">Execute o instalador no servidor IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-129">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="a2e69-130">Reinicie o servidor ou execute **net stop was /y**, seguido por **net start w3svc** em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a2e69-130">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="a2e69-131">Criar o site do IIS</span><span class="sxs-lookup"><span data-stu-id="a2e69-131">Create the IIS site</span></span>

1. <span data-ttu-id="a2e69-132">No servidor IIS, crie uma pasta para conter arquivos e pastas publicados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2e69-132">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="a2e69-133">Em uma etapa a seguir, o caminho da pasta é fornecido ao IIS como o caminho físico para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a2e69-133">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="a2e69-134">No Gerenciador do IIS, abra o nó do servidor no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="a2e69-134">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="a2e69-135">Clique com botão direito do mouse na pasta **Sites**.</span><span class="sxs-lookup"><span data-stu-id="a2e69-135">Right-click the **Sites** folder.</span></span> <span data-ttu-id="a2e69-136">Selecione **Adicionar Site** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="a2e69-136">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="a2e69-137">Forneça um **Nome do site** e defina o **Caminho físico** como a pasta de implantação do aplicativo que você criou.</span><span class="sxs-lookup"><span data-stu-id="a2e69-137">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="a2e69-138">Forneça a configuração **Associação** e crie o site ao selecionar **OK**.</span><span class="sxs-lookup"><span data-stu-id="a2e69-138">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="a2e69-139">Criar um aplicativo Razor Pages do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e69-139">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="a2e69-140">Siga o tutorial <xref:getting-started> para criar um aplicativo Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a2e69-140">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="a2e69-141">Publicar e implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a2e69-141">Publish and deploy the app</span></span>

<span data-ttu-id="a2e69-142">*Publicar um aplicativo* significa produzir um aplicativo compilado que pode ser hospedado por um servidor.</span><span class="sxs-lookup"><span data-stu-id="a2e69-142">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="a2e69-143">*Implantar um aplicativo* significa mover o aplicativo publicado para um sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="a2e69-143">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="a2e69-144">A etapa de publicação é tratada pelo [SDK do .NET Core](/dotnet/core/sdk), enquanto a etapa de implantação pode ser tratada por diversas abordagens.</span><span class="sxs-lookup"><span data-stu-id="a2e69-144">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="a2e69-145">Este tutorial adota a abordagem de implantação de *pasta*, em que:</span><span class="sxs-lookup"><span data-stu-id="a2e69-145">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="a2e69-146">O aplicativo é publicado em uma pasta.</span><span class="sxs-lookup"><span data-stu-id="a2e69-146">The app is published to a folder.</span></span>
* <span data-ttu-id="a2e69-147">O conteúdo da pasta é movido para a pasta do site do IIS (o **caminho físico** para o site no Gerenciador do IIS).</span><span class="sxs-lookup"><span data-stu-id="a2e69-147">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a2e69-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2e69-148">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a2e69-149">Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções**, e selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="a2e69-149">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="a2e69-150">Na caixa de diálogo **Escolher um destino de publicação**, selecione a opção de publicação de **Pasta**.</span><span class="sxs-lookup"><span data-stu-id="a2e69-150">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="a2e69-151">Defina o caminho **Pasta ou Compartilhamento de arquivo**.</span><span class="sxs-lookup"><span data-stu-id="a2e69-151">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="a2e69-152">Se você criou uma pasta para o site do IIS que está disponível no computador de desenvolvimento como um compartilhamento de rede, forneça o caminho para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="a2e69-152">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="a2e69-153">O usuário atual deve ter acesso de gravação para publicar no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="a2e69-153">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="a2e69-154">Se não for possível implantar diretamente na pasta do site do IIS no servidor IIS, publique em uma pasta na mídia removida e mova fisicamente o aplicativo publicado para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no IIS Manager.</span><span class="sxs-lookup"><span data-stu-id="a2e69-154">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="a2e69-155">Mova o conteúdo da pasta *bin/Release/{TARGET FRAMEWORK}/publish* para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-155">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a2e69-156">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e69-156">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="a2e69-157">Em um shell de comando, publique o aplicativo na Configuração de versão usando o comando [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="a2e69-157">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="a2e69-158">Mova o conteúdo da pasta *bin/Release/{TARGET FRAMEWORK}/publish* para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-158">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a2e69-159">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a2e69-159">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a2e69-160">Clique com o botão direito do mouse no projeto em **Solução** e selecione **Publicar** > **Publicar na Pasta**.</span><span class="sxs-lookup"><span data-stu-id="a2e69-160">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="a2e69-161">Defina o caminho **Escolher uma pasta**.</span><span class="sxs-lookup"><span data-stu-id="a2e69-161">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="a2e69-162">Se você criou uma pasta para o site do IIS que está disponível no computador de desenvolvimento como um compartilhamento de rede, forneça o caminho para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="a2e69-162">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="a2e69-163">O usuário atual deve ter acesso de gravação para publicar no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="a2e69-163">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="a2e69-164">Se não for possível implantar diretamente na pasta do site do IIS no servidor IIS, publique em uma pasta na mídia removida e mova fisicamente o aplicativo publicado para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no IIS Manager.</span><span class="sxs-lookup"><span data-stu-id="a2e69-164">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="a2e69-165">Mova o conteúdo da pasta *bin/Release/{TARGET FRAMEWORK}/publish* para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-165">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="a2e69-166">Navegar no site</span><span class="sxs-lookup"><span data-stu-id="a2e69-166">Browse the website</span></span>

<span data-ttu-id="a2e69-167">O aplicativo pode ser acessado em um navegador depois de receber a primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="a2e69-167">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="a2e69-168">Faça uma solicitação para o aplicativo na associação de ponto de extremidade que você estabeleceu no Gerenciador do IIS para o site.</span><span class="sxs-lookup"><span data-stu-id="a2e69-168">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a2e69-169">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a2e69-169">Next steps</span></span>

<span data-ttu-id="a2e69-170">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="a2e69-170">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a2e69-171">Instalar o Pacote de Hospedagem do .NET Core no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="a2e69-171">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="a2e69-172">Criar um site do IIS no Gerenciador do IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e69-172">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="a2e69-173">Implantar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2e69-173">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="a2e69-174">Para saber mais sobre como hospedar aplicativos ASP.NET Core no IIS, confira o artigo Visão Geral do IIS:</span><span class="sxs-lookup"><span data-stu-id="a2e69-174">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="a2e69-175">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a2e69-175">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="a2e69-176">Artigos no conjunto de documentação do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e69-176">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="a2e69-177">Artigos referentes à implantação do aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e69-177">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="a2e69-178">Publicar um aplicativo Web em uma pasta usando o Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a2e69-178">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="a2e69-179">Artigos sobre a configuração HTTPS do IIS</span><span class="sxs-lookup"><span data-stu-id="a2e69-179">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="a2e69-180">Como configurar SSL no Gerenciador do IIS</span><span class="sxs-lookup"><span data-stu-id="a2e69-180">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="a2e69-181">Como configurar o SSL no IIS</span><span class="sxs-lookup"><span data-stu-id="a2e69-181">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="a2e69-182">Artigos sobre IIS e Windows Server</span><span class="sxs-lookup"><span data-stu-id="a2e69-182">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="a2e69-183">O site oficial da IIS da Microsoft</span><span class="sxs-lookup"><span data-stu-id="a2e69-183">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="a2e69-184">Biblioteca de conteúdo técnico do Windows Server</span><span class="sxs-lookup"><span data-stu-id="a2e69-184">Windows Server technical content library</span></span>](/windows-server/windows-server)
