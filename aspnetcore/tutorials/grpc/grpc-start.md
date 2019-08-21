---
title: Criar um cliente e um servidor gRPC do .NET Core no ASP.NET Core
author: juntaoluo
description: Este tutorial mostra como criar um serviço gRPC e um cliente gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo pronto e adicionar uma chamada de streaming duplex.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 496f659bd51e2404a936bea8aad77e674e1a285d
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022488"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="85756-104">Tutorial: Criar um cliente e um servidor gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85756-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="85756-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="85756-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="85756-106">Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="85756-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="85756-107">No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="85756-108">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="85756-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="85756-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="85756-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="85756-110">Criará um servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="85756-111">Criará um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="85756-112">Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="85756-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="85756-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85756-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85756-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85756-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85756-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85756-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="85756-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="85756-117">Criar um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="85756-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85756-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85756-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85756-119">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="85756-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="85756-120">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="85756-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="85756-121">Selecione **Avançar**</span><span class="sxs-lookup"><span data-stu-id="85756-121">Select **Next**</span></span>
* <span data-ttu-id="85756-122">Nomeie o projeto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="85756-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="85756-123">É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="85756-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="85756-124">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="85756-124">Select **Create**</span></span>
* <span data-ttu-id="85756-125">Na caixa de diálogo **Criar um novo Aplicativo Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="85756-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="85756-126">Selecione **.NET Core** e **ASP.NET Core 3.0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="85756-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="85756-127">Selecione o modelo do **Serviço gRPC**.</span><span class="sxs-lookup"><span data-stu-id="85756-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="85756-128">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="85756-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85756-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85756-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="85756-130">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="85756-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="85756-131">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="85756-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="85756-132">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="85756-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="85756-133">O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="85756-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="85756-134">O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="85756-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="85756-135">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de "GrpcGreeter". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="85756-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="85756-136">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="85756-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85756-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="85756-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="85756-138">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="85756-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="85756-139">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="85756-140">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="85756-140">Open the project</span></span>

