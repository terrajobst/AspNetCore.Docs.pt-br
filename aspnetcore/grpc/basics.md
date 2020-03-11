---
title: Serviços do gRPC com C#
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs C#com o.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664202"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="c73b2-103">serviços gRPCs com C\#</span><span class="sxs-lookup"><span data-stu-id="c73b2-103">gRPC services with C\#</span></span>

<span data-ttu-id="c73b2-104">Este documento descreve os conceitos necessários para escrever aplicativos do [gRPC](https://grpc.io/docs/guides/) no C#.</span><span class="sxs-lookup"><span data-stu-id="c73b2-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="c73b2-105">Os tópicos abordados aqui se aplicam a aplicativos gRPC baseados em [C-Core](https://grpc.io/blog/grpc-stacks)e em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c73b2-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="c73b2-106">arquivo proto</span><span class="sxs-lookup"><span data-stu-id="c73b2-106">proto file</span></span>

<span data-ttu-id="c73b2-107">o gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API.</span><span class="sxs-lookup"><span data-stu-id="c73b2-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="c73b2-108">Os buffers de protocolo (protobuf) são usados como o IDL (linguagem de design de interface) por padrão.</span><span class="sxs-lookup"><span data-stu-id="c73b2-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="c73b2-109">O arquivo *\*. proto* contém:</span><span class="sxs-lookup"><span data-stu-id="c73b2-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="c73b2-110">A definição do serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="c73b2-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="c73b2-111">As mensagens enviadas entre clientes e servidores.</span><span class="sxs-lookup"><span data-stu-id="c73b2-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="c73b2-112">Para obter mais informações sobre a sintaxe de arquivos protobuf, consulte a [documentação oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="c73b2-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="c73b2-113">Por exemplo, considere o arquivo *Greet. proto* usado em [introdução ao serviço gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="c73b2-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="c73b2-114">Define um serviço de `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="c73b2-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="c73b2-115">O serviço `Greeter` define uma chamada `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="c73b2-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="c73b2-116">`SayHello` envia uma mensagem de `HelloRequest` e recebe uma mensagem de `HelloReply`:</span><span class="sxs-lookup"><span data-stu-id="c73b2-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="c73b2-117">Adicionar um arquivo. proto a um aplicativo C\#</span><span class="sxs-lookup"><span data-stu-id="c73b2-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="c73b2-118">O arquivo *\*. proto* é incluído em um projeto adicionando-o ao grupo de itens de `<Protobuf>`:</span><span class="sxs-lookup"><span data-stu-id="c73b2-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="c73b2-119">C#Suporte de ferramentas para arquivos. proto</span><span class="sxs-lookup"><span data-stu-id="c73b2-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="c73b2-120">O pacote de ferramentas [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) é necessário para gerar os C# ativos de arquivos *\*. proto* .</span><span class="sxs-lookup"><span data-stu-id="c73b2-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="c73b2-121">Os ativos gerados (arquivos):</span><span class="sxs-lookup"><span data-stu-id="c73b2-121">The generated assets (files):</span></span>

* <span data-ttu-id="c73b2-122">São gerados conforme necessário sempre que o projeto é compilado.</span><span class="sxs-lookup"><span data-stu-id="c73b2-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="c73b2-123">Não são adicionados ao projeto ou verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="c73b2-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="c73b2-124">É um artefato de compilação contido no diretório *obj* .</span><span class="sxs-lookup"><span data-stu-id="c73b2-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="c73b2-125">Esse pacote é exigido pelos projetos servidor e cliente.</span><span class="sxs-lookup"><span data-stu-id="c73b2-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="c73b2-126">O metapacote `Grpc.AspNetCore` inclui uma referência a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="c73b2-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="c73b2-127">Projetos de servidor podem adicionar `Grpc.AspNetCore` usando o Gerenciador de pacotes no Visual Studio ou adicionando uma `<PackageReference>` ao arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="c73b2-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="c73b2-128">Projetos de cliente devem referenciar diretamente `Grpc.Tools` juntamente com os outros pacotes necessários para usar o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="c73b2-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="c73b2-129">O pacote de ferramentas não é necessário em tempo de execução, portanto, a dependência é marcada com `PrivateAssets="All"`:</span><span class="sxs-lookup"><span data-stu-id="c73b2-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="c73b2-130">Ativos C# gerados</span><span class="sxs-lookup"><span data-stu-id="c73b2-130">Generated C# assets</span></span>

<span data-ttu-id="c73b2-131">O pacote de ferramentas gera os C# tipos que representam as mensagens definidas nos arquivos de *\*. proto* incluídos.</span><span class="sxs-lookup"><span data-stu-id="c73b2-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="c73b2-132">Para ativos do lado do servidor, um tipo de base de serviço abstrato é gerado.</span><span class="sxs-lookup"><span data-stu-id="c73b2-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="c73b2-133">O tipo base contém as definições de todas as chamadas gRPC contidas no arquivo *. proto* .</span><span class="sxs-lookup"><span data-stu-id="c73b2-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="c73b2-134">Crie uma implementação de serviço concreto que derive desse tipo base e implemente a lógica para as chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="c73b2-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="c73b2-135">Para o `greet.proto`, o exemplo descrito anteriormente, um tipo de `GreeterBase` abstrato que contém um método de `SayHello` virtual é gerado.</span><span class="sxs-lookup"><span data-stu-id="c73b2-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="c73b2-136">Uma implementação concreta `GreeterService` substitui o método e implementa a lógica que manipula a chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="c73b2-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="c73b2-137">Para ativos do lado do cliente, um tipo de cliente concreto é gerado.</span><span class="sxs-lookup"><span data-stu-id="c73b2-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="c73b2-138">As chamadas gRPC no arquivo *. proto* são convertidas em métodos no tipo concreto, que pode ser chamado.</span><span class="sxs-lookup"><span data-stu-id="c73b2-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="c73b2-139">Para o `greet.proto`, o exemplo descrito anteriormente, um tipo de `GreeterClient` concreto é gerado.</span><span class="sxs-lookup"><span data-stu-id="c73b2-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="c73b2-140">Chame `GreeterClient.SayHelloAsync` para iniciar uma chamada gRPC para o servidor.</span><span class="sxs-lookup"><span data-stu-id="c73b2-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="c73b2-141">Por padrão, os ativos do cliente e do servidor são gerados para cada arquivo *\*. proto* incluído no grupo de itens de `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="c73b2-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="c73b2-142">Para garantir que apenas os ativos do servidor sejam gerados em um projeto de servidor, o atributo `GrpcServices` é definido como `Server`.</span><span class="sxs-lookup"><span data-stu-id="c73b2-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="c73b2-143">Da mesma forma, o atributo é definido como `Client` em projetos de cliente.</span><span class="sxs-lookup"><span data-stu-id="c73b2-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="c73b2-144">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c73b2-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
