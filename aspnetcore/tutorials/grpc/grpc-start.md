---
title: 'Tutorial: Introdução ao gRPC no ASP.NET Core'
author: juntaoluo
description: Esta série de tutoriais mostra como criar um serviço gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo proto e adicionar uma chamada de streaming duplex.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 5f7a2f6b57804b3295b23c322dcbac553b05528b
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320050"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a>Tutorial: Introdução ao serviço de gRPC no ASP.NET Core

Por [John Luo](https://github.com/juntaoluo)

Este tutorial ensina os conceitos básicos da criação de um serviço gRPC no ASP.NET Core.

No final, você terá um serviço gRPC que ecoa as saudações.

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

Neste tutorial, você:

> [!div class="checklist"]
> * Criar um serviço gRPC.
> * Executar o serviço.
> * Examinar os arquivos de projeto.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a>Criar um serviço gRPC

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Crie um novo Aplicativo Web ASP.NET Core.
  ![novo aplicativo Web ASP.NET Core](grpc-start/_static/np_3_0.1.png)
* Nomeie o projeto **GrpcGreeter**. É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.
  ![novo aplicativo Web ASP.NET Core](grpc-start/_static/np_3_0.2.png)
* Selecione **.NET Core** e **ASP.NET Core 3.0** na lista suspensa. Escolha o modelo do **serviço gRPC**.

  O seguinte projeto inicial é criado:

  ![Gerenciador de Soluções](grpc-start/_static/se3.0.png)

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

### <a name="test-the-service"></a>Testar o serviço

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Assegure-se de que o **GrpcGreeter.Server** esteja configurado como o Projeto de Inicialização e pressione Ctrl+F5 para executar o serviço gRPC sem o depurador.

  O Visual Studio executa o serviço em um prompt de comando. Os logs mostram que o serviço começou a escutar em `http://localhost:50051`.

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/server_start.png)

* Quando o serviço estiver em execução, defina o **GrpcGreeter.Client** como o Projeto de Inicialização e pressione Ctrl+F5 para executar o cliente sem depurador.

  O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient". O serviço enviará uma mensagem "Olá, GreeterClient" como uma resposta exibida no prompt de comando.

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/client.png)

  O serviço registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Execute o projeto do servidor GrpcGreeter.Server na linha de comando usando `dotnet run`. Os logs mostram que o serviço começou a escutar em `http://localhost:50051`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\example\GrpcGreeter\GrpcGreeter.Server
```

* Execute o projeto do cliente GrpcGreeter.Server na linha de comando separada usando `dotnet run`.

O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient". O serviço enviará uma mensagem "Olá, GreeterClient" como uma resposta exibida no prompt de comando.

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

O serviço registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\tp\GrpcGreeter\GrpcGreeter.Server
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 107.46730000000001ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a>Examinar os arquivos de projeto do projeto gRPC

Arquivos GrpcGreeter.Server:

* greet.proto: O arquivo *Protos/greet.proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC. Para obter mais informações, consulte <xref:grpc/index>.
* Pasta *Serviços*: contém a implementação do serviço `Greeter`.
* *appSettings.json*: contém dados de configuração, como o protocolo usado pelo Kestrel. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.
* *Program.cs*: contém o ponto de entrada para o serviço gRPC. Para obter mais informações, consulte <xref:fundamentals/host/web-host>.
* Startup.cs

contém código que configura o comportamento do aplicativo. Para obter mais informações, consulte <xref:fundamentals/startup>.

Arquivo GrpcGreeter.Client do cliente gRPC:

*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.

Neste tutorial, você:

> [!div class="checklist"]
> * Criou um serviço gRPC.
> * Executou o serviço e um cliente para testar o serviço.
> * Examinou os arquivos de projeto.
