---
title: Criar um cliente e um servidor gRPC do .NET Core no ASP.NET Core
author: juntaoluo
description: Este tutorial mostra como criar um serviço gRPC e um cliente gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo pronto e adicionar uma chamada de streaming duplex.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 6aef56ecd61ad71e166c03c12b28b25b931cdd88
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152932"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="7c0cc-104">Tutorial: Criar um cliente e um servidor gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c0cc-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="7c0cc-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="7c0cc-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="7c0cc-106">Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="7c0cc-107">No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="7c0cc-108">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7c0cc-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="7c0cc-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7c0cc-110">Criará um servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="7c0cc-111">Criará um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="7c0cc-112">Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="7c0cc-113">Criar um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="7c0cc-113">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c0cc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c0cc-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c0cc-115">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7c0cc-116">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-116">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="7c0cc-117">Selecione **Avançar**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-117">Select **Next**</span></span>
* <span data-ttu-id="7c0cc-118">Nomeie o projeto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-118">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="7c0cc-119">É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-119">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="7c0cc-120">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-120">Select **Create**</span></span>
* <span data-ttu-id="7c0cc-121">Na caixa de diálogo **Criar um novo Aplicativo Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-121">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="7c0cc-122">Selecione **.NET Core** e **ASP.NET Core 3.0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-122">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="7c0cc-123">Selecione o modelo do **Serviço gRPC**.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-123">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="7c0cc-124">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-124">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c0cc-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c0cc-125">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c0cc-126">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7c0cc-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7c0cc-127">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-127">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="7c0cc-128">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-128">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="7c0cc-129">O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-129">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="7c0cc-130">O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-130">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="7c0cc-131">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de "GrpcGreeter". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-131">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="7c0cc-132">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-132">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c0cc-133">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c0cc-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7c0cc-134">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-134">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="7c0cc-135">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-135">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="7c0cc-136">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="7c0cc-136">Open the project</span></span>

<span data-ttu-id="7c0cc-137">No Visual Studio, selecione **Arquivo > Abrir** e depois o arquivo *GrpcGreeter.sln*.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-137">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="7c0cc-138">Executar o serviço</span><span class="sxs-lookup"><span data-stu-id="7c0cc-138">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c0cc-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c0cc-139">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c0cc-140">Pressione `Ctrl+F5` para executar o serviço gRPC sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-140">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="7c0cc-141">O Visual Studio executa o serviço em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-141">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7c0cc-142">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c0cc-142">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7c0cc-143">Execute o projeto de Boas-vindas gRPC *GrpcGreeter* por meio da linha de comando usando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-143">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="7c0cc-144">Os logs mostram o serviço escutando em `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-144">The logs show the service listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="7c0cc-145">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="7c0cc-145">Examine the project files</span></span>

<span data-ttu-id="7c0cc-146">Arquivos de projeto *GrpcGreeter*:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-146">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="7c0cc-147">*greet.proto*: O arquivo *Protos/greet.proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-147">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="7c0cc-148">Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="7c0cc-148">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="7c0cc-149">Pasta *Serviços*: contém a implementação do serviço `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-149">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="7c0cc-150">*appSettings.json*: contém dados de configuração, como o protocolo usado pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-150">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="7c0cc-151">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-151">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="7c0cc-152">*Program.cs*: contém o ponto de entrada para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-152">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="7c0cc-153">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-153">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="7c0cc-154">*Startup.cs*: contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-154">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="7c0cc-155">Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="7c0cc-155">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="7c0cc-156">Criar o cliente gRPC em um aplicativo de console .NET</span><span class="sxs-lookup"><span data-stu-id="7c0cc-156">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c0cc-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c0cc-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c0cc-158">Abra uma segunda instância do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-158">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="7c0cc-159">Selecione **Arquivo** > **Novo** > **Projeto** na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-159">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="7c0cc-160">Na caixa de diálogo **Criar um novo projeto**, selecione **Aplicativo de Console (.NET Core)** .</span><span class="sxs-lookup"><span data-stu-id="7c0cc-160">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="7c0cc-161">Selecione **Avançar**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-161">Select **Next**</span></span>
* <span data-ttu-id="7c0cc-162">Na caixa de texto **Nome**, digite "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="7c0cc-162">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="7c0cc-163">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-163">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c0cc-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c0cc-164">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c0cc-165">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7c0cc-165">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7c0cc-166">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-166">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="7c0cc-167">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-167">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c0cc-168">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c0cc-168">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7c0cc-169">Siga as instruções [aqui](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-169">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="7c0cc-170">Adicionar os pacotes necessários</span><span class="sxs-lookup"><span data-stu-id="7c0cc-170">Add required packages</span></span>

<span data-ttu-id="7c0cc-171">O projeto cliente gRPC requer os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-171">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="7c0cc-172">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-172">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="7c0cc-173">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-173">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="7c0cc-174">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-174">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="7c0cc-175">O pacote de ferramentas não é necessário em tempo de execução e, portanto, a dependência é marcada com `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-175">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c0cc-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c0cc-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c0cc-177">Instalar os pacotes usando o PMC (Console do Gerenciador de Pacotes) ou Gerenciar Pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-177">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="7c0cc-178">Opção do PMC para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="7c0cc-178">PMC option to install packages</span></span>

