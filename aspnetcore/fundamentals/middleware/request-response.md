---
title: Operações de solicitação e de resposta no ASP.NET Core
author: jkotalik
description: Aprenda a ler o corpo da solicitação e grave o corpo da resposta no ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jkotalik
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: f29c0aff95887d639cf3d1a8c8fe9f9228159f7a
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400717"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="dd398-103">Operações de solicitação e de resposta no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd398-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="dd398-104">De [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="dd398-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="dd398-105">Este artigo explica como ler o corpo da solicitação e gravar no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="dd398-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="dd398-106">Ao gravar um middleware, talvez seja necessário escrever código para essas operações.</span><span class="sxs-lookup"><span data-stu-id="dd398-106">You might need to write code for these operations when you're writing middleware.</span></span> <span data-ttu-id="dd398-107">Fora isso, você normalmente não precisa gravar esse código já que as operações são tratadas pelo MVC e pelo Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="dd398-107">Otherwise, you typically don't have to write this code because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="dd398-108">No ASP.NET Core 3.0, há duas abstrações para os corpos de solicitação e resposta: <xref:System.IO.Stream> e <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="dd398-108">In ASP.NET Core 3.0, there are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="dd398-109">Para a leitura da solicitação, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) é um <xref:System.IO.Stream> e `HttpRequest.BodyPipe` é um <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="dd398-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyPipe` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="dd398-110">Para a gravação da resposta, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) é um `HttpResponse.BodyPipe` é um <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="dd398-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyPipe` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="dd398-111">Recomendamos pipelines em fluxos.</span><span class="sxs-lookup"><span data-stu-id="dd398-111">We recommend pipelines over streams.</span></span> <span data-ttu-id="dd398-112">Os fluxos podem ser mais fáceis de usar em algumas operações simples, mas os pipelines têm uma vantagem no desempenho e são mais fáceis de usar na maioria dos cenários.</span><span class="sxs-lookup"><span data-stu-id="dd398-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="dd398-113">Na versão 3.0, o ASP.NET Core começa a usar pipelines em vez de fluxos internamente.</span><span class="sxs-lookup"><span data-stu-id="dd398-113">In 3.0, ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="dd398-114">Os exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="dd398-114">Examples include:</span></span>

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

