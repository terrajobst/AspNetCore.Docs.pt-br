---
title: Gerenciamento de memória e padrões no ASP.NET Core
author: rick-anderson
description: Saiba como a memória é gerenciada em ASP.NET Core e como o coletor de lixo (GC) funciona.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: performance/memory
ms.openlocfilehash: 0ae367e954e21e2f696a3b292fa64f1d2dba98ec
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829017"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Gerenciamento de memória e coleta de lixo (GC) no ASP.NET Core

Por [Sébastien Ros](https://github.com/sebastienros) e [Rick Anderson](https://twitter.com/RickAndMSFT)

O gerenciamento de memória é complexo, mesmo em uma estrutura gerenciada como o .NET. Analisar e compreender os problemas de memória pode ser desafiador. Este artigo:

* Foi motivada por muitos *vazamentos de memória* e problemas de *GC não funcionar* . A maioria desses problemas foi causada por não entender como o consumo de memória funciona no .NET Core ou não entender como ele é medido.
* Demonstra o uso de memória problemática e sugere abordagens alternativas.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Como a coleta de lixo (GC) funciona no .NET Core

O GC aloca segmentos de heap em que cada segmento é um intervalo contíguo de memória. Os objetos colocados no heap são categorizados em uma das três gerações: 0, 1 ou 2. A geração determina a frequência com que o GC tenta liberar memória em objetos gerenciados que não são mais referenciados pelo aplicativo. As gerações com números menores são GC com mais frequência.

Os objetos são movidos de uma geração para outra com base em seu tempo de vida. Como os objetos residem mais tempo, eles são movidos para uma geração mais alta. Conforme mencionado anteriormente, as gerações mais altas são GC com menos frequência. Os objetos de vida de curto prazo sempre permanecem na geração 0. Por exemplo, os objetos que são referenciados durante a vida útil de uma solicitação da Web são de vida curta. [Singletons](xref:fundamentals/dependency-injection#service-lifetimes) de nível de aplicativo geralmente migram para a geração 2.

Quando um aplicativo ASP.NET Core é iniciado, o GC:

* Reserva alguma memória para os segmentos de heap iniciais.
* Confirma uma pequena parte da memória quando o tempo de execução é carregado.

As alocações de memória anteriores são feitas por motivos de desempenho. O benefício de desempenho vem de segmentos de heap na memória contígua.

### <a name="call-gccollect"></a>Chame GC. Reúnem

Chamando [GC. Coletar](xref:System.GC.Collect*) explicitamente:

* **Não** deve ser feito por aplicativos de ASP.NET Core de produção.
* É útil ao investigar vazamentos de memória.
* Ao investigar, o GC removeu todos os objetos pendente da memória, portanto, a memória pode ser medida.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analisando o uso de memória de um aplicativo

Ferramentas dedicadas podem ajudar a analisar o uso de memória:

- Contando referências de objeto
- Medindo quanto impacto o GC tem sobre o uso da CPU
- Medindo o espaço de memória usado para cada geração

Use as seguintes ferramentas para analisar o uso de memória:

* [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace): pode ser usado em máquinas de produção.
* [Analisar o uso de memória sem o depurador do Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Analisar o uso de memória no Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Detectando problemas de memória

O Gerenciador de tarefas pode ser usado para ter uma ideia da quantidade de memória que um aplicativo ASP.NET está usando. O valor de memória do Gerenciador de tarefas:

* Representa a quantidade de memória usada pelo processo ASP.NET.
* Inclui os objetos de vida do aplicativo e outros consumidores de memória, como o uso de memória nativa.

Se o valor de memória do Gerenciador de tarefas aumentar indefinidamente e nunca achatar, o aplicativo terá um vazamento de memória. As seções a seguir demonstram e explicam vários padrões de uso de memória.

## <a name="sample-display-memory-usage-app"></a>Exemplo de aplicativo de uso de memória de vídeo

O [aplicativo de exemplo MemoryLeak](https://github.com/sebastienros/memoryleak) está disponível no github. O aplicativo MemoryLeak:

* Inclui um controlador de diagnóstico que reúne a memória em tempo real e os dados do GC para o aplicativo.
* Tem uma página de índice que exibe os dados de memória e GC. A página de índice é atualizada a cada segundo.
* Contém um controlador de API que fornece vários padrões de carga de memória.
* Não é uma ferramenta com suporte, no entanto, pode ser usada para exibir os padrões de uso de memória de aplicativos ASP.NET Core.

Execute MemoryLeak. A memória alocada aumenta lentamente até que ocorra um GC. A memória aumenta porque a ferramenta aloca um objeto personalizado para capturar dados. A imagem a seguir mostra a página de índice MemoryLeak quando ocorre um GC de Gen 0. O gráfico mostra 0 RPS (solicitações por segundo) porque nenhum ponto de extremidade de API do controlador de API foi chamado.

![gráfico anterior](memory/_static/0RPS.png)

O gráfico exibe dois valores para o uso de memória:

- Alocado: a quantidade de memória ocupada por objetos gerenciados
- [Conjunto de trabalho](/windows/win32/memory/working-set): o conjunto de páginas no espaço de endereço virtual do processo que estão atualmente residentes na memória física. O conjunto de trabalho mostrado é o mesmo valor que o Gerenciador de tarefas exibe.

### <a name="transient-objects"></a>Objetos transitórios

A API a seguir cria uma instância de cadeia de caracteres de 10 KB e a retorna para o cliente. Em cada solicitação, um novo objeto é alocado na memória e gravado na resposta. As cadeias são armazenadas como caracteres UTF-16 no .NET para que cada caractere aceite 2 bytes na memória.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

O grafo a seguir é gerado com uma carga relativamente pequena no para mostrar como as alocações de memória são afetadas pelo GC.

![gráfico anterior](memory/_static/bigstring.png)

O gráfico anterior mostra:

* 4K RPS (solicitações por segundo).
* As coleções de GC de geração 0 ocorrem a cada dois segundos.
* O conjunto de trabalho é constante em aproximadamente 500 MB.
* A CPU é 12%.
* O consumo de memória e a versão (por meio de GC) são estáveis.

O gráfico a seguir é obtido com a taxa de transferência máxima que pode ser tratada pelo computador.

![gráfico anterior](memory/_static/bigstring2.png)

O gráfico anterior mostra:

* 22K RPS
* As coleções de GC de geração 0 ocorrem várias vezes por segundo.
* As coleções de geração 1 são disparadas porque o aplicativo alocou significativamente mais memória por segundo.
* O conjunto de trabalho é constante em aproximadamente 500 MB.
* A CPU é 33%.
* O consumo de memória e a versão (por meio de GC) são estáveis.
* A CPU (33%) Não está superutilizado, portanto, a coleta de lixo pode acompanhar um grande número de alocações.

### <a name="workstation-gc-vs-server-gc"></a>GC de estação de trabalho vs. servidor GC

O coletor de lixo do .NET tem dois modos diferentes:

* **Estação**de trabalho GC: otimizada para o desktop.
* **GC do servidor**. O GC padrão para aplicativos ASP.NET Core. Otimizado para o servidor.

O modo GC pode ser definido explicitamente no arquivo de projeto ou no arquivo *runtimeconfig. JSON* do aplicativo publicado. A marcação a seguir mostra a configuração `ServerGarbageCollection` no arquivo de projeto:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Alterar `ServerGarbageCollection` no arquivo de projeto requer que o aplicativo seja recriado.

**Observação:** A coleta de lixo do servidor **não** está disponível em computadores com um único núcleo. Para obter mais informações, consulte <xref:System.Runtime.GCSettings.IsServerGC>.

A imagem a seguir mostra o perfil de memória em um 5K RPS usando o GC de estação de trabalho.

![gráfico anterior](memory/_static/workstation.png)

As diferenças entre este gráfico e a versão do servidor são significativas:

- O conjunto de trabalho cai de 500 MB a 70 MB.
- O GC faz coleções de geração 0 várias vezes por segundo, em vez de cada dois segundos.
- O GC cai de 300 MB a 10 MB.

Em um ambiente de servidor Web típico, o uso da CPU é mais importante do que a memória, portanto, o GC do servidor é melhor. Se a utilização de memória for alta e o uso da CPU for relativamente baixo, o GC de estação de trabalho poderá ser mais eficaz. Por exemplo, alta densidade hospedando vários aplicativos Web em que a memória é escassa.

### <a name="persistent-object-references"></a>Referências de objeto persistente

O GC não pode liberar objetos que são referenciados. Os objetos que são referenciados, mas que não são mais necessários, resultam em um vazamento de memória. Se o aplicativo frequentemente alocar objetos e falhar em liberá-los depois que eles não forem mais necessários, o uso de memória aumentará ao longo do tempo.

A API a seguir cria uma instância de cadeia de caracteres de 10 KB e a retorna para o cliente. A diferença com o exemplo anterior é que essa instância é referenciada por um membro estático, o que significa que ela nunca está disponível para coleta.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

O código anterior:

* É um exemplo de um vazamento de memória típico.
* Com chamadas frequentes, faz com que a memória do aplicativo aumente até que o processo falhe com uma exceção `OutOfMemory`.

![gráfico anterior](memory/_static/eternal.png)

Na imagem anterior:

* O teste de carga do ponto de extremidade `/api/staticstring` causa um aumento linear na memória.
* O GC tenta liberar memória à medida que a pressão de memória cresce, chamando uma coleção de geração 2.
* O GC não pode liberar a memória vazada. Alocada e conjunto de trabalho aumenta com o tempo.

Alguns cenários, como caching, exigem que as referências de objeto sejam mantidas até que a pressão de memória as force a ser liberada. A classe <xref:System.WeakReference> pode ser usada para esse tipo de código de cache. Um objeto `WeakReference` é coletado sob pressões de memória. A implementação padrão de <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> usa `WeakReference`.

### <a name="native-memory"></a>Memória nativa

Alguns objetos do .NET Core dependem da memória nativa. A memória nativa **não** pode ser coletada pelo GC. O objeto .NET usando a memória nativa deve liberá-lo usando código nativo.

O .NET fornece a interface <xref:System.IDisposable> para permitir que os desenvolvedores liberem memória nativa. Mesmo se <xref:System.IDisposable.Dispose*> não for chamado, as classes implementadas corretamente chamarão `Dispose` quando o [finalizador](/dotnet/csharp/programming-guide/classes-and-structs/destructors) for executado.

Considere o código a seguir:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) é uma classe gerenciada, portanto, qualquer instância será coletada no final da solicitação.

A imagem a seguir mostra o perfil de memória ao invocar a API de `fileprovider` continuamente.

![gráfico anterior](memory/_static/fileprovider.png)

O gráfico anterior mostra um problema óbvio com a implementação dessa classe, pois ela mantém o aumento do uso da memória. Esse é um problema conhecido que está sendo acompanhado nesse [problema](https://github.com/dotnet/aspnetcore/issues/3110).

O mesmo vazamento pode ocorrer no código do usuário, de uma das seguintes formas:

* Não está liberando a classe corretamente.
* Esquecer de invocar o método `Dispose`dos objetos dependentes que devem ser descartados.

### <a name="large-objects-heap"></a>Heap de objetos grandes

A alocação de memória frequente/ciclos livres pode fragmentar a memória, especialmente ao alocar grandes partes de memória. Os objetos são alocados em blocos contíguos de memória. Para atenuar a fragmentação, quando o GC libera memória, ele trys para desfragmentá-la. Esse processo é chamado de **compactação**. A compactação envolve a movimentação de objetos. Mover objetos grandes impõe uma penalidade de desempenho. Por esse motivo, o GC cria uma zona de memória especial para objetos _grandes_ , chamada de Loh ( [heap de objeto grande](/dotnet/standard/garbage-collection/large-object-heap) ). Os objetos maiores que 85.000 bytes (aproximadamente 83 KB) são:

* Colocado no LOH.
* Não compactado.
* Coletados durante a geração 2 GCs.

Quando o LOH estiver cheio, o GC irá disparar uma coleção de geração 2. Coleções de geração 2:

* São inerentemente lentas.
* Além disso, incorre no custo de disparar uma coleção em todas as outras gerações.

O código a seguir compacta o LOH imediatamente:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Consulte <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> para obter informações sobre como compactar o LOH.

Em contêineres que usam o .NET Core 3,0 e posterior, o LOH é compactado automaticamente.

A seguinte API que ilustra esse comportamento:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

O gráfico a seguir mostra o perfil de memória de chamar o ponto de extremidade `/api/loh/84975`, em carga máxima:

![gráfico anterior](memory/_static/loh1.png)

O gráfico a seguir mostra o perfil de memória de chamar o ponto de extremidade `/api/loh/84976`, alocando *apenas mais um byte*:

![gráfico anterior](memory/_static/loh2.png)

Observação: a estrutura de `byte[]` tem bytes de sobrecarga. É por isso que 84.976 bytes dispara o limite de 85.000.

Comparando os dois gráficos anteriores:

* O conjunto de trabalho é semelhante para ambos os cenários, cerca de 450 MB.
* As solicitações em LOH (84.975 bytes) mostram a maioria das coleções de 0 geração.
* As solicitações over LOH geram coleções de geração 2 de constantes. As coleções de geração 2 são caras. Mais CPU é necessária e a taxa de transferência cai quase 50%.

Objetos grandes temporários são particularmente problemáticos porque causam Gen2 GCs.

Para obter o desempenho máximo, o uso de objeto grande deve ser minimizado. Se possível, divida objetos grandes. Por exemplo, o middleware de [cache de resposta](xref:performance/caching/response) no ASP.NET Core dividir as entradas de cache em blocos com menos de 85.000 bytes.

Os links a seguir mostram a abordagem de ASP.NET Core para manter os objetos sob o limite de LOH:

* [ResponseCaching/streams/StreamUtilities. cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache. cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Para obter mais informações, consulte .

* [Heap de objeto grande descoberto](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Heap de objeto grande](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

O uso incorreto de <xref:System.Net.Http.HttpClient> pode resultar em um vazamento de recurso. Recursos do sistema, como conexões de banco de dados, soquetes, identificadores de arquivo, etc.:

* São mais escassos que a memória.
* São mais problemáticas quando vazados do que a memória.

Os desenvolvedores .NET experientes sabem chamar <xref:System.IDisposable.Dispose*> em objetos que implementam <xref:System.IDisposable>. Não descartar objetos que implementam `IDisposable` normalmente resulta em memória vazada ou em recursos de sistema vazados.

`HttpClient` implementa `IDisposable`, mas **não** deve ser descartado em todas as invocações. Em vez disso, `HttpClient` deve ser reutilizada.

O ponto de extremidade a seguir cria e descarta uma nova instância de `HttpClient` em cada solicitação:

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

Sob carga, as seguintes mensagens de erro são registradas em log:

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

Embora as instâncias de `HttpClient` sejam descartadas, a conexão de rede real leva algum tempo para ser liberada pelo sistema operacional. Ao criar continuamente novas conexões, ocorre o _esgotamento de portas_ . Cada conexão de cliente requer sua própria porta de cliente.

Uma maneira de evitar o esgotamento de porta é reutilizar a mesma instância de `HttpClient`:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

A instância de `HttpClient` é liberada quando o aplicativo é interrompido. Este exemplo mostra que nem todos os recursos descartáveis devem ser descartados após cada uso.

Consulte o seguinte para obter uma maneira melhor de lidar com o tempo de vida de uma instância de `HttpClient`:

* [HttpClient e gerenciamento de tempo de vida](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [Blog de fábrica do HTTPClient](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Pooling de objetos

O exemplo anterior mostrou como a instância de `HttpClient` pode se tornar estática e reutilizada por todas as solicitações. A reutilização impede a execução de recursos.

Pool de objetos:

* Usa o padrão de reutilização.
* O é projetado para objetos que são caros de criar.

Um pool é uma coleção de objetos pré-carregados que podem ser reservados e liberados entre threads. Os pools podem definir regras de alocação, como limites, tamanhos predefinidos ou taxa de crescimento.

O pacote NuGet [Microsoft. Extensions. objectpool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contém classes que ajudam a gerenciar esses pools.

O ponto de extremidade de API a seguir instancia um buffer de `byte` que é preenchido com números aleatórios em cada solicitação:

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

A exibição de gráfico a seguir chamando a API anterior com carga moderada:

![gráfico anterior](memory/_static/array.png)

No gráfico anterior, as coletas de geração 0 acontecem aproximadamente uma vez por segundo.

O código anterior pode ser otimizado com o pool do buffer de `byte` usando [ArrayPool\<t >](xref:System.Buffers.ArrayPool`1). Uma instância estática é reutilizada entre solicitações.

O que é diferente com essa abordagem é que um objeto em pool é retornado da API. Isso significa que:

* O objeto está fora do seu controle assim que você retorna do método.
* Não é possível liberar o objeto.

Para configurar a alienação do objeto:

* Encapsular a matriz em pool em um objeto descartável.
* Registre o objeto em pool com [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose` se encarregará de chamar `Dispose`no objeto de destino para que ele seja liberado apenas quando a solicitação HTTP for concluída.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

A aplicação da mesma carga que a versão não-agrupada resulta no seguinte gráfico:

![gráfico anterior](memory/_static/pooledarray.png)

A principal diferença são os bytes alocados e, como consequência, menos coleções de geração 0.

## <a name="additional-resources"></a>Recursos adicionais

* [Coleta de lixo](/dotnet/standard/garbage-collection/)
* [Compreendendo modos de GC diferentes com o Visualizador de simultaneidade](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Heap de objeto grande descoberto](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Heap de objeto grande](/dotnet/standard/garbage-collection/large-object-heap)
