---
uid: signalr/overview/older-versions/scaleout-with-redis
title: Expansão do SignalR com Redis (SignalR 1.x) | Microsoft Docs
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 6abecf80-8ffa-41ba-b0d9-1d9edbe7687b
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-redis
msc.type: authoredcontent
ms.openlocfilehash: 74294bd04d5649f2ec54e58adb744f5e30525162
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836409"
---
<a name="signalr-scaleout-with-redis-signalr-1x"></a>Expansão do SignalR com Redis (SignalR 1.x)
====================
por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

Neste tutorial, você usará [Redis](http://redis.io/) para distribuir mensagens através de um aplicativo de SignalR é implantado em duas instâncias separadas do IIS.

O redis é um repositório de chave-valor na memória. Ele também dá suporte a um sistema de mensagens com um modelo de publicação/assinatura. O backplane SignalR Redis usa o recurso de publicação/assinatura para encaminhar mensagens para outros servidores.

![](scaleout-with-redis/_static/image1.png)

Para este tutorial, você usará os três servidores:

- Dois servidores que executam o Windows, o que você usará para implantar um aplicativo do SignalR.
- Um servidor que executa o Linux, o que você usará para executar o Redis. Para as capturas de tela neste tutorial, usei o Ubuntu 12.04 TLS.

Se você não tiver três servidores físicos para usar, você pode criar máquinas virtuais no Hyper-V. Outra opção é criar VMs no Azure.

Embora este tutorial usa a implementação do Redis oficial, há também uma [porta do Windows do Redis](https://github.com/MSOpenTech/redis) do MSOpenTech. Instalação e configuração são diferentes, mas caso contrário, as etapas são as mesmas.

> [!NOTE] 
> 
> Expansão do SignalR com Redis não oferece suporte a clusters do Redis.


## <a name="overview"></a>Visão geral

Antes de passarmos para o tutorial detalhado, aqui está uma visão rápida do que você deve fazer.

1. Instale o Redis e inicie o servidor Redis.
2. Adicione esses pacotes do NuGet ao seu aplicativo: 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.Redis](http://nuget.org/packages/Microsoft.AspNet.SignalR.Redis)
3. Crie um aplicativo do SignalR.
4. Adicione o seguinte código ao global. asax para configurar o backplane: 

    [!code-csharp[Main](scaleout-with-redis/samples/sample1.cs)]

## <a name="ubuntu-on-hyper-v"></a>Ubuntu no Hyper-V

Usando o Windows Hyper-V, você pode criar facilmente uma VM do Ubuntu no Windows Server.

Baixe o ISO do Ubuntu da [ http://www.ubuntu.com ](http://www.ubuntu.com/).

No Hyper-V, adicione uma nova VM. No **conectar disco rígido Virtual** etapa, selecione **criar um disco rígido virtual**.

![](scaleout-with-redis/_static/image2.png)

No **opções de instalação** etapa, selecione **arquivo de imagem (. ISO)**, clique em **procurar**e navegue até o ISO de instalação do Ubuntu.

![](scaleout-with-redis/_static/image3.png)

## <a name="install-redis"></a>Instale o Redis

Siga as etapas em [ http://redis.io/download ](http://redis.io/download) para baixar e compilar o Redis.

[!code-console[Main](scaleout-with-redis/samples/sample2.cmd)]

Isso compila os binários do Redis `src` directory.

Por padrão, o Redis não requer uma senha. Para definir uma senha, edite o `redis.conf` arquivo, que está localizado no diretório raiz do código-fonte. (Faça uma cópia de backup do arquivo antes de editá-lo!) Adicione a seguinte diretiva para `redis.conf`:

[!code-powershell[Main](scaleout-with-redis/samples/sample3.ps1)]

Agora, inicie o servidor Redis:

[!code-css[Main](scaleout-with-redis/samples/sample4.css)]

![](scaleout-with-redis/_static/image4.png)

Abrir porta 6379, que é a porta padrão que Redis escuta. (Você pode alterar o número da porta no arquivo de configuração).

## <a name="create-the-signalr-application"></a>Criar o aplicativo de SignalR

Crie um aplicativo do SignalR, seguindo um destes tutoriais:

- [Introdução ao SignalR](../getting-started/tutorial-getting-started-with-signalr.md)
- [Introdução ao SignalR e MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md)

Em seguida, modificaremos o aplicativo de bate-papo para dar suporte à expansão com Redis. Primeiro, adicione o pacote do SignalR.Redis NuGet ao seu projeto. No Visual Studio, do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**, em seguida, selecione **Package Manager Console**. Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:

[!code-powershell[Main](scaleout-with-redis/samples/sample5.ps1)]

Em seguida, abra o arquivo global asax. Adicione o seguinte código para o **Application\_iniciar** método:

[!code-csharp[Main](scaleout-with-redis/samples/sample6.cs)]

- "servidor" é o nome do servidor que está executando o Redis.
- *porta* é o número da porta
- "password" é a senha que você definiu no arquivo conf.
- "AppName" é qualquer cadeia de caracteres. O SignalR cria um canal de pub/sub Redis com esse nome.

Por exemplo:

[!code-csharp[Main](scaleout-with-redis/samples/sample7.cs)]

## <a name="deploy-and-run-the-application"></a>Implantar e executar o aplicativo

Prepare suas instâncias do Windows Server para implantar o aplicativo do SignalR.

Adicione a função do IIS. Inclua recursos de "Desenvolvimento de aplicativos", incluindo o protocolo WebSocket.

![](scaleout-with-redis/_static/image5.png)

Também incluem o serviço de gerenciamento (listados em "Ferramentas de gerenciamento").

![](scaleout-with-redis/_static/image6.png)

**Install Web implantação 3.0.** Quando você executar o Gerenciador do IIS, ele solicitará que você instale o Microsoft Web Platform, ou pode [baixar o intstaller](https://go.microsoft.com/fwlink/?LinkId=255386). No instalador da plataforma, procure implantação da Web e instale o Web Deploy 3.0

![](scaleout-with-redis/_static/image7.png)

Verifique se o serviço de gerenciamento da Web está em execução. Caso contrário, inicie o serviço. (Se você não vir o serviço de gerenciamento da Web na lista de serviços do Windows, certifique-se de que você instalou o serviço de gerenciamento quando você adicionou a função do IIS.)

Por padrão, o serviço de gerenciamento da Web escuta na porta TCP 8172. No Firewall do Windows, crie uma nova regra de entrada para permitir o tráfego TCP na porta 8172. Para obter mais informações, consulte [Configurando regras de Firewall](https://technet.microsoft.com/library/dd448559(WS.10).aspx). (Se você estiver hospedando as VMs no Azure, você pode fazer isso diretamente no portal do Azure. Ver [como configurar pontos de extremidade para uma máquina Virtual](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)

Agora você está pronto para implantar o projeto do Visual Studio em seu computador de desenvolvimento para o servidor. No Gerenciador de soluções, clique com botão direito a solução e clique em **publicar**.

Para obter mais documentação sobre a implantação da web, consulte [mapa de conteúdo de implantação da Web para Visual Studio e o ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).

Se você implantar o aplicativo em dois servidores, você pode abrir cada instância em uma janela separada do navegador e ver que cada um receba mensagens do SignalR de outra. (É claro que, em um ambiente de produção, os dois servidores estaria atrás de um balanceador de carga.)

![](scaleout-with-redis/_static/image8.png)

Se você estiver curioso para ver as mensagens que são enviadas para o Redis, você pode usar o **redis-cli** cliente, que é instalado com o Redis.

[!code-xml[Main](scaleout-with-redis/samples/sample8.xml)]

![](scaleout-with-redis/_static/image9.png)
