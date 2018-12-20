---
uid: aspnet/overview/developing-apps-with-windows-azure/maintainable-azure-websites-managing-change-and-scale
title: 'Laboratório prático: sites do Azure sustentáveis: gerenciamento de alteração e escala | Microsoft Docs'
author: rick-anderson
description: Neste laboratório, saiba como o Microsoft Azure torna fácil criar e implantar sites em produção.
ms.author: riande
ms.date: 07/16/2014
ms.assetid: ecfd0eb4-c4ad-44e6-9db9-a2a66611ff6a
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/maintainable-azure-websites-managing-change-and-scale
msc.type: authoredcontent
ms.openlocfilehash: bc6de2f0c8b2cd958c198abb90fc4ad97613e973
ms.sourcegitcommit: a4dcca4f1cb81227c5ed3c92dc0e28be6e99447b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48913301"
---
<a name="hands-on-lab-maintainable-azure-websites-managing-change-and-scale"></a>Laboratório prático: sites do Azure sustentáveis: gerenciamento de alteração e escala
====================
por [Web Camps equipe](https://twitter.com/webcamps)

[Baixe o Kit de treinamento do Web Camps](http://aka.ms/webcamps-training-kit)

> Microsoft Azure facilita compilar e implantar sites em produção. Mas você não ainda terminou quando seu aplicativo estiver ativo, você estiver apenas começando! Você precisará lidar com a alteração requisitos, as atualizações de banco de dados, escala e muito mais. Felizmente, o serviço de aplicativo do Azure tem cobertura completa, com muitos recursos para ajudá-lo a manter seus sites em perfeito funcionamento.
>
> O Azure oferece desenvolvimento seguro e flexível, implantação e opções de dimensionamento para qualquer aplicativo da web de tamanho. Aproveite as ferramentas existentes para criar e implantar aplicativos sem a dificuldade de gerenciar a infraestrutura.
>
> Provisione um aplicativo web de produção por conta própria em minutos por implantar facilmente o conteúdo criado usando a ferramenta de desenvolvimento favorita. Você pode implantar um site existente diretamente do controle do código-fonte com suporte para **Git**, **GitHub**, **Bitbucket**, **TFS**e até mesmo  **DropBox**. Implantar diretamente do seu IDE favorito ou de scripts que usam **PowerShell** no Windows ou **CLI** ferramentas executadas em qualquer sistema operacional. Uma vez implantado, mantenha seus sites atualizados constantemente com o suporte para a implantação contínua.
>
> Azure fornece armazenamento em nuvem escalonável e durável, backup e soluções de recuperação para quaisquer dados, pequenos ou grandes. Ao implantar aplicativos em um ambiente de produção, os serviços de armazenamento, como tabelas, Blobs e bancos de dados SQL, ajuda você a dimensionar seu aplicativo na nuvem.
>
> Com bancos de dados SQL, é importante manter o seu banco de dados produtivo atualizadas durante a implantação de novas versões do seu aplicativo. Obrigado **Entity Framework Code First Migrations**, o desenvolvimento e implantação do seu modelo de dados foi simplificado para atualizar seus ambientes em minutos. Este laboratório prático mostrará os tópicos diferentes, que você pode encontrar ao implantar seu aplicativo web em ambientes de produção no Microsoft Azure.
>
> Todo o código de exemplo e trechos de código são incluídos no Web Camps treinamento Kit, disponível em [ http://aka.ms/webcamps-training-kit ](http://aka.ms/webcamps-training-kit).
>
> Para obter mais cobertura detalhada deste tópico, consulte o [criando aplicativos de nuvem do mundo Real com o Azure de livro eletrônico](building-real-world-cloud-apps-with-windows-azure/introduction.md).


<a id="Overview"></a>
## <a name="overview"></a>Visão geral

<a id="Objectives"></a>
### <a name="objectives"></a>Objetivos

Neste laboratório prático, você aprenderá como:

- Habilitar migrações do Entity Framework com um modelo existente
- Atualizar o modelo de objeto e o banco de dados adequadamente usando migrações do Entity Framework
- Implantar no serviço de aplicativo do Azure usando o Git
- Reversão para uma implantação anterior usando o portal de gerenciamento do Azure
- Usar o armazenamento do Azure para dimensionar um aplicativo web
- Configurar o dimensionamento automático para um aplicativo web usando o Portal de gerenciamento do Azure
- Criar e configurar um projeto de teste de carga no Visual Studio

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Pré-requisitos

O exemplo a seguir é necessário para concluir este laboratório prático:

- [O Visual Studio Express 2013 para Web](https://www.microsoft.com/visualstudio/) ou maior
- [SDK do Azure para .NET 2.2](https://www.microsoft.com/windowsazure/sdk/)
- [Sistema de controle de versão do GIT](http://git-scm.com/download)
- Uma assinatura do Microsoft Azure

    - Inscreva-se um [avaliação gratuita](http://aka.ms/watk-freetrial)
    - Se você for um Visual Studio Professional, o Test Professional, o Premium ou o Ultimate com MSDN ou plataformas MSDN, ativar sua [benefício do MSDN](http://aka.ms/watk-msdn) agora para começar a desenvolver e testar no Azure
    - [BizSpark](http://aka.ms/watk-bizspark) membros recebem automaticamente o Azure benefício por meio do Visual Studio Ultimate com assinaturas do MSDN
    - Os membros de [Microsoft Partner Network](http://aka.ms/watk-mpn) programa Cloud Essentials recebem créditos mensais do Azure sem nenhum encargo

<a id="Setup"></a>
### <a name="setup"></a>Configuração

Para executar os exercícios neste laboratório prático, você precisará configurar seu ambiente pela primeira vez.

1. Abra o Windows Explorer e navegue para o laboratório **origem** pasta.
2. Clique duas vezes em **Setup. cmd** e selecione **executar como administrador** para iniciar o processo de instalação que irá configurar seu ambiente e instalem os trechos de código do Visual Studio para este laboratório.
3. Se a caixa de diálogo controle de conta de usuário é exibida, confirme a ação para continuar.

> [!NOTE]
> Verifique se que você tiver marcado todas as dependências para este laboratório antes de executar a instalação.


<a id="CodeSnippets"></a>
### <a name="using-the-code-snippets"></a>Usando os trechos de código

Em todo o documento de laboratório, você será instruído a inserir blocos de código. Para sua conveniência, a maioria desse código é fornecido como o Visual Studio trechos de código que pode ser acessada de dentro do Visual Studio 2013 para evitar ter que adicioná-lo manualmente.

> [!NOTE]
> Cada exercício é acompanhado por uma solução inicial localizada na **começar** pasta do exercício que permite que você siga cada exercício independentemente dos outros. Esteja ciente de que os trechos de código são adicionados durante um exercício estão ausentes desses iniciando soluções e podem não funcionar até concluir o exercício. Dentro do código-fonte para um exercício, você também encontrará uma **final** pasta que contém uma solução do Visual Studio com o código que é o resultado de concluir as etapas no exercício correspondente. Você pode usar essas soluções como uma diretriz se você precisar de ajuda adicional ao trabalhar com este laboratório prático.


* * *

<a id="Exercises"></a>
## <a name="exercises"></a>Exercícios

Este laboratório prático inclui os seguintes exercícios:

1. [Usando as migrações do Entity Framework](#Exercise1)
2. [Implantar um aplicativo Web de preparo](#Exercise2)
3. [Executar a reversão da implantação em produção](#Exercise3)
4. [Dimensionamento usando o armazenamento do Azure](#Exercise4)
5. [Usar o dimensionamento automático para aplicativos Web](#Exercise5) (opcional para o Visual Studio 2013 Ultimate edition)

Tempo estimado para concluir este laboratório: **75 minutos**

> [!NOTE]
> Quando você inicia o Visual Studio pela primeira vez, você deve selecionar uma das coleções de configurações predefinidas. Cada coleção predefinida foi projetada para corresponder a um estilo de desenvolvimento específico e determina o comportamento do editor, layouts de janela, trechos de código IntelliSense e opções da caixa de diálogo. Os procedimentos neste laboratório descrevem as ações necessárias para realizar uma determinada tarefa no Visual Studio ao usar o **configurações gerais de desenvolvimento** coleção. Se você escolher uma coleção de configurações diferentes para seu ambiente de desenvolvimento, pode haver diferenças nas etapas que você deve levar em conta.


<a id="Exercise1"></a>
### <a name="exercise-1-using-entity-framework-migrations"></a>Exercício 1: Usar migrações do Entity Framework

Quando você estiver desenvolvendo um aplicativo, seu modelo de dados pode mudar ao longo do tempo. Essas alterações poderá afetar o modelo existente no banco de dados (se você estiver criando uma nova versão) e é importante manter seu banco de dados atualizados para evitar erros.

Para simplificar a essas alterações em seu modelo de rastreamento **Entity Framework Code First Migrations** automaticamente detectar alterações comparando seu modelo com o esquema de banco de dados e gera o código específico para atualizar seu banco de dados Criando novos *versões* do banco de dados.

Este exercício mostra como habilitar **migrações** para seu aplicativo e como você pode facilmente detectar e gerar alterações para atualizar seus bancos de dados.

<a id="Ex1Task1"></a>
#### <a name="task-1--enabling-migrations"></a>Tarefa 1 – habilitar migrações

Nesta tarefa, você passará pelas etapas de habilitação **Entity Framework Code First Migrations** para o banco de dados **Geek Quiz**, alterando o modelo e Noções básicas sobre como essas alterações são refletidas no banco de dados.

1. Abra o Visual Studio e abra o **GeekQuiz.sln** arquivo de solução de **Source\Ex1 UsingEntityFrameworkMigrations\Begin**.
2. Compile a solução para baixar e instalar o **NuGet** as dependências do pacote. Para fazer isso, a solução com o botão direito e clique em **compilar solução** ou pressione **Ctrl + Shift + B**.
3. Dos **ferramentas** menu no Visual Studio, selecione **Gerenciador de pacotes NuGet**e, em seguida, clique em **Package Manager Console**.
4. No **Package Manager Console**, digite o seguinte comando e pressione **Enter**. Uma migração inicial com base no modelo existente será criada.

    [!code-powershell[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample1.ps1)]

    ![Habilitar migrações](maintainable-azure-websites-managing-change-and-scale/_static/image1.png "habilitar migrações")

    *Habilitar migrações*

    > [!NOTE]
    > Este comando adiciona uma **migrações** pasta ao projeto de Geek Quiz que contém um arquivo chamado **Configuration.cs**. O **configuração** classe permite que você configurar o comportamento de migrações para o seu contexto.
5. Com as migrações habilitadas, você precisará atualizar o **Configuration** classe para popular o banco de dados com os dados iniciais que **Geek Quiz** requer. Sob **migrações**, substitua o **Configuration.cs** arquivo com aquele localizado na **Source\Assets** pasta deste laboratório.

    > [!NOTE]
    > Uma vez que **migrações** chamará o **semente** método com cada atualização do banco de dados, você precisa ter certeza de que os registros não são duplicados no banco de dados. O **AddOrUpdate** método ajudará a evitar dados duplicados.
6. Para adicionar uma migração inicial, digite o seguinte comando e pressione **Enter**.

    > [!NOTE]
    > Certifique-se de que não há nenhum banco de dados denominado &quot;GeekQuizProd&quot; em sua instância de LocalDB.

    [!code-powershell[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample2.ps1)]

    ![Adição de migração de esquema de base](maintainable-azure-websites-managing-change-and-scale/_static/image2.png "adicionando a migração de esquema base")

    *Adição de migração de esquema base*

    > [!NOTE]
    > **Add-Migration** irá dar suporte a próxima migração com base em alterações feitas ao seu modelo, pois a última migração foi criada. Nesse caso, que é a primeira migração do projeto, ele adicionará os scripts para criar todas as tabelas definidas na **TriviaContext** classe.
7. Execute a migração para atualizar o banco de dados, executando o comando a seguir. Para este comando, especifique o **Verbose** sinalizador para exibir as instruções SQL que está sendo aplicadas ao banco de dados de destino.

    [!code-powershell[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample3.ps1)]

    ![Criando banco de dados inicial](maintainable-azure-websites-managing-change-and-scale/_static/image3.png "Criando banco de dados inicial")

    *Criando o banco de dados inicial*

    > [!NOTE]
    > **Update-Database** aplicará qualquer pendente migrações ao banco de dados. Nesse caso, ele criará o banco de dados usando a cadeia de caracteres de conexão definida no arquivo Web. config.
8. Vá para **modo de exibição** menu e open **SQL Server Object Explorer**.

    ![Abrir no Pesquisador de objetos do SQL Server](maintainable-azure-websites-managing-change-and-scale/_static/image4.png "abrir no Pesquisador de objetos do SQL Server")

    *Abrir no Pesquisador de objetos do SQL Server*
9. No **Pesquisador de objetos do SQL Server** janela, conectar-se à instância do LocalDB clicando com o **SQL Server** nó e selecionando **adicionar SQL Server...**  opção.

    ![Adição de uma instância do SQL Server](maintainable-azure-websites-managing-change-and-scale/_static/image5.png "adicionando uma instância do SQL Server")

    *A adição de uma instância do SQL Server para o Pesquisador de objetos do SQL Server*
10. Defina o **nome do servidor** para *(localdb) \v11.0* e deixe **autenticação do Windows** como seu modo de autenticação. Clique em **Connect** para continuar.

    ![Conectar-se ao LocalDB](maintainable-azure-websites-managing-change-and-scale/_static/image6.png "conectar-se ao LocalDB")

    *Conectar-se ao LocalDB*
11. Abra o **GeekQuizProd** do banco de dados e expanda o **tabelas** nó. Como você pode ver, o **Update-Database** comando gerado de todas as tabelas definidas na **TriviaContext** classe. Localize o **dbo. TriviaQuestions** de tabela e abra o nó de colunas. A próxima tarefa, você adicionar uma nova coluna a essa tabela e atualiza o banco de dados usando **migrações**.

    ![Colunas de perguntas de desafios](maintainable-azure-websites-managing-change-and-scale/_static/image7.png "os desafios de perguntas de colunas")

    *Os desafios de perguntas de colunas*

<a id="Ex1Task2"></a>
#### <a name="task-2--updating-database-schema-using-migrations"></a>Tarefa 2 – atualizar esquema de banco de dados usando migrações

Nesta tarefa, você usará **Entity Framework Code First Migrations** para detectar uma alteração em seu modelo e gerar o código necessário para atualizar o banco de dados. Você atualizará o **TriviaQuestions** entidade com a adição de uma nova propriedade. Em seguida, execute comandos para criar uma nova migração para incluir a nova coluna na tabela.

1. Na **Gerenciador de soluções**, clique duas vezes o **TriviaQuestion.cs** localizado dentro do arquivo a **modelos** pasta.
2. Adicionar uma nova propriedade chamada **dica**, conforme mostrado no trecho de código a seguir.

    [!code-csharp[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample4.cs)]
3. No **Package Manager Console**, digite o seguinte comando e pressione **Enter**. Será criada uma nova migração refletindo a alteração feita em nosso modelo.

    [!code-powershell[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample5.ps1)]

    ![Add-Migration](maintainable-azure-websites-managing-change-and-scale/_static/image8.png "Add-Migration")

    *Add-Migration*

    > [!NOTE]
    > Um arquivo de migração é composto de dois métodos, **para cima** e **para baixo**.
    >
    > - O **backup** método será usado para especificar o que muda a versão atual do nosso aplicativo precisam se aplicam ao banco de dados.
    > - O **para baixo** é usado para reverter as alterações que adicionamos à **backup** método.
    >
    > Quando a migração de banco de dados atualiza o banco de dados, ele será executado todas as migrações na ordem de carimbo de hora e somente aquelas que não foram usados desde a última atualização (o \_tabela MigrationHistory mantém o controle de quais migrações foram aplicadas). O **backup** será chamado de método de todas as migrações e fará as alterações que especificamos no banco de dados. Se decidirmos voltar para uma migração anterior, o **para baixo** método será chamado para refazer as alterações em uma ordem inversa.
4. No **Package Manager Console**, digite o seguinte comando e pressione **Enter**.

    [!code-powershell[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample6.ps1)]
5. A saída do **Update-Database** comando gerado uma **Alter Table** instrução SQL para adicionar uma nova coluna para o **TriviaQuestions** de tabela, conforme mostrado na imagem abaixo.

    ![Adicione a instrução SQL de coluna gerada](maintainable-azure-websites-managing-change-and-scale/_static/image9.png "adicionar coluna instrução de SQL gerada")

    *Adicione a instrução SQL de coluna gerada*
6. Na **Pesquisador de objetos do SQL Server**, atualize o **dbo. TriviaQuestions** de tabela e verifique se o novo **dica** coluna é exibida.

    ![Mostrando a nova coluna de dica](maintainable-azure-websites-managing-change-and-scale/_static/image10.png "mostrando a nova coluna de dica")

    *Mostrando a nova coluna de dica*
7. Volta a **TriviaQuestion.cs** editor, adicione um **StringLength** restrição para o *dica* propriedade, conforme mostrado no trecho de código a seguir.

    [!code-csharp[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample7.cs)]
8. No **Package Manager Console**, digite o seguinte comando e pressione **Enter**.

    [!code-powershell[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample8.ps1)]
9. No **Package Manager Console**, digite o seguinte comando e pressione **Enter**.

    [!code-powershell[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample9.ps1)]
10. A saída do **Update-Database** comando gerado uma **Alter Table** instrução SQL para atualizar o *dica* o tipo de coluna a **TriviaQuestions** de tabela, conforme mostrado na imagem abaixo.

    ![Alterar instrução de SQL da coluna gerada](maintainable-azure-websites-managing-change-and-scale/_static/image11.png "alterar instrução de SQL da coluna gerada")

    *Alterar instrução de SQL da coluna gerada*
11. Na **Pesquisador de objetos do SQL Server**, atualize o **dbo. TriviaQuestions** de tabela e verifique se o **dica** é do tipo de coluna **nvarchar(150)**.

    ![Mostrando a nova restrição](maintainable-azure-websites-managing-change-and-scale/_static/image12.png "mostrando a nova restrição")

    *Mostrando a nova restrição*

<a id="Exercise2"></a>
### <a name="exercise-2-deploying-a-web-app-to-staging"></a>Exercício 2: Implantando um aplicativo Web de preparo

**Aplicativos Web no serviço de aplicativo do Azure** permite que você execute a publicação em etapas. Publicação em etapas cria um slot de site de preparo para cada site de produção padrão e permite que você troque esses slots, sem nenhum tempo de inatividade. Isso é realmente útil para validar as alterações antes de liberar para o público, incrementalmente integrar o conteúdo do site e reverter em caso de alterações não estão funcionando conforme o esperado.

Neste exercício, você implantará o aplicativo **Geek Quiz** ao ambiente de preparo do aplicativo web usando o controle do código-fonte Git. Para fazer isso, você irá criar o aplicativo web e provisionar os componentes necessários no portal de gerenciamento, configurar uma **Git** repositório e enviar por push o aplicativo de código-fonte do seu computador local para o slot de preparo. Você também atualizará seu banco de dados de produção com o **migrações do Code First** criado no exercício anterior. Em seguida, você executará o aplicativo nesse ambiente de teste para verificar sua operação. Quando estiver satisfeito que ele está funcionando de acordo com suas expectativas, você promoverá o aplicativo em produção.

> [!NOTE]
> Para habilitar a publicação em etapas, o aplicativo web deve estar no **o modo padrão**. Observe que encargos adicionais serão gerados se você alterar o seu aplicativo web para o modo padrão. Para obter mais informações sobre preços, consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).


<a id="Ex2Task1"></a>
#### <a name="task-1--creating-a-web-app-in-azure-app-service"></a>Tarefa 1 – criar um aplicativo Web no serviço de aplicativo do Azure

Nesta tarefa, você criará um aplicativo web no **serviço de aplicativo do Azure** no portal de gerenciamento. Você também configurará um **banco de dados SQL** para persistir os dados de aplicativo e configurar um repositório Git para controle de origem.

1. Vá para o [portal de gerenciamento do Azure](https://manage.windowsazure.com) e entre usando a conta Microsoft associada à sua assinatura.

    ![Entrar no portal de gerenciamento do Azure](maintainable-azure-websites-managing-change-and-scale/_static/image13.png)

    *Entrar no portal de gerenciamento do Azure*
2. Clique em **New** na barra de comandos na parte inferior da página.

    ![Criando um novo aplicativo web](maintainable-azure-websites-managing-change-and-scale/_static/image14.png "criando um novo aplicativo web")

    *Criando um novo aplicativo web*
3. Clique em **Compute**, **site** e, em seguida, **criação personalizada**.

    ![Criando um novo aplicativo web usando criação personalizada](maintainable-azure-websites-managing-change-and-scale/_static/image15.png "criando um novo aplicativo web usando criação personalizada")

    *Criando um novo aplicativo web usando criação personalizada*
4. No **New Website - criação personalizada** caixa de diálogo caixa, forneça uma disponível **URL** (por exemplo, *Geek Quiz*), selecione um local no **região** lista suspensa e selecione **criar um novo banco de dados SQL** na **banco de dados** lista suspensa. Por fim, selecione o **publicar do controle de origem** caixa de seleção e clique em **próxima**.

    ![Personalizando o novo aplicativo web](maintainable-azure-websites-managing-change-and-scale/_static/image16.png)

    *Personalizando o novo aplicativo web*
5. Especifique as seguintes informações para as configurações de banco de dados:

   - No **nome** texto, digite um nome de banco de dados (por exemplo, *geekquiz\_db*)
   - No servidor **lista suspensa** lista, selecione **servidor de banco de dados SQL novo**. Como alternativa, você pode selecionar um servidor existente.
   - No **nome de usuário de banco de dados** e **senha do banco de dados** caixas, insira o nome de usuário administrador e a senha para o servidor de banco de dados SQL. Se você selecionar um servidor, você já tiver criado, você será solicitado a senha.

     ![Especificando as configurações de banco de dados](maintainable-azure-websites-managing-change-and-scale/_static/image17.png)

     *Especificando as configurações de banco de dados*
6. Clique em **Próximo** para continuar.
7. Selecione **repositório Git Local** para o controle do código-fonte para usar e clique em **próxima**.

    > [!NOTE]
    > Você será solicitado para as credenciais de implantação (um nome de usuário e senha).

    ![Criando o repositório do Git](maintainable-azure-websites-managing-change-and-scale/_static/image18.png)

    *Criando o repositório do Git*
8. Aguarde até que o novo aplicativo web é criado.

    > [!NOTE]
    > Por padrão, o Azure fornece domínios *azurewebsites.net* , mas também oferece a possibilidade de definir domínios personalizados usando o portal de gerenciamento do Azure. No entanto, você pode gerenciar domínios personalizados somente se você estiver usando alguns modos de serviço de aplicativo do Azure.
    >
    > O serviço de aplicativo do Azure está disponível nas edições gratuito, compartilhado, básico, Standard e Premium. No modo gratuito e compartilhado, todos os aplicativos web executados em um ambiente de multilocatário em têm cotas para uso de CPU, memória e rede. O número máximo de aplicativos gratuitos pode variar com seu plano. No modo padrão, você escolha quais aplicativos executados em máquinas virtuais dedicadas que correspondem no Azure de padrão de recursos de computação. Você pode encontrar a configuração do modo de aplicativo web na **escala** menu do aplicativo web.
    >
    > ![O serviço de aplicativo do Azure modos](maintainable-azure-websites-managing-change-and-scale/_static/image19.png "modos do serviço de aplicativo do Azure")
    >
    > Se você estiver usando **Shared** ou **padrão** modo, você será capaz de gerenciar domínios personalizados para seu aplicativo web, vá para seu aplicativo **configurar** menu e em **Gerenciar domínios** sob *nomes de domínio*.
    >
    > ![Gerenciar domínios](maintainable-azure-websites-managing-change-and-scale/_static/image20.png "gerenciar domínios")
    >
    > ![Gerenciar domínios personalizados](maintainable-azure-websites-managing-change-and-scale/_static/image21.png "gerenciar domínios personalizados")
9. Depois que o aplicativo web é criado, clique no link sob a **URL** coluna para verificar se o novo aplicativo web está em execução.

    ![Navegando até o novo aplicativo web](maintainable-azure-websites-managing-change-and-scale/_static/image22.png)

    *Navegando até o novo aplicativo web*

    ![aplicativo Web em execução](maintainable-azure-websites-managing-change-and-scale/_static/image23.png)

    *aplicativo Web em execução*

<a id="Ex2Task2"></a>
#### <a name="task-2--creating-the-production-sql-database"></a>Tarefa 2 – criar o banco de dados do SQL de produção

Nesta tarefa, você aprenderá a usar o **Entity Framework Code First Migrations** para criar o banco de dados direcionamento a **banco de dados SQL** instância que você criou na tarefa anterior.

1. No Portal de gerenciamento, navegue até o aplicativo web criado na tarefa anterior e vá para seu **Dashboard**.
2. No **Dashboard** , clique em **exibir cadeias de caracteres de conexão** link sob a **visão rápida** seção.

    ![Exibir cadeias de caracteres de conexão](maintainable-azure-websites-managing-change-and-scale/_static/image24.png "exibir cadeias de caracteres de conexão")

    *Exibir cadeias de conexão*
3. Cópia de **cadeia de caracteres de conexão** de valor e fechar a caixa de diálogo.

    ![Cadeia de caracteres de Conexão no Portal de gerenciamento do Azure](maintainable-azure-websites-managing-change-and-scale/_static/image25.png "cadeia de caracteres de Conexão no Portal de gerenciamento do Azure")

    *Cadeia de caracteres de Conexão no Portal de gerenciamento do Azure*
4. Clique em **bancos de dados SQL** para ver a lista de bancos de dados SQL do Azure

    ![Menu de banco de dados SQL](maintainable-azure-websites-managing-change-and-scale/_static/image26.png "menu banco de dados SQL")

    *Menu de banco de dados SQL*
5. Localize o banco de dados que você criou na tarefa anterior e clique no servidor.

    ![Servidor de banco de dados SQL](maintainable-azure-websites-managing-change-and-scale/_static/image27.png "o servidor de banco de dados SQL")

    *Servidor de banco de dados SQL*
6. No **início rápido** página do servidor, clique em **configurar**.

    ![Menu Configurar](maintainable-azure-websites-managing-change-and-scale/_static/image28.png "menu Configurar")

    *Configurar o menu*
7. No **endereços IP permitidos** seção, clique em **Adicionar para os endereços IP permitidos** link para habilitar o IP para se conectar ao servidor de banco de dados SQL.

    ![Endereços IP permitidos](maintainable-azure-websites-managing-change-and-scale/_static/image29.png "endereços IP permitidos")

    *Endereços IP permitidos*
8. Clique em **salvar** na parte inferior da página para concluir a etapa.
9. Alterne para Visual Studio.
10. No **Package Manager Console**, execute o comando a seguir substituindo *[YOUR-CONNECTION-STRING]* espaço reservado com a cadeia de conexão que você copiou do Azure

    [!code-powershell[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample10.ps1)]

    ![Atualizar banco de dados visando o Windows Azure SQL Database](maintainable-azure-websites-managing-change-and-scale/_static/image30.png "Atualizar banco de dados visando o Windows Azure SQL Database")

    *Atualizar banco de dados visando o banco de dados SQL*

<a id="Ex2Task3"></a>
#### <a name="task-3--deploying-geek-quiz-to-staging-using-git"></a>Tarefa 3 – implantar teste Geek de preparo usando o Git

Nesta tarefa, você habilitará a publicação em etapas em seu aplicativo web. Em seguida, você usará o Git para publicar o aplicativo Geek Quiz diretamente do seu computador local ao ambiente de preparo do aplicativo web.

1. Volte para o portal e clique no nome do aplicativo web sob o **nome** coluna para exibir as páginas de gerenciamento.

    ![Abrir as páginas de gerenciamento de aplicativo web](maintainable-azure-websites-managing-change-and-scale/_static/image31.png)

    *Abrir as páginas de gerenciamento de aplicativo web*
2. Navegue até a **escala** página. Sob o **gerais** seção, selecione **padrão** para a configuração e clique em **salvar** na barra de comandos.

    > [!NOTE]
    > Para executar todos os aplicativos web na região atual e a assinatura no **padrão** modo, deixe o **Selecionar tudo** na caixa de diálogo de **escolha Sites** configuração. Caso contrário, desmarque a **Selecionar tudo** caixa de seleção.

    ![Atualizando o aplicativo web para o modo padrão](maintainable-azure-websites-managing-change-and-scale/_static/image32.png "atualizando o aplicativo web para o modo padrão")

    *Atualizando o aplicativo Web para o modo padrão*
3. Clique em **Sim** para confirmar as alterações.

    ![Confirmando a alteração para o modo padrão](maintainable-azure-websites-managing-change-and-scale/_static/image33.png "continuar com a alteração do modo de aplicativo web")

    *Confirmando a alteração para o modo padrão*
4. Vá para o **Dashboard** página e clique em **habilitar publicação em etapas em** sob o **visão geral rápida** seção.

    ![Habilitar a publicação em etapas](maintainable-azure-websites-managing-change-and-scale/_static/image34.png "habilitando publicação em etapas em")

    *Habilitar a publicação em etapas*
5. Clique em **Sim** para habilitar a publicação em etapas.

    ![Confirmando a publicação em etapas em](maintainable-azure-websites-managing-change-and-scale/_static/image35.png "clicar em Sim para habilitar a publicação em etapas")

    *Confirmando a publicação em etapas*
6. Na lista de aplicativos web, expanda a marca à esquerda do nome do aplicativo web para exibir o slot de sites de preparo. Ele tem o nome do aplicativo web, seguido por ***(preparo)***. Clique no site de preparo para ir para a página de gerenciamento.

    ![Navegar para o aplicativo web de preparo](maintainable-azure-websites-managing-change-and-scale/_static/image36.png "navegando até o aplicativo web de preparo")

    *Navegar para o aplicativo de preparo*
7. Observe que essa página de gerenciamento he se parece com qualquer outra página do aplicativo web gerenciamento. Navegue até a **implantações** página e copie o **URL do Git** valor. Você o usará posteriormente neste exercício.

    ![Copiar o valor da URL do Git](maintainable-azure-websites-managing-change-and-scale/_static/image37.png)

    *Copiar o valor da URL do Git*
8. Abra uma nova **Git Bash** console e execute os comandos a seguir. Atualização a *[seu-aplicativo-PATH]* espaço reservado com o caminho para o **GeekQuiz** solução, localizada no **Source\Ex1 DeployingWebSiteToStaging\Begin** pasta do Neste laboratório.

    [!code-console[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample11.cmd)]

    ![Inicialização de Git e a primeira confirmação](maintainable-azure-websites-managing-change-and-scale/_static/image38.png)

    *Inicialização de Git e a primeira confirmação*
9. Execute o seguinte comando para enviar por push o seu aplicativo web para o computador remoto **Git** repositório. Substitua o espaço reservado com a URL que você obteve no portal de gerenciamento. Você será solicitado para a sua senha de implantação.

    [!code-console[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample12.cmd)]

    ![Enviar por push para o Windows Azure](maintainable-azure-websites-managing-change-and-scale/_static/image39.png)

    *Enviar por push para o Azure*

    > [!NOTE]
    > Quando você implanta o conteúdo para o host do FTP ou repositório GIT de um aplicativo web, você deve autenticar usando o **credenciais de implantação** criado a partir do aplicativo web **início rápido** ou **painel**  páginas de gerenciamento. Se você não souber suas credenciais de implantação você poderá redefini-los usando o portal de gerenciamento com facilidade. Abra o aplicativo web **Dashboard** da página e clique no **redefinir suas credenciais de implantação** link. Forneça uma nova senha e clique em **Okey**. Credenciais de implantação são válidas para uso com todos os aplicativos web associados com sua assinatura.
10. Para verificar se o aplicativo web foi enviado com êxito ao Azure, volte para o portal de gerenciamento e clique em **sites**.
11. Localize seu aplicativo web e expanda a entrada para exibir o slot de sites de preparo. Clique em seu **nome** para ir para a página de gerenciamento.
12. Clique em **implantações** para ver as **histórico de implantação**. Verifique se há um **implantação ativa** com seu  *&quot;confirmação inicial&quot;*.

    ![Implantação ativa](maintainable-azure-websites-managing-change-and-scale/_static/image40.png)

    *Implantação ativa*
13. Por fim, clique em **procurar** na barra de comandos para ir para o aplicativo web.

    ![Procurar o aplicativo web](maintainable-azure-websites-managing-change-and-scale/_static/image41.png)

    *Procurar o aplicativo web*
14. Se o aplicativo é implantado com êxito, você verá a página de logon do teste "geek".

    > [!NOTE]
    > O endereço URL do aplicativo implantado contém o nome do aplicativo web, seguido por *-preparo*.

    ![Aplicativo em execução no ambiente de preparo](maintainable-azure-websites-managing-change-and-scale/_static/image42.png)

    *Aplicativo em execução no ambiente de preparo*
15. Se você quiser explorar o aplicativo, clique em **registrar** para registrar um novo usuário. Conclua os detalhes da conta, inserindo um nome de usuário, endereço de email e senha. Em seguida, o aplicativo mostra a primeira pergunta do teste. Responda a algumas perguntas para garantir que ele está funcionando conforme o esperado.

    ![Aplicativo pronto para ser usado](maintainable-azure-websites-managing-change-and-scale/_static/image43.png)

    *Aplicativo pronto para ser usado*

<a id="Ex2Task4"></a>
#### <a name="task-4--promoting-the-web-app-to-production"></a>Tarefa 4 – elevando o aplicativo Web para produção

Agora que você verificou que o aplicativo web está funcionando corretamente no ambiente de preparo, você está pronto para promovê-lo para produção. Nesta tarefa, você alternará o slot de site de preparo pelo slot de site de produção.

1. Volte para o portal de gerenciamento e selecione o slot de sites de preparo. Clique em **troque** na barra de comandos.

    ![Alterne para a produção](maintainable-azure-websites-managing-change-and-scale/_static/image44.png)

    *Alterne para a produção*
2. Clique em **Sim** na caixa de diálogo de confirmação para continuar com a operação de permuta. Azure imediatamente alternará o conteúdo do site de produção com o conteúdo do site de preparo.

    > [!NOTE]
    > Algumas configurações da versão de preparo serão copiadas automaticamente para a versão de produção (por exemplo, conexão de cadeia de caracteres substituições, mapeamentos de manipulador, etc.), mas outras configurações não serão alterado (por exemplo, pontos de extremidade DNS, as associações SSL, etc.).

    ![Confirmar a operação de permuta](maintainable-azure-websites-managing-change-and-scale/_static/image45.png)

    *Confirmar a operação de permuta*
3. Depois que a troca for concluída, selecione o slot de produção e clique em **procurar** na barra de comandos para abrir o site de produção. Observe a URL na barra de endereços.

    > [!NOTE]
    > Talvez você precise atualizar seu navegador para limpar o cache. No Internet Explorer, você pode fazer isso pressionando **CTRL + R**.

    ![Aplicativo Web em execução no ambiente de produção](maintainable-azure-websites-managing-change-and-scale/_static/image46.png)
4. No **GitBash** de console, atualize a URL remota para o repositório Git local para o slot de produção de destino. Para fazer isso, execute o comando a seguir substituindo os espaços reservados com seu nome de usuário de implantação e o nome do seu aplicativo web.

    > [!NOTE]
    > Os seguintes exercícios, você enviará as alterações para o site de produção em vez de preparo apenas para simplificar o laboratório. Em um cenário do mundo real, é recomendável verificar as alterações no ambiente de preparo antes de promover para produção.

    [!code-console[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample13.cmd)]

<a id="Exercise3"></a>
### <a name="exercise-3-performing-deployment-rollback-in-production"></a>Exercício 3: Executar a reversão da implantação em produção

Há cenários em que você não tem um slot de preparo para executar o intercâmbio entre o preparo e produção, por exemplo, se você estiver trabalhando com **livre** ou **compartilhado** modo. Nesses cenários, você deve testar seu aplicativo em um ambiente de teste – localmente ou em um site remoto – antes da implantação em produção. No entanto, é possível que um problema não detectado durante a fase de teste pode surgir no local de produção. Nesse caso, é importante ter um mecanismo para alternar facilmente para uma versão anterior e mais estável do aplicativo assim que possível.

No **serviço de aplicativo do Azure**, a implantação contínua do controle de origem torna esse possível graças a **reimplantar** ação disponível no portal de gerenciamento. O Azure mantém as implantações associadas as confirmações enviadas por push para o repositório e fornece uma opção para reimplantar o aplicativo usando qualquer uma das suas implantações anteriores, a qualquer momento.

Neste exercício, você executará uma alteração ao código na **teste Geek** aplicativo intencionalmente injeta uma *bug*. Você implantará o aplicativo em produção para ver o erro e, em seguida, você aproveitará o recurso de reimplantação para retornar ao estado anterior.

<a id="Ex3Task1"></a>
#### <a name="task-1--updating-the-geek-quiz-application"></a>Tarefa 1 – Atualizando o aplicativo de teste Geek

Nesta tarefa, você será refatorar um pequeno trecho de código do **TriviaController** classe para extrair a parte da lógica que recupera a opção de teste selecionado do banco de dados em um novo método.

1. Alterne para a instância do Visual Studio com o **GeekQuiz** solução do exercício anterior.
2. Na **Gerenciador de soluções**, abra o **TriviaController.cs** dentro do arquivo a **controladores** pasta.
3. Localize o **StoreAsync** método e selecione o código realçado na figura a seguir.

    ![Selecionando o código](maintainable-azure-websites-managing-change-and-scale/_static/image47.png)

    *Selecionando o código*
4. O código selecionado com o botão direito, expanda o **refatorar** menu e selecione **extrair método...** .

    ![Extrair o código como um novo método](maintainable-azure-websites-managing-change-and-scale/_static/image48.png)

    *Selecionando o método Extract*
5. No **extrair método** caixa de diálogo, o nome do novo método *MatchesOption* e clique em **Okey**.

    ![Especificando o nome do método](maintainable-azure-websites-managing-change-and-scale/_static/image49.png)

    *Especificando o nome para o método extraído*
6. O código selecionado, em seguida, é extraído para o **MatchesOption** método. O código resultante é mostrado no trecho a seguir.

    [!code-csharp[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample14.cs)]
7. Pressione **CTRL + S** para salvar as alterações.

<a id="Ex3Task2"></a>
#### <a name="task-2--redeploying-the-geek-quiz-application"></a>Tarefa 2 – reimplantar o aplicativo de teste Geek

Agora você enviará por push as alterações feitas na tarefa anterior para o repositório, que irá disparar uma nova implantação para o ambiente de produção. Em seguida, você será corrigido um problema usando o **ferramentas de desenvolvimento F12** fornecida pelo Internet Explorer e, em seguida, executar uma reversão para a implantação anterior no portal de gerenciamento do Azure.

1. Abra uma nova **Git Bash** console para implantar o aplicativo atualizado no serviço de aplicativo do Azure.
2. Execute os seguintes comandos para enviar por push as alterações para o Azure. Atualizar o *[seu-aplicativo-PATH]* espaço reservado com o caminho para o **GeekQuiz** solução. Você será solicitado para a sua senha de implantação.

    [!code-console[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample15.cmd)]

    ![Enviando o código refatorado para o Azure](maintainable-azure-websites-managing-change-and-scale/_static/image50.png)

    *Enviando o código refatorado para o Azure*
3. Abra o Internet Explorer e navegue até seu aplicativo web (por exemplo, `http://<your-web-site>.azurewebsites.net`). Faça logon usando as credenciais criadas anteriormente.
4. Pressione **F12** para iniciar as ferramentas de desenvolvimento, selecione a **rede** guia e clique no **reproduzir** botão para iniciar a gravação.

    ![Iniciar gravação de rede](maintainable-azure-websites-managing-change-and-scale/_static/image51.png "Iniciar gravação de rede")

    *Iniciar gravação de rede*
5. Selecione qualquer opção de teste. Você verá que nada acontece.
6. No **F12** janela, a entrada correspondente à solicitação HTTP POST mostra um HTTP **500** resultado.

    ![HTTP 500 Erro](maintainable-azure-websites-managing-change-and-scale/_static/image52.png)

    *HTTP 500 Erro*
7. Selecione o **Console** guia. Um erro será registrado com os detalhes da causa.

    ![Erro em log](maintainable-azure-websites-managing-change-and-scale/_static/image53.png)

    *Erro em log*
8. Localize a parte de detalhes do erro. Claramente, esse erro é causado pelo código de refatoração é confirmada nas etapas anteriores.

    `Details: LINQ to Entities does not recognize the method 'Boolean MatchesOption ...`.
9. Não feche o navegador.
10. Em uma nova instância do navegador, navegue até a [portal de gerenciamento do Azure](https://manage.windowsazure.com) e entre usando a conta Microsoft associada à sua assinatura.
11. Selecione **sites** e clique no aplicativo web que você criou no Exercício 2.
12. Navegue até a **implantações** página. Observe que todas as confirmações executadas são listadas no histórico de implantação.

    ![Lista de implantações existentes](maintainable-azure-websites-managing-change-and-scale/_static/image54.png)

    *Lista de implantações existentes*
13. Selecione a confirmação anterior e clique em **reimplantar** na barra de comandos.

    ![Reimplantar a confirmação anterior](maintainable-azure-websites-managing-change-and-scale/_static/image55.png)

    *Reimplantar a confirmação anterior*
14. Quando solicitado a confirmar, clique em **Sim**.

    ![Confirmando a reimplantação](maintainable-azure-websites-managing-change-and-scale/_static/image56.png)
15. Quando a implantação for concluída, alterne novamente para a instância do navegador com seu aplicativo web e pressione **CTRL + F5**.
16. Clique em qualquer uma das opções. A animação flip agora leva local e o resultado (*corretas/incorretas*) será exibida.
17. (Opcional) Alterne para o **Git Bash** console e execute os comandos a seguir para reverter para a confirmação anterior.

    > [!NOTE]
    > Esses comandos criam uma nova confirmação que desfaz todas as alterações no repositório Git que foram feitas na confirmação incorreta. Azure, em seguida, irá reimplantar o aplicativo usando a nova confirmação.

    [!code-console[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample16.cmd)]

<a id="Exercise4"></a>
### <a name="exercise-4-scaling-using-azure-storage"></a>Exercício 4: Dimensionamento usando o armazenamento do Azure

**BLOBs** são a maneira mais simples de armazenar grandes quantidades de texto não estruturado ou dados binários, como vídeo, áudio e imagens. Mover o conteúdo estático de seu aplicativo para o armazenamento, ajuda a dimensionar seu aplicativo pelo fornecimento de imagens ou documentos diretamente para o navegador.

Neste exercício, você moverá o conteúdo estático de seu aplicativo para um contêiner de Blob. Em seguida, você irá configurar seu aplicativo para adicionar um **regra de reescrita de URL do ASP.NET** na **Web. config** para redirecionar seu conteúdo para o contêiner de Blob.

<a id="Ex4Task1"></a>
#### <a name="task-1--creating-an-azure-storage-account"></a>Tarefa 1 – criar uma conta de armazenamento do Azure

Nesta tarefa, você aprenderá como criar uma nova conta de armazenamento usando o portal de gerenciamento.

1. Navegue até a [portal de gerenciamento do Azure](https://manage.windowsazure.com) e entre usando a conta Microsoft associada à sua assinatura.
2. Selecione **novo | Serviços de dados | Armazenamento | Criação rápida** para começar a criar uma nova conta de armazenamento. Insira um nome exclusivo para a conta e selecione uma **região** na lista. Clique em **criar conta de armazenamento** para continuar.

    ![Criar uma nova conta de armazenamento](maintainable-azure-websites-managing-change-and-scale/_static/image57.png "criando uma nova conta de armazenamento")

    *Criar uma nova conta de armazenamento*
3. No **armazenamento** seção, aguarde até que o status da nova conta de armazenamento seja *Online* para continuar com a etapa a seguir.

    ![Conta de armazenamento criada](maintainable-azure-websites-managing-change-and-scale/_static/image58.png "conta de armazenamento criada")

    *Conta de armazenamento criada*
4. Clique no nome da conta de armazenamento e, em seguida, clique no **Dashboard** link na parte superior da página. O **Dashboard** página fornece informações sobre o status da conta e os pontos de extremidade de serviço que podem ser usados em seus aplicativos.

    ![Exibindo o painel de controle de conta de armazenamento](maintainable-azure-websites-managing-change-and-scale/_static/image59.png "exibindo o painel de controle de conta de armazenamento")

    *Exibindo o painel de controle de conta de armazenamento*
5. Clique o **gerenciar chaves de acesso** botão na barra de navegação.

    ![Botão de gerenciamento de chaves de acesso](maintainable-azure-websites-managing-change-and-scale/_static/image60.png "botão Gerenciar chaves de acesso")

    *Gerenciar o botão chaves de acesso*
6. No **gerenciar chaves de acesso** caixa de diálogo, copie o **nome da conta de armazenamento** e **chave de acesso primária** pois você precisará no exercício a seguir. Em seguida, feche a caixa de diálogo.

    ![Caixa de diálogo Gerenciar chave de acesso](maintainable-azure-websites-managing-change-and-scale/_static/image61.png "caixa de diálogo Gerenciar chave de acesso")

    *Gerenciar a caixa de diálogo chave de acesso*

<a id="Ex4Task2"></a>
#### <a name="task-2--uploading-an-asset-to-azure-blob-storage"></a>Tarefa 2 – carregar um ativo para o armazenamento de BLOBs do Azure

Nesta tarefa, você usará a janela do Gerenciador de servidores do Visual Studio para se conectar à sua conta de armazenamento. Você, em seguida, crie um contêiner de blob e carregar um arquivo com o logotipo do Geek Quiz para o contêiner.

1. Alterne para a instância do Visual Studio com o **GeekQuiz** solução do exercício anterior.
2. Na barra de menus, selecione **modo de exibição** e, em seguida, clique em **Gerenciador de servidores**.
3. Na **Gerenciador de servidores**, clique com botão direito do **Azure** nó e selecione **conectar ao Azure...** . Entre usando a conta Microsoft associada à sua assinatura.

    ![Conectar ao Windows Azure](maintainable-azure-websites-managing-change-and-scale/_static/image62.png)

    *Conectar-se para o Azure*
4. Expanda o **Azure** nó, clique com botão direito **armazenamento** e selecione **anexar armazenamento externo...** .
5. No **adicionar uma nova conta de armazenamento** caixa de diálogo, digite o **nome da conta** e **chave da conta** obtido na tarefa anterior e clique **Okey**.

    ![Adicionar caixa de diálogo nova conta de armazenamento](maintainable-azure-websites-managing-change-and-scale/_static/image63.png)

    *Adicionar caixa de diálogo nova conta de armazenamento*
6. Sua conta de armazenamento deve aparecer sob o **armazenamento** nó. Expanda sua conta de armazenamento, clique com botão direito **Blobs** e selecione **criar contêiner de Blob...** .

    ![Criar contêiner de Blob](maintainable-azure-websites-managing-change-and-scale/_static/image64.png "criar contêiner de Blob")

    *Criar contêiner de Blob*
7. No **criar contêiner de Blob** diálogo caixa, digite um nome para o contêiner de blob e clique em **Okey**.

    ![Caixa de diálogo Criar contêiner de Blob](maintainable-azure-websites-managing-change-and-scale/_static/image65.png "caixa de diálogo Criar contêiner de Blob")

    *Criar caixa de diálogo do contêiner de Blob*
8. O novo contêiner de blob deve ser adicionado para o **Blobs** nó. Altere as permissões de acesso no contêiner para tornar o contêiner público. Para fazer isso, clique com botão direito do **imagens** contêiner e selecione **propriedades**.

    ![Propriedades do contêiner de imagens](maintainable-azure-websites-managing-change-and-scale/_static/image66.png "propriedades do contêiner de imagens")

    *Propriedades do contêiner de imagens*
9. No **propriedades** janela, defina as **acesso de leitura público** para **contêiner**.

    ![Alterando a propriedade de acesso de leitura público](maintainable-azure-websites-managing-change-and-scale/_static/image67.png "alterando a propriedade de acesso de leitura público")

    *Alterando a propriedade de acesso de leitura público*
10. Quando for perguntado se você tiver certeza você deseja alterar a propriedade de acesso público, clique em **Sim**.

    ![Aviso do Microsoft Visual Studio](maintainable-azure-websites-managing-change-and-scale/_static/image68.png "aviso do Microsoft Visual Studio")

    *Aviso do Microsoft Visual Studio*
11. Na **Gerenciador de servidores**, clique com botão direito no **imagens** contêiner de blob e selecione **exibir contêiner de Blob**.

    ![Exibir contêiner de Blob](maintainable-azure-websites-managing-change-and-scale/_static/image69.png "exibir contêiner de Blob")

    *Exibir contêiner de Blob*
12. O contêiner de imagens deve abrir em uma nova janela e uma legenda com entradas não deve ser mostrada. Clique o **carregar** ícone para carregar um arquivo para o contêiner de blob.

    ![O contêiner de imagens sem entradas](maintainable-azure-websites-managing-change-and-scale/_static/image70.png "o contêiner de imagens sem entradas")

    *Contêiner de imagens sem entradas*
13. No **carregar Blob** diálogo caixa, navegue até a **ativos** pasta do laboratório. Selecione o **logotipo big.png** do arquivo e clique em **abrir**.
14. Aguarde até que o arquivo é carregado. Quando o carregamento for concluído, o arquivo deve ser listado no contêiner de imagens. A entrada do arquivo com o botão direito e selecione **Copiar URL**.

    ![Copie a URL do blob](maintainable-azure-websites-managing-change-and-scale/_static/image71.png "copiar a URL do arquivo de blob")

    *Copie a URL do blob*
15. Abra o Internet Explorer e cole a URL. A imagem a seguir deve ser mostrada no navegador.

    ![imagem de logotipo big.png do armazenamento de BLOBs do Windows](maintainable-azure-websites-managing-change-and-scale/_static/image72.png "imagem de logotipo big.png do armazenamento")

    *imagem de logotipo big.png do armazenamento de BLOBs do Azure*

<a id="Ex4Task3"></a>
#### <a name="task-3--updating-the-solution-to-consume-static-content-from-azure-blob-storage"></a>Tarefa 3 – atualizar a solução para consumir o conteúdo estático do armazenamento de BLOBs do Azure

Nesta tarefa, você irá configurar o **GeekQuiz** solução para consumir a imagem carregada para o armazenamento de BLOBs do Azure (em vez da imagem localizada no aplicativo web), adicionando uma regra de reescrita de URL do ASP.NET no **Web. config**arquivo.

1. No Visual Studio, abra o **Web. config** dentro do arquivo a **GeekQuiz** do projeto e localize o **&lt;System. webServer&gt;** elemento.
2. Adicione o código a seguir para adicionar uma reescrita de URL para a regra, atualizando o espaço reservado com o nome da sua conta de armazenamento.

    (Código de trecho de código – *UrlRewriteRule WebSitesInProduction - Ex4 -*)

    [!code-xml[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample17.xml)]

    > [!NOTE]
    > Regravação de URL é o processo de interceptação de uma solicitação da Web de entrada e redirecionando a solicitação para um recurso diferente. A regras de regravação de URL informa ao mecanismo de regravação quando uma solicitação precisa ser redirecionada e onde devem eles ser redirecionados. Uma regra de regravação é composta de duas cadeias de caracteres: o padrão a ser procurado na URL solicitada (geralmente, usando expressões regulares), e a cadeia de caracteres para substituir o padrão de com, se encontrado. Para obter mais informações, consulte [regravação de URL no ASP.NET](https://msdn.microsoft.com/library/ms972974.aspx).
3. Pressione **CTRL + S** para salvar as alterações.
4. Abra uma nova **Git Bash** console para implantar o aplicativo atualizado no serviço de aplicativo do Azure.
5. Execute os seguintes comandos para enviar por push as alterações para o Azure. Atualizar o *[seu-aplicativo-PATH]* espaço reservado com o caminho para o **GeekQuiz** solução. Você será solicitado para a sua senha de implantação.

    [!code-console[Main](maintainable-azure-websites-managing-change-and-scale/samples/sample18.cmd)]

    ![Implantação de atualização para o Azure](maintainable-azure-websites-managing-change-and-scale/_static/image73.png)

    *Implantação de atualização para o Azure*

<a id="Ex4Task4"></a>
#### <a name="task-4--verification"></a>Tarefa 4 – verificação

Essa tarefa, você usará **Internet Explorer** para procurar o aplicativo **Geek Quiz** e verifique que a reescrita de URL para a regra para funciona de imagens e você serão redirecionados para a imagem hospedada no **BLOBs do Azure Armazenamento**.

1. Abra o Internet Explorer e navegue até seu aplicativo web (por exemplo, `http://<your-web-site>.azurewebsites.net`). Faça logon usando as credenciais criadas anteriormente.

    ![Mostrando o aplicativo Geek do teste da web com a imagem](maintainable-azure-websites-managing-change-and-scale/_static/image74.png "mostrando o aplicativo Geek do teste da web com a imagem")

    *Mostrando o aplicativo Geek do teste da web com a imagem*
2. Pressione **F12** para iniciar as ferramentas de desenvolvimento, selecione a **rede** guia e inicie a gravação.

    ![Iniciar gravação de rede](maintainable-azure-websites-managing-change-and-scale/_static/image75.png "Iniciar gravação de rede")

    *Iniciar gravação de rede*
3. Pressione **CTRL + F5** para atualizar a página da web.
4. Quando a página já está carregada, você deverá ver uma solicitação HTTP para o **/img/logo-big.png** URL com um HTTP **301** resultado (redirecionamento) e outra solicitação para `http://[YOUR-STORAGE-ACCOUNT].blob.core.windows.net/images/logo-big.png` URL com um HTTP **200** resultado.

    ![Verificando o redirecionamento de URL](maintainable-azure-websites-managing-change-and-scale/_static/image76.png "mostrando o redirecionamento em ferramentas de desenvolvimento")

    *Verificando o redirecionamento de URL*

<a id="Exercise5"></a>
### <a name="exercise-5-using-autoscale-for-web-apps"></a>Exercício 5: Usar o dimensionamento automático para aplicativos Web

> [!NOTE]
> Este exercício é opcional, porque ele requer suporte para a carga da Web &amp; que está disponível somente para o teste de desempenho **Visual Studio 2013 Ultimate Edition**. Para obter mais informações sobre recursos específicos do Visual Studio 2013, comparar versões [aqui](https://www.microsoft.com/visualstudio/eng/products/compare).


**Aplicativos de Web do serviço de aplicativo do Azure** fornece o recurso de dimensionamento automático para aplicativos web em execução **o modo padrão**. Dimensionamento automático permite que o Azure dimensione automaticamente a contagem de instâncias do aplicativo web, dependendo da carga. Quando o dimensionamento automático está habilitado, o Azure verifica a CPU do seu aplicativo web uma vez a cada cinco minutos e adiciona instâncias conforme necessário nesse ponto no tempo. Se o uso da CPU for baixo, Azure removerá instâncias de uma vez a cada duas horas para garantir que o desempenho do seu aplicativo web não está degradado.

Neste exercício você percorrer as etapas necessárias para configurar o **AutoEscala** de recursos para o aplicativo web **Geek Quiz**. Você vai verificar esse recurso, executando um teste de carga do Visual Studio para gerar carga suficiente da CPU no aplicativo para disparar uma atualização de instância.

<a id="Ex5Task1"></a>
#### <a name="task-1--configuring-autoscale-based-on-the-cpu-metric"></a>Tarefa 1 – Configurar dimensionamento automático com base na métrica de CPU

Nesta tarefa, você usará o portal de gerenciamento do Azure para habilitar o recurso de dimensionamento automático para o aplicativo web criado no Exercício 2.

1. No [portal de gerenciamento do Azure](https://manage.windowsazure.com/), selecione **sites** e clique no aplicativo web que você criou no Exercício 2.
2. Navegue até a **escala** página. Sob o **capacidade** seção, selecione **CPU** para o **escala por métrica** configuração.

    > [!NOTE]
    > Ao dimensionar por CPU, o Azure ajusta dinamicamente o número de instâncias que o aplicativo usa se o uso da CPU for alterado.

    ![Selecionando para dimensionar por CPU](maintainable-azure-websites-managing-change-and-scale/_static/image77.png "selecionando a métrica da CPU para dimensionamento automático")

    *Selecionando para dimensionar por CPU*
3. Alterar o **CPU de destino** configuração de **20**-**40** por cento.

    > [!NOTE]
    > Este intervalo representa o uso médio de CPU para seu aplicativo web. Azure adicionará ou removerá instâncias para manter seu aplicativo web nesse intervalo. O número mínimo e máximo de instâncias usadas para dimensionamento é especificado na **contagem de instâncias** configuração. Azure nunca ficará acima ou além desse limite.
    >
    > O padrão **Target CPU** valores são modificados apenas para os fins deste laboratório. Configurando o intervalo de CPU com valores pequenos, você está aumentando as chances para disparar o dimensionamento automático quando uma carga moderada é colocada no aplicativo.

    ![Alterando o destino de atividade da CPU entre 20 e 40 por cento](maintainable-azure-websites-managing-change-and-scale/_static/image78.png "alterando o destino da CPU para que fique entre 20 e 40 por cento")

    *Alterando a CPU de destino para ser entre 20 e 40 por cento*
4. Clique em **salvar** na barra de comandos para salvar as alterações.

<a id="Ex5Task2"></a>
#### <a name="task-2--load-testing-with-visual-studio"></a>Tarefa 2 – teste de carga com o Visual Studio

Agora que **AutoEscala** tiver sido configurado, você aprenderá a criar um **projeto de teste de carga e desempenho na Web** no Visual Studio para gerar alguma carga de CPU em seu aplicativo web.

1. Abra **Visual Studio Ultimate 2013** e selecione **arquivo | Novo | Projeto...**  para iniciar uma nova solução.

    ![Criar um novo projeto](maintainable-azure-websites-managing-change-and-scale/_static/image79.png "criando um novo projeto")

    *Criar um novo projeto*
2. No **novo projeto** caixa de diálogo, selecione **projeto de teste de carga e desempenho na Web** sob o **Visual c# | Teste** guia. Certifique-se **.NET Framework 4.5** é selecionada, nomeie o projeto *WebAndLoadTestProject*, escolha um **local** e clique em **Okey**.

    ![Criar um novo projeto de teste de carga e da Web](maintainable-azure-websites-managing-change-and-scale/_static/image80.png "criando um novo projeto de teste de carga e Web")

    *Criar um novo projeto de teste de carga e Web*
3. No **WebTest1** com o botão direito do **WebTest1** nó e clique em **Adicionar solicitação**.

    ![Adição de uma solicitação para WebTest1](maintainable-azure-websites-managing-change-and-scale/_static/image81.png "adicionando uma solicitação ao WebTest1")

    *Adição de uma solicitação para WebTest1*
4. No **propriedades** janela do novo nó de solicitação, atualize o **Url** propriedade para apontar para a URL do aplicativo web (por exemplo, *[ http://geek-quiz.azurewebsites.net/ ](http://geek-quiz.azurewebsites.net/)*).

    ![Alterar a propriedade de Url](maintainable-azure-websites-managing-change-and-scale/_static/image82.png "alterando a propriedade de Url")

    *Alterar a propriedade de Url*
5. No **WebTest1** janela, clique com botão direito **WebTest1** e clique em **Loop adicionar...** .

    ![Adicionando um loop a WebTest1](maintainable-azure-websites-managing-change-and-scale/_static/image83.png "adicionando um loop a WebTest1")

    *Adicionando um loop a WebTest1*
6. No **Adicionar regra condicional e itens a Loop** caixa de diálogo, selecione o **Loop For** de regra e modifique as propriedades a seguir.

   1. **Valor de término:** 1000
   2. **Nome do parâmetro de contexto:** iterador
   3. **Valor de incremento:** 1

      ![Selecionando a regra de Loop For e atualização das propriedades](maintainable-azure-websites-managing-change-and-scale/_static/image84.png "selecionando a regra de Loop For e atualização das propriedades")

      *Selecionando a regra de Loop For e atualização das propriedades*
7. Sob o **itens em loop** , selecione a solicitação que você criou anteriormente para ser o primeiro e o último item para o loop. Clique em **OK** para continuar.

    ![Selecionar o primeiro e o último item para o loop](maintainable-azure-websites-managing-change-and-scale/_static/image85.png "selecionando o primeiro e o último item para o loop")

    *Selecionar o primeiro e o último item para o loop*
8. No **Gerenciador de soluções**, clique com botão direito a **WebAndLoadTestProject** do projeto, expanda o **Add** menu e selecione **teste de carga...** .

    ![Adicionando um teste de carga ao projeto WebAndLoadTestProject](maintainable-azure-websites-managing-change-and-scale/_static/image86.png "adicionando um teste de carga ao projeto WebAndLoadTestProject")

    *Adicionando um teste de carga ao projeto WebAndLoadTestProject*
9. No **novo Assistente de teste de carga** caixa de diálogo, clique em **próxima**.

    ![Novo Assistente de teste de carga](maintainable-azure-websites-managing-change-and-scale/_static/image87.png "novo Assistente de teste de carga")

    *Novo Assistente de teste de carga*
10. No **cenário** página, selecione **usar tempos de processamento** e clique em **próxima**.

    ![Selecionar para não usar tempos de processamento](maintainable-azure-websites-managing-change-and-scale/_static/image88.png "selecionando para não usar tempos de processamento")

    *A seleção para não usar tempos de processamento*
11. No **padrão de carga** página, certifique-se de que o **carga constante** opção está selecionada. Alterar o **contagem de usuários** definir como **250** usuários e clique em **próxima**.

    ![Alterando a contagem de usuários para 250](maintainable-azure-websites-managing-change-and-scale/_static/image89.png "alterando a contagem de usuários para 250")

    *Alterando a contagem de usuários para 250*
12. No **testar o modelo de combinação** página, selecione **baseado na ordem sequencial** e clique em **próxima**.

    ![Selecionando o modelo de combinação](maintainable-azure-websites-managing-change-and-scale/_static/image90.png "selecionando o modelo de combinação de teste")

    *Selecionando o modelo de combinação de teste*
13. No **modelo de combinação** , clique em **adicionar...**  para adicionar um teste na combinação.

    ![Adicionando um teste a combinação de testes](maintainable-azure-websites-managing-change-and-scale/_static/image91.png "adicionando um teste a combinação de testes")

    *Adicionando um teste a combinação de testes*
14. No **adicionar testes** caixa de diálogo, clique duas vezes em **WebTest1** para adicionar o teste para o **testes selecionados** lista. Clique em **OK** para continuar.

    ![Adicionando o teste WebTest1](maintainable-azure-websites-managing-change-and-scale/_static/image92.png "adicionando o teste WebTest1")

    *Adicionando o teste WebTest1*
15. Volta a **combinação de testes** , clique em **próxima**.

    ![Concluindo a página de combinação de testes](maintainable-azure-websites-managing-change-and-scale/_static/image93.png "Concluindo a página de combinação de testes")

    *Concluindo a página de combinação de testes*
16. No **combinação de redes** , clique em **próxima**.

    ![Clicar em Avançar na página de combinação de redes](maintainable-azure-websites-managing-change-and-scale/_static/image94.png "clicar em Avançar na página de combinação de redes")

    *Clicar em Avançar na página de combinação de redes*
17. No **combinação de navegadores** página, selecione **Internet Explorer 10.0** como o tipo de navegador e clique em **próxima**.

    ![Selecionar o tipo de navegador](maintainable-azure-websites-managing-change-and-scale/_static/image95.png "selecionando o tipo de navegador")

    *Selecionar o tipo de navegador*
18. No **conjuntos de contadores** , clique em **próxima**.

    ![Clicar em Avançar na página de conjuntos de contadores](maintainable-azure-websites-managing-change-and-scale/_static/image96.png "clicando em Avançar na página de conjuntos de contadores")

    *Clicar em Avançar na página de conjuntos de contadores*
19. No **configurações de execução** , defina as **duração do teste de carga** para **5 minutos** e clique em **concluir**.

    ![Definindo a duração do teste de carga para 5 minutos](maintainable-azure-websites-managing-change-and-scale/_static/image97.png "definindo a duração do teste de carga para 5 minutos")

    *Definindo a duração do teste de carga para 5 minutos*
20. Na **Gerenciador de soluções**, clique duas vezes o **Local.settings** arquivo para explorar as configurações de teste. Por padrão, o Visual Studio usa seu computador local para executar os testes.

    > [!NOTE]
    > Como alternativa, você pode configurar seu projeto de teste para executar os testes de carga na nuvem usando **planos de teste do Azure**. Planos de teste do Azure fornece uma carga baseada em nuvem testes de serviço que simula uma carga mais realista, evitando as restrições do ambiente local, como a capacidade de CPU, memória disponível e largura de banda de rede. Para obter mais informações sobre como usar planos de teste do Azure para executar testes de carga, consulte [cenários de teste de carga](/azure/devops/test/load-test/overview?view=vsts).

    ![Configurações de teste](maintainable-azure-websites-managing-change-and-scale/_static/image98.png)

<a id="Ex5Task3"></a>
#### <a name="task-3--autoscale-verification"></a>Tarefa 3 – verificação de dimensionamento automático

Agora você irá executar o teste de carga que você criou na tarefa anterior e ver como seu aplicativo web se comporta sob carga.

1. Na **Gerenciador de soluções**, clique duas vezes em **LoadTest1.loadtest** para abrir o teste de carga.

    ![Abrindo Loadtest1](maintainable-azure-websites-managing-change-and-scale/_static/image99.png "abrindo Loadtest1")

    *Abertura Loadtest1*
2. No **LoadTest1.loadtest** janela, clique no primeiro botão na caixa de ferramentas para executar o teste de carga.

    ![Executando o teste de carga](maintainable-azure-websites-managing-change-and-scale/_static/image100.png "executando o teste de carga")

    *Executando o teste de carga*
3. Aguarde até que o teste de carga é concluído.

    > [!NOTE]
    > O teste de carga simula vários usuários que enviam solicitações para o aplicativo web ao mesmo tempo. Quando a execução do teste, você pode monitorar os contadores disponíveis para detectar erros, avisos ou outras informações relacionadas a sua execução de teste de carga.

    ![Execução de teste de carga](maintainable-azure-websites-managing-change-and-scale/_static/image101.png "aguardando até que o teste de carga é concluído")

    *Teste de carga em execução*
4. Quando o teste for concluído, volte para o portal de gerenciamento e navegue até a **escala** página do seu aplicativo web. Sob o **capacidade** seção, você deverá ver no gráfico de uma nova instância foi implantada automaticamente.

    ![Nova instância implantada automaticamente](maintainable-azure-websites-managing-change-and-scale/_static/image102.png)

    *Nova instância implantada automaticamente*

    > [!NOTE]
    > Pode levar vários minutos para que as alterações sejam exibidas no gráfico (pressione **CTRL + F5** periodicamente para atualizar a página). Se você não vir todas as alterações, você pode tentar o seguinte:
    >
    > - Aumente a duração do teste de carga (por exemplo, a **10 minutos**)
    > - Reduzir os valores mínimos e máximo do **Target CPU** intervalo na configuração de dimensionamento automático do seu aplicativo web
    > - Executar o teste de carga na nuvem com o **planos de teste do Azure**. Obter mais informações [aqui](/azure/devops/test/load-test/index?view=vsts)

* * *

<a id="Summary"></a>
## <a name="summary"></a>Resumo

Neste laboratório prático, você aprendeu a configurar e implantar seu aplicativo para aplicativos da web de produção no Azure. Iniciado detectando e atualizar seus bancos de dados usando **Entity Framework Code First Migrations**, em seguida, dará continuidade ao implantar novas versões do seu site usando **Git** e execução de reversões para o versão estável mais recente do seu site. Além disso, você aprendeu como dimensionar seu aplicativo usando o armazenamento para mover o conteúdo estático para um contêiner de Blob.
