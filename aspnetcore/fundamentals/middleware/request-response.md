---
title: Operações de solicitação e de resposta no ASP.NET Core
author: jkotalik
description: Aprenda a ler o corpo da solicitação e grave o corpo da resposta no ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: 5e531c0ce0ed48097054fd81ddc3655a66cc7c5f
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081678"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="ebbad-103">Operações de solicitação e de resposta no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebbad-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="ebbad-104">De [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="ebbad-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="ebbad-105">Este artigo explica como ler o corpo da solicitação e gravar no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="ebbad-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="ebbad-106">O código para essas operações pode ser necessário ao escrever middleware.</span><span class="sxs-lookup"><span data-stu-id="ebbad-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="ebbad-107">Fora do middleware de escrita, o código personalizado geralmente não é necessário porque as operações são tratadas pelo MVC e Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="ebbad-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="ebbad-108">Há duas abstrações para os corpos de solicitação e resposta: <xref:System.IO.Stream> e <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="ebbad-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="ebbad-109">Para a leitura da solicitação, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) é um <xref:System.IO.Stream> e `HttpRequest.BodyReader` é um <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="ebbad-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="ebbad-110">Para a gravação de resposta, [HttpResponse. Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) é <xref:System.IO.Stream>um `HttpResponse.BodyWriter` e é <xref:System.IO.Pipelines.PipeWriter>um.</span><span class="sxs-lookup"><span data-stu-id="ebbad-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="ebbad-111">Os pipelines são recomendados em fluxos.</span><span class="sxs-lookup"><span data-stu-id="ebbad-111">Pipelines are recommended over streams.</span></span> <span data-ttu-id="ebbad-112">Os fluxos podem ser mais fáceis de usar em algumas operações simples, mas os pipelines têm uma vantagem no desempenho e são mais fáceis de usar na maioria dos cenários.</span><span class="sxs-lookup"><span data-stu-id="ebbad-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="ebbad-113">ASP.NET Core está começando a usar pipelines em vez de fluxos internamente.</span><span class="sxs-lookup"><span data-stu-id="ebbad-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="ebbad-114">Os exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="ebbad-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="ebbad-115">Os fluxos não estão sendo removidos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="ebbad-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="ebbad-116">Os `FileStreams` fluxos continuam a ser usados em todo o .net e muitos tipos de fluxo não têm equivalentes de pipe `ResponseCompression`, como e.</span><span class="sxs-lookup"><span data-stu-id="ebbad-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="ebbad-117">Exemplos de fluxos</span><span class="sxs-lookup"><span data-stu-id="ebbad-117">Stream examples</span></span>

<span data-ttu-id="ebbad-118">Suponha que o objetivo seja criar um middleware que leia todo o corpo da solicitação como uma lista de cadeias de caracteres, dividindo em novas linhas.</span><span class="sxs-lookup"><span data-stu-id="ebbad-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="ebbad-119">Uma implementação simples de fluxo pode ter uma aparência semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="ebbad-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="ebbad-120">Esse código funciona, mas há alguns problemas:</span><span class="sxs-lookup"><span data-stu-id="ebbad-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="ebbad-121">Antes de ser acrescentado ao `StringBuilder`, o exemplo cria outra cadeia de caracteres (`encodedString`), que é imediatamente descartada.</span><span class="sxs-lookup"><span data-stu-id="ebbad-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="ebbad-122">Esse processo ocorre em todos os bytes no fluxo, portanto, o resultado é uma alocação extra de memória do tamanho de todo o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="ebbad-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="ebbad-123">O exemplo lê a cadeia de caracteres inteira antes da divisão em novas linhas.</span><span class="sxs-lookup"><span data-stu-id="ebbad-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="ebbad-124">É mais eficiente verificar se há novas linhas na matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="ebbad-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="ebbad-125">Aqui está um exemplo que corrige alguns dos problemas anteriores:</span><span class="sxs-lookup"><span data-stu-id="ebbad-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="ebbad-126">Este exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="ebbad-126">This preceding example:</span></span>

