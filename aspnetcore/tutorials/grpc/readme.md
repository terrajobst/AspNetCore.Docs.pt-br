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
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029009"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="9fba8-102">Criar um cliente e um servidor gRPC no ASP.NET Core 3.0 usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fba8-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="9fba8-103">Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fba8-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="9fba8-104">No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="9fba8-105">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="9fba8-105">In this tutorial, you;</span></span>

* <span data-ttu-id="9fba8-106">Criará um servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="9fba8-107">Criará um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-107">Create a gRPC client.</span></span>
* <span data-ttu-id="9fba8-108">Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="9fba8-109">Criar um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="9fba8-109">Create a gRPC service</span></span>

* <span data-ttu-id="9fba8-110">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="9fba8-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9fba8-111">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="9fba8-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="9fba8-112">Selecione **Avançar**</span><span class="sxs-lookup"><span data-stu-id="9fba8-112">Select **Next**</span></span>
* <span data-ttu-id="9fba8-113">Nomeie o projeto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="9fba8-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="9fba8-114">É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="9fba8-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="9fba8-115">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="9fba8-115">Select **Create**</span></span>
* <span data-ttu-id="9fba8-116">Na caixa de diálogo **Criar um novo Aplicativo Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="9fba8-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="9fba8-117">Selecione **.NET Core** e **ASP.NET Core 3.0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="9fba8-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="9fba8-118">Selecione o modelo do **Serviço gRPC**.</span><span class="sxs-lookup"><span data-stu-id="9fba8-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="9fba8-119">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="9fba8-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="9fba8-120">Executar o serviço</span><span class="sxs-lookup"><span data-stu-id="9fba8-120">Run the service</span></span>

