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
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="8c00c-104">Tutorial: Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c00c-104">Tutorial: Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="8c00c-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="8c00c-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="8c00c-106">Este tutorial ensina os conceitos básicos da criação de um serviço gRPC no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c00c-106">This tutorial teaches the basics of building a gRPC service on ASP.NET Core.</span></span>

<span data-ttu-id="8c00c-107">No final, você terá um serviço gRPC que ecoa as saudações.</span><span class="sxs-lookup"><span data-stu-id="8c00c-107">At the end, you'll have a gRPC service that echoes greetings.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

<span data-ttu-id="8c00c-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="8c00c-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8c00c-109">Criar um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c00c-109">Create a gRPC service.</span></span>
> * <span data-ttu-id="8c00c-110">Executar o serviço.</span><span class="sxs-lookup"><span data-stu-id="8c00c-110">Run the service.</span></span>
> * <span data-ttu-id="8c00c-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="8c00c-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="8c00c-112">Criar um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="8c00c-112">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8c00c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c00c-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c00c-114">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="8c00c-114">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8c00c-115">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c00c-115">Create a new ASP.NET Core Web Application.</span></span>
  <span data-ttu-id="8c00c-116">![novo aplicativo Web ASP.NET Core](grpc-start/_static/np_3_0.1.png)</span><span class="sxs-lookup"><span data-stu-id="8c00c-116">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.1.png)</span></span>
* <span data-ttu-id="8c00c-117">Nomeie o projeto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="8c00c-117">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="8c00c-118">É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="8c00c-118">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="8c00c-119">![novo aplicativo Web ASP.NET Core](grpc-start/_static/np_3_0.2.png)</span><span class="sxs-lookup"><span data-stu-id="8c00c-119">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.2.png)</span></span>
* <span data-ttu-id="8c00c-120">Selecione **.NET Core** e **ASP.NET Core 3.0** na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="8c00c-120">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown.</span></span> <span data-ttu-id="8c00c-121">Escolha o modelo do **serviço gRPC**.</span><span class="sxs-lookup"><span data-stu-id="8c00c-121">Choose the **gRPC Service** template.</span></span>

  <span data-ttu-id="8c00c-122">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="8c00c-122">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8c00c-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c00c-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8c00c-125">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8c00c-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8c00c-126">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="8c00c-126">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="8c00c-127">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="8c00c-127">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="8c00c-128">O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="8c00c-128">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="8c00c-129">O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8c00c-129">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="8c00c-130">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de "GrpcGreeter". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="8c00c-130">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="8c00c-131">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="8c00c-131">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8c00c-132">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8c00c-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8c00c-133">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="8c00c-133">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="8c00c-134">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c00c-134">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="8c00c-135">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="8c00c-135">Open the project</span></span>

<span data-ttu-id="8c00c-136">No Visual Studio, selecione **Arquivo > Abrir** e depois o arquivo *GrpcGreeter.sln*.</span><span class="sxs-lookup"><span data-stu-id="8c00c-136">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="test-the-service"></a><span data-ttu-id="8c00c-137">Testar o serviço</span><span class="sxs-lookup"><span data-stu-id="8c00c-137">Test the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8c00c-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c00c-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c00c-139">Assegure-se de que o **GrpcGreeter.Server** esteja configurado como o Projeto de Inicialização e pressione Ctrl+F5 para executar o serviço gRPC sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="8c00c-139">Ensure the **GrpcGreeter.Server** is set as the Startup Project and press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="8c00c-140">O Visual Studio executa o serviço em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="8c00c-140">Visual Studio runs the service in a command prompt.</span></span> <span data-ttu-id="8c00c-141">Os logs mostram que o serviço começou a escutar em `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="8c00c-141">The logs show that the service started listening on `http://localhost:50051`.</span></span>

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/server_start.png)

* <span data-ttu-id="8c00c-143">Quando o serviço estiver em execução, defina o **GrpcGreeter.Client** como o Projeto de Inicialização e pressione Ctrl+F5 para executar o cliente sem depurador.</span><span class="sxs-lookup"><span data-stu-id="8c00c-143">Once the service is running, set the **GrpcGreeter.Client** is set as the Startup Project and press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="8c00c-144">O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="8c00c-144">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="8c00c-145">O serviço enviará uma mensagem "Olá, GreeterClient" como uma resposta exibida no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="8c00c-145">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/client.png)

  <span data-ttu-id="8c00c-147">O serviço registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="8c00c-147">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="8c00c-149">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8c00c-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8c00c-150">Execute o projeto do servidor GrpcGreeter.Server na linha de comando usando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8c00c-150">Run the Server project GrpcGreeter.Server from the command line using `dotnet run`.</span></span> <span data-ttu-id="8c00c-151">Os logs mostram que o serviço começou a escutar em `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="8c00c-151">The logs show that the service started listening on `http://localhost:50051`.</span></span>

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

* <span data-ttu-id="8c00c-152">Execute o projeto do cliente GrpcGreeter.Server na linha de comando separada usando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8c00c-152">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="8c00c-153">O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="8c00c-153">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="8c00c-154">O serviço enviará uma mensagem "Olá, GreeterClient" como uma resposta exibida no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="8c00c-154">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="8c00c-155">O serviço registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="8c00c-155">The service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="8c00c-156">Examinar os arquivos de projeto do projeto gRPC</span><span class="sxs-lookup"><span data-stu-id="8c00c-156">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="8c00c-157">Arquivos GrpcGreeter.Server:</span><span class="sxs-lookup"><span data-stu-id="8c00c-157">GrpcGreeter.Server files:</span></span>

* <span data-ttu-id="8c00c-158">greet.proto: O arquivo *Protos/greet.proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c00c-158">greet.proto: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="8c00c-159">Para obter mais informações, consulte <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="8c00c-159">For more information, see <xref:grpc/index>.</span></span>
* <span data-ttu-id="8c00c-160">Pasta *Serviços*: contém a implementação do serviço `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="8c00c-160">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="8c00c-161">*appSettings.json*: contém dados de configuração, como o protocolo usado pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8c00c-161">*appSettings.json*:Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="8c00c-162">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8c00c-162">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="8c00c-163">*Program.cs*: contém o ponto de entrada para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c00c-163">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="8c00c-164">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="8c00c-164">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="8c00c-165">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="8c00c-165">Startup.cs</span></span>

<span data-ttu-id="8c00c-166">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8c00c-166">Contains code that configures app behavior.</span></span> <span data-ttu-id="8c00c-167">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8c00c-167">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="8c00c-168">Arquivo GrpcGreeter.Client do cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="8c00c-168">gRPC client GrpcGreeter.Client file:</span></span>

<span data-ttu-id="8c00c-169">*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c00c-169">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="8c00c-170">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="8c00c-170">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8c00c-171">Criou um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c00c-171">Created a gRPC service.</span></span>
> * <span data-ttu-id="8c00c-172">Executou o serviço e um cliente para testar o serviço.</span><span class="sxs-lookup"><span data-stu-id="8c00c-172">Ran the service and a client to test the service.</span></span>
> * <span data-ttu-id="8c00c-173">Examinou os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="8c00c-173">Examined the project files.</span></span>
