---
title: Serviços do gRPC com C#
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPC com C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: ce2682848dc6a81293545c27f0be779e12a3a600
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "59515337"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="bba02-103">Serviços de gRPC com C\#</span><span class="sxs-lookup"><span data-stu-id="bba02-103">gRPC services with C\#</span></span>

<span data-ttu-id="bba02-104">Este documento descreve os conceitos necessários para escrever [gRPC](https://grpc.io/docs/guides/) aplicativos C#.</span><span class="sxs-lookup"><span data-stu-id="bba02-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="bba02-105">Os tópicos abordados aqui se aplicam a ambos [C-core](https://grpc.io/blog/grpc-stacks)-aplicativos baseados no ASP.NET Core e com base gRPC.</span><span class="sxs-lookup"><span data-stu-id="bba02-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="bba02-106">arquivo proto</span><span class="sxs-lookup"><span data-stu-id="bba02-106">proto file</span></span>

<span data-ttu-id="bba02-107">gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API.</span><span class="sxs-lookup"><span data-stu-id="bba02-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="bba02-108">Buffers de protocolo (protobuf) são usados como linguagem de Design de Interface (IDL) por padrão.</span><span class="sxs-lookup"><span data-stu-id="bba02-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="bba02-109">O *.proto* arquivo contém:</span><span class="sxs-lookup"><span data-stu-id="bba02-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="bba02-110">A definição do serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="bba02-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="bba02-111">As mensagens enviadas entre clientes e servidores.</span><span class="sxs-lookup"><span data-stu-id="bba02-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="bba02-112">Para obter mais informações sobre a sintaxe dos arquivos de protobuf, consulte o [documentação oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="bba02-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="bba02-113">Por exemplo, considere a *greet.proto* arquivo usado no [Introdução ao serviço gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="bba02-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="bba02-114">Define um `Greeter` service.</span><span class="sxs-lookup"><span data-stu-id="bba02-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="bba02-115">O `Greeter` serviço define um `SayHello` chamar.</span><span class="sxs-lookup"><span data-stu-id="bba02-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="bba02-116">`SayHello` envia um `HelloRequest` da mensagem e recebe um `HelloResponse` mensagem:</span><span class="sxs-lookup"><span data-stu-id="bba02-116">`SayHello` sends a `HelloRequest` message and receives a `HelloResponse` message:</span></span>

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcStart/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="bba02-117">Adicionar um arquivo de .proto a C\# aplicativo</span><span class="sxs-lookup"><span data-stu-id="bba02-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="bba02-118">O *.proto* arquivo está incluído em um projeto, adicionando-o para o `<Protobuf>` grupo de itens:</span><span class="sxs-lookup"><span data-stu-id="bba02-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="bba02-119">C#Suporte a ferramentas para arquivos .proto</span><span class="sxs-lookup"><span data-stu-id="bba02-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="bba02-120">O pacote de ferramentas [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) é necessária para gerar o C# ativos do *.proto* arquivos.</span><span class="sxs-lookup"><span data-stu-id="bba02-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="bba02-121">Os ativos gerados (arquivos):</span><span class="sxs-lookup"><span data-stu-id="bba02-121">The generated assets (files):</span></span>

* <span data-ttu-id="bba02-122">São gerados em uma base conforme a necessidade cada vez que o projeto é compilado.</span><span class="sxs-lookup"><span data-stu-id="bba02-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="bba02-123">Não são adicionados ao projeto ou verificadas no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="bba02-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="bba02-124">Estão contido em um artefato de compilação a *obj* directory.</span><span class="sxs-lookup"><span data-stu-id="bba02-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="bba02-125">Esse pacote é necessário por projetos do servidor e cliente.</span><span class="sxs-lookup"><span data-stu-id="bba02-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="bba02-126">`Grpc.Tools` podem ser adicionados usando o Gerenciador de pacotes no Visual Studio ou adicionando um `<PackageReference>` ao arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="bba02-126">`Grpc.Tools` can be added by using the Package Manager in Visual Studio or adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=1&range=16)]

<span data-ttu-id="bba02-127">O pacote de ferramentas não é necessário no tempo de execução, portanto, a dependência é marcada com `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="bba02-127">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

## <a name="generated-c-assets"></a><span data-ttu-id="bba02-128">Gerado C# ativos</span><span class="sxs-lookup"><span data-stu-id="bba02-128">Generated C# assets</span></span>

<span data-ttu-id="bba02-129">Gera o pacote de ferramentas do C# tipos que representam as mensagens definidas os incluídos *.proto* arquivos.</span><span class="sxs-lookup"><span data-stu-id="bba02-129">The tooling package generates the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="bba02-130">Para ativos do lado do servidor, um tipo de base abstrata de serviço é gerado.</span><span class="sxs-lookup"><span data-stu-id="bba02-130">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="bba02-131">O tipo de base contém as definições de todas as chamadas gRPC contidas na *.proto* arquivo.</span><span class="sxs-lookup"><span data-stu-id="bba02-131">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="bba02-132">Crie uma implementação de serviço concreto que deriva desse tipo de base e implementa a lógica para as chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="bba02-132">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="bba02-133">Para o `greet.proto`, o exemplo descrito anteriormente, um resumo `GreeterBase` tipo que contém uma máquina virtual `SayHello` método é gerado.</span><span class="sxs-lookup"><span data-stu-id="bba02-133">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="bba02-134">Uma implementação concreta `GreeterService` substitui o método e implementa a lógica que trata a chamada gRPC.</span><span class="sxs-lookup"><span data-stu-id="bba02-134">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="bba02-135">Para ativos do lado do cliente, um tipo concreto de cliente é gerado.</span><span class="sxs-lookup"><span data-stu-id="bba02-135">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="bba02-136">O gRPC chama o *.proto* arquivo são convertidas em métodos do tipo concreto, o que pode ser chamado.</span><span class="sxs-lookup"><span data-stu-id="bba02-136">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="bba02-137">Para o `greet.proto`, o exemplo descrito anteriormente, um concreto `GreeterClient` tipo é gerado.</span><span class="sxs-lookup"><span data-stu-id="bba02-137">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="bba02-138">Chamar `GreeterClient.SayHello` para iniciar uma chamada de gRPC para o servidor.</span><span class="sxs-lookup"><span data-stu-id="bba02-138">Call `GreeterClient.SayHello` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Client/Program.cs?highlight=9-11&name=snippet)]

<span data-ttu-id="bba02-139">Por padrão, os ativos do servidor e cliente são gerados para cada *.proto* arquivo incluído no `<Protobuf>` grupo de itens.</span><span class="sxs-lookup"><span data-stu-id="bba02-139">By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="bba02-140">Para garantir que somente os ativos do servidor são gerados em um projeto do servidor, o `GrpcServices` atributo é definido como `Server`.</span><span class="sxs-lookup"><span data-stu-id="bba02-140">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

<span data-ttu-id="bba02-141">Da mesma forma, o atributo é definido como `Client` em projetos de cliente.</span><span class="sxs-lookup"><span data-stu-id="bba02-141">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bba02-142">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bba02-142">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
