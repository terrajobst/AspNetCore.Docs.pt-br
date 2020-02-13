---
title: Hospedar e implantar o ASP.NET Core
author: guardrex
description: Aprenda como configurar ambientes de hospedagem e implantar aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/index
ms.openlocfilehash: 173fcfe6d9368a1892d82b32d7b8f5e3cc71fc65
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172364"
---
# <a name="host-and-deploy-aspnet-core"></a>Hospedar e implantar o ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

Em geral, implantar um aplicativo ASP.NET Core em um ambiente de hospedagem:

* Implante o aplicativo publicado em uma pasta no servidor de hospedagem.
* Configure um gerenciador de processo que inicia o aplicativo quando a solicitação chega e reinicia-o depois que ele falha ou que o servidor é reinicializado.
* No caso da configuração de um proxy reverso, defina um proxy reverso para encaminhar solicitações para o aplicativo.

## <a name="publish-to-a-folder"></a>Publicar em uma pasta

O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila o código do aplicativo e copia os arquivos necessários para executar o aplicativo em uma pasta *publish*. Ao implantar do Visual Studio, a etapa `dotnet publish` ocorre automaticamente antes de os arquivos serem copiados para o destino da implantação.

### <a name="folder-contents"></a>Conteúdo da pasta

A pasta *publish* contém um ou mais arquivos do assembly, dependências e, opcionalmente, o runtime do .NET.

Um aplicativo .NET Core pode ser publicado como uma *implantação autocontida* ou uma *implantação dependente de estrutura*. Se o aplicativo for autocontido, os arquivos do assembly que contêm o runtime do .NET serão incluídos na pasta *publish*. Se o aplicativo depender da estrutura, os arquivos de runtime do .NET não serão incluídos porque o aplicativo tem uma referência para uma versão do .NET que está instalada no servidor. O modelo de implantação padrão é dependente da estrutura. Para obter mais informações, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

Além de arquivos *.exe* e *.dll*, a pasta *publish* para um aplicativo ASP.NET Core normalmente contém arquivos de configuração, ativos estáticos e exibições do MVC. Para obter mais informações, consulte <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Configure um gerenciador de processo

Um aplicativo ASP.NET Core é um aplicativo de console que deve ser iniciado quando um servidor é inicializado e reiniciado após falhas. Para automatizar inicializações e reinicializações, um gerenciador de processo é necessário. Os gerenciadores de processo mais comuns para o ASP.NET Core são:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Serviço Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Configurar um proxy reverso

Se o aplicativo usar o servidor Web do servidor [Kestrel](xref:fundamentals/servers/kestrel), você poderá usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) ou [IIS](xref:host-and-deploy/iis/index) como um servidor proxy reverso. Um servidor proxy reverso recebe solicitações HTTP da Internet e encaminha-as para o Kestrel.

Qualquer configuração&mdash;com ou sem um servidor proxy reverso&mdash;é uma configuração de hospedagem compatível. Para obter mais informações, consulte [Quando usar Kestrel com um proxy reverso](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy e balanceadores de carga. Sem configuração adicional, um aplicativo pode não ter acesso ao esquema (HTTP/HTTPS) e ao endereço IP remoto em que uma solicitação foi originada. Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Usar o Visual Studio e o MSBuild para automatizar as implantações

A implantação muitas vezes requer tarefas adicionais além de copiar a saída da [dotnet publish](/dotnet/core/tools/dotnet-publish) para um servidor. Por exemplo, arquivos extras podem ser necessários ou excluídos da pasta *publish*. O MSBuild, que é usado pelo Visual Studio para implantação da Web, pode ser personalizado para fazer muitas outras tarefas durante a implantação. Para saber mais, confira <xref:host-and-deploy/visual-studio-publish-profiles> e o livro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/).

