---
title: Criar um cliente e um servidor gRPC do .NET Core no ASP.NET Core
author: juntaoluo
description: Este tutorial mostra como criar um serviço gRPC e um cliente gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo pronto e adicionar uma chamada de streaming duplex.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 8507c3dcefeb61a4dd34a1ff967c082d287cf646
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555891"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Tutorial: Criar um cliente e um servidor gRPC no ASP.NET Core

Por [John Luo](https://github.com/juntaoluo)

Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.

No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).

Neste tutorial, você:

> [!div class="checklist"]
> * Criará um servidor gRPC.
> * Criará um cliente gRPC.
> * Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a>Criar um serviço gRPC

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core**.
* Selecione **Avançar**
* Nomeie o projeto **GrpcGreeter**. É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.
* Selecione **Criar**
* Na caixa de diálogo **Criar um novo Aplicativo Web ASP.NET Core**:
  * Selecione **.NET Core** e **ASP.NET Core 3.0** nos menus suspensos. 
  * Selecione o modelo do **Serviço gRPC**.
  * Selecione **Criar**

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.
* Execute os seguintes comandos:

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter*.
  * O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.

  Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de "GrpcGreeter". Deseja adicioná-los?**
* Selecione **Sim**

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Em um terminal, execute os seguintes comandos:

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um serviço gRPC.

### <a name="open-the-project"></a>Abrir o projeto

No Visual Studio, selecione **Arquivo > Abrir** e depois o arquivo *GrpcGreeter.sln*.

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a>Executar o serviço

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Pressione `Ctrl+F5` para executar o serviço gRPC sem o depurador.

  O Visual Studio executa o serviço em um prompt de comando.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Execute o projeto de Boas-vindas gRPC *GrpcGreeter* por meio da linha de comando usando `dotnet run`.

<!-- End of combined VS/Mac tabs -->

---

Os logs mostram o serviço escutando em `http://localhost:50051`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Arquivos de projeto *GrpcGreeter*:

* *greet.proto*: O arquivo *Protos/greet.proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC. Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).
* Pasta *Serviços*: contém a implementação do serviço `Greeter`.
* *appSettings.json*: contém dados de configuração, como o protocolo usado pelo Kestrel. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.
* *Program.cs*: contém o ponto de entrada para o serviço gRPC. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.
* *Startup.cs*: contém código que configura o comportamento do aplicativo. Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Criar o cliente gRPC em um aplicativo de console .NET

## <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Abra uma segunda instância do Visual Studio.
* Selecione **Arquivo** > **Novo** > **Projeto** na barra de menus.
* Na caixa de diálogo **Criar um novo projeto**, selecione **Aplicativo de Console (.NET Core)** .
* Selecione **Avançar**
* Na caixa de texto **Nome**, digite "GrpcGreeterClient".
* Selecione **Criar**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.
* Execute os seguintes comandos:

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Siga as instruções [aqui](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient*.

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a>Adicionar os pacotes necessários

O projeto cliente gRPC requer os seguintes pacotes:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf. O pacote de ferramentas não é necessário em tempo de execução e, portanto, a dependência é marcada com `PrivateAssets="All"`.

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Instalar os pacotes usando o PMC (Console do Gerenciador de Pacotes) ou Gerenciar Pacotes NuGet.

#### <a name="pmc-option-to-install-packages"></a>Opção do PMC para instalar pacotes

* No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**
* Na janela **Console do Gerenciador de Pacotes**, navegue para o diretório no qual o arquivo *GrpcGreeterClient.csproj* existe.
* Execute os seguintes comandos:

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Opção Gerenciar Pacotes NuGet para instalar pacotes

* Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**
* Selecione a guia **Procurar**.
* Insira **Grpc.Core** na caixa de pesquisa.
* Selecione o pacote **Grpc.Core** na guia **Procurar** e selecione **Instalar**.
* Repita para `Google.Protobuf` e `Grpc.Tools`.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute os comandos a seguir no **Terminal Integrado**:

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse na pasta **Pacotes** em **Painel de Soluções** > **Adicionar Pacotes**
* Insira **Grpc.Net.Client** na caixa de pesquisa.
* Selecione o pacote **Grpc.Net.Client** no painel de resultados e selecione **Adicionar Pacote**
* Repita para `Google.Protobuf` e `Grpc.Tools`.

---

### <a name="add-greetproto"></a>Adicionar greet.proto

* Crie uma pasta **Protos** no projeto do cliente gRPC.
* Copie o arquivo **Protos\greet.proto** do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.
* Edite o arquivo de projeto *GrpcGreeterClient.csproj*:

  # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

  Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto**.

  # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

  Selecione o arquivo *GrpcGreeterClient.csproj*.

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  Clique com o botão direito do mouse no projeto e selecione **Ferramentas > Editar Arquivo**.

  ---

* Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo **greet.proto**:

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Criar o cliente Greeter

Compile o projeto para criar os tipos no namespace `GrpcGreeter`. Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.

Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.

O cliente Greeter é criado da seguinte forma:

* Criando uma instância de `HttpClient` que contém as informações para criar a conexão com o serviço gRPC.
* Como usar o `HttpClient` para construir o cliente Greeter:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

O cliente Greeter chama o método `SayHello` assíncrono. O resultado da chamada `SayHello` é exibido:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testar o cliente gRPC com o serviço de Boas-vindas do gRPC

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.
* No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Inicie o serviço Greeter.
* Inicie o cliente.

<!-- End of combined VS/Mac tabs -->

---

O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient". O serviço envia a mensagem "Olá, GreeterClient" como uma resposta. A resposta "Olá, GreeterClient" é exibida no prompt de comando:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="next-steps"></a>Próximas etapas

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
