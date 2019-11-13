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
ms.openlocfilehash: ac727ed0517a8b30fd8194c010576fdd74a5950a
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74052852"
---
# <a name="tutorial-get-started-with-aspnet-core-opno-locsignalr"></a><span data-ttu-id="cb0e3-103">Tutorial: introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="cb0e3-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cb0e3-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="cb0e3-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cb0e3-106">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-106">Create a web project.</span></span>
> * <span data-ttu-id="cb0e3-107">Adicione a biblioteca de cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="cb0e3-108">Crie um hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="cb0e3-109">Configure o projeto a ser usado SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="cb0e3-110">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="cb0e3-111">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-111">At the end, you'll have a working chat app:</span></span>

![[! Parar. Aplicativo de exemplo não LOC (Signalr)]](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="cb0e3-113">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="cb0e3-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cb0e3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb0e3-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cb0e3-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb0e3-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cb0e3-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cb0e3-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="cb0e3-117">Criar um projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="cb0e3-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cb0e3-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb0e3-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="cb0e3-119">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="cb0e3-120">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core** e depois selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="cb0e3-121">Na caixa de diálogo **Configurar seu novo projeto**, nomeie o projeto *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="cb0e3-122">Na caixa de diálogo **criar um novo aplicativo web ASP.NET Core** , selecione **.net Core** e **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="cb0e3-123">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cb0e3-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb0e3-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="cb0e3-126">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="cb0e3-127">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cb0e3-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cb0e3-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cb0e3-129">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="cb0e3-130">Selecione **.NET Core > Aplicativo > Aplicativo Web** (não selecione **Aplicativo Web (Model-View-Controller)** ) e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="cb0e3-131">Verifique se a **Estrutura de Destino** está definida para **.NET Core 3.0** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="cb0e3-132">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-opno-locsignalr-client-library"></a><span data-ttu-id="cb0e3-133">Adicionar a biblioteca de cliente SignalR</span><span class="sxs-lookup"><span data-stu-id="cb0e3-133">Add the SignalR client library</span></span>

<span data-ttu-id="cb0e3-134">A biblioteca do SignalR Server está incluída na estrutura compartilhada do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="cb0e3-135">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="cb0e3-136">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="cb0e3-137">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cb0e3-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb0e3-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="cb0e3-139">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="cb0e3-140">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="cb0e3-141">Para **Biblioteca**, insira `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="cb0e3-142">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="cb0e3-143">Defina **local de destino** como *wwwroot/js/signalr/* e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="cb0e3-145">LibMan cria uma pasta *wwwroot/js/signalr* e copia os arquivos selecionados nela.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cb0e3-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb0e3-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="cb0e3-147">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="cb0e3-148">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="cb0e3-149">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="cb0e3-150">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="cb0e3-151">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="cb0e3-152">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="cb0e3-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="cb0e3-153">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-153">Copy only the specified files.</span></span>

  <span data-ttu-id="cb0e3-154">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cb0e3-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cb0e3-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cb0e3-156">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="cb0e3-157">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="cb0e3-158">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="cb0e3-159">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="cb0e3-160">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="cb0e3-161">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="cb0e3-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="cb0e3-162">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-162">Copy only the specified files.</span></span>

  <span data-ttu-id="cb0e3-163">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-opno-locsignalr-hub"></a><span data-ttu-id="cb0e3-164">Criar um hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="cb0e3-164">Create a SignalR hub</span></span>

<span data-ttu-id="cb0e3-165">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="cb0e3-166">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="cb0e3-167">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="cb0e3-168">A classe `ChatHub` herda da classe SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="cb0e3-169">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="cb0e3-170">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="cb0e3-171">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> SignalR<span data-ttu-id="cb0e3-172"> código é assíncrono para fornecer a escalabilidade máxima.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-172"> code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-opno-locsignalr"></a><span data-ttu-id="cb0e3-173">Configurar SignalR</span><span class="sxs-lookup"><span data-stu-id="cb0e3-173">Configure SignalR</span></span>

<span data-ttu-id="cb0e3-174">O servidor de SignalR deve ser configurado para passar SignalR solicitações para SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="cb0e3-175">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="cb0e3-176">Essas alterações adicionam SignalR à injeção de dependência de ASP.NET Core e sistemas de roteamento.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-opno-locsignalr-client-code"></a><span data-ttu-id="cb0e3-177">Adicionar SignalR código de cliente</span><span class="sxs-lookup"><span data-stu-id="cb0e3-177">Add SignalR client code</span></span>

* <span data-ttu-id="cb0e3-178">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="cb0e3-179">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-179">The preceding code:</span></span>

  * <span data-ttu-id="cb0e3-180">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="cb0e3-181">Cria uma lista com `id="messagesList"` para exibir as mensagens que são recebidas do hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="cb0e3-182">Inclui referências de script para SignalR e o código do aplicativo *chat. js* que você cria na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="cb0e3-183">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="cb0e3-184">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-184">The preceding code:</span></span>

  * <span data-ttu-id="cb0e3-185">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="cb0e3-186">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="cb0e3-187">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="cb0e3-188">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="cb0e3-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cb0e3-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb0e3-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cb0e3-190">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cb0e3-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb0e3-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cb0e3-192">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cb0e3-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cb0e3-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cb0e3-194">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="cb0e3-195">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="cb0e3-196">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="cb0e3-197">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-197">The name and message are displayed on both pages instantly.</span></span>

  ![[! Parar. Aplicativo de exemplo não LOC (Signalr)]](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="cb0e3-199">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="cb0e3-200">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="cb0e3-201">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="cb0e3-202">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="cb0e3-203">![Erro de signalr.js não encontrado](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="cb0e3-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="cb0e3-204">Se você receber o erro ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY no Chrome, execute estes comandos para atualizar seu certificado de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cb0e3-205">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="cb0e3-206">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="cb0e3-207">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-207">Create a web project.</span></span>   
> * <span data-ttu-id="cb0e3-208">Adicione a biblioteca de cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="cb0e3-209">Crie um hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="cb0e3-210">Configure o projeto a ser usado SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="cb0e3-211">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="cb0e3-212">No final, você terá um aplicativo de chat em funcionamento: ![[! Parar. Não-LOC (Signalr)] aplicativo de exemplo](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="cb0e3-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="cb0e3-213">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="cb0e3-213">Prerequisites</span></span>    

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cb0e3-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb0e3-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cb0e3-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb0e3-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cb0e3-216">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cb0e3-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="cb0e3-217">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="cb0e3-217">Create a web project</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cb0e3-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb0e3-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="cb0e3-219">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="cb0e3-220">Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cb0e3-221">Dê ao projeto o nome de *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-221">Name the project *SignalRChat*.</span></span> 

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="cb0e3-223">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-223">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="cb0e3-224">Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cb0e3-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb0e3-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="cb0e3-227">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="cb0e3-228">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cb0e3-229">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cb0e3-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="cb0e3-230">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="cb0e3-231">Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="cb0e3-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="cb0e3-232">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-232">Select **Next**.</span></span>  

* <span data-ttu-id="cb0e3-233">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-233">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-opno-locsignalr-client-library"></a><span data-ttu-id="cb0e3-234">Adicionar a biblioteca de cliente SignalR</span><span class="sxs-lookup"><span data-stu-id="cb0e3-234">Add the SignalR client library</span></span> 

<span data-ttu-id="cb0e3-235">A biblioteca do SignalR Server está incluída no metapacote do `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="cb0e3-236">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="cb0e3-237">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="cb0e3-238">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-238">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>  

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cb0e3-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb0e3-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="cb0e3-240">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="cb0e3-241">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="cb0e3-242">Para **Biblioteca**, insira `@aspnet/signalr@1` e selecione a versão mais recente que não seja uma versão prévia.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-242">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span> 

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="cb0e3-244">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="cb0e3-245">Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="cb0e3-247">O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cb0e3-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb0e3-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="cb0e3-249">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="cb0e3-250">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="cb0e3-251">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  <span data-ttu-id="cb0e3-252">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="cb0e3-253">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="cb0e3-254">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="cb0e3-255">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="cb0e3-256">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cb0e3-257">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cb0e3-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="cb0e3-258">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="cb0e3-259">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="cb0e3-260">Execute o comando a seguir para obter a biblioteca de cliente do SignalR usando o LibMan.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  <span data-ttu-id="cb0e3-261">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="cb0e3-262">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="cb0e3-263">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="cb0e3-264">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="cb0e3-265">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

--- 

## <a name="create-a-opno-locsignalr-hub"></a><span data-ttu-id="cb0e3-266">Criar um hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="cb0e3-266">Create a SignalR hub</span></span>   

<span data-ttu-id="cb0e3-267">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="cb0e3-268">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="cb0e3-269">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="cb0e3-270">A classe `ChatHub` herda da classe SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="cb0e3-271">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="cb0e3-272">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="cb0e3-273">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> SignalR<span data-ttu-id="cb0e3-274"> código é assíncrono para fornecer a escalabilidade máxima.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-274"> code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-opno-locsignalr"></a><span data-ttu-id="cb0e3-275">Configurar SignalR</span><span class="sxs-lookup"><span data-stu-id="cb0e3-275">Configure SignalR</span></span>  

<span data-ttu-id="cb0e3-276">O servidor de SignalR deve ser configurado para passar SignalR solicitações para SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="cb0e3-277">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="cb0e3-278">Essas alterações adicionam SignalR ao sistema de injeção de dependência ASP.NET Core e ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-opno-locsignalr-client-code"></a><span data-ttu-id="cb0e3-279">Adicionar SignalR código de cliente</span><span class="sxs-lookup"><span data-stu-id="cb0e3-279">Add SignalR client code</span></span>    

* <span data-ttu-id="cb0e3-280">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="cb0e3-281">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-281">The preceding code:</span></span>   

  * <span data-ttu-id="cb0e3-282">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="cb0e3-283">Cria uma lista com `id="messagesList"` para exibir as mensagens que são recebidas do hub de SignalR.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="cb0e3-284">Inclui referências de script para SignalR e o código do aplicativo *chat. js* que você cria na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="cb0e3-285">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="cb0e3-286">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-286">The preceding code:</span></span>   

  * <span data-ttu-id="cb0e3-287">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="cb0e3-288">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="cb0e3-289">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="cb0e3-290">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="cb0e3-290">Run the app</span></span>  

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cb0e3-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb0e3-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="cb0e3-292">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cb0e3-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb0e3-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="cb0e3-294">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cb0e3-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli  
  dotnet run -p SignalRChat.csproj  
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cb0e3-295">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cb0e3-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="cb0e3-296">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-296">From the menu, select **Run > Start Without Debugging**.</span></span>  

--- 

* <span data-ttu-id="cb0e3-297">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>    

* <span data-ttu-id="cb0e3-298">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="cb0e3-299">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-299">The name and message are displayed on both pages instantly.</span></span>   

  ![[! Parar. Aplicativo de exemplo não LOC (Signalr)]](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="cb0e3-301">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="cb0e3-302">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="cb0e3-303">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="cb0e3-304">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="cb0e3-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="cb0e3-305">![Erro de signalr.js não encontrado](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="cb0e3-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="cb0e3-306">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cb0e3-306">Additional resources</span></span> 
* [<span data-ttu-id="cb0e3-307">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="cb0e3-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
