---
title: Criar um cliente e um servidor gRPC do .NET Core no ASP.NET Core
author: juntaoluo
description: Este tutorial mostra como criar um serviço gRPC e um cliente gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo pronto e adicionar uma chamada de streaming duplex.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 8/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 9eeb71ca751005780560f0f2200edc2013541c34
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925221"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="d7569-104">Tutorial: Criar um cliente e um servidor gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7569-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="d7569-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="d7569-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="d7569-106">Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7569-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="d7569-107">No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="d7569-108">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d7569-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d7569-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="d7569-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d7569-110">Criará um servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="d7569-111">Criará um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="d7569-112">Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d7569-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d7569-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7569-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7569-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d7569-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7569-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d7569-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d7569-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="d7569-117">Criar um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="d7569-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7569-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7569-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7569-119">Inicie o Visual Studio e selecione **Criar um novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="d7569-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="d7569-120">Como alternativa, no menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="d7569-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d7569-121">Na caixa de diálogo **criar um novo projeto** , selecione **serviço GRPC** e selecione **Avançar**:</span><span class="sxs-lookup"><span data-stu-id="d7569-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Caixa de diálogo **Criar um novo projeto**](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="d7569-123">Nomeie o projeto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="d7569-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="d7569-124">É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="d7569-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="d7569-125">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d7569-125">Select **Create**.</span></span>
* <span data-ttu-id="d7569-126">Na caixa de diálogo **Criar um novo serviço gRPC**:</span><span class="sxs-lookup"><span data-stu-id="d7569-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="d7569-127">O modelo de **Serviço gRPC** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="d7569-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="d7569-128">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d7569-128">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d7569-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7569-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d7569-130">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d7569-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d7569-131">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="d7569-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d7569-132">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d7569-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="d7569-133">O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="d7569-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="d7569-134">O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d7569-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="d7569-135">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de "GrpcGreeter". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="d7569-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="d7569-136">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="d7569-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d7569-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d7569-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d7569-138">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d7569-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="d7569-139">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="d7569-140">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="d7569-140">Open the project</span></span>

<span data-ttu-id="d7569-141">No Visual Studio, selecione **Arquivo** > **Abrir** e depois o arquivo *GrpcGreeter.sln*.</span><span class="sxs-lookup"><span data-stu-id="d7569-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.sln* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="d7569-142">Executar o serviço</span><span class="sxs-lookup"><span data-stu-id="d7569-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7569-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7569-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7569-144">Pressione `Ctrl+F5` para executar o serviço gRPC sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="d7569-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="d7569-145">O Visual Studio executa o serviço em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="d7569-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d7569-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7569-146">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d7569-147">Execute o projeto de Boas-vindas gRPC *GrpcGreeter* por meio da linha de comando usando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d7569-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d7569-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d7569-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d7569-149">Execute o projeto de Boas-vindas gRPC *GrpcGreeter* por meio da linha de comando usando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d7569-149">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

---

