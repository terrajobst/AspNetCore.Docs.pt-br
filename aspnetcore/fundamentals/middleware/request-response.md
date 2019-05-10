---
title: Operações de solicitação e de resposta no ASP.NET Core
author: jkotalik
description: Aprenda a ler o corpo da solicitação e grave o corpo da resposta no ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jkotalik
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b6e3cd4b79e0c062b271c65cd5ecbdb4ef80c3a1
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085507"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operações de solicitação e de resposta no ASP.NET Core

De [Justin Kotalik](https://github.com/jkotalik)

Este artigo explica como ler o corpo da solicitação e gravar no corpo da resposta. Ao gravar um middleware, talvez seja necessário escrever código para essas operações. Fora isso, você normalmente não precisa gravar esse código já que as operações são tratadas pelo MVC e pelo Razor Pages.

No ASP.NET Core 3.0, há duas abstrações para os corpos de solicitação e resposta: <xref:System.IO.Stream> e <xref:System.IO.Pipelines.Pipe>. Para a leitura da solicitação, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) é um <xref:System.IO.Stream> e `HttpRequest.BodyPipe` é um <xref:System.IO.Pipelines.PipeReader>. Para a gravação da resposta, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) é um `HttpResponse.BodyPipe` é um <xref:System.IO.Pipelines.PipeWriter>.

Recomendamos pipelines em fluxos. Os fluxos podem ser mais fáceis de usar em algumas operações simples, mas os pipelines têm uma vantagem no desempenho e são mais fáceis de usar na maioria dos cenários. Na versão 3.0, o ASP.NET Core começa a usar pipelines em vez de fluxos internamente. Os exemplos incluem:

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

Os fluxos não desaparecerão. Eles continuarão a ser usados em todo o .NET e muitos tipos de fluxo não possuem equivalentes no pipeline, como `FileStreams` e `ResponseCompression`.

## <a name="stream-examples"></a>Exemplos de fluxos

Suponha que você queira criar um middleware para ler todo o corpo da solicitação como uma lista de cadeias de caracteres, dividindo em novas linhas. Uma implementação simples de fluxo pode ter uma aparência semelhante ao exemplo a seguir:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

Esse código funciona, mas há alguns problemas:

- Antes de ser acrescentado ao `StringBuilder`, o exemplo cria outra cadeia de caracteres (`encodedString`), que é imediatamente descartada. Esse processo ocorre em todos os bytes no fluxo, portanto, o resultado é uma alocação extra de memória do tamanho de todo o corpo da solicitação.
- O exemplo lê a cadeia de caracteres inteira antes da divisão em novas linhas. O mais eficiente seria verificar se há novas linhas na matriz de bytes.

Veja um exemplo que corrige alguns desses problemas:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Neste exemplo:

- Não armazena em buffer todo o corpo da solicitação em um `StringBuilder`, a menos que não haja caracteres da nova linha.
- Não chama `Split` na cadeia de caracteres.

No entanto, ainda há alguns problemas:

- Se os caracteres de nova linha forem esparsos, grande parte do corpo da solicitação será armazenado em buffer na cadeia de caracteres.
- Ele ainda cria cadeias de caracteres (`remainingString`) e os adiciona ao buffer da cadeia de caracteres, o que resultará em uma alocação extra.

Apesar desses problemas terem solução, o código tem se tornando cada vez mais complicado, mostrando poucas melhorias. Os pipelines oferecem uma maneira de resolver esses problemas com uma complexidade de código mínima.

## <a name="pipelines"></a>Pipelines

O exemplo a seguir mostra como o mesmo cenário pode ser tratado usando um `PipeReader`:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Este exemplo corrige muitos problemas das implementações de fluxos:

- Um buffer de cadeia de caracteres não é necessário porque o `PipeReader` lida com bytes que não foram usados.
- As cadeias de caracteres codificadas são adicionadas diretamente à lista de cadeias de caracteres retornadas.
- A criação da cadeia de caracteres não tem alocações, além da memória usada pela cadeia de caracteres (exceto a chamada `ToArray()`).

## <a name="adapters"></a>Adaptadores

Agora que as propriedades `Body` e `BodyPipe` estão disponíveis para `HttpRequest` e `HttpResponse`, o que acontece quando você define `Body` para um fluxo diferente? Na versão 3.0, um novo conjunto de adaptadores ajusta automaticamente um tipo ao outro. Por exemplo, se você definir `HttpRequest.Body` para um novo fluxo, `HttpRequest.BodyPipe` será automaticamente definido como uma nova `PipeReader` que encapsula `HttpRequest.Body`. O mesmo comportamento se aplica à configuração da propriedade `BodyPipe`. Se você definir `HttpResponse.BodyPipe` como um novo `PipeWriter`, `HttpResponse.Body` será automaticamente definido como um novo fluxo que encapsula `HttpResponse.BodyPipe`.

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` é uma novidade na versão 3.0. Ele é usado para indicar que os cabeçalhos não poderão ser modificados e para executar retornos de chamada `OnStarting`. Em 3.0-preview3, você precisa chamar `StartAsync` antes de usar `HttpRequest.BodyPipe` e, em versões futuras, isso será uma recomendação. Ao usar o Kestrel como um servidor, chamar StartAsync antes de usar o `PipeReader` garantirá que a memória retornada por `GetMemory` pertencerá ao <xref:System.IO.Pipelines.Pipe> interno do Kestrel, em vez de pertencer ao buffer externo.

## <a name="additional-resources"></a>Recursos adicionais

- [Introdução ao System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>