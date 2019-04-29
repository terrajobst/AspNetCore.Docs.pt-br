---
title: 'Tutorial: Introdução ao gRPC no ASP.NET Core'
author: juntaoluo
description: Esta série de tutoriais mostra como criar um serviço gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo proto e adicionar uma chamada de streaming duplex.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: a67050331cc8563b1baf5312b92910c1bbdfbd69
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672561"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="abbbb-104">Tutorial: Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="abbbb-104">Tutorial: Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="abbbb-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="abbbb-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="abbbb-106">Este tutorial ensina os conceitos básicos da criação de um serviço gRPC no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="abbbb-106">This tutorial teaches the basics of building a gRPC service on ASP.NET Core.</span></span>

<span data-ttu-id="abbbb-107">No final, você terá um serviço gRPC que ecoa as saudações.</span><span class="sxs-lookup"><span data-stu-id="abbbb-107">At the end, you'll have a gRPC service that echoes greetings.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

<span data-ttu-id="abbbb-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="abbbb-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="abbbb-109">Criar um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="abbbb-109">Create a gRPC service.</span></span>
> * <span data-ttu-id="abbbb-110">Executará o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="abbbb-110">Run the gRPC service.</span></span>
> * <span data-ttu-id="abbbb-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="abbbb-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="abbbb-112">Criar um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="abbbb-112">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="abbbb-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="abbbb-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="abbbb-114">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="abbbb-114">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="abbbb-115">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="abbbb-115">Create a new ASP.NET Core Web Application.</span></span>
  <span data-ttu-id="abbbb-116">![novo aplicativo Web ASP.NET Core](grpc-start/_static/np_3_0.1.png)</span><span class="sxs-lookup"><span data-stu-id="abbbb-116">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.1.png)</span></span>
* <span data-ttu-id="abbbb-117">Nomeie o projeto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="abbbb-117">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="abbbb-118">É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="abbbb-118">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="abbbb-119">![novo aplicativo Web ASP.NET Core](grpc-start/_static/np_3_0.2.png)</span><span class="sxs-lookup"><span data-stu-id="abbbb-119">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.2.png)</span></span>
* <span data-ttu-id="abbbb-120">Selecione **.NET Core** e **ASP.NET Core 3.0** na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="abbbb-120">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown.</span></span> <span data-ttu-id="abbbb-121">Escolha o modelo do **serviço gRPC**.</span><span class="sxs-lookup"><span data-stu-id="abbbb-121">Choose the **gRPC Service** template.</span></span>

  <span data-ttu-id="abbbb-122">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="abbbb-122">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="abbbb-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="abbbb-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="abbbb-125">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="abbbb-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="abbbb-126">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="abbbb-126">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="abbbb-127">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="abbbb-127">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="abbbb-128">O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="abbbb-128">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="abbbb-129">O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="abbbb-129">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="abbbb-130">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de "GrpcGreeter". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="abbbb-130">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="abbbb-131">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="abbbb-131">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="abbbb-132">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="abbbb-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="abbbb-133">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="abbbb-133">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="abbbb-134">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="abbbb-134">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="abbbb-135">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="abbbb-135">Open the project</span></span>

<span data-ttu-id="abbbb-136">No Visual Studio, selecione **Arquivo > Abrir** e depois o arquivo *GrpcGreeter.sln*.</span><span class="sxs-lookup"><span data-stu-id="abbbb-136">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="abbbb-137">Executar o serviço</span><span class="sxs-lookup"><span data-stu-id="abbbb-137">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="abbbb-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="abbbb-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="abbbb-139">Pressione Ctrl+F5 para executar o serviço gRPC sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="abbbb-139">Press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="abbbb-140">O Visual Studio executa o serviço em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="abbbb-140">Visual Studio runs the service in a command prompt.</span></span> <span data-ttu-id="abbbb-141">Os logs mostram que o serviço começou a escutar em `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="abbbb-141">The logs show that the service started listening on `http://localhost:50051`.</span></span>

  ![novo Aplicativo Web ASP.NET Core](grpc-start/_static/server_start.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="abbbb-143">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="abbbb-143">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="abbbb-144">Execute o projeto de Boas-vindas gRPC GrpcGreeter por meio da linha de comando usando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="abbbb-144">Run the gRPC Greeter project GrpcGreeter from the command line using `dotnet run`.</span></span> <span data-ttu-id="abbbb-145">Os logs mostram que o serviço começou a escutar em `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="abbbb-145">The logs show that the service started listening on `http://localhost:50051`.</span></span>

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
```

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="abbbb-146">O próximo tutorial demonstrará como criar um cliente gRPC, que é necessário para testar o serviço de Boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="abbbb-146">The next tutorial will demonstrate how to build a gRPC client, which is required to test the Greeter service.</span></span>

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="abbbb-147">Examinar os arquivos de projeto do projeto gRPC</span><span class="sxs-lookup"><span data-stu-id="abbbb-147">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="abbbb-148">Arquivos do GrpcGreeter:</span><span class="sxs-lookup"><span data-stu-id="abbbb-148">GrpcGreeter files:</span></span>

* <span data-ttu-id="abbbb-149">greet.proto: O arquivo *Protos/greet.proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="abbbb-149">greet.proto: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="abbbb-150">Para obter mais informações, consulte <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="abbbb-150">For more information, see <xref:grpc/index>.</span></span>
* <span data-ttu-id="abbbb-151">Pasta *Serviços*: contém a implementação do serviço `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="abbbb-151">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="abbbb-152">*appSettings.json*: contém dados de configuração, como o protocolo usado pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="abbbb-152">*appSettings.json*:Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="abbbb-153">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="abbbb-153">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="abbbb-154">*Program.cs*: contém o ponto de entrada para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="abbbb-154">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="abbbb-155">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="abbbb-155">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="abbbb-156">Startup.cs: contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="abbbb-156">Startup.cs: Contains code that configures app behavior.</span></span> <span data-ttu-id="abbbb-157">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="abbbb-157">For more information, see <xref:fundamentals/startup>.</span></span>

### <a name="test-the-service"></a><span data-ttu-id="abbbb-158">Testar o serviço</span><span class="sxs-lookup"><span data-stu-id="abbbb-158">Test the service</span></span>

## <a name="additional-resources"></a><span data-ttu-id="abbbb-159">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="abbbb-159">Additional resources</span></span>

<span data-ttu-id="abbbb-160">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="abbbb-160">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="abbbb-161">Criou um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="abbbb-161">Created a gRPC service.</span></span>
> * <span data-ttu-id="abbbb-162">Executou o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="abbbb-162">Ran the gRPC service.</span></span>
> * <span data-ttu-id="abbbb-163">Examinou os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="abbbb-163">Examined the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="abbbb-164">Avançar: Criar um cliente gRPC do .NET Core</span><span class="sxs-lookup"><span data-stu-id="abbbb-164">Next: Create a .NET Core gRPC client</span></span>](xref:tutorials/grpc/grpc-client)