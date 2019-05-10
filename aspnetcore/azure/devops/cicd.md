---
title: Integração contínua e implantação - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Integração contínua e implantação em DevOps com o ASP.NET Core e o Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893993"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="77242-103">Integração contínua e implantação</span><span class="sxs-lookup"><span data-stu-id="77242-103">Continuous integration and deployment</span></span>

<span data-ttu-id="77242-104">No capítulo anterior, você criou um repositório Git local para o aplicativo de leitor de Feed simples.</span><span class="sxs-lookup"><span data-stu-id="77242-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="77242-105">Neste capítulo, você irá publicar esse código em um repositório do GitHub e construir um pipeline de serviços de DevOps do Azure usando os Pipelines do Azure.</span><span class="sxs-lookup"><span data-stu-id="77242-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="77242-106">O pipeline permite compilações contínuas e implantações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77242-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="77242-107">Qualquer confirmação para o repositório GitHub dispara uma compilação e uma implantação em slot de preparo do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="77242-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="77242-108">Nesta seção, você concluirá as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="77242-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="77242-109">Publicar o código do aplicativo no GitHub</span><span class="sxs-lookup"><span data-stu-id="77242-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="77242-110">Desconectar-se a implantação do Git local</span><span class="sxs-lookup"><span data-stu-id="77242-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="77242-111">Criar uma organização de DevOps do Azure</span><span class="sxs-lookup"><span data-stu-id="77242-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="77242-112">Criar um projeto de equipe nos serviços de DevOps do Azure</span><span class="sxs-lookup"><span data-stu-id="77242-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="77242-113">Criar uma definição de compilação</span><span class="sxs-lookup"><span data-stu-id="77242-113">Create a build definition</span></span>
* <span data-ttu-id="77242-114">Criar um pipeline de lançamento</span><span class="sxs-lookup"><span data-stu-id="77242-114">Create a release pipeline</span></span>
* <span data-ttu-id="77242-115">Confirmar alterações no GitHub e implantar automaticamente no Azure</span><span class="sxs-lookup"><span data-stu-id="77242-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="77242-116">Examinar o pipeline de Pipelines do Azure</span><span class="sxs-lookup"><span data-stu-id="77242-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="77242-117">Publicar o código do aplicativo no GitHub</span><span class="sxs-lookup"><span data-stu-id="77242-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="77242-118">Abra uma janela do navegador e navegue até `https://github.com`.</span><span class="sxs-lookup"><span data-stu-id="77242-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="77242-119">Clique o **+** lista suspensa no cabeçalho e selecione **novo repositório**:</span><span class="sxs-lookup"><span data-stu-id="77242-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Opção novo repositório do GitHub](media/cicd/github-new-repo.png)

1. <span data-ttu-id="77242-121">Selecione sua conta na **proprietário** lista suspensa e insira *leitor de feeds simples* no **nome do repositório** caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="77242-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="77242-122">Clique o **criar repositório** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="77242-123">Abra o shell de comando do seu computador local.</span><span class="sxs-lookup"><span data-stu-id="77242-123">Open your local machine's command shell.</span></span> <span data-ttu-id="77242-124">Navegue até o diretório no qual o *leitor de feeds simples* repositório Git é armazenado.</span><span class="sxs-lookup"><span data-stu-id="77242-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="77242-125">Renomear existente *origin* remoto para *upstream*.</span><span class="sxs-lookup"><span data-stu-id="77242-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="77242-126">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="77242-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="77242-127">Adicione um novo *origem* remoto que aponta para a sua cópia do repositório no GitHub.</span><span class="sxs-lookup"><span data-stu-id="77242-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="77242-128">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="77242-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="77242-129">Publica seu repositório Git local para o repositório GitHub recém-criado.</span><span class="sxs-lookup"><span data-stu-id="77242-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="77242-130">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="77242-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="77242-131">Abra uma janela do navegador e navegue até `https://github.com/<GitHub_username>/simple-feed-reader/`.</span><span class="sxs-lookup"><span data-stu-id="77242-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="77242-132">Valide que seu código aparece no repositório do GitHub.</span><span class="sxs-lookup"><span data-stu-id="77242-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="77242-133">Desconectar-se a implantação do Git local</span><span class="sxs-lookup"><span data-stu-id="77242-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="77242-134">Remova a implantação Git local com as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="77242-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="77242-135">Pipelines do Azure (um serviço de DevOps do Azure) substitui e amplia essa funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="77242-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="77242-136">Abra o [portal do Azure](https://portal.azure.com/)e navegue até a *de preparo (mywebapp\<unique_number\>/de preparo)* aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="77242-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="77242-137">O aplicativo Web pode estar localizado rapidamente digitando *preparo* na caixa de pesquisa do portal:</span><span class="sxs-lookup"><span data-stu-id="77242-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![termo de pesquisa do aplicativo Web de preparo](media/cicd/portal-search-box.png)

