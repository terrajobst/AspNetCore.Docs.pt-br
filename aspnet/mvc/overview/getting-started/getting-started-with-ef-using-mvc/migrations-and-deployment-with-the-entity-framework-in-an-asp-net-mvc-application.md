---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Usar migrações do EF em um aplicativo ASP.NET MVC e implantar no Azure'
author: tdykstra
description: Neste tutorial, você pode habilitar migrações do Code First e implanta o aplicativo em nuvem no Azure.
ms.author: riande
ms.date: 01/16/2019
ms.topic: tutorial
ms.assetid: d4dfc435-bda6-4621-9762-9ba270f8de4e
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: dd6bf5d8eb8a05dad1d230ef40c9b863e2af7094
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889906"
---
# <a name="tutorial-use-ef-migrations-in-an-aspnet-mvc-app-and-deploy-to-azure"></a>Tutorial: Usar migrações do EF em um aplicativo ASP.NET MVC e implantar no Azure

Até agora o aplicativo web de exemplo Contoso University tiver sido executado localmente em IIS Express no computador de desenvolvimento. Para disponibilizar um aplicativo real para que outras pessoas usem a Internet, você precisará implantá-lo em um provedor de hospedagem na web. Neste tutorial, você pode habilitar migrações do Code First e implanta o aplicativo em nuvem no Azure:

- Habilite migrações do Code First. O recurso de migrações permite que você altere o modelo de dados e implantar suas alterações para a produção atualizando o esquema de banco de dados sem precisar descartar e recriar o banco de dados.
- Implante no Azure. Esta etapa é opcional. Você pode continuar com os tutoriais restantes sem ter implantado o projeto.

É recomendável que você usar um processo de integração contínua com o controle do código-fonte para a implantação, mas este tutorial não aborda esses tópicos. Para obter mais informações, consulte o [controle de origem](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) e [integração contínua](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) capítulos [criando aplicativos de nuvem do mundo Real com o Azure](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

Neste tutorial, você:

> [!div class="checklist"]
> * Habilitar migrações do Code First
> * Implantar o aplicativo no Azure (opcional)

## <a name="prerequisites"></a>Pré-requisitos

- [Resiliência de conexão e interceptação de comando](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="enable-code-first-migrations"></a>Habilitar migrações do Code First

Quando você desenvolve um novo aplicativo, o modelo de dados é alterado com frequência e, sempre que o modelo é alterado, ele fica fora de sincronia com o banco de dados. Você configurou o Entity Framework para descartar e recriar o banco de dados sempre que você alterar o modelo de dados automaticamente. Quando você adicionar, remover, ou alterar as classes de entidade ou alterar seu `DbContext` de classe, na próxima vez que você executar o aplicativo automaticamente exclui o banco de dados existente, cria um novo que corresponde ao modelo e o propagará com dados de teste.

Esse método de manter o banco de dados em sincronia com o modelo de dados funciona bem até que você implante o aplicativo em produção. Quando o aplicativo está em execução na produção, ele normalmente armazena os dados que você deseja manter, e você não deseja perder tudo sempre que você fizer uma alteração, como adicionar uma nova coluna. O [migrações do Code First](https://msdn.microsoft.com/data/jj591621) recurso resolve esse problema, permitindo que o Code First atualizar o esquema de banco de dados, em vez de descartar e recriar o banco de dados. Neste tutorial, você implantará o aplicativo e para se preparar para fazer isso, você vai habilitar migrações.

1. Desabilitar o inicializador que você configurou anteriormente comentando ou excluindo o `contexts` elemento que você adicionou ao arquivo de Web. config do aplicativo.

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.xml?highlight=2,6)]
2. Também no aplicativo *Web. config* file, altere o nome do banco de dados na cadeia de conexão para ContosoUniversity2.

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.xml?highlight=2)]

    Essa alteração configura o projeto para que a primeira migração crie um novo banco de dados. Isso não é obrigatório, mas você verá posteriormente por que é uma boa ideia.
3. No menu **Ferramentas**, selecione **Gerenciador de pacotes NuGet** > **Console do Gerenciador de pacotes**.

