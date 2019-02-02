---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
title: Configurando um servidor Web para Web de publicação de implantação (manipulador de implantação da Web) | Microsoft Docs
author: jrjlee
description: Este tópico descreve como configurar um servidor web de serviços de informações da Internet (IIS) para dar suporte à implantação usando Henrique de implantar o IIS da Web e publicação na web...
ms.author: riande
ms.date: 01/29/2017
ms.assetid: 90ebf911-1c46-4470-b876-1335bd0f590f
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
msc.type: authoredcontent
ms.openlocfilehash: cf18a8860d34daa23f61e3dde13c2c79c6c0d4a5
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667317"
---
# <a name="configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler"></a>Configuração de um servidor Web para publicação de Implantação da Web (manipulador de Implantação da Web)

[Baixar PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Este tópico descreve como configurar um servidor web de serviços de informações da Internet (IIS) para dar suporte à implantação usando o manipulador de implantação do IIS da Web e publicação na web.
> 
> Quando você trabalha com o Web Deploy 2.0 ou posterior, há três abordagens principais que você pode usar para obter seus aplicativos ou sites em um servidor web. Você pode:
> 
> - Use o *serviço de agente remoto de implantação da Web*. Essa abordagem requer menos configuração do servidor web, mas você precisa fornecer as credenciais de um administrador de servidor local para implantar qualquer coisa no servidor.
> - Use o *manipulador de implantação da Web*. Essa abordagem é muito mais complexa e exige mais esforço inicial para configurar o servidor web. No entanto, quando você usa essa abordagem, você pode configurar o IIS para permitir que usuários não-administrador executar a implantação. O manipulador de implantação da Web só está disponível no IIS versão 7 ou posterior.
> - Use *implantação offline*. Essa abordagem requer o mínimo de configuração do servidor web, mas um administrador de servidor manualmente deve copiar o pacote da web no servidor e importá-lo por meio do Gerenciador do IIS.
> 
> Para obter mais informações sobre os principais recursos, as vantagens e desvantagens dessas abordagens, consulte [escolhendo a abordagem da direita para a implantação da Web](choosing-the-right-approach-to-web-deployment.md).


Sim, se você quiser permitir que os usuários não-administrador implantar conteúdo em sites específicos do IIS. Essa abordagem geralmente é desejável nesses tipos de cenários:

- Ambientes de preparo ou produção, em que a conta de serviço ou a pessoa que dispara a implantação remota é improvável que têm acesso às credenciais de um administrador de servidor.
- Ambientes hospedados, onde você deseja oferecer aos usuários remotos a capacidade de atualizar seus sites sem lhes conceder controle total de seus servidores web (ou o acesso a sites de outra pessoa).

Em cenários de desenvolvimento ou teste, ou em organizações menores, implantação de conteúdo usando as credenciais de administrador de servidor é geralmente menor contenciosos. Nesses cenários, configurando os servidores de web para dar suporte à implantação usando o [serviço Web de implantação remoto agente](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md) oferece uma abordagem mais simples.

## <a name="task-overview"></a>Visão geral da tarefa

Para configurar o servidor web para aceitar e implantar pacotes de web de um computador remoto usando a abordagem do manipulador de implantação da Web, você precisará:

- Crie ou escolha uma conta de usuário de domínio (o "usuário não administrador") cujas credenciais que você usará para realizar implantações.
- Instale o IIS 7.5, incluindo o serviço de gerenciamento da Web e o módulo de autenticação básica.
- Instale a implantação da Web 2.1 ou posterior.
- Configurar o serviço de gerenciamento da Web para permitir conexões remotas e iniciar o serviço.
- Crie um site do IIS para hospedar o conteúdo implantado.
- Conceda as permissões de usuário não administrador no seu site no Gerenciador do IIS.
- Certifique-se de que o serviço de gerenciamento da Web regras de delegação permitem que o serviço para adicionar e alterar o conteúdo do site usando sua conta de usuário não administrador.
- Configure os firewalls para permitir conexões de entrada na porta 8172.

Para hospedar a solução de exemplo ContactManager especificamente, você também precisará:

- Instale o .NET Framework 4.0.
- Instale o ASP.NET MVC 3.

Este tópico mostra como executar cada um desses procedimentos. As tarefas e instruções passo a passo neste tópico pressupõem que você está começando com uma compilação limpa do servidor executando o Windows Server 2016. Antes de continuar, verifique se:

- Windows Server 2016
- O servidor está ingressado no domínio.
- O servidor tem um endereço IP estático.

> [!NOTE]
> Para obter mais informações sobre como adicionar computadores a um domínio, consulte [ingressando computadores no domínio e fazendo logon](https://technet.microsoft.com/library/cc725618(v=WS.10).aspx). Para obter mais informações sobre como configurar endereços IP estáticos, consulte [configurar um endereço IP estático](https://technet.microsoft.com/library/cc754203(v=ws.10).aspx).


## <a name="install-products-and-components"></a>Instalar produtos e componentes

Esta seção orientará você instalar os componentes e produtos necessários no servidor web. Antes de começar, uma prática recomendada é executar o Windows Update para garantir que seu servidor está completamente atualizado.

Nesse caso, você precisa instalar essas coisas:

- **Configuração recomendada do IIS 7**. Isso permite que o **servidor Web (IIS)** função em seu servidor web e instala o conjunto de módulos do IIS e componentes que você precisa para hospedar um aplicativo ASP.NET.
- **IIS: Serviço de gerenciamento**. Isso instala o serviço de gerenciamento da Web (WMSvc) no IIS. Esse serviço permite o gerenciamento remoto de sites do IIS e expõe o ponto de extremidade do manipulador de implantação da Web aos clientes.
- **IIS: Autenticação básica**. Isso instala o módulo de autenticação básica do IIS. Isso permite que o serviço de gerenciamento da Web (WMSvc) autenticar as credenciais fornecidas.
- **Web Deployment Tool 2.1 ou posterior**. Isso instala a implantação da Web (e seu arquivo executável subjacente, MSDeploy.exe) em seu servidor. Como parte desse processo, ele instala o manipulador de implantação da Web e integra-se com o serviço de gerenciamento da Web.
- **.NET Framework 4.0**. Isso é necessário para executar aplicativos que foram criados com esta versão do .NET Framework.
- **ASP.NET MVC 3**. Isso instala os assemblies que você precisa para executar aplicativos MVC 3.

> [!NOTE]
> Este passo a passo descreve o uso do Web Platform Installer para instalar e configurar vários componentes. Embora você não precisa usar o Web Platform Installer, ele simplifica o processo de instalação automaticamente detectando as dependências e garantindo que você sempre obtenha as versões mais recentes do produto. Para obter mais informações, consulte [Microsoft Web Platform Installer](https://go.microsoft.com/?linkid=9805118).


**Para instalar os componentes e produtos necessários**

1. Baixe e instale o [Web Platform Installer](https://go.microsoft.com/?linkid=9805118).
2. Quando a instalação for concluída, o Web Platform Installer será iniciado automaticamente.

    > [!NOTE]
    > Agora você pode iniciar o Web Platform Installer a qualquer momento do **iniciar** menu. Para fazer isso, nos **inicie** menu, clique em **todos os programas**e, em seguida, clique em **Microsoft Web Platform Installer**.
3. Na parte superior do **Web Platform Installer** janela, clique em **produtos**.
4. No lado esquerdo da janela, no painel de navegação, clique em **estruturas**.
5. No **Microsoft .NET Framework 4** linha, se o .NET Framework já não estiver instalado, clique em **Add**.

    > [!NOTE]
    > Você já pode ter instalado o .NET Framework 4.0 por meio do Windows Update. Se um produto ou componente já estiver instalado, o Web Platform Installer indicará isso substituindo o **Add** botão com o texto **instalado**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image1.png)
6. No **ASP.NET MVC 3 (Visual Studio 2010)** de linhas, clique em **Add**.
7. No painel de navegação, clique em **Server**.
8. No **configuração recomendada do IIS 7** de linhas, clique em **Add**.
9. No **ferramenta de implantação da Web 2.1** de linhas, clique em **Add**.
10. No **IIS: Autenticação básica** de linha, clique em **Add**.
11. No **IIS: Serviço de gerenciamento** de linha, clique em **Add**.
12. Clique em **Instalar**. O Web Platform Installer mostrará uma lista de produtos&#x2014;junto com quaisquer dependências associadas&#x2014;a serem instalados e solicitará que você aceite os termos de licença.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image2.png)
13. Examine os termos de licença e se você concordar com os termos, clique em **aceito**.
14. Quando a instalação for concluída, clique em **terminar**e, em seguida, feche o **Web Platform Installer** janela.

Se você instalou o .NET Framework 4.0 antes de instalar o IIS, você precisará executar o [ferramenta de registro ASP.NET IIS](https://msdn.microsoft.com/library/k6h9cz8h(v=VS.100).aspx) (aspnet\_regiis.exe) para registrar a versão mais recente do ASP.NET com IIS. Se você não fizer isso, você descobrirá que o IIS servirá conteúdo estático (como arquivos HTML) sem problemas, mas ele retornará **404.0 de erro HTTP – não encontrado** quando você tenta navegar para conteúdo ASP.NET. Você pode usar o procedimento a seguir para garantir que o ASP.NET 4.0 está registrado.

**Para registrar o ASP.NET 4.0 com IIS**

1. Clique em **inicie**e, em seguida, digite **Prompt de comando**.
2. Nos resultados da pesquisa, clique com botão direito **Prompt de comando**e, em seguida, clique em **executar como administrador**.
3. Na janela do Prompt de comando, navegue até a **%WINDIR%\Microsoft.NET\Framework\v4.0.30319** directory.
4. Digite o seguinte comando e pressione Enter:

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample1.cmd)]
5. Se você planeja hospedar aplicativos de web de 64 bits em qualquer ponto, você também deve registrar a versão de 64 bits do ASP.NET com o IIS. Para fazer isso, na janela do Prompt de comando, navegue até a **%WINDIR%\Microsoft.NET\Framework64\v4.0.30319** directory.
6. Digite o seguinte comando e pressione Enter:

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample2.cmd)]

