---
title: ASP.NET Core Blazor cenários avançados
author: guardrex
description: Saiba mais sobre cenários avançados no Blazor, incluindo como incorporar a lógica de RenderTreeBuilder manual em um aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659449"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>Cenários avançados de ASP.NET Core mais experientes

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Manipulador de circuito de servidor mais incrivelmente

O servidor de mais incrivelmente permite que o código defina um *manipulador de circuito*, que permite a execução de código em alterações no estado do circuito de um usuário. Um manipulador de circuito é implementado derivando de `CircuitHandler` e registrando a classe no contêiner de serviço do aplicativo. O exemplo a seguir de um manipulador de circuito rastreia conexões de sinalização abertas:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

Os manipuladores de circuito são registrados usando DI. Instâncias com escopo são criadas por instância de um circuito. Usando o `TrackingCircuitHandler` no exemplo anterior, um serviço singleton é criado porque o estado de todos os circuitos deve ser acompanhado:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Se os métodos de um manipulador de circuitos personalizados lançarem uma exceção sem tratamento, a exceção será fatal para o circuito de servidor mais incrivelmente. Para tolerar exceções no código de um manipulador ou em métodos chamados, empacote o código em uma ou mais instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com o tratamento de erros e o registro em log.

Quando um circuito termina porque um usuário se desconectou e a estrutura está limpando o estado do circuito, a estrutura descarta o escopo de DI do circuito. Descartar o escopo descarta todos os serviços de DI no escopo do circuito que implementam <xref:System.IDisposable?displayProperty=fullName>. Se qualquer serviço de DI lançar uma exceção sem tratamento durante a alienação, a estrutura registrará a exceção.

## <a name="manual-rendertreebuilder-logic"></a>Lógica RenderTreeBuilder manual

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` fornece métodos para manipular componentes e elementos, incluindo a criação manual de componentes C# no código.

> [!NOTE]
> O uso de `RenderTreeBuilder` para criar componentes é um cenário avançado. Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em um comportamento indefinido.

Considere o seguinte componente de `PetDetails`, que pode ser compilado manualmente em outro componente:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

No exemplo a seguir, o loop no método `CreateComponent` gera três componentes `PetDetails`. Ao chamar `RenderTreeBuilder` métodos para criar os componentes (`OpenComponent` e `AddAttribute`), os números de sequência são números de linha de código-fonte. O algoritmo de diferença mais grande do que se baseia nos números de sequência correspondentes a linhas distintas de código, não a invocações de chamada distintas. Ao criar um componente com métodos `RenderTreeBuilder`, codifique os argumentos para números de sequência. **O uso de um cálculo ou contador para gerar o número de sequência pode levar a um desempenho insatisfatório.** Para obter mais informações, consulte os [números de sequência relacionados à seção números de linha de código e não ordem de execução](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent` componente:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Os tipos no `Microsoft.AspNetCore.Components.RenderTree` permitem o processamento dos *resultados* de operações de renderização. Esses são detalhes internos da implementação da estrutura mais incrivelmente. Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Números de sequência se relacionam a números de linha de código e não a ordem de execução

Os arquivos de componente Razor ( *. Razor*) são sempre compilados. A compilação é uma vantagem potencial sobre a interpretação do código, pois a etapa de compilação pode ser usada para injetar informações que melhoram o desempenho do aplicativo em tempo de execução.

Um exemplo importante desses aprimoramentos envolve *números de sequência*. Os números de sequência indicam ao tempo de execução que as saídas vieram de quais linhas de código distintas e ordenadas. O tempo de execução usa essas informações para gerar comparações de árvore eficientes em tempo linear, o que é muito mais rápido do que normalmente é possível para um algoritmo de comparação de árvore geral.

Considere o seguinte arquivo de componente Razor ( *. Razor*):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

O código anterior é compilado para algo semelhante ao seguinte:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Quando o código é executado pela primeira vez, se `someFlag` for `true`, o Construtor receberá:

| Sequence | Tipo      | Dados   |
| :------: | --------- | :----: |
| 0        | Nó de texto | First  |
| 1        | Nó de texto | Segundo |

Imagine que `someFlag` se torna `false`e a marcação é renderizada novamente. Desta vez, o Construtor recebe:

| Sequence | Tipo       | Dados   |
| :------: | ---------- | :----: |
| 1        | Nó de texto  | Segundo |

Quando o tempo de execução executa uma comparação, ele vê que o item na sequência `0` foi removido e, portanto, gera o seguinte *script de edição*trivial:

* Remova o primeiro nó de texto.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>O problema com a geração de números de sequência programaticamente

Imagine, em vez disso, que você escreveu a seguinte lógica do construtor de árvore de renderização:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Agora, a primeira saída é:

| Sequence | Tipo      | Dados   |
| :------: | --------- | :----: |
| 0        | Nó de texto | First  |
| 1        | Nó de texto | Segundo |

Esse resultado é idêntico ao caso anterior, portanto, não existem problemas negativos. `someFlag` é `false` no segundo processamento e a saída é:

| Sequence | Tipo      | Dados   |
| :------: | --------- | ------ |
| 0        | Nó de texto | Segundo |

Desta vez, o algoritmo diff vê que *duas* alterações ocorreram e o algoritmo gera o seguinte script de edição:

