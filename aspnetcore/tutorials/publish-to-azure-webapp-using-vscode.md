---
title: Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio Code
author: ricardoserradas
description: Saiba como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio Code
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 90ba130f13903cd45eca062c0eca8945eff2e0fa
ms.sourcegitcommit: 7a2c820692f04bfba04398641b70f27fa15391f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72290648"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="ac9a6-103">Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac9a6-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="ac9a6-104">Por [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="ac9a6-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="ac9a6-105">Para solucionar um problema de implantação do Serviço de Aplicativo, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="ac9a6-106">Introdução</span><span class="sxs-lookup"><span data-stu-id="ac9a6-106">Intro</span></span>

<span data-ttu-id="ac9a6-107">Com este tutorial, você aprenderá a criar um aplicativo ASP.NET Core MVC e implantá-lo no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="ac9a6-108">Configurar</span><span class="sxs-lookup"><span data-stu-id="ac9a6-108">Set up</span></span>

- <span data-ttu-id="ac9a6-109">Abra uma [conta do Azure gratuita](https://azure.microsoft.com/free/dotnet/) se você não tiver uma.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="ac9a6-110">Instale o [SDK do .NET Core](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="ac9a6-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="ac9a6-111">Instale o [Visual Studio Code](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="ac9a6-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="ac9a6-112">Instale a [Extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) para o Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac9a6-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="ac9a6-113">Instale a [extensão de serviço Azure app](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) para Visual Studio Code e configure-a antes de continuar</span><span class="sxs-lookup"><span data-stu-id="ac9a6-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="ac9a6-114">Criar um projeto ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="ac9a6-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="ac9a6-115">Usando um terminal, navegue para a pasta em que deseja criar o projeto e use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ac9a6-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="ac9a6-116">Você terá uma estrutura de pastas semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="ac9a6-116">You'll have a folder structure similar to the following:</span></span>

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="ac9a6-117">Abri-lo com o Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac9a6-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="ac9a6-118">Depois que o projeto for criado, abra-o com o Visual Studio Code usando uma das seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="ac9a6-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="ac9a6-119">Por meio da linha de comando</span><span class="sxs-lookup"><span data-stu-id="ac9a6-119">Through the command line</span></span>

<span data-ttu-id="ac9a6-120">Use o seguinte comando na pasta em que você criou o projeto:</span><span class="sxs-lookup"><span data-stu-id="ac9a6-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="ac9a6-121">Se o comando abaixo não funcionar, verifique se a instalação está configurada corretamente referenciando [este link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span><span class="sxs-lookup"><span data-stu-id="ac9a6-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="ac9a6-122">Por meio da interface do Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac9a6-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="ac9a6-123">Abra o Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac9a6-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="ac9a6-124">No menu, selecione `File > Open Folder`</span><span class="sxs-lookup"><span data-stu-id="ac9a6-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="ac9a6-125">Selecione a raiz da pasta em que você criou o projeto MVC</span><span class="sxs-lookup"><span data-stu-id="ac9a6-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="ac9a6-126">Ao abrir a pasta do projeto, você receberá uma mensagem informando que os ativos necessários para o build e a depuração estão ausentes.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="ac9a6-127">Aceite a ajuda para adicioná-los.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-127">Accept the help to add them.</span></span>

![Interface do Visual Studio Code com o projeto carregado](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="ac9a6-129">Uma pasta `.vscode` será criada com a estrutura do projeto.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="ac9a6-130">Ela conterá os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="ac9a6-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="ac9a6-131">Eles são arquivos de utilitário para ajudá-lo a criar e depurar seu aplicativo Web .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="ac9a6-132">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ac9a6-132">Run the app</span></span>

<span data-ttu-id="ac9a6-133">Antes de implantar o aplicativo no Azure, verifique se ele está sendo executado corretamente no computador local.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="ac9a6-134">Pressione F5 para executar o projeto</span><span class="sxs-lookup"><span data-stu-id="ac9a6-134">Press F5 to run the project</span></span>

<span data-ttu-id="ac9a6-135">Seu aplicativo Web iniciará a execução em uma nova guia do navegador padrão.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="ac9a6-136">Você poderá observar um aviso de privacidade assim que ele for iniciado.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="ac9a6-137">Isso ocorre porque o aplicativo será iniciado usando HTTP e HTTPS, e ele navega para o ponto de extremidade HTTPS por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Aviso de privacidade durante a depuração do aplicativo localmente](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="ac9a6-139">Para manter a sessão de depuração, clique em `Advanced` e, em seguida, `Continue to localhost (unsafe)`.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="ac9a6-140">Gerar o pacote de implantação localmente</span><span class="sxs-lookup"><span data-stu-id="ac9a6-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="ac9a6-141">Abrir o terminal do Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac9a6-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="ac9a6-142">Use o seguinte comando para gerar um pacote `Release` para uma subpasta chamada `publish`:</span><span class="sxs-lookup"><span data-stu-id="ac9a6-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="ac9a6-143">Uma pasta `publish` será criada com a estrutura do projeto</span><span class="sxs-lookup"><span data-stu-id="ac9a6-143">A new `publish` folder will be created under the project structure</span></span>

![Estrutura de pastas de publicação](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="ac9a6-145">Publicar no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="ac9a6-145">Publish to Azure App Service</span></span>

<span data-ttu-id="ac9a6-146">Aproveitando a extensão do Serviço de Aplicativo do Azure para Visual Studio Code, siga as etapas a seguir para publicar o site diretamente no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="ac9a6-147">Se você estiver criando um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="ac9a6-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="ac9a6-148">Clique com o botão direito do mouse na pasta `publish` e selecione `Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="ac9a6-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="ac9a6-149">Selecione a assinatura em que deseja criar o aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="ac9a6-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="ac9a6-150">Selecione `Create New Web App`</span><span class="sxs-lookup"><span data-stu-id="ac9a6-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="ac9a6-151">Insira um nome para o aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="ac9a6-151">Enter a name for the Web App</span></span>

<span data-ttu-id="ac9a6-152">A extensão criará o aplicativo Web e começará automaticamente a implantar o pacote nele.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="ac9a6-153">Depois que a implantação for concluída, clique em `Browse Website` para validar a implantação.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Mensagem de implantação bem-sucedida](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="ac9a6-155">Depois de clicar em `Browse Website`, você navegará para ele usando o navegador padrão:</span><span class="sxs-lookup"><span data-stu-id="ac9a6-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Novo aplicativo Web implantado com êxito](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="ac9a6-157">Se você estiver fazendo uma implantação em um aplicativo Web existente</span><span class="sxs-lookup"><span data-stu-id="ac9a6-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="ac9a6-158">Clique com o botão direito do mouse na pasta `publish` e selecione `Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="ac9a6-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="ac9a6-159">Selecione a assinatura na qual reside o aplicativo Web existente</span><span class="sxs-lookup"><span data-stu-id="ac9a6-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="ac9a6-160">Selecione o aplicativo Web na lista</span><span class="sxs-lookup"><span data-stu-id="ac9a6-160">Select the Web App from the list</span></span>
- <span data-ttu-id="ac9a6-161">O Visual Studio Code perguntará se você deseja substituir o conteúdo existente.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="ac9a6-162">Clique em `Deploy` para confirmar</span><span class="sxs-lookup"><span data-stu-id="ac9a6-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="ac9a6-163">A extensão implantará o conteúdo atualizado no aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="ac9a6-164">Após a conclusão, clique em `Browse Website` para validar a implantação.</span><span class="sxs-lookup"><span data-stu-id="ac9a6-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Aplicativo Web existente implantado com êxito](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="ac9a6-166">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="ac9a6-166">Next steps</span></span>

- [<span data-ttu-id="ac9a6-167">Criar seu primeiro pipeline do Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="ac9a6-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="ac9a6-168">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ac9a6-168">Additional resources</span></span>

- [<span data-ttu-id="ac9a6-169">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="ac9a6-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="ac9a6-170">Grupo de recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="ac9a6-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)