Como uma prática recomendada, use Windows Update novamente neste momento para baixar e instalar as atualizações disponíveis para os novos produtos e componentes que você instalou.

## <a name="configure-the-web-management-service"></a>Configurar o serviço de gerenciamento da Web

Agora que você instalou tudo o que você precisa, a próxima etapa é configurar o serviço de gerenciamento da Web no IIS. Em um alto nível, você precisa concluir estas tarefas:

- Habilite a autenticação básica no nível do servidor.
- Configure o serviço de gerenciamento da Web para aceitar conexões remotas.
- Inicie o serviço de gerenciamento da Web.
- Verifique se as regras necessárias de delegação do serviço de gerenciamento da Web estão em vigor.

**Para configurar o serviço de gerenciamento da Web**

1. Sobre o **iniciar** , aponte para **ferramentas administrativas**e, em seguida, clique em **serviços de informações da Internet (IIS) Manager**.
2. No Gerenciador do IIS, nos **conexões** painel, clique no nó de servidor (por exemplo, **STAGEWEB1**).

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image3.png)
3. No painel central, sob **IIS**, clique duas vezes em **autenticação**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image20.png)
4. Clique com botão direito **autenticação básica**e, em seguida, clique em **habilitar**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image5.png)
5. No **conexões** painel, clique no nó de servidor novamente para retornar as configurações de nível superior.
6. No painel central, sob **Management**, clique duas vezes em **serviço de gerenciamento**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image6.png)
7. No painel central, selecione **habilitar conexões remotas**.

    > [!NOTE]
    > Se o serviço de gerenciamento da Web já está em execução, será preciso interrompê-lo pela primeira vez.