* <span data-ttu-id="ebbad-127">Não armazena em buffer todo o corpo da solicitação em um `StringBuilder`, a menos que não haja caracteres da nova linha.</span><span class="sxs-lookup"><span data-stu-id="ebbad-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="ebbad-128">Não chama `Split` na cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="ebbad-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="ebbad-129">No entanto, ainda há alguns problemas:</span><span class="sxs-lookup"><span data-stu-id="ebbad-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="ebbad-130">Se os caracteres de nova linha forem esparsos, grande parte do corpo da solicitação será armazenada em buffer na cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="ebbad-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="ebbad-131">O código continua a criar cadeias`remainingString`de caracteres () e as adiciona ao buffer de cadeias, o que resulta em uma alocação extra.</span><span class="sxs-lookup"><span data-stu-id="ebbad-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="ebbad-132">Esses problemas são corrigível, mas o código está se tornando cada vez mais complicado com pouca melhoria.</span><span class="sxs-lookup"><span data-stu-id="ebbad-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="ebbad-133">Os pipelines oferecem uma maneira de resolver esses problemas com uma complexidade de código mínima.</span><span class="sxs-lookup"><span data-stu-id="ebbad-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="ebbad-134">Pipelines</span><span class="sxs-lookup"><span data-stu-id="ebbad-134">Pipelines</span></span>

<span data-ttu-id="ebbad-135">O exemplo a seguir mostra como o mesmo cenário pode ser tratado usando um `PipeReader`:</span><span class="sxs-lookup"><span data-stu-id="ebbad-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="ebbad-136">Este exemplo corrige muitos problemas das implementações de fluxos:</span><span class="sxs-lookup"><span data-stu-id="ebbad-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="ebbad-137">Não há necessidade de um buffer de cadeia de caracteres `PipeReader` porque os bytes de identificadores que não foram usados.</span><span class="sxs-lookup"><span data-stu-id="ebbad-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="ebbad-138">As cadeias de caracteres codificadas são adicionadas diretamente à lista de cadeias de caracteres retornadas.</span><span class="sxs-lookup"><span data-stu-id="ebbad-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="ebbad-139">A criação da cadeia de caracteres não tem alocações, além da memória usada pela cadeia de caracteres (exceto a chamada `ToArray()`).</span><span class="sxs-lookup"><span data-stu-id="ebbad-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="ebbad-140">Adaptadores</span><span class="sxs-lookup"><span data-stu-id="ebbad-140">Adapters</span></span>

<span data-ttu-id="ebbad-141">Ambas `Body` as `BodyReader/BodyWriter` Propriedades e estão disponíveis `HttpRequest` para `HttpResponse`o e o.</span><span class="sxs-lookup"><span data-stu-id="ebbad-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="ebbad-142">Quando você define `Body` para um fluxo diferente, um novo conjunto de adaptadores adapta automaticamente cada tipo para o outro.</span><span class="sxs-lookup"><span data-stu-id="ebbad-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="ebbad-143">Se você definir `HttpRequest.Body` para um novo fluxo, `HttpRequest.BodyReader` o será automaticamente definido como `HttpRequest.Body`um `PipeReader` novo encapsulado.</span><span class="sxs-lookup"><span data-stu-id="ebbad-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="ebbad-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="ebbad-144">StartAsync</span></span>

<span data-ttu-id="ebbad-145">`HttpResponse.StartAsync`é usado para indicar que os cabeçalhos são não modificáveis e para `OnStarting` executar retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="ebbad-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="ebbad-146">Ao usar o Kestrel como um servidor, `StartAsync` chamar antes de `PipeReader` usar as garantias de que `GetMemory` a memória retornada pelo pertence <xref:System.IO.Pipelines.Pipe> ao Kestrel interno, e não a um buffer externo.</span><span class="sxs-lookup"><span data-stu-id="ebbad-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ebbad-147">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ebbad-147">Additional resources</span></span>

* [<span data-ttu-id="ebbad-148">Introdução ao System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="ebbad-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