1. No `PM>` prompt digite os seguintes comandos:

    ```text
    enable-migrations
    add-migration InitialCreate
    ```

    O `enable-migrations` comando cria um *migrações* pasta no projeto ContosoUniversity e ele coloca nessa pasta um *Configuration.cs* arquivo que você pode editar para configurar migrações.

    (Se você perdeu a etapa acima que direciona você para alterar o nome do banco de dados, as migrações encontrará o banco de dados existente e fazer automaticamente o `add-migration` comando. Isso é okey, que isso apenas significa que você não será executado um teste do código migrações antes de implantar o banco de dados. Posteriormente, quando você executar o `update-database` comando não acontece nada porque o banco de dados já existe.)

    Abra o *ContosoUniversity\Migrations\Configuration.cs* arquivo. Como a classe de inicializador que vimos anteriormente, o `Configuration` classe inclui um `Seed` método.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

    A finalidade de [semente](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) método é para que você possa inserir ou atualizar dados de teste depois que o Code First cria ou atualiza o banco de dados. O método é chamado quando o banco de dados é criado e sempre que o esquema de banco de dados é atualizado após a alteração de um modelo de dados.

### <a name="set-up-the-seed-method"></a>Configurar o método de semente

Quando você descartar e recria o banco de dados para cada alteração de modelo de dados, você usar a classe de inicializador `Seed` método para inserir dados de teste, porque após cada alteração de modelo de banco de dados é descartado e todos os dados de teste é perdido. Com migrações do Code First, teste os dados são mantidos após as alterações do banco de dados, portanto, incluindo dados de teste na [semente](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) método normalmente não é necessário. Na verdade, você não deseja que o `Seed` método para inserir dados de teste se você usará as migrações para implantar o banco de dados em produção, pois o `Seed` método será executado na produção. Nesse caso, você deseja que o `Seed` método a ser inserido no banco de dados somente os dados que você precisa em produção. Por exemplo, você pode querer o banco de dados para incluir nomes de departamento real no `Department` tabela quando o aplicativo está disponível em produção.

Para este tutorial, você usará as migrações para a implantação, mas seu `Seed` método vai inserir dados de teste de qualquer forma para torná-lo mais fácil de ver como a funcionalidade do aplicativo funciona sem precisar inserir manualmente o muitos dados.

