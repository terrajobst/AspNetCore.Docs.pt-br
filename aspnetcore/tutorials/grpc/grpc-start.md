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
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="f1716-104">Tutorial: Criar um cliente e um servidor gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1716-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="f1716-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="f1716-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="f1716-106">Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1716-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="f1716-107">No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="f1716-108">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f1716-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f1716-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="f1716-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f1716-110">Criará um servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="f1716-111">Criará um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="f1716-112">Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1716-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f1716-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1716-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1716-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f1716-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1716-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f1716-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f1716-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="f1716-117">Criar um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="f1716-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1716-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1716-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1716-119">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="f1716-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f1716-120">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f1716-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f1716-121">Selecione **Avançar**</span><span class="sxs-lookup"><span data-stu-id="f1716-121">Select **Next**</span></span>
* <span data-ttu-id="f1716-122">Nomeie o projeto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="f1716-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="f1716-123">É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="f1716-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="f1716-124">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="f1716-124">Select **Create**</span></span>
* <span data-ttu-id="f1716-125">Na caixa de diálogo **Criar um novo Aplicativo Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="f1716-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="f1716-126">Selecione **.NET Core** e **ASP.NET Core 3.0** nos menus suspensos.</span><span class="sxs-lookup"><span data-stu-id="f1716-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="f1716-127">Selecione o modelo do **Serviço gRPC**.</span><span class="sxs-lookup"><span data-stu-id="f1716-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="f1716-128">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="f1716-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f1716-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1716-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f1716-130">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f1716-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f1716-131">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="f1716-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f1716-132">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f1716-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="f1716-133">O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="f1716-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="f1716-134">O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f1716-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="f1716-135">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de "GrpcGreeter". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="f1716-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="f1716-136">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="f1716-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f1716-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f1716-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f1716-138">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f1716-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="f1716-139">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="f1716-140">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="f1716-140">Open the project</span></span>

<span data-ttu-id="f1716-141">No Visual Studio, selecione **Arquivo > Abrir** e depois o arquivo *GrpcGreeter.sln*.</span><span class="sxs-lookup"><span data-stu-id="f1716-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="f1716-142">Executar o serviço</span><span class="sxs-lookup"><span data-stu-id="f1716-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1716-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1716-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1716-144">Pressione `Ctrl+F5` para executar o serviço gRPC sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="f1716-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="f1716-145">O Visual Studio executa o serviço em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="f1716-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f1716-146">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f1716-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f1716-147">Execute o projeto de Boas-vindas gRPC *GrpcGreeter* por meio da linha de comando usando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="f1716-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="f1716-148">Os logs mostram o serviço escutando em `http://localhost:50051`.</span><span class="sxs-lookup"><span data-stu-id="f1716-148">The logs show the service listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="f1716-149">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="f1716-149">Examine the project files</span></span>

<span data-ttu-id="f1716-150">Arquivos de projeto *GrpcGreeter*:</span><span class="sxs-lookup"><span data-stu-id="f1716-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="f1716-151">*greet.proto*: O arquivo *Protos/greet.proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="f1716-152">Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="f1716-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="f1716-153">Pasta *Serviços*: contém a implementação do serviço `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="f1716-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="f1716-154">*appSettings.json*: contém dados de configuração, como o protocolo usado pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f1716-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="f1716-155">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f1716-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="f1716-156">*Program.cs*: contém o ponto de entrada para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="f1716-157">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="f1716-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="f1716-158">*Startup.cs*: contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f1716-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="f1716-159">Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="f1716-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="f1716-160">Criar o cliente gRPC em um aplicativo de console .NET</span><span class="sxs-lookup"><span data-stu-id="f1716-160">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1716-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1716-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1716-162">Abra uma segunda instância do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f1716-162">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="f1716-163">Selecione **Arquivo** > **Novo** > **Projeto** na barra de menus.</span><span class="sxs-lookup"><span data-stu-id="f1716-163">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="f1716-164">Na caixa de diálogo **Criar um novo projeto**, selecione **Aplicativo de Console (.NET Core)** .</span><span class="sxs-lookup"><span data-stu-id="f1716-164">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="f1716-165">Selecione **Avançar**</span><span class="sxs-lookup"><span data-stu-id="f1716-165">Select **Next**</span></span>
* <span data-ttu-id="f1716-166">Na caixa de texto **Nome**, digite "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="f1716-166">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="f1716-167">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f1716-167">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f1716-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1716-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f1716-169">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f1716-169">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f1716-170">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="f1716-170">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f1716-171">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f1716-171">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f1716-172">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f1716-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f1716-173">Siga as instruções [aqui](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="f1716-173">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="f1716-174">Adicionar os pacotes necessários</span><span class="sxs-lookup"><span data-stu-id="f1716-174">Add required packages</span></span>

<span data-ttu-id="f1716-175">O projeto cliente gRPC requer os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="f1716-175">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="f1716-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f1716-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="f1716-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="f1716-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="f1716-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="f1716-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="f1716-179">O pacote de ferramentas não é necessário em tempo de execução e, portanto, a dependência é marcada com `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="f1716-179">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1716-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1716-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f1716-181">Instalar os pacotes usando o PMC (Console do Gerenciador de Pacotes) ou Gerenciar Pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="f1716-181">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="f1716-182">Opção do PMC para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="f1716-182">PMC option to install packages</span></span>

