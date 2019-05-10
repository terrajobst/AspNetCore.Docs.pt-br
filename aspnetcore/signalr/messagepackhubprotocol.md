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

MessagePack suporte para o cliente JavaScript é fornecido pelo `@aspnet/signalr-protocol-msgpack` pacote npm.

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

## <a name="messagepack-quirks"></a>MessagePack quirks

Existem algumas questões a serem consideradas ao usar o protocolo de Hub MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack diferencia maiusculas de minúsculas

O protocolo MessagePack diferencia maiusculas de minúsculas. Por exemplo, considere o seguinte C# classe:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Durante o envio do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, uma vez que as maiusculas e minúsculas devem corresponder a C# classe exatamente. Por exemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Usando o `camelCased` nomes não vincular corretamente para o C# classe. Você pode contornar isso usando o `Key` atributo para especificar um nome diferente para a propriedade MessagePack. Para obter mais informações, consulte [a documentação do CSharp MessagePack](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind não é preservada quando serializar/desserializar

O protocolo MessagePack não fornece uma maneira para codificar a `Kind` valor de um `DateTime`. Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC. Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter para UTC antes de enviá-los. Convertê-los do UTC em hora local quando você recebê-las.

Para obter mais informações sobre essa limitação, consulte GitHub problema [aspnet/SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>Não há suporte para MinValue MessagePack em JavaScript

O [msgpack5](https://github.com/mcollina/msgpack5) biblioteca usada pelo cliente SignalR JavaScript não oferece suporte a `timestamp96` tipo no MessagePack. Esse tipo é usado para codificar os valores de data muito grandes (ou muito no início no passado ou muito distante no futuro). O valor de `DateTime.MinValue` está `January 1, 0001` que deve ser codificado em um `timestamp96` valor. Devido a isso, enviando `DateTime.MinValue` um JavaScript cliente não tem suporte. Quando `DateTime.MinValue` é recebida pelo cliente JavaScript, o seguinte erro será gerado:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Geralmente, `DateTime.MinValue` é usado para codificar um "ausente" ou `null` valor. Se você precisar codificar esse valor em MessagePack, use um valor anulável `DateTime` valor (`DateTime?`) ou codificar um separado `bool` valor que indica se a data estiver presente.

Para obter mais informações sobre essa limitação, consulte GitHub problema [aspnet/SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Suporte de MessagePack no ambiente de compilação "ahead-of-time"

O [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) usada pelo cliente do .NET e no servidor de biblioteca usa geração de código para otimizar a serialização. Como resultado, ele não é suportado por padrão em ambientes que usam a compilação "ahead of time" (como Unity ou o Xamarin iOS). É possível usar MessagePack nesses ambientes gerando"pré-" o código do serializador/desserializador. Para obter mais informações, consulte [a documentação do CSharp MessagePack](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports). Depois de gerar previamente os serializadores, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Verificações de tipo sejam mais estritas no MessagePack

O protocolo do Hub JSON executará as conversões de tipo durante a desserialização. Por exemplo, se o objeto de entrada tem um valor de propriedade que é um número (`{ foo: 42 }`), mas a propriedade na classe do .NET é do tipo `string`, o valor será convertido. No entanto, MessagePack não realiza essa conversão e lançará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obter mais informações sobre essa limitação, consulte GitHub problema [aspnet/SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)
