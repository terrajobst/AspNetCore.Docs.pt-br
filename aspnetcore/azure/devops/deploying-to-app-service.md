---
title: Implantar um aplicativo no serviço de aplicativo - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Implante um aplicativo ASP.NET Core no serviço de aplicativo do Azure, a primeira etapa para operações de desenvolvimento com ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: e09d03f1d30f128b1db1588aa92b28ec3e4ae626
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264377"
---
# <a name="deploy-an-app-to-app-service"></a>Implantar um aplicativo de serviço de aplicativo

[O serviço de aplicativo do Azure](/azure/app-service/) é plataforma de hospedagem de web do Azure. Implantar um aplicativo web no serviço de aplicativo do Azure pode ser feito manualmente ou por um processo automatizado. Esta seção do guia discute métodos de implantação que podem ser disparados manualmente ou por script usando a linha de comando ou disparada manualmente usando o Visual Studio.

Nesta seção, você vai realizar as seguintes tarefas:

* Baixe e compile o aplicativo de exemplo.
* Crie um aplicativo serviço de aplicativo Web usando o Azure Cloud Shell.
* Implante o aplicativo de exemplo no Azure usando Git.
* Implante uma alteração no aplicativo usando o Visual Studio.
* Adicione um slot de preparo para o aplicativo web.
* Implante uma atualização para o slot de preparo.
* Troca os slots de preparo e produção.

## <a name="download-and-test-the-app"></a>Baixar e testar o aplicativo

