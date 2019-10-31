---
page_type: sample
description: Este tutorial mostra como criar um serviço gRPC e um cliente gRPC no ASP.NET Core.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: a281adc3b1fe90eeb32c1185750f911af683af83
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029009"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>Criar um cliente e um servidor gRPC no ASP.NET Core 3.0 usando o Visual Studio

Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.

No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.

Neste tutorial, você:

* Criará um servidor gRPC.
* Criará um cliente gRPC.
* Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.

## <a name="create-a-grpc-service"></a>Criar um serviço gRPC

* No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core**.
* Selecione **Avançar**
* Nomeie o projeto **GrpcGreeter**. É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.
* Selecione **Criar**
* Na caixa de diálogo **Criar um novo Aplicativo Web ASP.NET Core**:
  * Selecione **.NET Core** e **ASP.NET Core 3.0** nos menus suspensos. 
  * Selecione o modelo do **Serviço gRPC**.
  * Selecione **Criar**

### <a name="run-the-service"></a>Executar o serviço

* Pressione `Ctrl+F5` para executar o serviço gRPC sem o depurador.

  O Visual Studio executa o serviço em um prompt de comando.

Os logs mostram o serviço escutando em `https://localhost:5001`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> O modelo gRPC é configurado para usar [o protocolo TLS](https://tools.ietf.org/html/rfc5246). Os clientes gRPC precisam usar HTTPS para chamar o servidor.
>
> O macOS não é compatível com gRPC do ASP.NET Core com TLS. É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS. Para obter mais informações, veja [gRPC e ASP.NET Core no macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).

### <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Arquivos de projeto *GrpcGreeter*:

* *greet.proto*: O arquivo *Protos/greet.proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC. Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).
* Pasta *Serviços*: contém a implementação do serviço `Greeter`.
* *appSettings.json*: contém dados de configuração, como o protocolo usado pelo Kestrel. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.
* *Program.cs*: contém o ponto de entrada para o serviço gRPC. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.
* *Startup.cs*: contém código que configura o comportamento do aplicativo. Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Criar o cliente gRPC em um aplicativo de console .NET

* Abra uma segunda instância do Visual Studio.
* Selecione **Arquivo** > **Novo** > **Projeto** na barra de menus.
* Na caixa de diálogo **Criar um novo projeto**, selecione **Aplicativo de Console (.NET Core)** .
* Selecione **Avançar**
* Na caixa de texto **Nome**, digite "GrpcGreeterClient".
* Selecione **Criar**.

### <a name="add-required-packages"></a>Adicionar os pacotes necessários

O projeto cliente gRPC requer os seguintes pacotes:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf. O pacote de ferramentas não é necessário em tempo de execução e, portanto, a dependência é marcada com `PrivateAssets="All"`.

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
* Insira **Grpc.Net.Client** na caixa de pesquisa.
* Selecione o pacote **Grpc.Net.Client** na guia **Procurar** e selecione **Instalar**.
* Repita para `Google.Protobuf` e `Grpc.Tools`.

### <a name="add-greetproto"></a>Adicionar greet.proto

* Crie uma pasta **Protos** no projeto do cliente gRPC.
* Copie o arquivo **Protos\greet.proto** do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.
* Edite o arquivo de projeto *GrpcGreeterClient.csproj*:

  Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto**.

* Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo **greet.proto**:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Criar o cliente Greeter

Compile o projeto para criar os tipos no namespace `GrpcGreeter`. Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.

Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            var httpClient = new HttpClient();
            // The port number(5001) must match the port of the gRPC server.
            httpClient.BaseAddress = new Uri("https://localhost:5001");
            var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.

O cliente Greeter é criado da seguinte forma:

* Criando uma instância de `HttpClient` que contém as informações para criar a conexão com o serviço gRPC.
* Como usar o `HttpClient` para construir o cliente Greeter:

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
    var reply = await client.SayHelloAsync(
                      new HelloRequest { Name = "GreeterClient" });
    Console.WriteLine("Greeting: " + reply.Message);
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

O cliente Greeter chama o método `SayHello` assíncrono. O resultado da chamada `SayHello` é exibido:

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
    var reply = await client.SayHelloAsync(
                      new HelloRequest { Name = "GreeterClient" });
    Console.WriteLine("Greeting: " + reply.Message);
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testar o cliente gRPC com o serviço de Boas-vindas do gRPC

* No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.
* No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o cliente sem o depurador.

O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient". O serviço envia a mensagem "Olá, GreeterClient" como uma resposta. A resposta "Olá, GreeterClient" é exibida no prompt de comando:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="docs-help--next-steps-for-grpc"></a>Documentos de ajuda e próximas etapas de para gRPC

* [Introdução ao gRPC no ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [Serviços do gRPC com C#](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [Migrar serviços do gRPC do C Core para o ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
