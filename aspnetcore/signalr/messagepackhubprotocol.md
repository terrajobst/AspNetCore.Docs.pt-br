---
title: Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core
author: bradygaster
description: Adicione o protocolo Hub MessagePack ao SignalRde ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 1b01357233a9b95a5da052d92e30232c94e78a78
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727229"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="c6450-103">Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6450-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="c6450-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="c6450-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="c6450-105">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="c6450-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c6450-106">O que é o MessagePack?</span><span class="sxs-lookup"><span data-stu-id="c6450-106">What is MessagePack?</span></span>

<span data-ttu-id="c6450-107">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária que é rápido e compacto.</span><span class="sxs-lookup"><span data-stu-id="c6450-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="c6450-108">É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="c6450-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c6450-109">Como é um formato binário, as mensagens são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="c6450-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c6450-110"> tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.</span><span class="sxs-lookup"><span data-stu-id="c6450-110"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c6450-111">Configurar o MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="c6450-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="c6450-112">Para habilitar o protocolo de Hub MessagePack no servidor, instale o pacote de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6450-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c6450-113">No arquivo Startup.cs, adicione `AddMessagePackProtocol` à chamada `AddSignalR` para habilitar o suporte a MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="c6450-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c6450-114">O JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="c6450-114">JSON is enabled by default.</span></span> <span data-ttu-id="c6450-115">Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.</span><span class="sxs-lookup"><span data-stu-id="c6450-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c6450-116">Para personalizar como o MessagePack formatará seus dados, `AddMessagePackProtocol` terá um delegado para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="c6450-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c6450-117">Nesse delegado, a propriedade `FormatterResolvers` pode ser usada para configurar opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="c6450-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c6450-118">Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="c6450-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c6450-119">Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="c6450-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c6450-120">Configurar o MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="c6450-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c6450-121">O JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="c6450-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c6450-122">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="c6450-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="c6450-123">Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c6450-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c6450-124">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="c6450-124">.NET client</span></span>

<span data-ttu-id="c6450-125">Para habilitar o MessagePack no cliente .NET, instale o pacote de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` e chame `AddMessagePackProtocol` no `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c6450-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c6450-126">Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.</span><span class="sxs-lookup"><span data-stu-id="c6450-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c6450-127">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="c6450-127">JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6450-128">O suporte do MessagePack para o cliente JavaScript é fornecido pelo pacote `@microsoft/signalr-protocol-msgpack` NPM.</span><span class="sxs-lookup"><span data-stu-id="c6450-128">MessagePack support for the JavaScript client is provided by the `@microsoft/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="c6450-129">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="c6450-129">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c6450-130">O suporte do MessagePack para o cliente JavaScript é fornecido pelo pacote `@aspnet/signalr-protocol-msgpack` NPM.</span><span class="sxs-lookup"><span data-stu-id="c6450-130">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="c6450-131">Instale o pacote executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="c6450-131">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

<span data-ttu-id="c6450-132">Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="c6450-132">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6450-133">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c6450-133">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c6450-134">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c6450-134">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

<span data-ttu-id="c6450-135">Em um navegador, a biblioteca de `msgpack5` também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="c6450-135">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c6450-136">Use uma marca de `<script>` para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="c6450-136">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c6450-137">A biblioteca pode ser encontrada em *node_modules \msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="c6450-137">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c6450-138">Ao usar o elemento `<script>`, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="c6450-138">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c6450-139">Se *signalr-Protocol-msgpack. js* for referenciado antes de *msgpack5. js*, ocorrerá um erro ao tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="c6450-139">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c6450-140">*signalr. js* também é necessário antes de *signalr-Protocol-msgpack. js*.</span><span class="sxs-lookup"><span data-stu-id="c6450-140">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c6450-141">Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="c6450-141">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c6450-142">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c6450-142">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c6450-143">Peculiaridades MessagePacks</span><span class="sxs-lookup"><span data-stu-id="c6450-143">MessagePack quirks</span></span>

<span data-ttu-id="c6450-144">Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="c6450-144">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c6450-145">MessagePack diferencia maiúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="c6450-145">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c6450-146">O protocolo MessagePack diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="c6450-146">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c6450-147">Por exemplo, considere a seguinte C# classe:</span><span class="sxs-lookup"><span data-stu-id="c6450-147">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c6450-148">Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e C# minúsculas devem corresponder exatamente à classe.</span><span class="sxs-lookup"><span data-stu-id="c6450-148">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c6450-149">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="c6450-149">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c6450-150">O uso de nomes de `camelCased` não se C# associará corretamente à classe.</span><span class="sxs-lookup"><span data-stu-id="c6450-150">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c6450-151">Você pode contornar isso usando o atributo `Key` para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="c6450-151">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c6450-152">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="c6450-152">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c6450-153">DateTime. Kind não é preservado ao serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="c6450-153">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c6450-154">O protocolo MessagePack não fornece uma maneira de codificar o valor de `Kind` de um `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="c6450-154">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c6450-155">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="c6450-155">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="c6450-156">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter em UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="c6450-156">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c6450-157">Converta-os do UTC para a hora local quando você os receber.</span><span class="sxs-lookup"><span data-stu-id="c6450-157">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="c6450-158">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="c6450-158">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c6450-159">Não há suporte para DateTime. MinValue em MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="c6450-159">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c6450-160">A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo cliente JavaScript SignalR não dá suporte ao tipo `timestamp96` em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="c6450-160">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c6450-161">Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="c6450-161">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c6450-162">O valor de `DateTime.MinValue` é `January 1, 0001` que deve ser codificado em um valor `timestamp96`.</span><span class="sxs-lookup"><span data-stu-id="c6450-162">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c6450-163">Por isso, não há suporte para o envio de `DateTime.MinValue` para um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c6450-163">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c6450-164">Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:</span><span class="sxs-lookup"><span data-stu-id="c6450-164">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c6450-165">Geralmente, `DateTime.MinValue` é usado para codificar um valor "Missing" ou `null`.</span><span class="sxs-lookup"><span data-stu-id="c6450-165">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c6450-166">Se você precisar codificar esse valor em MessagePack, use um valor de `DateTime` anulável (`DateTime?`) ou codifique um valor `bool` separado indicando se a data está presente.</span><span class="sxs-lookup"><span data-stu-id="c6450-166">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c6450-167">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="c6450-167">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c6450-168">Suporte a MessagePack no ambiente de compilação "antes do tempo"</span><span class="sxs-lookup"><span data-stu-id="c6450-168">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c6450-169">A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="c6450-169">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c6450-170">Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="c6450-170">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c6450-171">É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="c6450-171">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c6450-172">Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="c6450-172">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="c6450-173">Depois de ter gerado previamente os serializadores, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="c6450-173">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c6450-174">As verificações de tipo são mais estritas em MessagePack</span><span class="sxs-lookup"><span data-stu-id="c6450-174">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c6450-175">O protocolo de Hub JSON executará conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="c6450-175">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c6450-176">Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número (`{ foo: 42 }`), mas a propriedade na classe .NET for do tipo `string`, o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="c6450-176">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c6450-177">No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="c6450-177">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c6450-178">Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="c6450-178">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c6450-179">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="c6450-179">Related resources</span></span>

* [<span data-ttu-id="c6450-180">Introdução</span><span class="sxs-lookup"><span data-stu-id="c6450-180">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c6450-181">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="c6450-181">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c6450-182">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="c6450-182">JavaScript client</span></span>](xref:signalr/javascript-client)