Você pode implantar diretamente do Visual Studio para o Serviço de Aplicativo do Azure usando [o recurso Publicar na Web](xref:tutorials/publish-to-azure-webapp-using-vs) ou usando o [suporte ao Git interno](xref:host-and-deploy/azure-apps/azure-continuous-deployment). O Azure DevOps Services dá suporte à [implantação contínua para o Serviço de Aplicativo do Azure](/azure/devops/pipelines/targets/webapp). Para obter mais informações, confira [DevOps com ASP.NET Core e Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publicar no Azure

Confira <xref:tutorials/publish-to-azure-webapp-using-vs> para obter instruções sobre como publicar um aplicativo no Azure usando o Visual Studio. Um exemplo adicional é fornecido em [Criar um aplicativo Web ASP.NET Core no Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publicar com MSDeploy no Windows

Confira <xref:host-and-deploy/visual-studio-publish-profiles> para obter instruções sobre como publicar um aplicativo com um perfil de publicação do Visual Studio, inclusive de um prompt de comando do Windows, usando o comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).

## <a name="internet-information-services-iis"></a>Serviços de Informações da Internet (IIS)

Para implantações no Serviços de Informações da Internet (IIS) com a configuração fornecida pelo arquivo *Web. config* , consulte os artigos em <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Hospedar em uma web farm

Para obter informações sobre a configuração para hospedar aplicativos do ASP.NET Core em um ambiente de web farm (por exemplo, a implantação de várias instâncias do aplicativo para escalabilidade), veja <xref:host-and-deploy/web-farm>.

## <a name="host-on-docker"></a>Host no Docker

Para obter mais informações, consulte <xref:host-and-deploy/docker/index>.

## <a name="perform-health-checks"></a>Executar verificações de integridade

Use o Middleware de verificação de integridade para executar verificações de integridade em um aplicativo e suas dependências. Para obter mais informações, consulte <xref:host-and-deploy/health-checks>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:test/troubleshoot>
* [Hospedagem ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Em geral, implantar um aplicativo ASP.NET Core em um ambiente de hospedagem:

* Implante o aplicativo publicado em uma pasta no servidor de hospedagem.
* Configure um gerenciador de processo que inicia o aplicativo quando a solicitação chega e reinicia-o depois que ele falha ou que o servidor é reinicializado.
* No caso da configuração de um proxy reverso, defina um proxy reverso para encaminhar solicitações para o aplicativo.

## <a name="publish-to-a-folder"></a>Publicar em uma pasta

O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila o código do aplicativo e copia os arquivos necessários para executar o aplicativo em uma pasta *publish*. Ao implantar do Visual Studio, a etapa `dotnet publish` ocorre automaticamente antes de os arquivos serem copiados para o destino da implantação.

### <a name="folder-contents"></a>Conteúdo da pasta

A pasta *publish* contém um ou mais arquivos do assembly, dependências e, opcionalmente, o runtime do .NET.

Um aplicativo .NET Core pode ser publicado como uma *implantação autocontida* ou uma *implantação dependente de estrutura*. Se o aplicativo for autocontido, os arquivos do assembly que contêm o runtime do .NET serão incluídos na pasta *publish*. Se o aplicativo depender da estrutura, os arquivos de runtime do .NET não serão incluídos porque o aplicativo tem uma referência para uma versão do .NET que está instalada no servidor. O modelo de implantação padrão é dependente da estrutura. Para obter mais informações, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).

Além de arquivos *.exe* e *.dll*, a pasta *publish* para um aplicativo ASP.NET Core normalmente contém arquivos de configuração, ativos estáticos e exibições do MVC. Para obter mais informações, consulte <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Configure um gerenciador de processo

Um aplicativo ASP.NET Core é um aplicativo de console que deve ser iniciado quando um servidor é inicializado e reiniciado após falhas. Para automatizar inicializações e reinicializações, um gerenciador de processo é necessário. Os gerenciadores de processo mais comuns para o ASP.NET Core são:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Serviço Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Configurar um proxy reverso

Se o aplicativo usar o servidor Web do servidor [Kestrel](xref:fundamentals/servers/kestrel), você poderá usar [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) ou [IIS](xref:host-and-deploy/iis/index) como um servidor proxy reverso. Um servidor proxy reverso recebe solicitações HTTP da Internet e encaminha-as para o Kestrel.

Qualquer configuração&mdash;com ou sem um servidor proxy reverso&mdash;é uma configuração de hospedagem compatível. Para obter mais informações, consulte [Quando usar Kestrel com um proxy reverso](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy e balanceadores de carga. Sem configuração adicional, um aplicativo pode não ter acesso ao esquema (HTTP/HTTPS) e ao endereço IP remoto em que uma solicitação foi originada. Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Usar o Visual Studio e o MSBuild para automatizar as implantações

A implantação muitas vezes requer tarefas adicionais além de copiar a saída da [dotnet publish](/dotnet/core/tools/dotnet-publish) para um servidor. Por exemplo, arquivos extras podem ser necessários ou excluídos da pasta *publish*. O MSBuild, que é usado pelo Visual Studio para implantação da Web, pode ser personalizado para fazer muitas outras tarefas durante a implantação. Para saber mais, confira <xref:host-and-deploy/visual-studio-publish-profiles> e o livro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/).

Você pode implantar diretamente do Visual Studio para o Serviço de Aplicativo do Azure usando [o recurso Publicar na Web](xref:tutorials/publish-to-azure-webapp-using-vs) ou usando o [suporte ao Git interno](xref:host-and-deploy/azure-apps/azure-continuous-deployment). O Azure DevOps Services dá suporte à [implantação contínua para o Serviço de Aplicativo do Azure](/azure/devops/pipelines/targets/webapp). Para obter mais informações, confira [DevOps com ASP.NET Core e Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publicar no Azure

Confira <xref:tutorials/publish-to-azure-webapp-using-vs> para obter instruções sobre como publicar um aplicativo no Azure usando o Visual Studio. Um exemplo adicional é fornecido em [Criar um aplicativo Web ASP.NET Core no Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publicar com MSDeploy no Windows

Confira <xref:host-and-deploy/visual-studio-publish-profiles> para obter instruções sobre como publicar um aplicativo com um perfil de publicação do Visual Studio, inclusive de um prompt de comando do Windows, usando o comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).

## <a name="internet-information-services-iis"></a>Serviços de Informações da Internet (IIS)

Para implantações no Serviços de Informações da Internet (IIS) com a configuração fornecida pelo arquivo *Web. config* , consulte os artigos em <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Hospedar em uma web farm

Para obter informações sobre a configuração para hospedar aplicativos do ASP.NET Core em um ambiente de web farm (por exemplo, a implantação de várias instâncias do aplicativo para escalabilidade), veja <xref:host-and-deploy/web-farm>.

## <a name="host-on-docker"></a>Host no Docker

Para obter mais informações, consulte <xref:host-and-deploy/docker/index>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:test/troubleshoot>
* [Hospedagem ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
