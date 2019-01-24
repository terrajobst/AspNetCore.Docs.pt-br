---
title: Usar o protocolo de MessagePack Hub no SignalR do ASP.NET Core
author: bradygaster
description: Adicione o protocolo de Hub MessagePack ao SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 2852ca93c62e706e9a5203625822c2fb954fd2b8
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835603"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>Usar o protocolo de MessagePack Hub no SignalR do ASP.NET Core

Por [Brennan Conroy](https://github.com/BrennanConroy)

Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados [começar](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>O que é MessagePack?

[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária que é rápido e compacto. Ela é útil quando o desempenho e a largura de banda são uma preocupação porque cria mensagens menores em comparação comparadas [JSON](https://www.json.org/). Porque ele é um formato binário, as mensagens são ilegíveis ao examinar os logs e rastreamentos de rede, a menos que os bytes são passados por meio de um analisador MessagePack. O SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e servidor usar.

## <a name="configure-messagepack-on-the-server"></a>Configurar MessagePack no servidor

Para habilitar o protocolo do Hub MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo. No arquivo Startup.cs, adicione `AddMessagePackProtocol` para o `AddSignalR` chamada para habilitar o suporte de MessagePack no servidor.

> [!NOTE]
> JSON é habilitado por padrão. Adicionar MessagePack habilita o suporte para JSON e MessagePack clientes.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar como MessagePack formatará a seus dados, `AddMessagePackProtocol` aceita um delegado para configurar as opções. No delegado, o `FormatterResolvers` propriedade pode ser usada para configurar as opções de serialização MessagePack. Para obter mais informações sobre como funcionam os resolvedores, visite a biblioteca, MessagePack em [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser tratados.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

## <a name="configure-messagepack-on-the-client"></a>Configurar MessagePack no cliente

> [!NOTE]
> JSON é habilitado por padrão para os clientes com suporte. Os clientes só podem dar suporte a um único protocolo. Adicionar suporte MessagePack qualquer substituirá anteriormente protocolos configurados.

### <a name="net-client"></a>Cliente .NET

Para habilitar MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame `AddMessagePackProtocol` em `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Isso `AddMessagePackProtocol` chamada aceita um delegado para configurar opções de como o servidor.

### <a name="javascript-client"></a>Cliente JavaScript

MessagePack suporte para o cliente Javascript é fornecido pelo `@aspnet/signalr-protocol-msgpack` pacote NPM.

```console
npm install @aspnet/signalr-protocol-msgpack
```

Depois de instalar o pacote npm, o módulo pode ser usado diretamente por meio de um carregador de módulo de JavaScript ou importado para o navegador fazendo referência a *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* arquivo. Em um navegador, o `msgpack5` biblioteca também deve ser referenciada. Use um `<script>` marca para criar uma referência. A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Ao usar o `<script>` elemento, a ordem é importante. Se *signalr-protocol-msgpack.js* é referenciada antes *msgpack5.js*, ocorre um erro quando tentar se conectar com MessagePack. *SignalR.js* também é necessária antes *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Adicionando `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` para o `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)
