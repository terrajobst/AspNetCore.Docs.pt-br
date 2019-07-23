---
title: Introdução ao SignalR para ASP.NET Core
author: bradygaster
description: Neste tutorial, você criará um aplicativo de chat que usa o SignalR para ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 07/08/2019
uid: tutorials/signalr
ms.openlocfilehash: 53d3763a93cc72b6bcf85b64a706500299b3597f
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67893724"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="c5123-103">Tutorial: Introdução ao SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5123-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5123-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="c5123-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="c5123-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c5123-106">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="c5123-106">Create a web project.</span></span>
> * <span data-ttu-id="c5123-107">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="c5123-108">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="c5123-109">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="c5123-110">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="c5123-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="c5123-111">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="c5123-111">At the end, you'll have a working chat app:</span></span>

![Aplicativo de exemplo do SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="c5123-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c5123-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c5123-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5123-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c5123-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5123-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c5123-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c5123-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="c5123-117">Criar um projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="c5123-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c5123-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5123-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="c5123-119">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="c5123-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="c5123-120">Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core** e depois selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="c5123-121">Na caixa de diálogo **Configurar seu novo projeto**, nomeie o projeto *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="c5123-122">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, selecione **.NET Core** e **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="c5123-122">In the **Create a new ASP.NET Core Web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="c5123-123">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c5123-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5123-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="c5123-126">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="c5123-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="c5123-127">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="c5123-127">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c5123-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c5123-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c5123-129">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="c5123-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="c5123-130">Selecione **.NET Core > Aplicativo > Aplicativo Web** (não selecione **Aplicativo Web (Model-View-Controller)** ) e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="c5123-131">Verifique se a **Estrutura de Destino** está definida para **.NET Core 3.0** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="c5123-132">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="c5123-133">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-133">Add the SignalR client library</span></span>

<span data-ttu-id="c5123-134">A biblioteca do servidor SignalR está incluída na estrutura compartilhada do ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="c5123-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="c5123-135">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="c5123-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="c5123-136">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="c5123-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="c5123-137">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="c5123-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c5123-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5123-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="c5123-139">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="c5123-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="c5123-140">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="c5123-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="c5123-141">Para **Biblioteca**, insira `@aspnet/signalr@next`.</span><span class="sxs-lookup"><span data-stu-id="c5123-141">For **Library**, enter `@aspnet/signalr@next`.</span></span>
<!-- when 3.0 is released, change @next to @latest -->

* <span data-ttu-id="c5123-142">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="c5123-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="c5123-143">Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-143">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/3.x/libman1.png)

  <span data-ttu-id="c5123-145">O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.</span><span class="sxs-lookup"><span data-stu-id="c5123-145">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c5123-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5123-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="c5123-147">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="c5123-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="c5123-148">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="c5123-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="c5123-149">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="c5123-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="c5123-150">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="c5123-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="c5123-151">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="c5123-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="c5123-152">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="c5123-152">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="c5123-153">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="c5123-153">Copy only the specified files.</span></span>

  <span data-ttu-id="c5123-154">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="c5123-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c5123-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c5123-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c5123-156">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="c5123-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="c5123-157">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="c5123-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="c5123-158">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="c5123-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="c5123-159">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="c5123-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="c5123-160">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="c5123-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="c5123-161">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="c5123-161">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="c5123-162">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="c5123-162">Copy only the specified files.</span></span>

  <span data-ttu-id="c5123-163">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="c5123-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="c5123-164">Criar um hub do SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-164">Create a SignalR hub</span></span>

<span data-ttu-id="c5123-165">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="c5123-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="c5123-166">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="c5123-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="c5123-167">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c5123-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="c5123-168">A classe `ChatHub` é herda da classe `Hub` do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="c5123-169">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="c5123-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="c5123-170">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="c5123-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="c5123-171">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="c5123-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="c5123-172">O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.</span><span class="sxs-lookup"><span data-stu-id="c5123-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="c5123-173">Configurar o SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-173">Configure SignalR</span></span>

