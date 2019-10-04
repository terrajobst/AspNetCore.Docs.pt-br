---
title: Introdução ao SignalR para ASP.NET Core
author: bradygaster
description: Neste tutorial, você criará um aplicativo de chat que usa o SignalR para ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2019
uid: tutorials/signalr
ms.openlocfilehash: bec01adc2682f83b0225df66e221bd2e4ea9feb4
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925332"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="7ea81-103">Tutorial: Introdução ao SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ea81-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ea81-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="7ea81-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="7ea81-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7ea81-106">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="7ea81-106">Create a web project.</span></span>
> * <span data-ttu-id="7ea81-107">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="7ea81-108">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="7ea81-109">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="7ea81-110">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="7ea81-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="7ea81-111">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="7ea81-111">At the end, you'll have a working chat app:</span></span>

![Aplicativo de exemplo do SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="7ea81-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7ea81-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea81-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea81-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea81-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea81-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7ea81-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7ea81-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="7ea81-117">Criar um projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="7ea81-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea81-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea81-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="7ea81-119">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="7ea81-120">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core** e depois selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="7ea81-121">Na caixa de diálogo **Configurar seu novo projeto**, nomeie o projeto *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="7ea81-122">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, selecione **.NET Core** e **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-122">In the **Create a new ASP.NET Core Web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="7ea81-123">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea81-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea81-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="7ea81-126">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="7ea81-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="7ea81-127">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="7ea81-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7ea81-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7ea81-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7ea81-129">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="7ea81-130">Selecione **.NET Core > Aplicativo > Aplicativo Web** (não selecione **Aplicativo Web (Model-View-Controller)** ) e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="7ea81-131">Verifique se a **Estrutura de Destino** está definida para **.NET Core 3.0** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="7ea81-132">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="7ea81-133">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-133">Add the SignalR client library</span></span>

<span data-ttu-id="7ea81-134">A biblioteca do servidor SignalR está incluída na estrutura compartilhada do ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="7ea81-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="7ea81-135">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="7ea81-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="7ea81-136">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="7ea81-137">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="7ea81-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea81-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea81-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="7ea81-139">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="7ea81-140">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="7ea81-141">Para **Biblioteca**, insira `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="7ea81-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="7ea81-142">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="7ea81-143">Defina **local de destino** como *wwwroot/js/signalr/* e selecione **instalar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="7ea81-145">LibMan cria uma pasta *wwwroot/js/signalr* e copia os arquivos selecionados nela.</span><span class="sxs-lookup"><span data-stu-id="7ea81-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea81-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea81-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="7ea81-147">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="7ea81-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="7ea81-148">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="7ea81-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="7ea81-149">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="7ea81-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="7ea81-150">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="7ea81-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="7ea81-151">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="7ea81-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="7ea81-152">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="7ea81-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="7ea81-153">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="7ea81-153">Copy only the specified files.</span></span>

  <span data-ttu-id="7ea81-154">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="7ea81-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7ea81-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7ea81-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7ea81-156">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="7ea81-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="7ea81-157">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="7ea81-158">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="7ea81-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="7ea81-159">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="7ea81-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="7ea81-160">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="7ea81-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="7ea81-161">Copie os arquivos para o destino *wwwroot/js/signalr* .</span><span class="sxs-lookup"><span data-stu-id="7ea81-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="7ea81-162">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="7ea81-162">Copy only the specified files.</span></span>

  <span data-ttu-id="7ea81-163">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="7ea81-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="7ea81-164">Criar um hub do SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-164">Create a SignalR hub</span></span>

<span data-ttu-id="7ea81-165">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="7ea81-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="7ea81-166">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="7ea81-167">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ea81-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="7ea81-168">A classe `ChatHub` é herda da classe `Hub` do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="7ea81-169">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="7ea81-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="7ea81-170">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="7ea81-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="7ea81-171">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="7ea81-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="7ea81-172">O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.</span><span class="sxs-lookup"><span data-stu-id="7ea81-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="7ea81-173">Configurar o SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-173">Configure SignalR</span></span>

<span data-ttu-id="7ea81-174">O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="7ea81-175">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=6,30,58)]

  <span data-ttu-id="7ea81-176">Essas alterações adicionam o SignalR ao sistemas de injeção de dependência e roteamento do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ea81-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="7ea81-177">Adicionar o código de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-177">Add SignalR client code</span></span>

* <span data-ttu-id="7ea81-178">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ea81-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="7ea81-179">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ea81-179">The preceding code:</span></span>

  * <span data-ttu-id="7ea81-180">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="7ea81-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="7ea81-181">Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="7ea81-182">Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="7ea81-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="7ea81-183">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ea81-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="7ea81-184">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ea81-184">The preceding code:</span></span>

  * <span data-ttu-id="7ea81-185">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="7ea81-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="7ea81-186">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="7ea81-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="7ea81-187">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="7ea81-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="7ea81-188">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="7ea81-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea81-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea81-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7ea81-190">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="7ea81-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea81-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea81-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7ea81-192">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7ea81-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7ea81-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7ea81-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7ea81-194">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="7ea81-195">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="7ea81-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="7ea81-196">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="7ea81-197">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="7ea81-197">The name and message are displayed on both pages instantly.</span></span>

  ![Aplicativo de exemplo do SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="7ea81-199">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="7ea81-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="7ea81-200">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ea81-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="7ea81-201">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="7ea81-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="7ea81-202">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="7ea81-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="7ea81-203">![Erro de signalr.js não encontrado](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="7ea81-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="7ea81-204">Se você receber o erro ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY no Chrome, execute estes comandos para atualizar seu certificado de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="7ea81-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="7ea81-205">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="7ea81-205">Next steps</span></span>

<span data-ttu-id="7ea81-206">Para saber mais sobre o SignalR, confira a introdução:</span><span class="sxs-lookup"><span data-stu-id="7ea81-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7ea81-207">Introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ea81-208">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="7ea81-209">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="7ea81-209">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7ea81-210">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="7ea81-210">Create a web project.</span></span>
> * <span data-ttu-id="7ea81-211">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-211">Add the SignalR client library.</span></span>
> * <span data-ttu-id="7ea81-212">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-212">Create a SignalR hub.</span></span>
> * <span data-ttu-id="7ea81-213">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-213">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="7ea81-214">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="7ea81-214">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="7ea81-215">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="7ea81-215">At the end, you'll have a working chat app:</span></span>

![Aplicativo de exemplo do SignalR](signalr/_static/2.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="7ea81-217">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7ea81-217">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea81-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea81-218">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea81-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea81-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7ea81-220">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7ea81-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7ea81-221">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="7ea81-221">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea81-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea81-222">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="7ea81-223">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-223">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="7ea81-224">Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-224">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="7ea81-225">Dê ao projeto o nome de *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-225">Name the project *SignalRChat*.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)

* <span data-ttu-id="7ea81-227">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="7ea81-227">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="7ea81-228">Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-228">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea81-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea81-230">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="7ea81-231">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="7ea81-231">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="7ea81-232">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="7ea81-232">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7ea81-233">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7ea81-233">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7ea81-234">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-234">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="7ea81-235">Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="7ea81-235">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="7ea81-236">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-236">Select **Next**.</span></span>

* <span data-ttu-id="7ea81-237">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-237">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="7ea81-238">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-238">Add the SignalR client library</span></span>

<span data-ttu-id="7ea81-239">A biblioteca do servidor SignalR está incluída no metapacote `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="7ea81-239">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="7ea81-240">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="7ea81-240">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="7ea81-241">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-241">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="7ea81-242">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="7ea81-242">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea81-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea81-243">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="7ea81-244">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-244">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="7ea81-245">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-245">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="7ea81-246">Para **Biblioteca**, insira `@aspnet/signalr@1` e selecione a versão mais recente que não seja uma versão prévia.</span><span class="sxs-lookup"><span data-stu-id="7ea81-246">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/2.x/libman1.png)

* <span data-ttu-id="7ea81-248">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-248">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="7ea81-249">Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-249">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/2.x/libman2.png)

  <span data-ttu-id="7ea81-251">O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.</span><span class="sxs-lookup"><span data-stu-id="7ea81-251">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea81-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea81-252">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="7ea81-253">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="7ea81-253">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="7ea81-254">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="7ea81-254">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="7ea81-255">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="7ea81-255">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="7ea81-256">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="7ea81-256">The parameters specify the following options:</span></span>
  * <span data-ttu-id="7ea81-257">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="7ea81-257">Use the unpkg provider.</span></span>
  * <span data-ttu-id="7ea81-258">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-258">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="7ea81-259">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="7ea81-259">Copy only the specified files.</span></span>

  <span data-ttu-id="7ea81-260">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="7ea81-260">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7ea81-261">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7ea81-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7ea81-262">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="7ea81-262">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="7ea81-263">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-263">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="7ea81-264">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="7ea81-264">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="7ea81-265">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="7ea81-265">The parameters specify the following options:</span></span>
  * <span data-ttu-id="7ea81-266">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="7ea81-266">Use the unpkg provider.</span></span>
  * <span data-ttu-id="7ea81-267">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-267">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="7ea81-268">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="7ea81-268">Copy only the specified files.</span></span>

  <span data-ttu-id="7ea81-269">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="7ea81-269">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="7ea81-270">Criar um hub do SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-270">Create a SignalR hub</span></span>

<span data-ttu-id="7ea81-271">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="7ea81-271">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="7ea81-272">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-272">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="7ea81-273">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ea81-273">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]

  <span data-ttu-id="7ea81-274">A classe `ChatHub` é herda da classe `Hub` do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-274">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="7ea81-275">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="7ea81-275">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="7ea81-276">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="7ea81-276">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="7ea81-277">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="7ea81-277">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="7ea81-278">O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.</span><span class="sxs-lookup"><span data-stu-id="7ea81-278">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="7ea81-279">Configurar o SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-279">Configure SignalR</span></span>

<span data-ttu-id="7ea81-280">O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-280">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="7ea81-281">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7ea81-281">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="7ea81-282">Essas alterações adicionam o SignalR ao sistema de injeção de dependência e ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ea81-282">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="7ea81-283">Adicionar o código de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-283">Add SignalR client code</span></span>

* <span data-ttu-id="7ea81-284">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ea81-284">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]

  <span data-ttu-id="7ea81-285">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ea81-285">The preceding code:</span></span>

  * <span data-ttu-id="7ea81-286">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="7ea81-286">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="7ea81-287">Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-287">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="7ea81-288">Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="7ea81-288">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="7ea81-289">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ea81-289">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]

  <span data-ttu-id="7ea81-290">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ea81-290">The preceding code:</span></span>

  * <span data-ttu-id="7ea81-291">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="7ea81-291">Creates and starts a connection.</span></span>
  * <span data-ttu-id="7ea81-292">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="7ea81-292">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="7ea81-293">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="7ea81-293">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="7ea81-294">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="7ea81-294">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ea81-295">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ea81-295">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7ea81-296">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="7ea81-296">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7ea81-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ea81-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7ea81-298">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7ea81-298">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7ea81-299">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7ea81-299">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7ea81-300">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-300">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="7ea81-301">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="7ea81-301">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="7ea81-302">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="7ea81-302">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="7ea81-303">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="7ea81-303">The name and message are displayed on both pages instantly.</span></span>

  ![Aplicativo de exemplo do SignalR](signalr/_static/2.x/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="7ea81-305">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="7ea81-305">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="7ea81-306">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ea81-306">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="7ea81-307">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="7ea81-307">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="7ea81-308">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="7ea81-308">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="7ea81-309">![Erro de signalr.js não encontrado](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="7ea81-309">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ea81-310">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7ea81-310">Additional resources</span></span>

* [<span data-ttu-id="7ea81-311">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="7ea81-311">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)

## <a name="next-steps"></a><span data-ttu-id="7ea81-312">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="7ea81-312">Next steps</span></span>

<span data-ttu-id="7ea81-313">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="7ea81-313">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7ea81-314">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="7ea81-314">Create a web app project.</span></span>
> * <span data-ttu-id="7ea81-315">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-315">Add the SignalR client library.</span></span>
> * <span data-ttu-id="7ea81-316">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-316">Create a SignalR hub.</span></span>
> * <span data-ttu-id="7ea81-317">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="7ea81-317">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="7ea81-318">Adicionar o código que usa o hub para enviar mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="7ea81-318">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="7ea81-319">Para saber mais sobre o SignalR, confira a introdução:</span><span class="sxs-lookup"><span data-stu-id="7ea81-319">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7ea81-320">Introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7ea81-320">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end
