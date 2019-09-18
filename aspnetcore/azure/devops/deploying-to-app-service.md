---
title: Implantar um aplicativo no serviço de aplicativo - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Implante um aplicativo ASP.NET Core no serviço de aplicativo do Azure, a primeira etapa para operações de desenvolvimento com ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080445"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="e787c-103">Implantar um aplicativo de serviço de aplicativo</span><span class="sxs-lookup"><span data-stu-id="e787c-103">Deploy an app to App Service</span></span>

<span data-ttu-id="e787c-104">[O serviço de aplicativo do Azure](/azure/app-service/) é plataforma de hospedagem de web do Azure.</span><span class="sxs-lookup"><span data-stu-id="e787c-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="e787c-105">Implantar um aplicativo web no serviço de aplicativo do Azure pode ser feito manualmente ou por um processo automatizado.</span><span class="sxs-lookup"><span data-stu-id="e787c-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="e787c-106">Esta seção do guia discute métodos de implantação que podem ser disparados manualmente ou por script usando a linha de comando ou disparada manualmente usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e787c-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="e787c-107">Nesta seção, você vai realizar as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="e787c-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="e787c-108">Baixe e compile o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="e787c-108">Download and build the sample app.</span></span>
* <span data-ttu-id="e787c-109">Crie um aplicativo serviço de aplicativo Web usando o Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="e787c-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="e787c-110">Implante o aplicativo de exemplo no Azure usando Git.</span><span class="sxs-lookup"><span data-stu-id="e787c-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="e787c-111">Implante uma alteração no aplicativo usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e787c-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="e787c-112">Adicione um slot de preparo para o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="e787c-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="e787c-113">Implante uma atualização para o slot de preparo.</span><span class="sxs-lookup"><span data-stu-id="e787c-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="e787c-114">Troca os slots de preparo e produção.</span><span class="sxs-lookup"><span data-stu-id="e787c-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="e787c-115">Baixar e testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e787c-115">Download and test the app</span></span>

