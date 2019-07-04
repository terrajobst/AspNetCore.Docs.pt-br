---
title: Introdução ao SignalR para ASP.NET Core
author: bradygaster
description: Neste tutorial, você criará um aplicativo de chat que usa o SignalR para ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/signalr
ms.openlocfilehash: 9a4296550a17ac2c348f2406e9f5b39877b02b59
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555934"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="c71af-103">Tutorial: Introdução ao SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c71af-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="c71af-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="c71af-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="c71af-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c71af-106">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="c71af-106">Create a web project.</span></span>
> * <span data-ttu-id="c71af-107">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="c71af-108">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="c71af-109">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="c71af-110">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="c71af-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="c71af-111">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="c71af-111">At the end, you'll have a working chat app:</span></span>

![Aplicativo de exemplo do SignalR](signalr/_static/signalr-get-started-finished.png)

<span data-ttu-id="c71af-113">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c71af-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c71af-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c71af-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c71af-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c71af-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c71af-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c71af-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c71af-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c71af-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c71af-118">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="c71af-118">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c71af-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c71af-119">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="c71af-120">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="c71af-120">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="c71af-121">Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c71af-121">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="c71af-122">Dê ao projeto o nome de *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="c71af-122">Name the project *SignalRChat*.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/signalr-new-project-dialog.png)

* <span data-ttu-id="c71af-124">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c71af-124">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="c71af-125">Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="c71af-125">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c71af-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c71af-127">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="c71af-128">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="c71af-128">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="c71af-129">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="c71af-129">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c71af-130">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c71af-130">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c71af-131">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="c71af-131">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="c71af-132">Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="c71af-132">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="c71af-133">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="c71af-133">Select **Next**.</span></span>

* <span data-ttu-id="c71af-134">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c71af-134">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="c71af-135">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="c71af-135">Add the SignalR client library</span></span>

<span data-ttu-id="c71af-136">A biblioteca do servidor SignalR está incluída no metapacote `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="c71af-136">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="c71af-137">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="c71af-137">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="c71af-138">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="c71af-138">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="c71af-139">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="c71af-139">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c71af-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c71af-140">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="c71af-141">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="c71af-141">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="c71af-142">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="c71af-142">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="c71af-143">Para **Biblioteca**, insira `@aspnet/signalr@1` e selecione a versão mais recente que não seja uma versão prévia.</span><span class="sxs-lookup"><span data-stu-id="c71af-143">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/libman1.png)

* <span data-ttu-id="c71af-145">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="c71af-145">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="c71af-146">Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="c71af-146">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/libman2.png)

  <span data-ttu-id="c71af-148">O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.</span><span class="sxs-lookup"><span data-stu-id="c71af-148">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c71af-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c71af-149">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="c71af-150">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="c71af-150">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="c71af-151">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="c71af-151">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="c71af-152">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="c71af-152">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="c71af-153">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="c71af-153">The parameters specify the following options:</span></span>
  * <span data-ttu-id="c71af-154">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="c71af-154">Use the unpkg provider.</span></span>
  * <span data-ttu-id="c71af-155">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="c71af-155">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="c71af-156">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="c71af-156">Copy only the specified files.</span></span>

  <span data-ttu-id="c71af-157">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="c71af-157">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c71af-158">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c71af-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c71af-159">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="c71af-159">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="c71af-160">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="c71af-160">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="c71af-161">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="c71af-161">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="c71af-162">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="c71af-162">The parameters specify the following options:</span></span>
  * <span data-ttu-id="c71af-163">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="c71af-163">Use the unpkg provider.</span></span>
  * <span data-ttu-id="c71af-164">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="c71af-164">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="c71af-165">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="c71af-165">Copy only the specified files.</span></span>

  <span data-ttu-id="c71af-166">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="c71af-166">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="c71af-167">Criar um hub do SignalR</span><span class="sxs-lookup"><span data-stu-id="c71af-167">Create a SignalR hub</span></span>

<span data-ttu-id="c71af-168">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="c71af-168">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="c71af-169">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="c71af-169">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="c71af-170">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c71af-170">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  <span data-ttu-id="c71af-171">A classe `ChatHub` é herda da classe `Hub` do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-171">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="c71af-172">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="c71af-172">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="c71af-173">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="c71af-173">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="c71af-174">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="c71af-174">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="c71af-175">O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.</span><span class="sxs-lookup"><span data-stu-id="c71af-175">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="c71af-176">Configurar o SignalR</span><span class="sxs-lookup"><span data-stu-id="c71af-176">Configure SignalR</span></span>

<span data-ttu-id="c71af-177">O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-177">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="c71af-178">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c71af-178">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="c71af-179">Essas alterações adicionam o SignalR ao sistema de injeção de dependência e ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c71af-179">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="c71af-180">Adicionar o código de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="c71af-180">Add SignalR client code</span></span>

* <span data-ttu-id="c71af-181">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c71af-181">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  <span data-ttu-id="c71af-182">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="c71af-182">The preceding code:</span></span>

  * <span data-ttu-id="c71af-183">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="c71af-183">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="c71af-184">Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-184">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="c71af-185">Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="c71af-185">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="c71af-186">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="c71af-186">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  <span data-ttu-id="c71af-187">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="c71af-187">The preceding code:</span></span>

  * <span data-ttu-id="c71af-188">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="c71af-188">Creates and starts a connection.</span></span>
  * <span data-ttu-id="c71af-189">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="c71af-189">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="c71af-190">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="c71af-190">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="c71af-191">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c71af-191">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c71af-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c71af-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c71af-193">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="c71af-193">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c71af-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c71af-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c71af-195">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c71af-195">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c71af-196">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c71af-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c71af-197">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="c71af-197">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="c71af-198">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="c71af-198">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="c71af-199">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="c71af-199">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="c71af-200">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="c71af-200">The name and message are displayed on both pages instantly.</span></span>

  ![Aplicativo de exemplo do SignalR](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="c71af-202">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="c71af-202">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="c71af-203">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c71af-203">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="c71af-204">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="c71af-204">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="c71af-205">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="c71af-205">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="c71af-206">![Erro de signalr.js não encontrado](signalr/_static/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="c71af-206">![signalr.js not found error](signalr/_static/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="c71af-207">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c71af-207">Next steps</span></span>

<span data-ttu-id="c71af-208">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="c71af-208">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c71af-209">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="c71af-209">Create a web app project.</span></span>
> * <span data-ttu-id="c71af-210">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-210">Add the SignalR client library.</span></span>
> * <span data-ttu-id="c71af-211">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-211">Create a SignalR hub.</span></span>
> * <span data-ttu-id="c71af-212">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="c71af-212">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="c71af-213">Adicionar o código que usa o hub para enviar mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="c71af-213">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="c71af-214">Para saber mais sobre o SignalR, confira a introdução:</span><span class="sxs-lookup"><span data-stu-id="c71af-214">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c71af-215">Introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c71af-215">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
