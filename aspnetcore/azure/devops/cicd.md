---
title: Integração contínua e implantação - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Integração contínua e implantação em DevOps com o ASP.NET Core e o Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655830"
---
# <a name="continuous-integration-and-deployment"></a>Integração e implantação contínuas

No capítulo anterior, você criou um repositório Git local para o aplicativo de leitor de Feed simples. Neste capítulo, você irá publicar esse código em um repositório do GitHub e construir um pipeline de serviços de DevOps do Azure usando os Pipelines do Azure. O pipeline permite compilações contínuas e implantações do aplicativo. Qualquer confirmação para o repositório GitHub dispara uma compilação e uma implantação em slot de preparo do aplicativo Web do Azure.

Nesta seção, você concluirá as seguintes tarefas:

* Publicar o código do aplicativo no GitHub
* Desconectar-se a implantação do Git local
* Criar uma organização de DevOps do Azure
* Criar um projeto de equipe nos serviços de DevOps do Azure
* Criar a definição de build
* Criar um pipeline de lançamento
* Confirmar alterações no GitHub e implantar automaticamente no Azure
* Examinar o pipeline de Pipelines do Azure

## <a name="publish-the-apps-code-to-github"></a>Publicar o código do aplicativo no GitHub

1. Abra uma janela do navegador e navegue até `https://github.com`.
1. Clique na lista suspensa **+** no cabeçalho e selecione **novo repositório**:

    ![Opção novo repositório do GitHub](media/cicd/github-new-repo.png)

1. Selecione sua conta na lista suspensa **proprietário** e insira *leitor de feed simples* na caixa de texto nome do **repositório** .
1. Clique no botão **criar repositório** .
1. Abra o shell de comando do seu computador local. Navegue até o diretório no qual o repositório Git do *leitor de feeds simples* está armazenado.
1. Renomeie o remoto de *origem* existente para *upstream*. Execute o comando a seguir:

    ```console
    git remote rename origin upstream
    ```

1. Adicione um novo ponto remoto de *origem* apontando para sua cópia do repositório no github. Execute o comando a seguir:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Publica seu repositório Git local para o repositório GitHub recém-criado. Execute o comando a seguir:

    ```console
    git push -u origin master
    ```

1. Abra uma janela do navegador e navegue até `https://github.com/<GitHub_username>/simple-feed-reader/`. Valide que seu código aparece no repositório do GitHub.

## <a name="disconnect-local-git-deployment"></a>Desconectar-se a implantação do Git local

Remova a implantação Git local com as etapas a seguir. Pipelines do Azure (um serviço de DevOps do Azure) substitui e amplia essa funcionalidade.