8. No **ações** painel, clique em **iniciar** para iniciar o serviço de gerenciamento da Web.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image7.png)
9. Se você for solicitado a salvar suas configurações, clique em **Sim**.

    > [!NOTE]
    > Talvez você queira configurar o serviço para iniciar automaticamente. Para fazer isso, abra o console Serviços, clique com botão direito **serviço de gerenciamento da Web**e, em seguida, clique em **propriedades**. No **tipo de inicialização** lista suspensa, selecione **automáticas**e, em seguida, clique em **Okey**.
10. No **conexões** painel, clique no nó de servidor novamente para retornar as configurações de nível superior.
11. No painel central, sob **Management**, clique duas vezes em **delegação de gerenciamento de serviço**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image8.png)
12. Verifique se o painel central contém um conjunto de regras.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image9.png)

    Essas regras permitem que os usuários autorizados do serviço de gerenciamento da Web usar vários provedores de implantação da Web. Por exemplo, para implantar aplicativos web e o conteúdo ao IIS por meio do manipulador de implantação da Web, deve haver uma regra de delegação que permite que todos os usuários do serviço de gerenciamento da Web para usar autenticados os **contentPath** e **iisApp**  provedores (a última regra que você pode ver na captura de tela).

    Se você instalou a produtos e componentes na ordem descrita neste tópico, a versão mais recente da implantação da Web deve adicionar automaticamente todas as regras de delegação necessária para o serviço de gerenciamento da Web. Se a página de delegação de gerenciamento de serviço não mostrar todas as regras, você precisará criá-los. Para obter instruções sobre como fazer isso, consulte [configurar o manipulador de implantação da Web](https://go.microsoft.com/?linkid=9805124).
13. No **conexões** painel, clique no nó de servidor novamente para retornar as configurações de nível superior.

## <a name="create-and-configure-an-iis-website"></a>Criar e configurar um site do IIS

Antes de implantar conteúdo da web ao seu servidor, você precisa criar e configurar um site do IIS para hospedar o conteúdo. A implantação da Web só pode implantar pacotes da web para um site do IIS existente; ele não é possível criar o site para você. Você também precisará fazer uma pequena configuração extra para permitir que sua conta de administrador implantar conteúdo remotamente. Em um alto nível, você precisa concluir estas tarefas:

- Crie uma pasta no sistema de arquivos para hospedar seu conteúdo.
- Criar um site do IIS para fornecer o conteúdo e associá-lo na pasta local.
- Conceder permissões de leitura para a identidade do pool de aplicativos na pasta local.
- Conceda as permissões necessárias do IIS para a conta de domínio que implantará seu aplicativo web.

Embora não haja nada que impeça você de implantação de conteúdo para o site padrão no IIS, essa abordagem não é recomendada para algo diferente de cenários de teste ou demonstração. Para simular um ambiente de produção, você deve criar um novo site do IIS com as configurações que são específicas para os requisitos do seu aplicativo.

**Para criar um site do IIS**

1. No sistema de arquivos local, crie uma pasta para armazenar seu conteúdo (por exemplo, **C:\DemoSite**).
2. Sobre o **iniciar** , aponte para **ferramentas administrativas**e, em seguida, clique em **serviços de informações da Internet (IIS) Manager**.
3. No Gerenciador do IIS, nos **conexões** painel, expanda o nó do servidor (por exemplo, **STAGEWEB1**).

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image10.png)
4. Clique com botão direito do **Sites** nó e clique **Adicionar Site**.
5. No **nome do Site** , digite um nome para o site do IIS (por exemplo, **DemoSite**).
6. No **caminho físico** caixa, digite (ou procure) o caminho para a pasta local (por exemplo, **C:\DemoSite**).
7. No **porta** , digite o número da porta na qual você deseja hospedar o site da Web (por exemplo, **85**).

    > [!NOTE]
    > Os números de porta padrão são 80 para HTTP e 443 para HTTPS. No entanto, se você hospedar esse site na porta 80, você precisará parar o site padrão antes de poder acessar seu site.