* <span data-ttu-id="f1716-183">No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="f1716-183">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="f1716-184">Na janela **Console do Gerenciador de Pacotes**, navegue para o diretório no qual o arquivo *GrpcGreeterClient.csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="f1716-184">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="f1716-185">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f1716-185">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="f1716-186">Opção Gerenciar Pacotes NuGet para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="f1716-186">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="f1716-187">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="f1716-187">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="f1716-188">Selecione a guia **Procurar**.</span><span class="sxs-lookup"><span data-stu-id="f1716-188">Select the **Browse** tab.</span></span>
* <span data-ttu-id="f1716-189">Insira **Grpc.Core** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="f1716-189">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="f1716-190">Selecione o pacote **Grpc.Core** na guia **Procurar** e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="f1716-190">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="f1716-191">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="f1716-191">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f1716-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1716-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f1716-193">Execute os comandos a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="f1716-193">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f1716-194">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f1716-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f1716-195">Clique com o botão direito do mouse na pasta **Pacotes** em **Painel de Soluções** > **Adicionar Pacotes**</span><span class="sxs-lookup"><span data-stu-id="f1716-195">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="f1716-196">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="f1716-196">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="f1716-197">Selecione o pacote **Grpc.Net.Client** no painel de resultados e selecione **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="f1716-197">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="f1716-198">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="f1716-198">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="f1716-199">Adicionar greet.proto</span><span class="sxs-lookup"><span data-stu-id="f1716-199">Add greet.proto</span></span>

* <span data-ttu-id="f1716-200">Crie uma pasta **Protos** no projeto do cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-200">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="f1716-201">Copie o arquivo **Protos\greet.proto** do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-201">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="f1716-202">Edite o arquivo de projeto *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="f1716-202">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1716-203">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1716-203">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="f1716-204">Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto**.</span><span class="sxs-lookup"><span data-stu-id="f1716-204">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f1716-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1716-205">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="f1716-206">Selecione o arquivo *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="f1716-206">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f1716-207">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f1716-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="f1716-208">Clique com o botão direito do mouse no projeto e selecione **Ferramentas > Editar Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="f1716-208">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="f1716-209">Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo **greet.proto**:</span><span class="sxs-lookup"><span data-stu-id="f1716-209">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="f1716-210">Criar o cliente Greeter</span><span class="sxs-lookup"><span data-stu-id="f1716-210">Create the Greeter client</span></span>

<span data-ttu-id="f1716-211">Compile o projeto para criar os tipos no namespace `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="f1716-211">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="f1716-212">Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.</span><span class="sxs-lookup"><span data-stu-id="f1716-212">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="f1716-213">Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f1716-213">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="f1716-214">*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-214">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="f1716-215">O cliente Greeter é criado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="f1716-215">The Greeter client is created by:</span></span>

* <span data-ttu-id="f1716-216">Criando uma instância de `HttpClient` que contém as informações para criar a conexão com o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="f1716-216">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="f1716-217">Como usar o `HttpClient` para construir o cliente Greeter:</span><span class="sxs-lookup"><span data-stu-id="f1716-217">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

<span data-ttu-id="f1716-218">O cliente Greeter chama o método `SayHello` assíncrono.</span><span class="sxs-lookup"><span data-stu-id="f1716-218">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="f1716-219">O resultado da chamada `SayHello` é exibido:</span><span class="sxs-lookup"><span data-stu-id="f1716-219">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="f1716-220">Testar o cliente gRPC com o serviço de Boas-vindas do gRPC</span><span class="sxs-lookup"><span data-stu-id="f1716-220">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1716-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1716-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1716-222">No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="f1716-222">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="f1716-223">No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="f1716-223">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f1716-224">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f1716-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f1716-225">Inicie o serviço Greeter.</span><span class="sxs-lookup"><span data-stu-id="f1716-225">Start the Greeter service.</span></span>
* <span data-ttu-id="f1716-226">Inicie o cliente.</span><span class="sxs-lookup"><span data-stu-id="f1716-226">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="f1716-227">O cliente envia uma saudação para o serviço com uma mensagem contendo o nome "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="f1716-227">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="f1716-228">O serviço envia a mensagem "Olá, GreeterClient" como uma resposta.</span><span class="sxs-lookup"><span data-stu-id="f1716-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="f1716-229">A resposta "Olá, GreeterClient" é exibida no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="f1716-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="f1716-230">O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="f1716-230">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="f1716-231">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f1716-231">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