1. Substitua o conteúdo do *Configuration.cs* arquivo pelo código a seguir, que carrega dados de teste para o novo banco de dados.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

    O [semente](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) método usa o objeto de contexto do banco de dados como um parâmetro de entrada e o código no método utiliza o objeto para adicionar novas entidades no banco de dados. Para cada tipo de entidade, o código cria uma coleção de novas entidades e os adiciona ao apropriado [DbSet](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.103).aspx) propriedade e, em seguida, salva as alterações no banco de dados. Não é necessário chamar o [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) método depois de cada grupo de entidades, assim como é feito aqui, mas fazer isso ajuda a localizar a origem de um problema se ocorrer uma exceção enquanto o código é escrito para o banco de dados.

    Algumas das instruções que inserem dados usam o [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) método para executar uma operação "upsert". Porque o `Seed` método é executado sempre que você executar o `update-database` de comando, normalmente após cada migração, você simplesmente não é possível inserir dados, porque as linhas que você está tentando adicionar já estará lá após a primeira migração que cria o banco de dados. A operação "upsert" impede que os erros que acontecem se você tentar inserir uma linha que já existe, mas ***substituições*** as alterações nos dados feitas durante o teste o aplicativo. Com os dados de teste em algumas tabelas você talvez não queira que aconteça: em alguns casos quando você altera os dados durante o teste você deseja as suas alterações para permanecer após as atualizações do banco de dados. Nesse caso, você deseja fazer uma operação de inserção condicional: inserir uma linha apenas se ele ainda não existir. O método de propagação usa as duas abordagens.

    O primeiro parâmetro passado para o [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) método Especifica a propriedade a ser usada para verificar se já existe uma linha. Para os dados de aluno de teste que você fornecer, o `LastName` propriedade pode ser usada para essa finalidade, pois cada sobrenome na lista é exclusivo:

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

    Esse código supõe que o último nomes sejam exclusivos. Se você adicionar manualmente um aluno com um sobrenome duplicado, você obterá a seguinte exceção na próxima vez que você executar uma migração:

    **Sequência contém mais de um elemento**

    Para obter informações sobre como lidar com dados redundantes, como dois alunos denominados "Alexander Carson", consulte [Seeding e bancos de dados de depuração Entity Framework (EF)](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) no blog de Rick Anderson. Para obter mais informações sobre o `AddOrUpdate` método, consulte [tome cuidado com o EF 4.3 AddOrUpdate método](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/) no blog de Julie.

    O código que cria `Enrollment` entidades pressupõe que você tenha o `ID` valor em entidades no `students` coleção, embora você não definir essa propriedade no código que cria a coleção.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs?highlight=2)]

    Você pode usar o `ID` propriedade aqui porque o `ID` valor é definido quando você chama `SaveChanges` para o `students` coleção. O EF automaticamente obtém o valor de chave primária quando ele insere uma entidade no banco de dados e atualiza o `ID` propriedade da entidade na memória.

    O código que adiciona cada `Enrollment` entidade para o `Enrollments` conjunto de entidades não usa o `AddOrUpdate` método. Ele verifica se uma entidade já existe e insere a entidade se ela não existir. Essa abordagem preserva as alterações feitas para um nível de registro usando o interface do usuário do aplicativo. O código executa um loop em cada membro do `Enrollment` [lista](https://msdn.microsoft.com/library/6sh2ey19.aspx) e se o registro não for encontrado no banco de dados, ele adiciona o registro no banco de dados. Na primeira vez que você atualizar o banco de dados, o banco de dados estará vazio, portanto, ele adicionará cada registro.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

2. Compile o projeto.

### <a name="execute-the-first-migration"></a>Execute a primeira migração

Quando você executou o `add-migration` de comando, as migrações gerou o código que cria o banco de dados do zero. Esse código também está na *migrações* pasta, no arquivo chamado  *&lt;timestamp&gt;\_InitialCreate.cs*. O `Up` método da `InitialCreate` classe cria as tabelas de banco de dados que correspondem aos conjuntos de entidade do modelo de dados, e o `Down` método exclui-los.

[!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

As migrações chamam o método `Up` para implementar as alterações do modelo de dados para uma migração. Quando você insere um comando para reverter a atualização, as Migrações chamam o método `Down`.

Essa é a migração inicial que foi criada quando você inseriu o `add-migration InitialCreate` comando. O parâmetro (`InitialCreate` no exemplo) é usado para o arquivo de nome e pode ser que você quiser; normalmente deve escolher uma palavra ou frase que resume o que está sendo feito na migração. Por exemplo, você pode nomear uma migração posterior &quot;AddDepartmentTable&quot;.

Se você criou a migração inicial quando o banco de dados já existia, o código de criação de banco de dados é gerado, mas ele não precisa ser executado porque o banco de dados já corresponde ao modelo de dados. Quando você implantar o aplicativo em outro ambiente no qual o banco de dados ainda não existe, esse código será executado para criar o banco de dados; portanto, é uma boa ideia testá-lo primeiro. É por isso que você alterou o nome do banco de dados na cadeia de conexão anteriormente&mdash;para que as migrações possam criar um novo do zero.

1. No **Package Manager Console** janela, digite o seguinte comando:

    `update-database`

    O `update-database` comando executa o `Up` método para criar o banco de dados e, em seguida, ele é executado o `Seed` para popular o banco de dados. O mesmo processo será executado automaticamente em produção depois que você implantar o aplicativo, como você verá na seção a seguir.
2. Use **Gerenciador de servidores** para inspecionar o banco de dados, como você fez no primeiro tutorial e executar o aplicativo para verificar que tudo ainda funciona como antes.

## <a name="deploy-to-azure"></a>Implantar no Azure

Até agora o aplicativo tiver sido executado localmente no IIS Express no computador de desenvolvimento. Para torná-lo disponível para que outras pessoas usem a Internet, você precisará implantá-lo em um provedor de hospedagem na web. Nesta seção do tutorial, você o implantará no Azure. Esta seção é opcional. Você pode ignorar essa etapa e continue com o tutorial a seguir, ou você pode adaptar as instruções nesta seção para um provedor de hospedagem diferente de sua escolha.

### <a name="use-code-first-migrations-to-deploy-the-database"></a>Usar migrações do Code First para implantar o banco de dados

Para implantar o banco de dados, você usará migrações do Code First. Quando você cria o perfil de publicação que você usa para definir as configurações para a implantação do Visual Studio, você selecionará uma caixa de seleção rotulada **Atualizar banco de dados**. Essa configuração faz com que o processo de implantação configurar automaticamente o aplicativo *Web. config* arquivo no servidor de destino, para que o Code First usa o `MigrateDatabaseToLatestVersion` a classe do inicializador.

Visual Studio não faz nada com o banco de dados durante o processo de implantação enquanto ela está copiando o seu projeto para o servidor de destino. Quando você executar o aplicativo implantado e acessa o banco de dados pela primeira vez após a implantação, o Code First verifica de se o banco de dados corresponde ao modelo de dados. Se houver uma incompatibilidade, Code First cria automaticamente o banco de dados (se ele ainda não exista) ou atualiza o esquema de banco de dados para a versão mais recente (se um banco de dados existe, mas não corresponde ao modelo). Se o aplicativo implementar um migrações `Seed` método, o método é executado depois que o banco de dados é criado ou o esquema é atualizado.

Suas migrações `Seed` método insere dados de teste. Se você estivesse Implantando um ambiente de produção, você teria que alterar o `Seed` , de modo que ele insere apenas os dados que você deseja a ser inserido no seu banco de dados de produção. Por exemplo, no seu modelo de dados atual você talvez queira ter cursos real, mas os alunos fictícios no banco de dados de desenvolvimento. Você pode escrever um `Seed` método para carregar no desenvolvimento e comente os alunos fictícios antes de implantar em produção. Ou você pode escrever um `Seed` método carregar somente os cursos e insira os alunos fictícios no banco de dados de teste manualmente usando a interface do usuário do aplicativo.

### <a name="get-an-azure-account"></a>Obter uma conta do Azure

Você precisará de uma conta do Azure. Se você ainda não tiver um, mas você tem uma assinatura do Visual Studio, você pode [ativar os benefícios da assinatura](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/
). Caso contrário, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [avaliação gratuita do Azure](https://azure.microsoft.com/free/).

### <a name="create-a-web-site-and-a-sql-database-in-azure"></a>Criar um site da web e um banco de dados SQL no Azure

Seu aplicativo web no Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em máquinas virtuais (VMs) que são compartilhadas com outros clientes do Azure. Um ambiente de hospedagem compartilhado é uma maneira de baixo custo para começar a usar na nuvem. Posteriormente, se aumentar o tráfego da web, o aplicativo possa ser dimensionado para atender à necessidade executando em VMs dedicadas. Para saber mais sobre as opções de preço para o serviço de aplicativo do Azure, leia [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

Você implantará o banco de dados no banco de dados SQL do Azure. Banco de dados SQL é um serviço de banco de dados relacional baseado em nuvem que se baseia em tecnologias do SQL Server. Ferramentas e aplicativos que funcionam com o SQL Server também funcionam com o banco de dados SQL.

1. No [Portal de gerenciamento](https://portal.azure.com), escolha **criar um recurso** na parte esquerda guia e, em seguida, escolha **ver todos** no **novo** painel (ou *folha*) para ver todos os recursos disponíveis. Escolher **aplicativo Web + SQL** na **Web** seção o **tudo** folha. Por fim, escolha **criar**.

    ![Criar um recurso no Portal do Azure](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/create-azure-resource.png)

   O formulário para criar um novo **novo aplicativo Web + SQL** recurso abre.

2. Insira uma cadeia de caracteres a **nome do aplicativo** caixa para usar como a URL exclusiva para seu aplicativo. A URL completa consistirá em que você inserir aqui mais o domínio padrão dos serviços de aplicativo do Azure (. azurewebsites.net). Se o **nome do aplicativo** já está em uso, o assistente notifica um vermelho *o nome do aplicativo não está disponível* mensagem. Se o **nome do aplicativo** está disponível, você verá uma marca de seleção verde.

3. No **assinatura** , escolha a assinatura do Azure no qual você deseja que o **serviço de aplicativo** deve residir.

4. No **grupo de recursos** caixa de texto, escolha um grupo de recursos ou criar um novo. Essa configuração especifica qual Datacenter seu site da web será executado em. Para obter mais informações sobre grupos de recursos, consulte [grupos de recursos](/azure/azure-resource-manager/resource-group-overview#resource-groups).

5. Criar um novo **plano do serviço de aplicativo** clicando o *seção de serviço de aplicativo*, **criar novo**e preencha **plano do serviço de aplicativo** (pode ser mesmo nome Serviço de aplicativo), **local**, e **tipo de preço** (há uma opção gratuita).

6. Clique em **banco de dados SQL**e, em seguida, escolha **criar um novo banco de dados** ou selecione um banco de dados existente.

7. No **nome** , digite um nome para seu banco de dados.
8. Clique o **servidor de destino** caixa e, em seguida, selecione **criar um novo servidor**. Como alternativa, se você tiver criado um servidor, você pode selecionar esse servidor na lista de servidores disponíveis.
9. Escolher **tipo de preço** , escolha *gratuito*. Se forem necessários recursos adicionais, o banco de dados pode ser escalado verticalmente a qualquer momento. Para saber mais sobre os preços do Azure SQL, consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
10. Modifique [agrupamento](/sql/relational-databases/collations/collation-and-unicode-support) conforme necessário.
11. Insira um administrador **nome de usuário de administrador de SQL** e **senha de administrador do SQL**.

   - Se você selecionou **servidor do novo banco de dados SQL**, definir um novo nome e uma senha que você usará mais tarde ao acessar o banco de dados.
   - Se você selecionou um servidor que você criou anteriormente, insira as credenciais para o servidor.

12. Coleta de telemetria pode ser habilitada para o serviço de aplicativo usando o Application Insights. Com pouca configuração, o Application Insights coleta eventos importantes, exceção, dependência, solicitação e informações de rastreamento. Para saber mais sobre o Application Insights, consulte [do Azure Monitor](https://azure.microsoft.com/services/monitor/).
13. Clique em **criar** na parte inferior para indicar que tiver terminado.

    O Portal de gerenciamento retorna à página do painel e o **notificações** área na parte superior da página mostra que o site está sendo criado. Depois de algum tempo (normalmente menos de um minuto), há uma notificação de que a implantação foi bem-sucedida. Na barra de navegação à esquerda, o novo serviço de aplicativo aparece na **serviços de aplicativos** seção e o novo banco de dados SQL aparece na **bancos de dados SQL** seção.

### <a name="deploy-the-app-to-azure"></a>Implantar o aplicativo no Azure

1. No Visual Studio, clique com botão direito no projeto no **Gerenciador de soluções** e selecione **publicar** no menu de contexto.

2. Sobre o **escolher um destino de publicação** , escolha **serviço de aplicativo** e, em seguida, **selecionar existente**e, em seguida, escolha **publicar**.

    ![Selecionar uma página de destino de publicação](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/publish-select-existing-azure-app-service.png)

3. Se você ainda não adicionou anteriormente sua assinatura do Azure no Visual Studio, execute as etapas na tela. Essas etapas habilitam o Visual Studio para se conectar à sua assinatura do Azure assim que a lista de **serviços de aplicativos** incluirá o seu site da web.

4. Sobre o **serviço de aplicativo** página, selecione o **assinatura** você adicionou o serviço de aplicativo. Sob **modo de exibição**, selecione **grupo de recursos**. Expanda o grupo de recursos que você adicionou o serviço de aplicativo e, em seguida, selecione o serviço de aplicativo. Escolher **Okey** para publicar o aplicativo.

5. O **saída** janela mostra quais ações de implantação foram executadas e relata a conclusão bem-sucedida da implantação.

6. Após a implantação bem-sucedida, o navegador padrão abre automaticamente a URL do site da web implantado.

    ![Students_index_page_with_paging](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/cloud-app-browser.png)

    Seu aplicativo agora está em execução na nuvem.

Neste ponto, o *SchoolContext* banco de dados tenha sido criado no banco de dados SQL do Azure porque você selecionou **executar migrações do Code First (executado na inicialização do aplicativo)**. O *Web. config* arquivo no site da web implantado foi alterado para que o [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) inicializador é executado na primeira vez em que seu código lê ou grava dados no banco de dados (o que aconteceu Quando você selecionou o **alunos** guia):

![Web.config file excerpt](https://asp.net/media/4367421/mig.png)

Além disso, o processo de implantação criada uma nova cadeia de caracteres de conexão *(SchoolContext\_DatabasePublish*) para migrações do Code First a ser usado para atualizar o esquema de banco de dados e a propagação do banco de dados.

![Cadeia de caracteres de Conexão no arquivo Web. config](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image26.png)

Você pode encontrar a versão implantada do arquivo Web. config em seu próprio computador *ContosoUniversity\obj\Release\Package\PackageTmp\Web.config*. Você pode acessar o implantado *Web. config* próprio arquivo por meio de FTP. Para obter instruções, consulte [implantação de Web do ASP.NET usando o Visual Studio: Implantando uma atualização de código](xref:web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update). Siga as instruções que começam com "para usar uma ferramenta FTP, você precisa de três itens: a URL de FTP, o nome de usuário e a senha."

> [!NOTE]
> O aplicativo web não implementa a segurança, portanto, qualquer pessoa que encontre a URL pode alterar os dados. Para obter instruções sobre como proteger o site da web, consulte [implantar um aplicativo ASP.NET MVC seguro com o banco de dados de associação, OAuth e SQL no Azure](/aspnet/core/security/authorization/secure-data). Você pode impedir que outras pessoas usando o site, interrompendo o serviço usando o Portal de gerenciamento do Azure ou **Gerenciador de servidores** no Visual Studio.

![Parar o item de menu de serviço de aplicativo](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/server-explorer-stop-app-service.png)

## <a name="advanced-migrations-scenarios"></a>Cenários de migrações avançadas

Se você implantar um banco de dados, executando migrações automaticamente, conforme mostrado neste tutorial, e você estiver implantando em um site da web que é executado em vários servidores, você poderia obter vários servidores tentando executar migrações ao mesmo tempo. As migrações são atômicas, portanto, se dois servidores tentarem executar a migração do mesmo, um será bem-sucedida e o outro falhará (supondo que as operações não podem ser feitas de duas vezes). Nesse cenário para evitar esses problemas, você pode chamar migrações manualmente e definir seu próprio código para que ele ocorre apenas uma vez. Para obter mais informações, consulte [em execução e scripts de migrações de código](http://romiller.com/2012/02/09/running-scripting-migrations-from-code/) no blog de Rowan Miller e [Migrate.exe](/ef/ef6/modeling/code-first/migrations/migrate-exe) (para executar migrações a partir da linha de comando).

Para obter informações sobre outros cenários de migrações, consulte [a série Screencast migrações](https://blogs.msdn.com/b/adonet/archive/2014/03/12/migrations-screencast-series.aspx).

## <a name="code-first-initializers"></a>Inicializadores de primeiro código

Na seção de implantação, você viu a [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) inicializador que está sendo usado. Código pela primeira vez também fornece outros inicializadores, incluindo [CreateDatabaseIfNotExists](https://msdn.microsoft.com/library/gg679221(v=vs.103).aspx) (o padrão), [DropCreateDatabaseIfModelChanges](https://msdn.microsoft.com/library/gg679604(v=VS.103).aspx) (que você usou anteriormente) e [ DropCreateDatabaseAlways](https://msdn.microsoft.com/library/gg679506(v=VS.103).aspx). O `DropCreateAlways` inicializador pode ser útil para configurar condições para testes de unidade. Você também pode escrever seus próprios inicializadores, e você pode chamar um inicializador explicitamente, se você não quiser esperar até que o aplicativo lê ou grava no banco de dados.

Para obter mais informações sobre inicializadores, consulte [Noções básicas sobre inicializadores de banco de dados no Entity Framework Code First](http://www.codeguru.com/csharp/article.php/c19999/Understanding-Database-Initializers-in-Entity-Framework-Code-First.htm) e o capítulo 6 do livro [Programming Entity Framework: Code First](http://shop.oreilly.com/product/0636920022220.do) por Julie Lerman e Rowan Miller.

## <a name="get-the-code"></a>Obter o código

[Baixe o projeto concluído](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Recursos adicionais

Links para outros recursos do Entity Framework pode ser encontrado na [acesso a dados ASP.NET – recursos recomendados](xref:whitepapers/aspnet-data-access-content-map).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Migrações do Code First habilitadas
> * Implantou o aplicativo no Azure (opcional)

Avance para o próximo artigo para saber como criar um modelo de dados mais complexo para um aplicativo ASP.NET MVC.
> [!div class="nextstepaction"]
> [Criar um modelo de dados mais complexo](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)
