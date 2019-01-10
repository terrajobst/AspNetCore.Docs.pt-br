---
title: Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio
author: rick-anderson
description: Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: e71cb8badbbc852685c845e6bbb0bbb12ab5499f
ms.sourcegitcommit: 68a3081dd175d6518d1bfa31b4712bd8a2dd3864
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53577806"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="ffd1c-103">Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffd1c-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="ffd1c-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Cesar Blum Silveira](https://github.com/cesarbs)</span><span class="sxs-lookup"><span data-stu-id="ffd1c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="ffd1c-105">Confira [Publicar no Azure do Visual Studio para Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) se você estiver trabalhando no macOS.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-105">See [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) if you are working on macOS.</span></span>

<span data-ttu-id="ffd1c-106">Para solucionar um problema de implantação do Serviço de Aplicativo, confira <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-106">To troubleshoot an App Service deployment issue, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="set-up"></a><span data-ttu-id="ffd1c-107">Configurar</span><span class="sxs-lookup"><span data-stu-id="ffd1c-107">Set up</span></span>

* <span data-ttu-id="ffd1c-108">Abra uma [conta do Azure gratuita](https://azure.microsoft.com/free/dotnet/) se você não tiver uma.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="ffd1c-109">Como criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="ffd1c-109">Create a web app</span></span>

<span data-ttu-id="ffd1c-110">Na página inicial do Visual Studio, selecione **Arquivo > Novo > Projeto...**</span><span class="sxs-lookup"><span data-stu-id="ffd1c-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu Arquivo](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="ffd1c-112">Complete a caixa de diálogo **Novo Projeto**:</span><span class="sxs-lookup"><span data-stu-id="ffd1c-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="ffd1c-113">No painel esquerdo, selecione **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="ffd1c-114">No painel central, toque em **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ffd1c-115">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-115">Select **OK**.</span></span>

![Caixa de diálogo Novo Projeto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="ffd1c-117">Na caixa de diálogo **Novo Aplicativo Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="ffd1c-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="ffd1c-118">Selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-118">Select **Web Application**.</span></span>
* <span data-ttu-id="ffd1c-119">Selecione **Mudar Autenticação**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-119">Select **Change Authentication**.</span></span>

![Caixa de diálogo Novo Projeto](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="ffd1c-121">A caixa de diálogo **Mudar Autenticação** é exibida.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="ffd1c-122">Selecione **Contas de Usuário Individuais**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="ffd1c-123">Selecione **OK** para retornar para o **Novo Aplicativo Web do ASP.NET Core**, em seguida, selecione **OK** novamente.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Caixa de diálogo Nova autenticação da Web do ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="ffd1c-125">O Visual Studio cria a solução.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="ffd1c-126">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ffd1c-126">Run the app</span></span>

* <span data-ttu-id="ffd1c-127">Pressione CTRL+F5 para executar o projeto.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="ffd1c-128">Teste os links **Sobre** e **Contato**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-128">Test the **About** and **Contact** links.</span></span>

![Aplicativo Web aberto no Microsoft Edge no localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="ffd1c-130">Registrar um usuário</span><span class="sxs-lookup"><span data-stu-id="ffd1c-130">Register a user</span></span>

* <span data-ttu-id="ffd1c-131">Selecione **Registrar** e registre um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="ffd1c-132">Você pode usar um endereço de email fictício.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-132">You can use a fictitious email address.</span></span> <span data-ttu-id="ffd1c-133">Ao enviar, a página exibirá o seguinte erro:</span><span class="sxs-lookup"><span data-stu-id="ffd1c-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="ffd1c-134">*"Erro Interno do Servidor: uma operação de banco de dados falhou ao processar a solicitação. Exceção do SQL: não é possível abrir o banco de dados. A aplicação de migrações existentes ao contexto do BD do Aplicativo pode resolver esse problema."*</span><span class="sxs-lookup"><span data-stu-id="ffd1c-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="ffd1c-135">Selecione **Aplicar Migrações** e, depois que a página for atualizada, atualize a página.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Erro Interno do Servidor: uma operação de banco de dados falhou ao processar a solicitação.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="ffd1c-139">O aplicativo exibe o email usado para registrar o novo usuário e um link **Fazer logout**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Aplicativo Web aberto no Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="ffd1c-142">Implantar o aplicativo no Azure</span><span class="sxs-lookup"><span data-stu-id="ffd1c-142">Deploy the app to Azure</span></span>

<span data-ttu-id="ffd1c-143">Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Publicar...**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu contextual aberto com o link Publicar realçado](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="ffd1c-145">Na caixa de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="ffd1c-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="ffd1c-146">Selecione **Serviço de Aplicativo do Microsoft Azure**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="ffd1c-147">Selecione o ícone de engrenagem e, em seguida, **Criar Perfil**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="ffd1c-148">Selecione **Criar Perfil**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-148">Select **Create Profile**.</span></span>

![Caixa de diálogo Publicar](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="ffd1c-150">Criar recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="ffd1c-150">Create Azure resources</span></span>

<span data-ttu-id="ffd1c-151">A caixa de diálogo **Criar Serviço de Aplicativo** será exibida:</span><span class="sxs-lookup"><span data-stu-id="ffd1c-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="ffd1c-152">Insira sua assinatura.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-152">Enter your subscription.</span></span>
* <span data-ttu-id="ffd1c-153">Os campos de entrada **Nome do Aplicativo**, **Grupo de Recursos** e **Plano do Serviço de Aplicativo** serão populados.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="ffd1c-154">Você pode manter esses nomes ou alterá-los.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-154">You can keep these names or change them.</span></span>

![Caixa de diálogo Serviço de Aplicativo](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="ffd1c-156">Selecione a guia **Serviços** para criar um novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="ffd1c-157">Selecione o ícone verde **+** para criar um novo Banco de Dados SQL</span><span class="sxs-lookup"><span data-stu-id="ffd1c-157">Select the green **+** icon to create a new SQL Database</span></span>

![Novo Banco de Dados SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="ffd1c-159">Selecione **Novo...** na caixa de diálogo **Configurar Banco de Dados SQL** para criar um novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Novo servidor e Banco de Dados SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="ffd1c-161">A caixa de diálogo **Configurar o SQL Server** é exibida.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="ffd1c-162">Insira um nome de usuário do administrador e a senha e, em seguida, selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="ffd1c-163">Você pode manter o **Nome do Servidor** padrão.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="ffd1c-164">“admin” não é permitido como o nome de usuário administrador.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-164">"admin" isn't allowed as the administrator user name.</span></span>

![Caixa de diálogo Configurar o SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="ffd1c-166">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-166">Select **OK**.</span></span>

<span data-ttu-id="ffd1c-167">O Visual Studio retorna para a caixa de diálogo **Criar Serviço de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="ffd1c-168">Selecione **Criar** na caixa de diálogo **Criar Serviço de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Caixa de diálogo Configurar o Banco de Dados SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="ffd1c-170">O Visual Studio cria o aplicativo Web e o SQL Server no Azure.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="ffd1c-171">Esta etapa pode levar alguns minutos.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-171">This step can take a few minutes.</span></span> <span data-ttu-id="ffd1c-172">Para obter informações sobre os recursos criados, confira [Recursos adicionais](#additonal-resources).</span><span class="sxs-lookup"><span data-stu-id="ffd1c-172">For information on the resources created, see [Additional resources](#additonal-resources).</span></span>

<span data-ttu-id="ffd1c-173">Quando a implantação for concluída, selecione **Configurações**:</span><span class="sxs-lookup"><span data-stu-id="ffd1c-173">When deployment completes, select **Settings**:</span></span>

![Caixa de diálogo Configurar o SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="ffd1c-175">Na página **Configurações** da caixa de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="ffd1c-175">On the **Settings** page of the **Publish** dialog:</span></span>

  * <span data-ttu-id="ffd1c-176">Expanda **Bancos de Dados** e marque a opção **Usar esta cadeia de conexão no tempo de execução**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
  * <span data-ttu-id="ffd1c-177">Expanda **Migrações do Entity Framework** e marque a opção **Aplicar esta migração durante a publicação**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="ffd1c-178">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-178">Select **Save**.</span></span> <span data-ttu-id="ffd1c-179">O Visual Studio retorna para a caixa de diálogo **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Caixa de diálogo Publicar: Painel Configurações](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="ffd1c-181">Clique em **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-181">Click **Publish**.</span></span> <span data-ttu-id="ffd1c-182">O Visual Studio publica seu aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="ffd1c-183">Quando a implantação for concluída, o aplicativo será aberto em um navegador.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="ffd1c-184">Testar o aplicativo no Azure</span><span class="sxs-lookup"><span data-stu-id="ffd1c-184">Test your app in Azure</span></span>

* <span data-ttu-id="ffd1c-185">Teste os links **Sobre** e **Contato**</span><span class="sxs-lookup"><span data-stu-id="ffd1c-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="ffd1c-186">Registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="ffd1c-186">Register a new user</span></span>

![Aplicativo Web aberto no Microsoft Edge no Serviço de Aplicativo do Azure](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="ffd1c-188">Atualizar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="ffd1c-188">Update the app</span></span>

* <span data-ttu-id="ffd1c-189">Edite a página do Razor *Pages/About.cshtml* e altere seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="ffd1c-190">Por exemplo, você pode modificar o parágrafo para dizer "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="ffd1c-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="ffd1c-191">Clique com o botão direito do mouse no projeto e selecione **Publicar...** novamente.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-191">Right-click on the project and select **Publish...** again.</span></span>

![Menu contextual aberto com o link Publicar realçado](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="ffd1c-193">Depois que o aplicativo for publicado, verifique se as alterações feitas estão disponíveis no Azure.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Verifique se a tarefa está concluída](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="ffd1c-195">Limpar</span><span class="sxs-lookup"><span data-stu-id="ffd1c-195">Clean up</span></span>

<span data-ttu-id="ffd1c-196">Quando você concluir o teste do aplicativo, acesse o [portal do Azure](https://portal.azure.com/) e exclua o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="ffd1c-197">Selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos criado.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Portal do Azure: Grupos de Recursos no menu da barra lateral](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="ffd1c-199">Na página **Grupos de recursos**, selecione **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-199">In the **Resource groups** page, select **Delete**.</span></span>

![Portal do Azure: página Grupos de Recursos](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="ffd1c-201">Insira o nome do grupo de recursos e selecione **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="ffd1c-202">O aplicativo e todos os outros recursos criados neste tutorial agora foram excluídos do Azure.</span><span class="sxs-lookup"><span data-stu-id="ffd1c-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="ffd1c-203">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="ffd1c-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additonal-resources"></a><span data-ttu-id="ffd1c-204">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ffd1c-204">Additonal resources</span></span>

* [<span data-ttu-id="ffd1c-205">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="ffd1c-205">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="ffd1c-206">Grupo de recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="ffd1c-206">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="ffd1c-207">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="ffd1c-207">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:host-and-deploy/azure-apps/troubleshoot>