<span data-ttu-id="d7569-150">Os logs mostram o serviço escutando em `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d7569-150">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="d7569-151">O modelo gRPC é configurado para usar [o protocolo TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="d7569-151">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="d7569-152">Os clientes gRPC precisam usar HTTPS para chamar o servidor.</span><span class="sxs-lookup"><span data-stu-id="d7569-152">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="d7569-153">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="d7569-153">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="d7569-154">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="d7569-154">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="d7569-155">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="d7569-155">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="d7569-156">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="d7569-156">Examine the project files</span></span>

<span data-ttu-id="d7569-157">Arquivos de projeto *GrpcGreeter*:</span><span class="sxs-lookup"><span data-stu-id="d7569-157">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="d7569-158">*greet.proto* &ndash; O arquivo *Protos/greet.proto* define o gRPC `Greeter` e é usado para gerar os ativos do servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-158">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="d7569-159">Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="d7569-159">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="d7569-160">Pasta *Serviços*: contém a implementação do serviço `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="d7569-160">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="d7569-161">*appSettings.json* &ndash; Contém dados de configuração, como o protocolo usado pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d7569-161">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="d7569-162">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d7569-162">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="d7569-163">*Program.cs* &ndash; Contém o ponto de entrada para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-163">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="d7569-164">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="d7569-164">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="d7569-165">*Startup.cs* &ndash; Contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d7569-165">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="d7569-166">Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d7569-166">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="d7569-167">Criar o cliente gRPC em um aplicativo de console .NET</span><span class="sxs-lookup"><span data-stu-id="d7569-167">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7569-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7569-168">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7569-169">Abra uma segunda instância do Visual Studio e selecione **Criar um novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="d7569-169">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="d7569-170">Na caixa de diálogo **Criar um novo projeto**, selecione **Aplicativo de Console (.NET Core)** e **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d7569-170">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="d7569-171">Na caixa de texto **Nome**, digite **GrpcGreeterClient** e selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d7569-171">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d7569-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7569-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d7569-173">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d7569-173">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d7569-174">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="d7569-174">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d7569-175">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d7569-175">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d7569-176">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d7569-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d7569-177">Siga as instruções em [Compilar uma solução completa do .NET Core no macOS usando o Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="d7569-177">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="d7569-178">Adicionar os pacotes necessários</span><span class="sxs-lookup"><span data-stu-id="d7569-178">Add required packages</span></span>

<span data-ttu-id="d7569-179">O projeto cliente gRPC requer os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="d7569-179">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="d7569-180">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7569-180">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="d7569-181">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="d7569-181">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="d7569-182">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="d7569-182">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="d7569-183">O pacote de ferramentas não é necessário em tempo de execução e, portanto, a dependência é marcada com `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="d7569-183">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7569-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7569-184">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d7569-185">Instalar os pacotes usando o PMC (Console do Gerenciador de Pacotes) ou Gerenciar Pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="d7569-185">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="d7569-186">Opção do PMC para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="d7569-186">PMC option to install packages</span></span>

* <span data-ttu-id="d7569-187">No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="d7569-187">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="d7569-188">Na janela do **Console do Gerenciador de Pacotes**, execute `cd GrpcGreeterClient` para alterar os diretórios para a pasta que contém os arquivos *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="d7569-188">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="d7569-189">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d7569-189">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="d7569-190">Opção Gerenciar Pacotes NuGet para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="d7569-190">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="d7569-191">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="d7569-191">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="d7569-192">Selecione a guia **Procurar**.</span><span class="sxs-lookup"><span data-stu-id="d7569-192">Select the **Browse** tab.</span></span>
* <span data-ttu-id="d7569-193">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d7569-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="d7569-194">Selecione o pacote **Grpc.Net.Client** na guia **Procurar** e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="d7569-194">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="d7569-195">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="d7569-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d7569-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7569-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d7569-197">Execute os comandos a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="d7569-197">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d7569-198">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d7569-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d7569-199">Clique com o botão direito do mouse na pasta **Pacotes** em **Painel de Soluções** > **Adicionar Pacotes**</span><span class="sxs-lookup"><span data-stu-id="d7569-199">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="d7569-200">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="d7569-200">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="d7569-201">Selecione o pacote **Grpc.Net.Client** no painel de resultados e selecione **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="d7569-201">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="d7569-202">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="d7569-202">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="d7569-203">Adicionar greet.proto</span><span class="sxs-lookup"><span data-stu-id="d7569-203">Add greet.proto</span></span>

* <span data-ttu-id="d7569-204">Crie uma pasta *Protos* no projeto do cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-204">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="d7569-205">Copie o arquivo *Protos\greet.proto* do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-205">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="d7569-206">Edite o arquivo de projeto *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="d7569-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7569-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7569-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="d7569-208">Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto**.</span><span class="sxs-lookup"><span data-stu-id="d7569-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d7569-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7569-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="d7569-210">Selecione o arquivo *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="d7569-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d7569-211">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d7569-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="d7569-212">Clique com o botão direito do mouse no projeto e selecione **Ferramentas** > **Editar Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="d7569-212">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="d7569-213">Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo *greet.proto*:</span><span class="sxs-lookup"><span data-stu-id="d7569-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="d7569-214">Criar o cliente Greeter</span><span class="sxs-lookup"><span data-stu-id="d7569-214">Create the Greeter client</span></span>

<span data-ttu-id="d7569-215">Compile o projeto para criar os tipos no namespace `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="d7569-215">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="d7569-216">Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.</span><span class="sxs-lookup"><span data-stu-id="d7569-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="d7569-217">Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d7569-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="d7569-218">*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="d7569-219">O cliente Greeter é criado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="d7569-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="d7569-220">Criando uma instância de `HttpClient` que contém as informações para criar a conexão com o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-220">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="d7569-221">Usando o `HttpClient` para construir um canal do gRPC e o cliente do Greeter:</span><span class="sxs-lookup"><span data-stu-id="d7569-221">Using the `HttpClient` to construct a gRPC channel and the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="d7569-222">O cliente Greeter chama o método `SayHello` assíncrono.</span><span class="sxs-lookup"><span data-stu-id="d7569-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="d7569-223">O resultado da chamada `SayHello` é exibido:</span><span class="sxs-lookup"><span data-stu-id="d7569-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="d7569-224">Testar o cliente gRPC com o serviço de Boas-vindas do gRPC</span><span class="sxs-lookup"><span data-stu-id="d7569-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7569-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7569-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7569-226">No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="d7569-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="d7569-227">No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o cliente sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="d7569-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d7569-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7569-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d7569-229">Inicie o serviço Greeter.</span><span class="sxs-lookup"><span data-stu-id="d7569-229">Start the Greeter service.</span></span>
* <span data-ttu-id="d7569-230">Inicie o cliente.</span><span class="sxs-lookup"><span data-stu-id="d7569-230">Start the client.</span></span>


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d7569-231">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d7569-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d7569-232">Inicie o serviço Greeter.</span><span class="sxs-lookup"><span data-stu-id="d7569-232">Start the Greeter service.</span></span>
* <span data-ttu-id="d7569-233">Inicie o cliente.</span><span class="sxs-lookup"><span data-stu-id="d7569-233">Start the client.</span></span>

---

<span data-ttu-id="d7569-234">O cliente envia uma saudação para o serviço com uma mensagem contendo seu nome, *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="d7569-234">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="d7569-235">O serviço envia a mensagem "Olá, GreeterClient" como uma resposta.</span><span class="sxs-lookup"><span data-stu-id="d7569-235">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="d7569-236">A resposta "Olá, GreeterClient" é exibida no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="d7569-236">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="d7569-237">O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="d7569-237">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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

> [!NOTE]
> <span data-ttu-id="d7569-238">O código neste artigo requer o certificado de desenvolvimento HTTPS do ASP.NET Core para proteger o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="d7569-238">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="d7569-239">Se houver falha no cliente e for exibida a mensagem `The remote certificate is invalid according to the validation procedure.`, o certificado de desenvolvimento não será confiável.</span><span class="sxs-lookup"><span data-stu-id="d7569-239">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="d7569-240">Para obter instruções sobre como corrigir esse problema, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core no Windows e no macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="d7569-240">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="d7569-241">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="d7569-241">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
