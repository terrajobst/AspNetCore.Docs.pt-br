---
title: Introdução ao SignalR para ASP.NET Core
author: bradygaster
description: Neste tutorial, você criará um aplicativo de chat que usa o SignalR para ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/signalr
ms.openlocfilehash: 078f1875d22a90f90575826e6f212205cd4b3d5b
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519185"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="a8fd5-103">Tutorial: introdução ao SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8fd5-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8fd5-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="a8fd5-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a8fd5-106">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-106">Create a web project.</span></span>
> * <span data-ttu-id="a8fd5-107">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="a8fd5-108">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="a8fd5-109">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="a8fd5-110">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="a8fd5-111">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-111">At the end, you'll have a working chat app:</span></span>

![Aplicativo de exemplo do SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="a8fd5-113">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="a8fd5-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8fd5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8fd5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8fd5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8fd5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8fd5-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8fd5-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="a8fd5-117">Criar um projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="a8fd5-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8fd5-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8fd5-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="a8fd5-119">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="a8fd5-120">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core** e depois selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="a8fd5-121">Na caixa de diálogo **Configurar seu novo projeto**, nomeie o projeto *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="a8fd5-122">Na caixa de diálogo **criar um novo aplicativo web ASP.NET Core** , selecione **.net Core** e **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="a8fd5-123">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8fd5-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8fd5-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="a8fd5-126">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="a8fd5-127">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8fd5-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8fd5-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a8fd5-129">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="a8fd5-130">Selecione **.NET Core > Aplicativo > Aplicativo Web** (não selecione **Aplicativo Web (Model-View-Controller)** ) e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="a8fd5-131">Verifique se a **Estrutura de Destino** está definida para **.NET Core 3.0** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="a8fd5-132">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="a8fd5-133">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-133">Add the SignalR client library</span></span>

<span data-ttu-id="a8fd5-134">A biblioteca do servidor SignalR está incluída na estrutura compartilhada do ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="a8fd5-135">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="a8fd5-136">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="a8fd5-137">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8fd5-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8fd5-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="a8fd5-139">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="a8fd5-140">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="a8fd5-141">Para **Biblioteca**, insira `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="a8fd5-142">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="a8fd5-143">Defina **local de destino** como *wwwroot/js/signalr/* e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="a8fd5-145">LibMan cria uma pasta *wwwroot/js/signalr* e copia os arquivos selecionados nela.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8fd5-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8fd5-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="a8fd5-147">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="a8fd5-148">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="a8fd5-149">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="a8fd5-150">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="a8fd5-151">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="a8fd5-152">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="a8fd5-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="a8fd5-153">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-153">Copy only the specified files.</span></span>

  <span data-ttu-id="a8fd5-154">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8fd5-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8fd5-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a8fd5-156">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="a8fd5-157">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="a8fd5-158">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="a8fd5-159">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="a8fd5-160">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="a8fd5-161">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="a8fd5-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="a8fd5-162">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-162">Copy only the specified files.</span></span>

  <span data-ttu-id="a8fd5-163">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="a8fd5-164">Criar um hub do SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-164">Create a SignalR hub</span></span>

<span data-ttu-id="a8fd5-165">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="a8fd5-166">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="a8fd5-167">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="a8fd5-168">A classe `ChatHub` é herda da classe `Hub` do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="a8fd5-169">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="a8fd5-170">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="a8fd5-171">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="a8fd5-172">O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="a8fd5-173">Configurar o SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-173">Configure SignalR</span></span>

<span data-ttu-id="a8fd5-174">O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="a8fd5-175">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="a8fd5-176">Essas alterações adicionam o SignalR ao sistemas de injeção de dependência e roteamento do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="a8fd5-177">Adicionar o código de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-177">Add SignalR client code</span></span>

* <span data-ttu-id="a8fd5-178">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="a8fd5-179">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-179">The preceding code:</span></span>

  * <span data-ttu-id="a8fd5-180">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="a8fd5-181">Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="a8fd5-182">Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="a8fd5-183">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="a8fd5-184">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-184">The preceding code:</span></span>

  * <span data-ttu-id="a8fd5-185">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="a8fd5-186">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="a8fd5-187">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="a8fd5-188">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a8fd5-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8fd5-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8fd5-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a8fd5-190">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8fd5-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8fd5-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a8fd5-192">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8fd5-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8fd5-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a8fd5-194">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="a8fd5-195">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="a8fd5-196">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="a8fd5-197">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-197">The name and message are displayed on both pages instantly.</span></span>

  ![Aplicativo de exemplo do SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="a8fd5-199">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="a8fd5-200">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="a8fd5-201">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="a8fd5-202">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="a8fd5-203">![Erro de signalr.js não encontrado](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="a8fd5-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="a8fd5-204">Se você receber o erro ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY no Chrome, execute estes comandos para atualizar seu certificado de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="a8fd5-205">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a8fd5-205">Next steps</span></span>

<span data-ttu-id="a8fd5-206">Para saber mais sobre o SignalR, confira a introdução:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="a8fd5-207">Introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a8fd5-208">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="a8fd5-209">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-209">You learn how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="a8fd5-210">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-210">Create a web project.</span></span>   
> * <span data-ttu-id="a8fd5-211">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-211">Add the SignalR client library.</span></span> 
> * <span data-ttu-id="a8fd5-212">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-212">Create a SignalR hub.</span></span>   
> * <span data-ttu-id="a8fd5-213">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-213">Configure the project to use SignalR.</span></span>   
> * <span data-ttu-id="a8fd5-214">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-214">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="a8fd5-215">No final, você terá um aplicativo de chat em funcionamento: aplicativo de exemplo ![SignalR @ no__t-1</span><span class="sxs-lookup"><span data-stu-id="a8fd5-215">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

## <a name="prerequisites"></a><span data-ttu-id="a8fd5-216">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="a8fd5-216">Prerequisites</span></span>    

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8fd5-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8fd5-217">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8fd5-218">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8fd5-218">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8fd5-219">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8fd5-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="a8fd5-220">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="a8fd5-220">Create a web project</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8fd5-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8fd5-221">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="a8fd5-222">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-222">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="a8fd5-223">Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-223">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a8fd5-224">Dê ao projeto o nome de *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-224">Name the project *SignalRChat*.</span></span> 

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="a8fd5-226">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-226">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="a8fd5-227">Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-227">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8fd5-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8fd5-229">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="a8fd5-230">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-230">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="a8fd5-231">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-231">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8fd5-232">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8fd5-232">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="a8fd5-233">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-233">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="a8fd5-234">Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="a8fd5-234">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="a8fd5-235">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-235">Select **Next**.</span></span>  

* <span data-ttu-id="a8fd5-236">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-236">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="a8fd5-237">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-237">Add the SignalR client library</span></span>   

<span data-ttu-id="a8fd5-238">A biblioteca do servidor SignalR está incluída no metapacote `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-238">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="a8fd5-239">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-239">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="a8fd5-240">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-240">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="a8fd5-241">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-241">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>    

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8fd5-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8fd5-242">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="a8fd5-243">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-243">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="a8fd5-244">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-244">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="a8fd5-245">Para **Biblioteca**, insira `@aspnet/signalr@1` e selecione a versão mais recente que não seja uma versão prévia.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-245">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span> 

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="a8fd5-247">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-247">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="a8fd5-248">Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-248">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="a8fd5-250">O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-250">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8fd5-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8fd5-251">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="a8fd5-252">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-252">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="a8fd5-253">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-253">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="a8fd5-254">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-254">You might have to wait a few seconds before seeing output.</span></span>   

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  <span data-ttu-id="a8fd5-255">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-255">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="a8fd5-256">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-256">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="a8fd5-257">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-257">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="a8fd5-258">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-258">Copy only the specified files.</span></span>  

  <span data-ttu-id="a8fd5-259">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-259">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8fd5-260">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8fd5-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="a8fd5-261">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-261">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="a8fd5-262">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-262">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="a8fd5-263">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-263">Run the following command to get the SignalR client library by using LibMan.</span></span>  

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  <span data-ttu-id="a8fd5-264">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-264">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="a8fd5-265">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-265">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="a8fd5-266">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-266">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="a8fd5-267">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-267">Copy only the specified files.</span></span>  

  <span data-ttu-id="a8fd5-268">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-268">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

--- 

## <a name="create-a-signalr-hub"></a><span data-ttu-id="a8fd5-269">Criar um hub do SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-269">Create a SignalR hub</span></span> 

<span data-ttu-id="a8fd5-270">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-270">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="a8fd5-271">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-271">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="a8fd5-272">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-272">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="a8fd5-273">A classe `ChatHub` é herda da classe `Hub` do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-273">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="a8fd5-274">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-274">The `Hub` class manages connections, groups, and messaging.</span></span>    

  <span data-ttu-id="a8fd5-275">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-275">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="a8fd5-276">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-276">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="a8fd5-277">O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-277">SignalR code is asynchronous to provide maximum scalability.</span></span>  

## <a name="configure-signalr"></a><span data-ttu-id="a8fd5-278">Configurar o SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-278">Configure SignalR</span></span>    

<span data-ttu-id="a8fd5-279">O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-279">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>  

* <span data-ttu-id="a8fd5-280">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-280">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="a8fd5-281">Essas alterações adicionam o SignalR ao sistema de injeção de dependência e ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-281">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>    

## <a name="add-signalr-client-code"></a><span data-ttu-id="a8fd5-282">Adicionar o código de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-282">Add SignalR client code</span></span>  

* <span data-ttu-id="a8fd5-283">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-283">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="a8fd5-284">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-284">The preceding code:</span></span>   

  * <span data-ttu-id="a8fd5-285">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-285">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="a8fd5-286">Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-286">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span> 
  * <span data-ttu-id="a8fd5-287">Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-287">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>  

* <span data-ttu-id="a8fd5-288">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-288">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="a8fd5-289">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-289">The preceding code:</span></span>   

  * <span data-ttu-id="a8fd5-290">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-290">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="a8fd5-291">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-291">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="a8fd5-292">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-292">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="a8fd5-293">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a8fd5-293">Run the app</span></span>  

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8fd5-294">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8fd5-294">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="a8fd5-295">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-295">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a8fd5-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8fd5-296">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="a8fd5-297">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-297">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli  
  dotnet run -p SignalRChat.csproj  
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a8fd5-298">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a8fd5-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="a8fd5-299">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-299">From the menu, select **Run > Start Without Debugging**.</span></span>  

--- 

* <span data-ttu-id="a8fd5-300">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-300">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>    

* <span data-ttu-id="a8fd5-301">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-301">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="a8fd5-302">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-302">The name and message are displayed on both pages instantly.</span></span>   

  ![Aplicativo de exemplo do SignalR](signalr/_static/2.x/signalr-get-started-finished.png)   

> [!TIP]    
> <span data-ttu-id="a8fd5-304">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-304">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="a8fd5-305">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-305">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="a8fd5-306">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-306">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="a8fd5-307">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-307">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="a8fd5-308">![Erro de signalr.js não encontrado](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="a8fd5-308">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="a8fd5-309">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a8fd5-309">Additional resources</span></span> 
* [<span data-ttu-id="a8fd5-310">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="a8fd5-310">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

## <a name="next-steps"></a><span data-ttu-id="a8fd5-311">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a8fd5-311">Next steps</span></span>   

<span data-ttu-id="a8fd5-312">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-312">In this tutorial, you learned how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="a8fd5-313">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-313">Create a web app project.</span></span>   
> * <span data-ttu-id="a8fd5-314">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-314">Add the SignalR client library.</span></span> 
> * <span data-ttu-id="a8fd5-315">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-315">Create a SignalR hub.</span></span>   
> * <span data-ttu-id="a8fd5-316">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-316">Configure the project to use SignalR.</span></span>   
> * <span data-ttu-id="a8fd5-317">Adicionar o código que usa o hub para enviar mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="a8fd5-317">Add code that uses the hub to send messages from any client to all connected clients.</span></span>   
<span data-ttu-id="a8fd5-318">Para saber mais sobre o SignalR, confira a introdução:</span><span class="sxs-lookup"><span data-stu-id="a8fd5-318">To learn more about SignalR, see the introduction:</span></span>  
> [!div class="nextstepaction"] 
> [<span data-ttu-id="a8fd5-319">Introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="a8fd5-319">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction) 
::: moniker-end

