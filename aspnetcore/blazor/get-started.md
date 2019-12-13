---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Comece a usar o Blazor criando um aplicativo Blazor com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: e368ecaf931d392de7e52ec2d5a2dfd171c2c86f
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943753"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a>Introdução ao ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Introdução ao Blazor:

::: moniker range=">= aspnetcore-3.1"

1. Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcionalmente, instale o modelo [WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) :
   * Instale o [SDK do .NET Core 3,1 ou posterior (versão prévia)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
   * Execute o comando a seguir em um shell de comando. O [Microsoft. AspNetCore.Blazor. ](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)O pacote de modelos tem uma versão de visualização enquanto Blazor Webassembly está em visualização.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. Siga as orientações para sua escolha de ferramentas:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Instale o [Visual Studio 16,4 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a carga de trabalho de **desenvolvimento da web e do ASP.net** .

   2 \. Crie um novo projeto.

   3 \. Selecione **Blazor aplicativo**. Selecione **Avançar**.

   4 \. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

   5 \. Para obter uma experiência de Webassembly Blazor, escolha o modelo de **aplicativoBlazor Webassembly** . Para obter uma experiência de Blazor Server, escolha o modelo de **aplicativoBlazor Server** . Selecione **Criar**. Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.

   6 \. Pressione **Ctrl**+**F5** para executar o aplicativo.

   > [!NOTE]
   > Se você instalou a extensão do Blazor Visual Studio para uma versão prévia anterior do ASP.NET Core Blazor (visualização 6 ou anterior), você pode desinstalar a extensão. Instalar os modelos de Blazor em um shell de comando agora é suficiente para retonar os modelos no Visual Studio.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. Instalar o [Visual Studio Code](https://code.visualstudio.com/).

   2 \. Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3 \. Para obter uma experiência de Webassembly Blazor, execute o seguinte comando em um shell de comando:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Para obter uma experiência de Blazor Server, execute o seguinte comando em um shell de comando:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.

   4 \. Abra a pasta *WebApplication1* em Visual Studio Code.

   5 \. Para um projeto do Blazor Server, o IDE solicita que você adicione ativos para compilar e depurar o projeto. Selecione **Sim** na barra superior.

   6 \. Se estiver usando um aplicativo do Blazor Server, execute o aplicativo usando o depurador de Visual Studio Code. Se estiver usando um aplicativo Webassembly Blazor, execute `dotnet run` na pasta do projeto do aplicativo.

   7 \. Em um navegador, navegue até `https://localhost:5001`.

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

   1 \. Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/). Alterne o [canal de atualização para visualização](/visualstudio/mac/install-preview).

   2 \. Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.

   3 \. Na barra lateral, selecione **.NET Core** > **aplicativo**.

   4 \. Selecione o modelo de **aplicativo doBlazor Server** . Somente o modelo do Blazor Server está disponível no Visual Studio para Mac no momento. Para obter uma experiência de Webassembly Blazor, siga as instruções na guia **CLI do .NET Core** . Depois de selecionar o modelo do Blazor Server, selecione **Avançar**. Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5 \. Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.

   6 \. No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`. Selecione **Criar**.

   7 \. Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*. Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

   Para obter uma experiência de Webassembly Blazor, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obter uma experiência de Blazor Server, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.

   Em um navegador, navegue até `https://localhost:5001`.

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. Instale o [SDK do .NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)mais recente.

1. Opcionalmente, instale o modelo [WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) :
   * Instale o [SDK do .NET Core 3,1 ou posterior (versão prévia)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
   * Execute o comando a seguir em um shell de comando. O [Microsoft. AspNetCore.Blazor. ](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)O pacote de modelos tem uma versão de visualização enquanto Blazor Webassembly está em visualização.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. Siga as orientações para sua escolha de ferramentas:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Instale o [Visual Studio](https://visualstudio.com/vs/) mais recente com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

   2 \. Opcionalmente, instale o [Visual Studio 16,4 Preview 2 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com o ASP.net e a carga de trabalho de **desenvolvimento Web** para o desenvolvimento de aplicativos Webassembly Blazor.

   3 \. Crie um novo projeto.

   4 \. Selecione **Blazor aplicativo**. Selecione **Avançar**.

   5 \. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

   6 \. Para obter uma experiência de Webassembly Blazor, escolha o modelo de **aplicativoBlazor Webassembly** . Para obter uma experiência de Blazor Server, escolha o modelo de **aplicativoBlazor Server** . Selecione **Criar**. Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.

   7 \. Pressione **F5** para executar o aplicativo.

   > [!NOTE]
   > Se você instalou a extensão do Blazor Visual Studio para uma versão prévia anterior do ASP.NET Core Blazor (visualização 6 ou anterior), você pode desinstalar a extensão. Instalar os modelos de Blazor em um shell de comando agora é suficiente para retonar os modelos no Visual Studio.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. Instalar o [Visual Studio Code](https://code.visualstudio.com/).

   2 \. Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3 \. Para obter uma experiência de Webassembly Blazor, execute o seguinte comando em um shell de comando:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Para obter uma experiência de Blazor Server, execute o seguinte comando em um shell de comando:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.

   4 \. Abra a pasta *WebApplication1* em Visual Studio Code.

   5 \. Para um projeto do Blazor Server, o IDE solicita que você adicione ativos para compilar e depurar o projeto. Selecione **Sim** na barra superior.

   6 \. Se estiver usando um aplicativo do Blazor Server, execute o aplicativo usando o depurador de Visual Studio Code. Se estiver usando um aplicativo Webassembly Blazor, execute `dotnet run` na pasta do projeto do aplicativo.

   7 \. Em um navegador, navegue até `https://localhost:5001`.

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

   1 \. Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/). Alterne o [canal de atualização para visualização](/visualstudio/mac/install-preview).

   2 \. Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.

   3 \. Na barra lateral, selecione **.NET Core** > **aplicativo**.

   4 \. Selecione o modelo de **aplicativo doBlazor Server** . Somente o modelo do Blazor Server está disponível no Visual Studio para Mac no momento. Para obter uma experiência de Webassembly Blazor, siga as instruções na guia **CLI do .NET Core** . Depois de selecionar o modelo do Blazor Server, selecione **Avançar**. Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5 \. Defina a **estrutura de destino** como **.NET Core 3,0** e selecione **Avançar**.

   6 \. No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`. Selecione **Criar**.

   7 \. Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*. Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

   Para obter uma experiência de Webassembly Blazor, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obter uma experiência de Blazor Server, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obter informações sobre os dois modelos de hospedagem Blazor, *Blazor Server* e *Blazor Webassembly*, consulte <xref:blazor/hosting-models>.

   Em um navegador, navegue até `https://localhost:5001`.

   ---

::: moniker-end

Várias páginas estão disponíveis em guias na barra lateral:

* Home
* Contador
* Buscar dados

Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página. Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas com Blazor C#você pode usar.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Uma solicitação de `/counter` no navegador, conforme especificado pela diretiva `@page` na parte superior, faz com que o componente `Counter` processe seu conteúdo. Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.

Sempre que o botão **clicar em mim** estiver selecionado:

* O evento `onclick` é acionado.
* O método `IncrementCount` é chamado.
* O `currentCount` é incrementado.
* O componente é renderizado novamente.

O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).

Adicione um componente a outro componente usando a sintaxe HTML. Por exemplo, adicione o componente `Counter` à Home Page do aplicativo adicionando um elemento `<Counter />` ao componente `Index`.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Execute o aplicativo. A Home Page tem seu próprio contador fornecido pelo componente `Counter`.

Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho. Para adicionar um parâmetro ao componente `Counter`, atualize o bloco de `@code` do componente:

* Adicione uma propriedade pública para `IncrementAmount` com um atributo `[Parameter]`.
* Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Especifique o `IncrementAmount` no elemento `<Counter>` do componente de `Index` usando um atributo.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Execute o aplicativo. O componente `Index` tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado. O componente `Counter` (*Counter. Razor*) em `/counter` continua a incrementar um.

## <a name="next-steps"></a>Próximas etapas

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/templates>
* <xref:signalr/introduction>
