---
title: Publicar um aplicativo ASP.NET Core no IIS
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um servidor IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 4ac7b3a2f738e443263dd888f556e0aff7c8099b
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082370"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Publicar um aplicativo ASP.NET Core no IIS

Por [Luke Latham](https://github.com/guardrex)

Este tutorial mostra como hospedar um aplicativo ASP.NET Core em um servidor IIS.

Este tutorial cobre os seguintes assuntos:

> [!div class="checklist"]
> * Instalar o Pacote de Hospedagem do .NET Core no Windows Server.
> * Criar um site do IIS no Gerenciador do IIS.
> * Implantar um aplicativo ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

* [SDK do .NET Core](/dotnet/core/sdk) instalado no computador de desenvolvimento.
* O Windows Server foi configurado com a função de servidor **Servidor Web (IIS)** . Se o servidor não estiver configurado para hospedar sites com o IIS, siga as orientações na seção *Configuração do IIS* do artigo <xref:host-and-deploy/iis/index#iis-configuration> e, em seguida, retorne a este tutorial.

> [!WARNING]
> **A configuração do IIS e a segurança do site envolvem conceitos que não são cobertos por este tutorial.** Consulte as diretrizes do IIS na [documentação do IIS da Microsoft](https://www.iis.net/) e o [artigo ASP.NET Core sobre como hospedar com o IIS](xref:host-and-deploy/iis/index) antes de hospedar aplicativos de produção no IIS.
>
> Os cenários importantes para a hospedagem do IIS não cobertos por este tutorial incluem:
>
> * [Criação de um hive de Registro para proteção de dados de ASP.NET Core](xref:host-and-deploy/iis/index#data-protection)
> * [Configuração da ACL (lista de controle de acesso) do pool de aplicativos](xref:host-and-deploy/iis/index#application-pool-identity)
> * Para se concentrar nos conceitos de implantação do IIS, este tutorial implanta um aplicativo sem segurança HTTPS configurada no IIS. Para obter mais informações sobre como hospedar um aplicativo habilitado para o protocolo HTTPS, confira os tópicos de segurança na seção [Recursos adicionais](#additional-resources) deste artigo. Mais diretrizes para hospedar aplicativos ASP.NET Core são apresentadas no <xref:host-and-deploy/iis/index> artigo.

## <a name="install-the-net-core-hosting-bundle"></a>Instalar o pacote de hospedagem do .NET Core

Instalar o *Pacote de Hospedagem do .NET Core* no servidor IIS. O pacote instala o Tempo de Execução .NET Core, a Biblioteca do .NET Core e o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module). O módulo permite que aplicativos do ASP.NET Core sejam executados por trás do IIS. Se o sistema não tiver uma conexão com a Internet, obtenha e instale os [Pacotes redistribuíveis do Microsoft Visual C++ 2015](https://www.microsoft.com/download/details.aspx?id=53840) antes de instalar o pacote de hospedagem do .NET Core.

Baixe o instalador usando o seguinte link:

[Instalador de pacote de hospedagem do .NET Core atual (download direto)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Execute o instalador no servidor IIS.

1. Reinicie o servidor ou execute **net stop was /y**, seguido por **net start w3svc** em um shell de comando.

## <a name="create-the-iis-site"></a>Criar o site do IIS

1. No servidor IIS, crie uma pasta para conter arquivos e pastas publicados do aplicativo. Em uma etapa a seguir, o caminho da pasta é fornecido ao IIS como o caminho físico para o aplicativo.

1. No Gerenciador do IIS, abra o nó do servidor no painel **Conexões**. Clique com botão direito do mouse na pasta **Sites**. Selecione **Adicionar Site** no menu contextual.

1. Forneça um **Nome do site** e defina o **Caminho físico** como a pasta de implantação do aplicativo que você criou. Forneça a configuração **Associação** e crie o site ao selecionar **OK**.

## <a name="create-an-aspnet-core-razor-pages-app"></a>Criar um aplicativo Razor Pages do ASP.NET Core

Siga o tutorial <xref:getting-started> para criar um aplicativo Razor Pages.

## <a name="publish-and-deploy-the-app"></a>Publicar e implantar o aplicativo

*Publicar um aplicativo* significa produzir um aplicativo compilado que pode ser hospedado por um servidor. *Implantar um aplicativo* significa mover o aplicativo publicado para um sistema de hospedagem. A etapa de publicação é tratada pelo [SDK do .NET Core](/dotnet/core/sdk), enquanto a etapa de implantação pode ser tratada por diversas abordagens. Este tutorial adota a abordagem de implantação de *pasta*, em que:

* O aplicativo é publicado em uma pasta.
* O conteúdo da pasta é movido para a pasta do site do IIS (o **caminho físico** para o site no Gerenciador do IIS).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções**, e selecione **Publicar**.
1. Na caixa de diálogo **Escolher um destino de publicação**, selecione a opção de publicação de **Pasta**.
1. Defina o caminho **Pasta ou Compartilhamento de arquivo**.
   * Se você criou uma pasta para o site do IIS que está disponível no computador de desenvolvimento como um compartilhamento de rede, forneça o caminho para o compartilhamento. O usuário atual deve ter acesso de gravação para publicar no compartilhamento.
   * Se não for possível implantar diretamente na pasta do site do IIS no servidor IIS, publique em uma pasta na mídia removida e mova fisicamente o aplicativo publicado para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no IIS Manager. Mova o conteúdo da pasta *bin/Release/{TARGET FRAMEWORK}/publish* para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no Gerenciador do IIS.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

1. Em um shell de comando, publique o aplicativo na Configuração de versão usando o comando [dotnet publish](/dotnet/core/tools/dotnet-publish):

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Mova o conteúdo da pasta *bin/Release/{TARGET FRAMEWORK}/publish* para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no Gerenciador do IIS.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Clique com o botão direito do mouse no projeto em **Solução** e selecione **Publicar** > **Publicar na Pasta**.
1. Defina o caminho **Escolher uma pasta**.
   * Se você criou uma pasta para o site do IIS que está disponível no computador de desenvolvimento como um compartilhamento de rede, forneça o caminho para o compartilhamento. O usuário atual deve ter acesso de gravação para publicar no compartilhamento.
   * Se não for possível implantar diretamente na pasta do site do IIS no servidor IIS, publique em uma pasta na mídia removida e mova fisicamente o aplicativo publicado para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no IIS Manager. Mova o conteúdo da pasta *bin/Release/{TARGET FRAMEWORK}/publish* para a pasta do site do IIS no servidor, que é o **Caminho físico** do site no Gerenciador do IIS.

---

## <a name="browse-the-website"></a>Navegar no site

O aplicativo pode ser acessado em um navegador depois de receber a primeira solicitação. Faça uma solicitação para o aplicativo na associação de ponto de extremidade que você estabeleceu no Gerenciador do IIS para o site.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Instalar o Pacote de Hospedagem do .NET Core no Windows Server.
> * Criar um site do IIS no Gerenciador do IIS.
> * Implantar um aplicativo ASP.NET Core.

Para saber mais sobre como hospedar aplicativos ASP.NET Core no IIS, confira o artigo Visão Geral do IIS:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Recursos adicionais

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Artigos no conjunto de documentação do ASP.NET Core

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Artigos referentes à implantação do aplicativo ASP.NET Core

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Publicar um aplicativo Web em uma pasta usando o Visual Studio para Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Artigos sobre a configuração HTTPS do IIS

* [Como configurar SSL no Gerenciador do IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Como configurar o SSL no IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Artigos sobre IIS e Windows Server

* [O site oficial da IIS da Microsoft](https://www.iis.net/)
* [Biblioteca de conteúdo técnico do Windows Server](/windows-server/windows-server)