<span data-ttu-id="c5123-174">O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="c5123-175">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c5123-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=6,30,58)]

  <span data-ttu-id="c5123-176">Essas alterações adicionam o SignalR ao sistemas de injeção de dependência e roteamento do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5123-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="c5123-177">Adicionar o código de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-177">Add SignalR client code</span></span>

* <span data-ttu-id="c5123-178">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c5123-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="c5123-179">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="c5123-179">The preceding code:</span></span>

  * <span data-ttu-id="c5123-180">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="c5123-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="c5123-181">Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="c5123-182">Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="c5123-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="c5123-183">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c5123-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="c5123-184">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="c5123-184">The preceding code:</span></span>

  * <span data-ttu-id="c5123-185">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="c5123-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="c5123-186">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="c5123-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="c5123-187">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="c5123-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="c5123-188">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c5123-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c5123-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5123-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5123-190">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="c5123-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c5123-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5123-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c5123-192">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c5123-192">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c5123-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c5123-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c5123-194">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="c5123-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="c5123-195">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c5123-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="c5123-196">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="c5123-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="c5123-197">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="c5123-197">The name and message are displayed on both pages instantly.</span></span>

  ![Aplicativo de exemplo do SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="c5123-199">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="c5123-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="c5123-200">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c5123-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="c5123-201">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="c5123-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="c5123-202">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="c5123-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="c5123-203">![Erro de signalr.js não encontrado](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="c5123-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="c5123-204">Se você receber o erro ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY no Chrome ou NS_ERROR_NET_INADEQUATE_SECURITY no Firefox, execute estes comandos para atualizar o certificado de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="c5123-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome or NS_ERROR_NET_INADEQUATE_SECURITY in Firefox, run these commands to update your development certificate:</span></span>
>   ```
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="c5123-205">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c5123-205">Next steps</span></span>

<span data-ttu-id="c5123-206">Para saber mais sobre o SignalR, confira a introdução:</span><span class="sxs-lookup"><span data-stu-id="c5123-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c5123-207">Introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c5123-208">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="c5123-209">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="c5123-209">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c5123-210">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="c5123-210">Create a web project.</span></span>
> * <span data-ttu-id="c5123-211">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-211">Add the SignalR client library.</span></span>
> * <span data-ttu-id="c5123-212">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-212">Create a SignalR hub.</span></span>
> * <span data-ttu-id="c5123-213">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-213">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="c5123-214">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="c5123-214">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="c5123-215">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="c5123-215">At the end, you'll have a working chat app:</span></span>

![Aplicativo de exemplo do SignalR](signalr/_static/2.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="c5123-217">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c5123-217">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c5123-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5123-218">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c5123-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5123-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c5123-220">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c5123-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c5123-221">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="c5123-221">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c5123-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5123-222">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="c5123-223">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="c5123-223">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="c5123-224">Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c5123-224">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="c5123-225">Dê ao projeto o nome de *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="c5123-225">Name the project *SignalRChat*.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)

* <span data-ttu-id="c5123-227">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c5123-227">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="c5123-228">Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="c5123-228">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c5123-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5123-230">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="c5123-231">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="c5123-231">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="c5123-232">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="c5123-232">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c5123-233">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c5123-233">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c5123-234">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="c5123-234">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="c5123-235">Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="c5123-235">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="c5123-236">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-236">Select **Next**.</span></span>

* <span data-ttu-id="c5123-237">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-237">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="c5123-238">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-238">Add the SignalR client library</span></span>

<span data-ttu-id="c5123-239">A biblioteca do servidor SignalR está incluída no metapacote `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="c5123-239">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="c5123-240">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="c5123-240">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="c5123-241">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="c5123-241">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="c5123-242">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="c5123-242">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c5123-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5123-243">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="c5123-244">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="c5123-244">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="c5123-245">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="c5123-245">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="c5123-246">Para **Biblioteca**, insira `@aspnet/signalr@1` e selecione a versão mais recente que não seja uma versão prévia.</span><span class="sxs-lookup"><span data-stu-id="c5123-246">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/2.x/libman1.png)

* <span data-ttu-id="c5123-248">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="c5123-248">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="c5123-249">Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="c5123-249">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/2.x/libman2.png)

  <span data-ttu-id="c5123-251">O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.</span><span class="sxs-lookup"><span data-stu-id="c5123-251">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c5123-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5123-252">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="c5123-253">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="c5123-253">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="c5123-254">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="c5123-254">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="c5123-255">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="c5123-255">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="c5123-256">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="c5123-256">The parameters specify the following options:</span></span>
  * <span data-ttu-id="c5123-257">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="c5123-257">Use the unpkg provider.</span></span>
  * <span data-ttu-id="c5123-258">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="c5123-258">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="c5123-259">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="c5123-259">Copy only the specified files.</span></span>

  <span data-ttu-id="c5123-260">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="c5123-260">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c5123-261">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c5123-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c5123-262">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="c5123-262">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="c5123-263">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="c5123-263">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="c5123-264">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="c5123-264">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="c5123-265">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="c5123-265">The parameters specify the following options:</span></span>
  * <span data-ttu-id="c5123-266">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="c5123-266">Use the unpkg provider.</span></span>
  * <span data-ttu-id="c5123-267">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="c5123-267">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="c5123-268">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="c5123-268">Copy only the specified files.</span></span>

  <span data-ttu-id="c5123-269">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="c5123-269">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="c5123-270">Criar um hub do SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-270">Create a SignalR hub</span></span>