* <span data-ttu-id="9fba8-121">Pressione `Ctrl+F5` para executar o serviço gRPC sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="9fba8-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="9fba8-122">O Visual Studio executa o serviço em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="9fba8-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="9fba8-123">Os logs mostram o serviço escutando em `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9fba8-123">The logs show the service listening on `https://localhost:5001`.</span></span>

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
> <span data-ttu-id="9fba8-124">O modelo gRPC é configurado para usar [o protocolo TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="9fba8-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="9fba8-125">Os clientes gRPC precisam usar HTTPS para chamar o servidor.</span><span class="sxs-lookup"><span data-stu-id="9fba8-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="9fba8-126">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="9fba8-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="9fba8-127">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="9fba8-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="9fba8-128">Para obter mais informações, veja [gRPC e ASP.NET Core no macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span><span class="sxs-lookup"><span data-stu-id="9fba8-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="9fba8-129">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="9fba8-129">Examine the project files</span></span>

<span data-ttu-id="9fba8-130">Arquivos de projeto *GrpcGreeter*:</span><span class="sxs-lookup"><span data-stu-id="9fba8-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="9fba8-131">*greet.proto*: O arquivo *Protos/greet.proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="9fba8-132">Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="9fba8-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="9fba8-133">Pasta *Serviços*: contém a implementação do serviço `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="9fba8-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="9fba8-134">*appSettings.json*: contém dados de configuração, como o protocolo usado pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9fba8-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="9fba8-135">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9fba8-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="9fba8-136">*Program.cs*: contém o ponto de entrada para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="9fba8-137">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9fba8-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="9fba8-138">*Startup.cs*: contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9fba8-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="9fba8-139">Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="9fba8-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="9fba8-140">Criar o cliente gRPC em um aplicativo de console .NET</span><span class="sxs-lookup"><span data-stu-id="9fba8-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="9fba8-141">Abra uma segunda instância do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9fba8-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="9fba8-142">Selecione **Arquivo** > **Novo** > **Projeto** na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="9fba8-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="9fba8-143">Na caixa de diálogo **Criar um novo projeto**, selecione **Aplicativo de Console (.NET Core)** .</span><span class="sxs-lookup"><span data-stu-id="9fba8-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="9fba8-144">Selecione **Avançar**</span><span class="sxs-lookup"><span data-stu-id="9fba8-144">Select **Next**</span></span>
* <span data-ttu-id="9fba8-145">Na caixa de texto **Nome**, digite "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="9fba8-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="9fba8-146">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="9fba8-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="9fba8-147">Adicionar os pacotes necessários</span><span class="sxs-lookup"><span data-stu-id="9fba8-147">Add required packages</span></span>

<span data-ttu-id="9fba8-148">O projeto cliente gRPC requer os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="9fba8-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="9fba8-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fba8-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="9fba8-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="9fba8-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="9fba8-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="9fba8-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="9fba8-152">O pacote de ferramentas não é necessário em tempo de execução e, portanto, a dependência é marcada com `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="9fba8-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="9fba8-153">Instalar os pacotes usando o PMC (Console do Gerenciador de Pacotes) ou Gerenciar Pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="9fba8-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="9fba8-154">Opção do PMC para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="9fba8-154">PMC option to install packages</span></span>

* <span data-ttu-id="9fba8-155">No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="9fba8-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="9fba8-156">Na janela **Console do Gerenciador de Pacotes**, navegue para o diretório no qual o arquivo *GrpcGreeterClient.csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="9fba8-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="9fba8-157">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="9fba8-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="9fba8-158">Opção Gerenciar Pacotes NuGet para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="9fba8-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="9fba8-159">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="9fba8-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="9fba8-160">Selecione a guia **Procurar**.</span><span class="sxs-lookup"><span data-stu-id="9fba8-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="9fba8-161">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="9fba8-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="9fba8-162">Selecione o pacote **Grpc.Net.Client** na guia **Procurar** e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="9fba8-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="9fba8-163">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="9fba8-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="9fba8-164">Adicionar greet.proto</span><span class="sxs-lookup"><span data-stu-id="9fba8-164">Add greet.proto</span></span>

* <span data-ttu-id="9fba8-165">Crie uma pasta **Protos** no projeto do cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="9fba8-166">Copie o arquivo **Protos\greet.proto** do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="9fba8-167">Edite o arquivo de projeto *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="9fba8-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="9fba8-168">Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto**.</span><span class="sxs-lookup"><span data-stu-id="9fba8-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="9fba8-169">Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo **greet.proto**:</span><span class="sxs-lookup"><span data-stu-id="9fba8-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="9fba8-170">Criar o cliente Greeter</span><span class="sxs-lookup"><span data-stu-id="9fba8-170">Create the Greeter client</span></span>

<span data-ttu-id="9fba8-171">Compile o projeto para criar os tipos no namespace `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="9fba8-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="9fba8-172">Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.</span><span class="sxs-lookup"><span data-stu-id="9fba8-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="9fba8-173">Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="9fba8-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

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

<span data-ttu-id="9fba8-174">*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="9fba8-175">O cliente Greeter é criado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="9fba8-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="9fba8-176">Criando uma instância de `HttpClient` que contém as informações para criar a conexão com o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="9fba8-176">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="9fba8-177">Como usar o `HttpClient` para construir o cliente Greeter:</span><span class="sxs-lookup"><span data-stu-id="9fba8-177">Using the `HttpClient` to construct the Greeter client:</span></span>

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

<span data-ttu-id="9fba8-178">O cliente Greeter chama o método `SayHello` assíncrono.</span><span class="sxs-lookup"><span data-stu-id="9fba8-178">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="9fba8-179">O resultado da chamada `SayHello` é exibido:</span><span class="sxs-lookup"><span data-stu-id="9fba8-179">The result of the `SayHello` call is displayed:</span></span>

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

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="9fba8-180">Testar o cliente gRPC com o serviço de Boas-vindas do gRPC</span><span class="sxs-lookup"><span data-stu-id="9fba8-180">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="9fba8-181">No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="9fba8-181">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="9fba8-182">No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o cliente sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="9fba8-182">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="9fba8-183">O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="9fba8-183">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="9fba8-184">O serviço envia a mensagem "Olá, GreeterClient" como uma resposta.</span><span class="sxs-lookup"><span data-stu-id="9fba8-184">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="9fba8-185">A resposta "Olá, GreeterClient" é exibida no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="9fba8-185">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="9fba8-186">O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="9fba8-186">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="9fba8-187">Documentos de ajuda e próximas etapas de para gRPC</span><span class="sxs-lookup"><span data-stu-id="9fba8-187">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="9fba8-188">Introdução ao gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9fba8-188">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="9fba8-189">Serviços do gRPC com C#</span><span class="sxs-lookup"><span data-stu-id="9fba8-189">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="9fba8-190">Migrar serviços do gRPC do C Core para o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9fba8-190">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
