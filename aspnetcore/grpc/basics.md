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
# <a name="grpc-services-with-c"></a>Serviços de gRPC com C\#

Este documento descreve os conceitos necessários para escrever [gRPC](https://grpc.io/docs/guides/) aplicativos C#. Os tópicos abordados aqui se aplicam a ambos [C-core](https://grpc.io/blog/grpc-stacks)-aplicativos baseados no ASP.NET Core e com base gRPC.

## <a name="proto-file"></a>arquivo proto

gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API. Buffers de protocolo (protobuf) são usados como linguagem de Design de Interface (IDL) por padrão. O *.proto* arquivo contém:

* A definição do serviço gRPC.
* As mensagens enviadas entre clientes e servidores.

Para obter mais informações sobre a sintaxe dos arquivos de protobuf, consulte o [documentação oficial (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Por exemplo, considere a *greet.proto* arquivo usado no [Introdução ao serviço gRPC](xref:tutorials/grpc/grpc-start):

* Define um `Greeter` service.
* O `Greeter` serviço define um `SayHello` chamar.
* `SayHello` envia um `HelloRequest` da mensagem e recebe um `HelloResponse` mensagem:

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcStart/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>Adicionar um arquivo de .proto a C\# aplicativo

O *.proto* arquivo está incluído em um projeto, adicionando-o para o `<Protobuf>` grupo de itens:

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

## <a name="c-tooling-support-for-proto-files"></a>C#Suporte a ferramentas para arquivos .proto

O pacote de ferramentas [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) é necessária para gerar o C# ativos do *.proto* arquivos. Os ativos gerados (arquivos):

* São gerados em uma base conforme a necessidade cada vez que o projeto é compilado.
* Não são adicionados ao projeto ou verificadas no controle do código-fonte.
* Estão contido em um artefato de compilação a *obj* directory.

Esse pacote é necessário por projetos do servidor e cliente. `Grpc.Tools` podem ser adicionados usando o Gerenciador de pacotes no Visual Studio ou adicionando um `<PackageReference>` ao arquivo de projeto:

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=1&range=16)]

O pacote de ferramentas não é necessário no tempo de execução, portanto, a dependência é marcada com `PrivateAssets="All"`.

## <a name="generated-c-assets"></a>Gerado C# ativos

Gera o pacote de ferramentas do C# tipos que representam as mensagens definidas os incluídos *.proto* arquivos.

Para ativos do lado do servidor, um tipo de base abstrata de serviço é gerado. O tipo de base contém as definições de todas as chamadas gRPC contidas na *.proto* arquivo. Crie uma implementação de serviço concreto que deriva desse tipo de base e implementa a lógica para as chamadas gRPC. Para o `greet.proto`, o exemplo descrito anteriormente, um resumo `GreeterBase` tipo que contém uma máquina virtual `SayHello` método é gerado. Uma implementação concreta `GreeterService` substitui o método e implementa a lógica que trata a chamada gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Services/GreeterService.cs?name=snippet)]

Para ativos do lado do cliente, um tipo concreto de cliente é gerado. O gRPC chama o *.proto* arquivo são convertidas em métodos do tipo concreto, o que pode ser chamado. Para o `greet.proto`, o exemplo descrito anteriormente, um concreto `GreeterClient` tipo é gerado. Chamar `GreeterClient.SayHello` para iniciar uma chamada de gRPC para o servidor.

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Client/Program.cs?highlight=9-11&name=snippet)]

Por padrão, os ativos do servidor e cliente são gerados para cada *.proto* arquivo incluído no `<Protobuf>` grupo de itens. Para garantir que somente os ativos do servidor são gerados em um projeto do servidor, o `GrpcServices` atributo é definido como `Server`.

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/GrpcGreeter.Server.csproj?highlight=2&range=7-10)]

Da mesma forma, o atributo é definido como `Client` em projetos de cliente.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
