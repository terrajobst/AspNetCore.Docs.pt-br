---
title: Usar ASP.NET Core SignalR com TypeScript e webpack
author: ssougnez
description: Neste tutorial, você configura o webpack para agrupar e criar um ASP.NET Core SignalR aplicativo Web cujo cliente é escrito em TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: f8bbd9ed2e9c792197eb29be459f7e5ee499bfd1
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172007"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>Usar o SignalR do ASP.NET Core com TypeScript e Webpack

Por [Sébastien Sougnez](https://twitter.com/ssougnez) e [Scott Addie](https://twitter.com/Scott_Addie)

O [Webpack](https://webpack.js.org/) habilita os desenvolvedores a agrupar e criar recursos de um aplicativo Web do lado do cliente. Este tutorial demonstra como usar o Webpack em um aplicativo Web SignalR do ASP.NET Core cujo cliente é escrito em [TypeScript](https://www.typescriptlang.org/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Gerar um aplicativo inicial SignalR do ASP.NET Core por scaffold
> * Configurar o cliente TypeScript do SignalR
> * Configurar um pipeline de build usando o Webpack
> * Configurar o servidor SignalR
> * Habilitar a comunicação entre o cliente e o servidor

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([como baixar](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Prerequisites

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a carga de trabalho de **desenvolvimento Web e do ASP.NET**
* [SDK do .NET Core 3.0 ou posterior](https://www.microsoft.com/net/download/all)
* [Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [SDK do .NET Core 3.0 ou posterior](https://www.microsoft.com/net/download/all)
* [C# para Visual Studio Code versão 1.17.1 ou posterior](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Criar o aplicativo Web do ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Configure o Visual Studio para pesquisar o npm na variável de ambiente *PATH*. Por padrão, o Visual Studio usa a versão do npm encontrada no diretório de instalação. Siga estas instruções no Visual Studio:

1. Inicie o Visual Studio. Na janela iniciar, selecione **continuar sem código**.
1. Navegue até **ferramentas** > **Opções** > **projetos e soluções** > **Web gerenciamento de pacotes** > **ferramentas da Web externas**.
1. Selecione a entrada *$(PATH)* na lista. Clique na seta para cima para mover a entrada para a segunda posição na lista e selecione **OK**.

    ![Configuração do Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

A configuração do Visual Studio foi concluída.

1. Use a opção do menu **Arquivo** > **Novo** > **Projeto** e escolha o modelo **Aplicativo Web do ASP.NET Core**. Selecione **Avançar**.
1. Nomeie o projeto *SignalRWebPack*e selecione **criar**.
1. Selecione *.NET Core* na lista suspensa estrutura de destino e selecione *ASP.NET Core 3,1* na lista suspensa seletor de estrutura. Selecione o modelo **vazio** e selecione **criar**.

Adicione o pacote de `Microsoft.TypeScript.MSBuild` ao projeto:

1. No **Gerenciador de soluções** (painel direito), clique com o botão direito do mouse no nó do projeto e selecione **gerenciar pacotes NuGet**. Na guia **procurar** , procure `Microsoft.TypeScript.MSBuild`e, em seguida, clique em **instalar** à direita para instalar o pacote.

O Visual Studio adiciona o pacote NuGet sob o nó **dependências** no **Gerenciador de soluções**, habilitando a compilação TypeScript no projeto.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o comando a seguir no **Terminal Integrado**:

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* O comando `dotnet new` cria um aplicativo Web vazio ASP.NET Core em um diretório *SignalRWebPack* .
* O comando `code` abre a pasta *SignalRWebPack* na instância atual do Visual Studio Code.

Execute o seguinte comando CLI do .NET Core no **terminal integrado**:

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

O comando anterior adiciona o pacote (Microsoft. TypeScript. MSBuild) [https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/], habilitando a compilação do TypeScript no projeto.

---

## <a name="configure-webpack-and-typescript"></a>Configurar Webpack e TypeScript

As etapas a seguir configuram a conversão do TypeScript para JavaScript e o agrupamento de recursos no lado do cliente.

1. Execute o seguinte comando na raiz do projeto para criar um arquivo *Package. JSON* :

    ```console
    npm init -y
    ```

1. Adicione a propriedade realçada ao arquivo *Package. JSON* e salve as alterações do arquivo:

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    Definir a propriedade `private` como `true` evita avisos de instalação de pacote na próxima etapa.

1. Instalar os pacotes de npm exigidos. Execute o seguinte comando na raiz do projeto:

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Alguns detalhes de comando a se observar:

    * Um número de versão segue o sinal `@` para cada nome de pacote. O npm instala essas versões específicas do pacote.
    * A opção `-E` desabilita o comportamento padrão do npm de escrever os operadores de intervalo de [versão semântica](https://semver.org/) para *package.json*. Por exemplo, `"webpack": "4.41.5"` é usado em vez de `"webpack": "^4.41.5"`. Essa opção evita atualizações não intencionais para versões de pacote mais recentes.

    Consulte o [NPM-install](https://docs.npmjs.com/cli/install) docs para obter mais detalhes.

1. Substitua a propriedade `scripts` do arquivo *Package. JSON* pelo seguinte código:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Uma explicação sobre os scripts:

    * `build`: agrupa os recursos do lado do cliente no modo de desenvolvimento e observa alterações de arquivo. O observador de arquivos faz com que o lote se regenere toda vez que um arquivo de projeto é alterado. A opção `mode` desabilita otimizações de produção, como tree shaking e minificação. Use somente `build` no desenvolvimento.
    * `release`: agrupa os recursos do lado do cliente no modo de produção.
    * `publish`: executa o script `release` para agrupar recursos do lado do cliente no modo de produção. Chama o comando [publicar](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core para publicar o aplicativo.

1. Crie um arquivo chamado *webpack. config. js*, na raiz do projeto, com o seguinte código:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    O arquivo precedente configura a compilação Webpack. Detalhes de configuração a serem notados:

    * A propriedade `output` substitui o valor padrão do *dist*. Em vez disso, o lote é emitido no diretório *wwwroot*.
    * A matriz `resolve.extensions` inclui *.js* para importar o JavaScript do cliente SignalR.

1. Crie um novo diretório *src* na raiz do projeto para armazenar os ativos do lado do cliente do projeto.

1. Crie *src/index.html* com a marcação a seguir.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    A HTML precedente define a marcação clichê da página inicial.

1. Crie um novo diretório *src/css*. Seu propósito é armazenar os arquivos do projeto *.css*.

1. Crie *src/CSS/Main. css* com o seguinte CSS:

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    O arquivo precedente *main.css* define o estilo do aplicativo.

1. Crie *src/tsconfig. JSON* com o seguinte JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    O código precedente configura o compilador TypeScript para produzir um JavaScript compatível com [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Crie *src/index. TS* com o seguinte código:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    O TypeScript precedente recupera referências a elementos DOM e anexa dois manipuladores de eventos:

    * `keyup`: esse evento é acionado quando o usuário digita na caixa de texto `tbMessage`. A função `send` é chamada quando o usuário pressionar a tecla **Enter**.
    * `click`: esse evento é acionado quando o usuário clica no botão **Enviar**. A função `send` é chamada.

## <a name="configure-the-app"></a>Configurar o aplicativo

1. Em `Startup.Configure`, adicione chamadas para [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   O código anterior permite que o servidor Localize e forneça o arquivo *index. html* .  O arquivo é servido se o usuário insere sua URL completa ou a URL raiz do aplicativo Web.

1. No final de `Startup.Configure`, mapeie uma rota */Hub* para o hub de `ChatHub`. Substitua o código que exibe *Olá, mundo!* pela seguinte linha: 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. Em `Startup.ConfigureServices`, chame [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Crie um novo diretório chamado *hubs* na raiz do projeto *SignalRWebPack/* para armazenar o Hub do signalr.

1. Crie o hub *Hubs/ChatHub.cs* com o código a seguir:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Adicione a seguinte instrução de `using` na parte superior do arquivo *Startup.cs* para resolver a referência de `ChatHub`:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Habilitar a comunicação entre o cliente e o servidor

O aplicativo atualmente exibe um formulário básico para enviar mensagens, mas ainda não está funcional. O servidor está escutando uma rota específica, mas não faz nada com as mensagens enviadas.

1. Execute o seguinte comando na raiz do projeto:

    ```console
    npm i @microsoft/signalr @types/node
    ```

    O comando anterior é instalado:

     * O [cliente do TypeScript do signalr](https://www.npmjs.com/package/@microsoft/signalr), que permite ao cliente enviar mensagens para o servidor.
     * As definições de tipo do TypeScript para node. js, que habilita a verificação de tempo de compilação de tipos node. js.

1. Adicione o código destacado ao arquivo *src/index.ts*:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    O código precedente é compatível com o recebimento de mensagens do servidor. A classe `HubConnectionBuilder` cria um novo construtor para configurar a conexão do servidor. A função `withUrl` configura a URL do hub.

    O SignalR habilita a troca de mensagens entre um cliente e um servidor. Cada mensagem tem um nome específico. Por exemplo, mensagens com o nome `messageReceived` podem executar a lógica responsável por exibir a nova mensagem na zona de mensagens. É possível escutar uma mensagem específica por meio da função `on`. Qualquer número de nomes de mensagem pode ser escutado. Também é possível passar parâmetros para a mensagem, como o nome do autor e o conteúdo da mensagem recebida. Quando o cliente recebe a mensagem, um novo elemento `div` é criado com o nome do autor e o conteúdo da mensagem em seu atributo `innerHTML`. Ele é adicionado ao elemento principal `div` que exibe as mensagens.

1. Agora que o cliente pode receber mensagens, configure-o para enviá-las. Adicione o código destacado ao arquivo *src/index.ts*:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    Enviar uma mensagem por meio da conexão WebSockets exige uma chamada para o método `send`. O primeiro parâmetro do método é o nome da mensagem. Os dados da mensagem residem nos outros parâmetros. Neste exemplo, uma mensagem identificada como `newMessage` é enviada ao servidor. A mensagem é composta do nome de usuário e da entrada em uma caixa de texto. Se o envio for bem-sucedido, o valor da caixa de texto será limpo.

1. Adicione o método `NewMessage` à classe `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    O código precedente transmite as mensagens recebidas para todos os usuários conectados quando o servidor as recebe. Não é necessário ter um método genérico `on` para receber todas as mensagens. Um método nomeado com o nome da mensagem é suficiente.

    Neste exemplo, o cliente TypeScript envia uma mensagem identificada como `newMessage`. O método `NewMessage` de C# espera os dados enviados pelo cliente. É feita uma chamada para [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) em [clients. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). As mensagens recebidas são enviadas a todos os clientes conectados ao hub.

## <a name="test-the-app"></a>Testar o aplicativo

Confirme que o aplicativo funciona com as seguintes etapas.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Execute o Webpack no modo de *versão*. Usando a janela do **console do Gerenciador de pacotes** , execute o seguinte comando na raiz do projeto. Se você não estiver na raiz do projeto, insira `cd SignalRWebPack` antes de inserir o comando.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Selecione **Debug** > **Iniciar sem depuração** para iniciar o aplicativo em um navegador sem anexar o depurador. O arquivo *wwwroot/index.html* é fornecido em `http://localhost:<port_number>`.

   Se você receber erros de compilação, tente fechar e reabrir a solução. 

1. Abra outra instância do navegador (qualquer navegador). Cole a URL na barra de endereços.

1. Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**. O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Execute o Webpack no modo de *versão* executando o comando a seguir na raiz do projeto:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Crie e execute o aplicativo executando o seguinte comando na raiz do projeto:

    ```dotnetcli
    dotnet run
    ```

    O servidor Web inicia o aplicativo e o disponibiliza no localhost.

1. Abra um navegador em `http://localhost:<port_number>`. O arquivo *wwwroot/index.html* é fornecido. Copie a URL da barra de endereços.

1. Abra outra instância do navegador (qualquer navegador). Cole a URL na barra de endereços.

1. Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**. O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.

---

![mensagem exibida em ambas as janelas do navegador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Prerequisites

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a carga de trabalho de **desenvolvimento Web e do ASP.NET**
* [SDK 2.2 ou posterior do .NET Core](https://www.microsoft.com/net/download/all)
* [Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [SDK 2.2 ou posterior do .NET Core](https://www.microsoft.com/net/download/all)
* [C# para Visual Studio Code versão 1.17.1 ou posterior](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Criar o aplicativo Web do ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Configure o Visual Studio para pesquisar o npm na variável de ambiente *PATH*. Por padrão, o Visual Studio usa a versão do npm encontrada no diretório de instalação. Siga estas instruções no Visual Studio:

1. Navegue até **ferramentas** > **Opções** > **projetos e soluções** > **Web gerenciamento de pacotes** > **ferramentas da Web externas**.
1. Selecione a entrada *$(PATH)* na lista. Clique na seta para cima para mover a entrada para a segunda posição da lista.

    ![Configuração do Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

A configuração do Visual Studio foi concluída. É hora de criar o projeto.

1. Use a opção de menu **arquivo** > **nova** > **projeto** e escolha o modelo de **aplicativo Web ASP.NET Core** .
1. Nomeie o projeto *SignalRWebPack*e selecione **criar**.
1. Selecione *.NET Core* no menu suspenso da estrutura de destino e selecione *ASP.NET Core 2.2* no menu suspenso do seletor de estrutura. Selecione o modelo **vazio** e selecione **criar**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o comando a seguir no **Terminal Integrado**:

```dotnetcli
dotnet new web -o SignalRWebPack
```

Um aplicativo Web ASP.NET Core vazio, direcionado ao .NET Core, será criado em um diretório *SignalRWebPack*.

---

## <a name="configure-webpack-and-typescript"></a>Configurar Webpack e TypeScript

As etapas a seguir configuram a conversão do TypeScript para JavaScript e o agrupamento de recursos no lado do cliente.

1. Execute o seguinte comando na raiz do projeto para criar um arquivo *Package. JSON* :

    ```console
    npm init -y
    ```

1. Adicione a propriedade destacada ao arquivo *package.json*:

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    Definir a propriedade `private` como `true` evita avisos de instalação de pacote na próxima etapa.

1. Instalar os pacotes de npm exigidos. Execute o seguinte comando na raiz do projeto:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Alguns detalhes de comando a se observar:

    * Um número de versão segue o sinal `@` para cada nome de pacote. O npm instala essas versões específicas do pacote.
    * A opção `-E` desabilita o comportamento padrão do npm de escrever os operadores de intervalo de [versão semântica](https://semver.org/) para *package.json*. Por exemplo, `"webpack": "4.29.3"` é usado em vez de `"webpack": "^4.29.3"`. Essa opção evita atualizações não intencionais para versões de pacote mais recentes.

    Consulte o [NPM-install](https://docs.npmjs.com/cli/install) docs para obter mais detalhes.

1. Substitua a propriedade `scripts` do arquivo *Package. JSON* pelo seguinte código:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Uma explicação sobre os scripts:

    * `build`: agrupa os recursos do lado do cliente no modo de desenvolvimento e observa alterações de arquivo. O observador de arquivos faz com que o lote se regenere toda vez que um arquivo de projeto é alterado. A opção `mode` desabilita otimizações de produção, como tree shaking e minificação. Use somente `build` no desenvolvimento.
    * `release`: agrupa os recursos do lado do cliente no modo de produção.
    * `publish`: executa o script `release` para agrupar recursos do lado do cliente no modo de produção. Chama o comando [publicar](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core para publicar o aplicativo.

1. Crie um arquivo chamado *webpack. config. js* na raiz do projeto, com o seguinte código:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    O arquivo precedente configura a compilação Webpack. Detalhes de configuração a serem notados:

    * A propriedade `output` substitui o valor padrão do *dist*. Em vez disso, o lote é emitido no diretório *wwwroot*.
    * A matriz `resolve.extensions` inclui *.js* para importar o JavaScript do cliente SignalR.

1. Crie um novo diretório *src* na raiz do projeto para armazenar os ativos do lado do cliente do projeto.

1. Crie *src/index.html* com a marcação a seguir.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    A HTML precedente define a marcação clichê da página inicial.

1. Crie um novo diretório *src/css*. Seu propósito é armazenar os arquivos do projeto *.css*.

1. Crie *src/CSS/Main. css* com a seguinte marcação:

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    O arquivo precedente *main.css* define o estilo do aplicativo.

1. Crie *src/tsconfig. JSON* com o seguinte JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    O código precedente configura o compilador TypeScript para produzir um JavaScript compatível com [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Crie *src/index. TS* com o seguinte código:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    O TypeScript precedente recupera referências a elementos DOM e anexa dois manipuladores de eventos:

    * `keyup`: esse evento é acionado quando o usuário digita na caixa de texto `tbMessage`. A função `send` é chamada quando o usuário pressionar a tecla **Enter**.
    * `click`: esse evento é acionado quando o usuário clica no botão **Enviar**. A função `send` é chamada.

## <a name="configure-the-aspnet-core-app"></a>Configurar o aplicativo do ASP.NET Core

1. O código fornecido no método `Startup.Configure` exibe *Olá, Mundo*. Substitua a chamada para o método `app.Run` com chamadas para [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    O código precedente permite que o servidor localize e forneça o arquivo *index.html*, se o usuário inserir a URL completa ou a URL raiz do aplicativo Web.

1. Chamar [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) em `Startup.ConfigureServices`. Ele adiciona os serviços do Signalr ao projeto.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Mapeie uma rota */hub* para o hub `ChatHub`. Adicione as seguintes linhas ao final da `Startup.Configure`:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. Crie um novo diretório, chamado *Hubs*, na raiz do projeto. A finalidade é armazenar o hub SignalR, que é criado na próxima etapa.

1. Crie o hub *Hubs/ChatHub.cs* com o código a seguir:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Adicione o código a seguir ao topo do arquivo *Startup.cs* para resolver a referência `ChatHub`:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Habilitar a comunicação entre o cliente e o servidor

Atualmente, o aplicativo exibe um formulário simples para enviar mensagens. Nada acontece quando você tenta fazer alguma coisa. O servidor está escutando uma rota específica, mas não faz nada com as mensagens enviadas.

1. Execute o seguinte comando na raiz do projeto:

    ```console
    npm install @aspnet/signalr
    ```

    O comando precedente instala o [cliente TypeScript do SignalR](https://www.npmjs.com/package/@microsoft/signalr), que permite ao cliente enviar mensagens para o servidor.

1. Adicione o código destacado ao arquivo *src/index.ts*:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    O código precedente é compatível com o recebimento de mensagens do servidor. A classe `HubConnectionBuilder` cria um novo construtor para configurar a conexão do servidor. A função `withUrl` configura a URL do hub.

    O SignalR habilita a troca de mensagens entre um cliente e um servidor. Cada mensagem tem um nome específico. Por exemplo, mensagens com o nome `messageReceived` podem executar a lógica responsável por exibir a nova mensagem na zona de mensagens. É possível escutar uma mensagem específica por meio da função `on`. Você pode escutar qualquer número de nomes de mensagem. Também é possível passar parâmetros para a mensagem, como o nome do autor e o conteúdo da mensagem recebida. Quando o cliente recebe a mensagem, um novo elemento `div` é criado com o nome do autor e o conteúdo da mensagem em seu atributo `innerHTML`. A nova mensagem é adicionada ao elemento de `div` principal exibindo as mensagens.

1. Agora que o cliente pode receber mensagens, configure-o para enviá-las. Adicione o código destacado ao arquivo *src/index.ts*:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    Enviar uma mensagem por meio da conexão WebSockets exige uma chamada para o método `send`. O primeiro parâmetro do método é o nome da mensagem. Os dados da mensagem residem nos outros parâmetros. Neste exemplo, uma mensagem identificada como `newMessage` é enviada ao servidor. A mensagem é composta do nome de usuário e da entrada em uma caixa de texto. Se o envio for bem-sucedido, o valor da caixa de texto será limpo.

1. Adicione o método `NewMessage` à classe `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    O código precedente transmite as mensagens recebidas para todos os usuários conectados quando o servidor as recebe. Não é necessário ter um método genérico `on` para receber todas as mensagens. Um método nomeado com o nome da mensagem é suficiente.

    Neste exemplo, o cliente TypeScript envia uma mensagem identificada como `newMessage`. O método `NewMessage` de C# espera os dados enviados pelo cliente. É feita uma chamada para [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) em [clients. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). As mensagens recebidas são enviadas a todos os clientes conectados ao hub.

## <a name="test-the-app"></a>Testar o aplicativo

Confirme que o aplicativo funciona com as seguintes etapas.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Execute o Webpack no modo de *versão*. Usando a janela do **console do Gerenciador de pacotes** , execute o seguinte comando na raiz do projeto. Se você não estiver na raiz do projeto, insira `cd SignalRWebPack` antes de inserir o comando.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Selecione **Debug** > **Iniciar sem depuração** para iniciar o aplicativo em um navegador sem anexar o depurador. O arquivo *wwwroot/index.html* é fornecido em `http://localhost:<port_number>`.

1. Abra outra instância do navegador (qualquer navegador). Cole a URL na barra de endereços.

1. Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**. O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Execute o Webpack no modo de *versão* executando o comando a seguir na raiz do projeto:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Crie e execute o aplicativo executando o seguinte comando na raiz do projeto:

    ```dotnetcli
    dotnet run
    ```

    O servidor Web inicia o aplicativo e o disponibiliza no localhost.

1. Abra um navegador em `http://localhost:<port_number>`. O arquivo *wwwroot/index.html* é fornecido. Copie a URL da barra de endereços.

1. Abra outra instância do navegador (qualquer navegador). Cole a URL na barra de endereços.

1. Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**. O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.

---

![mensagem exibida em ambas as janelas do navegador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