8. Deixe o **nome do Host** caixa em branco, a menos que você deseja configurar um registro do sistema de nome de domínio (DNS) para o site e, em seguida, clique em **Okey**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image11.png)

    > [!NOTE]
    > Em um ambiente de produção, provavelmente você vai querer hospedar seu site da Web na porta 80 e configurar um cabeçalho de host, juntamente com registros DNS correspondentes. Para obter mais informações sobre como configurar cabeçalhos de host no IIS 7, consulte [configurar um cabeçalho de Host para um Site da Web (IIS 7)](https://technet.microsoft.com/library/cc753195(WS.10).aspx). Para obter mais informações sobre a função de servidor DNS no Windows Server, consulte [visão geral do servidor DNS](https://technet.microsoft.com/en-gb/library/cc770392.aspx) e [servidor DNS](https://technet.microsoft.com/windowsserver/dd448607).
9. No painel **Ações** , em **Editar Site**, clique em **Ligações**.
10. No **ligações do Site** caixa de diálogo, clique em **Add**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image12.png)
11. No **adicionar ligação do Site** caixa de diálogo, defina as **endereço IP** e **porta** para corresponder à sua configuração de site existente.
12. No **nome do Host** , digite o nome do seu servidor web (por exemplo, **STAGEWEB1**) e, em seguida, clique em **Okey**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image13.png)

    > [!NOTE]
    > A primeira associação do site permite que você acesse o site localmente usando o endereço IP e a porta ou `http://localhost:85`. A segunda associação do site permite que você acesse o site de outros computadores no domínio usando o nome do computador (por exemplo, http://stageweb1:85).
13. No **ligações do Site** caixa de diálogo, clique em **fechar**.
14. No **conexões** painel, clique em **Pools de aplicativos**.
15. No **Pools de aplicativos** painel, clique no nome do seu pool de aplicativos e, em seguida, clique em **configurações básicas**. Por padrão, o nome do seu pool de aplicativos corresponderá ao nome do seu site (por exemplo, **DemoSite**).
16. No **versão do .NET CLR** lista, selecione **v4.0.30319 .NET CLR**e, em seguida, clique em **Okey**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image21.png)

    > [!NOTE]
    > A solução de exemplo requer o .NET Framework 4.0. Isso não é um requisito para a implantação da Web em geral.

