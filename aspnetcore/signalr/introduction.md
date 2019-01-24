---
title: Introdução ao SignalR do ASP.NET Core
author: bradygaster
description: Saiba como a biblioteca SignalR do ASP.NET Core simplifica a adição de funcionalidade em tempo real aos aplicativos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: 673efafce60dfa46cb99f9537fda2bca42bf9822
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836357"
---
# <a name="introduction-to-aspnet-core-signalr"></a>Introdução ao SignalR do ASP.NET Core

## <a name="what-is-signalr"></a>O que é o SignalR?

SignalR do ASP.NET Core é uma biblioteca de software livre que simplifica a adição da funcionalidade da web em tempo real aos aplicativos. A funcionalidade da web em tempo real permite que o código do lado do servidor para conteúdo de envio por push aos clientes instantaneamente.

Bons candidatos para o SignalR:

* Aplicativos que exigem atualizações de alta frequência do servidor. Exemplos são jogos, redes sociais, de votação, leilão, mapas e aplicativos GPS.
* Os painéis e monitoramento de aplicativos. Exemplos incluem painéis da empresa, atualizações de vendas instantâneas, ou alertas de viagem.
* Aplicativos de colaboração. Quadro de comunicações aplicativos e software de reunião de equipe são exemplos de aplicativos de colaboração.
* Aplicativos que exigem as notificações. Redes sociais, email, bate-papo, jogos, alertas de viagem e muitos outros aplicativos usam notificações.

O SignalR fornece uma API para a criação de servidor para cliente [chamadas de procedimento remoto (RPC)](https://wikipedia.org/wiki/Remote_procedure_call). As RPCs chamam funções JavaScript em clientes do código do lado do servidor do .NET Core.

Aqui estão alguns recursos do SignalR para ASP.NET Core:

* Lida com gerenciamento de conexão automaticamente.
* Envia mensagens para todos os clientes conectados simultaneamente. Por exemplo, uma sala de bate-papo.
* Envia mensagens para clientes específicos ou grupos de clientes.
* É dimensionada para lidar com o aumento de tráfego.

A fonte é hospedada em um [SignalR repositório no GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).

## <a name="transports"></a>Transportes

O SignalR dá suporte a várias técnicas para manipular as comunicações em tempo real:

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* Eventos enviados pelo servidor
* Sondagem longa

O SignalR escolhe automaticamente o melhor método de transporte que está dentro de recursos do servidor e cliente.

## <a name="hubs"></a>Hubs

Usa o SignalR *hubs* para comunicação entre clientes e servidores.

Um hub é um pipeline de alto nível que permite que um cliente e servidor chamar métodos em si. O SignalR manipula a expedição entre limites de máquina automaticamente, permitindo que os clientes chamar métodos no servidor e vice-versa. Você pode passar parâmetros fortemente tipados para métodos, que habilita a associação de modelo. O SignalR fornece dois protocolos de hub interno: um protocolo de texto com base em JSON e um protocolo binário, com base em [MessagePack](https://msgpack.org/).  MessagePack geralmente cria mensagens menores em comparação comparadas JSON. Devem dar suporte a navegadores mais antigos [nível XHR 2](https://caniuse.com/#feat=xhr2) para fornecer suporte de protocolo MessagePack.

Os hubs de chamar o código do lado do cliente enviando mensagens que contêm o nome e parâmetros do método do lado do cliente. Os objetos enviados como parâmetros de método são desserializados usando o protocolo configurado. O cliente tenta corresponder o nome a um método no código do lado do cliente. Quando o cliente encontra uma correspondência, ele chama o método e passa a ele os dados de parâmetro desserializado.

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução ao SignalR para ASP.NET Core](xref:tutorials/signalr)
* [Plataformas com suporte](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
