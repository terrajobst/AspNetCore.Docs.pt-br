---
title: Formatadores personalizados na API Web ASP.NET Core
author: rick-anderson
description: Saiba como criar e usar formatadores personalizados para APIs Web no ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: dd25cda460ba758cd07de094eaadd1f2d8c28657
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667667"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="79fea-103">Formatadores personalizados na API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79fea-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="79fea-104">Por [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="79fea-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="79fea-105">ASP.NET Core MVC dá suporte à troca de dados em APIs Web usando formatadores de entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="79fea-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="79fea-106">Os formatadores de entrada são usados pela [Associação de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="79fea-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="79fea-107">Os formatadores de saída são usados para [Formatar respostas](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="79fea-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="79fea-108">A estrutura fornece formatadores de entrada e saída internos para JSON e XML.</span><span class="sxs-lookup"><span data-stu-id="79fea-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="79fea-109">Ele fornece um formatador de saída interno para texto sem formatação, mas não fornece um formatador de entrada para texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="79fea-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="79fea-110">Este artigo mostra como adicionar suporte para formatos adicionais criando formatadores personalizados.</span><span class="sxs-lookup"><span data-stu-id="79fea-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="79fea-111">Para obter um exemplo de um formatador de entrada personalizado para texto sem formatação, consulte [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) no github.</span><span class="sxs-lookup"><span data-stu-id="79fea-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="79fea-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="79fea-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="79fea-113">Quando usar formatadores personalizados</span><span class="sxs-lookup"><span data-stu-id="79fea-113">When to use custom formatters</span></span>

<span data-ttu-id="79fea-114">Use um formatador personalizado quando desejar que o processo de [negociação de conteúdo](xref:web-api/advanced/formatting#content-negotiation) dê suporte a um tipo de conteúdo que não seja suportado pelos formatadores internos.</span><span class="sxs-lookup"><span data-stu-id="79fea-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="79fea-115">Por exemplo, se alguns dos clientes de sua API Web puderem usar o formato [Protobuf](https://github.com/google/protobuf), talvez você queira usar Protobuf com esses clientes porque é mais eficiente.</span><span class="sxs-lookup"><span data-stu-id="79fea-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="79fea-116">Ou talvez você queira que sua API Web envie endereços e nomes de contato no formato [vCard](https://wikipedia.org/wiki/VCard), um formato usado normalmente para troca de dados de contato.</span><span class="sxs-lookup"><span data-stu-id="79fea-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="79fea-117">O aplicativo de exemplo fornecido com este artigo implementa um formatador vCard simples.</span><span class="sxs-lookup"><span data-stu-id="79fea-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="79fea-118">Visão geral de como usar um formatador personalizado</span><span class="sxs-lookup"><span data-stu-id="79fea-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="79fea-119">Estas são as etapas para criar e usar um formatador personalizado:</span><span class="sxs-lookup"><span data-stu-id="79fea-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="79fea-120">Criar uma classe de formatador de saída se quiser serializar os dados a serem enviados ao cliente.</span><span class="sxs-lookup"><span data-stu-id="79fea-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="79fea-121">Criar uma classe de formatador de entrada se quiser desserializar os dados recebidos do cliente.</span><span class="sxs-lookup"><span data-stu-id="79fea-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="79fea-122">Adicionar instâncias de seus formatadores às coleções `InputFormatters` e `OutputFormatters` em [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="79fea-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="79fea-123">As seções a seguir fornecem diretrizes e exemplos de código para cada uma dessas etapas.</span><span class="sxs-lookup"><span data-stu-id="79fea-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="79fea-124">Como criar uma classe de formatador personalizado</span><span class="sxs-lookup"><span data-stu-id="79fea-124">How to create a custom formatter class</span></span>

<span data-ttu-id="79fea-125">Para criar um formatador:</span><span class="sxs-lookup"><span data-stu-id="79fea-125">To create a formatter:</span></span>

* <span data-ttu-id="79fea-126">Derive a classe da classe base apropriada.</span><span class="sxs-lookup"><span data-stu-id="79fea-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="79fea-127">Especifique codificações e tipos de mídia válidos no construtor.</span><span class="sxs-lookup"><span data-stu-id="79fea-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="79fea-128">Substitua os métodos `CanReadType`/`CanWriteType`</span><span class="sxs-lookup"><span data-stu-id="79fea-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="79fea-129">Substitua os métodos `ReadRequestBodyAsync`/`WriteResponseBodyAsync`</span><span class="sxs-lookup"><span data-stu-id="79fea-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="79fea-130">Derivar da classe base apropriada</span><span class="sxs-lookup"><span data-stu-id="79fea-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="79fea-131">Para tipos de mídia de texto (por exemplo, vCard), derive da classe base [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) ou [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).</span><span class="sxs-lookup"><span data-stu-id="79fea-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="79fea-132">Para obter um exemplo de formatador de entrada, consulte o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="79fea-132">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="79fea-133">Para tipos binários, derive da classe base [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) ou [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).</span><span class="sxs-lookup"><span data-stu-id="79fea-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="79fea-134">Especifique codificações e tipos de mídia válidos</span><span class="sxs-lookup"><span data-stu-id="79fea-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="79fea-135">No construtor, especifique codificações e tipos de mídia válidos adicionando-os às coleções `SupportedMediaTypes` e `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="79fea-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="79fea-136">Para obter um exemplo de formatador de entrada, consulte o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="79fea-136">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="79fea-137">Não é possível fazer a injeção de dependência de construtor em uma classe de formatador.</span><span class="sxs-lookup"><span data-stu-id="79fea-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="79fea-138">Por exemplo, não é possível obter um agente adicionando um parâmetro de agente ao construtor.</span><span class="sxs-lookup"><span data-stu-id="79fea-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="79fea-139">Para acessar serviços, você precisa usar o objeto de contexto que é passado para seus métodos.</span><span class="sxs-lookup"><span data-stu-id="79fea-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="79fea-140">O exemplo de código [abaixo](#read-write) mostra como isso é feito.</span><span class="sxs-lookup"><span data-stu-id="79fea-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="79fea-141">Substituir CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="79fea-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="79fea-142">Especifique o tipo no qual desserializar ou do qual serializar substituindo os métodos `CanReadType` ou `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="79fea-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="79fea-143">Por exemplo, talvez você só possa criar texto de vCard de um tipo `Contact` e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="79fea-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="79fea-144">Para obter um exemplo de formatador de entrada, consulte o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="79fea-144">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="79fea-145">O método CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="79fea-145">The CanWriteResult method</span></span>

<span data-ttu-id="79fea-146">Em alguns cenários, você precisa substituir `CanWriteResult` em vez de `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="79fea-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="79fea-147">Use `CanWriteResult` se as condições a seguir forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="79fea-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="79fea-148">O método de ação retorna uma classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="79fea-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="79fea-149">Há classes derivadas que podem ser retornadas em runtime.</span><span class="sxs-lookup"><span data-stu-id="79fea-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="79fea-150">Você precisa saber em runtime qual classe derivada foi retornada pela ação.</span><span class="sxs-lookup"><span data-stu-id="79fea-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="79fea-151">Por exemplo, suponha que sua assinatura do método de ação retorne um tipo `Person`, mas ele pode retornar um tipo `Student` ou `Instructor` que deriva de `Person`.</span><span class="sxs-lookup"><span data-stu-id="79fea-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="79fea-152">Se você quiser que o formatador trate apenas de objetos `Student`, verifique o tipo de [Objeto](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) no objeto de contexto fornecido ao método `CanWriteResult`.</span><span class="sxs-lookup"><span data-stu-id="79fea-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="79fea-153">Observe que não é necessário usar `CanWriteResult` quando o método de ação retorna `IActionResult`; nesse caso, o método `CanWriteType` recebe o tipo de runtime.</span><span class="sxs-lookup"><span data-stu-id="79fea-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="79fea-154">Substituir ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="79fea-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="79fea-155">Você faz o trabalho real de desserialização ou serialização em `ReadRequestBodyAsync` ou `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="79fea-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="79fea-156">As linhas destacadas no exemplo a seguir mostram como obter serviços do contêiner de injeção de dependência (não é possível obtê-los dos parâmetros do construtor).</span><span class="sxs-lookup"><span data-stu-id="79fea-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="79fea-157">Para obter um exemplo de formatador de entrada, consulte o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="79fea-157">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="79fea-158">Como configurar o MVC para usar um formatador personalizado</span><span class="sxs-lookup"><span data-stu-id="79fea-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="79fea-159">Para usar um formatador personalizado, adicione uma instância da classe de formatador à coleção `InputFormatters` ou `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="79fea-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="79fea-160">Formatadores são avaliados na ordem em que você os insere.</span><span class="sxs-lookup"><span data-stu-id="79fea-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="79fea-161">O primeiro deles tem precedência.</span><span class="sxs-lookup"><span data-stu-id="79fea-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="79fea-162">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="79fea-162">Next steps</span></span>

* <span data-ttu-id="79fea-163">[Aplicativo de exemplo para este documento](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), que implementa formatadores de entrada e saída simples de vCard.</span><span class="sxs-lookup"><span data-stu-id="79fea-163">[Sample app for this doc](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="79fea-164">O aplicativo lê e grava vCards parecidos com o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="79fea-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="79fea-165">Para ver a saída do vCard, execute o aplicativo e envie uma solicitação Get com o cabeçalho de aceitação "texto/vcard" para `http://localhost:63313/api/contacts/` (ao executar com Visual Studio) ou `http://localhost:5000/api/contacts/` (ao executar da linha de comando).</span><span class="sxs-lookup"><span data-stu-id="79fea-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="79fea-166">Para adicionar um vCard à coleção de contatos na memória, envie uma solicitação Post para a mesma URL, com cabeçalho Content-Type "texto/vcard" e com o texto do vCard no corpo, formatado como o exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="79fea-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
