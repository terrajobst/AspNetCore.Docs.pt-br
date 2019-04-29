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
# <a name="tutorial-create-a-net-core-grpc-client"></a><span data-ttu-id="b7b27-104">Tutorial: Criar um cliente gRPC do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b7b27-104">Tutorial: Create a .NET Core gRPC client</span></span>

<span data-ttu-id="b7b27-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="b7b27-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="b7b27-106">Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) .NET Core que pode se comunicar com os serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="b7b27-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client that can communicate with gRPC services.</span></span>

<span data-ttu-id="b7b27-107">No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.</span><span class="sxs-lookup"><span data-stu-id="b7b27-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

<span data-ttu-id="b7b27-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="b7b27-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b7b27-109">Criará um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="b7b27-109">Create a gRPC client.</span></span>
> * <span data-ttu-id="b7b27-110">Executará o serviço no serviço de Boas-vindas do gRPC criado no tutorial anterior.</span><span class="sxs-lookup"><span data-stu-id="b7b27-110">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="b7b27-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="b7b27-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a><span data-ttu-id="b7b27-112">Criar um aplicativo de console .NET Core</span><span class="sxs-lookup"><span data-stu-id="b7b27-112">Create a .NET console application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7b27-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7b27-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b7b27-114">Siga as instruções [aqui](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio) para criar um aplicativo de console com o nome *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="b7b27-114">Follow the instructions [here](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b7b27-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7b27-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b7b27-116">Siga as instruções [aqui](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code) para criar um aplicativo de console com o nome *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="b7b27-116">Follow the instructions [here](https://docs.microsoft.com/en-us/dotnet/core/tutorials/with-visual-studio-code) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b7b27-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b7b27-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b7b27-118">Siga as instruções [aqui](https://docs.microsoft.com/en-us/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="b7b27-118">Follow the instructions [here](https://docs.microsoft.com/en-us/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a><span data-ttu-id="b7b27-119">Adicionar os pacotes necessários</span><span class="sxs-lookup"><span data-stu-id="b7b27-119">Add required packages</span></span>

<span data-ttu-id="b7b27-120">Adicione os seguintes pacotes ao projeto de cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="b7b27-120">Add the following packages to the gRPC client project:</span></span>

* <span data-ttu-id="b7b27-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), que contém a API do C# para o cliente C-core.</span><span class="sxs-lookup"><span data-stu-id="b7b27-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), which contains the C# API for the C-core client.</span></span>
* <span data-ttu-id="b7b27-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="b7b27-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="b7b27-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="b7b27-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="b7b27-124">O pacote de ferramentas não é necessário em tempo de execução e, portanto, a dependência é marcada com `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="b7b27-124">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="b7b27-125">Os pacotes podem ser adicionados com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="b7b27-125">Packages can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7b27-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7b27-126">Visual Studio</span></span>](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="b7b27-127">Opção do PMC para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="b7b27-127">PMC option to install packages</span></span>

* <span data-ttu-id="b7b27-128">No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="b7b27-128">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="b7b27-129">Na janela **Console do Gerenciador de Pacotes**, navegue para o diretório no qual o arquivo *GrpcGreeterClient.csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="b7b27-129">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="b7b27-130">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b7b27-130">Run the following command:</span></span>

    ```powershell
    Install-Package Grpc.Core
    ```

* <span data-ttu-id="b7b27-131">Repita o `Install-Package` para Google.Protobuf e Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="b7b27-131">Repeat the `Install-Package` for Google.Protobuf and Grpc.Tools</span></span>

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="b7b27-132">Opção Gerenciar Pacotes NuGet para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="b7b27-132">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="b7b27-133">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="b7b27-133">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="b7b27-134">Defina a **Origem do pacote** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="b7b27-134">Set the **Package source** to "nuget.org"</span></span>
* <span data-ttu-id="b7b27-135">Insira "Grpc.Core" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="b7b27-135">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="b7b27-136">Selecione o pacote "Grpc.Core" na guia **Procurar** e clique em **Instalar**</span><span class="sxs-lookup"><span data-stu-id="b7b27-136">Select the "Grpc.Core" package from the **Browse** tab and click **Install**</span></span>
* <span data-ttu-id="b7b27-137">Repita a etapa para Google.Protobuf e Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="b7b27-137">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b7b27-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7b27-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b7b27-139">Execute o comando a seguir do **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="b7b27-139">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package Grpc.Core
```

