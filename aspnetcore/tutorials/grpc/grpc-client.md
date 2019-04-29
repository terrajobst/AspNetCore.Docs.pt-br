---
title: 'Tutorial: Criar um cliente gRPC do .NET Core'
author: juntaoluo
description: Esta série de tutoriais mostra como criar um serviço gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo pronto e adicionar uma chamada de streaming duplex.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 4/10/2019
uid: tutorials/grpc/grpc-client
ms.openlocfilehash: 031afbfaf097c518a85400b0b6abbc135c1bc611
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59674123"
---
# <a name="tutorial-create-a-net-core-grpc-client"></a>Tutorial: Criar um cliente gRPC do .NET Core

Por [John Luo](https://github.com/juntaoluo)

Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) .NET Core que pode se comunicar com os serviços gRPC.

No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

Neste tutorial, você:

> [!div class="checklist"]
> * Criará um cliente gRPC.
> * Executará o serviço no serviço de Boas-vindas do gRPC criado no tutorial anterior.
> * Examinar os arquivos de projeto.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a>Criar um aplicativo de console .NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Siga as instruções [aqui](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio) para criar um aplicativo de console com o nome *GrpcGreeterClient*.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Siga as instruções [aqui](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code) para criar um aplicativo de console com o nome *GrpcGreeterClient*.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Siga as instruções [aqui](https://docs.microsoft.com/en-us/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient*.

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a>Adicionar os pacotes necessários

Adicione os seguintes pacotes ao projeto de cliente gRPC:

* [Grpc.Core](https://www.nuget.org/packages/Grpc.Core), que contém a API do C# para o cliente C-core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf. O pacote de ferramentas não é necessário em tempo de execução e, portanto, a dependência é marcada com `PrivateAssets="All"`.

Os pacotes podem ser adicionados com as seguintes abordagens:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a>Opção do PMC para instalar pacotes

* No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**
* Na janela **Console do Gerenciador de Pacotes**, navegue para o diretório no qual o arquivo *GrpcGreeterClient.csproj* existe.
* Execute o seguinte comando:

    ```powershell
    Install-Package Grpc.Core
    ```

* Repita o `Install-Package` para Google.Protobuf e Grpc.Tools

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a>Opção Gerenciar Pacotes NuGet para instalar pacotes

* Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**
* Defina a **Origem do pacote** para "nuget.org"
* Insira "Grpc.Core" na caixa de pesquisa
* Selecione o pacote "Grpc.Core" na guia **Procurar** e clique em **Instalar**
* Repita a etapa para Google.Protobuf e Grpc.Tools

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o comando a seguir do **Terminal Integrado**:

```console
dotnet add TodoApi.csproj package Grpc.Core
```

Repita a etapa para Google.Protobuf e Grpc.Tools

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse na pasta *Pacotes* em **Painel de Soluções** > **Adicionar Pacotes...**
* Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"
* Insira "Grpc.Core" na caixa de pesquisa
* Selecione o pacote "Grpc.Core" no painel de resultados e clique em **Adicionar Pacote**
* Repita a etapa para Google.Protobuf e Grpc.Tools

---

## <a name="add-the-greetproto-file"></a>Adicionar o arquivo greet.proto

Copie o arquivo **Protos\greet.proto** do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC. Adicione o arquivo **greet.proto** ao grupo de itens `<Protobuf>` do arquivo de projeto GrpcGreeterClient:

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> Abra o arquivo de projeto do GrpcGreeterClient clicando com o botão direito do mouse no projeto e selecionando a opção **Editar GrpcGreeterClient.csproj** no menu suspenso.
>
> ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/edit_csproj.png)
>
> Como alternativa, você pode navegar para o diretório GrpcGreeterClient e editar o `GrpcGreeterClient.csproj` com seu editor favorito.

O atributo `GrpcServices="Client"` é adicionado, de modo que somente os ativos de cliente C# são gerados para o arquivo protobuf incluído. Compile o projeto de cliente para disparar a geração dos ativos de cliente C#.

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a>Criar um GreeterClient e invocar a chamada unária SayHello

Adicione o seguinte código ao método `Main` do arquivo `Program.cs` do projeto de cliente gRPC:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

Para acessar os tipos necessários, as seguintes instruções using são necessárias:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

O GreeterClient é criado por meio da criação de uma instância de um `Channel` que contém as informações para criação da conexão com o serviço gRPC e seu uso para construção do `GreeterClient`:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

O GreeterClient contém a chamada unária `SayHello` que pode ser invocada de forma assíncrona:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

Os resultados da chamada `SayHello` são armazenados em `reply`, que, em seguida, pode ser exibido:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

O `Channel` usado pelo cliente deverá ser desligado quando as operações concluírem a liberação de todos os recursos:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> Você precisa compilar o projeto para que os tipos no namespace **Greeter** sejam resolvidos. Esses tipos são gerados automaticamente durante o build e só ficam disponíveis após a execução de um build.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testar o cliente gRPC com o serviço de Boas-vindas do gRPC

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Verifique se o serviço de Boas-vindas criado no tutorial anterior está em execução.

* Depois que o serviço estiver em execução, retorne ao projeto **GrpcGreeterClient** para defini-lo como o Projeto de Inicialização. Pressione Ctrl+F5 para executar o cliente sem o depurador.

  O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient". O serviço enviará uma mensagem "Olá, GreeterClient" como uma resposta exibida no prompt de comando.

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/client.png)

  O serviço registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Verifique se o serviço de Boas-vindas criado no tutorial anterior está em execução.

* Execute o projeto do cliente GrpcGreeter.Server na linha de comando separada usando `dotnet run`.

O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient". O serviço enviará uma mensagem "Olá, GreeterClient" como uma resposta exibida no prompt de comando.

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

O serviço registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.

```console
dbug: Grpc.AspNetCore.Server.Internal.GrpcServiceBinder[1]
      Added gRPC method 'SayHello' to service 'Greet.Greeter'. Method type: 'Unary', route pattern: '/Greet.Greeter/SayHello'.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\Docs\aspnetcore\tutorials\grpc\grpc-start\samples\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 194.5798ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a>Examinar os arquivos de projeto do projeto gRPC

Arquivo GrpcGreeterClient do cliente gRPC:

*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.

## <a name="additional-resources"></a>Recursos adicionais

Neste tutorial, você:

> [!div class="checklist"]
> * Criará um cliente gRPC.
> * Executará o serviço no serviço de Boas-vindas do gRPC criado no tutorial anterior.
> * Examinar os arquivos de projeto.

> [!div class="step-by-step"]
> [Anterior: Criar um serviço de Boas-vindas do gRPC](xref:tutorials/grpc/grpc-start)