1. <span data-ttu-id="77242-139">Clique em **Central de implantação**.</span><span class="sxs-lookup"><span data-stu-id="77242-139">Click **Deployment Center**.</span></span> <span data-ttu-id="77242-140">Um novo painel é exibido.</span><span class="sxs-lookup"><span data-stu-id="77242-140">A new panel appears.</span></span> <span data-ttu-id="77242-141">Clique em **desconectar** para remover a configuração de controle de código-fonte Git local que foi adicionada no capítulo anterior.</span><span class="sxs-lookup"><span data-stu-id="77242-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="77242-142">Confirme a operação de remoção clicando o **Sim** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="77242-143">Navegue até a *mywebapp < unique_number >* o serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77242-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="77242-144">Como lembrete, caixa de pesquisa do portal pode ser usada para localizar rapidamente o serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77242-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="77242-145">Clique em **Central de implantação**.</span><span class="sxs-lookup"><span data-stu-id="77242-145">Click **Deployment Center**.</span></span> <span data-ttu-id="77242-146">Um novo painel é exibido.</span><span class="sxs-lookup"><span data-stu-id="77242-146">A new panel appears.</span></span> <span data-ttu-id="77242-147">Clique em **desconectar** para remover a configuração de controle de código-fonte Git local que foi adicionada no capítulo anterior.</span><span class="sxs-lookup"><span data-stu-id="77242-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="77242-148">Confirme a operação de remoção clicando o **Sim** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="77242-149">Criar uma organização de DevOps do Azure</span><span class="sxs-lookup"><span data-stu-id="77242-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="77242-150">Abra um navegador e navegue até a [página de criação de organização de DevOps do Azure](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="77242-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="77242-151">Digite um nome exclusivo para o **escolher um nome inesquecível** textbox para formar a URL para acessar a sua organização de DevOps do Azure.</span><span class="sxs-lookup"><span data-stu-id="77242-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="77242-152">Selecione o **Git** botão de opção, já que o código é hospedado em um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="77242-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="77242-153">Clique no botão **Continue (Continuar)**.</span><span class="sxs-lookup"><span data-stu-id="77242-153">Click the **Continue** button.</span></span> <span data-ttu-id="77242-154">Após uma breve espera, uma conta e um projeto de equipe, chamado *MyFirstProject*, são criados.</span><span class="sxs-lookup"><span data-stu-id="77242-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Página de criação de organização de DevOps do Azure](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="77242-156">Abra o email de confirmação indicando que a organização de DevOps do Azure e o projeto estão prontos para uso.</span><span class="sxs-lookup"><span data-stu-id="77242-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="77242-157">Clique o **inicie seu projeto** botão:</span><span class="sxs-lookup"><span data-stu-id="77242-157">Click the **Start your project** button:</span></span>

    ![Botão seu projeto inicial](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="77242-159">Um navegador é aberto  *\<account_name\>. visualstudio.com*.</span><span class="sxs-lookup"><span data-stu-id="77242-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="77242-160">Clique o *MyFirstProject* link para começar a configurar o pipeline de DevOps do projeto.</span><span class="sxs-lookup"><span data-stu-id="77242-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="77242-161">Configurar o pipeline de Pipelines do Azure</span><span class="sxs-lookup"><span data-stu-id="77242-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="77242-162">Há três etapas distintas para ser concluída.</span><span class="sxs-lookup"><span data-stu-id="77242-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="77242-163">As etapas nos resultados em um pipeline de DevOps operacional três seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="77242-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="77242-164">DevOps do Azure conceda acesso ao repositório do GitHub</span><span class="sxs-lookup"><span data-stu-id="77242-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="77242-165">Expanda o **ou compilar o código de um repositório externo** accordion.</span><span class="sxs-lookup"><span data-stu-id="77242-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="77242-166">Clique o **instalação compilar** botão:</span><span class="sxs-lookup"><span data-stu-id="77242-166">Click the **Setup Build** button:</span></span>

    ![Botão de compilação da configuração](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="77242-168">Selecione o **GitHub** opção a **selecionar uma fonte de** seção:</span><span class="sxs-lookup"><span data-stu-id="77242-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Selecione uma fonte - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="77242-170">A autorização é necessária antes de DevOps do Azure pode acessar seu repositório do GitHub.</span><span class="sxs-lookup"><span data-stu-id="77242-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="77242-171">Insira *< GitHub_username > conexão do GitHub* na **nome de Conexão** caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="77242-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="77242-172">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="77242-172">For example:</span></span>

    ![Nome da conexão do GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="77242-174">Se a autenticação de dois fatores é ativada em sua conta do GitHub, um token de acesso pessoal é necessário.</span><span class="sxs-lookup"><span data-stu-id="77242-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="77242-175">Clique nesse caso, o **autorizar com um token de acesso pessoal do GitHub** link.</span><span class="sxs-lookup"><span data-stu-id="77242-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="77242-176">Consulte a [instruções oficiais de criação de token de acesso pessoal do GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) para obter ajuda.</span><span class="sxs-lookup"><span data-stu-id="77242-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="77242-177">Somente o *repositório* escopo das permissões é necessária.</span><span class="sxs-lookup"><span data-stu-id="77242-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="77242-178">Caso contrário, clique o **autorizar usando OAuth** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="77242-179">Quando solicitado, entre sua conta do GitHub.</span><span class="sxs-lookup"><span data-stu-id="77242-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="77242-180">Em seguida, selecione a autorização para conceder acesso a sua organização de DevOps do Azure.</span><span class="sxs-lookup"><span data-stu-id="77242-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="77242-181">Se for bem-sucedido, um novo ponto de extremidade de serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="77242-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="77242-182">Clique no botão de reticências ao lado de **repositório** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="77242-183">Selecione o *< GitHub_username > / leitor de feeds simples* repositório na lista.</span><span class="sxs-lookup"><span data-stu-id="77242-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="77242-184">Clique o **selecionar** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="77242-185">Selecione o *mestre* ramificar a **branch padrão para compilações de manuais e programadas** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="77242-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="77242-186">Clique no botão **Continue (Continuar)**.</span><span class="sxs-lookup"><span data-stu-id="77242-186">Click the **Continue** button.</span></span> <span data-ttu-id="77242-187">Página seleção de modelo é exibido.</span><span class="sxs-lookup"><span data-stu-id="77242-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="77242-188">Criar a definição de compilação</span><span class="sxs-lookup"><span data-stu-id="77242-188">Create the build definition</span></span>

1. <span data-ttu-id="77242-189">Na página seleção do modelo, insira *ASP.NET Core* na caixa de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="77242-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![Na página do modelo de pesquisa de ASP.NET Core](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="77242-191">Os resultados da pesquisa de modelo são exibidos.</span><span class="sxs-lookup"><span data-stu-id="77242-191">The template search results appear.</span></span> <span data-ttu-id="77242-192">Passe o mouse sobre o **ASP.NET Core** modelo e clique no **aplicar** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="77242-193">O **tarefas** guia da definição de compilação é exibida.</span><span class="sxs-lookup"><span data-stu-id="77242-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="77242-194">Clique na guia **Gatilhos** .</span><span class="sxs-lookup"><span data-stu-id="77242-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="77242-195">Verifique as **habilitar a integração contínua** caixa.</span><span class="sxs-lookup"><span data-stu-id="77242-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="77242-196">Sob o **filtros de ramificação** seção, confirme se o **tipo** lista suspensa é definida como *Include*.</span><span class="sxs-lookup"><span data-stu-id="77242-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="77242-197">Defina as **especificação de Branch** lista suspensa para *mestre*.</span><span class="sxs-lookup"><span data-stu-id="77242-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Habilitar as configurações de integração contínua](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="77242-199">Essas configurações fazem com que uma compilação disparar quando qualquer alteração é enviada por push para o *mestre* branch do repositório do GitHub.</span><span class="sxs-lookup"><span data-stu-id="77242-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="77242-200">Integração contínua é testada na [confirmar alterações no GitHub e implantar automaticamente para o Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) seção.</span><span class="sxs-lookup"><span data-stu-id="77242-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="77242-201">Clique o **salvar e enfileirar** botão e, em seguida, selecione o **salvar** opção:</span><span class="sxs-lookup"><span data-stu-id="77242-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Botão Salvar](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="77242-203">A seguinte caixa de diálogo modal será exibida:</span><span class="sxs-lookup"><span data-stu-id="77242-203">The following modal dialog appears:</span></span>

    ![Salvar a definição de compilação - caixa de diálogo modal](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="77242-205">Usar a pasta padrão de *\\* e clique no **salvar** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="77242-206">Criar o pipeline de lançamento</span><span class="sxs-lookup"><span data-stu-id="77242-206">Create the release pipeline</span></span>

1. <span data-ttu-id="77242-207">Clique o **versões** guia de seu projeto de equipe.</span><span class="sxs-lookup"><span data-stu-id="77242-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="77242-208">Clique o **novo pipeline** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-208">Click the **New pipeline** button.</span></span>

    ![Guia versões - botão novo de definição](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="77242-210">O painel de seleção de modelo é exibido.</span><span class="sxs-lookup"><span data-stu-id="77242-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="77242-211">Na página seleção do modelo, insira *serviço de aplicativo* na caixa de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="77242-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Caixa de pesquisa de modelo de pipeline de lançamento](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="77242-213">Os resultados da pesquisa de modelo são exibidos.</span><span class="sxs-lookup"><span data-stu-id="77242-213">The template search results appear.</span></span> <span data-ttu-id="77242-214">Passe o mouse sobre o **implantação de serviço de aplicativo do Azure com o Slot** modelo e clique no **aplicar** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="77242-215">O **Pipeline** guia do pipeline de lançamento é exibida.</span><span class="sxs-lookup"><span data-stu-id="77242-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Guia Pipeline do pipeline de lançamento](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="77242-217">Clique o **Add** botão na **artefatos** caixa.</span><span class="sxs-lookup"><span data-stu-id="77242-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="77242-218">O **adicionar um artefato** painel é exibido:</span><span class="sxs-lookup"><span data-stu-id="77242-218">The **Add artifact** panel appears:</span></span>

    ![Pipeline de lançamento - Adicionar painel de artefato](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="77242-220">Selecione o **construir** lado a lado do **tipo de fonte** seção.</span><span class="sxs-lookup"><span data-stu-id="77242-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="77242-221">Esse tipo permite a vinculação do pipeline de lançamento para a definição de compilação.</span><span class="sxs-lookup"><span data-stu-id="77242-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="77242-222">Selecione *MyFirstProject* da **projeto** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="77242-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="77242-223">Selecione o nome de definição de compilação *MyFirstProject do ASP.NET Core-CI*, da **fonte (definição de compilação)** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="77242-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="77242-224">Selecione *mais recente* da **versão padrão** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="77242-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="77242-225">Essa opção cria os artefatos produzidos pela execução mais recente da definição de compilação.</span><span class="sxs-lookup"><span data-stu-id="77242-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="77242-226">Substitua o texto na **alias de origem** caixa de texto com *Drop*.</span><span class="sxs-lookup"><span data-stu-id="77242-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="77242-227">Clique no botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="77242-227">Click the **Add** button.</span></span> <span data-ttu-id="77242-228">O **artefatos** seção será atualizado para exibir as alterações.</span><span class="sxs-lookup"><span data-stu-id="77242-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="77242-229">Clique no ícone de raio para habilitar implantações contínuas:</span><span class="sxs-lookup"><span data-stu-id="77242-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Pipeline de lançamento artefatos - ícone de raio](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="77242-231">Com essa opção habilitada, uma implantação ocorre sempre que uma nova compilação está disponível.</span><span class="sxs-lookup"><span data-stu-id="77242-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="77242-232">Um **gatilho de implantação contínua** painel aparece à direita.</span><span class="sxs-lookup"><span data-stu-id="77242-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="77242-233">Clique no botão de alternância para habilitar o recurso.</span><span class="sxs-lookup"><span data-stu-id="77242-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="77242-234">Não é necessário habilitar o **gatilho de solicitação de Pull**.</span><span class="sxs-lookup"><span data-stu-id="77242-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="77242-235">Clique o **Add** na lista suspensa a **criar filtros de ramificação** seção.</span><span class="sxs-lookup"><span data-stu-id="77242-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="77242-236">Escolha o **ramificação de padrão da definição de compilação** opção.</span><span class="sxs-lookup"><span data-stu-id="77242-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="77242-237">Esse filtro faz com que a versão disparar apenas para uma compilação do repositório do GitHub *mestre* branch.</span><span class="sxs-lookup"><span data-stu-id="77242-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="77242-238">Clique no botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="77242-238">Click the **Save** button.</span></span> <span data-ttu-id="77242-239">Clique o **Okey** botão na resultante **salvar** caixa de diálogo modal.</span><span class="sxs-lookup"><span data-stu-id="77242-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="77242-240">Clique o **ambiente 1** caixa.</span><span class="sxs-lookup"><span data-stu-id="77242-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="77242-241">Uma **ambiente** painel aparece à direita.</span><span class="sxs-lookup"><span data-stu-id="77242-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="77242-242">Alterar o *ambiente 1* texto na **nome do ambiente** caixa de texto *produção*.</span><span class="sxs-lookup"><span data-stu-id="77242-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Pipeline de lançamento - caixa de texto de nome de ambiente](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="77242-244">Clique o **1 fase, 2 tarefas** link na **produção** caixa:</span><span class="sxs-lookup"><span data-stu-id="77242-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Pipeline de lançamento - link.png do ambiente de produção](media/cicd/vsts-production-link.png)

    <span data-ttu-id="77242-246">O **tarefas** guia do ambiente é exibida.</span><span class="sxs-lookup"><span data-stu-id="77242-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="77242-247">Clique o **implantar o serviço de aplicativo do Azure ao Slot** tarefa.</span><span class="sxs-lookup"><span data-stu-id="77242-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="77242-248">Suas configurações aparecem em um painel à direita.</span><span class="sxs-lookup"><span data-stu-id="77242-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="77242-249">Selecione a assinatura do Azure associada com o serviço de aplicativo da **assinatura do Azure** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="77242-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="77242-250">Depois de selecionadas, clique o **autorizar** botão.</span><span class="sxs-lookup"><span data-stu-id="77242-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="77242-251">Selecione *aplicativo Web* da **tipo de aplicativo** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="77242-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="77242-252">Selecione *mywebapp / < unique_number / >* da **nome do serviço de aplicativo** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="77242-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="77242-253">Selecione *AzureTutorial* da **grupo de recursos** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="77242-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="77242-254">Selecione *preparo* da **Slot** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="77242-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="77242-255">Clique no botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="77242-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="77242-256">Passe o mouse sobre o nome do pipeline de versão padrão.</span><span class="sxs-lookup"><span data-stu-id="77242-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="77242-257">Clique no ícone de lápis para editá-lo.</span><span class="sxs-lookup"><span data-stu-id="77242-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="77242-258">Use *MyFirstProject do ASP.NET Core-CD* como o nome.</span><span class="sxs-lookup"><span data-stu-id="77242-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Nome do pipeline de lançamento](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="77242-260">Clique no botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="77242-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="77242-261">Confirmar alterações no GitHub e implantar automaticamente no Azure</span><span class="sxs-lookup"><span data-stu-id="77242-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="77242-262">Abra *SimpleFeedReader.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="77242-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="77242-263">No Gerenciador de soluções, abra *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="77242-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="77242-264">Alteração `<h2>Simple Feed Reader - V3</h2>` para `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="77242-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="77242-265">Pressione **Ctrl**+**Shift**+**B** para compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77242-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="77242-266">Confirme o arquivo para o repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="77242-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="77242-267">Use o **alterações** página no Visual Studio *Team Explorer* guia ou execute o seguinte usando o shell de comando do computador local:</span><span class="sxs-lookup"><span data-stu-id="77242-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="77242-268">Enviar por push a alteração na *mestre* ramificar para o *origem* remoto do seu repositório GitHub:</span><span class="sxs-lookup"><span data-stu-id="77242-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="77242-269">A confirmação é exibida no repositório do GitHub *mestre* branch:</span><span class="sxs-lookup"><span data-stu-id="77242-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![Confirmação do GitHub no branch mestre](media/cicd/github-commit.png)

    <span data-ttu-id="77242-271">A compilação é disparada, como integração contínua está habilitada na definição de build **gatilhos** guia:</span><span class="sxs-lookup"><span data-stu-id="77242-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![Habilitar a integração contínua](media/cicd/enable-ci.png)

1. <span data-ttu-id="77242-273">Navegue até a **enfileirado** guia da **Pipelines do Azure** > **compilações** página nos serviços de DevOps do Azure.</span><span class="sxs-lookup"><span data-stu-id="77242-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="77242-274">Compilação enfileirada mostra a ramificação e confirme que disparou a compilação:</span><span class="sxs-lookup"><span data-stu-id="77242-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![compilação enfileirada](media/cicd/build-queued.png)

1. <span data-ttu-id="77242-276">A compilação for bem-sucedida, ocorre uma implantação do Azure.</span><span class="sxs-lookup"><span data-stu-id="77242-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="77242-277">Navegue até o aplicativo no navegador.</span><span class="sxs-lookup"><span data-stu-id="77242-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="77242-278">Observe que o texto "V4" é exibido no título:</span><span class="sxs-lookup"><span data-stu-id="77242-278">Notice that the "V4" text appears in the heading:</span></span>

    ![aplicativo atualizado](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="77242-280">Examinar o pipeline de Pipelines do Azure</span><span class="sxs-lookup"><span data-stu-id="77242-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="77242-281">Definição de compilação</span><span class="sxs-lookup"><span data-stu-id="77242-281">Build definition</span></span>

<span data-ttu-id="77242-282">Uma definição de compilação foi criada com o nome *MyFirstProject do ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="77242-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="77242-283">Após a conclusão, a compilação produz um *. zip* arquivo, incluindo os ativos a serem publicadas.</span><span class="sxs-lookup"><span data-stu-id="77242-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="77242-284">O pipeline de lançamento implanta esses ativos do Azure.</span><span class="sxs-lookup"><span data-stu-id="77242-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="77242-285">A definição de compilação **tarefas** guia lista as etapas individuais que está sendo usadas.</span><span class="sxs-lookup"><span data-stu-id="77242-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="77242-286">Há cinco tarefas de compilação.</span><span class="sxs-lookup"><span data-stu-id="77242-286">There are five build tasks.</span></span>

![tarefas de definição de compilação](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="77242-288">**Restaure** &mdash; executa o `dotnet restore` comando restaurar pacotes do NuGet do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77242-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="77242-289">O pacote padrão feed usado é nuget.org.</span><span class="sxs-lookup"><span data-stu-id="77242-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="77242-290">**Crie** &mdash; executa o `dotnet build --configuration release` comando para compilar o código do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77242-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="77242-291">Isso `--configuration` opção é usada para produzir uma versão otimizada do código, que é adequado para implantação em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="77242-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="77242-292">Modificar a *BuildConfiguration* variável na definição de compilação **variáveis** guia se, por exemplo, uma configuração de depuração é necessária.</span><span class="sxs-lookup"><span data-stu-id="77242-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="77242-293">**Teste** &mdash; executa o `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` comando para executar testes de unidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="77242-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="77242-294">Testes de unidade são executados em qualquer linguagem C# projeto de correspondência a `**/*Tests/*.csproj` padrão glob.</span><span class="sxs-lookup"><span data-stu-id="77242-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="77242-295">Resultados de teste são salvos em um *trx* arquivo no local especificado pelo `--results-directory` opção.</span><span class="sxs-lookup"><span data-stu-id="77242-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="77242-296">Se qualquer teste falhar, a compilação falhará e não será implantada.</span><span class="sxs-lookup"><span data-stu-id="77242-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="77242-297">Para verificar se o trabalho de testes de unidade, modifique *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* propositadamente quebrar um dos testes.</span><span class="sxs-lookup"><span data-stu-id="77242-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="77242-298">Por exemplo, altere `Assert.True(result.Count > 0);` à `Assert.False(result.Count > 0);` no `Returns_News_Stories_Given_Valid_Uri` método.</span><span class="sxs-lookup"><span data-stu-id="77242-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="77242-299">Confirme e envie a alteração ao GitHub.</span><span class="sxs-lookup"><span data-stu-id="77242-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="77242-300">A compilação é disparada e falha.</span><span class="sxs-lookup"><span data-stu-id="77242-300">The build is triggered and fails.</span></span> <span data-ttu-id="77242-301">O status do pipeline de compilação muda para **falha**.</span><span class="sxs-lookup"><span data-stu-id="77242-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="77242-302">Reverta a alteração, a confirmação e o envio por push novamente.</span><span class="sxs-lookup"><span data-stu-id="77242-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="77242-303">A compilação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="77242-303">The build succeeds.</span></span>

1. <span data-ttu-id="77242-304">**Publique** &mdash; executa o `dotnet publish --configuration release --output <local_path_on_build_agent>` comando para produzir uma *. zip* arquivo com os artefatos a serem implantados.</span><span class="sxs-lookup"><span data-stu-id="77242-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="77242-305">O `--output` opção especifica o local de publicação do *. zip* arquivo.</span><span class="sxs-lookup"><span data-stu-id="77242-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="77242-306">Se o local é especificado passando um [variável predefinida](/azure/devops/pipelines/build/variables) denominado `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="77242-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="77242-307">Essa variável se expande para um caminho local, como *c:\agent\_work\1\a*, no agente de compilação.</span><span class="sxs-lookup"><span data-stu-id="77242-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="77242-308">**Publicar artefato** &mdash; Publishes as *. zip* produzido pelo arquivo o **publicar** tarefa.</span><span class="sxs-lookup"><span data-stu-id="77242-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="77242-309">A tarefa aceita o *. zip* local como um parâmetro, que é a variável predefinida do arquivo `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="77242-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="77242-310">O *. zip* arquivo é publicado como uma pasta chamada *drop*.</span><span class="sxs-lookup"><span data-stu-id="77242-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="77242-311">Clique na definição de compilação **resumo** link para exibir um histórico das compilações com a definição:</span><span class="sxs-lookup"><span data-stu-id="77242-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Histórico de definição de compilação do captura de tela mostrando](media/cicd/build-definition-summary.png)

<span data-ttu-id="77242-313">Na página resultante, clique no link correspondente ao número de build exclusivo:</span><span class="sxs-lookup"><span data-stu-id="77242-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Página resumida de definição de compilação captura de tela mostrando](media/cicd/build-definition-completed.png)

<span data-ttu-id="77242-315">Um resumo dessa compilação específica é exibido.</span><span class="sxs-lookup"><span data-stu-id="77242-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="77242-316">Clique o **artefatos** guia e observe o *drop* produzida pela compilação de pasta é listada:</span><span class="sxs-lookup"><span data-stu-id="77242-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Captura de tela mostrando os artefatos de definição de compilação - pasta-depósito](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="77242-318">Use o **Baixe** e **explorar** links para inspecionar os artefatos publicados.</span><span class="sxs-lookup"><span data-stu-id="77242-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="77242-319">Pipeline de lançamento</span><span class="sxs-lookup"><span data-stu-id="77242-319">Release pipeline</span></span>

<span data-ttu-id="77242-320">Um pipeline de lançamento foi criado com o nome *MyFirstProject do ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="77242-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Visão geral da captura de tela mostrando release pipeline](media/cicd/release-definition-overview.png)

<span data-ttu-id="77242-322">Os dois principais componentes do pipeline de lançamento são as **artefatos** e o **ambientes**.</span><span class="sxs-lookup"><span data-stu-id="77242-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="77242-323">Clicando na caixa na **artefatos** seção revela o seguinte painel:</span><span class="sxs-lookup"><span data-stu-id="77242-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Captura de tela mostrando artefatos de pipeline da versão](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="77242-325">O **fonte (definição de compilação)** valor representa a definição de compilação ao qual esse pipeline de versão está vinculada.</span><span class="sxs-lookup"><span data-stu-id="77242-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="77242-326">O *. zip* arquivo produzido por uma execução bem-sucedida da definição de compilação é fornecido para o *produção* ambiente para implantação no Azure.</span><span class="sxs-lookup"><span data-stu-id="77242-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="77242-327">Clique o *1 fase, 2 tarefas* link na *produção* caixa do ambiente para exibir as tarefas de pipeline de lançamento:</span><span class="sxs-lookup"><span data-stu-id="77242-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Captura de tela mostrando tarefas de pipeline de lançamento](media/cicd/release-definition-tasks.png)

<span data-ttu-id="77242-329">O pipeline de lançamento consiste em duas tarefas: *Implantar o serviço de aplicativo do Azure ao Slot* e *gerenciar permuta do Slot de serviço de aplicativo do Azure -*.</span><span class="sxs-lookup"><span data-stu-id="77242-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="77242-330">Clicar a primeira tarefa revela a configuração de tarefa a seguir:</span><span class="sxs-lookup"><span data-stu-id="77242-330">Clicking the first task reveals the following task configuration:</span></span>

![Tarefa de implantação do pipeline de lançamento captura de tela mostrando](media/cicd/release-definition-task1.png)

<span data-ttu-id="77242-332">A assinatura do Azure, o tipo de serviço, o nome do aplicativo web, o grupo de recursos e o slot de implantação são definidos na tarefa de implantação.</span><span class="sxs-lookup"><span data-stu-id="77242-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="77242-333">O **pacote ou pasta** caixa de texto contém o *. zip* caminho do arquivo a ser extraído e implantado para o *preparo* slot do *mywebapp\<exclusivo número\>*  aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="77242-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="77242-334">Clicar a tarefa de troca de slot revela a configuração de tarefa a seguir:</span><span class="sxs-lookup"><span data-stu-id="77242-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Tarefa de troca de slot captura de tela mostrando release pipeline](media/cicd/release-definition-task2.png)

<span data-ttu-id="77242-336">A assinatura, grupo de recursos, tipo de serviço, nome do aplicativo web e detalhes do slot de implantação são fornecidas.</span><span class="sxs-lookup"><span data-stu-id="77242-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="77242-337">O **troca com produção** caixa de seleção está marcada.</span><span class="sxs-lookup"><span data-stu-id="77242-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="77242-338">Consequentemente, os bits implantados para o *preparo* slot são trocadas no ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="77242-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="77242-339">Leitura adicional</span><span class="sxs-lookup"><span data-stu-id="77242-339">Additional reading</span></span>

* [<span data-ttu-id="77242-340">Criar seu primeiro pipeline com o Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="77242-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="77242-341">Projeto de compilação e .NET Core</span><span class="sxs-lookup"><span data-stu-id="77242-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="77242-342">Implantar um aplicativo web com Pipelines do Azure</span><span class="sxs-lookup"><span data-stu-id="77242-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