<span data-ttu-id="85756-141">No Visual Studio, selecione **Arquivo > Abrir** e depois o arquivo *GrpcGreeter.sln*.</span><span class="sxs-lookup"><span data-stu-id="85756-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="85756-142">Executar o serviço</span><span class="sxs-lookup"><span data-stu-id="85756-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85756-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85756-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85756-144">Pressione `Ctrl+F5` para executar o serviço gRPC sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="85756-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="85756-145">O Visual Studio executa o serviço em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="85756-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="85756-146">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="85756-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="85756-147">Execute o projeto de Boas-vindas gRPC *GrpcGreeter* por meio da linha de comando usando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="85756-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="85756-148">Os logs mostram o serviço escutando em `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="85756-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="85756-149">O modelo gRPC é configurado para usar [o protocolo TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="85756-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="85756-150">Os clientes gRPC precisam usar HTTPS para chamar o servidor.</span><span class="sxs-lookup"><span data-stu-id="85756-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="85756-151">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="85756-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="85756-152">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="85756-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="85756-153">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="85756-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="85756-154">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="85756-154">Examine the project files</span></span>

<span data-ttu-id="85756-155">Arquivos de projeto *GrpcGreeter*:</span><span class="sxs-lookup"><span data-stu-id="85756-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="85756-156">*greet.proto*: O arquivo *Protos/greet.proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-156">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="85756-157">Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="85756-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="85756-158">Pasta *Serviços*: contém a implementação do serviço `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="85756-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="85756-159">*appSettings.json*: contém dados de configuração, como o protocolo usado pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85756-159">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="85756-160">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="85756-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="85756-161">*Program.cs*: contém o ponto de entrada para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-161">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="85756-162">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="85756-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="85756-163">*Startup.cs*: contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85756-163">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="85756-164">Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="85756-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="85756-165">Criar o cliente gRPC em um aplicativo de console .NET</span><span class="sxs-lookup"><span data-stu-id="85756-165">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85756-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85756-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85756-167">Abra uma segunda instância do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="85756-167">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="85756-168">Selecione **Arquivo** > **Novo** > **Projeto** na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="85756-168">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="85756-169">Na caixa de diálogo **Criar um novo projeto**, selecione **Aplicativo de Console (.NET Core)** .</span><span class="sxs-lookup"><span data-stu-id="85756-169">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="85756-170">Selecione **Avançar**</span><span class="sxs-lookup"><span data-stu-id="85756-170">Select **Next**</span></span>
* <span data-ttu-id="85756-171">Na caixa de texto **Nome**, digite "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="85756-171">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="85756-172">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="85756-172">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85756-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85756-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="85756-174">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="85756-174">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="85756-175">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="85756-175">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="85756-176">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="85756-176">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85756-177">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="85756-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="85756-178">Siga as instruções [aqui](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="85756-178">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="85756-179">Adicionar os pacotes necessários</span><span class="sxs-lookup"><span data-stu-id="85756-179">Add required packages</span></span>

<span data-ttu-id="85756-180">O projeto cliente gRPC requer os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="85756-180">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="85756-181">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="85756-181">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="85756-182">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="85756-182">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="85756-183">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="85756-183">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="85756-184">O pacote de ferramentas não é necessário em tempo de execução e, portanto, a dependência é marcada com `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="85756-184">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85756-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85756-185">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="85756-186">Instalar os pacotes usando o PMC (Console do Gerenciador de Pacotes) ou Gerenciar Pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="85756-186">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="85756-187">Opção do PMC para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="85756-187">PMC option to install packages</span></span>

* <span data-ttu-id="85756-188">No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="85756-188">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="85756-189">Na janela **Console do Gerenciador de Pacotes**, navegue para o diretório no qual o arquivo *GrpcGreeterClient.csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="85756-189">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="85756-190">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="85756-190">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="85756-191">Opção Gerenciar Pacotes NuGet para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="85756-191">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="85756-192">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="85756-192">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="85756-193">Selecione a guia **Procurar**.</span><span class="sxs-lookup"><span data-stu-id="85756-193">Select the **Browse** tab.</span></span>
* <span data-ttu-id="85756-194">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="85756-194">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="85756-195">Selecione o pacote **Grpc.Net.Client** na guia **Procurar** e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="85756-195">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="85756-196">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="85756-196">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85756-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85756-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="85756-198">Execute os comandos a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="85756-198">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85756-199">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="85756-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="85756-200">Clique com o botão direito do mouse na pasta **Pacotes** em **Painel de Soluções** > **Adicionar Pacotes**</span><span class="sxs-lookup"><span data-stu-id="85756-200">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="85756-201">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="85756-201">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="85756-202">Selecione o pacote **Grpc.Net.Client** no painel de resultados e selecione **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="85756-202">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="85756-203">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="85756-203">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="85756-204">Adicionar greet.proto</span><span class="sxs-lookup"><span data-stu-id="85756-204">Add greet.proto</span></span>

* <span data-ttu-id="85756-205">Crie uma pasta **Protos** no projeto do cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-205">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="85756-206">Copie o arquivo **Protos\greet.proto** do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-206">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="85756-207">Edite o arquivo de projeto *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="85756-207">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85756-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85756-208">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="85756-209">Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto**.</span><span class="sxs-lookup"><span data-stu-id="85756-209">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85756-210">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85756-210">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="85756-211">Selecione o arquivo *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="85756-211">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85756-212">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="85756-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="85756-213">Clique com o botão direito do mouse no projeto e selecione **Ferramentas > Editar Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="85756-213">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="85756-214">Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo **greet.proto**:</span><span class="sxs-lookup"><span data-stu-id="85756-214">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="85756-215">Criar o cliente Greeter</span><span class="sxs-lookup"><span data-stu-id="85756-215">Create the Greeter client</span></span>

<span data-ttu-id="85756-216">Compile o projeto para criar os tipos no namespace `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="85756-216">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="85756-217">Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.</span><span class="sxs-lookup"><span data-stu-id="85756-217">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="85756-218">Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="85756-218">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="85756-219">*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-219">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="85756-220">O cliente Greeter é criado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="85756-220">The Greeter client is created by:</span></span>

* <span data-ttu-id="85756-221">Criando uma instância de `HttpClient` que contém as informações para criar a conexão com o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="85756-221">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="85756-222">Como usar o `HttpClient` para construir o cliente Greeter:</span><span class="sxs-lookup"><span data-stu-id="85756-222">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="85756-223">O cliente Greeter chama o método `SayHello` assíncrono.</span><span class="sxs-lookup"><span data-stu-id="85756-223">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="85756-224">O resultado da chamada `SayHello` é exibido:</span><span class="sxs-lookup"><span data-stu-id="85756-224">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="85756-225">Testar o cliente gRPC com o serviço de Boas-vindas do gRPC</span><span class="sxs-lookup"><span data-stu-id="85756-225">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85756-226">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85756-226">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85756-227">No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="85756-227">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="85756-228">No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o cliente sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="85756-228">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="85756-229">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="85756-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="85756-230">Inicie o serviço Greeter.</span><span class="sxs-lookup"><span data-stu-id="85756-230">Start the Greeter service.</span></span>
* <span data-ttu-id="85756-231">Inicie o cliente.</span><span class="sxs-lookup"><span data-stu-id="85756-231">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="85756-232">O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="85756-232">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="85756-233">O serviço envia a mensagem "Olá, GreeterClient" como uma resposta.</span><span class="sxs-lookup"><span data-stu-id="85756-233">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="85756-234">A resposta "Olá, GreeterClient" é exibida no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="85756-234">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="85756-235">O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="85756-235">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="85756-236">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="85756-236">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
