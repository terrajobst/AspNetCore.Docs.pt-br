---
title: Operações de solicitação e de resposta no ASP.NET Core
author: jkotalik
description: Aprenda a ler o corpo da solicitação e grave o corpo da resposta no ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: e992401da2d194b178afbe51a293d103def0f940
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238148"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operações de solicitação e de resposta no ASP.NET Core

De [Justin Kotalik](https://github.com/jkotalik)

Este artigo explica como ler o corpo da solicitação e gravar no corpo da resposta. O código para essas operações pode ser necessário ao escrever middleware. Fora do middleware de escrita, o código personalizado geralmente não é necessário porque as operações são tratadas pelo MVC e Razor Pages.

Há duas abstrações para os corpos de solicitação e resposta: <xref:System.IO.Stream> e <xref:System.IO.Pipelines.Pipe>. Para a leitura da solicitação, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) é um <xref:System.IO.Stream> e `HttpRequest.BodyReader` é um <xref:System.IO.Pipelines.PipeReader>. Para a gravação da resposta, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) é um `HttpResponse.BodyWriter` é um <xref:System.IO.Pipelines.PipeWriter>.

Os pipelines são recomendados em fluxos. Os fluxos podem ser mais fáceis de usar em algumas operações simples, mas os pipelines têm uma vantagem no desempenho e são mais fáceis de usar na maioria dos cenários. ASP.NET Core está começando a usar pipelines em vez de fluxos internamente. Os exemplos incluem:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Os fluxos não estão sendo removidos da estrutura. Os `FileStreams` fluxos continuam a ser usados em todo o .net e muitos tipos de fluxo não têm equivalentes de pipe `ResponseCompression`, como e.

## <a name="stream-examples"></a>Exemplos de fluxos

Suponha que o objetivo seja criar um middleware que leia todo o corpo da solicitação como uma lista de cadeias de caracteres, dividindo em novas linhas. Uma implementação simples de fluxo pode ter uma aparência semelhante ao exemplo a seguir:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

Esse código funciona, mas há alguns problemas:

* Antes de ser acrescentado ao `StringBuilder`, o exemplo cria outra cadeia de caracteres (`encodedString`), que é imediatamente descartada. Esse processo ocorre em todos os bytes no fluxo, portanto, o resultado é uma alocação extra de memória do tamanho de todo o corpo da solicitação.
* O exemplo lê a cadeia de caracteres inteira antes da divisão em novas linhas. É mais eficiente verificar se há novas linhas na matriz de bytes.

Aqui está um exemplo que corrige alguns dos problemas anteriores:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Este exemplo anterior:

* Não armazena em buffer todo o corpo da solicitação em um `StringBuilder`, a menos que não haja caracteres da nova linha.
* Não chama `Split` na cadeia de caracteres.

No entanto, ainda há alguns problemas:

* Se os caracteres de nova linha forem esparsos, grande parte do corpo da solicitação será armazenada em buffer na cadeia de caracteres.
* O código continua a criar cadeias`remainingString`de caracteres () e as adiciona ao buffer de cadeias, o que resulta em uma alocação extra.

Esses problemas são corrigível, mas o código está se tornando cada vez mais complicado com pouca melhoria. Os pipelines oferecem uma maneira de resolver esses problemas com uma complexidade de código mínima.

## <a name="pipelines"></a>Pipelines

O exemplo a seguir mostra como o mesmo cenário pode ser tratado usando um `PipeReader`:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Este exemplo corrige muitos problemas das implementações de fluxos:

* Não há necessidade de um buffer de cadeia de caracteres `PipeReader` porque os bytes de identificadores que não foram usados.
* As cadeias de caracteres codificadas são adicionadas diretamente à lista de cadeias de caracteres retornadas.
* A criação da cadeia de caracteres não tem alocações, além da memória usada pela cadeia de caracteres (exceto a chamada `ToArray()`).

## <a name="adapters"></a>Adaptadores

Ambas `Body` as `BodyReader/BodyWriter` Propriedades e estão disponíveis `HttpRequest` para `HttpResponse`o e o. Quando você define `Body` para um fluxo diferente, um novo conjunto de adaptadores adapta automaticamente cada tipo para o outro. Se você definir `HttpRequest.Body` para um novo fluxo, `HttpRequest.BodyReader` o será automaticamente definido como `HttpRequest.Body`um `PipeReader` novo encapsulado.

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync`é usado para indicar que os cabeçalhos são não modificáveis e para `OnStarting` executar retornos de chamada. Ao usar o Kestrel como um servidor, `StartAsync` chamar antes de `PipeReader` usar as garantias de que `GetMemory` a memória retornada pelo pertence <xref:System.IO.Pipelines.Pipe> ao Kestrel interno, e não a um buffer externo.

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução ao System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