<span data-ttu-id="dd398-115">Os fluxos não desaparecerão.</span><span class="sxs-lookup"><span data-stu-id="dd398-115">Streams aren't going away.</span></span> <span data-ttu-id="dd398-116">Eles continuarão a ser usados em todo o .NET e muitos tipos de fluxo não possuem equivalentes no pipeline, como `FileStreams` e `ResponseCompression`.</span><span class="sxs-lookup"><span data-stu-id="dd398-116">They continue to be used throughout .NET, and many stream types don't have pipe equivalents, like `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="dd398-117">Exemplos de fluxos</span><span class="sxs-lookup"><span data-stu-id="dd398-117">Stream examples</span></span>

<span data-ttu-id="dd398-118">Suponha que você queira criar um middleware para ler todo o corpo da solicitação como uma lista de cadeias de caracteres, dividindo em novas linhas.</span><span class="sxs-lookup"><span data-stu-id="dd398-118">Suppose you want to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="dd398-119">Uma implementação simples de fluxo pode ter uma aparência semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="dd398-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="dd398-120">Esse código funciona, mas há alguns problemas:</span><span class="sxs-lookup"><span data-stu-id="dd398-120">This code works, but there are some issues:</span></span>

- <span data-ttu-id="dd398-121">Antes de ser acrescentado ao `StringBuilder`, o exemplo cria outra cadeia de caracteres (`encodedString`), que é imediatamente descartada.</span><span class="sxs-lookup"><span data-stu-id="dd398-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="dd398-122">Esse processo ocorre em todos os bytes no fluxo, portanto, o resultado é uma alocação extra de memória do tamanho de todo o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="dd398-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
- <span data-ttu-id="dd398-123">O exemplo lê a cadeia de caracteres inteira antes da divisão em novas linhas.</span><span class="sxs-lookup"><span data-stu-id="dd398-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="dd398-124">O mais eficiente seria verificar se há novas linhas na matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="dd398-124">It would be more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="dd398-125">Veja um exemplo que corrige alguns desses problemas:</span><span class="sxs-lookup"><span data-stu-id="dd398-125">Here's an example that fixes some of these issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="dd398-126">Neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="dd398-126">This example:</span></span>

- <span data-ttu-id="dd398-127">Não armazena em buffer todo o corpo da solicitação em um `StringBuilder`, a menos que não haja caracteres da nova linha.</span><span class="sxs-lookup"><span data-stu-id="dd398-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
- <span data-ttu-id="dd398-128">Não chama `Split` na cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="dd398-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="dd398-129">No entanto, ainda há alguns problemas:</span><span class="sxs-lookup"><span data-stu-id="dd398-129">However, there are still are a few issues:</span></span>

- <span data-ttu-id="dd398-130">Se os caracteres de nova linha forem esparsos, grande parte do corpo da solicitação será armazenado em buffer na cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="dd398-130">If newline characters are sparse, much of the request body is buffered in the string .</span></span>
- <span data-ttu-id="dd398-131">Ele ainda cria cadeias de caracteres (`remainingString`) e os adiciona ao buffer da cadeia de caracteres, o que resultará em uma alocação extra.</span><span class="sxs-lookup"><span data-stu-id="dd398-131">It still creates strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="dd398-132">Apesar desses problemas terem solução, o código tem se tornando cada vez mais complicado, mostrando poucas melhorias.</span><span class="sxs-lookup"><span data-stu-id="dd398-132">These issues are fixable, but the code is becoming more and more complicated with little improvement.</span></span> <span data-ttu-id="dd398-133">Os pipelines oferecem uma maneira de resolver esses problemas com uma complexidade de código mínima.</span><span class="sxs-lookup"><span data-stu-id="dd398-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="dd398-134">Pipelines</span><span class="sxs-lookup"><span data-stu-id="dd398-134">Pipelines</span></span>

<span data-ttu-id="dd398-135">O exemplo a seguir mostra como o mesmo cenário pode ser tratado usando um `PipeReader`:</span><span class="sxs-lookup"><span data-stu-id="dd398-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="dd398-136">Este exemplo corrige muitos problemas das implementações de fluxos:</span><span class="sxs-lookup"><span data-stu-id="dd398-136">This example fixes many issues that the streams implementations had:</span></span>

- <span data-ttu-id="dd398-137">Um buffer de cadeia de caracteres não é necessário porque o `PipeReader` lida com bytes que não foram usados.</span><span class="sxs-lookup"><span data-stu-id="dd398-137">There is no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
- <span data-ttu-id="dd398-138">As cadeias de caracteres codificadas são adicionadas diretamente à lista de cadeias de caracteres retornadas.</span><span class="sxs-lookup"><span data-stu-id="dd398-138">Encoded strings are directly added to the list of returned strings.</span></span>
- <span data-ttu-id="dd398-139">A criação da cadeia de caracteres não tem alocações, além da memória usada pela cadeia de caracteres (exceto a chamada `ToArray()`).</span><span class="sxs-lookup"><span data-stu-id="dd398-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="dd398-140">Adaptadores</span><span class="sxs-lookup"><span data-stu-id="dd398-140">Adapters</span></span>

<span data-ttu-id="dd398-141">Agora que as propriedades `Body` e `BodyPipe` estão disponíveis para `HttpRequest` e `HttpResponse`, o que acontece quando você define `Body` para um fluxo diferente?</span><span class="sxs-lookup"><span data-stu-id="dd398-141">Now that both `Body` and `BodyPipe` properties are available for `HttpRequest` and `HttpResponse`, what happens when you set `Body` to a different stream?</span></span> <span data-ttu-id="dd398-142">Na versão 3.0, um novo conjunto de adaptadores ajusta automaticamente um tipo ao outro.</span><span class="sxs-lookup"><span data-stu-id="dd398-142">In 3.0, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="dd398-143">Por exemplo, se você definir `HttpRequest.Body` para um novo fluxo, `HttpRequest.BodyPipe` será automaticamente definido como uma nova `PipeReader` que encapsula `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="dd398-143">For example, if you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyPipe` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span> <span data-ttu-id="dd398-144">O mesmo comportamento se aplica à configuração da propriedade `BodyPipe`.</span><span class="sxs-lookup"><span data-stu-id="dd398-144">The same behavior applies to setting the `BodyPipe` property.</span></span> <span data-ttu-id="dd398-145">Se você definir `HttpResponse.BodyPipe` como um novo `PipeWriter`, `HttpResponse.Body` será automaticamente definido como um novo fluxo que encapsula `HttpResponse.BodyPipe`.</span><span class="sxs-lookup"><span data-stu-id="dd398-145">If `HttpResponse.BodyPipe` is set to a new `PipeWriter`, the `HttpResponse.Body` is automatically set to a new stream that wraps `HttpResponse.BodyPipe`.</span></span>

## <a name="startasync"></a><span data-ttu-id="dd398-146">StartAsync</span><span class="sxs-lookup"><span data-stu-id="dd398-146">StartAsync</span></span>

<span data-ttu-id="dd398-147">`HttpResponse.StartAsync` é uma novidade na versão 3.0.</span><span class="sxs-lookup"><span data-stu-id="dd398-147">`HttpResponse.StartAsync` is new in 3.0.</span></span> <span data-ttu-id="dd398-148">Ele é usado para indicar que os cabeçalhos não poderão ser modificados e para executar retornos de chamada `OnStarting`.</span><span class="sxs-lookup"><span data-stu-id="dd398-148">It is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="dd398-149">Em 3.0-preview3, você precisa chamar `StartAsync` antes de usar `HttpRequest.BodyPipe` e, em versões futuras, isso será uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="dd398-149">In 3.0-preview3, you have to call `StartAsync` before using `HttpRequest.BodyPipe`, and in future releases, it will be a recommendation.</span></span> <span data-ttu-id="dd398-150">Ao usar o Kestrel como um servidor, chamar StartAsync antes de usar o `PipeReader` garantirá que a memória retornada por `GetMemory` pertencerá ao <xref:System.IO.Pipelines.Pipe> interno do Kestrel, em vez de pertencer ao buffer externo.</span><span class="sxs-lookup"><span data-stu-id="dd398-150">When using Kestrel as a server, calling StartAsync before using the `PipeReader` guarantees that memory returned by `GetMemory` will belong to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dd398-151">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dd398-151">Additional resources</span></span>

* [<span data-ttu-id="dd398-152">Introdução ao System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="dd398-152">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>