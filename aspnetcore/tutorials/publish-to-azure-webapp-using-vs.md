---
title: Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio
author: rick-anderson
description: Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693967"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio

Por [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Consulte [publicar um aplicativo Web para Azure app serviço usando Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) se você estiver trabalhando no MacOS.

Para solucionar um problema de implantação do Serviço de Aplicativo, confira <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Configurar

* Abra uma [conta do Azure gratuita](https://azure.microsoft.com/free/dotnet/) se você não tiver uma. 

## <a name="create-a-web-app"></a>Como criar um aplicativo Web

Na página inicial do Visual Studio, selecione **Arquivo > Novo > Projeto...**

![Menu Arquivo](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Complete a caixa de diálogo **Novo Projeto**:

* No painel esquerdo, selecione **.NET Core**.
* No painel central, toque em **Aplicativo Web ASP.NET Core**.
* Selecione **OK**.

![Caixa de diálogo Novo Projeto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Na caixa de diálogo **Novo Aplicativo Web ASP.NET Core**:

* Selecione **Aplicativo Web**.
* Selecione **Mudar Autenticação**.

![Caixa de diálogo Novo Projeto](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

A caixa de diálogo **Mudar Autenticação** é exibida. 

* Selecione **Contas de Usuário Individuais**.
* Selecione **OK** para retornar para o **Novo Aplicativo Web do ASP.NET Core**, em seguida, selecione **OK** novamente.

![Caixa de diálogo Nova autenticação da Web do ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

O Visual Studio cria a solução.

## <a name="run-the-app"></a>Executar o aplicativo

* Pressione CTRL+F5 para executar o projeto.
* Teste os links **Sobre** e **Contato**.

![Aplicativo Web aberto no Microsoft Edge no localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrar um usuário

* Selecione **Registrar** e registre um novo usuário. Você pode usar um endereço de email fictício. Ao enviar, a página exibirá o seguinte erro:

    *"Erro interno do servidor: uma operação de banco de dados falhou ao processar a solicitação. Exceção SQL: não é possível abrir o banco de dados. A aplicação de migrações existentes para o contexto do BD de aplicativos pode resolver esse problema. "*
* Selecione **Aplicar Migrações** e, depois que a página for atualizada, atualize a página.

![Erro interno do servidor: uma operação de banco de dados falhou ao processar a solicitação. Exceção SQL: não é possível abrir o banco de dados. A aplicação de migrações existentes ao contexto do BD do Aplicativo pode resolver esse problema.](publish-to-azure-webapp-using-vs/_static/mig.png)

O aplicativo exibe o email usado para registrar o novo usuário e um link **Fazer logout**.

![Aplicativo Web aberto no Microsoft Edge. O link Registrar é substituído pelo texto Olá, email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Implantar o aplicativo no Azure

Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Publicar...** .

![Menu contextual aberto com o link Publicar realçado](publish-to-azure-webapp-using-vs/_static/pub.png)

Na caixa de diálogo **Publicar**:

* Selecione **Serviço de Aplicativo do Microsoft Azure**.
* Selecione o ícone de engrenagem e, em seguida, **Criar Perfil**.
* Selecione **Criar Perfil**.

![Caixa de diálogo Publicar](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Criar recursos do Azure

A caixa de diálogo **Criar Serviço de Aplicativo** será exibida:

* Insira sua assinatura.
* Os campos de entrada **Nome do Aplicativo**, **Grupo de Recursos** e **Plano do Serviço de Aplicativo** serão populados. Você pode manter esses nomes ou alterá-los.

![Caixa de diálogo Serviço de Aplicativo](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Selecione a guia **Serviços** para criar um novo banco de dados.

* Selecione o ícone verde **+** para criar um novo Banco de Dados SQL

![Novo Banco de Dados SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* Selecione **Novo...** na caixa de diálogo **Configurar Banco de Dados SQL** para criar um novo banco de dados.

![Novo servidor e Banco de Dados SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

A caixa de diálogo **Configurar o SQL Server** é exibida.

* Insira um nome de usuário do administrador e a senha e, em seguida, selecione **OK**. Você pode manter o **Nome do Servidor** padrão. 

> [!NOTE]
> “admin” não é permitido como o nome de usuário administrador.

![Caixa de diálogo Configurar o SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Selecione **OK**.

O Visual Studio retorna para a caixa de diálogo **Criar Serviço de Aplicativo**.

* Selecione **Criar** na caixa de diálogo **Criar Serviço de Aplicativo**.

![Caixa de diálogo Configurar o Banco de Dados SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

O Visual Studio cria o aplicativo Web e o SQL Server no Azure. Esta etapa pode levar alguns minutos. Para obter informações sobre os recursos criados, confira [Recursos adicionais](#additional-resources).

Quando a implantação for concluída, selecione **Configurações**:

![Caixa de diálogo Configurar o SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

Na página **Configurações** da caixa de diálogo **Publicar**:

* Expanda **Bancos de Dados** e marque a opção **Usar esta cadeia de conexão no runtime**.
* Expanda **Migrações do Entity Framework** e marque a opção **Aplicar esta migração durante a publicação**.

* Selecione **Salvar**. O Visual Studio retorna para a caixa de diálogo **Publicar**. 

![Caixa de diálogo Publicar: painel Configurações](publish-to-azure-webapp-using-vs/_static/pubs.png)

Clique em **Publicar**. O Visual Studio publica seu aplicativo no Azure. Quando a implantação for concluída, o aplicativo será aberto em um navegador.

### <a name="test-your-app-in-azure"></a>Testar o aplicativo no Azure

* Teste os links **Sobre** e **Contato**

* Registrar um novo usuário

![Aplicativo Web aberto no Microsoft Edge no Serviço de Aplicativo do Azure](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Atualizar o aplicativo

* Edite a página do Razor *Pages/About.cshtml* e altere seu conteúdo. Por exemplo, você pode modificar o parágrafo para dizer "Hello ASP.NET Core!":

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Clique com o botão direito do mouse no projeto e selecione **Publicar...** novamente.

![Menu contextual aberto com o link Publicar realçado](publish-to-azure-webapp-using-vs/_static/pub.png)

* Depois que o aplicativo for publicado, verifique se as alterações feitas estão disponíveis no Azure.

![Verifique se a tarefa está concluída](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Limpar

Quando você concluir o teste do aplicativo, acesse o [portal do Azure](https://portal.azure.com/) e exclua o aplicativo.

* Selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos criado.

![Portal do Azure: Grupos de Recursos no menu da barra lateral](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Na página **Grupos de recursos**, selecione **Excluir**.

![Portal do Azure: página Grupos de Recursos](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Insira o nome do grupo de recursos e selecione **Excluir**. O aplicativo e todos os outros recursos criados neste tutorial agora foram excluídos do Azure.

### <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Recursos adicionais

* Para o Visual Studio Code, confira [Perfis de publicação](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-overview)
* [Grupo de recursos do Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Banco de Dados SQL do Azure](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
