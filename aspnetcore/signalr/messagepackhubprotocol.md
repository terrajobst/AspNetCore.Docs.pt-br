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
ms.openlocfilehash: cd052a97db1e20d6c7aa00f47cf6a7d01a9bc305
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963761"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>Usar o protocolo Hub MessagePack no SignalR para ASP.NET Core

Por [Brennan Conroy](https://github.com/BrennanConroy)

Este artigo pressupõe que o leitor esteja familiarizado com os tópicos abordados em [introdução](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>O que é o MessagePack?

[MessagePack](https://msgpack.org/index.html) é um formato de serialização binária que é rápido e compacto. É útil quando o desempenho e a largura de banda são uma preocupação, pois ele cria mensagens menores comparadas ao [JSON](https://www.json.org/). Como é um formato binário, as mensagens são ilegíveis ao examinar rastreamentos de rede e logs, a menos que os bytes sejam passados por um analisador MessagePack. SignalR tem suporte interno para o formato MessagePack e fornece APIs para o cliente e o servidor usarem.

## <a name="configure-messagepack-on-the-server"></a>Configurar o MessagePack no servidor

Para habilitar o protocolo de Hub MessagePack no servidor, instale o pacote de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` em seu aplicativo. No arquivo Startup.cs, adicione `AddMessagePackProtocol` à chamada `AddSignalR` para habilitar o suporte a MessagePack no servidor.

> [!NOTE]
> O JSON é habilitado por padrão. Adicionar MessagePack habilita o suporte para clientes JSON e MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Para personalizar como o MessagePack formatará seus dados, `AddMessagePackProtocol` terá um delegado para configurar as opções. Nesse delegado, a propriedade `FormatterResolvers` pode ser usada para configurar opções de serialização MessagePack. Para obter mais informações sobre como os resolvedores funcionam, visite a biblioteca MessagePack em [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp). Os atributos podem ser usados nos objetos que você deseja serializar para definir como eles devem ser manipulados.

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

## <a name="configure-messagepack-on-the-client"></a>Configurar o MessagePack no cliente

> [!NOTE]
> O JSON é habilitado por padrão para os clientes com suporte. Os clientes só podem dar suporte a um único protocolo. Adicionar o suporte a MessagePack substituirá todos os protocolos configurados anteriormente.

### <a name="net-client"></a>Cliente .NET

Para habilitar o MessagePack no cliente .NET, instale o pacote de `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` e chame `AddMessagePackProtocol` no `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Essa `AddMessagePackProtocol` chamada usa um delegado para configurar opções assim como o servidor.

### <a name="javascript-client"></a>Cliente JavaScript

::: moniker range=">= aspnetcore-3.0"

O suporte do MessagePack para o cliente JavaScript é fornecido pelo pacote `@microsoft/signalr-protocol-msgpack` NPM. Instale o pacote executando o seguinte comando em um shell de comando:

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O suporte do MessagePack para o cliente JavaScript é fornecido pelo pacote `@aspnet/signalr-protocol-msgpack` NPM. Instale o pacote executando o seguinte comando em um shell de comando:

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

Depois de instalar o pacote NPM, o módulo pode ser usado diretamente por meio de um carregador de módulo JavaScript ou importado para o navegador referenciando o seguinte arquivo:

::: moniker range=">= aspnetcore-3.0"

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

::: moniker-end

Em um navegador, a biblioteca de `msgpack5` também deve ser referenciada. Use uma marca de `<script>` para criar uma referência. A biblioteca pode ser encontrada em *node_modules \msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Ao usar o elemento `<script>`, a ordem é importante. Se *signalr-Protocol-msgpack. js* for referenciado antes de *msgpack5. js*, ocorrerá um erro ao tentar se conectar com MessagePack. *signalr. js* também é necessário antes de *signalr-Protocol-msgpack. js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Adicionar `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` ao `HubConnectionBuilder` irá configurar o cliente para usar o protocolo MessagePack ao se conectar a um servidor.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Neste momento, não há nenhuma opção de configuração para o protocolo MessagePack no cliente JavaScript.

## <a name="messagepack-quirks"></a>Peculiaridades MessagePacks

Há alguns problemas a serem considerados ao usar o protocolo de Hub MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack diferencia maiúsculas de minúsculas

O protocolo MessagePack diferencia maiúsculas de minúsculas. Por exemplo, considere a seguinte C# classe:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Ao enviar do cliente JavaScript, você deve usar `PascalCased` nomes de propriedade, já que os maiúsculas e C# minúsculas devem corresponder exatamente à classe. Por exemplo:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

O uso de nomes de `camelCased` não se C# associará corretamente à classe. Você pode contornar isso usando o atributo `Key` para especificar um nome diferente para a propriedade MessagePack. Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind não é preservado ao serializar/desserializar

O protocolo MessagePack não fornece uma maneira de codificar o valor de `Kind` de um `DateTime`. Como resultado, ao desserializar uma data, o protocolo de Hub MessagePack assume que a data de entrada está no formato UTC. Se você estiver trabalhando com `DateTime` valores na hora local, é recomendável converter em UTC antes de enviá-los. Converta-os do UTC para a hora local quando você os receber.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>Não há suporte para DateTime. MinValue em MessagePack em JavaScript

A biblioteca [msgpack5](https://github.com/mcollina/msgpack5) usada pelo cliente JavaScript SignalR não dá suporte ao tipo `timestamp96` em MessagePack. Esse tipo é usado para codificar valores de data muito grandes (muito cedo no passado ou muito distante no futuro). O valor de `DateTime.MinValue` é `January 1, 0001` que deve ser codificado em um valor `timestamp96`. Por isso, não há suporte para o envio de `DateTime.MinValue` para um cliente JavaScript. Quando `DateTime.MinValue` é recebido pelo cliente JavaScript, o seguinte erro é gerado:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Geralmente, `DateTime.MinValue` é usado para codificar um valor "Missing" ou `null`. Se você precisar codificar esse valor em MessagePack, use um valor de `DateTime` anulável (`DateTime?`) ou codifique um valor `bool` separado indicando se a data está presente.

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Suporte a MessagePack no ambiente de compilação "antes do tempo"

A biblioteca [MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp) usada pelo cliente .net e pelo servidor usa a geração de código para otimizar a serialização. Como resultado, ele não tem suporte por padrão em ambientes que usam a compilação "antecipada" (como o Xamarin iOS ou Unity). É possível usar o MessagePack nesses ambientes ao "gerar previamente" o código do serializador/desserializador. Para obter mais informações, consulte [a documentação do MessagePack-Csharp](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports). Depois de ter gerado previamente os serializadores, você pode registrá-los usando o delegado de configuração passado para `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>As verificações de tipo são mais estritas em MessagePack

O protocolo de Hub JSON executará conversões de tipo durante a desserialização. Por exemplo, se o objeto de entrada tiver um valor de propriedade que seja um número (`{ foo: 42 }`), mas a propriedade na classe .NET for do tipo `string`, o valor será convertido. No entanto, o MessagePack não executa essa conversão e gerará uma exceção que pode ser vista nos logs do lado do servidor (e no console do):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Para obter mais informações sobre essa limitação, consulte o problema do GitHub [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)
