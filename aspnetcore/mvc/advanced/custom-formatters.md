---
title: Formatadores personalizados em APIs Web do ASP.NET Core MVC
author: tdykstra
description: Saiba como criar e usar formatadores personalizados para APIs Web no ASP.NET Core.
manager: wpickett
ms.author: tdykstra
ms.date: 02/08/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mvc/models/custom-formatters
ms.openlocfilehash: 8a42f2d885bd0a0c6d2bd05f9c589def2e15d50a
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2018
---
# <a name="custom-formatters-in-aspnet-core-mvc-web-apis"></a><span data-ttu-id="0bcab-103">Formatadores personalizados nas APIs Web ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0bcab-103">Custom formatters in ASP.NET Core MVC web APIs</span></span>

<span data-ttu-id="0bcab-104">Por [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="0bcab-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="0bcab-105">O ASP.NET Core MVC tem suporte interno para troca de dados em APIs Web usando formatos de texto sem formatação, XML ou JSON.</span><span class="sxs-lookup"><span data-stu-id="0bcab-105">ASP.NET Core MVC has built-in support for data exchange in web APIs by using JSON, XML, or plain text formats.</span></span> <span data-ttu-id="0bcab-106">Este artigo mostra como adicionar suporte para formatos adicionais criando formatadores personalizados.</span><span class="sxs-lookup"><span data-stu-id="0bcab-106">This article shows how to add support for additional formats by creating custom formatters.</span></span>

<span data-ttu-id="0bcab-107">[Exibir ou baixar amostra do GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="0bcab-107">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample).</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="0bcab-108">Quando usar formatadores personalizados</span><span class="sxs-lookup"><span data-stu-id="0bcab-108">When to use custom formatters</span></span>

<span data-ttu-id="0bcab-109">Use um formatador personalizado quando quiser que o processo de [negociação de conteúdo](xref:mvc/models/formatting) dê suporte a um tipo de conteúdo que não tem suporte dos formatadores internos (JSON, XML e texto sem formatação).</span><span class="sxs-lookup"><span data-stu-id="0bcab-109">Use a custom formatter when you want the [content negotiation](xref:mvc/models/formatting) process to support a content type that isn't supported by the built-in formatters (JSON, XML, and plain text).</span></span>

<span data-ttu-id="0bcab-110">Por exemplo, se alguns dos clientes de sua API Web puderem usar o formato [Protobuf](https://github.com/google/protobuf), talvez você queira usar Protobuf com esses clientes porque é mais eficiente.</span><span class="sxs-lookup"><span data-stu-id="0bcab-110">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span>  <span data-ttu-id="0bcab-111">Ou talvez você queira que sua API Web envie endereços e nomes de contato no formato [vCard](https://wikipedia.org/wiki/VCard), um formato usado normalmente para troca de dados de contato.</span><span class="sxs-lookup"><span data-stu-id="0bcab-111">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="0bcab-112">O aplicativo de exemplo fornecido com este artigo implementa um formatador vCard simples.</span><span class="sxs-lookup"><span data-stu-id="0bcab-112">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="0bcab-113">Visão geral de como usar um formatador personalizado</span><span class="sxs-lookup"><span data-stu-id="0bcab-113">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="0bcab-114">Estas são as etapas para criar e usar um formatador personalizado:</span><span class="sxs-lookup"><span data-stu-id="0bcab-114">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="0bcab-115">Criar uma classe de formatador de saída se quiser serializar os dados a serem enviados ao cliente.</span><span class="sxs-lookup"><span data-stu-id="0bcab-115">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="0bcab-116">Criar uma classe de formatador de entrada se quiser desserializar os dados recebidos do cliente.</span><span class="sxs-lookup"><span data-stu-id="0bcab-116">Create an input formatter class if you want to deserialize data received from the client.</span></span> 
* <span data-ttu-id="0bcab-117">Adicionar instâncias de seus formatadores às coleções `InputFormatters` e `OutputFormatters` em [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="0bcab-117">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="0bcab-118">As seções a seguir fornecem diretrizes e exemplos de código para cada uma dessas etapas.</span><span class="sxs-lookup"><span data-stu-id="0bcab-118">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="0bcab-119">Como criar uma classe de formatador personalizado</span><span class="sxs-lookup"><span data-stu-id="0bcab-119">How to create a custom formatter class</span></span>

<span data-ttu-id="0bcab-120">Para criar um formatador:</span><span class="sxs-lookup"><span data-stu-id="0bcab-120">To create a formatter:</span></span>

* <span data-ttu-id="0bcab-121">Derive a classe da classe base apropriada.</span><span class="sxs-lookup"><span data-stu-id="0bcab-121">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="0bcab-122">Especifique codificações e tipos de mídia válidos no construtor.</span><span class="sxs-lookup"><span data-stu-id="0bcab-122">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="0bcab-123">Substitua os métodos `CanReadType`/`CanWriteType`</span><span class="sxs-lookup"><span data-stu-id="0bcab-123">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="0bcab-124">Substitua os métodos `ReadRequestBodyAsync`/`WriteResponseBodyAsync`</span><span class="sxs-lookup"><span data-stu-id="0bcab-124">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="0bcab-125">Derivar da classe base apropriada</span><span class="sxs-lookup"><span data-stu-id="0bcab-125">Derive from the appropriate base class</span></span>

<span data-ttu-id="0bcab-126">Para tipos de mídia de texto (por exemplo, vCard), derive da classe base [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) ou [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).</span><span class="sxs-lookup"><span data-stu-id="0bcab-126">For text media types (for example, vCard), derive from the [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="0bcab-127">Para tipos binários, derive da classe base [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) ou [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter).</span><span class="sxs-lookup"><span data-stu-id="0bcab-127">For binary types, derive from the [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="0bcab-128">Especifique codificações e tipos de mídia válidos</span><span class="sxs-lookup"><span data-stu-id="0bcab-128">Specify valid media types and encodings</span></span>

<span data-ttu-id="0bcab-129">No construtor, especifique codificações e tipos de mídia válidos adicionando-os às coleções `SupportedMediaTypes` e `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="0bcab-129">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

> [!NOTE]  
> <span data-ttu-id="0bcab-130">Não é possível fazer a injeção de dependência de construtor em uma classe de formatador.</span><span class="sxs-lookup"><span data-stu-id="0bcab-130">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="0bcab-131">Por exemplo, não é possível obter um agente adicionando um parâmetro de agente ao construtor.</span><span class="sxs-lookup"><span data-stu-id="0bcab-131">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="0bcab-132">Para acessar serviços, você precisa usar o objeto de contexto que é passado para seus métodos.</span><span class="sxs-lookup"><span data-stu-id="0bcab-132">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="0bcab-133">O exemplo de código [abaixo](#read-write) mostra como isso é feito.</span><span class="sxs-lookup"><span data-stu-id="0bcab-133">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="0bcab-134">Substituir CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="0bcab-134">Override CanReadType/CanWriteType</span></span> 

<span data-ttu-id="0bcab-135">Especifique o tipo no qual desserializar ou do qual serializar substituindo os métodos `CanReadType` ou `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="0bcab-135">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="0bcab-136">Por exemplo, talvez você só possa criar texto de vCard de um tipo `Contact` e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="0bcab-136">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="0bcab-137">O método CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="0bcab-137">The CanWriteResult method</span></span>

<span data-ttu-id="0bcab-138">Em alguns cenários, você precisa substituir `CanWriteResult` em vez de `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="0bcab-138">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="0bcab-139">Use `CanWriteResult` se as condições a seguir forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="0bcab-139">Use `CanWriteResult` if the following conditions are true:</span></span>

  * <span data-ttu-id="0bcab-140">O método de ação retorna uma classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="0bcab-140">Your action method returns a model class.</span></span>
  * <span data-ttu-id="0bcab-141">Há classes derivadas que podem ser retornadas em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="0bcab-141">There are derived classes which might be returned at runtime.</span></span>
  * <span data-ttu-id="0bcab-142">Você precisa saber em tempo de execução qual classe derivada foi retornada pela ação.</span><span class="sxs-lookup"><span data-stu-id="0bcab-142">You need to know at runtime which derived class was returned by the action.</span></span>  

<span data-ttu-id="0bcab-143">Por exemplo, suponha que sua assinatura do método de ação retorne um tipo `Person`, mas ele pode retornar um tipo `Student` ou `Instructor` que deriva de `Person`.</span><span class="sxs-lookup"><span data-stu-id="0bcab-143">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="0bcab-144">Se você quiser que o formatador trate apenas de objetos `Student`, verifique o tipo de [Objeto](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) no objeto de contexto fornecido ao método `CanWriteResult`.</span><span class="sxs-lookup"><span data-stu-id="0bcab-144">If you want your formatter to handle only `Student` objects, check the type of [Object](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="0bcab-145">Observe que não é necessário usar `CanWriteResult` quando o método de ação retorna `IActionResult`; nesse caso, o método `CanWriteType` recebe o tipo de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="0bcab-145">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>
### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="0bcab-146">Substituir ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="0bcab-146">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span> 

<span data-ttu-id="0bcab-147">Você faz o trabalho real de desserialização ou serialização em `ReadRequestBodyAsync` ou `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="0bcab-147">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span>  <span data-ttu-id="0bcab-148">As linhas destacadas no exemplo a seguir mostram como obter serviços do contêiner de injeção de dependência (não é possível obtê-los dos parâmetros do construtor).</span><span class="sxs-lookup"><span data-stu-id="0bcab-148">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="0bcab-149">Como configurar o MVC para usar um formatador personalizado</span><span class="sxs-lookup"><span data-stu-id="0bcab-149">How to configure MVC to use a custom formatter</span></span>
 
<span data-ttu-id="0bcab-150">Para usar um formatador personalizado, adicione uma instância da classe de formatador à coleção `InputFormatters` ou `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="0bcab-150">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="0bcab-151">Formatadores são avaliados na ordem em que você os insere.</span><span class="sxs-lookup"><span data-stu-id="0bcab-151">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="0bcab-152">O primeiro deles tem precedência.</span><span class="sxs-lookup"><span data-stu-id="0bcab-152">The first one takes precedence.</span></span> 

## <a name="next-steps"></a><span data-ttu-id="0bcab-153">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="0bcab-153">Next steps</span></span>

<span data-ttu-id="0bcab-154">Veja o [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample), que implementa formatadores de entrada e saída simples de vCard.</span><span class="sxs-lookup"><span data-stu-id="0bcab-154">See the [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span>  <span data-ttu-id="0bcab-155">O aplicativo lê e grava vCards parecidos com o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="0bcab-155">The application reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="0bcab-156">Para ver a saída do vCard, execute o aplicativo e envie uma solicitação Get com o cabeçalho de aceitação "texto/vcard" para `http://localhost:63313/api/contacts/` (ao executar com Visual Studio) ou `http://localhost:5000/api/contacts/` (ao executar da linha de comando).</span><span class="sxs-lookup"><span data-stu-id="0bcab-156">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="0bcab-157">Para adicionar um vCard à coleção de contatos na memória, envie uma solicitação Post para a mesma URL, com cabeçalho Content-Type "texto/vcard" e com o texto do vCard no corpo, formatado como o exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="0bcab-157">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>