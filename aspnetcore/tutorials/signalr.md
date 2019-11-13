---
title: Introdução ao ASP.NET Core SignalR
author: bradygaster
description: Neste tutorial, você cria um aplicativo de chat que usa ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 962cc0318ebbfc7fac16ca0947a2e3e83e51665c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73964036"
---
# <a name="tutorial-get-started-with-aspnet-core-opno-locsignalr"></a><span data-ttu-id="f9851-103">Tutorial: introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="f9851-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9851-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="f9851-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="f9851-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f9851-106">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="f9851-106">Create a web project.</span></span>
> * <span data-ttu-id="f9851-107">Adicione a biblioteca de cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="f9851-108">Crie um hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="f9851-109">Configure o projeto a ser usado SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="f9851-110">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="f9851-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="f9851-111">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="f9851-111">At the end, you'll have a working chat app:</span></span>

![[! Parar. Aplicativo de exemplo não LOC (Signalr)]](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="f9851-113">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="f9851-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9851-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9851-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f9851-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9851-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f9851-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f9851-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="f9851-117">Criar um projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="f9851-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9851-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9851-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="f9851-119">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="f9851-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="f9851-120">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core** e depois selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="f9851-121">Na caixa de diálogo **Configurar seu novo projeto**, nomeie o projeto *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="f9851-122">Na caixa de diálogo **criar um novo aplicativo web ASP.NET Core** , selecione **.net Core** e **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="f9851-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="f9851-123">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f9851-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9851-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="f9851-126">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="f9851-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="f9851-127">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f9851-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f9851-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f9851-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f9851-129">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="f9851-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="f9851-130">Selecione **.NET Core > Aplicativo > Aplicativo Web** (não selecione **Aplicativo Web (Model-View-Controller)** ) e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="f9851-131">Verifique se a **Estrutura de Destino** está definida para **.NET Core 3.0** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="f9851-132">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-opno-locsignalr-client-library"></a><span data-ttu-id="f9851-133">Adicionar a biblioteca de cliente SignalR</span><span class="sxs-lookup"><span data-stu-id="f9851-133">Add the SignalR client library</span></span>

<span data-ttu-id="f9851-134">A biblioteca do SignalR Server está incluída na estrutura compartilhada do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f9851-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="f9851-135">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="f9851-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="f9851-136">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="f9851-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="f9851-137">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="f9851-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9851-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9851-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="f9851-139">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="f9851-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="f9851-140">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="f9851-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="f9851-141">Para **Biblioteca**, insira `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="f9851-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="f9851-142">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="f9851-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="f9851-143">Defina **local de destino** como *wwwroot/js/signalr/* e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="f9851-145">LibMan cria uma pasta *wwwroot/js/signalr* e copia os arquivos selecionados nela.</span><span class="sxs-lookup"><span data-stu-id="f9851-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f9851-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9851-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="f9851-147">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="f9851-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="f9851-148">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="f9851-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="f9851-149">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="f9851-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="f9851-150">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="f9851-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="f9851-151">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="f9851-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="f9851-152">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="f9851-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="f9851-153">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="f9851-153">Copy only the specified files.</span></span>

  <span data-ttu-id="f9851-154">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="f9851-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f9851-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f9851-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f9851-156">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="f9851-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="f9851-157">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="f9851-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="f9851-158">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="f9851-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="f9851-159">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="f9851-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="f9851-160">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="f9851-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="f9851-161">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="f9851-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="f9851-162">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="f9851-162">Copy only the specified files.</span></span>

  <span data-ttu-id="f9851-163">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="f9851-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-opno-locsignalr-hub"></a><span data-ttu-id="f9851-164">Criar um hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="f9851-164">Create a SignalR hub</span></span>

<span data-ttu-id="f9851-165">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="f9851-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="f9851-166">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="f9851-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="f9851-167">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f9851-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="f9851-168">A classe `ChatHub` herda da classe SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="f9851-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="f9851-169">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="f9851-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="f9851-170">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="f9851-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="f9851-171">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="f9851-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> SignalR<span data-ttu-id="f9851-172"> código é assíncrono para fornecer a escalabilidade máxima.</span><span class="sxs-lookup"><span data-stu-id="f9851-172"> code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-opno-locsignalr"></a><span data-ttu-id="f9851-173">Configurar SignalR</span><span class="sxs-lookup"><span data-stu-id="f9851-173">Configure SignalR</span></span>

<span data-ttu-id="f9851-174">O servidor de SignalR deve ser configurado para passar SignalR solicitações para SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="f9851-175">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f9851-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="f9851-176">Essas alterações adicionam SignalR à injeção de dependência de ASP.NET Core e sistemas de roteamento.</span><span class="sxs-lookup"><span data-stu-id="f9851-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-opno-locsignalr-client-code"></a><span data-ttu-id="f9851-177">Adicionar SignalR código de cliente</span><span class="sxs-lookup"><span data-stu-id="f9851-177">Add SignalR client code</span></span>

* <span data-ttu-id="f9851-178">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f9851-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="f9851-179">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="f9851-179">The preceding code:</span></span>

  * <span data-ttu-id="f9851-180">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="f9851-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="f9851-181">Cria uma lista com `id="messagesList"` para exibir as mensagens que são recebidas do hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="f9851-182">Inclui referências de script para SignalR e o código do aplicativo *chat. js* que você cria na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="f9851-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="f9851-183">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f9851-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="f9851-184">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="f9851-184">The preceding code:</span></span>

  * <span data-ttu-id="f9851-185">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="f9851-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="f9851-186">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="f9851-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="f9851-187">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="f9851-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="f9851-188">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9851-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9851-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9851-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f9851-190">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="f9851-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f9851-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9851-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f9851-192">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f9851-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f9851-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f9851-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f9851-194">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="f9851-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="f9851-195">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="f9851-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="f9851-196">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="f9851-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="f9851-197">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="f9851-197">The name and message are displayed on both pages instantly.</span></span>

  ![[! Parar. Aplicativo de exemplo não LOC (Signalr)]](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="f9851-199">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="f9851-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="f9851-200">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f9851-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="f9851-201">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="f9851-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="f9851-202">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="f9851-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="f9851-203">![Erro de signalr.js não encontrado](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="f9851-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="f9851-204">Se você receber o erro ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY no Chrome, execute estes comandos para atualizar seu certificado de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="f9851-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="f9851-205">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f9851-205">Next steps</span></span>

<span data-ttu-id="f9851-206">Para saber mais sobre SignalR, consulte a introdução:</span><span class="sxs-lookup"><span data-stu-id="f9851-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="f9851-207">[Introdução ao ASP.NET Core SignalR](xref:signalr/introduction)</span><span class="sxs-lookup"><span data-stu-id="f9851-207">[Introduction to ASP.NET Core SignalR](xref:signalr/introduction)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f9851-208">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="f9851-209">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="f9851-209">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="f9851-210">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="f9851-210">Create a web project.</span></span>   
> * <span data-ttu-id="f9851-211">Adicione a biblioteca de cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-211">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="f9851-212">Crie um hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-212">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="f9851-213">Configure o projeto a ser usado SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-213">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="f9851-214">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="f9851-214">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="f9851-215">No final, você terá um aplicativo de chat em funcionamento: ![[! Parar. Não-LOC (Signalr)] aplicativo de exemplo](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="f9851-215">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="f9851-216">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="f9851-216">Prerequisites</span></span>    

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9851-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9851-217">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f9851-218">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9851-218">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f9851-219">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f9851-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="f9851-220">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="f9851-220">Create a web project</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9851-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9851-221">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="f9851-222">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="f9851-222">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="f9851-223">Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f9851-223">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f9851-224">Dê ao projeto o nome de *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="f9851-224">Name the project *SignalRChat*.</span></span> 

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="f9851-226">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f9851-226">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="f9851-227">Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="f9851-227">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f9851-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9851-229">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="f9851-230">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="f9851-230">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="f9851-231">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f9851-231">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f9851-232">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f9851-232">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="f9851-233">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="f9851-233">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="f9851-234">Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="f9851-234">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="f9851-235">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-235">Select **Next**.</span></span>  

* <span data-ttu-id="f9851-236">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-236">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-opno-locsignalr-client-library"></a><span data-ttu-id="f9851-237">Adicionar a biblioteca de cliente SignalR</span><span class="sxs-lookup"><span data-stu-id="f9851-237">Add the SignalR client library</span></span> 

<span data-ttu-id="f9851-238">A biblioteca do SignalR Server está incluída no metapacote do `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="f9851-238">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="f9851-239">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="f9851-239">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="f9851-240">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="f9851-240">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="f9851-241">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="f9851-241">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>  

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9851-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9851-242">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="f9851-243">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="f9851-243">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="f9851-244">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="f9851-244">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="f9851-245">Para **Biblioteca**, insira `@aspnet/signalr@1` e selecione a versão mais recente que não seja uma versão prévia.</span><span class="sxs-lookup"><span data-stu-id="f9851-245">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span> 

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="f9851-247">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="f9851-247">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="f9851-248">Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="f9851-248">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="f9851-250">O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.</span><span class="sxs-lookup"><span data-stu-id="f9851-250">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f9851-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9851-251">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="f9851-252">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="f9851-252">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="f9851-253">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="f9851-253">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="f9851-254">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="f9851-254">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  <span data-ttu-id="f9851-255">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="f9851-255">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="f9851-256">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="f9851-256">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="f9851-257">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="f9851-257">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="f9851-258">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="f9851-258">Copy only the specified files.</span></span>  

  <span data-ttu-id="f9851-259">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="f9851-259">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f9851-260">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f9851-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="f9851-261">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="f9851-261">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="f9851-262">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="f9851-262">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="f9851-263">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="f9851-263">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  <span data-ttu-id="f9851-264">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="f9851-264">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="f9851-265">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="f9851-265">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="f9851-266">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="f9851-266">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="f9851-267">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="f9851-267">Copy only the specified files.</span></span>  

  <span data-ttu-id="f9851-268">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="f9851-268">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

--- 

## <a name="create-a-opno-locsignalr-hub"></a><span data-ttu-id="f9851-269">Criar um hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="f9851-269">Create a SignalR hub</span></span>   

<span data-ttu-id="f9851-270">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="f9851-270">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="f9851-271">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="f9851-271">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="f9851-272">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f9851-272">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="f9851-273">A classe `ChatHub` herda da classe SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="f9851-273">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="f9851-274">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="f9851-274">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="f9851-275">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="f9851-275">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="f9851-276">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="f9851-276">JavaScript client code that calls the method is shown later in the tutorial.</span></span> SignalR<span data-ttu-id="f9851-277"> código é assíncrono para fornecer a escalabilidade máxima.</span><span class="sxs-lookup"><span data-stu-id="f9851-277"> code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-opno-locsignalr"></a><span data-ttu-id="f9851-278">Configurar SignalR</span><span class="sxs-lookup"><span data-stu-id="f9851-278">Configure SignalR</span></span>  

<span data-ttu-id="f9851-279">O servidor de SignalR deve ser configurado para passar SignalR solicitações para SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-279">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="f9851-280">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f9851-280">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="f9851-281">Essas alterações adicionam SignalR ao sistema de injeção de dependência ASP.NET Core e ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="f9851-281">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-opno-locsignalr-client-code"></a><span data-ttu-id="f9851-282">Adicionar SignalR código de cliente</span><span class="sxs-lookup"><span data-stu-id="f9851-282">Add SignalR client code</span></span>    

* <span data-ttu-id="f9851-283">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f9851-283">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="f9851-284">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="f9851-284">The preceding code:</span></span>   

  * <span data-ttu-id="f9851-285">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="f9851-285">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="f9851-286">Cria uma lista com `id="messagesList"` para exibir as mensagens que são recebidas do hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-286">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="f9851-287">Inclui referências de script para SignalR e o código do aplicativo *chat. js* que você cria na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="f9851-287">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="f9851-288">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f9851-288">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="f9851-289">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="f9851-289">The preceding code:</span></span>   

  * <span data-ttu-id="f9851-290">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="f9851-290">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="f9851-291">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="f9851-291">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="f9851-292">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="f9851-292">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="f9851-293">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f9851-293">Run the app</span></span>  

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9851-294">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9851-294">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="f9851-295">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="f9851-295">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f9851-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9851-296">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="f9851-297">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f9851-297">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli  
  dotnet run -p SignalRChat.csproj  
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f9851-298">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f9851-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="f9851-299">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="f9851-299">From the menu, select **Run > Start Without Debugging**.</span></span>  

--- 

* <span data-ttu-id="f9851-300">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="f9851-300">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>    

* <span data-ttu-id="f9851-301">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="f9851-301">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="f9851-302">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="f9851-302">The name and message are displayed on both pages instantly.</span></span>   

  ![[! Parar. Aplicativo de exemplo não LOC (Signalr)]](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="f9851-304">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="f9851-304">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="f9851-305">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f9851-305">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="f9851-306">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="f9851-306">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="f9851-307">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="f9851-307">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="f9851-308">![Erro de signalr.js não encontrado](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="f9851-308">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="f9851-309">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f9851-309">Additional resources</span></span> 
* [<span data-ttu-id="f9851-310">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="f9851-310">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

## <a name="next-steps"></a><span data-ttu-id="f9851-311">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f9851-311">Next steps</span></span>   

<span data-ttu-id="f9851-312">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="f9851-312">In this tutorial, you learned how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="f9851-313">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="f9851-313">Create a web app project.</span></span>   
> * <span data-ttu-id="f9851-314">Adicione a biblioteca de cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-314">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="f9851-315">Crie um hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-315">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="f9851-316">Configure o projeto a ser usado SignalR.</span><span class="sxs-lookup"><span data-stu-id="f9851-316">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="f9851-317">Adicionar o código que usa o hub para enviar mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="f9851-317">Add code that uses the hub to send messages from any client to all connected clients.</span></span>   
<span data-ttu-id="f9851-318">Para saber mais sobre SignalR, consulte a introdução:</span><span class="sxs-lookup"><span data-stu-id="f9851-318">To learn more about SignalR, see the introduction:</span></span>    
> [!div class="nextstepaction"] 
> <span data-ttu-id="f9851-319">[Introdução ao ASP.NET Core SignalR](xref:signalr/introduction)</span><span class="sxs-lookup"><span data-stu-id="f9851-319">[Introduction to ASP.NET Core SignalR](xref:signalr/introduction)</span></span>   
::: moniker-end

