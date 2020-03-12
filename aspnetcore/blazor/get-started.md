---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Comece a usar o Blazor criando um aplicativo Blazor com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 89c7529d2b8ec97db731f7c7268e19937c398115
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083243"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introdução ao ASP.NET Core mais incrivelmente

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Introdução ao mais incrivelmente:

1. Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcionalmente, instale o modelo [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) :
   * Instale o [SDK do .NET Core 3.1.102 ou posterior (versão prévia)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
   * Execute o comando a seguir em um shell de comando. O pacote [Microsoft. AspNetCore. Components. Webassembly. templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) tem uma versão de visualização enquanto o Webassembly de mais de baixo está em visualização.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
   ```

   > [!NOTE]
   > SDK do .NET Core versão 3.1.102 ou posterior é **necessária** para usar o modelo Webassembly do 3,2 Preview 2 mais recente. Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.

1. Siga as orientações para sua escolha de ferramentas:

   # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Instale o [Visual Studio 2019 versão 16,4 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

   2 \. Criar um novo projeto.

   3 \. Selecione **aplicativo mais incrivelmente**. Selecione **Avançar**.

   4 \. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

   5 \. Para obter uma experiência de Webassembly mais experiente, escolha o modelo de **aplicativo Webassembly mais incrivelmente** . Para uma experiência de servidor mais incrivelmente, escolha o modelo de **aplicativo de servidor** mais experiente. Selecione **Criar**. Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>. Se o modelo Webassembly mais antigo não estiver presente, retorne à etapa anterior e reinstale o modelo.

   6 \. Pressione **Ctrl**+**F5** para executar o aplicativo.

   > [!NOTE]
   > Se você instalou a extensão do Visual Studio para uma versão prévia anterior do ASP.NET Core mais recente (visualização 6 ou anterior), você pode desinstalar a extensão. Instalar os modelos mais bem em um shell de comando agora é suficiente para trazer os modelos no Visual Studio.

   # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. Instale o [Visual Studio Code](https://code.visualstudio.com/).

   2 \. Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

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

   6 \. Se estiver usando um aplicativo de servidor mais incrivelmente, execute o aplicativo usando o depurador de Visual Studio Code. Se estiver usando um aplicativo Webassembly mais incrivelmente, execute `dotnet run` na pasta do projeto do aplicativo.

   7 \. Em um navegador, navegue até `https://localhost:5001`.

   # <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

   1 \. Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

   2 \. Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.

   3 \. Na barra lateral, selecione **.NET Core** > **aplicativo**.

   4 \. Selecione o modelo de **aplicativo de servidor mais incrivelmente** . Somente o modelo de servidor mais útil está disponível no Visual Studio para Mac no momento. Para obter uma experiência de Webassembly mais experiente, siga as instruções na guia **CLI do .NET Core** . Depois de selecionar o modelo de servidor mais novo, selecione **Avançar**. Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5 \. Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.

   6 \. No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`. Selecione **Criar**.

   7 \. Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*. Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.

   Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.

   # <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

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
