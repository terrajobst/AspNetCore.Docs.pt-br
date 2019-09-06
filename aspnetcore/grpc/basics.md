---
title: Serviços do gRPC com C#
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs C#com o.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: e17a4561f2d4f8ceccb293a8a8c237de58e4ee3c
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310416"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="f83f3-103">serviços gRPCs com C\#</span><span class="sxs-lookup"><span data-stu-id="f83f3-103">gRPC services with C\#</span></span>

<span data-ttu-id="f83f3-104">Este documento descreve os conceitos necessários para escrever aplicativos do [gRPC](https://grpc.io/docs/guides/) no C#.</span><span class="sxs-lookup"><span data-stu-id="f83f3-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="f83f3-105">Os tópicos abordados aqui se aplicam a aplicativos gRPC baseados em [C-Core](https://grpc.io/blog/grpc-stacks)e em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f83f3-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="f83f3-106">arquivo proto</span><span class="sxs-lookup"><span data-stu-id="f83f3-106">proto file</span></span>

<span data-ttu-id="f83f3-107">o gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API.</span><span class="sxs-lookup"><span data-stu-id="f83f3-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="f83f3-108">Os buffers de protocolo (protobuf) são usados como o IDL (linguagem de design de interface) por padrão.</span><span class="sxs-lookup"><span data-stu-id="f83f3-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="f83f3-109">O arquivo. proto contém:  *\**</span><span class="sxs-lookup"><span data-stu-id="f83f3-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="f83f3-110">A definição do serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="f83f3-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="f83f3-111">As mensagens enviadas entre clientes e servidores.</span><span class="sxs-lookup"><span data-stu-id="f83f3-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="f83f3-112">Para obter mais informações sobre a sintaxe de arquivos protobuf, consulte a [documentação oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="f83f3-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="f83f3-113">Por exemplo, considere o arquivo *Greet. proto* usado em [introdução ao serviço gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="f83f3-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="f83f3-114">Define um `Greeter` serviço.</span><span class="sxs-lookup"><span data-stu-id="f83f3-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="f83f3-115">O `Greeter` serviço define uma `SayHello` chamada.</span><span class="sxs-lookup"><span data-stu-id="f83f3-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="f83f3-116">`SayHello`envia uma `HelloRequest` mensagem e recebe uma `HelloReply` mensagem:</span><span class="sxs-lookup"><span data-stu-id="f83f3-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="f83f3-117">Adicionar um arquivo. proto a um aplicativo\# C</span><span class="sxs-lookup"><span data-stu-id="f83f3-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="f83f3-118">O arquivo  *\*. proto* está incluído em um projeto adicionando-o ao grupo `<Protobuf>` de itens:</span><span class="sxs-lookup"><span data-stu-id="f83f3-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="f83f3-119">C#Suporte de ferramentas para arquivos. proto</span><span class="sxs-lookup"><span data-stu-id="f83f3-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="f83f3-120">O pacote de ferramentas [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) é necessário para gerar os C# ativos de  *\*arquivos. proto* .</span><span class="sxs-lookup"><span data-stu-id="f83f3-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="f83f3-121">Os ativos gerados (arquivos):</span><span class="sxs-lookup"><span data-stu-id="f83f3-121">The generated assets (files):</span></span>

* <span data-ttu-id="f83f3-122">São gerados conforme necessário sempre que o projeto é compilado.</span><span class="sxs-lookup"><span data-stu-id="f83f3-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="f83f3-123">Não são adicionados ao projeto ou verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="f83f3-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="f83f3-124">É um artefato de compilação contido no diretório *obj* .</span><span class="sxs-lookup"><span data-stu-id="f83f3-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="f83f3-125">Esse pacote é exigido pelos projetos servidor e cliente.</span><span class="sxs-lookup"><span data-stu-id="f83f3-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="f83f3-126">O `Grpc.AspNetCore` metapacote inclui uma referência a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="f83f3-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="f83f3-127">Projetos de servidor podem `Grpc.AspNetCore` adicionar usando o Gerenciador de pacotes no Visual Studio ou adicionando `<PackageReference>` um ao arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="f83f3-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="f83f3-128">Projetos de cliente devem fazer `Grpc.Tools` referência direta ao lado dos outros pacotes necessários para usar o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="f83f3-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="f83f3-129">O pacote de ferramentas não é necessário em tempo de execução, portanto, a `PrivateAssets="All"`dependência é marcada com:</span><span class="sxs-lookup"><span data-stu-id="f83f3-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="f83f3-130">Ativos C# gerados</span><span class="sxs-lookup"><span data-stu-id="f83f3-130">Generated C# assets</span></span>

<span data-ttu-id="f83f3-131">O pacote de ferramentas gera os C# tipos que representam as mensagens definidas nos arquivos  *\*. proto* incluídos.</span><span class="sxs-lookup"><span data-stu-id="f83f3-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="f83f3-132">Para ativos do lado do servidor, um tipo de base de serviço abstrato é gerado.</span><span class="sxs-lookup"><span data-stu-id="f83f3-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="f83f3-133">O tipo base contém as definições de todas as chamadas gRPC contidas no arquivo *. proto* .</span><span class="sxs-lookup"><span data-stu-id="f83f3-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="f83f3-134">Crie uma implementação de serviço concreto que derive desse tipo base e implemente a lógica para as chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="f83f3-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="f83f3-135">Para o `greet.proto`, o exemplo descrito anteriormente, um tipo `GreeterBase` abstrato que contém um método `SayHello` virtual é gerado.</span><span class="sxs-lookup"><span data-stu-id="f83f3-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="f83f3-136">Uma implementação `GreeterService` concreta substitui o método e implementa a lógica que manipula a chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="f83f3-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="f83f3-137">Para ativos do lado do cliente, um tipo de cliente concreto é gerado.</span><span class="sxs-lookup"><span data-stu-id="f83f3-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="f83f3-138">As chamadas gRPC no arquivo *. proto* são convertidas em métodos no tipo concreto, que pode ser chamado.</span><span class="sxs-lookup"><span data-stu-id="f83f3-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="f83f3-139">Para o `greet.proto`, o exemplo descrito anteriormente, um tipo `GreeterClient` concreto é gerado.</span><span class="sxs-lookup"><span data-stu-id="f83f3-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="f83f3-140">Chame `GreeterClient.SayHelloAsync` para iniciar uma chamada de gRPC para o servidor.</span><span class="sxs-lookup"><span data-stu-id="f83f3-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="f83f3-141">Por padrão, os ativos do cliente e do servidor são gerados para cada `<Protobuf>`  *\*arquivo. proto* incluído no grupo de itens.</span><span class="sxs-lookup"><span data-stu-id="f83f3-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="f83f3-142">Para garantir que apenas os ativos do servidor sejam gerados em um projeto de `GrpcServices` servidor, o atributo `Server`é definido como.</span><span class="sxs-lookup"><span data-stu-id="f83f3-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="f83f3-143">Da mesma forma, o atributo é `Client` definido como em projetos de cliente.</span><span class="sxs-lookup"><span data-stu-id="f83f3-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f83f3-144">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f83f3-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
