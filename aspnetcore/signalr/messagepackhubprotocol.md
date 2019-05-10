---
title: Usar o protocolo de MessagePack Hub no SignalR do ASP.NET Core
author: bradygaster
description: Adicione o protocolo de Hub MessagePack ao SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/27/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7742f6f8bb53fb3c299ff98ae52a0da519ff396c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896513"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="85fed-103">Usar o protocolo de MessagePack Hub no SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85fed-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="85fed-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="85fed-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="85fed-105">Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados [começar](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="85fed-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="85fed-106">O que é MessagePack?</span><span class="sxs-lookup"><span data-stu-id="85fed-106">What is MessagePack?</span></span>

<span data-ttu-id="85fed-107">[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária que é rápido e compacto.</span><span class="sxs-lookup"><span data-stu-id="85fed-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="85fed-108">Ela é útil quando o desempenho e a largura de banda são uma preocupação porque cria mensagens menores em comparação comparadas [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="85fed-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="85fed-109">Porque ele é um formato binário, as mensagens são ilegíveis ao examinar os logs e rastreamentos de rede, a menos que os bytes são passados por meio de um analisador MessagePack.</span><span class="sxs-lookup"><span data-stu-id="85fed-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="85fed-110">O SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e servidor usar.</span><span class="sxs-lookup"><span data-stu-id="85fed-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="85fed-111">Configurar MessagePack no servidor</span><span class="sxs-lookup"><span data-stu-id="85fed-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="85fed-112">Para habilitar o protocolo do Hub MessagePack no servidor, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85fed-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="85fed-113">No arquivo Startup.cs, adicione `AddMessagePackProtocol` para o `AddSignalR` chamada para habilitar o suporte de MessagePack no servidor.</span><span class="sxs-lookup"><span data-stu-id="85fed-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="85fed-114">JSON é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="85fed-114">JSON is enabled by default.</span></span> <span data-ttu-id="85fed-115">Adicionar MessagePack habilita o suporte para JSON e MessagePack clientes.</span><span class="sxs-lookup"><span data-stu-id="85fed-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="85fed-116">Para personalizar como MessagePack formatará a seus dados, `AddMessagePackProtocol` aceita um delegado para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="85fed-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="85fed-117">No delegado, o `FormatterResolvers` propriedade pode ser usada para configurar as opções de serialização MessagePack.</span><span class="sxs-lookup"><span data-stu-id="85fed-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="85fed-118">Para obter mais informações sobre como funcionam os resolvedores, visite a biblioteca, MessagePack em [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="85fed-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="85fed-119">Atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser tratados.</span><span class="sxs-lookup"><span data-stu-id="85fed-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="85fed-120">Configurar MessagePack no cliente</span><span class="sxs-lookup"><span data-stu-id="85fed-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="85fed-121">JSON é habilitado por padrão para os clientes com suporte.</span><span class="sxs-lookup"><span data-stu-id="85fed-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="85fed-122">Os clientes só podem dar suporte a um único protocolo.</span><span class="sxs-lookup"><span data-stu-id="85fed-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="85fed-123">Adicionar suporte MessagePack qualquer substituirá anteriormente protocolos configurados.</span><span class="sxs-lookup"><span data-stu-id="85fed-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="85fed-124">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="85fed-124">.NET client</span></span>

<span data-ttu-id="85fed-125">Para habilitar MessagePack no cliente .NET, instale o `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pacote e chame `AddMessagePackProtocol` em `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="85fed-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="85fed-126">Isso `AddMessagePackProtocol` chamada aceita um delegado para configurar opções de como o servidor.</span><span class="sxs-lookup"><span data-stu-id="85fed-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="85fed-127">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="85fed-127">JavaScript client</span></span>

<span data-ttu-id="85fed-128">MessagePack suporte para o cliente JavaScript é fornecido pelo `@aspnet/signalr-protocol-msgpack` pacote npm.</span><span class="sxs-lookup"><span data-stu-id="85fed-128">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="85fed-129">Depois de instalar o pacote npm, o módulo pode ser usado diretamente por meio de um carregador de módulo de JavaScript ou importado para o navegador fazendo referência a *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* arquivo.</span><span class="sxs-lookup"><span data-stu-id="85fed-129">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* file.</span></span> <span data-ttu-id="85fed-130">Em um navegador, o `msgpack5` biblioteca também deve ser referenciada.</span><span class="sxs-lookup"><span data-stu-id="85fed-130">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="85fed-131">Use um `<script>` marca para criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="85fed-131">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="85fed-132">A biblioteca pode ser encontrada em *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="85fed-132">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="85fed-133">Ao usar o `<script>` elemento, a ordem é importante.</span><span class="sxs-lookup"><span data-stu-id="85fed-133">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="85fed-134">Se *signalr-protocol-msgpack.js* é referenciada antes *msgpack5.js*, ocorre um erro quando tentar se conectar com MessagePack.</span><span class="sxs-lookup"><span data-stu-id="85fed-134">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="85fed-135">*SignalR.js* também é necessária antes *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="85fed-135">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="85fed-136">Adicionando `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` para o `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.</span><span class="sxs-lookup"><span data-stu-id="85fed-136">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="85fed-137">Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="85fed-137">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="85fed-138">MessagePack quirks</span><span class="sxs-lookup"><span data-stu-id="85fed-138">MessagePack quirks</span></span>

<span data-ttu-id="85fed-139">Existem algumas questões a serem consideradas ao usar o protocolo de Hub MessagePack.</span><span class="sxs-lookup"><span data-stu-id="85fed-139">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="85fed-140">MessagePack diferencia maiusculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="85fed-140">MessagePack is case-sensitive</span></span>

<span data-ttu-id="85fed-141">O protocolo MessagePack diferencia maiusculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="85fed-141">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="85fed-142">Por exemplo, considere o seguinte C# classe:</span><span class="sxs-lookup"><span data-stu-id="85fed-142">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="85fed-143">Durante o envio do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, uma vez que as maiusculas e minúsculas devem corresponder a C# classe exatamente.</span><span class="sxs-lookup"><span data-stu-id="85fed-143">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="85fed-144">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="85fed-144">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="85fed-145">Usando o `camelCased` nomes não vincular corretamente para o C# classe.</span><span class="sxs-lookup"><span data-stu-id="85fed-145">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="85fed-146">Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack.</span><span class="sxs-lookup"><span data-stu-id="85fed-146">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="85fed-147">Para obter mais informações, consulte [a documentação do CSharp MessagePack](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="85fed-147">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="85fed-148">DateTime. Kind não é preservada quando serializar/desserializar</span><span class="sxs-lookup"><span data-stu-id="85fed-148">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="85fed-149">O protocolo MessagePack não fornece uma maneira para codificar a `Kind` valor de um `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="85fed-149">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="85fed-150">Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC.</span><span class="sxs-lookup"><span data-stu-id="85fed-150">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="85fed-151">Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los.</span><span class="sxs-lookup"><span data-stu-id="85fed-151">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="85fed-152">Convertê-los do UTC em hora local quando você recebê-las.</span><span class="sxs-lookup"><span data-stu-id="85fed-152">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="85fed-153">Para obter mais informações sobre essa limitação, consulte GitHub problema [aspnet/SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="85fed-153">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="85fed-154">Não há suporte para MinValue MessagePack em JavaScript</span><span class="sxs-lookup"><span data-stu-id="85fed-154">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="85fed-155">O [msgpack5](https://github.com/mcollina/msgpack5) biblioteca usada pelo cliente SignalR JavaScript não oferece suporte a `timestamp96` tipo no MessagePack.</span><span class="sxs-lookup"><span data-stu-id="85fed-155">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="85fed-156">Esse tipo é usado para codificar os valores de data muito grandes (ou muito no início no passado ou muito distante no futuro).</span><span class="sxs-lookup"><span data-stu-id="85fed-156">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="85fed-157">O valor de `DateTime.MinValue` está `January 1, 0001` que deve ser codificado em um `timestamp96` valor.</span><span class="sxs-lookup"><span data-stu-id="85fed-157">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="85fed-158">Devido a isso, enviando `DateTime.MinValue` um JavaScript cliente não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="85fed-158">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="85fed-159">Quando `DateTime.MinValue` é recebida pelo cliente JavaScript, o seguinte erro será gerado:</span><span class="sxs-lookup"><span data-stu-id="85fed-159">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="85fed-160">Geralmente, `DateTime.MinValue` é usado para codificar um "ausente" ou `null` valor.</span><span class="sxs-lookup"><span data-stu-id="85fed-160">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="85fed-161">Se você precisar codificar esse valor em MessagePack, use um valor anulável `DateTime` valor (`DateTime?`) ou codificar um separado `bool` valor que indica se a data estiver presente.</span><span class="sxs-lookup"><span data-stu-id="85fed-161">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="85fed-162">Para obter mais informações sobre essa limitação, consulte GitHub problema [aspnet/SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="85fed-162">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="85fed-163">Suporte de MessagePack no ambiente de compilação "ahead-of-time"</span><span class="sxs-lookup"><span data-stu-id="85fed-163">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="85fed-164">O [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) usada pelo cliente do .NET e no servidor de biblioteca usa geração de código para otimizar a serialização.</span><span class="sxs-lookup"><span data-stu-id="85fed-164">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="85fed-165">Como resultado, ele não é suportado por padrão em ambientes que usam a compilação "ahead of time" (como Unity ou o Xamarin iOS).</span><span class="sxs-lookup"><span data-stu-id="85fed-165">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="85fed-166">É possível usar MessagePack nesses ambientes gerando"pré-" o código do serializador/desserializador.</span><span class="sxs-lookup"><span data-stu-id="85fed-166">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="85fed-167">Para obter mais informações, consulte [a documentação do CSharp MessagePack](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="85fed-167">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="85fed-168">Depois de gerar previamente os serializadores, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="85fed-168">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="85fed-169">Verificações de tipo sejam mais estritas no MessagePack</span><span class="sxs-lookup"><span data-stu-id="85fed-169">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="85fed-170">O protocolo do Hub JSON executará as conversões de tipo durante a desserialização.</span><span class="sxs-lookup"><span data-stu-id="85fed-170">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="85fed-171">Por exemplo, se o objeto de entrada tem um valor de propriedade que é um número (`{ foo: 42 }`), mas a propriedade na classe do .NET é do tipo `string`, o valor será convertido.</span><span class="sxs-lookup"><span data-stu-id="85fed-171">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="85fed-172">No entanto, MessagePack não realiza essa conversão e lançará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):</span><span class="sxs-lookup"><span data-stu-id="85fed-172">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="85fed-173">Para obter mais informações sobre essa limitação, consulte GitHub problema [aspnet/SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="85fed-173">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="85fed-174">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="85fed-174">Related resources</span></span>

* [<span data-ttu-id="85fed-175">Introdução</span><span class="sxs-lookup"><span data-stu-id="85fed-175">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="85fed-176">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="85fed-176">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="85fed-177">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="85fed-177">JavaScript client</span></span>](xref:signalr/javascript-client)
