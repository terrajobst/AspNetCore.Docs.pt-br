---
title: Integração contínua e implantação - DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Integração contínua e implantação em DevOps com o ASP.NET Core e o Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165250"
---
# <a name="continuous-integration-and-deployment"></a>Integração contínua e implantação

No capítulo anterior, você criou um repositório Git local para o aplicativo de leitor de Feed simples. Neste capítulo, você irá publicar esse código em um repositório do GitHub e construir um pipeline de serviços de DevOps do Azure usando os Pipelines do Azure. O pipeline permite compilações contínuas e implantações do aplicativo. Qualquer confirmação para o repositório GitHub dispara uma compilação e uma implantação em slot de preparo do aplicativo Web do Azure.

Nesta seção, você concluirá as seguintes tarefas:

* Publicar o código do aplicativo no GitHub
* Desconectar-se a implantação do Git local
* Criar uma organização de DevOps do Azure
* Criar um projeto de equipe nos serviços de DevOps do Azure
* Criar uma definição de compilação
* Criar um pipeline de lançamento
* Confirmar alterações no GitHub e implantar automaticamente no Azure
* Examinar o pipeline de Pipelines do Azure

## <a name="publish-the-apps-code-to-github"></a>Publicar o código do aplicativo no GitHub

1. Abra uma janela do navegador e navegue até `https://github.com`.
1. Clique o **+** lista suspensa no cabeçalho e selecione **novo repositório**:

    ![Opção novo repositório do GitHub](media/cicd/github-new-repo.png)

1. Selecione sua conta na **proprietário** lista suspensa e insira *leitor de feeds simples* no **nome do repositório** caixa de texto.
1. Clique o **criar repositório** botão.
1. Abra o shell de comando do seu computador local. Navegue até o diretório no qual o *leitor de feeds simples* repositório Git é armazenado.
1. Renomear existente *origin* remoto para *upstream*. Execute o seguinte comando:

    ```console
    git remote rename origin upstream
    ```

1. Adicione um novo *origem* remoto que aponta para a sua cópia do repositório no GitHub. Execute o seguinte comando:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Publica seu repositório Git local para o repositório GitHub recém-criado. Execute o seguinte comando:

    ```console
    git push -u origin master
    ```

1. Abra uma janela do navegador e navegue até `https://github.com/<GitHub_username>/simple-feed-reader/`. Valide que seu código aparece no repositório do GitHub.

## <a name="disconnect-local-git-deployment"></a>Desconectar-se a implantação do Git local

Remova a implantação Git local com as etapas a seguir. Pipelines do Azure (um serviço de DevOps do Azure) substitui e amplia essa funcionalidade.

