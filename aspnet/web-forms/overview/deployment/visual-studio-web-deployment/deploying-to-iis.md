---
uid: web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
title: 'Implantação da Web do ASP.NET usando o Visual Studio: Implantando para teste | Microsoft Docs'
author: tdykstra
description: Esta série de tutoriais mostra como implantar (publicar) um ASP.NET web de aplicativo para aplicativos de Web do serviço de aplicativo do Azure ou para um provedor de hospedagem de terceiros, usin...
ms.author: riande
ms.date: 01/16/2019
ms.assetid: 8bf2c4fb-4ee5-4841-bfc2-03462c1f7a7a
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
msc.type: authoredcontent
ms.openlocfilehash: d49dfad368ca4b81bb865103a99ec223a1cc66df
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396331"
---
<a name="aspnet-web-deployment-using-visual-studio-deploying-to-test"></a>Implantação da Web do ASP.NET usando o Visual Studio: Implantando para teste
====================
por [Tom Dykstra](https://github.com/tdykstra)

> Esta série de tutoriais mostra como implantar (publicar) um ASP.NET web application para aplicativos de Web do serviço de aplicativo do Azure ou em um provedor de hospedagem de terceiros usando o Visual Studio 2017. Para obter informações sobre a série, consulte [o primeiro tutorial na série](introduction.md).

## <a name="overview"></a>Visão geral

Neste tutorial, você implantará um aplicativo web do ASP.NET para o servidor de informações da Internet (IIS) no computador local.

Geralmente, quando você desenvolve um aplicativo, executá-lo e testá-lo no Visual Studio. Por padrão, os projetos de aplicativos web no Visual Studio 2017 usam o IIS Express como servidor web de desenvolvimento. O IIS Express se comporta como o IIS completo que o Visual Studio Development Server (também conhecido como Cassini), que usa o Visual Studio 2017 por padrão. Mas nenhum servidor web de desenvolvimento funciona exatamente como o IIS. Consequentemente, um aplicativo pode executar e testar corretamente no Visual Studio, mas falham quando ele é implantado no IIS.

Com confiança, você pode testar seu aplicativo de duas maneiras:

1. Implante seu aplicativo no IIS no computador de desenvolvimento usando o mesmo processo que você usará posteriormente para implantá-lo em seu ambiente de produção.

   Você pode configurar o Visual Studio para usar o IIS quando você executar um projeto da web, mas que não seria testar seu processo de implantação. Esse método valida seu processo de implantação e que seu aplicativo seja executado corretamente no IIS.

2. Implante seu aplicativo em um ambiente de teste semelhante ao seu ambiente de produção. 
 
   O ambiente de produção para esses tutoriais é aplicativos Web no serviço de aplicativo do Azure. O ambiente de teste ideal é um aplicativo web adicional criado no serviço do Azure. Embora ele será configurado da mesma forma que um aplicativo web de produção, seria usá-lo somente para teste.

Opção 2 é a maneira mais confiável para testar. Se você usar a opção 2, você não precisa necessariamente usar a opção 1. No entanto se você estiver implantando em um terceiro de hospedagem, opção 2 pode não ser viável ou poderá ser cara, portanto, esta série de tutoriais mostra os dois métodos. Orientação para a opção 2 é fornecida na [implantando no ambiente de produção](deploying-to-production.md) tutorial.

Para obter mais informações sobre como usar os servidores web no Visual Studio, consulte [servidores Web no Visual Studio para projetos Web ASP.NET](https://msdn.microsoft.com/library/58wxa9w5.aspx).

Lembrete: Se você receber uma mensagem de erro ou se algo não funciona ao percorrer o tutorial, certifique-se de verificar a [página de solução de problemas](troubleshooting.md).

## <a name="download-the-contoso-university-starter-project"></a>Baixe o projeto inicial do Contoso University

Baixe e instale a solução do Visual Studio do Contoso University inicial e o projeto. Essa solução contém o tutorial concluído. 

[Baixe o projeto inicial](http://go.microsoft.com/fwlink/p/?LinkId=282627)

## <a name="install-iis"></a>Instalar o IIS

Para implantar em IIS no computador de desenvolvimento, confirme se o IIS e implantação da Web estão instalados. Por padrão, o Visual Studio instala a implantação da Web, mas o IIS não está incluído na configuração padrão Windows 10, Windows 8 ou Windows 7. Se você já tiver instalado o IIS e o pool de aplicativos padrão já está definido para o .NET 4, vá para [a próxima seção](#sqlexpress).

1. É recomendável que você use o [Web Platform Installer (WPI)](https://www.microsoft.com/web/downloads/platform.aspx) para instalar o IIS e implantação da Web. WPI Instala uma configuração recomendada do IIS que inclui os pré-requisitos de implantação da Web e o IIS, se necessário.

   Se você já tiver instalado o IIS, implantação da Web ou qualquer um de seus componentes obrigatórios, o WPI Instala apenas o que está faltando.

   * Use o Web Platform Installer para instalar o IIS e implantação da Web:
    
     ![Instalar o IIS usando WPI](deploying-to-iis/_static/image30.png)

     ![Instale o Web Deploy usando WPI](deploying-to-iis/_static/image31.png)

     Você verá mensagens indicando que o IIS 7 será instalado. O link funciona para o IIS 8 no Windows 8; mas, para o Windows 8 e versões posteriores, percorra as etapas a seguir para certificar-se de que o ASP.NET 4.7 é instalado:

   * Abra **painel de controle** > **programas** > **programas e recursos** > **ou desativar recursos do Windows ativar** .

   * Expandir **serviços de informações da Internet**, **serviços da World Wide Web**, e **recursos de desenvolvimento de aplicativo**.
   
   * Confirme **ASP.NET 4.7** está selecionado.

     ![Selecione ASP.NET 4.7](deploying-to-iis/_static/image1a.png)

   * Confirme **serviços da World Wide Web** e **Console de gerenciamento IIS** está selecionado. Isso instala o IIS e o Gerenciador do IIS.
    
     ![Selecione os serviços da World Wide Web](deploying-to-iis/_static/image24.png)    
  
   * Selecione **OK**. Aparecem mensagens de caixa de diálogo indicando a instalação está ocorrendo.

Depois de instalar o IIS, execute **o Gerenciador do IIS** para certificar-se de que o .NET Framework versão 4 é atribuído ao pool de aplicativos padrão.

1. Pressione WINDOWS + R para abrir o **executar** caixa de diálogo.

   (No Windows 8 ou posterior, digite "executar" o **iniciar** página. No Windows 7, selecione **executados** da **iniciar** menu. Se **executados** não está no **iniciar** menu, barra de tarefas com o botão direito, selecione **propriedades**, selecione o **Menu Iniciar** , selecione **Personalizar**e selecione **executar comando**.)

2. Insira "inetmgr" e selecione **Okey**.

3. No **conexões** painel, expanda o nó do servidor e selecione **Pools de aplicativos**. No **Pools de aplicativos** painel se **DefaultAppPool** é atribuído para o .NET framework versão 4, como mostra a ilustração a seguir, pule para a próxima seção.

   ![Inetmgr_showing_4.0_app_pools](deploying-to-iis/_static/image5a.png)

4. Se você vir apenas dois pools de aplicativos e ambos são definidos para o .NET Framework 2.0, instale o ASP.NET 4 no IIS.

   Para o Windows 8 ou posterior, consulte as instruções da seção anterior para garantir que o ASP.NET 4.7 é instalado, ou consulte [como instalar o ASP.NET 4.5 no Windows 8 e Windows Server 2012](https://support.microsoft.com/kb/2736284). Para o Windows 7, abra uma janela de prompt de comando clicando **Prompt de comando** em que o Windows **inicie** menu e selecionando **executar como administrador**. Execute [aspnet\_regiis.exe](https://msdn.microsoft.com/library/k6h9cz8h.aspx) para instalar o ASP.NET 4 no IIS usando os comandos a seguir. (Em sistemas de 32 bits, substitua "Framework64" com "Estrutura".)

   [!code-console[Main](deploying-to-iis/samples/sample1.cmd)]

   Este comando cria um novo aplicativo que pools para o .NET Framework 4, mas o pool de aplicativos padrão permanecerá definido como 2.0. Você está implantando um aplicativo destinos .NET 4 para esse pool de aplicativos, portanto, altere o pool de aplicativos para o .NET 4.

5. Se você fechou **o Gerenciador do IIS**, execute-o novamente, expanda o nó do servidor e selecione **Pools de aplicativos**.

6. No **Pools de aplicativos** painel, selecione **DefaultAppPool**. No **ações** painel, selecione **configurações básicas**.

   ![Inetmgr_selecting_Basic_Settings_for_app_pool](deploying-to-iis/_static/image25.png)

7. No **Editar Pool de aplicativos** caixa de diálogo, altere o **versão do .NET CLR** para **.NET CLR v4.0.30319**. Selecione **OK**.

   ![Selecting_.NET_4_for_DefaultAppPool](deploying-to-iis/_static/image6a.png)

Agora você está pronto para publicar um aplicativo web no IIS. No entanto, primeiro, crie bancos de dados para teste.

<a id="sqlexpress"></a>

## <a name="install-sql-server-express"></a>Instalar o SQL Server Express

LocalDB não é projetado para funcionar no IIS, para que seu ambiente de teste deve ter o SQL Server Express instalado. Se você estiver usando o Visual Studio 2010 do SQL Server Express, ele já está instalado por padrão. Se você estiver usando o Visual Studio 2012 ou posterior, instale o SQL Server Express.

Para instalar o SQL Server Express, baixe e instale-o em [Centro de Download: Microsoft SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express). 

Na primeira página da Central de instalação do SQL Server, selecione **instalação autônoma do novo SQL Server ou adicionar recursos a uma instalação existente** e siga as instruções, aceitando as opções padrão. No Assistente de instalação, aceite as configurações padrão. Para obter mais informações sobre opções de instalação, consulte [instalar o SQL Server do Assistente de instalação (instalação)](https://msdn.microsoft.com/library/ms143219.aspx).

## <a name="create-sql-server-express-databases-for-the-test-environment"></a>Criar bancos de dados SQL Server Express para o ambiente de teste

O aplicativo Contoso University tem dois bancos de dados: 

1. Banco de dados de associação 
2. banco de dados do aplicativo 

Você pode implantar esses bancos de dados para dois bancos de dados separados ou para um banco de dados. Combiná-los torna junções de banco de dados entre eles mais fácil. 

Se você estiver implantando em um provedor de hospedagem de terceiros, seu plano de hospedagem também pode fornecer um motivo para combiná-los. Por exemplo, o provedor pode cobrar mais por vários bancos de dados ou não pode até permitir mais de um banco de dados.

Neste tutorial, você implantará para dois bancos de dados no ambiente de teste e um banco de dados em ambientes de preparo e produção.

Dos **modo de exibição** menu no Visual Studio, selecione **Gerenciador de servidores** (**Gerenciador de banco de dados** no Visual Web Developer). Clique com botão direito **conexões de dados** e selecione **Create New SQL Server Database**.

![Selecting_Create_New_SQL_Server_Database](deploying-to-iis/_static/image8.png)

No **Create New SQL Server Database** caixa de diálogo, digite ". \SQLExpress" no **nome do servidor** caixa e "aspnet-ContosoUniversity" no **novo nome de banco de dados** caixa. Selecione **OK**.

![Create aspnet-ContosoUniversity](deploying-to-iis/_static/image9.png)

Siga o mesmo procedimento para criar um novo banco de dados School do SQL Server Express denominado `ContosoUniversity`.

**Gerenciador de servidores** mostra dois novos bancos de dados.

![Novos bancos de dados no Gerenciador de servidores](deploying-to-iis/_static/image10.png)

## <a name="create-a-grant-script-for-the-new-databases"></a>Criar um script de concessão para novos bancos de dados

Quando o aplicativo é executado no IIS no computador de desenvolvimento, o aplicativo usa credenciais do pool de aplicativos padrão para acessar o banco de dados. No entanto, por padrão, o pool de aplicativos não tem permissão para abrir os bancos de dados. Isso significa que você precisa executar um script para conceder essa permissão. Nesta seção, você criará esse script e executá-lo mais tarde para certificar-se de que o aplicativo pode abrir os bancos de dados quando ele é executado no IIS.

Em um editor de texto, copie os seguintes comandos SQL em um novo arquivo e salve-o como *Grant.sql*. 

[!code-sql[Main](deploying-to-iis/samples/sample2.sql)]

No Visual Studio, abra a solução Contoso University. A solução (não um dos projetos) e selecione **adicionar**. Selecione **Item existente**, navegue até *Grant.sql*e abri-lo.

> [!NOTE]
> Este script foi desenvolvido para trabalhar com o SQL Server Express 2012 ou posterior e com as configurações do IIS no Windows 10, Windows 8 ou Windows 7, conforme elas são especificadas neste tutorial. Se você estiver usando uma versão diferente do SQL Server ou do Windows, ou se você configurar o IIS no computador de forma diferente, as alterações a esse script podem ser necessárias. Para obter mais informações sobre scripts do SQL Server, consulte [Manuais Online do SQL Server](https://go.microsoft.com/fwlink/?LinkId=132511).

> [!NOTE] 
> **Observação de segurança** este script dá `db_owner` permissões para o usuário que acessa o banco de dados em tempo de execução, o que é o que você terá no ambiente de produção. Em alguns cenários, você talvez queira especificar um usuário que tem o esquema de banco de dados completo permissões apenas para a implantação de atualização e especificar para o tempo de execução de um usuário diferente que tenha permissões apenas para ler e gravar dados. Para obter mais informações, consulte [revisar as alterações da Web. config automática para migrações do Code First](#reviewingmigrations) posteriormente neste tutorial.

<a id="publish"></a>

## <a name="run-the-grant-script-in-the-application-database"></a>Execute o script de concessão no banco de dados de aplicativo

Você pode configurar o perfil de publicação para executar o script de concessão no banco de dados de associação durante a implantação, pois essa implantação de banco de dados usa o [dbDacFx](https://docs.microsoft.com/iis/publish/using-web-deploy/dbdacfx-provider-for-incremental-database-publishing) provedor. Você não pode executar scripts durante a implantação de migrações do Code First, que é como você está implantando o banco de dados do aplicativo. Isso significa que você precisa executar manualmente o script antes da implantação no banco de dados do aplicativo.

1. No Visual Studio, abra o *Grant.sql* arquivo que você criou anteriormente.

2. Selecione **conectar-se**. 

    ![Botão conectar](deploying-to-iis/_static/image11.png)

3. No **conectar ao servidor** caixa de diálogo, digite *. \SQLExpress* como o **nome do servidor**. Selecione **conectar-se**.

4. Na lista suspensa de banco de dados, selecione **ContosoUniversity**. Selecione **executar**. 

   ![](deploying-to-iis/_static/image12.png)

A identidade do pool de aplicativos padrão agora tem permissões suficientes no banco de dados de aplicativo para migrações do Code First criar as tabelas de banco de dados quando o aplicativo é executado.

## <a name="publish-to-iis"></a>Publicar no IIS

Há várias maneiras que você pode implantar em IIS usando o Visual Studio e a implantação da Web:

* Use o Visual Studio, um clique para publicar.
* Publica a partir da linha de comando.
* Criar um pacote de implantação e instalá-lo usando o Gerenciador do IIS. O pacote tem um arquivo. zip com todos os arquivos e metadados necessários para instalar um site no IIS.
* Criar um pacote de implantação e instalá-lo usando a linha de comando.

O processo que você seguiu nos tutoriais anteriores para configurar o Visual Studio para automatizar tarefas de implantação se aplica a todos esses métodos. Esses tutoriais, você usará os primeiros dois métodos. Para obter informações sobre como usar pacotes de implantação, consulte [Implantando um aplicativo da web criando e instalando um pacote de implantação da web](https://go.microsoft.com/fwlink/p/?LinkId=282413#package) no mapa de conteúdo de implantação Web para Visual Studio e do ASP.NET.

Antes de publicar, certifique-se de que você está executando o Visual Studio no modo de administrador. Se você não vir **(administrador)** na barra de título, feche o Visual Studio. No Windows 8 (ou posterior) **inicie** página ou o Windows 7 **inicie** menu, clique com botão direito no ícone do Visual Studio e selecione **executar como administrador**. Modo de administrador só é necessária para a publicação quando você estiver publicando para IIS no computador local.

### <a name="create-the-publish-profile"></a>Criar o perfil de publicação

1. No **Gerenciador de soluções**, clique com botão direito do **ContosoUniversity** projeto (não o **ContosoUniversity.DAL** projeto). Selecione **Publicar**. O **publicar** página será exibida.

2. Selecione **novo perfil**. O **escolher um destino de publicação** caixa de diálogo é exibida.

3. Selecione **IIS, FTP, etc**. Selecione **Criar Perfil**. O **publicar** assistente é exibido.

   ![Guia de perfil do Assistente da Web de publicação](deploying-to-iis/_static/image26.png)

4. Dos **método de publicação** menu suspenso, selecione **implantação da Web**.

5. Para **Server**, insira *localhost*.

6. Para **nome do Site**, insira *Default Web Site/ContosoUniversity*.

7. Para **URL de destino**, insira *http://localhost/ContosoUniversity*.

   O **URL de destino** configuração não é necessária. Quando o Visual Studio terminar de implantar o aplicativo, ele abre automaticamente o navegador padrão para essa URL. Se você não quiser que o navegador para abrir automaticamente após a implantação, deixe essa caixa em branco.

8. Selecione **validar Conexão** para verificar se as configurações estão corretas e se conectar ao IIS no computador local.

   Uma marca de seleção verde verifica se a conexão foi bem-sucedida.

   ![Guia de Conexão do Assistente da Web de publicação](deploying-to-iis/_static/image27.png)

9. Selecione **próxima** para ir para o **configurações** guia.

10. O **configuração** caixa suspensa Especifica a configuração de compilação para implantar. Deixe-a configurada como o valor padrão de **versão**. Você não implantar compilações de depuração neste tutorial.

11. Expandir **opções de publicação do arquivo**. Selecione **excluir arquivos do aplicativo\_pasta de dados**.

    No ambiente de teste, o aplicativo acessa os bancos de dados que você criou no SQL Server Express instância local, não os arquivos. mdf na *App\_dados* pasta.

12. Deixe o **pré-compilar durante a publicação** e **remover arquivos adicionais no destino** caixas de seleção desmarcadas.

    ![Opções de publicação do arquivo na guia Configurações](deploying-to-iis/_static/image15a.png)

    Pré-compilação é uma opção que é útil principalmente para grandes sites. Ela pode reduzir o tempo de inicialização na primeira vez que uma página é solicitada, depois que o site for publicado.

    Você não precisa remover arquivos adicionais, como este é a primeira implantação e não haverá todos os arquivos na pasta de destino ainda.

    > [!NOTE] 
    > Se você selecionar **remover arquivos adicionais no destino** para uma implantação subsequente ao mesmo site, certifique-se de que você use o recurso de visualização para que você veja com antecedência quais arquivos serão excluídos antes de implantar. O comportamento esperado é que a implantação da Web excluirá os arquivos no servidor de destino que você excluiu no seu projeto. No entanto, é comparado com a estrutura de pasta inteira sob as pastas de origem e de destino; e, em alguns cenários de implantação da Web pode excluir arquivos que não deseja excluir.
    > 
    > Por exemplo se você tiver um aplicativo web em uma subpasta no servidor quando você implanta um projeto para a pasta raiz, a subpasta será excluída. Você pode ter um projeto do site principal em contoso.com e o outro projeto para um blog em contoso.com/blog. O aplicativo de blog está em uma subpasta. Se você selecionar **remover arquivos adicionais no destino** quando você implanta o site principal, será possível excluir o aplicativo de blog.
    > 
    > Para obter outro exemplo, seu aplicativo\_pasta de dados pode ser excluída de forma inesperada. Alguns bancos de dados, como o SQL Server Compact armazenam arquivos de banco de dados no aplicativo\_pasta de dados. Após a implantação inicial, você não deseja manter copiando os arquivos de banco de dados em implantações subsequentes, para que você selecionar **excluir aplicativo\_dados** na guia empacotar/Publicar Web. Depois de fazer isso se você tiver **remover arquivos adicionais no destino** selecionado, os arquivos de banco de dados e o aplicativo\_própria pasta de dados será excluída na próxima vez que você publicar.

### <a name="configure-deployment-for-the-membership-database"></a>Configurar a implantação para o banco de dados de associação

As etapas a seguir se aplicam para o **DefaultConnection** banco de dados na caixa de diálogo **bancos de dados** seção.

1. No **cadeia de caracteres de conexão remota** , digite a seguinte cadeia de conexão que aponta para o novo SQL Server Express associação ao banco de dados.

   [!code-console[Main](deploying-to-iis/samples/sample3.cmd)]

   O processo de implantação coloca essa cadeia de caracteres de conexão no arquivo Web. config implantado porque **Use essa cadeia de caracteres de conexão em tempo de execução** está selecionado.

    Você também pode obter a cadeia de conexão do **Gerenciador de servidores**. Na **Gerenciador de servidores**, expanda **conexões de dados** e selecione o  **&lt;machinename&gt;\sqlexpress.aspnet-ContosoUniversity** banco de dados, depois do **propriedades** cópia de janela a **cadeia de caracteres de Conexão** valor. Que a cadeia de caracteres de conexão terá uma configuração adicional que você pode excluir: `Pooling=False`.

2. Selecione **Atualizar banco de dados**.

   Isso faz com que o esquema de banco de dados a ser criado no banco de dados de destino durante a implantação. Nas próximas etapas, você especificar os scripts adicionais que você precisa executar: uma para conceder acesso de banco de dados para o pool de aplicativos padrão e para implantar dados.

3. Selecione **configurar atualizações de banco de dados**.
 
4. No **configurar atualizações de banco de dados** caixa de diálogo, selecione **Adicionar Script do SQL**. Navegue até a *Grant.sql* script que você salvou anteriormente na pasta da solução.

5. Repita o processo para adicionar o *aspnet-data-dev.sql* script.

   ![Configurar atualizações de banco de dados para o banco de dados de associação](deploying-to-iis/_static/image16.png)

6. Selecione **fechar**.

### <a name="configure-deployment-for-the-application-database"></a>Configurar a implantação para o banco de dados do aplicativo

Quando o Visual Studio detecta um Entity Framework `DbContext` classe, ele cria uma entrada na **bancos de dados** seção tem um **executar migrações do Code First** caixa de seleção em vez de um  **Atualizar banco de dados** caixa de seleção. Para este tutorial, você usará essa caixa de seleção para especificar a implantação de migrações do Code First.

Em alguns cenários, talvez você esteja usando um `DbContext` banco de dados, mas você deseja usar o provedor dbDacFx em vez de migrações para implantar o banco de dados. Nesse caso, consulte [como implantar um banco de dados Code First sem migrações?](https://msdn.microsoft.com/library/ee942158.aspx#deploy_code_first_without_migrations) nas perguntas frequentes sobre ASP.NET Web implantação no MSDN.

As etapas a seguir se aplicam para o **SchoolContext** banco de dados na caixa de diálogo **bancos de dados** seção.

1. No **cadeia de caracteres de conexão remota** , digite a seguinte cadeia de conexão que aponta para o novo SQL Server Express aplicativo banco de dados.

   [!code-console[Main](deploying-to-iis/samples/sample4.cmd)]

   O processo de implantação coloca essa cadeia de caracteres de conexão no arquivo Web. config implantado porque **Use essa cadeia de caracteres de conexão em tempo de execução** está selecionado.

   Você também pode obter a cadeia de caracteres de conexão de banco de dados de aplicativo do **Gerenciador de servidores** da mesma forma, você tem a cadeia de caracteres de conexão de banco de dados de associação.

2. Selecione **executar migrações do Code First (executado na inicialização do aplicativo)**.

   Essa opção faz com que o processo de implantação configurar o arquivo Web. config implantado para especificar o `MigrateDatabaseToLatestVersion` inicializador. Esse inicializador atualiza automaticamente o banco de dados para a versão mais recente quando o aplicativo acessa o banco de dados pela primeira vez após a implantação.

### <a name="configure-publish-profile-transforms"></a>Configurar transformações de perfil de publicação

1. Selecione **fechar**. Selecione **Sim** quando for perguntado se você deseja salvar as alterações.

2. Na **Gerenciador de soluções**, expanda **Properties**, expanda **PublishProfiles**.

3. Clique com botão direito *CustomProfile.pubxml* e renomeie- *Test.pubxml*.

4. Clique com botão direito *Test.pubxml*. Selecione **adicionar transformação de Config**.

   ![Adicionar menu de transformação de configuração](deploying-to-iis/_static/image17.png)

   O Visual Studio cria o *Web.Test.config* arquivo de transformação e o abre.

5. No *Web.Test.config* transformar o arquivo, insira o seguinte código imediatamente após a abertura marca de configuração.

   [!code-xml[Main](deploying-to-iis/samples/sample5.xml)]

    Quando você usa o teste de perfil de publicação, essa transformação define o indicador de ambiente para "Test". No site implantado, você verá "(teste)" após o cabeçalho H1 de "Contoso University".

6. Salve e feche o arquivo.

7. Com o botão direito do *Web.Test.config* do arquivo e selecione **visualização transformar** para certificar-se de que a transformação que você codificou produz as alterações esperadas.

    O **visualização da Web. config** janela mostra o resultado da aplicação de ambos os *Release* transforma e o *Web.Test.config* transforma.

### <a name="preview-the-deployment-updates"></a>Visualize as atualizações de implantação

1. Abrir o **publicar na Web** novamente o Assistente de (com o botão direito no projeto ContosoUniversity, selecione **publicar**, em seguida, **visualização**).

2. No **versão prévia** caixa de diálogo, selecione **iniciar visualização** para ver uma lista dos arquivos que serão copiados. 

   ![Visualização de publicação](deploying-to-iis/_static/image29.png)

   Você também pode selecionar o **banco de dados de visualização** link para ver os scripts que serão executados no banco de dados de associação. (Nenhum script é executado para a implantação de migrações do Code First, portanto, não há nada a visualização para o banco de dados do aplicativo.)

3. Selecione **Publicar**.

   Se o Visual Studio não está no modo de administrador, você poderá receber uma mensagem de erro de permissões. Nesse caso, feche o Visual Studio, abra-o no modo de administrador e tente publicar novamente.

   Se o Visual Studio está no modo de administrador, o **saída** janela relatórios bem-sucedida compilar e publicar.

   ![Output_window_publish_Test](deploying-to-iis/_static/image20.png)

   Se você digitou a URL na **URL de destino** caixa no perfil de publicação **Conexão** guia, o navegador abre automaticamente para a página inicial do Contoso University em execução no IIS em seu computador.

## <a name="test-in-the-test-environment"></a>Teste no ambiente de teste

Observe que o indicador de ambiente mostra "(teste)" em vez de "(desenvolvimento)", que mostra que o *Web. config* transformação para o indicador de ambiente foi bem-sucedida.

Execute o **instrutores** página para verificar se que o Code First propagado o banco de dados do instrutor. Quando você seleciona essa página, ele pode levar alguns minutos para carregar porque o Code First cria o banco de dados e, em seguida, executa o `Seed` método. (Ele não fez isso quando você estava na home page, porque o aplicativo não tente acessar o banco de dados ainda.)

Selecione o **alunos** guia para verificar se o banco de dados implantado não tem nenhum aluno.

Selecione **adicionar alunos** da **alunos** menu. Adicionar um aluno e, em seguida, exibir o novo aluno na **alunos** página. Isso confirma que você pode escrever com êxito no banco de dados.

Dos **cursos** menu, selecione **atualização créditos**. O **créditos de atualização** página requer permissões de administrador, portanto, o **Log In** página é exibida. Insira as credenciais de conta de administrador que você criou anteriormente ("admin" e "devpwd"). O **atualização créditos** página é exibida. Isso verifica que a conta de administrador que você criou no tutorial anterior foi implantada corretamente no ambiente de teste.

Verificar se um *ELMAH* pasta existe na *c:\inetpub\wwwroot\ContosoUniversity* pasta com apenas o arquivo de espaço reservado para ele.

<a id="reviewingmigrations"></a>

## <a name="review-the-automatic-webconfig-changes-for-code-first-migrations"></a>Examine as alterações automáticas de Web. config para migrações do Code First

Abra o *Web. config* arquivo no aplicativo implantado na *C:\inetpub\wwwroot\ContosoUniversity* e você pode ver onde o processo de implantação configurado migrações do Code First para automaticamente Atualize o banco de dados para a versão mais recente.

![](deploying-to-iis/_static/image21.png)

O processo de implantação também criou uma nova cadeia de conexão para migrações do Code First exclusivamente atualizar o esquema de banco de dados:

![Cadeia de caracteres de conexão Database_Publish](deploying-to-iis/_static/image22.png)

Essa cadeia de caracteres de conexão adicional permite que você especifique uma conta de usuário para atualizações de esquema de banco de dados e uma conta de usuário diferentes para acesso de dados do aplicativo. Por exemplo, você pode atribuir a **db\_proprietário** função para migrações do Code First e **db\_datareader** com **db\_datawriter**as funções para o aplicativo. Isso é um padrão comum de defesa em profundidade que impede que o código potencialmente mal-intencionado no aplicativo de alterar o esquema de banco de dados. (Por exemplo, isso pode acontecer em um ataque de injeção de SQL.) Esses tutoriais não usam esse padrão. Para implementar esse padrão em seu cenário, siga estas etapas:

1. No **Publicar Web** assistente sob o **configurações** , insira a cadeia de caracteres de conexão que especifica um usuário com permissões de atualização do esquema de banco de dados completo. Desmarque a **Use essa cadeia de caracteres de conexão em tempo de execução** caixa de seleção. No arquivo Web. config implantado, isso se torna o `DatabasePublish` cadeia de caracteres de conexão.

2. Crie uma transformação do arquivo Web. config para a cadeia de caracteres de conexão que você deseja que o aplicativo para usar em tempo de execução.

## <a name="summary"></a>Resumo

Agora você implantou seu aplicativo ao IIS no computador de desenvolvimento e testá-lo lá.

![Home page no teste](deploying-to-iis/_static/image23.png)

Isso verifica se o processo de implantação copiados de conteúdo do aplicativo para o local certo (excluindo os arquivos que você não quiser implantar) e também essa implantação da Web configurado IIS corretamente durante a implantação. No próximo tutorial, você executará mais um teste que localiza uma tarefa de implantação que ainda não foi feita: definindo permissões de pasta na *Elm ah* pasta.

## <a name="more-information"></a>Mais informações

Para obter informações sobre como executar o IIS ou IIS Express no Visual Studio, consulte os seguintes recursos:

- [Visão geral do IIS Express](https://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview) no site IIS.net.
- [Introdução ao IIS Express](https://weblogs.asp.net/scottgu/archive/2010/06/28/introducing-iis-express.aspx) no blog de Guthrie.
- [Web servidores no Visual Studio para projetos Web ASP.NET](https://msdn.microsoft.com/library/58wxa9w5.aspx).
- [Principais diferenças entre o IIS e o ASP.NET Development Server](../../older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md) no site do ASP.NET.

Para obter informações sobre quais problemas podem surgir quando seu aplicativo é executado em confiança média, consulte [hospedando aplicativos do ASP.NET em confiança média](http://www.4guysfromrolla.com/articles/100307-1.aspx) em que a equipe de site Rolla de quatro.

> [!div class="step-by-step"]
> [Anterior](project-properties.md)
> [Próximo](setting-folder-permissions.md)
