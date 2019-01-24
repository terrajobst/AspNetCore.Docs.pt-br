---
uid: signalr/overview/older-versions/scaleout-with-sql-server
title: Expansão do SignalR com o SQL Server (SignalR 1.x) | Microsoft Docs
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 1dca7967-8296-444a-9533-837eb284e78c
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 7fd05a4487be4cc96fa945cf08226841e3f01806
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835929"
---
<a name="signalr-scaleout-with-sql-server-signalr-1x"></a>Expansão do SignalR com o SQL Server (SignalR 1.x)
====================
por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

Neste tutorial, você usará o SQL Server para distribuir mensagens através de um aplicativo de SignalR é implantado em duas instâncias separadas do IIS. Você também pode executar este tutorial em um computador de teste único, mas para obter o efeito completo, você precisará implantar o aplicativo do SignalR em dois ou mais servidores. Você também deve instalar o SQL Server em um dos servidores, ou em um servidor dedicado separado. Outra opção é executar o tutorial usando as máquinas virtuais no Azure.

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a>Pré-requisitos

Microsoft SQL Server 2005 ou posterior. Backplane dá suporte a edições de área de trabalho e o servidor do SQL Server. Ele não oferece suporte a banco de dados do Azure ou SQL Server Compact Edition. (Se seu aplicativo estiver hospedado no Azure, considere o backplane do barramento de serviço em vez disso.)

## <a name="overview"></a>Visão geral

Antes de passarmos para o tutorial detalhado, aqui está uma visão rápida do que você deve fazer.

1. Crie um novo banco de dados vazio. Backplane criará as tabelas necessárias neste banco de dados.
2. Adicione esses pacotes do NuGet ao seu aplicativo: 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.SqlServer](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. Crie um aplicativo do SignalR.
4. Adicione o seguinte código ao global. asax para configurar o backplane: 

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

## <a name="configure-the-database"></a>Configurar o banco de dados

Decida se o aplicativo usará a autenticação do Windows ou autenticação do SQL Server para acessar o banco de dados. Independentemente disso, certifique-se de que o usuário de banco de dados tem permissões para fazer logon, criar esquemas e criar tabelas.

Crie um novo banco de dados para o backplane usar. Você pode atribuir qualquer nome para o banco de dados. Você não precisa criar todas as tabelas no banco de dados; backplane criará as tabelas necessárias.

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a>Habilite o Service Broker

É recomendável habilitar o Service Broker para o banco de dados do backplane. O Service Broker fornece suporte nativo para mensagens e enfileiramento no SQL Server, que permite que o backplane receber atualizações com mais eficiência. (No entanto, o backplane também funciona sem o Service Broker.)

Para verificar se o Service Broker está habilitado, consulte o **está\_broker\_habilitada** coluna no **sys. Databases** exibição do catálogo.

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

Para habilitar o Service Broker, use a seguinte consulta SQL:

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> Se essa consulta é exibido um deadlock, certifique-se não há nenhum aplicativo conectado ao banco de dados.

Se você tiver habilitado o rastreamento, os rastreamentos também mostrará se o Service Broker está habilitado.

## <a name="create-a-signalr-application"></a>Criar um aplicativo de SignalR

Crie um aplicativo do SignalR, seguindo um destes tutoriais:

- [Introdução ao SignalR](../getting-started/tutorial-getting-started-with-signalr.md)
- [Introdução ao SignalR e MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md)

Em seguida, modificaremos o aplicativo de bate-papo para dar suporte à expansão com o SQL Server. Primeiro, adicione o pacote do SignalR.SqlServer NuGet ao seu projeto. No Visual Studio, do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**, em seguida, selecione **Package Manager Console**. Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

Em seguida, abra o arquivo global asax. Adicione o seguinte código para o **Application\_iniciar** método:

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a>Implantar e executar o aplicativo

Prepare suas instâncias do Windows Server para implantar o aplicativo do SignalR.

Adicione a função do IIS. Inclua recursos de "Desenvolvimento de aplicativos", incluindo o protocolo WebSocket.

![](scaleout-with-sql-server/_static/image4.png)

Também incluem o serviço de gerenciamento (listados em "Ferramentas de gerenciamento").

![](scaleout-with-sql-server/_static/image5.png)

**Install Web implantação 3.0.** Quando você executar o Gerenciador do IIS, ele solicitará que você instale o Microsoft Web Platform, ou pode [baixar o intstaller](https://go.microsoft.com/fwlink/?LinkId=255386). No instalador da plataforma, procure implantação da Web e instale o Web Deploy 3.0

![](scaleout-with-sql-server/_static/image6.png)

Verifique se o serviço de gerenciamento da Web está em execução. Caso contrário, inicie o serviço. (Se você não vir o serviço de gerenciamento da Web na lista de serviços do Windows, certifique-se de que você instalou o serviço de gerenciamento quando você adicionou a função do IIS.)

Por fim, abra a porta 8172 para TCP. Essa é a porta que usa a ferramenta de implantação da Web.

Agora você está pronto para implantar o projeto do Visual Studio em seu computador de desenvolvimento para o servidor. No Gerenciador de soluções, clique com botão direito a solução e clique em **publicar**.

Para obter mais documentação sobre a implantação da web, consulte [mapa de conteúdo de implantação da Web para Visual Studio e o ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).

Se você implantar o aplicativo em dois servidores, você pode abrir cada instância em uma janela separada do navegador e ver que cada um receba mensagens do SignalR de outra. (É claro que, em um ambiente de produção, os dois servidores estaria atrás de um balanceador de carga.)

![](scaleout-with-sql-server/_static/image7.png)

Depois de executar o aplicativo, você pode ver que o SignalR cria automaticamente tabelas no banco de dados:

![](scaleout-with-sql-server/_static/image8.png)

O SignalR gerencia as tabelas. Desde que o aplicativo for implantado, não excluir linhas, modifique a tabela e assim por diante.
