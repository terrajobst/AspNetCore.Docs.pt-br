---
title: Introdução ao ASP.NET Core SignalR
author: bradygaster
description: Neste tutorial, você cria um aplicativo de chat que usa ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 55ebdbfa4556deca74a6cdf0638307425cd1a01a
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317497"
---
# <a name="tutorial-get-started-with-aspnet-core-opno-locsignalr"></a><span data-ttu-id="72d5f-103">Tutorial: introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="72d5f-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="72d5f-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="72d5f-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="72d5f-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="72d5f-106">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="72d5f-106">Create a web project.</span></span>
> * <span data-ttu-id="72d5f-107">Adicione a biblioteca de cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="72d5f-108">Crie um hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="72d5f-109">Configure o projeto a ser usado SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="72d5f-110">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="72d5f-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="72d5f-111">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="72d5f-111">At the end, you'll have a working chat app:</span></span>

![[! Parar. Aplicativo de exemplo não LOC (Signalr)]](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="72d5f-113">{1&gt;Pré-requisitos&lt;1}</span><span class="sxs-lookup"><span data-stu-id="72d5f-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72d5f-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72d5f-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72d5f-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72d5f-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="72d5f-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="72d5f-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="72d5f-117">Criar um projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="72d5f-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72d5f-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72d5f-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="72d5f-119">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="72d5f-120">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core** e depois selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="72d5f-121">Na caixa de diálogo **Configurar seu novo projeto**, nomeie o projeto *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="72d5f-122">Na caixa de diálogo **criar um novo aplicativo web ASP.NET Core** , selecione **.net Core** e **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="72d5f-123">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72d5f-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72d5f-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="72d5f-126">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="72d5f-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="72d5f-127">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="72d5f-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="72d5f-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="72d5f-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="72d5f-129">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="72d5f-130">Selecione **.NET Core > Aplicativo > Aplicativo Web** (não selecione **Aplicativo Web (Model-View-Controller)** ) e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="72d5f-131">Verifique se a **Estrutura de Destino** está definida para **.NET Core 3.0** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="72d5f-132">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-opno-locsignalr-client-library"></a><span data-ttu-id="72d5f-133">Adicionar a biblioteca de cliente SignalR</span><span class="sxs-lookup"><span data-stu-id="72d5f-133">Add the SignalR client library</span></span>

<span data-ttu-id="72d5f-134">A biblioteca do SignalR Server está incluída na estrutura compartilhada do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="72d5f-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="72d5f-135">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="72d5f-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="72d5f-136">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="72d5f-137">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="72d5f-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72d5f-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72d5f-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="72d5f-139">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="72d5f-140">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="72d5f-141">Para **Biblioteca**, insira `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="72d5f-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="72d5f-142">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="72d5f-143">Defina **local de destino** como *wwwroot/js/signalr/* e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="72d5f-145">LibMan cria uma pasta *wwwroot/js/signalr* e copia os arquivos selecionados nela.</span><span class="sxs-lookup"><span data-stu-id="72d5f-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72d5f-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72d5f-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="72d5f-147">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="72d5f-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="72d5f-148">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="72d5f-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="72d5f-149">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="72d5f-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="72d5f-150">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="72d5f-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="72d5f-151">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="72d5f-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="72d5f-152">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="72d5f-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="72d5f-153">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="72d5f-153">Copy only the specified files.</span></span>

  <span data-ttu-id="72d5f-154">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="72d5f-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="72d5f-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="72d5f-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="72d5f-156">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="72d5f-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="72d5f-157">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="72d5f-158">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="72d5f-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="72d5f-159">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="72d5f-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="72d5f-160">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="72d5f-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="72d5f-161">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="72d5f-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="72d5f-162">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="72d5f-162">Copy only the specified files.</span></span>

  <span data-ttu-id="72d5f-163">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="72d5f-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-opno-locsignalr-hub"></a><span data-ttu-id="72d5f-164">Criar um hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="72d5f-164">Create a SignalR hub</span></span>

<span data-ttu-id="72d5f-165">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="72d5f-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="72d5f-166">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="72d5f-167">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="72d5f-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="72d5f-168">A classe `ChatHub` herda da classe SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="72d5f-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="72d5f-169">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="72d5f-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="72d5f-170">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="72d5f-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="72d5f-171">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="72d5f-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> SignalR<span data-ttu-id="72d5f-172"> código é assíncrono para fornecer a escalabilidade máxima.</span><span class="sxs-lookup"><span data-stu-id="72d5f-172"> code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-opno-locsignalr"></a><span data-ttu-id="72d5f-173">Configurar SignalR</span><span class="sxs-lookup"><span data-stu-id="72d5f-173">Configure SignalR</span></span>

<span data-ttu-id="72d5f-174">O servidor de SignalR deve ser configurado para passar SignalR solicitações para SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="72d5f-175">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="72d5f-176">Essas alterações adicionam SignalR à injeção de dependência de ASP.NET Core e sistemas de roteamento.</span><span class="sxs-lookup"><span data-stu-id="72d5f-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-opno-locsignalr-client-code"></a><span data-ttu-id="72d5f-177">Adicionar SignalR código de cliente</span><span class="sxs-lookup"><span data-stu-id="72d5f-177">Add SignalR client code</span></span>

* <span data-ttu-id="72d5f-178">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="72d5f-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="72d5f-179">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="72d5f-179">The preceding code:</span></span>

  * <span data-ttu-id="72d5f-180">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="72d5f-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="72d5f-181">Cria uma lista com `id="messagesList"` para exibir as mensagens que são recebidas do hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="72d5f-182">Inclui referências de script para SignalR e o código do aplicativo *chat. js* que você cria na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="72d5f-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="72d5f-183">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="72d5f-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="72d5f-184">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="72d5f-184">The preceding code:</span></span>

  * <span data-ttu-id="72d5f-185">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="72d5f-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="72d5f-186">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="72d5f-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="72d5f-187">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="72d5f-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="72d5f-188">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="72d5f-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72d5f-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72d5f-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="72d5f-190">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="72d5f-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72d5f-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72d5f-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="72d5f-192">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="72d5f-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="72d5f-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="72d5f-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="72d5f-194">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="72d5f-195">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="72d5f-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="72d5f-196">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="72d5f-197">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="72d5f-197">The name and message are displayed on both pages instantly.</span></span>

  ![[! Parar. Aplicativo de exemplo não LOC (Signalr)]](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="72d5f-199">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="72d5f-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="72d5f-200">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="72d5f-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="72d5f-201">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="72d5f-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="72d5f-202">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="72d5f-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="72d5f-203">![Erro de signalr.js não encontrado](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="72d5f-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="72d5f-204">Se você receber o erro ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY no Chrome, execute estes comandos para atualizar seu certificado de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="72d5f-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="72d5f-205">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="72d5f-206">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="72d5f-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="72d5f-207">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="72d5f-207">Create a web project.</span></span>   
> * <span data-ttu-id="72d5f-208">Adicione a biblioteca de cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="72d5f-209">Crie um hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="72d5f-210">Configure o projeto a ser usado SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="72d5f-211">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="72d5f-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="72d5f-212">No final, você terá um aplicativo de chat em funcionamento: ![[! Parar. Não-LOC (Signalr)] aplicativo de exemplo](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="72d5f-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="72d5f-213">{1&gt;Pré-requisitos&lt;1}</span><span class="sxs-lookup"><span data-stu-id="72d5f-213">Prerequisites</span></span>    

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72d5f-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72d5f-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72d5f-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72d5f-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="72d5f-216">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="72d5f-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="72d5f-217">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="72d5f-217">Create a web project</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72d5f-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72d5f-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="72d5f-219">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="72d5f-220">Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="72d5f-221">Dê ao projeto o nome de *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-221">Name the project *SignalRChat*.</span></span> 

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="72d5f-223">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="72d5f-223">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="72d5f-224">Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72d5f-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72d5f-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="72d5f-227">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="72d5f-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="72d5f-228">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="72d5f-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="72d5f-229">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="72d5f-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="72d5f-230">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="72d5f-231">Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="72d5f-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="72d5f-232">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-232">Select **Next**.</span></span>  

* <span data-ttu-id="72d5f-233">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-233">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-opno-locsignalr-client-library"></a><span data-ttu-id="72d5f-234">Adicionar a biblioteca de cliente SignalR</span><span class="sxs-lookup"><span data-stu-id="72d5f-234">Add the SignalR client library</span></span> 

<span data-ttu-id="72d5f-235">A biblioteca do SignalR Server está incluída no metapacote do `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="72d5f-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="72d5f-236">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="72d5f-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="72d5f-237">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="72d5f-238">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="72d5f-238">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>  

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72d5f-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72d5f-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="72d5f-240">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="72d5f-241">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="72d5f-242">Para **Biblioteca**, insira `@microsoft/signalr@3` e selecione a versão mais recente que não seja uma versão prévia.</span><span class="sxs-lookup"><span data-stu-id="72d5f-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="72d5f-244">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="72d5f-245">Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="72d5f-247">O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.</span><span class="sxs-lookup"><span data-stu-id="72d5f-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72d5f-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72d5f-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="72d5f-249">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="72d5f-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="72d5f-250">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="72d5f-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="72d5f-251">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="72d5f-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="72d5f-252">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="72d5f-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="72d5f-253">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="72d5f-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="72d5f-254">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="72d5f-255">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="72d5f-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="72d5f-256">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="72d5f-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="72d5f-257">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="72d5f-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="72d5f-258">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="72d5f-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="72d5f-259">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="72d5f-260">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="72d5f-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="72d5f-261">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="72d5f-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="72d5f-262">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="72d5f-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="72d5f-263">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="72d5f-264">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="72d5f-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="72d5f-265">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="72d5f-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-opno-locsignalr-hub"></a><span data-ttu-id="72d5f-266">Criar um hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="72d5f-266">Create a SignalR hub</span></span>   

<span data-ttu-id="72d5f-267">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="72d5f-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="72d5f-268">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="72d5f-269">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="72d5f-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="72d5f-270">A classe `ChatHub` herda da classe SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="72d5f-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="72d5f-271">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="72d5f-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="72d5f-272">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="72d5f-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="72d5f-273">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="72d5f-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> SignalR<span data-ttu-id="72d5f-274"> código é assíncrono para fornecer a escalabilidade máxima.</span><span class="sxs-lookup"><span data-stu-id="72d5f-274"> code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-opno-locsignalr"></a><span data-ttu-id="72d5f-275">Configurar SignalR</span><span class="sxs-lookup"><span data-stu-id="72d5f-275">Configure SignalR</span></span>  

<span data-ttu-id="72d5f-276">O servidor de SignalR deve ser configurado para passar SignalR solicitações para SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="72d5f-277">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="72d5f-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="72d5f-278">Essas alterações adicionam SignalR ao sistema de injeção de dependência ASP.NET Core e ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="72d5f-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-opno-locsignalr-client-code"></a><span data-ttu-id="72d5f-279">Adicionar SignalR código de cliente</span><span class="sxs-lookup"><span data-stu-id="72d5f-279">Add SignalR client code</span></span>    

* <span data-ttu-id="72d5f-280">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="72d5f-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="72d5f-281">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="72d5f-281">The preceding code:</span></span>   

  * <span data-ttu-id="72d5f-282">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="72d5f-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="72d5f-283">Cria uma lista com `id="messagesList"` para exibir as mensagens que são recebidas do hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="72d5f-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="72d5f-284">Inclui referências de script para SignalR e o código do aplicativo *chat. js* que você cria na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="72d5f-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="72d5f-285">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="72d5f-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="72d5f-286">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="72d5f-286">The preceding code:</span></span>   

  * <span data-ttu-id="72d5f-287">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="72d5f-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="72d5f-288">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="72d5f-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="72d5f-289">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="72d5f-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="72d5f-290">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="72d5f-290">Run the app</span></span>  

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72d5f-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72d5f-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="72d5f-292">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="72d5f-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72d5f-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72d5f-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="72d5f-294">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="72d5f-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="72d5f-295">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="72d5f-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="72d5f-296">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="72d5f-297">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="72d5f-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="72d5f-298">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="72d5f-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="72d5f-299">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="72d5f-299">The name and message are displayed on both pages instantly.</span></span>   

  ![[! Parar. Aplicativo de exemplo não LOC (Signalr)]](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="72d5f-301">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="72d5f-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="72d5f-302">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="72d5f-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="72d5f-303">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="72d5f-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="72d5f-304">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="72d5f-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="72d5f-305">![Erro de signalr.js não encontrado](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="72d5f-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="72d5f-306">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="72d5f-306">Additional resources</span></span> 
* [<span data-ttu-id="72d5f-307">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="72d5f-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