1. Abra o [portal do Azure](https://portal.azure.com/)e navegue até o aplicativo Web de *preparo (mywebapp\<unique_number\>/staging)* . O aplicativo Web pode ser localizado rapidamente inserindo o *preparo* na caixa de pesquisa do portal:

    ![termo de pesquisa do aplicativo Web de preparo](media/cicd/portal-search-box.png)

1. Clique em **central de implantação**. Um novo painel é exibido. Clique em **Desconectar** para remover a configuração de controle do código-fonte git local que foi adicionada no capítulo anterior. Confirme a operação de remoção clicando no botão **Sim** .
1. Navegue até o *< myWebApp unique_number >* serviço de aplicativo. Como lembrete, caixa de pesquisa do portal pode ser usada para localizar rapidamente o serviço de aplicativo.
1. Clique em **central de implantação**. Um novo painel é exibido. Clique em **Desconectar** para remover a configuração de controle do código-fonte git local que foi adicionada no capítulo anterior. Confirme a operação de remoção clicando no botão **Sim** .

## <a name="create-an-azure-devops-organization"></a>Criar uma organização de DevOps do Azure

1. Abra um navegador e navegue até a [página de criação da organização do Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Digite um nome exclusivo na caixa de texto **selecionar um nome memorizado** para formar a URL para acessar sua organização DevOps do Azure.
1. Selecione o botão de opção **git** , pois o código está hospedado em um repositório github.
1. Clique no botão **Continuar**. Após uma breve espera, uma conta e um projeto de equipe, chamados *MyFirstProject*, são criados.

    ![Página de criação de organização de DevOps do Azure](media/cicd/vsts-account-creation.png)

1. Abra o email de confirmação indicando que a organização de DevOps do Azure e o projeto estão prontos para uso. Clique no botão **iniciar o projeto** :

    ![Botão seu projeto inicial](media/cicd/vsts-start-project.png)

1. Um navegador é aberto para *\<account_name\>. VisualStudio.com*. Clique no link *MyFirstProject* para começar a configurar o pipeline DevOps do projeto.

## <a name="configure-the-azure-pipelines-pipeline"></a>Configurar o pipeline de Pipelines do Azure

Há três etapas distintas para ser concluída. As etapas nos resultados em um pipeline de DevOps operacional três seções a seguir.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>DevOps do Azure conceda acesso ao repositório do GitHub

1. Expanda o **ou crie o código de um acordeão do repositório externo** . Clique no botão **Configurar compilação** :

    ![Botão de compilação da configuração](media/cicd/vsts-setup-build.png)

1. Selecione a opção **GitHub** na seção **selecionar uma fonte** :

    ![Selecione uma fonte - GitHub](media/cicd/vsts-select-source.png)

1. A autorização é necessária antes de DevOps do Azure pode acessar seu repositório do GitHub. Insira *< GitHub_username > conexão do GitHub* na caixa de texto **nome da conexão** . Por exemplo:

    ![Nome da conexão do GitHub](media/cicd/vsts-repo-authz.png)

1. Se a autenticação de dois fatores é ativada em sua conta do GitHub, um token de acesso pessoal é necessário. Nesse caso, clique no link **autorizar com um token de acesso pessoal do GitHub** . Consulte as [instruções de criação do token de acesso pessoal do GitHub oficial](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) para obter ajuda. Somente o escopo de permissões do *repositório* é necessário. Caso contrário, clique no botão **autorizar usando OAuth** .
1. Quando solicitado, entre sua conta do GitHub. Em seguida, selecione a autorização para conceder acesso a sua organização de DevOps do Azure. Se for bem-sucedido, um novo ponto de extremidade de serviço é criado.
1. Clique no botão de reticências ao lado do botão **repositório** . Selecione o *< GitHub_username > repositório/Simple-Feed-Reader* na lista. Clique no botão **Selecionar**.
1. Selecione a ramificação *mestre* do **Branch padrão para a lista suspensa de compilações manuais e agendadas** . Clique no botão **Continuar**. Página seleção de modelo é exibido.

### <a name="create-the-build-definition"></a>Criar a definição de compilação

1. Na página seleção de modelo, insira *ASP.NET Core* na caixa de pesquisa:

    ![Na página do modelo de pesquisa de ASP.NET Core](media/cicd/vsts-template-selection.png)

1. Os resultados da pesquisa de modelo são exibidos. Passe o mouse sobre o modelo de **ASP.NET Core** e clique no botão **aplicar** .
1. A guia **tarefas** da definição de compilação é exibida. Clique na guia **Gatilhos** .
1. Marque a caixa **habilitar integração contínua** . Na seção **filtros de ramificação** , confirme se a lista suspensa **tipo** está definida como *incluir*. Defina a lista suspensa de **especificações da ramificação** como *mestre*.

    ![Habilitar as configurações de integração contínua](media/cicd/vsts-enable-ci.png)

    Essas configurações fazem com que uma compilação seja disparada quando qualquer alteração é enviada para o Branch *mestre* do repositório do github. A integração contínua é testada na seção [confirmar alterações no GitHub e implantar automaticamente no Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) .

1. Clique no botão **salvar & fila** e selecione a opção **salvar** :

    ![Botão Salvar](media/cicd/vsts-save-build.png)

1. A seguinte caixa de diálogo modal será exibida:

    ![Salvar a definição de compilação - caixa de diálogo modal](media/cicd/vsts-save-modal.png)

    Use a pasta padrão de *\\* e clique no botão **salvar** .

### <a name="create-the-release-pipeline"></a>Criar o pipeline de lançamento

1. Clique na guia **versões** do seu projeto de equipe. Clique no botão **novo pipeline** .

    ![Guia versões - botão novo de definição](media/cicd/vsts-new-release-definition.png)

    O painel de seleção de modelo é exibido.

1. Na página seleção de modelo, insira *serviço de aplicativo* na caixa de pesquisa:

    ![Caixa de pesquisa de modelo de pipeline de lançamento](media/cicd/vsts-release-template-search.png)

1. Os resultados da pesquisa de modelo são exibidos. Passe o mouse sobre a **implantação do serviço de Azure app com** o modelo de slot e clique no botão **aplicar** . A guia **pipeline** do pipeline de liberação é exibida.

    ![Guia Pipeline do pipeline de lançamento](media/cicd/vsts-release-definition-pipeline.png)

1. Clique no botão **Adicionar** na caixa **artefatos** . O painel **Adicionar artefato** é exibido:

    ![Pipeline de lançamento - Adicionar painel de artefato](media/cicd/vsts-release-add-artifact.png)

1. Selecione o bloco **Build** na seção **tipo de origem** . Esse tipo permite a vinculação do pipeline de lançamento para a definição de compilação.
1. Selecione *MyFirstProject* na lista suspensa **projeto** .
1. Selecione o nome de definição de compilação, *MyFirstProject-ASP.NET Core-CI*, na lista suspensa **origem (definição de compilação)** .
1. Selecione *mais recente* na lista suspensa **versão padrão** . Essa opção cria os artefatos produzidos pela execução mais recente da definição de compilação.
1. Substitua o texto na caixa de texto **alias de origem** por *drop*.
1. Clique no botão **Adicionar**. A seção **artefatos** é atualizada para exibir as alterações.
1. Clique no ícone de raio para habilitar implantações contínuas:

    ![Pipeline de lançamento artefatos - ícone de raio](media/cicd/vsts-artifacts-lightning-bolt.png)

    Com essa opção habilitada, uma implantação ocorre sempre que uma nova compilação está disponível.
1. Um painel de **gatilho de implantação contínua** aparece à direita. Clique no botão de alternância para habilitar o recurso. Não é necessário habilitar o **gatilho de solicitação pull**.
1. Clique no menu suspenso **Adicionar** na seção **filtros de Branch de compilação** . Escolha a opção de **ramificação padrão da definição de compilação** . Esse filtro faz com que o lançamento seja disparado apenas para uma compilação do Branch *mestre* do repositório do github.
1. Clique no botão **Salvar**. Clique no botão **OK** na caixa de diálogo **salvar** modal resultante.
1. Clique na caixa **ambiente 1** . Um painel de **ambiente** aparece à direita. Altere o texto do *ambiente 1* na caixa de texto **nome do ambiente** para *produção*.

   ![Pipeline de lançamento - caixa de texto de nome de ambiente](media/cicd/vsts-environment-name-textbox.png)

1. Clique no link **1 fase, 2 tarefas** na caixa **produção** :

    ![Pipeline de lançamento - link.png do ambiente de produção](media/cicd/vsts-production-link.png)

    A guia **tarefas** do ambiente é exibida.
1. Clique na tarefa **implantar o serviço de Azure app para o slot** . Suas configurações aparecem em um painel à direita.
1. Selecione a assinatura do Azure associada ao serviço de aplicativo na lista suspensa **assinatura do Azure** . Depois de selecionado, clique no botão **autorizar** .
1. Selecione *aplicativo Web* na lista suspensa **tipo de aplicativo** .
1. Selecione *myWebApp/< unique_number/>* na lista suspensa **nome do serviço de aplicativo** .
1. Selecione *AzureTutorial* na lista suspensa **grupo de recursos** .
1. Selecione *preparo* na lista suspensa **slot** .
1. Clique no botão **Salvar**.
1. Passe o mouse sobre o nome do pipeline de versão padrão. Clique no ícone de lápis para editá-lo. Use *MyFirstProject-ASP.NET Core-CD* como o nome.

    ![Nome do pipeline de lançamento](media/cicd/vsts-release-definition-name.png)

1. Clique no botão **Salvar**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Confirmar alterações no GitHub e implantar automaticamente no Azure

1. Abra *SimpleFeedReader. sln* no Visual Studio.
1. Em Gerenciador de Soluções, abra *Pages\Index.cshtml*. Alterar `<h2>Simple Feed Reader - V3</h2>` para `<h2>Simple Feed Reader - V4</h2>`.
1. Pressione **Ctrl**+**Shift**+**B** para compilar o aplicativo.
1. Confirme o arquivo para o repositório GitHub. Use a página **alterações** na guia *Team Explorer* do Visual Studio ou execute o seguinte usando o Shell de comando do computador local:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Envie por push a alteração no Branch *mestre* para o remoto de *origem* do seu repositório github:

    ```console
    git push origin master
    ```

    A confirmação aparece no Branch *mestre* do repositório do github:

    ![Confirmação do GitHub no branch mestre](media/cicd/github-commit.png)

    A compilação é disparada, pois a integração contínua está habilitada na guia **gatilhos** da definição de compilação:

    ![Habilitar a integração contínua](media/cicd/enable-ci.png)

1. Navegue até a guia **enfileirada** da página **builds** do **Azure pipelines** > no Azure DevOps Services. Compilação enfileirada mostra a ramificação e confirme que disparou a compilação:

    ![compilação enfileirada](media/cicd/build-queued.png)

1. A compilação for bem-sucedida, ocorre uma implantação do Azure. Navegue até o aplicativo no navegador. Observe que o texto "V4" é exibido no título:

    ![aplicativo atualizado](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Examinar o pipeline de Pipelines do Azure

### <a name="build-definition"></a>Definição da compilação

Uma definição de compilação foi criada com o nome *MyFirstProject-ASP.NET Core-CI*. Após a conclusão, a compilação produz um arquivo *. zip* , incluindo os ativos a serem publicados. O pipeline de lançamento implanta esses ativos do Azure.

A guia **tarefas** da definição de compilação lista as etapas individuais que estão sendo usadas. Há cinco tarefas de compilação.

![tarefas de definição de compilação](media/cicd/build-definition-tasks.png)

1. **Restore** &mdash; executa o comando `dotnet restore` para restaurar os pacotes NuGet do aplicativo. O pacote padrão feed usado é nuget.org.
1. O **Build** &mdash; executa o comando `dotnet build --configuration release` para compilar o código do aplicativo. Essa opção `--configuration` é usada para produzir uma versão otimizada do código, que é adequada para implantação em um ambiente de produção. Modifique a variável *BuildConfiguration* na guia **variáveis** da definição de compilação se, por exemplo, uma configuração de depuração for necessária.
1. O &mdash; de **teste** executa o comando `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` para executar os testes de unidade do aplicativo. Os testes de unidade são executados C# dentro de qualquer projeto correspondente ao padrão de glob de `**/*Tests/*.csproj`. Os resultados de teste são salvos em um arquivo *. trx* no local especificado pela opção `--results-directory`. Se qualquer teste falhar, a compilação falhará e não será implantada.

    > [!NOTE]
    > Para verificar se os testes de unidade funcionam, modifique *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* para quebrar propositadamente um dos testes. Por exemplo, altere `Assert.True(result.Count > 0);` para `Assert.False(result.Count > 0);` no método `Returns_News_Stories_Given_Valid_Uri`. Confirme e envie a alteração ao GitHub. A compilação é disparada e falha. O status do pipeline de compilação muda para **com falha**. Reverta a alteração, a confirmação e o envio por push novamente. A compilação for bem-sucedida.

1. O &mdash; de **publicação** executa o comando `dotnet publish --configuration release --output <local_path_on_build_agent>` para produzir um arquivo *. zip* com os artefatos a serem implantados. A opção `--output` especifica o local de publicação do arquivo *. zip* . Esse local é especificado passando uma [variável predefinida](/azure/devops/pipelines/build/variables) chamada `$(build.artifactstagingdirectory)`. Essa variável expande para um caminho local, como *c:\agent\_work\1\a*, no agente de compilação.
1. **Publicar artefato** &mdash; publica o arquivo *. zip* produzido pela tarefa de **publicação** . A tarefa aceita o local do arquivo *. zip* como um parâmetro, que é a variável predefinida `$(build.artifactstagingdirectory)`. O arquivo *. zip* é publicado como uma pasta chamada *drop*.

Clique no link **Resumo** da definição de compilação para exibir um histórico de compilações com a definição:

![Histórico de definição de compilação do captura de tela mostrando](media/cicd/build-definition-summary.png)

Na página resultante, clique no link correspondente ao número de build exclusivo:

![Página resumida de definição de compilação captura de tela mostrando](media/cicd/build-definition-completed.png)

Um resumo dessa compilação específica é exibido. Clique na guia **artefatos** e observe que a pasta de *destino* produzida pela compilação está listada:

![Captura de tela mostrando os artefatos de definição de compilação - pasta-depósito](media/cicd/build-definition-artifacts.png)

Use **baixar** e **explorar** links para inspecionar os artefatos publicados.

### <a name="release-pipeline"></a>Pipeline de lançamento

Um pipeline de versão foi criado com o nome *MyFirstProject-ASP.NET Core-CD*:

![Visão geral da captura de tela mostrando release pipeline](media/cicd/release-definition-overview.png)

Os dois principais componentes do pipeline de lançamento são os **artefatos** e os **ambientes**. Clicar na caixa na seção **artefatos** revela o seguinte painel:

![Captura de tela mostrando artefatos de pipeline da versão](media/cicd/release-definition-artifacts.png)

O valor de **origem (definição de compilação)** representa a definição de compilação à qual esse pipeline de liberação está vinculado. O arquivo *. zip* produzido por uma execução bem-sucedida da definição de compilação é fornecido ao ambiente de *produção* para implantação no Azure. Clique no link da *fase 1, 2 tarefas* na caixa ambiente de *produção* para exibir as tarefas do pipeline de liberação:

![Captura de tela mostrando tarefas de pipeline de lançamento](media/cicd/release-definition-tasks.png)

O pipeline de lançamento consiste em duas tarefas: *implantar Azure app serviço para o slot* e *gerenciar Azure app troca de slot de serviço*. Clicar a primeira tarefa revela a configuração de tarefa a seguir:

![Tarefa de implantação do pipeline de lançamento captura de tela mostrando](media/cicd/release-definition-task1.png)

A assinatura do Azure, o tipo de serviço, o nome do aplicativo web, o grupo de recursos e o slot de implantação são definidos na tarefa de implantação. A caixa de texto **pacote ou pasta** contém o caminho do arquivo *. zip* a ser extraído e implantado no slot de *preparo* do *myWebApp\<unique_number\>* aplicativo Web.

Clicar a tarefa de troca de slot revela a configuração de tarefa a seguir:

![Tarefa de troca de slot captura de tela mostrando release pipeline](media/cicd/release-definition-task2.png)

A assinatura, grupo de recursos, tipo de serviço, nome do aplicativo web e detalhes do slot de implantação são fornecidas. A caixa de seleção **alternar com produção** está marcada. Consequentemente, os bits implantados no slot de *preparo* são trocados no ambiente de produção.

## <a name="additional-reading"></a>Leitura adicional

* [Criar seu primeiro pipeline com o Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Compilar e projeto do .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Implantar um aplicativo Web com Azure Pipelines](/azure/devops/pipelines/targets/webapp)
