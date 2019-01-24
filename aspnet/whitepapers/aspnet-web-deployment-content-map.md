---
uid: whitepapers/aspnet-web-deployment-content-map
title: Recursos recomendados de implantação da Web do ASP.NET – | Microsoft Docs
author: rick-anderson
description: Este tópico fornece links para documentação de recursos sobre como implantar (publicar) ASP.NET web a aplicativos para o IIS usando o Visual Studio 2010, Visual da Web...
ms.author: riande
ms.date: 03/14/2014
ms.assetid: 58b583cd-c4ab-47a3-8527-8c92c298c91f
msc.legacyurl: /whitepapers/aspnet-web-deployment-content-map
msc.type: content
ms.openlocfilehash: 6df0c9d2f38ad1d39abd62787c600ef80da8e8e0
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836215"
---
<a name="aspnet-web-deployment---recommended-resources"></a>Recursos de implantação da Web do ASP.NET - recomendados
====================
> Este tópico fornece links para documentação de recursos sobre como implantar (publicar) ASP.NET web a aplicativos para o IIS usando o Visual Studio 2010, Visual Web Developer 2010 e versões posteriores.
> 
> Se você souber que um blog excelente postagem, [stackoverflow](http://stackoverflow.com) thread ou qualquer outro link que pode ser útil, [envie-em um email](mailto:aspnetue@microsoft.com?subject=Deployment%20Content%20Map) com o link.
> 
> > [!NOTE] 
> > 
> > Muitos desses recursos descrevem os recursos de implantação que estão disponíveis somente se você instalar uma versão recente do [publicar atualização do Visual Studio Web](https://go.microsoft.com/fwlink/?LinkID=208120). Alguns dos recursos estão disponíveis apenas no Visual Studio 2012 ou Visual Studio 2013.


Esse tópico contém as seguintes seções:

- [Noções básicas sobre opções de implantação para projetos web](#understanding)
- [Localizando provedores para um aplicativo ASP.NET de hospedagem](#findinghosting)
- [Implantar um aplicativo web do Visual Studio](#fromvs)
- [Implantar um aplicativo web criando e instalando um pacote de implantação da web](#package)
- [Implantar um aplicativo web usando um processo de CI (integração contínua)](#ci)
- [Usando transformações de Web. config para alterar as configurações no arquivo Web. config de destino ou no arquivo App. config durante a implantação](#transforms)
- [Usando parâmetros de implantação da Web para alterar as configurações do aplicativo web de destino durante a implantação](#webdeployparms)
- [Certificando-se de um aplicativo está offline durante a implantação](#appoffline)
- [Implantando um banco de dados ou alterações a um banco de dados como parte da implantação de aplicativos web](#databasewithweb)
- [Implantando um banco de dados separadamente da implantação de aplicativos web](#databaseseparate)
- [Implantar um aplicativo web que usa o aplicativo ASP.NET de serviços, como associação e a criação de perfil](#aspnetmembership)
- [Pré-compilação para implantação](#precompiling)
- [Implantando um aplicativo web de intranet](#intranet)
- [Automatizando tarefas comuns de implantação que não são automatizadas fora da caixa](#automating)
- [Configurar servidores web para que os desenvolvedores podem implantar aplicativos da web a eles usando a implantação da Web](#configuringservers)
- [Configurando servidores para um provedor de hospedagem](#hostingprovider)
- [Solucionar problemas de implantação](#troubleshooting)
- [Obtendo ajuda com uma pergunta de implantação específica](#gettinghelp)
- [Recursos adicionais](#additional)


<a id="understanding"></a>


## <a name="understanding-deployment-options-for-web-projects"></a>Noções básicas sobre opções de implantação para projetos web

- [Visão geral de implantação de Web para Visual Studio e o ASP.NET](https://msdn.microsoft.com/library/dd394698.aspx) (MSDN).
- [Como implantar um Site da Web do Azure Windows](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy). Explica as opções e links para recursos de implantação de projetos da web para Windows Azure Web Sites, incluindo [entrega contínua](../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery.md) (automatizada de [controle de fonte](../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control.md)), bem como usando o Visual Studio.
- [Melhorias de publicação na Web do Visual Studio 2012](../visual-studio/overview/2012/visual-studio-2012-web-publishing-improvements.md) (vídeo de Scott Hanselman).
- [Visão geral Post para a implantação da Web no VS 2010](http://vishaljoshi.blogspot.com/2009/09/overview-post-for-web-deployment-in-vs.html) (blog de Vishal Joshi). Uma postagem de blog mais antiga, mas alguns dos recursos do Visual Studio 2010, ele vinculará para ter informações ainda são relevantes para o Visual Studio 2012.


<a id="findinghosting"></a>


## <a name="finding-hosting-providers-for-an-aspnet-application"></a>Localizando provedores para um aplicativo ASP.NET de hospedagem

- [Hospedagem do ASP.NET](https://asp.net/hosting)


<a id="fromvs"></a>


## <a name="deploying-a-web-application-from-visual-studio"></a>Implantar um aplicativo web do Visual Studio

- [Como implantar um Site da Web do Azure Windows](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy). Explica as opções e fornece links para recursos para a implantação de projetos da web para o Windows Azure Web Sites. Inclui uma seção sobre a implantação do Visual Studio.
- [Implantação de Web do ASP.NET usando o Visual Studio](../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). série de tutoriais de 12 partes, mostra como implantar aplicativos web com bancos de dados do SQL Server. Para o banco de dados de implantação usa o provedor dbDacFx e o Entity Framework Code First Migrations. Também inclui informações sobre [transformações do arquivo Web. config](../web-forms/overview/deployment/visual-studio-web-deployment/web-config-transformations.md), [Implantando arquivos individuais](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update.md#specificfiles), [implantação de linha de comando](../web-forms/overview/deployment/visual-studio-web-deployment/command-line-deployment.md), e [como Personalizar a Visual Studio web publicar pipeline por meio da edição de arquivos. pubxml](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-extra-files.md). Aplica-se a todos os projetos da web ASP.NET, incluindo o Web Forms, MVC e API da Web.)
- [Como: Implantar um projeto Web usando um único clique publicação no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx) (informações de referência para o Assistente de publicação do Visual Studio Web.)
- [Implantando um aplicativo da Web ASP.NET com o SQL Server Compact usando o Visual Studio](../web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12.md). Isso é uma versão anterior do **implantação de Web do ASP.NET usando o Visual Studio** listadas na parte superior desta seção. Agora, principalmente útil para obter informações sobre como implantar bancos de dados do SQL Server Compact e como migrar do SQL Server Compact para uma edição completa do SQL Server.
- [.NET multicamadas aplicativo usando o armazenamento de tabelas, filas e Blobs](https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36) (site do Microsoft Azure). série de tutoriais de 5 partes, mostra como criar um projeto do MVC e implantá-lo em um serviço de nuvem do Windows Azure.


<a id="package"></a>
## <a name="deploying-a-web-application-by-creating-and-installing-a-web-deployment-package"></a>Implantar um aplicativo web criando e instalando um pacote de implantação da web

- [Como: Criar um pacote de implantação da Web no Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx) (MSDN).
- [Como: Instalar um pacote de implantação usando o arquivo Deploy. cmd criado pelo Visual Studio](https://msdn.microsoft.com/library/ff356104.aspx) (MSDN).
- [Usando um pacote de implantação da Web para implantar no IIS na caixa de desenvolvimento e em um host de terceiros](http://sedodream.com/2011/11/08/UsingAWebDeployPackageToDeployToIISOnTheDevBoxAndToAThirdPartyHost.aspx) (blog do Sayed Hashimi). Como usar o Gerenciador do IIS para instalar um pacote de implantação no IIS no computador local e em uma hospedagem da empresa que dá suporte ao Gerenciador do IIS para administração remota.
- [Criando um Web implantar o pacote do Visual Studio 2010](https://www.iis.net/learn/publish/using-web-deploy/building-a-web-deploy-package-from-visual-studio-2010) (IIS.NET web site). Inclui instruções para instalação e criação de pacote de linha de comando.
- [Uma vez publicar em qualquer lugar do pacote](http://sedodream.com/2012/03/14/PackageWebUpdatedAndVideoBelow.aspx) (blog do Sayed Hashimi). Apresenta um pacote NuGet que automatiza o processo de transformar o arquivo Web. config para vários ambientes de destino, para que você pode implantar um pacote em vários servidores. Consulte também o [PackageWeb vídeo](https://www.youtube.com/watch?v=-LvUJFI8CzM) por Sayed Hashimi.

Consulte também a seção a seguir.


<a id="ci"></a>


## <a name="deploying-a-web-application-using-a-continuous-integration-ci-process"></a>Implantar um aplicativo web usando um processo de CI (integração contínua)

- [Integração contínua e entrega contínua (Criando aplicativos de nuvem do mundo Real com o Windows Azure).](../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery.md) Capítulo de livro eletrônico que apresenta a integração contínua e entrega contínua.
- [Como implantar um Site da Web do Azure Windows](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy). Explica as opções e links para recursos de implantação de projetos da web para o Windows Azure Web Sites. Inclui uma seção sobre como automatizar a implantação do controle de origem.
- [Implantando aplicativos Web em cenários empresariais](../web-forms/overview/deployment/deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios.md). série de tutoriais de partes de 40, mostra como automatizar a implantação em um processo de CI usando o Visual Studio 2010 e o Team Foundation Server 2010.
- [Dentro do mecanismo de compilação da Microsoft: Usando MSBuild e o Team Foundation Build, Sayed Hashimi e William Bartholomew](http://msbuildbook.com). Este é um livro, não é um recurso da web, mas é um guia essencial para aprender a configurar o MSBuild para cenários de integração contínua.
- [Pacote de extensão do MSBuild](https://github.com/mikefourie/MSBuildExtensionPack). Inclui tarefas de implantação.
- [Team Foundation Build Customization Guide](https://aka.ms/vsarsolutions). Documentação por ALM Rangers sobre como configurar o Team Foundation Server abrange a implantação da web e inclui os tutoriais e vídeos.
- [XML SlowCheetah transforma a partir de um servidor de CI](http://sedodream.com/2011/12/12/SlowCheetahXMLTransformsFromACIServer.aspx) (blog do Sayed Hashimi). Explica como usar SlowCheetah suplemento do Visual Studio de um para transformar o App. config e outros arquivos XML.

Consulte também [certificando-se de um aplicativo está offline durante a implantação](aspnet-web-deployment-content-map.md#appoffline) mais adiante nesta página.


<a id="transforms"></a>


## <a name="using-webconfig-transformations-to-change-settings-in-the-destination-webconfig-file-or-appconfig-file-during-deployment"></a>Usando transformações de Web. config para alterar as configurações no arquivo Web. config de destino ou no arquivo App. config durante a implantação

- [As transformações do arquivo Web. config](../web-forms/overview/deployment/visual-studio-web-deployment/web-config-transformations.md).
- [Sintaxe de transformação Web. config para implantação de projeto da Web usando o Visual Studio](https://msdn.microsoft.com/library/dd465326.aspx) (MSDN).
- [Web Tools 2012.2 – transformações de Web. config](https://www.youtube.com/watch?v=HdPK8mxpKEI) (vídeo do YouTube por Sayed Hashimi). Mostra como configurar e visualizar as transformações de Web. config.
- [Como desabilitar a transformação do Web. config?](https://msdn.microsoft.com/library/ee942158.aspx#disable_web_config_transformation) (MSDN).
- [Quando devo usar parâmetros de implantação da Web, em vez de transformações de Web. config?](https://msdn.microsoft.com/library/ee942158.aspx#web_deploy_parameters) (MSDN).
- [XDT (transformação de documento XML) lançada em codeplex.com](https://blogs.msdn.com/b/webdev/archive/2013/04/23/xdt-xml-document-transform-released-on-codeplex-com.aspx) (blog do .NET Web Development and Tools). Anuncia a disponibilidade do código-fonte para o mecanismo de transformação do arquivo Web. config e lista algumas ferramentas que usá-lo.
- [Windows Azure Web Sites: Aplicativo como cadeias de caracteres e trabalho de cadeias de caracteres de Conexão](https://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) (blog do Microsoft Azure). Uma alternativa ao Web. config transforma se seu ambiente de destino for Windows Azure Web Sites e você deseja transformar `appSettings` ou `connectionStrings`.


<a id="webdeployparms"></a>


## <a name="using-web-deploy-parameters-to-change-settings-in-the-destination-web-application-during-deployment"></a>Usando parâmetros de implantação da Web para alterar as configurações do aplicativo web de destino durante a implantação

- [Como: Implantar o Web usar parâmetros em um pacote de implantação da Web](https://msdn.microsoft.com/library/ff398068.aspx) (MSDN).
- [MSDeploy: Como atualizar as configurações de aplicativo em Publicar com base no perfil de publicação](http://sedodream.com/2013/03/02/MSDeployHowToUpdateAppSettingsOnPublishBasedOnThePublishProfile.aspx) (blog do Sayed Hashimi). Mostra como integrar o implantação da Web parâmetros para o Visual Studio perfis de publicação.
- [Web parametrização implantar](https://www.iis.net/learn/publish/using-web-deploy/web-deploy-parameterization) (IIS.NET web site).
- [Web parametrização implantar em ação](http://vishaljoshi.blogspot.com/2010/07/web-deploy-parameterization-in-action.html) (blog de Vishal Joshi).
- [Web vs de parametrização de implantar. Transformação de Web. config](http://vishaljoshi.blogspot.com/2010/06/parameterization-vs-webconfig.html) (blog de Vishal Joshi).
- [Windows Azure Web Sites: Aplicativo como cadeias de caracteres e trabalho de cadeias de caracteres de Conexão](https://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) (blog do Microsoft Azure). Uma alternativa à Web implantar parâmetros se seu ambiente de destino for Windows Azure Web Sites e você deseja parametrizar `appSettings` ou `connectionStrings`.


<a id="appoffline"></a>


## <a name="making-sure-an-application-is-off-line-during-deployment"></a>Certificando-se de um aplicativo está offline durante a implantação

- [Implantação da Web do ASP.NET usando o Visual Studio: Implantando uma atualização de código](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update.md). Consulte a seção **levar o aplicativo offline durante a implantação.**
- [Colocar um aplicativo Offline antes da publicação](https://www.iis.net/learn/publish/deploying-application-packages/taking-an-application-offline-before-publishing) (IIS.net site). Explica a um recurso integrado do Web Deploy 3.0 que automatiza o tratamento de um aplicativo\_offline.htm arquivo. Esse recurso não funciona com o aplicativo personalizado\_offline.htm arquivos.
- [Como aproveitar seu aplicativo web offline durante a publicação](http://sedodream.com/2012/01/08/HowToTakeYourWebAppOfflineDuringPublishing.aspx) (blog do Sayed Hashimi). Como automatizar o processo de usar um aplicativo personalizado\_offline.htm arquivo.
- [Web de publicação de atualizações para o aplicativo offline e usechecksum](https://blogs.msdn.com/b/webdev/archive/2013/10/30/web-publishing-updates-for-app-offline-and-usechecksum.aspx) (blog de desenvolvimento da Web da Microsoft). Outra opção para automatizar o uso do aplicativo\_offline.htm arquivo.
- [Web implantar 3.5 RTW](https://blogs.iis.net/msdeploy/archive/2013/07/09/webdeploy-3-5-rtw.aspx) (IIS.net site). Novo recurso no Web implantar 3.5 para o aplicativo personalizado\_offline.htm arquivos.


<a id="databasewithweb"></a>


## <a name="deploying-a-database-or-changes-to-a-database-as-part-of-web-application-deployment"></a>Implantando um banco de dados ou alterações a um banco de dados como parte da implantação de aplicativos web

- [Configurando a implantação de banco de dados no Visual Studio](https://msdn.microsoft.com/library/dd394698.aspx#dbdeployment) (MSDN). Visão geral das opções para implantar um banco de dados com um projeto web.
- [Implantação de Web do ASP.NET usando o Visual Studio](../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). série de tutoriais de 12 partes, mostra a implantação de banco de dados usando o provedor dbDacFx e Entity Framework Code First Migrations.
- [Como: Implantar uma Web de publicação de projeto usando um único clique no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx) (MSDN).
- [Implantar um aplicativo ASP.NET MVC 5 seguro com associação, OAuth e banco de dados SQL para um Site da Web do Azure Windows](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Um tutorial longo que compila e implanta um aplicativo que usa um único SQL Server do banco de dados para dados de aplicativo e de associação.
- [Implantando um aplicativo da Web ASP.NET com o SQL Server Compact usando o Visual Studio](../web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12.md). série de tutoriais de 12 partes, mostra como implantar bancos de dados do SQL Server Compact e como migrar do SQL Server Compact para uma edição completa do SQL Server.

Consulte também Implantando um aplicativo da web criando e instalando um pacote de implantação da web e implantar um aplicativo web usando um processo de CI (integração contínua), anteriormente nesta página.


<a id="databaseseparate"></a>


## <a name="deploying-a-database-separately-from-web-application-deployment"></a>Implantando um banco de dados separadamente da implantação de aplicativos web

- [SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686(v=vs.103).aspx) (MSDN).
- [Incluir dados em um projeto de banco de dados do SQL Server](https://blogs.msdn.com/b/ssdt/archive/2012/02/02/including-data-in-an-sql-server-database-project.aspx) (blog da equipe do SQL Server Data Tools). Como implantar o esquema e os dados ao implantar um banco de dados.
- [Como implantar um banco de dados para o Windows Azure](https://docs.microsoft.com/azure/sql-database/sql-database-cloud-migrate) (site do Microsoft Azure)
- [Migrando bancos de dados banco de dados de SQL do Windows Azure (anteriormente conhecido como SQL Azure)](https://msdn.microsoft.com/library/windowsazure/ee730904.aspx) (MSDN).
- [Migrando um banco de dados para SQL Azure usando o SSDT](https://blogs.msdn.com/b/ssdt/archive/2012/04/19/migrating-a-database-to-sql-azure-using-ssdt.aspx) (blog da equipe do SQL Server Data Tools).
- [Migrando aplicativos centrados em dados para o Windows Azure](https://msdn.microsoft.com/library/jj156154.aspx) (MSDN).
- [Migrando bancos de dados do SQL Server para Windows Azure SQL Database](https://msdn.microsoft.com/library/windowsazure/jj156160.aspx) (MSDN).


<a id="aspnetmembership"></a>


## <a name="deploying-a-web-application-that-uses-aspnet-application-services-such-as-membership-and-profiling"></a>Implantar um aplicativo web que usa o aplicativo ASP.NET de serviços, como associação e a criação de perfil

- [Implantar um aplicativo ASP.NET MVC 5 seguro com associação, OAuth e banco de dados SQL para um Site da Web do Azure Windows](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Um tutorial longo que compila e implanta um aplicativo que usa um único SQL Server do banco de dados para dados de aplicativo e de associação.
- [O ASP.NET Identity](https://asp.net/identity/). Recursos para a identidade do ASP.NET.
- [Implantação de Web do ASP.NET usando o Visual Studio](../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). série de tutoriais de 12 partes, mostra como implantar um banco de dados de associação do ASP.NET.
- [Configuração de um site que usa os serviços de aplicativo](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/configuring-a-website-that-uses-application-services-cs.md). Para o site da web projetos, mas também é relevante para projetos de aplicativos web.
- [Usuários e funções no site de produção](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/users-and-roles-on-the-production-website-cs.md). Para o site da web projetos, mas também é relevante para projetos de aplicativos web.


<a id="precompiling"></a>


## <a name="precompiling-for-deployment"></a>Pré-compilação para implantação

- [Visão geral do pré-compilação de projeto de aplicativo do ASP.NET Web](https://msdn.microsoft.com/library/aa983464.aspx) (MSDN).
- [Empacotar/publicar na guia Web, propriedades do projeto](https://msdn.microsoft.com/library/dd410108.aspx) (MSDN).
- [Advanced pré-compilar a caixa de diálogo Configurações](https://msdn.microsoft.com/library/hh475319.aspx) (MSDN).


<a id="intranet"></a>


## <a name="deploying-an-intranet-web-application"></a>Implantando um aplicativo web de intranet

- [Use a opção de autenticação organizacional local (ADFS) com o ASP.NET no Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/) (Blog de Vittorio Bertocci.).
- [Como criar um Site da Intranet usando o ASP.NET MVC](https://msdn.microsoft.com/library/gg703322(VS.98).aspx) (MSDN). Gravado mais antigo do passo a passo para o Visual Studio 2010, não reflete as alterações importantes nos modelos de projeto de intranet, introduzidos no Visual Studio 2013.


<a id="automating"></a>


## <a name="automating-common-deployment-tasks-that-are-not-automated-out-of-the-box"></a>Automatizando tarefas comuns de implantação que não são automatizadas fora da caixa

- [Implantação da Web do ASP.NET usando o Visual Studio: Implantando arquivos extras](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-extra-files.md).
- [Definindo permissões de pasta em Publicar Web](http://sedodream.com/2011/11/08/SettingFolderPermissionsOnWebPublish.aspx) (blog do Sayed Hashimi).
- [Como estender o arquivo de destino para incluir configurações de registro para um pacote do projeto da web](https://blogs.msdn.com/webdevtools/archive/2010/02/09/how-to-extend-target-file-to-include-registry-settings-for-web-project-package.aspx) (blog de ferramentas de desenvolvimento da Web).
- [Estendendo a transformação XML (Web. config)](http://sedodream.com/2010/09/09/ExtendingXMLWebconfigConfigTransformation.aspx) (blog do Sayed Hashimi). Mostra como criar transformações personalizadas de XDT.
- [Web Take de provedor personalizado de ferramenta de implantação (MSDeploy) 1](http://sedodream.com/2010/03/11/WebDeploymentToolMSDeployCustomProviderTake1.aspx) (blog do Sayed Hashimi). Mostra como criar um provedor personalizado de implantação da Web.
- [Como empacotar e implantar componentes COM](https://blogs.msdn.com/webdevtools/archive/2010/03/03/how-to-package-and-deploy-com-component.aspx) (blog de ferramentas de desenvolvimento da Web).
- [Como os assemblies do pacote .NET](https://blogs.msdn.com/webdevtools/archive/2010/02/19/how-to-package-com-component.aspx) (blog de ferramentas de desenvolvimento da Web). Como implantar assemblies ao GAC.
- [Script tudo - Initialize sua VM do Windows Azure para seu servidor Web com IIS, implantação da Web e outras coisas](http://www.tugberkugurlu.com/archive/script-out-everything-initialize-your-windows-azure-vm-for-your-web-server-with-iis-web-deploy-and-other-stuff) (blog de Tugberk Ugurlu).


<a id="configuringservers"></a>


## <a name="configuring-web-servers-so-that-developers-can-deploy-web-applications-to-them-using-web-deploy"></a>Configurar servidores web para que os desenvolvedores podem implantar aplicativos da web a eles usando a implantação da Web

- [Instalando e configurando a implantação da Web para o administrador e não-administrador implantações](https://www.iis.net/learn/install/installing-publishing-technologies/installing-and-configuring-web-deploy) (IIS.net site).


<a id="hostingprovider"></a>


## <a name="configuring-servers-for-a-hosting-provider"></a>Configurando servidores para um provedor de hospedagem

- [Guia de implantação de hospedagem do Microsoft ASP.NET 4](https://go.microsoft.com/fwlink/?LinkId=191365) (Centro de Download da Microsoft).
- [Gerar um arquivo XML de perfil](https://www.iis.net/learn/web-hosting/joining-the-web-hosting-gallery/generate-a-profile-xml-file) (IIS.net site).


<a id="troubleshooting"></a>


## <a name="troubleshooting-deployment-problems"></a>Solucionar problemas de implantação

- [Solução de problemas de Windows Azure Web Sites no Visual Studio](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio) (site do Microsoft Azure).
- [Implantação da Web do ASP.NET usando o Visual Studio: Solução de problemas](../web-forms/overview/deployment/visual-studio-web-deployment/troubleshooting.md).
- [Implantar solução de problemas comuns de problemas com Web](https://www.iis.net/learn/publish/troubleshooting-web-deploy/troubleshooting-common-problems-with-web-deploy).
- [Implantar códigos de erro de Web](https://www.iis.net/learn/publish/troubleshooting-web-deploy/web-deploy-error-codes) (IIS.net site).
- [Perguntas Frequentes de implantação para Visual Studio e o ASP.NET Web](https://msdn.microsoft.com/library/ee942158.aspx) (MSDN).
- [Principais diferenças entre o IIS e o ASP.NET Development Server](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md).
- [Diferenças de configuração comuns entre desenvolvimento e produção](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/common-configuration-differences-between-development-and-production-cs.md).
- [Hospedar aplicativos ASP.NET de confiança médio](http://www.4guysfromrolla.com/articles/100307-1.aspx) (4 membros da equipe do site Rolla).


<a id="gettinghelp"></a>


## <a name="getting-help-with-a-specific-deployment-question"></a>Obtendo ajuda com uma pergunta de implantação específica

- [Fórum de implantação e configuração do ASP.NET](https://forums.asp.net/26.aspx/1?Configuration and Deployment).
- [StackOverflow.com](http://www.StackOverflow.com).


<a id="additional"></a>


## <a name="additional-resources"></a>Recursos adicionais

Esta seção fornece links para recursos adicionais que são úteis para aprender mais sobre como usar as ferramentas de implantação do Visual Studio e o IIS.

Os seguintes blogs geralmente contêm informações sobre a implantação de web do Visual Studio:

- [Ferramentas de desenvolvimento Web no blog Microsoft](https://blogs.msdn.com/b/webdevtools/).
- [Blog do sayed Hashimi](http://www.sedodream.com/).

Os seguintes recursos fornecem a documentação sobre a implantação da Web, a estrutura do IIS que usa o Visual Studio para executar tarefas de implantação de projeto de aplicativo web. Você pode fazer perguntas sobre a implantação da Web na [Fórum de ferramenta de implantação Web](https://go.microsoft.com/fwlink/?LinkId=149411) no site da web IIS.net.

- [Introdução ao Web implantar](https://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy).
- [Instalando e configurando Web implantar](https://www.iis.net/learn/install/installing-publishing-technologies/installing-and-configuring-web-deploy).
- [Instalação de implantar Scripts do PowerShell para automatizar Web](https://www.iis.net/learn/publish/using-web-deploy/powershell-scripts-for-automating-web-deploy-setup).
- [Ferramenta de implantação de Web](https://go.microsoft.com/fwlink/?LinkId=151481). Tabela de nível superior do nó de conteúdo para obter a documentação de implantação da Web no site do TechNet. Inclui informações de referência útil, mas com muita frequência da TechNet páginas não foram atualizadas há anos.
- [Namespace Microsoft.Web.Deployment](https://go.microsoft.com/fwlink/?LinkId=148630). Documentação da API, não foi atualizado desde a versão 1.0.
- [O blog da equipe de implantação do Microsoft Web](https://blogs.iis.net/msdeploy/default.aspx).
- [Guia Publicar no site do IIS.net](https://www.iis.net/learn/publish).