<span data-ttu-id="c5123-271">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="c5123-271">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="c5123-272">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="c5123-272">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="c5123-273">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c5123-273">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]

  <span data-ttu-id="c5123-274">A classe `ChatHub` é herda da classe `Hub` do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-274">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="c5123-275">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="c5123-275">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="c5123-276">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="c5123-276">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="c5123-277">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="c5123-277">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="c5123-278">O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.</span><span class="sxs-lookup"><span data-stu-id="c5123-278">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="c5123-279">Configurar o SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-279">Configure SignalR</span></span>

<span data-ttu-id="c5123-280">O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-280">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="c5123-281">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c5123-281">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="c5123-282">Essas alterações adicionam o SignalR ao sistema de injeção de dependência e ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5123-282">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="c5123-283">Adicionar o código de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-283">Add SignalR client code</span></span>

* <span data-ttu-id="c5123-284">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c5123-284">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]

  <span data-ttu-id="c5123-285">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="c5123-285">The preceding code:</span></span>

  * <span data-ttu-id="c5123-286">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="c5123-286">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="c5123-287">Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-287">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="c5123-288">Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="c5123-288">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="c5123-289">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c5123-289">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]

  <span data-ttu-id="c5123-290">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="c5123-290">The preceding code:</span></span>

  * <span data-ttu-id="c5123-291">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="c5123-291">Creates and starts a connection.</span></span>
  * <span data-ttu-id="c5123-292">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="c5123-292">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="c5123-293">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="c5123-293">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="c5123-294">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c5123-294">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c5123-295">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5123-295">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c5123-296">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="c5123-296">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c5123-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c5123-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c5123-298">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c5123-298">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c5123-299">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c5123-299">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c5123-300">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="c5123-300">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="c5123-301">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c5123-301">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="c5123-302">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="c5123-302">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="c5123-303">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="c5123-303">The name and message are displayed on both pages instantly.</span></span>

  ![Aplicativo de exemplo do SignalR](signalr/_static/2.x/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="c5123-305">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="c5123-305">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="c5123-306">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c5123-306">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="c5123-307">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="c5123-307">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="c5123-308">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="c5123-308">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="c5123-309">![Erro de signalr.js não encontrado](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="c5123-309">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="c5123-310">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c5123-310">Next steps</span></span>

<span data-ttu-id="c5123-311">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="c5123-311">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c5123-312">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="c5123-312">Create a web app project.</span></span>
> * <span data-ttu-id="c5123-313">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-313">Add the SignalR client library.</span></span>
> * <span data-ttu-id="c5123-314">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-314">Create a SignalR hub.</span></span>
> * <span data-ttu-id="c5123-315">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="c5123-315">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="c5123-316">Adicionar o código que usa o hub para enviar mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="c5123-316">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="c5123-317">Para saber mais sobre o SignalR, confira a introdução:</span><span class="sxs-lookup"><span data-stu-id="c5123-317">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c5123-318">Introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c5123-318">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end