* <span data-ttu-id="7c0cc-179">No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-179">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="7c0cc-180">Na janela **Console do Gerenciador de Pacotes**, navegue para o diretório no qual o arquivo *GrpcGreeterClient.csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-180">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="7c0cc-181">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-181">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="7c0cc-182">Opção Gerenciar Pacotes NuGet para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="7c0cc-182">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="7c0cc-183">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-183">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="7c0cc-184">Selecione a guia **Procurar**.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-184">Select the **Browse** tab.</span></span>
* <span data-ttu-id="7c0cc-185">Insira **Grpc.Core** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-185">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="7c0cc-186">Selecione o pacote **Grpc.Core** na guia **Procurar** e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-186">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="7c0cc-187">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-187">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c0cc-188">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c0cc-188">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c0cc-189">Execute os comandos a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-189">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c0cc-190">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c0cc-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7c0cc-191">Clique com o botão direito do mouse na pasta **Pacotes** em **Painel de Soluções** > **Adicionar Pacotes**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-191">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="7c0cc-192">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-192">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="7c0cc-193">Selecione o pacote **Grpc.Net.Client** no painel de resultados e selecione **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="7c0cc-193">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="7c0cc-194">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-194">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="7c0cc-195">Adicionar greet.proto</span><span class="sxs-lookup"><span data-stu-id="7c0cc-195">Add greet.proto</span></span>

* <span data-ttu-id="7c0cc-196">Crie uma pasta **Protos** no projeto do cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-196">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="7c0cc-197">Copie o arquivo **Protos\greet.proto** do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-197">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="7c0cc-198">Edite o arquivo de projeto *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-198">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c0cc-199">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c0cc-199">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="7c0cc-200">Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto**.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-200">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c0cc-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c0cc-201">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="7c0cc-202">Selecione o arquivo *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-202">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c0cc-203">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c0cc-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="7c0cc-204">Clique com o botão direito do mouse no projeto e selecione **Ferramentas > Editar Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-204">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="7c0cc-205">Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo **greet.proto**:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-205">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="7c0cc-206">Criar o cliente Greeter</span><span class="sxs-lookup"><span data-stu-id="7c0cc-206">Create the Greeter client</span></span>

<span data-ttu-id="7c0cc-207">Compile o projeto para criar os tipos no namespace `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-207">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="7c0cc-208">Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-208">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="7c0cc-209">Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-209">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="7c0cc-210">*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-210">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="7c0cc-211">O cliente Greeter é criado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-211">The Greeter client is created by:</span></span>

* <span data-ttu-id="7c0cc-212">Criando uma instância de `HttpClient` que contém as informações para criar a conexão com o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-212">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="7c0cc-213">Como usar o `HttpClient` para construir o cliente Greeter:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-213">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

<span data-ttu-id="7c0cc-214">O cliente Greeter chama o método `SayHello` assíncrono.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-214">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="7c0cc-215">O resultado da chamada `SayHello` é exibido:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-215">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="7c0cc-216">Testar o cliente gRPC com o serviço de Boas-vindas do gRPC</span><span class="sxs-lookup"><span data-stu-id="7c0cc-216">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c0cc-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c0cc-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c0cc-218">No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-218">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="7c0cc-219">No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-219">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7c0cc-220">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c0cc-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7c0cc-221">Inicie o serviço Greeter.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-221">Start the Greeter service.</span></span>
* <span data-ttu-id="7c0cc-222">Inicie o cliente.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-222">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="7c0cc-223">O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="7c0cc-223">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="7c0cc-224">O serviço envia a mensagem "Olá, GreeterClient" como uma resposta.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-224">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="7c0cc-225">A resposta "Olá, GreeterClient" é exibida no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="7c0cc-225">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="7c0cc-226">O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="7c0cc-226">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="7c0cc-227">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="7c0cc-227">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
