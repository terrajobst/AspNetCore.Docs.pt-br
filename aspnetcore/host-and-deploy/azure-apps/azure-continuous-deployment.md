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
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890091"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>Implantação contínua no Azure com o Visual Studio e o GIT com o ASP.NET Core

Por [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Este tutorial mostra como criar um aplicativo Web ASP.NET Core usando o Visual Studio e implantá-lo por meio do Visual Studio no Serviço de Aplicativo do Azure usando a implantação contínua.

Consulte também [Criar seu primeiro pipeline com o Azure Pipelines](/azure/devops/pipelines/get-started-yaml), que mostra como configurar um fluxo de trabalho de CD (entrega contínua) para o [Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-overview) usando o Azure DevOps Services. O Azure Pipelines (um serviço do Azure DevOps Services) simplifica a configuração de um pipeline de implantação robusta para publicar atualizações para aplicativos hospedados no Serviço de Aplicativo do Azure. O pipeline pode ser configurado no portal do Azure para criar, executar testes, implantar em um slot de preparo e, em seguida, implantar na produção.

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Para obter uma conta, [ative os benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) ou [inscreva-se em uma avaliação gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prerequisites

Este tutorial pressupõe que o seguinte software está instalado:

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Git](https://git-scm.com/downloads) para Windows

## <a name="create-an-aspnet-core-web-app"></a>Criar um aplicativo Web ASP.NET Core

1. Inicie o Visual Studio.

1. No menu **Arquivo**, selecione **Novo** > **Projeto**.

1. Selecione o modelo de projeto **Aplicativo Web ASP.NET Core**. Ele será exibido em **Instalado** > **Modelos** > **Visual C#**  >  **.NET Core**. Nomeie o projeto `SampleWebAppDemo`. Selecione a opção **Criar novo repositório GIT** e clique em **OK**.

   ![Caixa de diálogo Novo Projeto](azure-continuous-deployment/_static/01-new-project.png)

1. Na caixa de diálogo **Novo Projeto ASP.NET Core**, selecione o modelo **Vazio** do ASP.NET Core e clique em **OK**.

   ![Caixa de diálogo Novo projeto ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> A versão mais recente do .NET Core é a 2.0.

### <a name="running-the-web-app-locally"></a>Executando o aplicativo Web localmente

1. Depois que o Visual Studio concluir a criação do aplicativo, execute o aplicativo selecionando **Depurar** > **Iniciar Depuração**. Como alternativa, pressione **F5**.

   Talvez seja necessário alguns instantes para inicializar o Visual Studio e o novo aplicativo. Quando isso for concluído, o navegador mostrará o aplicativo em execução.

   ![Janela do navegador mostrando o aplicativo em execução que exibe “Olá, Mundo!”](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Depois de examinar o aplicativo Web em execução, feche o navegador e selecione o ícone “Parar Depuração” na barra de ferramentas do Visual Studio para interromper o aplicativo.

## <a name="create-a-web-app-in-the-azure-portal"></a>Criar um aplicativo Web no Portal do Azure

As etapas a seguir criam um aplicativo Web no portal do Azure:

1. Faça logon no [portal do Azure](https://portal.azure.com).

1. Selecione **NOVO** na parte superior esquerda da interface do portal.

1. Selecione **Web + Celular** > **Aplicativo Web**.

   ![Portal do Microsoft Azure: Botão Novo: Web + Celular em Marketplace: Botão Aplicativo Web em Aplicativos em Destaque](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. Na folha **Aplicativo Web**, insira um valor exclusivo para o **Nome do Serviço de Aplicativo**.

   ![Folha Aplicativo Web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > O **Nome do Serviço de Aplicativo** precisa ser exclusivo. O portal impõe essa regra quando o nome é fornecido. Se você fornecer outro valor, você precisará substituí-lo para cada ocorrência do **SampleWebAppDemo** neste tutorial.

   Também na folha **Aplicativo Web**, selecione um **Plano do Serviço de Aplicativo/Localização** existente ou crie um novo. Se você criar um novo plano, selecione o tipo de preço, o local e outras opções. Para obter mais informações sobre os planos do Serviço de Aplicativo, veja [Visão geral detalhada dos planos do Serviço de Aplicativo do Azure](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Selecione **Criar**. O Azure provisionará e iniciará o aplicativo Web.

   ![Portal do Azure: folha Informações Gerais da Demonstração de Aplicativo Web de Exemplo 01](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Habilitar a publicação do Git no novo aplicativo Web

O GIT é um sistema de controle de versão distribuída que pode ser usado para implantar um aplicativo Web do Serviço de Aplicativo do Azure. O código do aplicativo Web é armazenado em um repositório GIT local e implantado no Azure por push para um repositório remoto.

1. Faça logon no [portal do Azure](https://portal.azure.com).

1. Selecione **Serviços de Aplicativos** para exibir uma lista de serviços de aplicativos associados à assinatura do Azure.

1. Selecione o aplicativo Web criado na seção anterior deste tutorial.

1. Na folha **Implantação**, selecione **Opções de implantação** > **Escolher Origem** > **Repositório Git Local**.

   ![Folha Configurações: Folha Origem da implantação: Escolha a folha de origem](azure-continuous-deployment/_static/deployment-options.png)

1. Selecione **OK**.

1. Caso você não tenha configurado anteriormente as credenciais de implantação para publicar um aplicativo Web ou outro aplicativo do Serviço de Aplicativo, configure-as agora:

   * Selecione **Configurações** > **Credenciais de implantação**. A folha **Definir credenciais de implantação** é exibida.
   * Crie um nome de usuário e uma senha. Salve a senha para uso posterior ao configurar o GIT.
   * Selecione **Salvar**.

1. Na folha **Aplicativo Web**, selecione **Configurações** > **Propriedades**. A URL do repositório GIT remoto no qual você implantará será mostrada em **URL do GIT**.

1. Copie o valor **URL do GIT** para uso posterior no tutorial.

   ![Portal do Azure: folha Propriedades do aplicativo](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publicar o aplicativo Web no Serviço de Aplicativo do Azure

Nesta seção, você criará um repositório GIT local usando o Visual Studio e efetuará push desse repositório para o Azure para implantar o aplicativo Web. As etapas envolvidas incluem as seguintes:

* Adicione a configuração do repositório remoto usando o valor de URL do GIT, de modo que você possa implantar seu repositório local no Azure.
* Confirme as alterações do projeto.
* Envie as alterações do projeto por push do repositório local para o repositório remoto no Azure.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **“SampleWebAppDemo” da Solução** e selecione **Confirmar**. O **Team Explorer** é exibido.

   ![Guia Conectar do Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

1. No **Team Explorer**, selecione a **Página Inicial** (ícone da página inicial) > **Configurações** > **Configurações do Repositório**.

1. Na seção **Remotos** das **Configurações do Repositório**, selecione **Adicionar**. A caixa de diálogo **Adicionar Remoto** é exibida.

1. Defina o **Nome** do remoto como **Azure-SampleApp**.

1. Defina o valor de **Buscar** como a **URL do GIT** copiada do Azure anteriormente neste tutorial. Observe que essa é a URL que termina com **.git**.

   ![Caixa de diálogo Editar Remoto](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Como alternativa, especifique o repositório remoto na **Janela Comando** abrindo a **Janela Comando**, alterando para o diretório do projeto e inserindo o comando. Exemplo:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Selecione a **Página Inicial** (ícone da página inicial) > **Configurações** > **Configurações Globais**. Confirme se o nome e o endereço de email estão definidos. Se necessário, selecione **Atualizar**.

1. Selecione **Página Inicial** > **Alterações** para retornar à exibição **Alterações**.

1. Escreva uma mensagem de confirmação, como **Push Inicial nº 1** e selecione **Confirmar**. Essa ação cria uma *confirmação* localmente.

   ![Guia Conectar do Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Como alternativa, confirme as alterações na **Janela Comando** abrindo a **Janela Comando**, alterando para o diretório do projeto e inserindo os comandos do GIT. Exemplo:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Selecione **Página Inicial** > **Sincronização** > **Ações** > **Abrir Prompt de Comando**. O prompt de comando se abre no diretório do projeto.

1. Insira o seguinte comando na janela de comando:

   `git push -u Azure-SampleApp master`

1. Insira a senha das **credenciais de implantação** do Azure criada anteriormente no Azure.

   Esse comando inicia o processo de envio por push dos arquivos de projeto locais para o Azure. A saída do comando acima termina com uma mensagem informando que a implantação foi bem-sucedida.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Se a colaboração no projeto é necessária, considere a possibilidade de enviar por push para o [GitHub](https://github.com) antes de enviar por push para o Azure.
 
### <a name="verify-the-active-deployment"></a>Verificar a implantação ativa

Verifique se a transferência do aplicativo Web do ambiente local para o Azure é bem-sucedida.

No [portal do Azure](https://portal.azure.com), selecione o aplicativo Web. Selecione **Implantação** > **Opções de implantação**.

![Portal do Azure: Folha Configurações: folha Implantações mostrando a implantação bem-sucedida](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Executar o aplicativo no Azure

Agora que o aplicativo Web é implantado no Azure, execute o aplicativo.

Isso pode ser feito de duas maneiras:

* No portal do Azure, localize a folha do aplicativo Web desse aplicativo Web. Selecione **Procurar** para exibir o aplicativo no navegador padrão.
* Abra um navegador e insira a URL para o aplicativo Web. Exemplo: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Atualize o aplicativo Web e publique-o novamente

Depois de fazer alterações ao código local, republique:

1. No **Gerenciador de Soluções** do Visual Studio, abra o arquivo *Startup.cs*.

1. No método `Configure`, modifique o método `Response.WriteAsync` para que ele seja exibido da seguinte maneira:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Salve as alterações em *Startup.cs*.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **“SampleWebAppDemo” da Solução** e selecione **Confirmar**. O **Team Explorer** é exibido.

1. Escreva uma mensagem de confirmação, tal como `Update #2`.

1. Pressione o botão **Confirmar** para confirmar as alterações do projeto.

1. Selecione **Página Inicial** > **Sincronização** > **Ações** > **Enviar por Push**.

> [!NOTE]
> Como alternativa, envie as alterações por push da **Janela Comando** abrindo a **Janela Comando**, alterando para o diretório do projeto e inserindo um comando do GIT. Exemplo:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Exibir o aplicativo Web atualizado no Azure

Exiba o aplicativo Web atualizado selecionando **Procurar** na folha do aplicativo Web no portal do Azure ou abrindo um navegador e inserindo a URL do aplicativo Web. Exemplo: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Recursos adicionais

* [Criar seu primeiro pipeline com o Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Kudu do projeto](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
