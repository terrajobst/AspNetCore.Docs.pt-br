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
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>Tutorial: Introdução ao SignalR para ASP.NET Core

Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR. Você aprenderá como:

> [!div class="checklist"]
> * Crie um projeto Web.
> * Adicionar uma biblioteca de clientes do SignalR.
> * Criar um hub do SignalR.
> * Configurar o projeto para usar o SignalR.
> * Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.

No final, você terá um aplicativo de chat funcionando:

![Aplicativo de exemplo do SignalR](signalr/_static/signalr-get-started-finished.png)

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([como baixar](xref:index#how-to-download-a-sample)).

[!INCLUDE [|Prerequisites](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a>Criar um projeto Web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

* No menu, selecione **Arquivo > Novo Projeto**.

* Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**. Dê ao projeto o nome de *SignalRChat*.

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/signalr-new-project-dialog.png)

* Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages.

* Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.

* Execute os seguintes comandos:

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No menu, selecione **Arquivo > Nova Solução**.

* Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)**).

* Selecione **Avançar**.

* Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.

---

## <a name="add-the-signalr-client-library"></a>Adicionar a biblioteca de clientes do SignalR

A biblioteca do servidor SignalR está incluída no metapacote `Microsoft.AspNetCore.App`. A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto. Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*. unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

* No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Biblioteca do Lado do Cliente**.

* Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.

* Para **Biblioteca**, insira `@aspnet/signalr@1` e selecione a versão mais recente que não seja uma versão prévia.

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/libman1.png)

* Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.

* Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/libman2.png)

  O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* No terminal integrado, execute o seguinte comando para instalar o LibMan.

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan. Talvez seja necessário aguardar alguns segundos antes de ver a saída.

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Os parâmetros especificam as seguintes opções:
  * Use o provedor unpkg.
  * Copie os arquivos para o destino *wwwroot/lib/signalr*.
  * Copie apenas os arquivos especificados.

  A saída tem a aparência do seguinte exemplo:

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No **Terminal**, execute o comando a seguir para instalar o LibMan.

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.

* Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Os parâmetros especificam as seguintes opções:
  * Use o provedor unpkg.
  * Copie os arquivos para o destino *wwwroot/lib/signalr*.
  * Copie apenas os arquivos especificados.

  A saída tem a aparência do seguinte exemplo:

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>Criar um hub do SignalR

Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.

* Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.

* Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  A classe `ChatHub` é herda da classe `Hub` do SignalR. A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.

  O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes. O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial. O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.

## <a name="configure-signalr"></a>Configurar o SignalR

O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.

* Adicione o seguinte código realçado ao arquivo *Startup.cs*.

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  Essas alterações adicionam o SignalR ao sistema de injeção de dependência e ao pipeline do middleware do ASP.NET Core.

## <a name="add-signalr-client-code"></a>Adicionar o código de cliente do SignalR

* Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  O código anterior:

  * Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.
  * Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.
  * Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.

* Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  O código anterior:

  * Cria e inicia uma conexão.
  * Adiciona no botão Enviar um manipulador que envia mensagens ao hub.
  * Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.

## <a name="run-the-app"></a>Executar o aplicativo

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Pressione **CTRL + F5** para executar o aplicativo sem depuração.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* No terminal integrado, execute o seguinte comando:

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No menu, selecione **Executar > Iniciar sem Depuração**.

---

* Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

* Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.

  O nome e a mensagem são exibidos em ambas as páginas instantaneamente.

  ![Aplicativo de exemplo do SignalR](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console. Você pode encontrar erros relacionados ao código HTML e JavaScript. Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada. Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.
> ![Erro de signalr.js não encontrado](signalr/_static/f12-console.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um projeto de aplicativo Web.
> * Adicionar uma biblioteca de clientes do SignalR.
> * Criar um hub do SignalR.
> * Configurar o projeto para usar o SignalR.
> * Adicionar o código que usa o hub para enviar mensagens de qualquer cliente para todos os clientes conectados.

Para saber mais sobre o SignalR, confira a introdução:

> [!div class="nextstepaction"]
> [Introdução ao ASP.NET Core SignalR](xref:signalr/introduction)