Em ordem para o seu site para atender ao conteúdo, a identidade do pool de aplicativos deve ter permissões de leitura na pasta local que armazena o conteúdo. No IIS 7.5, pools de aplicativos executam com uma identidade de pool de aplicativos exclusivos por padrão (em comparação com versões anteriores do IIS, em que pools de aplicativos normalmente executaria usando a conta de serviço de rede). A identidade do pool de aplicativos não é uma conta de usuário real e não aparecer em qualquer lista de usuários ou grupos&#x2014;em vez disso, ele é criado dinamicamente quando o pool de aplicativos é iniciado. Cada identidade de pool de aplicativos é adicionada ao local **IIS\_IUSRS** grupo de segurança como um item oculto.

Para conceder permissões para uma identidade de pool de aplicativos em um arquivo ou pasta, que você tem duas opções:

- Atribuir permissões para a identidade do pool de aplicativos diretamente, usando o formato <strong>IIS AppPool\</ strong ><em>[nome do pool de aplicativos]</em>(por exemplo, <strong>IIS AppPool\DemoSite</strong>).
- Atribuir permissões para o **IIS\_IUSRS** grupo.

A abordagem mais comum é atribuir permissões ao local **IIS\_IUSRS** agrupar, porque essa abordagem permite que você altere pools de aplicativos sem precisar reconfigurar permissões do sistema de arquivos. O procedimento a seguir usa essa abordagem baseada em grupo.

> [!NOTE]
> Para obter mais informações sobre identidades do pool de aplicativos no IIS 7.5, consulte [identidades do Pool de aplicativos](https://go.microsoft.com/?linkid=9805123).


**Para configurar permissões de pasta para um site do IIS**

1. No Windows Explorer, navegue até o local da pasta local.
2. Clique com botão direito na pasta e, em seguida, clique em **propriedades**.
3. Sobre o **segurança** , clique em **editar**e, em seguida, clique em **adicionar**.
4. Clique em **locais**. No **locais** caixa de diálogo, selecione o servidor local e, em seguida, clique em **Okey**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image15.png)
5. No **selecionar usuários ou grupos** caixa de diálogo, digite **IIS\_IUSRS**, clique em **verificar nomes**e, em seguida, clique em **Okey**.
6. No <strong>permissões para</strong><em>[nome da pasta]</em> caixa de diálogo, observe que o novo grupo foi atribuído a <strong>leitura &amp; executar</strong>, <strong>Listar pasta conteúdo</strong>, e <strong>leitura</strong> permissões por padrão. Deixe isso inalterados e clique em <strong>Okey</strong>.
7. Clique em <strong>Okey</strong> para fechar o <em>[nome da pasta]</em><strong>propriedades</strong> caixa de diálogo.