1. Abra o [portal do Azure](https://portal.azure.com/)e navegue até a *de preparo (mywebapp\<unique_number\>/de preparo)* aplicativo Web. O aplicativo Web pode estar localizado rapidamente digitando *preparo* na caixa de pesquisa do portal:

    ![termo de pesquisa do aplicativo Web de preparo](media/cicd/portal-search-box.png)

1. Clique em **Central de implantação**. Um novo painel é exibido. Clique em **desconectar** para remover a configuração de controle de código-fonte Git local que foi adicionada no capítulo anterior. Confirme a operação de remoção clicando o **Sim** botão.
1. Navegue até a *mywebapp < unique_number >* o serviço de aplicativo. Como lembrete, caixa de pesquisa do portal pode ser usada para localizar rapidamente o serviço de aplicativo.
1. Clique em **Central de implantação**. Um novo painel é exibido. Clique em **desconectar** para remover a configuração de controle de código-fonte Git local que foi adicionada no capítulo anterior. Confirme a operação de remoção clicando o **Sim** botão.

## <a name="create-an-azure-devops-organization"></a>Criar uma organização de DevOps do Azure

1. Abra um navegador e navegue até a [página de criação de organização de DevOps do Azure](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Digite um nome exclusivo para o **escolher um nome inesquecível** textbox para formar a URL para acessar a sua organização de DevOps do Azure.
1. Selecione o **Git** botão de opção, já que o código é hospedado em um repositório GitHub.
1. Clique no botão **Continue (Continuar)**. Após uma breve espera, uma conta e um projeto de equipe, chamado *MyFirstProject*, são criados.

    ![Página de criação de organização de DevOps do Azure](media/cicd/vsts-account-creation.png)

1. Abra o email de confirmação indicando que a organização de DevOps do Azure e o projeto estão prontos para uso. Clique o **inicie seu projeto** botão:

    ![Botão seu projeto inicial](media/cicd/vsts-start-project.png)

1. Um navegador é aberto  *\<account_name\>. visualstudio.com*. Clique o *MyFirstProject* link para começar a configurar o pipeline de DevOps do projeto.

## <a name="configure-the-azure-pipelines-pipeline"></a>Configurar o pipeline de Pipelines do Azure

Há três etapas distintas para ser concluída. As etapas nos resultados em um pipeline de DevOps operacional três seções a seguir.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>DevOps do Azure conceda acesso ao repositório do GitHub

1. Expanda o **ou compilar o código de um repositório externo** accordion. Clique o **instalação compilar** botão:

    ![Botão de compilação da configuração](media/cicd/vsts-setup-build.png)

1. Selecione o **GitHub** opção a **selecionar uma fonte de** seção:

    ![Selecione uma fonte - GitHub](media/cicd/vsts-select-source.png)

1. A autorização é necessária antes de DevOps do Azure pode acessar seu repositório do GitHub. Insira *< GitHub_username > conexão do GitHub* na **nome de Conexão** caixa de texto. Por exemplo:

    ![Nome da conexão do GitHub](media/cicd/vsts-repo-authz.png)

1. Se a autenticação de dois fatores é ativada em sua conta do GitHub, um token de acesso pessoal é necessário. Clique nesse caso, o **autorizar com um token de acesso pessoal do GitHub** link. Consulte a [instruções oficiais de criação de token de acesso pessoal do GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) para obter ajuda. Somente o *repositório* escopo das permissões é necessária. Caso contrário, clique o **autorizar usando OAuth** botão.
1. Quando solicitado, entre sua conta do GitHub. Em seguida, selecione a autorização para conceder acesso a sua organização de DevOps do Azure. Se for bem-sucedido, um novo ponto de extremidade de serviço é criado.
1. Clique no botão de reticências ao lado de **repositório** botão. Selecione o *< GitHub_username > / leitor de feeds simples* repositório na lista. Clique o **selecionar** botão.
1. Selecione o *mestre* ramificar a **branch padrão para compilações de manuais e programadas** lista suspensa. Clique no botão **Continue (Continuar)**. Página seleção de modelo é exibido.

### <a name="create-the-build-definition"></a>Criar a definição de compilação

1. Na página seleção do modelo, insira *ASP.NET Core* na caixa de pesquisa:

    ![Na página do modelo de pesquisa de ASP.NET Core](media/cicd/vsts-template-selection.png)

1. Os resultados da pesquisa de modelo são exibidos. Passe o mouse sobre o **ASP.NET Core** modelo e clique no **aplicar** botão.
1. O **tarefas** guia da definição de compilação é exibida. Clique na guia **Gatilhos** .
1. Verifique as **habilitar a integração contínua** caixa. Sob o **filtros de ramificação** seção, confirme se o **tipo** lista suspensa é definida como *Include*. Defina as **especificação de Branch** lista suspensa para *mestre*.

    ![Habilitar as configurações de integração contínua](media/cicd/vsts-enable-ci.png)

    Essas configurações fazem com que uma compilação disparar quando qualquer alteração é enviada por push para o *mestre* branch do repositório do GitHub. Integração contínua é testada na [confirmar alterações no GitHub e implantar automaticamente para o Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) seção.

1. Clique o **salvar e enfileirar** botão e, em seguida, selecione o **salvar** opção:

    ![Botão Salvar](media/cicd/vsts-save-build.png)

1. A seguinte caixa de diálogo modal será exibida:

    ![Salvar a definição de compilação - caixa de diálogo modal](media/cicd/vsts-save-modal.png)

    Usar a pasta padrão de *\\* e clique no **salvar** botão.

### <a name="create-the-release-pipeline"></a>Criar o pipeline de lançamento

1. Clique o **versões** guia de seu projeto de equipe. Clique o **novo pipeline** botão.

    ![Guia versões - botão novo de definição](media/cicd/vsts-new-release-definition.png)

    O painel de seleção de modelo é exibido.

1. Na página seleção do modelo, insira *serviço de aplicativo* na caixa de pesquisa:

    ![Caixa de pesquisa de modelo de pipeline de lançamento](media/cicd/vsts-release-template-search.png)

1. Os resultados da pesquisa de modelo são exibidos. Passe o mouse sobre o **implantação de serviço de aplicativo do Azure com o Slot** modelo e clique no **aplicar** botão. O **Pipeline** guia do pipeline de lançamento é exibida.

    ![Guia Pipeline do pipeline de lançamento](media/cicd/vsts-release-definition-pipeline.png)

1. Clique o **Add** botão na **artefatos** caixa. O **adicionar um artefato** painel é exibido:

    ![Pipeline de lançamento - Adicionar painel de artefato](media/cicd/vsts-release-add-artifact.png)

1. Selecione o **construir** lado a lado do **tipo de fonte** seção. Esse tipo permite a vinculação do pipeline de lançamento para a definição de compilação.
1. Selecione *MyFirstProject* da **projeto** lista suspensa.
1. Selecione o nome de definição de compilação *MyFirstProject do ASP.NET Core-CI*, da **fonte (definição de compilação)** lista suspensa.
1. Selecione *mais recente* da **versão padrão** lista suspensa. Essa opção cria os artefatos produzidos pela execução mais recente da definição de compilação.
1. Substitua o texto na **alias de origem** caixa de texto com *Drop*.
1. Clique no botão **Adicionar**. O **artefatos** seção será atualizado para exibir as alterações.
1. Clique no ícone de raio para habilitar implantações contínuas:

    ![Pipeline de lançamento artefatos - ícone de raio](media/cicd/vsts-artifacts-lightning-bolt.png)

    Com essa opção habilitada, uma implantação ocorre sempre que uma nova compilação está disponível.
1. Um **gatilho de implantação contínua** painel aparece à direita. Clique no botão de alternância para habilitar o recurso. Não é necessário habilitar o **gatilho de solicitação de Pull**.
1. Clique o **Add** na lista suspensa a **criar filtros de ramificação** seção. Escolha o **ramificação de padrão da definição de compilação** opção. Esse filtro faz com que a versão disparar apenas para uma compilação do repositório do GitHub *mestre* branch.
1. Clique no botão **Salvar**. Clique o **Okey** botão na resultante **salvar** caixa de diálogo modal.
1. Clique o **ambiente 1** caixa. Uma **ambiente** painel aparece à direita. Alterar o *ambiente 1* texto na **nome do ambiente** caixa de texto *produção*.

   ![Pipeline de lançamento - caixa de texto de nome de ambiente](media/cicd/vsts-environment-name-textbox.png)

1. Clique o **1 fase, 2 tarefas** link na **produção** caixa:

    ![Pipeline de lançamento - link.png do ambiente de produção](media/cicd/vsts-production-link.png)

    O **tarefas** guia do ambiente é exibida.
1. Clique o **implantar o serviço de aplicativo do Azure ao Slot** tarefa. Suas configurações aparecem em um painel à direita.
1. Selecione a assinatura do Azure associada com o serviço de aplicativo da **assinatura do Azure** lista suspensa. Depois de selecionadas, clique o **autorizar** botão.
1. Selecione *aplicativo Web* da **tipo de aplicativo** lista suspensa.
1. Selecione *mywebapp / < unique_number / >* da **nome do serviço de aplicativo** lista suspensa.
1. Selecione *AzureTutorial* da **grupo de recursos** lista suspensa.
1. Selecione *preparo* da **Slot** lista suspensa.
1. Clique no botão **Salvar**.
1. Passe o mouse sobre o nome do pipeline de versão padrão. Clique no ícone de lápis para editá-lo. Use *MyFirstProject do ASP.NET Core-CD* como o nome.

    ![Nome do pipeline de lançamento](media/cicd/vsts-release-definition-name.png)

1. Clique no botão **Salvar**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Confirmar alterações no GitHub e implantar automaticamente no Azure

1. Abra *SimpleFeedReader.sln* no Visual Studio.
1. No Gerenciador de soluções, abra *Pages\Index.cshtml*. Alteração `<h2>Simple Feed Reader - V3</h2>` para `<h2>Simple Feed Reader - V4</h2>`.
1. Pressione **Ctrl**+**Shift**+**B** para compilar o aplicativo.
1. Confirme o arquivo para o repositório GitHub. Use o **alterações** página no Visual Studio *Team Explorer* guia ou execute o seguinte usando o shell de comando do computador local:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Enviar por push a alteração na *mestre* ramificar para o *origem* remoto do seu repositório GitHub:

    ```console
    git push origin master
    ```

    A confirmação é exibida no repositório do GitHub *mestre* branch:

    ![Confirmação do GitHub no branch mestre](media/cicd/github-commit.png)

    A compilação é disparada, como integração contínua está habilitada na definição de build **gatilhos** guia:

    ![Habilitar a integração contínua](media/cicd/enable-ci.png)

1. Navegue até a **enfileirado** guia da **Pipelines do Azure** > **compilações** página nos serviços de DevOps do Azure. Compilação enfileirada mostra a ramificação e confirme que disparou a compilação:

    ![compilação enfileirada](media/cicd/build-queued.png)

1. A compilação for bem-sucedida, ocorre uma implantação do Azure. Navegue até o aplicativo no navegador. Observe que o texto "V4" é exibido no título:

    ![aplicativo atualizado](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Examinar o pipeline de Pipelines do Azure

### <a name="build-definition"></a>Definição de compilação

Uma definição de compilação foi criada com o nome *MyFirstProject do ASP.NET Core-CI*. Após a conclusão, a compilação produz um *. zip* arquivo, incluindo os ativos a serem publicadas. O pipeline de lançamento implanta esses ativos do Azure.

A definição de compilação **tarefas** guia lista as etapas individuais que está sendo usadas. Há cinco tarefas de compilação.

![tarefas de definição de compilação](media/cicd/build-definition-tasks.png)

1. **Restaure** &mdash; executa o `dotnet restore` comando restaurar pacotes do NuGet do aplicativo. O pacote padrão feed usado é nuget.org.
1. **Crie** &mdash; executa o `dotnet build --configuration release` comando para compilar o código do aplicativo. Isso `--configuration` opção é usada para produzir uma versão otimizada do código, que é adequado para implantação em um ambiente de produção. Modificar a *BuildConfiguration* variável na definição de compilação **variáveis** guia se, por exemplo, uma configuração de depuração é necessária.
1. **Teste** &mdash; executa o `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` comando para executar testes de unidade do aplicativo. Testes de unidade são executados em qualquer linguagem C# projeto de correspondência a `**/*Tests/*.csproj` padrão glob. Resultados de teste são salvos em um *trx* arquivo no local especificado pelo `--results-directory` opção. Se qualquer teste falhar, a compilação falhará e não será implantada.

    > [!NOTE]
    > Para verificar se o trabalho de testes de unidade, modifique *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* propositadamente quebrar um dos testes. Por exemplo, altere `Assert.True(result.Count > 0);` à `Assert.False(result.Count > 0);` no `Returns_News_Stories_Given_Valid_Uri` método. Confirme e envie a alteração ao GitHub. A compilação é disparada e falha. O status do pipeline de compilação muda para **falha**. Reverta a alteração, a confirmação e o envio por push novamente. A compilação for bem-sucedida.

1. **Publique** &mdash; executa o `dotnet publish --configuration release --output <local_path_on_build_agent>` comando para produzir uma *. zip* arquivo com os artefatos a serem implantados. O `--output` opção especifica o local de publicação do *. zip* arquivo. Se o local é especificado passando um [variável predefinida](/azure/devops/pipelines/build/variables) denominado `$(build.artifactstagingdirectory)`. Essa variável se expande para um caminho local, como *c:\agent\_work\1\a*, no agente de compilação.
1. **Publicar artefato** &mdash; Publishes as *. zip* produzido pelo arquivo o **publicar** tarefa. A tarefa aceita o *. zip* local como um parâmetro, que é a variável predefinida do arquivo `$(build.artifactstagingdirectory)`. O *. zip* arquivo é publicado como uma pasta chamada *drop*.

Clique na definição de compilação **resumo** link para exibir um histórico das compilações com a definição:

![Histórico de definição de compilação do captura de tela mostrando](media/cicd/build-definition-summary.png)

Na página resultante, clique no link correspondente ao número de build exclusivo:

![Página resumida de definição de compilação captura de tela mostrando](media/cicd/build-definition-completed.png)

Um resumo dessa compilação específica é exibido. Clique o **artefatos** guia e observe o *drop* produzida pela compilação de pasta é listada:

![Captura de tela mostrando os artefatos de definição de compilação - pasta-depósito](media/cicd/build-definition-artifacts.png)

Use o **Baixe** e **explorar** links para inspecionar os artefatos publicados.

### <a name="release-pipeline"></a>Pipeline de lançamento

Um pipeline de lançamento foi criado com o nome *MyFirstProject do ASP.NET Core-CD*:

![Visão geral da captura de tela mostrando release pipeline](media/cicd/release-definition-overview.png)

Os dois principais componentes do pipeline de lançamento são as **artefatos** e o **ambientes**. Clicando na caixa na **artefatos** seção revela o seguinte painel:

![Captura de tela mostrando artefatos de pipeline da versão](media/cicd/release-definition-artifacts.png)

O **fonte (definição de compilação)** valor representa a definição de compilação ao qual esse pipeline de versão está vinculada. O *. zip* arquivo produzido por uma execução bem-sucedida da definição de compilação é fornecido para o *produção* ambiente para implantação no Azure. Clique o *1 fase, 2 tarefas* link na *produção* caixa do ambiente para exibir as tarefas de pipeline de lançamento:

![Captura de tela mostrando tarefas de pipeline de lançamento](media/cicd/release-definition-tasks.png)

O pipeline de lançamento consiste em duas tarefas: *Implantar o serviço de aplicativo do Azure ao Slot* e *gerenciar permuta do Slot de serviço de aplicativo do Azure -*. Clicar a primeira tarefa revela a configuração de tarefa a seguir:

![Tarefa de implantação do pipeline de lançamento captura de tela mostrando](media/cicd/release-definition-task1.png)

A assinatura do Azure, o tipo de serviço, o nome do aplicativo web, o grupo de recursos e o slot de implantação são definidos na tarefa de implantação. O **pacote ou pasta** caixa de texto contém o *. zip* caminho do arquivo a ser extraído e implantado para o *preparo* slot do *mywebapp\<exclusivo número\>*  aplicativo web.

Clicar a tarefa de troca de slot revela a configuração de tarefa a seguir:

![Tarefa de troca de slot captura de tela mostrando release pipeline](media/cicd/release-definition-task2.png)

A assinatura, grupo de recursos, tipo de serviço, nome do aplicativo web e detalhes do slot de implantação são fornecidas. O **troca com produção** caixa de seleção está marcada. Consequentemente, os bits implantados para o *preparo* slot são trocadas no ambiente de produção.

## <a name="additional-reading"></a>Leitura adicional

* [Criar seu primeiro pipeline com o Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Projeto de compilação e .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Implantar um aplicativo web com Pipelines do Azure](/azure/devops/pipelines/targets/webapp)