<span data-ttu-id="b7b27-140">Repita a etapa para Google.Protobuf e Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="b7b27-140">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b7b27-141">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b7b27-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b7b27-142">Clique com o botão direito do mouse na pasta *Pacotes* em **Painel de Soluções** > **Adicionar Pacotes...**</span><span class="sxs-lookup"><span data-stu-id="b7b27-142">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="b7b27-143">Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="b7b27-143">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="b7b27-144">Insira "Grpc.Core" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="b7b27-144">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="b7b27-145">Selecione o pacote "Grpc.Core" no painel de resultados e clique em **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="b7b27-145">Select the "Grpc.Core" package from the results pane and click **Add Package**</span></span>
* <span data-ttu-id="b7b27-146">Repita a etapa para Google.Protobuf e Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="b7b27-146">Repeat for Google.Protobuf and Grpc.Tools</span></span>

---

## <a name="add-the-greetproto-file"></a><span data-ttu-id="b7b27-147">Adicionar o arquivo greet.proto</span><span class="sxs-lookup"><span data-stu-id="b7b27-147">Add the greet.proto file</span></span>

<span data-ttu-id="b7b27-148">Copie o arquivo **Protos\greet.proto** do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="b7b27-148">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span> <span data-ttu-id="b7b27-149">Adicione o arquivo **greet.proto** ao grupo de itens `<Protobuf>` do arquivo de projeto GrpcGreeterClient:</span><span class="sxs-lookup"><span data-stu-id="b7b27-149">Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:</span></span>

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> <span data-ttu-id="b7b27-150">Abra o arquivo de projeto do GrpcGreeterClient clicando com o botão direito do mouse no projeto e selecionando a opção **Editar GrpcGreeterClient.csproj** no menu suspenso.</span><span class="sxs-lookup"><span data-stu-id="b7b27-150">You can open the project file of GrpcGreeterClient by right-clicking the project and selecting the **Edit GrpcGreeterClient.csproj** option from the dropdown menu.</span></span>
>
> ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/edit_csproj.png)
>
> <span data-ttu-id="b7b27-152">Como alternativa, você pode navegar para o diretório GrpcGreeterClient e editar o `GrpcGreeterClient.csproj` com seu editor favorito.</span><span class="sxs-lookup"><span data-stu-id="b7b27-152">Alternatively, you can navigate to the GrpcGreeterClient directory and edit the `GrpcGreeterClient.csproj` with your favorite editor.</span></span>

<span data-ttu-id="b7b27-153">O atributo `GrpcServices="Client"` é adicionado, de modo que somente os ativos de cliente C# são gerados para o arquivo protobuf incluído.</span><span class="sxs-lookup"><span data-stu-id="b7b27-153">The `GrpcServices="Client"` attribute is added so that only the C# client assets are generated for the included protobuf file.</span></span> <span data-ttu-id="b7b27-154">Compile o projeto de cliente para disparar a geração dos ativos de cliente C#.</span><span class="sxs-lookup"><span data-stu-id="b7b27-154">Build the client project to trigger the generation of the C# client assets.</span></span>

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a><span data-ttu-id="b7b27-155">Criar um GreeterClient e invocar a chamada unária SayHello</span><span class="sxs-lookup"><span data-stu-id="b7b27-155">Create a GreeterClient and invoke the SayHello unary call</span></span>

<span data-ttu-id="b7b27-156">Adicione o seguinte código ao método `Main` do arquivo `Program.cs` do projeto de cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="b7b27-156">Add the following code to `Main` method of the `Program.cs` file of the gRPC client project:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="b7b27-157">Para acessar os tipos necessários, as seguintes instruções using são necessárias:</span><span class="sxs-lookup"><span data-stu-id="b7b27-157">To access the required types the following using statements are required:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

<span data-ttu-id="b7b27-158">O GreeterClient é criado por meio da criação de uma instância de um `Channel` que contém as informações para criação da conexão com o serviço gRPC e seu uso para construção do `GreeterClient`:</span><span class="sxs-lookup"><span data-stu-id="b7b27-158">The GreeterClient is created by instantiating a `Channel` containing the information for creating the connection to the gRPC service and using it to construct the `GreeterClient`:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="b7b27-159">O GreeterClient contém a chamada unária `SayHello` que pode ser invocada de forma assíncrona:</span><span class="sxs-lookup"><span data-stu-id="b7b27-159">The GreeterClient contains the unary call `SayHello` which can be invoked asynchronously:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