Como uma tarefa final, você deve conceder as permissões apropriadas para o usuário não administrador cujas credenciais que você usará para implantar o conteúdo. Este usuário exige as permissões para implantar conteúdo remotamente ao seu site.

**Para configurar permissões do site do IIS para um usuário de domínio não-administrador**

1. No Gerenciador do IIS, no **conexões** painel, com o botão direito nó do seu site (por exemplo, **DemoSite**), aponte para **implantar**e, em seguida, clique em **configurar Web Publicação de implantação**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image16.png)
2. No **Configure Web implantar Publishing** caixa de diálogo, à direita do **selecionar um usuário para conceder permissões de publicação** lista, clique no botão de reticências.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image17.png)
3. No **permitir que o usuário** diálogo caixa, digite o nome de usuário e domínio da conta que você deseja usar para implantar o conteúdo e, em seguida, clique em **Okey**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image18.png)
4. No **Configure Web implantar Publishing** caixa de diálogo, clique em **instalação**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image19.png)

    > [!NOTE]
    > Esta operação executa duas funções principais em uma única etapa. Primeiro, ele concede ao usuário permissão para modificar o site remotamente por meio do serviço de gerenciamento da Web, acordo com as regras de delegação examinado na seção anterior. Em segundo lugar, ele concede ao usuário controle total da pasta de origem para o site, o que permite ao usuário adicionar, modificar e definir permissões no conteúdo do site.
5. No **Configure Web implantar Publishing** caixa de diálogo, clique em **fechar**.

## <a name="configure-firewall-exceptions"></a>Configurar exceções de Firewall

Por padrão, o serviço de gerenciamento do IIS Web escuta na porta TCP 8172. Se o Firewall do Windows estiver habilitado no seu servidor web, você precisará criar uma nova regra de entrada para permitir o tráfego TCP na porta 8172 (todo o tráfego de saída é permitido por padrão no Firewall do Windows). Se você usar um firewall de terceiros, você precisará criar regras para permitir o tráfego.

| Direção | De porta | A porta | Tipo de porta |
| --- | --- | --- | --- |
| De entrada | Qualquer | 8172 | TCP |
| Saída | 8172 | Qualquer | TCP |
  

Para obter mais informações sobre como configurar regras no Firewall do Windows, consulte [Configurando regras de Firewall](https://technet.microsoft.com/library/dd448559(WS.10).aspx). Para firewalls de terceiros, consulte a documentação do produto.

## <a name="conclusion"></a>Conclusão

Seu servidor web agora deve estar pronto para aceitar as implantações remotas para o manipulador do Web implantar por meio do serviço de gerenciamento da Web. Antes de tentar implantar um aplicativo web para o servidor, você talvez queira verificar estes pontos-chave:

- Você habilitou a autenticação básica no nível do servidor no IIS?
- Você habilitou a conexões remotas com o serviço de gerenciamento da Web?
- Você iniciou o serviço de gerenciamento da Web?
- Há gerenciamento de regras de serviço de delegação em vigor?
- A identidade do pool de aplicativos tem acesso de leitura à pasta de origem para o seu site?
- A conta de usuário não administrador que tem permissões de nível de site no IIS?
- O firewall permite conexões de entrada para o servidor na porta TCP 8172?

## <a name="further-reading"></a>Leitura adicional

Para obter orientação sobre como configurar arquivos de projeto personalizados do Microsoft Build Engine (MSBuild) para implantar pacotes da web para o manipulador de implantação da Web, consulte [configurando propriedades de implantação para um ambiente de destino](configuring-deployment-properties-for-a-target-environment.md).

> [!div class="step-by-step"]
> [Anterior](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)
> [Próximo](configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md)
