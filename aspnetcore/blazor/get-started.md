---
title: Introdução ao ASP.NET Core mais incrivelmente
author: guardrex
description: Comece com o mais elaborado criando um aplicativo mais incrivelmente com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/get-started
ms.openlocfilehash: 58773ae6c605ddc7a3d85fb97eeae40d0bbe15fb
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080518"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introdução ao ASP.NET Core mais incrivelmente

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Introdução ao mais incrivelmente:

1. Instale a versão mais recente do [SDK do .NET Core 3,0 Preview](https://dotnet.microsoft.com/download/dotnet-core/3.0) .

1. Instale os modelos mais poficados executando o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19457.4
   ```

1. Siga as orientações para sua escolha de ferramentas:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Instale a visualização mais recente do [Visual Studio](https://visualstudio.com/vs/preview) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

   2 \. Crie um novo projeto.

   3 \. Selecione **aplicativo mais incrivelmente**. Selecione **Avançar**.

   4 \. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

   5 \. Para obter uma experiência de Webassembly mais experiente, escolha o modelo de **aplicativo Webassembly mais incrivelmente** . Para uma experiência de servidor mais incrivelmente, escolha o modelo de **aplicativo de servidor** mais experiente. Selecione **Criar**. Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.

   6 \. Pressione **F5** para executar o aplicativo.

   > [!NOTE]
   > Se você instalou a extensão do Visual Studio para uma versão prévia anterior do ASP.NET Core mais recente (visualização 6 ou anterior), você pode desinstalar a extensão. Instalar os modelos mais bem em um shell de comando agora é suficiente para trazer os modelos no Visual Studio.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. Instalar o [Visual Studio Code](https://code.visualstudio.com/).

   2 \. Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3 \. Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.

   4 \. Abra a pasta *WebApplication1* em Visual Studio Code.

   5 \. Para um projeto de servidor mais incrivelmente, o IDE solicita que você adicione ativos para compilar e depurar o projeto. Selecione **Sim** na barra superior.

   6 \. Se estiver usando um aplicativo de servidor mais incrivelmente, execute o aplicativo usando o depurador de Visual Studio Code. Se estiver usando um aplicativo Webassembly mais incrivelmente, `dotnet run` execute na pasta do projeto do aplicativo.

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

   Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.

   Em um navegador, navegue até `https://localhost:5001`.

   ---

Várias páginas estão disponíveis em guias na barra lateral:

* Home
* Contador
* Buscar dados

Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página. Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas os componentes do Razor C#fornecem uma abordagem melhor usando o.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Uma solicitação para `/counter` no navegador, conforme especificado `@page` pela diretiva na parte superior, faz com que o `Counter` componente processe seu conteúdo. Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.

Sempre que o botão **clicar em mim** estiver selecionado:

* O `onclick` evento é acionado.
* O método `IncrementCount` é chamado.
* O `currentCount` é incrementado.
* O componente é renderizado novamente.

O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).

Adicione um componente a outro componente usando a sintaxe HTML. Por exemplo, adicione o `Counter` componente à página inicial do aplicativo adicionando um `<Counter />` elemento ao `Index` componente.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Execute o aplicativo. A Home Page tem seu próprio contador fornecido pelo `Counter` componente.

Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho. Para adicionar um parâmetro ao `Counter` componente, atualize o bloco do `@code` componente:

* Adicione uma propriedade pública para `IncrementAmount` com um `[Parameter]` atributo.
* Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Especifique o `IncrementAmount` noelemento`<Counter>` do componenteusandoumatributo.`Index`

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Execute o aplicativo. O `Index` componente tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado. O `Counter` componente (*Counter. Razor*) em `/counter` continua a incrementar um.

## <a name="next-steps"></a>Próximas etapas

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>