<span data-ttu-id="b7b27-160">Os resultados da chamada `SayHello` são armazenados em `reply`, que, em seguida, pode ser exibido:</span><span class="sxs-lookup"><span data-stu-id="b7b27-160">The results of the `SayHello` call is stored in `reply` which can then be displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

<span data-ttu-id="b7b27-161">O `Channel` usado pelo cliente deverá ser desligado quando as operações concluírem a liberação de todos os recursos:</span><span class="sxs-lookup"><span data-stu-id="b7b27-161">The `Channel` used by the client should be shut down when operations have finished to release all resources:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> <span data-ttu-id="b7b27-162">Você precisa compilar o projeto para que os tipos no namespace **Greeter** sejam resolvidos.</span><span class="sxs-lookup"><span data-stu-id="b7b27-162">You will need to build the project before the types in the **Greeter** namespace can be resolved.</span></span> <span data-ttu-id="b7b27-163">Esses tipos são gerados automaticamente durante o build e só ficam disponíveis após a execução de um build.</span><span class="sxs-lookup"><span data-stu-id="b7b27-163">These types are generated automatically during build and are not be available before a build is run.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="b7b27-164">Testar o cliente gRPC com o serviço de Boas-vindas do gRPC</span><span class="sxs-lookup"><span data-stu-id="b7b27-164">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7b27-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7b27-165">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7b27-166">Verifique se o serviço de Boas-vindas criado no tutorial anterior está em execução.</span><span class="sxs-lookup"><span data-stu-id="b7b27-166">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="b7b27-167">Depois que o serviço estiver em execução, retorne ao projeto **GrpcGreeterClient** para defini-lo como o Projeto de Inicialização.</span><span class="sxs-lookup"><span data-stu-id="b7b27-167">Once the service is running, return to the **GrpcGreeterClient** project set it as the Startup Project.</span></span> <span data-ttu-id="b7b27-168">Pressione Ctrl+F5 para executar o cliente sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="b7b27-168">Press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="b7b27-169">O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="b7b27-169">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="b7b27-170">O serviço enviará uma mensagem "Olá, GreeterClient" como uma resposta exibida no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="b7b27-170">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/client.png)

  <span data-ttu-id="b7b27-172">O serviço registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="b7b27-172">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b7b27-174">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b7b27-174">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b7b27-175">Verifique se o serviço de Boas-vindas criado no tutorial anterior está em execução.</span><span class="sxs-lookup"><span data-stu-id="b7b27-175">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="b7b27-176">Execute o projeto do cliente GrpcGreeter.Server na linha de comando separada usando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b7b27-176">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="b7b27-177">O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="b7b27-177">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="b7b27-178">O serviço enviará uma mensagem "Olá, GreeterClient" como uma resposta exibida no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="b7b27-178">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="b7b27-179">O serviço registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="b7b27-179">The service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="b7b27-180">Examinar os arquivos de projeto do projeto gRPC</span><span class="sxs-lookup"><span data-stu-id="b7b27-180">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="b7b27-181">Arquivo GrpcGreeterClient do cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="b7b27-181">gRPC client GrpcGreeterClient file:</span></span>

<span data-ttu-id="b7b27-182">*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="b7b27-182">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7b27-183">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b7b27-183">Additional resources</span></span>

<span data-ttu-id="b7b27-184">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="b7b27-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b7b27-185">Criará um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="b7b27-185">Create a gRPC client.</span></span>
> * <span data-ttu-id="b7b27-186">Executará o serviço no serviço de Boas-vindas do gRPC criado no tutorial anterior.</span><span class="sxs-lookup"><span data-stu-id="b7b27-186">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="b7b27-187">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="b7b27-187">Examine the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b7b27-188">Anterior: Criar um serviço de Boas-vindas do gRPC</span><span class="sxs-lookup"><span data-stu-id="b7b27-188">Previous: Create a gRPC Greeter Service</span></span>](xref:tutorials/grpc/grpc-start)