* Altere o valor do primeiro nó de texto para `Second`.
* Remova o segundo nó de texto.

A geração dos números de sequência perdeu todas as informações úteis sobre onde os `if/else` branches e loops estavam presentes no código original. Isso resulta em uma comparação **duas vezes mais longa** do que antes.

Esse é um exemplo trivial. Em casos mais realistas com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho é geralmente mais alto. Em vez de identificar imediatamente quais blocos de loop ou ramificações foram inseridos ou removidos, o algoritmo diff precisa recorrer profundamente nas árvores de renderização. Isso geralmente resulta na criação de scripts de edição mais longos porque o algoritmo diff é informado inconscientemente sobre como as estruturas antigas e novas se relacionam entre si.

### <a name="guidance-and-conclusions"></a>Diretrizes e conclusões

* O desempenho do aplicativo será afetado se os números de sequência forem gerados dinamicamente.
* A estrutura não pode criar seus próprios números de sequência automaticamente em tempo de execução porque as informações necessárias não existem, a menos que sejam capturadas no momento da compilação.
* Não grave blocos longos de lógica de `RenderTreeBuilder` implementada manualmente. Prefira arquivos *. Razor* e permita que o compilador lide com os números de sequência. Se você não conseguir evitar a lógica de `RenderTreeBuilder` manual, divida blocos longos de código em partes menores encapsuladas em chamadas `OpenRegion`/`CloseRegion`. Cada região tem seu próprio espaço separado de números de sequência, para que você possa reiniciar de zero (ou qualquer outro número arbitrário) dentro de cada região.
* Se os números de sequência forem codificados, o algoritmo diff só exigirá que os números de sequência aumentem de valor. O valor inicial e as lacunas são irrelevantes. Uma opção legítima é usar o número de linha de código como o número de sequência, ou começar de zero e aumentar por um ou centenas (ou qualquer intervalo preferencial). 
* Blazor usa números de sequência, enquanto outras estruturas de interface do usuário de diferenciação de árvore não as usam. A comparação é muito mais rápida quando números de sequência são usados e Blazor tem a vantagem de uma etapa de compilação que lida com números de sequência automaticamente para desenvolvedores que criam arquivos *. Razor* .

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Executar transferências de dados grandes em aplicativos do Blazor Server

Em alguns cenários, grandes quantidades de dados devem ser transferidas entre o JavaScript e o Blazor. Normalmente, grandes transferências de dados ocorrem quando:

* As APIs do sistema de arquivos do navegador são usadas para carregar ou baixar um arquivo.
* A interoperabilidade com uma biblioteca de terceiros é necessária.

No Blazor Server, uma limitação está em vigor para evitar a passagem de mensagens grandes e que podem resultar em problemas de desempenho.

Considere as seguintes diretrizes ao desenvolver código que transfere dados entre o JavaScript e o Blazor:

* Divida os dados em partes menores e envie os segmentos de dados sequencialmente até que todos os dados sejam recebidos pelo servidor.
* Não aloque objetos grandes em JavaScript e C# código.
* Não bloqueie o thread da interface do usuário principal por longos períodos ao enviar ou receber dados.
* Libere qualquer memória consumida quando o processo for concluído ou cancelado.
* Impor os seguintes requisitos adicionais para fins de segurança:
  * Declare o tamanho máximo de arquivo ou de dados que pode ser passado.
  * Declare a taxa de carregamento mínima do cliente para o servidor.
* Depois que os dados são recebidos pelo servidor, os dados podem ser:
  * Temporariamente armazenados em um buffer de memória até que todos os segmentos sejam coletados.
  * Consumido imediatamente. Por exemplo, os dados podem ser armazenados imediatamente em um banco de dado ou gravados no disco à medida que cada segmento é recebido.

A classe de carregador de arquivos a seguir manipula a interoperabilidade de JS com o cliente. A classe do carregador usa a interoperabilidade JS para:

* Sondar o cliente para enviar um segmento de dados.
* Anular a transação se a sondagem atingir o tempo limite.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

No exemplo anterior:

* O `_maxBase64SegmentSize` é definido como `8192`, que é calculado a partir de `_maxBase64SegmentSize = _segmentSize * 4 / 3`.
* As APIs de gerenciamento de memória do .NET Core de nível baixo são usadas para armazenar os segmentos de memória no servidor em `_uploadedSegments`.
* Um método `ReceiveFile` é usado para lidar com o upload por meio de interoperabilidade JS:
  * O tamanho do arquivo é determinado em bytes por meio de interoperabilidade de JS com `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.
  * O número de segmentos a serem recebidos é calculado e armazenado em `numberOfSegments`.
  * Os segmentos são solicitados em um loop de `for` por meio de interoperabilidade JS com `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`. Todos os segmentos, exceto os últimos, devem ser 8.192 bytes antes da decodificação. O cliente é forçado a enviar os dados de maneira eficiente.
  * Para cada segmento recebido, as verificações são executadas antes de decodificar com <xref:System.Convert.TryFromBase64String*>.
  * Um fluxo com os dados é retornado como um novo <xref:System.IO.Stream> (`SegmentedStream`) depois que o carregamento é concluído.

A classe de fluxo segmentada expõe a lista de segmentos como um <xref:System.IO.Stream>não pesquisável ReadOnly:

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

O código a seguir implementa as funções JavaScript para receber os dados:

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