O aplicativo usado neste guia é um aplicativo ASP.NET Core pré-criados [simples de leitor de Feed](https://github.com/Azure-Samples/simple-feed-reader/). É um aplicativo páginas Razor que usa o `Microsoft.SyndicationFeed.ReaderWriter` API para recuperar um feed RSS/Atom e exibir os itens de notícias em uma lista.

Fique à vontade examinar o código, mas é importante entender que não há nada de especial sobre este aplicativo. Ele é apenas um aplicativo ASP.NET Core simple para fins ilustrativos.

Em um shell de comando, baixe o código, compile o projeto e executá-lo da seguinte maneira.

> *Observação: Os usuários do Linux/macOS devem fazer as alterações apropriadas para caminhos, por exemplo, usando a barra invertida (`/`) em vez de barra invertida (`\`).*

1. Clone o código para uma pasta no seu computador local.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Alterar pasta de trabalho para o *leitor de feeds simples* pasta que foi criada.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Restaure os pacotes e compile a solução.

    ```console
    dotnet build
    ```

4. Execute o aplicativo.

    ```console
    dotnet run
    ```

    ![O comando dotnet run for bem-sucedida](./media/deploying-to-app-service/dotnet-run.png)

5. Abra um navegador e navegue até `http://localhost:5000`. O aplicativo permite que você digite ou cole um URL de feed de distribuição e exibir uma lista de itens de notícias.

     ![O aplicativo exibindo o conteúdo de um RSS feed](./media/deploying-to-app-service/app-in-browser.png)

6. Quando estiver satisfeito o aplicativo está funcionando corretamente, desligá-lo pressionando **Ctrl**+**C** no shell de comando.

## <a name="create-the-azure-app-service-web-app"></a>Criar o aplicativo Web do serviço de aplicativo do Azure

Para implantar o aplicativo, você precisará criar um serviço de aplicativo [aplicativo Web](/azure/app-service/app-service-web-overview). Após a criação do aplicativo Web, você implantará a ele em seu computador local usando o Git.

1. Entrar para o [Azure Cloud Shell](https://shell.azure.com/bash). Observação: Quando você entra pela primeira vez, o Cloud Shell solicita a criar uma conta de armazenamento para arquivos de configuração. Aceite os padrões ou forneça um nome exclusivo.

2. Use o Cloud Shell para as etapas a seguir.

    a. Declare uma variável para armazenar o nome do seu aplicativo web. O nome deve ser exclusivo a ser usado na URL padrão. Usando o `$RANDOM` função Bash para construir o nome garante a exclusividade e resulta no formato `webappname99999`.

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Crie um grupo de recursos. Grupos de recursos fornecem um meio para agregar os recursos do Azure para ser gerenciado como um grupo.

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    O `az` comando invoca o [CLI do Azure](/cli/azure/). A CLI pode ser executada localmente, mas usá-lo no Cloud Shell economiza tempo e configuração.

    c. Crie um plano de serviço de aplicativo na camada S1. Um plano do serviço de aplicativo é um agrupamento de aplicativos web que compartilham o mesmo tipo de preço. A camada S1 não é gratuita, mas ela é necessária para o recurso de slots de preparo.

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Crie recurso de aplicativo web usando o plano de serviço de aplicativo no mesmo grupo de recursos.

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Defina as credenciais de implantação. Essas credenciais de implantação se aplicam a todos os aplicativos web em sua assinatura. Não use caracteres especiais no nome do usuário.

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Configurar o aplicativo web para aceitar as implantações do Git local e a exibição de *URL de implantação do Git*. **Anote essa URL para referência posterior**.

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Exibição de *URL do aplicativo web*. Navegue até essa URL para ver o aplicativo web em branco. **Anote essa URL para referência posterior**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Usando um shell de comando no computador local, navegue até a pasta do projeto do aplicativo web (por exemplo, `.\simple-feed-reader\SimpleFeedReader`). Execute os seguintes comandos para configurar o Git para enviar por push para a URL de implantação:

    a. Adicione a URL remota no repositório local.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Enviar por push o local *mestre* ramificar para o *azure prod* do remote *mestre* branch.

    ```console
    git push azure-prod master
    ```

    Você será solicitado para as credenciais de implantação que você criou anteriormente. Observe a saída no shell de comando. Azure cria o aplicativo ASP.NET Core remotamente.

4. Em um navegador, navegue até a *URL do aplicativo Web* e observe o aplicativo foi compilado e implantado. Alterações adicionais podem ser confirmadas no repositório Git local com `git commit`. Essas alterações são enviadas por push para o Azure com o anterior `git push` comando.

## <a name="deployment-with-visual-studio"></a>Implantação com o Visual Studio

> *Observação: Esta seção se aplica somente ao Windows. Os usuários do Linux e macOS devem fazer a alteração descrita na etapa 2 abaixo. Salve o arquivo e confirmar a alteração no repositório local com `git commit`. Por fim, envie por push a alteração com `git push`, como na primeira seção.*

O aplicativo já foi implantado no shell de comando. Vamos usar ferramentas integradas do Visual Studio para implantar uma atualização para o aplicativo. Nos bastidores, o Visual Studio realiza a mesma coisa, como a ferramentas de linha de comando, mas dentro da interface de usuário familiar do Visual Studio.

1. Abra *SimpleFeedReader.sln* no Visual Studio.
2. No Gerenciador de soluções, abra *Pages\Index.cshtml*. Alteração `<h2>Simple Feed Reader</h2>` para `<h2>Simple Feed Reader - V2</h2>`.
3. Pressione **Ctrl**+**Shift**+**B** para compilar o aplicativo.
4. No Gerenciador de soluções, clique com botão direito no projeto e clique em **publicar**.

    ![Captura de tela mostrando o botão direito do mouse, publicar](./media/deploying-to-app-service/publish.png)
5. Visual Studio pode criar um novo recurso do serviço de aplicativo, mas essa atualização será publicada a implantação existente. No **escolher um destino de publicação** caixa de diálogo, selecione **serviço de aplicativo** na lista à esquerda e, em seguida, selecione **selecionar existente**. Clique em **Publicar**.
6. No **serviço de aplicativo** caixa de diálogo, confirme que a Microsoft ou conta organizacional usada para criar sua assinatura do Azure é exibida no canto superior direito. Se não estiver, clique na lista suspensa e adicioná-lo.
7. Confirme se o Azure correto **assinatura** está selecionado. Para **modo de exibição**, selecione **grupo de recursos**. Expanda o **AzureTutorial** grupo de recursos e, em seguida, selecione o aplicativo web existente. Clique em **OK**.

    ![Caixa de diálogo de publicar o serviço de aplicativo de captura de tela mostrando](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio compila e implanta o aplicativo do Azure. Navegue até a URL do aplicativo web. Validar que o `<h2>` modificação do elemento está ativo.

![O aplicativo com o título alterado](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Slots de implantação

Slots de implantação oferecer suporte a preparação de alterações sem afetar o aplicativo em execução na produção. Depois que a versão de preparada do aplicativo é validada por uma equipe de garantia de qualidade, slots de preparo e produção podem ser trocados. O aplicativo no ambiente de preparo é promovido para produção dessa maneira. As etapas a seguir criar um slot de preparo, implantar algumas alterações nele e trocar o slot de preparo em produção após a verificação.

1. Entrar para o [Azure Cloud Shell](https://shell.azure.com/bash), se não estiver conectado.
2. Crie o slot de preparo.

    a. Criar um slot de implantação com o nome *preparo*.

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Configurar o slot de preparo para usar a implantação do Git local e obter o **preparo** URL de implantação. **Anote essa URL para referência posterior**.

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Exiba a URL do slot de preparo. Navegue até a URL para ver o slot de preparo vazio. **Anote essa URL para referência posterior**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. Em um editor de texto ou o Visual Studio, modifique *Pages* novamente para que o `<h2>` elemento lê `<h2>Simple Feed Reader - V3</h2>` e salve o arquivo.

4. Confirmar o arquivo para o repositório Git local, usando o **alterações** página no Visual Studio *Team Explorer* guia, ou digitando o seguinte usando o shell de comando do computador local:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Usando o shell de comando do computador local, adicione a URL de implantação de preparo como um Git remoto e enviar por push as alterações confirmadas:

    a. Adicione a URL remota para a preparação para o repositório Git local.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Enviar por push o local *mestre* ramificar para o *azure preparo* do remote *mestre* branch.

    ```console
    git push azure-staging master
    ```

    Aguarde enquanto o Azure cria e implanta o aplicativo.

6. Para verificar se V3 foi implantado para o slot de preparo, abra duas janelas de navegador. Em uma janela, navegue até a URL do aplicativo web original. Em outra janela, navegue até a URL do aplicativo web preparo. A URL de produção serve V2 do aplicativo. A URL de preparo serve V3 do aplicativo.

    ![Captura de tela de comparar as janelas do navegador](./media/deploying-to-app-service/ready-to-swap.png)

7. No Cloud Shell, troca o slot de preparo verificado/começando-up para produção.

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Verifique se que a troca ocorreu ao atualizar as janelas do navegador de dois.

    ![Comparando as janelas do navegador após a troca](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Resumo

Nesta seção, as tarefas a seguir foram concluídas:

* Baixou e compilou o aplicativo de exemplo.
* Criado um aplicativo serviço de aplicativo Web usando o Azure Cloud Shell.
* Implantou o aplicativo de exemplo para o Azure usando Git.
* Implantada uma alteração para o aplicativo usando o Visual Studio.
* Adicionado um slot de preparo para o aplicativo web.
* Implantasse uma atualização para o slot de preparo.
* Trocado os slots de preparo e produção.

Na próxima seção, você aprenderá a criar um pipeline de DevOps com Pipelines do Azure.

## <a name="additional-reading"></a>Leitura adicional

* [Visão geral de aplicativos Web](/azure/app-service/app-service-web-overview)
* [Criar um aplicativo web .NET Core e o banco de dados SQL no serviço de aplicativo do Azure](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Configurar as credenciais de implantação do serviço de aplicativo do Azure](/azure/app-service/app-service-deployment-credentials)
* [Configurar ambientes de preparo no serviço de aplicativo do Azure](/azure/app-service/web-sites-staged-publishing)