<span data-ttu-id="e787c-116">O aplicativo usado neste guia é um aplicativo ASP.NET Core pré-criados [simples de leitor de Feed](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="e787c-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="e787c-117">É um aplicativo páginas Razor que usa o `Microsoft.SyndicationFeed.ReaderWriter` API para recuperar um feed RSS/Atom e exibir os itens de notícias em uma lista.</span><span class="sxs-lookup"><span data-stu-id="e787c-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="e787c-118">Fique à vontade examinar o código, mas é importante entender que não há nada de especial sobre este aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e787c-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="e787c-119">Ele é apenas um aplicativo ASP.NET Core simple para fins ilustrativos.</span><span class="sxs-lookup"><span data-stu-id="e787c-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="e787c-120">Em um shell de comando, baixe o código, compile o projeto e executá-lo da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="e787c-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="e787c-121">*Observação: Os usuários do linux/MacOS devem fazer as alterações apropriadas para caminhos, por exemplo, usando`/`a barra () em vez`\`da barra invertida ().*</span><span class="sxs-lookup"><span data-stu-id="e787c-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="e787c-122">Clone o código para uma pasta no seu computador local.</span><span class="sxs-lookup"><span data-stu-id="e787c-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="e787c-123">Alterar pasta de trabalho para o *leitor de feeds simples* pasta que foi criada.</span><span class="sxs-lookup"><span data-stu-id="e787c-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="e787c-124">Restaure os pacotes e compile a solução.</span><span class="sxs-lookup"><span data-stu-id="e787c-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="e787c-125">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e787c-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![O comando dotnet run for bem-sucedida](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="e787c-127">Abra um navegador e navegue até `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e787c-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="e787c-128">O aplicativo permite que você digite ou cole um URL de feed de distribuição e exibir uma lista de itens de notícias.</span><span class="sxs-lookup"><span data-stu-id="e787c-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![O aplicativo exibindo o conteúdo de um RSS feed](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="e787c-130">Quando estiver satisfeito o aplicativo está funcionando corretamente, desligá-lo pressionando **Ctrl**+**C** no shell de comando.</span><span class="sxs-lookup"><span data-stu-id="e787c-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="e787c-131">Criar o aplicativo Web do serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e787c-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="e787c-132">Para implantar o aplicativo, você precisará criar um serviço de aplicativo [aplicativo Web](/azure/app-service/app-service-web-overview).</span><span class="sxs-lookup"><span data-stu-id="e787c-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="e787c-133">Após a criação do aplicativo Web, você implantará a ele em seu computador local usando o Git.</span><span class="sxs-lookup"><span data-stu-id="e787c-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="e787c-134">Entrar para o [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="e787c-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="e787c-135">Observação: Quando você entra pela primeira vez, o Cloud Shell solicita a criação de uma conta de armazenamento para os arquivos de configuração.</span><span class="sxs-lookup"><span data-stu-id="e787c-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="e787c-136">Aceite os padrões ou forneça um nome exclusivo.</span><span class="sxs-lookup"><span data-stu-id="e787c-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="e787c-137">Use o Cloud Shell para as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="e787c-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="e787c-138">a.</span><span class="sxs-lookup"><span data-stu-id="e787c-138">a.</span></span> <span data-ttu-id="e787c-139">Declare uma variável para armazenar o nome do seu aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="e787c-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="e787c-140">O nome deve ser exclusivo a ser usado na URL padrão.</span><span class="sxs-lookup"><span data-stu-id="e787c-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="e787c-141">Usando o `$RANDOM` função Bash para construir o nome garante a exclusividade e resulta no formato `webappname99999`.</span><span class="sxs-lookup"><span data-stu-id="e787c-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="e787c-142">b.</span><span class="sxs-lookup"><span data-stu-id="e787c-142">b.</span></span> <span data-ttu-id="e787c-143">Crie um grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="e787c-143">Create a resource group.</span></span> <span data-ttu-id="e787c-144">Grupos de recursos fornecem um meio para agregar os recursos do Azure para ser gerenciado como um grupo.</span><span class="sxs-lookup"><span data-stu-id="e787c-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="e787c-145">O `az` comando invoca o [CLI do Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="e787c-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="e787c-146">A CLI pode ser executada localmente, mas usá-lo no Cloud Shell economiza tempo e configuração.</span><span class="sxs-lookup"><span data-stu-id="e787c-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="e787c-147">c.</span><span class="sxs-lookup"><span data-stu-id="e787c-147">c.</span></span> <span data-ttu-id="e787c-148">Crie um plano de serviço de aplicativo na camada S1.</span><span class="sxs-lookup"><span data-stu-id="e787c-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="e787c-149">Um plano do serviço de aplicativo é um agrupamento de aplicativos web que compartilham o mesmo tipo de preço.</span><span class="sxs-lookup"><span data-stu-id="e787c-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="e787c-150">A camada S1 não é gratuita, mas ela é necessária para o recurso de slots de preparo.</span><span class="sxs-lookup"><span data-stu-id="e787c-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="e787c-151">d.</span><span class="sxs-lookup"><span data-stu-id="e787c-151">d.</span></span> <span data-ttu-id="e787c-152">Crie recurso de aplicativo web usando o plano de serviço de aplicativo no mesmo grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="e787c-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="e787c-153">e.</span><span class="sxs-lookup"><span data-stu-id="e787c-153">e.</span></span> <span data-ttu-id="e787c-154">Defina as credenciais de implantação.</span><span class="sxs-lookup"><span data-stu-id="e787c-154">Set the deployment credentials.</span></span> <span data-ttu-id="e787c-155">Essas credenciais de implantação se aplicam a todos os aplicativos web em sua assinatura.</span><span class="sxs-lookup"><span data-stu-id="e787c-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="e787c-156">Não use caracteres especiais no nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="e787c-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="e787c-157">f.</span><span class="sxs-lookup"><span data-stu-id="e787c-157">f.</span></span> <span data-ttu-id="e787c-158">Configurar o aplicativo web para aceitar as implantações do Git local e a exibição de *URL de implantação do Git*.</span><span class="sxs-lookup"><span data-stu-id="e787c-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="e787c-159">**Anote essa URL para referência posterior**.</span><span class="sxs-lookup"><span data-stu-id="e787c-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="e787c-160">g.</span><span class="sxs-lookup"><span data-stu-id="e787c-160">g.</span></span> <span data-ttu-id="e787c-161">Exibição de *URL do aplicativo web*.</span><span class="sxs-lookup"><span data-stu-id="e787c-161">Display the *web app URL*.</span></span> <span data-ttu-id="e787c-162">Navegue até essa URL para ver o aplicativo web em branco.</span><span class="sxs-lookup"><span data-stu-id="e787c-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="e787c-163">**Anote essa URL para referência posterior**.</span><span class="sxs-lookup"><span data-stu-id="e787c-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="e787c-164">Usando um shell de comando no computador local, navegue até a pasta do projeto do aplicativo web (por exemplo, `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="e787c-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="e787c-165">Execute os seguintes comandos para configurar o Git para enviar por push para a URL de implantação:</span><span class="sxs-lookup"><span data-stu-id="e787c-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="e787c-166">a.</span><span class="sxs-lookup"><span data-stu-id="e787c-166">a.</span></span> <span data-ttu-id="e787c-167">Adicione a URL remota no repositório local.</span><span class="sxs-lookup"><span data-stu-id="e787c-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="e787c-168">b.</span><span class="sxs-lookup"><span data-stu-id="e787c-168">b.</span></span> <span data-ttu-id="e787c-169">Enviar por push o local *mestre* ramificar para o *azure prod* do remote *mestre* branch.</span><span class="sxs-lookup"><span data-stu-id="e787c-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="e787c-170">Você será solicitado para as credenciais de implantação que você criou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e787c-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="e787c-171">Observe a saída no shell de comando.</span><span class="sxs-lookup"><span data-stu-id="e787c-171">Observe the output in the command shell.</span></span> <span data-ttu-id="e787c-172">Azure cria o aplicativo ASP.NET Core remotamente.</span><span class="sxs-lookup"><span data-stu-id="e787c-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="e787c-173">Em um navegador, navegue até a *URL do aplicativo Web* e observe o aplicativo foi compilado e implantado.</span><span class="sxs-lookup"><span data-stu-id="e787c-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="e787c-174">Alterações adicionais podem ser confirmadas no repositório Git local com `git commit`.</span><span class="sxs-lookup"><span data-stu-id="e787c-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="e787c-175">Essas alterações são enviadas por push para o Azure com o anterior `git push` comando.</span><span class="sxs-lookup"><span data-stu-id="e787c-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="e787c-176">Implantação com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e787c-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="e787c-177">*Observação: Esta seção aplica-se somente ao Windows. Os usuários do Linux e macOS devem fazer a alteração descrita na etapa 2 abaixo. Salve o arquivo e confirmar a alteração no repositório local com `git commit`. Por fim, envie por push a alteração com `git push`, como na primeira seção.*</span><span class="sxs-lookup"><span data-stu-id="e787c-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="e787c-178">O aplicativo já foi implantado no shell de comando.</span><span class="sxs-lookup"><span data-stu-id="e787c-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="e787c-179">Vamos usar ferramentas integradas do Visual Studio para implantar uma atualização para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e787c-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="e787c-180">Nos bastidores, o Visual Studio realiza a mesma coisa, como a ferramentas de linha de comando, mas dentro da interface de usuário familiar do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e787c-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="e787c-181">Abra *SimpleFeedReader.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e787c-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="e787c-182">No Gerenciador de soluções, abra *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e787c-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="e787c-183">Alteração `<h2>Simple Feed Reader</h2>` para `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="e787c-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="e787c-184">Pressione **Ctrl**+**Shift**+**B** para compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e787c-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="e787c-185">No Gerenciador de soluções, clique com botão direito no projeto e clique em **publicar**.</span><span class="sxs-lookup"><span data-stu-id="e787c-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Captura de tela mostrando o botão direito do mouse, publicar](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="e787c-187">Visual Studio pode criar um novo recurso do serviço de aplicativo, mas essa atualização será publicada a implantação existente.</span><span class="sxs-lookup"><span data-stu-id="e787c-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="e787c-188">No **escolher um destino de publicação** caixa de diálogo, selecione **serviço de aplicativo** na lista à esquerda e, em seguida, selecione **selecionar existente**.</span><span class="sxs-lookup"><span data-stu-id="e787c-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="e787c-189">Clique em **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="e787c-189">Click **Publish**.</span></span>
6. <span data-ttu-id="e787c-190">No **serviço de aplicativo** caixa de diálogo, confirme que a Microsoft ou conta organizacional usada para criar sua assinatura do Azure é exibida no canto superior direito.</span><span class="sxs-lookup"><span data-stu-id="e787c-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="e787c-191">Se não estiver, clique na lista suspensa e adicioná-lo.</span><span class="sxs-lookup"><span data-stu-id="e787c-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="e787c-192">Confirme se o Azure correto **assinatura** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="e787c-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="e787c-193">Para **modo de exibição**, selecione **grupo de recursos**.</span><span class="sxs-lookup"><span data-stu-id="e787c-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="e787c-194">Expanda o **AzureTutorial** grupo de recursos e, em seguida, selecione o aplicativo web existente.</span><span class="sxs-lookup"><span data-stu-id="e787c-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="e787c-195">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="e787c-195">Click **OK**.</span></span>

    ![Caixa de diálogo de publicar o serviço de aplicativo de captura de tela mostrando](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="e787c-197">Visual Studio compila e implanta o aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="e787c-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="e787c-198">Navegue até a URL do aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="e787c-198">Browse to the web app URL.</span></span> <span data-ttu-id="e787c-199">Validar que o `<h2>` modificação do elemento está ativo.</span><span class="sxs-lookup"><span data-stu-id="e787c-199">Validate that the `<h2>` element modification is live.</span></span>

![O aplicativo com o título alterado](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="e787c-201">Slots de implantação</span><span class="sxs-lookup"><span data-stu-id="e787c-201">Deployment slots</span></span>

<span data-ttu-id="e787c-202">Slots de implantação oferecer suporte a preparação de alterações sem afetar o aplicativo em execução na produção.</span><span class="sxs-lookup"><span data-stu-id="e787c-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="e787c-203">Depois que a versão de preparada do aplicativo é validada por uma equipe de garantia de qualidade, slots de preparo e produção podem ser trocados.</span><span class="sxs-lookup"><span data-stu-id="e787c-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="e787c-204">O aplicativo no ambiente de preparo é promovido para produção dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="e787c-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="e787c-205">As etapas a seguir criar um slot de preparo, implantar algumas alterações nele e trocar o slot de preparo em produção após a verificação.</span><span class="sxs-lookup"><span data-stu-id="e787c-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="e787c-206">Entrar para o [Azure Cloud Shell](https://shell.azure.com/bash), se não estiver conectado.</span><span class="sxs-lookup"><span data-stu-id="e787c-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="e787c-207">Crie o slot de preparo.</span><span class="sxs-lookup"><span data-stu-id="e787c-207">Create the staging slot.</span></span>

    <span data-ttu-id="e787c-208">a.</span><span class="sxs-lookup"><span data-stu-id="e787c-208">a.</span></span> <span data-ttu-id="e787c-209">Criar um slot de implantação com o nome *preparo*.</span><span class="sxs-lookup"><span data-stu-id="e787c-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="e787c-210">b.</span><span class="sxs-lookup"><span data-stu-id="e787c-210">b.</span></span> <span data-ttu-id="e787c-211">Configurar o slot de preparo para usar a implantação do Git local e obter o **preparo** URL de implantação.</span><span class="sxs-lookup"><span data-stu-id="e787c-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="e787c-212">**Anote essa URL para referência posterior**.</span><span class="sxs-lookup"><span data-stu-id="e787c-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="e787c-213">c.</span><span class="sxs-lookup"><span data-stu-id="e787c-213">c.</span></span> <span data-ttu-id="e787c-214">Exiba a URL do slot de preparo.</span><span class="sxs-lookup"><span data-stu-id="e787c-214">Display the staging slot's URL.</span></span> <span data-ttu-id="e787c-215">Navegue até a URL para ver o slot de preparo vazio.</span><span class="sxs-lookup"><span data-stu-id="e787c-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="e787c-216">**Anote essa URL para referência posterior**.</span><span class="sxs-lookup"><span data-stu-id="e787c-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="e787c-217">Em um editor de texto ou o Visual Studio, modifique *Pages* novamente para que o `<h2>` elemento lê `<h2>Simple Feed Reader - V3</h2>` e salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="e787c-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="e787c-218">Confirmar o arquivo para o repositório Git local, usando o **alterações** página no Visual Studio *Team Explorer* guia, ou digitando o seguinte usando o shell de comando do computador local:</span><span class="sxs-lookup"><span data-stu-id="e787c-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="e787c-219">Usando o shell de comando do computador local, adicione a URL de implantação de preparo como um Git remoto e enviar por push as alterações confirmadas:</span><span class="sxs-lookup"><span data-stu-id="e787c-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="e787c-220">a.</span><span class="sxs-lookup"><span data-stu-id="e787c-220">a.</span></span> <span data-ttu-id="e787c-221">Adicione a URL remota para a preparação para o repositório Git local.</span><span class="sxs-lookup"><span data-stu-id="e787c-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="e787c-222">b.</span><span class="sxs-lookup"><span data-stu-id="e787c-222">b.</span></span> <span data-ttu-id="e787c-223">Enviar por push o local *mestre* ramificar para o *azure preparo* do remote *mestre* branch.</span><span class="sxs-lookup"><span data-stu-id="e787c-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="e787c-224">Aguarde enquanto o Azure cria e implanta o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e787c-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="e787c-225">Para verificar se V3 foi implantado para o slot de preparo, abra duas janelas de navegador.</span><span class="sxs-lookup"><span data-stu-id="e787c-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="e787c-226">Em uma janela, navegue até a URL do aplicativo web original.</span><span class="sxs-lookup"><span data-stu-id="e787c-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="e787c-227">Em outra janela, navegue até a URL do aplicativo web preparo.</span><span class="sxs-lookup"><span data-stu-id="e787c-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="e787c-228">A URL de produção serve V2 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e787c-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="e787c-229">A URL de preparo serve V3 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e787c-229">The staging URL serves V3 of the app.</span></span>

    ![Captura de tela de comparar as janelas do navegador](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="e787c-231">No Cloud Shell, troca o slot de preparo verificado/começando-up para produção.</span><span class="sxs-lookup"><span data-stu-id="e787c-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="e787c-232">Verifique se que a troca ocorreu ao atualizar as janelas do navegador de dois.</span><span class="sxs-lookup"><span data-stu-id="e787c-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Comparando as janelas do navegador após a troca](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="e787c-234">Resumo</span><span class="sxs-lookup"><span data-stu-id="e787c-234">Summary</span></span>

<span data-ttu-id="e787c-235">Nesta seção, as tarefas a seguir foram concluídas:</span><span class="sxs-lookup"><span data-stu-id="e787c-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="e787c-236">Baixou e compilou o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="e787c-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="e787c-237">Criado um aplicativo serviço de aplicativo Web usando o Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="e787c-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="e787c-238">Implantou o aplicativo de exemplo para o Azure usando Git.</span><span class="sxs-lookup"><span data-stu-id="e787c-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="e787c-239">Implantada uma alteração para o aplicativo usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e787c-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="e787c-240">Adicionado um slot de preparo para o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="e787c-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="e787c-241">Implantasse uma atualização para o slot de preparo.</span><span class="sxs-lookup"><span data-stu-id="e787c-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="e787c-242">Trocado os slots de preparo e produção.</span><span class="sxs-lookup"><span data-stu-id="e787c-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="e787c-243">Na próxima seção, você aprenderá a criar um pipeline de DevOps com Pipelines do Azure.</span><span class="sxs-lookup"><span data-stu-id="e787c-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="e787c-244">Leitura adicional</span><span class="sxs-lookup"><span data-stu-id="e787c-244">Additional reading</span></span>

* [<span data-ttu-id="e787c-245">Visão geral de aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="e787c-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="e787c-246">Criar um aplicativo web .NET Core e o banco de dados SQL no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e787c-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="e787c-247">Configurar as credenciais de implantação do serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e787c-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="e787c-248">Configurar ambientes de preparo no serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e787c-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
