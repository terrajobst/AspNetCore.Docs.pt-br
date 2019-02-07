---
title: Usar o protocolo de MessagePack Hub no SignalR do ASP.NET Core
author: bradygaster
description: Adicione o protocolo de Hub MessagePack ao SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: da6eeeb51f5d0fc2ad69978688ad1c4ca4d63dab
ms.sourcegitcommit: 3c2ba9a0d833d2a096d9d800ba67a1a7f9491af0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854335"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="2ea7c-103">Usar o protocolo de MessagePack Hub no SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2ea7c-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="2ea7c-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="2ea7c-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="2ea7c-105">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados [começar](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="2ea7c-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2ea7c-106">O que é MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2ea7c-106">What is MessagePack?</span></span>

<span data-ttu-id="2ea7c-107">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária que é rápido e compacto.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="2ea7c-108">Ela é útil quando o desempenho e a largura de banda são uma preocupação porque cria mensagens menores em comparação comparadas [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2ea7c-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2ea7c-109">Porque ele é um formato binário, as mensagens são ilegíveis ao examinar os logs e rastreamentos de rede, a menos que os bytes são passados por meio de um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2ea7c-110">O SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e servidor usar.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2ea7c-111">Configurar MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="2ea7c-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="2ea7c-112">Para habilitar o protocolo do Hub MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2ea7c-113">No arquivo Startup.cs, adicione `AddMessagePackProtocol` para o `AddSignalR` chamada para habilitar o suporte de MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7c-114">JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-114">JSON is enabled by default.</span></span> <span data-ttu-id="2ea7c-115">Adicionar MessagePack habilita o suporte para JSON e MessagePack clientes.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2ea7c-116">Para personalizar como MessagePack formatará a seus dados, `AddMessagePackProtocol` aceita um delegado para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2ea7c-117">No delegado, o `FormatterResolvers` propriedade pode ser usada para configurar as opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2ea7c-118">Para obter mais informações sobre como funcionam os resolvedores, visite a biblioteca, MessagePack em [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2ea7c-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2ea7c-119">Atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser tratados.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2ea7c-120">Configurar MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="2ea7c-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7c-121">JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2ea7c-122">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="2ea7c-123">Adicionar suporte MessagePack qualquer substituirá anteriormente protocolos configurados.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2ea7c-124">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ea7c-124">.NET client</span></span>

<span data-ttu-id="2ea7c-125">Para habilitar MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame `AddMessagePackProtocol` em `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2ea7c-126">Isso `AddMessagePackProtocol` chamada aceita um delegado para configurar opções de como o servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2ea7c-127">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7c-127">JavaScript client</span></span>

<span data-ttu-id="2ea7c-128">MessagePack suporte para o cliente JavaScript é fornecido pelo `@aspnet/signalr-protocol-msgpack` pacote npm.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-128">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="2ea7c-129">Depois de instalar o pacote npm, o módulo pode ser usado diretamente por meio de um carregador de módulo de JavaScript ou importado para o navegador fazendo referência a *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* arquivo.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-129">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* file.</span></span> <span data-ttu-id="2ea7c-130">Em um navegador, o `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-130">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2ea7c-131">Use um `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-131">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2ea7c-132">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-132">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2ea7c-133">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-133">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2ea7c-134">Se *signalr-protocol-msgpack.js* é referenciada antes *msgpack5.js*, ocorre um erro quando tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-134">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2ea7c-135">*SignalR.js* também é necessária antes *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-135">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2ea7c-136">Adicionando `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` para o `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-136">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2ea7c-137">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ea7c-137">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="2ea7c-138">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="2ea7c-138">Related resources</span></span>

* [<span data-ttu-id="2ea7c-139">Introdução</span><span class="sxs-lookup"><span data-stu-id="2ea7c-139">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2ea7c-140">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="2ea7c-140">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2ea7c-141">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="2ea7c-141">JavaScript client</span></span>](xref:signalr/javascript-client)
