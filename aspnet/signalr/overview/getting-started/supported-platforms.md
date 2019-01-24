---
uid: signalr/overview/getting-started/supported-platforms
title: Plataformas com suporte | Microsoft Docs
author: bradygaster
description: Este artigo descreve quais os clientes e servidores têm suporte pelo SignalR.
ms.author: bradyg
ms.date: 04/18/2018
ms.assetid: eac31beb-0f46-4afa-9def-e80904dea4f0
msc.legacyurl: /signalr/overview/getting-started/supported-platforms
msc.type: authoredcontent
ms.openlocfilehash: 60fa74b54797efbe14ba525160b2f750a4f5a451
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836045"
---
<a name="supported-platforms"></a>Plataformas compatíveis
====================
por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este artigo descreve quais os clientes e servidores têm suporte pelo SignalR. 
> 
> ## <a name="questions-and-comments"></a>Perguntas e comentários
> 
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).

Há suporte para o SignalR em uma variedade de configurações de servidor e cliente. Além disso, cada opção de transporte tem um conjunto de requisitos de seu próprio; Se os requisitos do sistema para um transporte não estiverem disponíveis, o SignalR normalmente realizará failover para outros transportes. Para obter mais informações sobre os transportes que dá suporte ao SignalR, consulte [transportes e Fallbacks](introduction-to-signalr.md#transports).

## <a name="server-system-requirements"></a>Requisitos de sistema do servidor

O componente do servidor SignalR pode ser hospedado em uma variedade de configurações do servidor. Esta seção descreve as versões com suporte dos sistemas operacionais, .NET framework, Internet Information Server e outros componentes.

### <a name="supported-server-operating-systems"></a>Sistemas operacionais de servidor compatíveis

O componente do servidor SignalR pode ser hospedado nos seguintes sistemas operacionais cliente ou servidor. Observe que para o SignalR usar o WebSockets, Windows Server 2012, Windows Server 2016 ou Windows 8 é necessário (WebSocket pode ser usado no Windows Azure Web Sites, desde que a versão do .NET framework do site é definido para a versão 4.5 e soquetes da Web está habilitado no site do Página de configuração).

- Windows Server 2016
- Windows Server 2012
- Windows Server 2008 r2
- Windows 10
- Windows 8
- Windows 7
- Microsoft Azure

### <a name="supported-server-net-framework-version"></a>Versão do servidor com suporte do .NET Framework

SignalR 2 tem suporte apenas no .NET Framework 4.5. Consulte a [atualizações recomendadas](#updates) seção atualizações que melhoram a confiabilidade, compatibilidade, estabilidade e desempenho.

### <a name="supported-server-iis-versions"></a>Versões IIS do servidor com suporte

Quando o SignalR é hospedado no IIS, as seguintes versões têm suporte. Observe que se um sistema operacional cliente for usado, como para desenvolvimento (Windows 8 ou Windows 7), versões completas do IIS ou no Cassini não devem ser usadas, desde que haja um limite de 10 conexões simultâneas impostas, que será ser atingido muito depressa desde que as conexões transitório, com frequência restabelecida e são não são descartados imediatamente após não mais sendo usados. O IIS Express deve ser usado em sistemas operacionais cliente.

Observe também que, para o SignalR usar o WebSocket, IIS 8 ou Express do IIS 8 deve ser usado, o servidor deve estar usando o Windows 8, Windows Server 2012 ou posterior, e WebSocket precisa estar habilitado no IIS. Para obter informações sobre como habilitar o WebSocket no IIS, consulte [o suporte de protocolo WebSocket do IIS 8.0](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support).

- O IIS 8 ou o IIS 8 Express.
- IIS 7 e 7.5. Suporte para [URLs sem extensão](https://support.microsoft.com/kb/980368) é necessária.
- O IIS deve estar em execução no modo integrado; Não há suporte para o modo clássico. Podem ocorrer atrasos da mensagem de até 30 segundos se o IIS é executado no modo clássico usando o transporte de eventos do Server-Sent.
- O aplicativo de hospedagem deve estar executando no modo de confiança total.

## <a name="client-system-requirements"></a>Requisitos do sistema cliente

O SignalR pode ser usado em uma variedade de plataformas de cliente. Esta seção descreve os requisitos do sistema para usar o SignalR em navegadores da web, aplicativos de área de trabalho do Windows, os aplicativos do Silverlight e dispositivos móveis.

### <a name="web-browsers"></a>Navegadores da Web

SignalR pode ser usado em uma variedade de navegadores da web, mas em geral, apenas as duas versões mais recentes têm suporte.

Aplicativos que usam o SignalR em navegadores devem usar a versão do jQuery 1.6.4 ou versões posteriores principais (como 1.7.2, 1.8.2 ou 1.9.1).

O SignalR pode ser usado nos seguintes navegadores:

- Versões do Microsoft Internet Explorer 8, 9, 10 e 11. Moderno, área de trabalho e as versões móveis têm suporte.
- Mozilla Firefox: versão atual - 1, versões do Windows e Mac.
- Google Chrome: versão atual - 1, versões do Windows e Mac.
- Safari: versão atual - 1, versões do Mac e iOS.
- Opera: versão atual - 1, somente o Windows.
- Navegador do Android

Além de exigir que determinados navegadores, vários meios de transporte usa o SignalR têm requisitos próprios. Os transportes a seguir têm suporte sob as seguintes configurações:

<a id="browser"></a>

**Requisitos de transporte de navegador da Web**

| Transporte | Internet Explorer | Chrome (iOS ou Windows) | Firefox | Safari (iOS ou OSX) | Android |
| --- | --- | --- | --- | --- | --- |
| WebSockets | 10+ | atual - 1 | atual - 1 | atual - 1 | N/D |
| Eventos enviados pelo servidor | N/D | atual - 1 | atual - 1 | atual - 1 | N/D |
| ForeverFrame | 8+ | N/D | N/D | N/D | 4.1 |
| Sondagem longa | 8+ | atual - 1 | atual - 1 | atual - 1 | 4.1 |

\*: 6 + obrigatório para a funcionalidade completa.

#### <a name="unsupported-browsers"></a>Navegadores sem suporte

Enquanto o SignalR *talvez* executar sem grandes problemas em versões mais antigas do navegador, testamos não ativamente SignalR neles e geralmente não corrigir bugs que podem aparecer dentro deles.

### <a name="windows-desktop-and-silverlight-applications"></a>Área de trabalho do Windows e aplicativos do Silverlight

Além de ser executado em um navegador da web, o SignalR pode ser hospedado no cliente do Windows autônomo ou aplicativos do Silverlight. Aplicativos de área de trabalho do Windows e Silverlight SignalR têm os seguintes requisitos de sistema.

- Aplicativos usando o .NET 4 têm suporte no Windows XP SP3 ou posterior.
- Aplicativos que usam o .NET Framework 4.5 têm suporte no Windows Vista ou posterior.

Além do sistema operacional e os requisitos do .NET framework, os transportes disponíveis para o SignalR têm requisitos próprios. Os transportes a seguir têm suporte sob as seguintes configurações:

**Requisitos de transporte do Silverlight e Windows Desktop**

| Transporte | Aplicativo .NET | Silverlight |
| --- | --- | --- |
| Soquetes da Web | Windows 8 e posteriores e .NET 4.5 + | N/D |
| Quadro para sempre | N/D | N/D |
| Eventos enviados pelo servidor | .NET 4+ | 5+ |
| Sondagem longa | .NET 4+ | 5+ |

<a id="android"></a>

### <a name="windows-store-and-windows-phone-applications"></a>Windows Store e aplicativos do Windows Phone

O SignalR pode ser usado em aplicativos da Windows Store e aplicativos do Windows Phone 8. Os transportes a seguir têm suporte sob as seguintes configurações:

**Requisitos de transporte de do Windows Phone e Windows Store**

| Transporte | Windows Store / .NET | Windows Store / JavaScript | Windows Phone / IE | Windows Phone / .NET |
| --- | --- | --- | --- | --- |
| WebSockets | N/D | Win8+ | 8+ | N/D |
| Quadro para sempre | N/D | Win8+ | 7.5+ | N/D |
| Eventos enviados pelo servidor | Win8+ | N/D | N/D | 8+ |
| Sondagem longa | Win8+ | Win8+ | 7.5+ | 8+ |

<a id="updates"></a>

## <a name="recommended-updates"></a>Atualizações recomendadas

As atualizações a seguir são recomendadas para servidores do SignalR:

- Uma atualização para o .NET Framework 4.5 está disponível [aqui](https://support.microsoft.com/kb/2750149).
- A Microsoft divulgará periodicamente QFEs para ASP.NET. Eles devem ser aplicados como disponíveis.
