---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Comece a usar o Blazor criando um aplicativo Blazor com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 9b4aee0be30568f098c756e9ab4cb5298e9a049b
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963004"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a>Introdução ao ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Introdução ao Blazor:

::: moniker range=">= aspnetcore-3.1"

1. Instale o [SDK da versão prévia do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Instale o modelo [WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) executando o comando a seguir em um shell de comando. O [Microsoft. AspNetCore.Blazor. ](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)O pacote de modelos tem uma versão de visualização enquanto Blazor Webassembly está em visualização.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. Siga as orientações para sua escolha de ferramentas:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Instale o [Visual Studio 16,4 Preview 2 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

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

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

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

1. Instale a versão mais recente do [SDK do .NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) .

1. Opcionalmente, instale o modelo [WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) instalando o [SDK do .NET Core 3,1 Preview](https://dotnet.microsoft.com/download/dotnet-core/3.1) e, em seguida, executando o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
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

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

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

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Uma solicitação de `/counter` no navegador, conforme especificado pela diretiva `@page` na parte superior, faz com que o componente `Counter` processe seu conteúdo. Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.

Sempre que o botão **clicar em mim** estiver selecionado:

* O evento `onclick` é acionado.
* O método `IncrementCount` é chamado.
* O `currentCount` é incrementado.
* O componente é renderizado novamente.

O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).

Adicione um componente a outro componente usando a sintaxe HTML. Por exemplo, adicione o componente `Counter` à página inicial do aplicativo adicionando um elemento `<Counter />` ao componente `Index`.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Execute o aplicativo. A Home Page tem seu próprio contador fornecido pelo componente `Counter`.

Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho. Para adicionar um parâmetro ao componente `Counter`, atualize o bloco `@code` do componente:

* Adicione uma propriedade pública para `IncrementAmount` com um atributo `[Parameter]`.
* Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Especifique o `IncrementAmount` no elemento `<Counter>` do componente `Index` usando um atributo.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Execute o aplicativo. O componente `Index` tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** é selecionado. O componente `Counter` (*Counter. Razor*) em `/counter` continua a incrementar um.

## <a name="next-steps"></a>Próximas etapas

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>
