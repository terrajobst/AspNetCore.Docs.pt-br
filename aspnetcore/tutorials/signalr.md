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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666743"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>Tutorial: introdução ao SignalR para ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR. Você aprenderá como:

> [!div class="checklist"]
> * Crie um projeto Web.
> * Adicionar uma biblioteca de clientes do SignalR.
> * Criar um hub do SignalR.
> * Configurar o projeto para usar o SignalR.
> * Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.

No final, você terá um aplicativo de chat funcionando:

![Aplicativo de exemplo do SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a>Criar um projeto do aplicativo Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* No menu, selecione **Arquivo > Novo Projeto**.

* Na caixa de diálogo **Criar novo projeto**, selecione **Aplicativo Web ASP.NET Core** e depois selecione **Avançar**.

* Na caixa de diálogo **Configurar seu novo projeto**, nomeie o projeto *SignalRChat* e, em seguida, selecione **Criar**.

* Na caixa de diálogo **criar um novo aplicativo web ASP.NET Core** , selecione **.net Core** e **ASP.NET Core 3,0**. 

* Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages e, em seguida, selecione **Criar**.

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.

* Execute os seguintes comandos:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No menu, selecione **Arquivo > Nova Solução**.

* Selecione **.NET Core > Aplicativo > Aplicativo Web** (não selecione **Aplicativo Web (Model-View-Controller)** ) e, em seguida, selecione **Avançar**.

* Verifique se a **Estrutura de Destino** está definida para **.NET Core 3.0** e, em seguida, selecione **Avançar**.

* Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.

---

## <a name="add-the-signalr-client-library"></a>Adicionar a biblioteca de clientes do SignalR

A biblioteca do servidor SignalR está incluída na estrutura compartilhada do ASP.NET Core 3.0. A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto. Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*. unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **biblioteca do lado do cliente**.

* Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**.

* Para **Biblioteca**, insira `@microsoft/signalr@latest`.

* Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*.

* Defina **local de destino** como *wwwroot/js/signalr/* e selecione **instalar**.

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan cria uma pasta *wwwroot/js/signalr* e copia os arquivos selecionados nela.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* No terminal integrado, execute o seguinte comando para instalar o LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan. Talvez seja necessário aguardar alguns segundos antes de ver a saída.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Os parâmetros especificam as seguintes opções:
  * Use o provedor unpkg.
  * Copie os arquivos para o destino *wwwroot/js/signalr* .
  * Copie apenas os arquivos especificados.

  A saída tem a aparência do seguinte exemplo:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No **Terminal**, execute o comando a seguir para instalar o LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*.

* Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Os parâmetros especificam as seguintes opções:
  * Use o provedor unpkg.
  * Copie os arquivos para o destino *wwwroot/js/signalr* .
  * Copie apenas os arquivos especificados.

  A saída tem a aparência do seguinte exemplo:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>Criar um hub do SignalR

Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.

* Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.

* Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  A classe `ChatHub` é herda da classe `Hub` do SignalR. A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.

  O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes. O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial. O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.

## <a name="configure-signalr"></a>Configurar o SignalR

O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.

* Adicione o seguinte código realçado ao arquivo *Startup.cs*.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Essas alterações adicionam o SignalR ao sistemas de injeção de dependência e roteamento do ASP.NET Core.

## <a name="add-signalr-client-code"></a>Adicionar o código de cliente do SignalR

* Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  O código anterior:

  * Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.
  * Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR.
  * Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.

* Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  O código anterior:

  * Cria e inicia uma conexão.
  * Adiciona no botão Enviar um manipulador que envia mensagens ao hub.
  * Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.

## <a name="run-the-app"></a>Executar o aplicativo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Pressione **CTRL + F5** para executar o aplicativo sem depuração.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* No terminal integrado, execute o seguinte comando:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No menu, selecione **Executar > Iniciar sem Depuração**.

---

* Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

* Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.

  O nome e a mensagem são exibidos em ambas as páginas instantaneamente.

  ![Aplicativo de exemplo do SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console. Você pode encontrar erros relacionados ao código HTML e JavaScript. Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada. Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.
>   ![Erro de signalr.js não encontrado](signalr/_static/3.x/f12-console.png)
> * Se você receber o erro ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY no Chrome, execute estes comandos para atualizar seu certificado de desenvolvimento:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando o SignalR. Você aprenderá como:   

> [!div class="checklist"]  
> * Crie um projeto Web.   
> * Adicionar uma biblioteca de clientes do SignalR. 
> * Criar um hub do SignalR.   
> * Configurar o projeto para usar o SignalR.   
> * Adicione o código que envia mensagens de qualquer cliente para todos os clientes conectados.  
No final, você terá um aplicativo de chat em funcionamento: ![aplicativo de exemplo Signalr](signalr/_static/2.x/signalr-get-started-finished.png) 

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Criar um projeto Web 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* No menu, selecione **Arquivo > Novo Projeto**. 

* Na caixa de diálogo **Novo Projeto**, selecione **Instalado > Visual C# > Web > Aplicativo Web ASP.NET Core**. Dê ao projeto o nome de *SignalRChat*. 

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Selecione **Aplicativo Web** para criar um projeto que usa Razor Pages. 

* Selecione uma estrutura de destino do **.NET Core**, selecione **ASP.NET Core 2.2** e clique em **OK**.    

  ![Caixa de diálogo Novo Projeto no Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) para a pasta na qual a nova pasta de projeto será criada.  

* Execute os seguintes comandos:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)   

* No menu, selecione **Arquivo > Nova Solução**.    

* Selecione **.NET Core > Aplicativo > Aplicativo Web ASP.NET Core** (não selecione **Aplicativo Web ASP.NET Core (MVC)** ).  

* Selecione **Avançar**.  

* Nomeie o projeto como *SignalRChat* e, em seguida, selecione **Criar**.   

--- 

## <a name="add-the-signalr-client-library"></a>Adicionar a biblioteca de clientes do SignalR   

A biblioteca do servidor SignalR está incluída no metapacote `Microsoft.AspNetCore.App`. A biblioteca de clientes do JavaScript não é incluída automaticamente no projeto. Neste tutorial, você usará o LibMan (Library Manager) para obter a biblioteca de clientes de *unpkg*. unpkg é uma CDN (rede de distribuição de conteúdo) que pode distribuir qualquer conteúdo do npm, o gerenciador de pacotes do Node.js.    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **biblioteca do lado do cliente**.  

* Na caixa de diálogo **Adicionar Biblioteca do Lado do Cliente**, para **Provedor**, selecione **unpkg**. 

* Para **Biblioteca**, insira `@microsoft/signalr@3` e selecione a versão mais recente que não seja uma versão prévia.  

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar biblioteca](signalr/_static/2.x/libman1.png)   

* Selecione **Escolher arquivos específicos**, expanda a pasta *distribuidor/navegador* e selecione *signalr.js* e *signalr.min.js*. 

* Defina **Localização de Destino** como *wwwroot/lib/signalr/* e selecione **Instalar**.    

  ![Caixa de diálogo Adicionar Biblioteca do Lado do Cliente – selecionar arquivos e destino](signalr/_static/2.x/libman2.png) 

  O LibMan cria uma pasta *wwwroot/lib/signalr* e copia os arquivos selecionados para ela.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* No terminal integrado, execute o seguinte comando para instalar o LibMan.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan. Talvez seja necessário aguardar alguns segundos antes de ver a saída.   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Os parâmetros especificam as seguintes opções: 
  * Use o provedor unpkg. 
  * Copie os arquivos para o destino *wwwroot/lib/signalr*.    
  * Copie apenas os arquivos especificados.  

  A saída tem a aparência do seguinte exemplo:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)   

* No **Terminal**, execute o comando a seguir para instalar o LibMan. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Navegue até a pasta do projeto, que inclui o arquivo *SignalRChat.csproj*. 

* Execute o comando a seguir para obter a biblioteca de clientes SignalR usando LibMan.  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Os parâmetros especificam as seguintes opções: 
  * Use o provedor unpkg. 
  * Copie os arquivos para o destino *wwwroot/lib/signalr*.    
  * Copie apenas os arquivos especificados.  

  A saída tem a aparência do seguinte exemplo:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a>Criar um hub do SignalR 

Um *hub* é uma classe que funciona como um pipeline de alto nível que lida com a comunicação entre cliente e servidor.   

* Na pasta do projeto SignalRChat, crie uma pasta *Hubs*.    

* Na pasta *Hubs*, crie um arquivo *ChatHub.cs* com o código a seguir: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  A classe `ChatHub` é herda da classe `Hub` do SignalR. A classe `Hub` gerencia conexões, grupos e sistemas de mensagens.    

  O método `SendMessage` pode ser chamado por um cliente conectado para enviar uma mensagem a todos os clientes. O código cliente do JavaScript que chama o método é mostrado posteriormente no tutorial. O código do SignalR é assíncrono para fornecer o máximo de escalabilidade.  

## <a name="configure-signalr"></a>Configurar o SignalR    

O servidor do SignalR precisa ser configurado para passar solicitações do SignalR ao SignalR.  

* Adicione o seguinte código realçado ao arquivo *Startup.cs*.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Essas alterações adicionam o SignalR ao sistema de injeção de dependência e ao pipeline do middleware do ASP.NET Core.    

## <a name="add-signalr-client-code"></a>Adicionar o código de cliente do SignalR  

* Substitua o conteúdo *Pages\Index.cshtml* pelo código a seguir:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  O código anterior:   

  * Cria as caixas de texto para o nome e a mensagem de texto e um botão Enviar.  
  * Cria uma lista com `id="messagesList"` para exibir as mensagens recebidas do hub do SignalR. 
  * Inclui referências de script ao SignalR e ao código do aplicativo *chat.js* que você criará na próxima etapa.  

* Na pasta *wwwroot/js*, crie um arquivo *chat.js* com o código a seguir:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  O código anterior:   

  * Cria e inicia uma conexão.    
  * Adiciona no botão Enviar um manipulador que envia mensagens ao hub. 
  * Adiciona no objeto de conexão um manipulador que recebe mensagens do hub e as adiciona à lista.  

## <a name="run-the-app"></a>Executar o aplicativo  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Pressione **CTRL + F5** para executar o aplicativo sem depuração.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* No terminal integrado, execute o seguinte comando:    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No menu, selecione **Executar > Iniciar sem Depuração**.

---

* Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

* Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar Mensagem**.  

  O nome e a mensagem são exibidos em ambas as páginas instantaneamente.   

  ![SignalR aplicativo de exemplo](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Se o aplicativo não funcionar, abra as ferramentas para desenvolvedores do navegador (F12) e acesse o console. Você pode encontrar erros relacionados ao código HTML e JavaScript. Por exemplo, suponha que você coloque *signalr.js* em uma pasta diferente daquela direcionada. Nesse caso, a referência a esse arquivo não funcionará e ocorrerá um erro 404 no console.   
> ![Erro de signalr.js não encontrado](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Recursos adicionais 
* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
