---
title: Introdução ao SignalR para ASP.NET Core
author: bradygaster
description: Neste tutorial, você criará um aplicativo de chat que usa o SignalR para ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/signalr
ms.openlocfilehash: 9a77460cfd8201ca357aad3415725d4b9a30b187
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64885041"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="0ad01-103">Tutorial: Introdução ao SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ad01-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="0ad01-104">Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="0ad01-105">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="0ad01-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0ad01-106">Crie um projeto Web.</span><span class="sxs-lookup"><span data-stu-id="0ad01-106">Create a web project.</span></span>
> * <span data-ttu-id="0ad01-107">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="0ad01-108">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="0ad01-109">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="0ad01-110">Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="0ad01-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="0ad01-111">No final, você terá um aplicativo de chat funcionando:</span><span class="sxs-lookup"><span data-stu-id="0ad01-111">At the end, you'll have a working chat app:</span></span>

![Aplicativo de exemplo do SignalR](signalr/_static/signalr-get-started-finished.png)

<span data-ttu-id="0ad01-113">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0ad01-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

[!INCLUDE [|Prerequisites](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="0ad01-114">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="0ad01-114">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0ad01-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ad01-115">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="0ad01-116">No menu, selecione **Arquivo > Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-116">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="0ad01-117">Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-117">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="0ad01-118">Dê ao projeto o nome de *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="0ad01-118">Name the project *SignalRChat*.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/signalr-new-project-dialog.png)

* <span data-ttu-id="0ad01-120">Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0ad01-120">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="0ad01-121">Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-121">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0ad01-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ad01-123">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="0ad01-124">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.</span><span class="sxs-lookup"><span data-stu-id="0ad01-124">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="0ad01-125">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0ad01-125">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0ad01-126">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0ad01-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0ad01-127">No menu, selecione **Arquivo > Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-127">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="0ad01-128">Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)**).</span><span class="sxs-lookup"><span data-stu-id="0ad01-128">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="0ad01-129">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-129">Select **Next**.</span></span>

* <span data-ttu-id="0ad01-130">Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-130">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="0ad01-131">Adicionar a biblioteca de clientes do SignalR</span><span class="sxs-lookup"><span data-stu-id="0ad01-131">Add the SignalR client library</span></span>

<span data-ttu-id="0ad01-132">A biblioteca do servidor SignalR está incluída no metapacote `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="0ad01-132">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="0ad01-133">A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto.</span><span class="sxs-lookup"><span data-stu-id="0ad01-133">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="0ad01-134">Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="0ad01-134">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="0ad01-135">unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.</span><span class="sxs-lookup"><span data-stu-id="0ad01-135">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0ad01-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ad01-136">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="0ad01-137">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-137">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="0ad01-138">Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-138">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="0ad01-139">Para **Biblioteca**, insira `@aspnet/signalr@1` e selecione a versão mais recente que não seja uma versão prévia.</span><span class="sxs-lookup"><span data-stu-id="0ad01-139">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/libman1.png)

* <span data-ttu-id="0ad01-141">Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="0ad01-141">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="0ad01-142">Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-142">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/libman2.png)

  <span data-ttu-id="0ad01-144">O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.</span><span class="sxs-lookup"><span data-stu-id="0ad01-144">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0ad01-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ad01-145">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="0ad01-146">No terminal integrado, execute o seguinte comando para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="0ad01-146">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="0ad01-147">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="0ad01-147">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="0ad01-148">Talvez seja necessário aguardar alguns segundos antes de ver a saída.</span><span class="sxs-lookup"><span data-stu-id="0ad01-148">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="0ad01-149">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="0ad01-149">The parameters specify the following options:</span></span>
  * <span data-ttu-id="0ad01-150">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="0ad01-150">Use the unpkg provider.</span></span>
  * <span data-ttu-id="0ad01-151">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="0ad01-151">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="0ad01-152">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="0ad01-152">Copy only the specified files.</span></span>

  <span data-ttu-id="0ad01-153">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="0ad01-153">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0ad01-154">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0ad01-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0ad01-155">No **Terminal**, execute o comando a seguir para instalar o LibMan.</span><span class="sxs-lookup"><span data-stu-id="0ad01-155">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="0ad01-156">Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0ad01-156">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="0ad01-157">Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.</span><span class="sxs-lookup"><span data-stu-id="0ad01-157">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="0ad01-158">Os parâmetros especificam as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="0ad01-158">The parameters specify the following options:</span></span>
  * <span data-ttu-id="0ad01-159">Use o provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="0ad01-159">Use the unpkg provider.</span></span>
  * <span data-ttu-id="0ad01-160">Copie os arquivos para o destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="0ad01-160">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="0ad01-161">Copie apenas os arquivos especificados.</span><span class="sxs-lookup"><span data-stu-id="0ad01-161">Copy only the specified files.</span></span>

  <span data-ttu-id="0ad01-162">A saída tem a aparência do seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="0ad01-162">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="0ad01-163">Criar um hub do SignalR</span><span class="sxs-lookup"><span data-stu-id="0ad01-163">Create a SignalR hub</span></span>

<span data-ttu-id="0ad01-164">Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="0ad01-164">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="0ad01-165">Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="0ad01-165">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="0ad01-166">Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0ad01-166">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  <span data-ttu-id="0ad01-167">A classe `ChatHub` é herda da classe `Hub` do SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-167">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="0ad01-168">A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.</span><span class="sxs-lookup"><span data-stu-id="0ad01-168">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="0ad01-169">O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="0ad01-169">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="0ad01-170">O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="0ad01-170">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="0ad01-171">O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.</span><span class="sxs-lookup"><span data-stu-id="0ad01-171">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="0ad01-172">Configurar o SignalR</span><span class="sxs-lookup"><span data-stu-id="0ad01-172">Configure SignalR</span></span>

<span data-ttu-id="0ad01-173">O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-173">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="0ad01-174">Adicione o seguinte código realçado ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="0ad01-174">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="0ad01-175">Essas alterações adicionam o SignalR ao sistema de injeção de dependência e ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ad01-175">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="0ad01-176">Adicionar o código de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="0ad01-176">Add SignalR client code</span></span>

* <span data-ttu-id="0ad01-177">Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0ad01-177">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  <span data-ttu-id="0ad01-178">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="0ad01-178">The preceding code:</span></span>

  * <span data-ttu-id="0ad01-179">Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="0ad01-179">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="0ad01-180">Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-180">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="0ad01-181">Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="0ad01-181">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="0ad01-182">Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0ad01-182">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  <span data-ttu-id="0ad01-183">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="0ad01-183">The preceding code:</span></span>

  * <span data-ttu-id="0ad01-184">Cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="0ad01-184">Creates and starts a connection.</span></span>
  * <span data-ttu-id="0ad01-185">Adiciona no botão Enviar um manipulador que envia mensagens ao hub.</span><span class="sxs-lookup"><span data-stu-id="0ad01-185">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="0ad01-186">Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.</span><span class="sxs-lookup"><span data-stu-id="0ad01-186">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="0ad01-187">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0ad01-187">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0ad01-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ad01-188">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0ad01-189">Pressione **CTRL + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="0ad01-189">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0ad01-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ad01-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0ad01-191">No terminal integrado, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0ad01-191">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0ad01-192">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0ad01-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0ad01-193">No menu, selecione **Executar > Iniciar sem Depuração**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-193">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="0ad01-194">Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="0ad01-194">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="0ad01-195">Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.</span><span class="sxs-lookup"><span data-stu-id="0ad01-195">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="0ad01-196">O nome e a mensagem são exibidos em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="0ad01-196">The name and message are displayed on both pages instantly.</span></span>

  ![Aplicativo de exemplo do SignalR](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="0ad01-198">Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console.</span><span class="sxs-lookup"><span data-stu-id="0ad01-198">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="0ad01-199">Você pode encontrar erros relacionados ao código HTML e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0ad01-199">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="0ad01-200">Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada.</span><span class="sxs-lookup"><span data-stu-id="0ad01-200">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="0ad01-201">Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.</span><span class="sxs-lookup"><span data-stu-id="0ad01-201">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="0ad01-202">![Erro de signalr.js não encontrado](signalr/_static/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="0ad01-202">![signalr.js not found error](signalr/_static/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="0ad01-203">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="0ad01-203">Next steps</span></span>

<span data-ttu-id="0ad01-204">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="0ad01-204">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0ad01-205">Criar um projeto de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="0ad01-205">Create a web app project.</span></span>
> * <span data-ttu-id="0ad01-206">Adicionar uma biblioteca de clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-206">Add the SignalR client library.</span></span>
> * <span data-ttu-id="0ad01-207">Criar um hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-207">Create a SignalR hub.</span></span>
> * <span data-ttu-id="0ad01-208">Configurar o projeto para usar o SignalR.</span><span class="sxs-lookup"><span data-stu-id="0ad01-208">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="0ad01-209">Adicionar o código que usa o hub para enviar mensagens de qualquer cliente para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="0ad01-209">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="0ad01-210">Para saber mais sobre o SignalR, confira a introdução:</span><span class="sxs-lookup"><span data-stu-id="0ad01-210">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0ad01-211">Introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="0ad01-211">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
