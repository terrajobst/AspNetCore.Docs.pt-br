---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Comece a usar o Blazor criando um aplicativo Blazor com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 9ebeb57d2fad7e4c288d61a46911f2bf64cac2fb
ms.sourcegitcommit: f3b1bcfd108e5d53f73abc0bf2555890869d953b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80320938"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Introdução ao ASP.NET Core mais incrivelmente

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Para começar com o mais claro, siga as orientações para sua escolha de ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Para criar aplicativos de servidor mais recentes, instale o [Visual Studio 2019 versão 16,4 ou posterior](https://visualstudio.microsoft.com/vs/preview/) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .

   Para criar aplicativos Webassembly mais popicores e de servidor de mais recente, instale o Visual Studio 2019 16,6 Preview 2 ou posterior com a ASP.NET e a carga de trabalho de **desenvolvimento na Web** .

   Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, *Webassembly* e *servidor*mais incrivelmente, consulte <xref:blazor/hosting-models>.

1. Crie um novo projeto.

1. Selecione **aplicativo mais incrivelmente**. Selecione **Avançar**.

1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Para obter uma experiência de Webassembly mais experiente (Visual Studio 16,6 Preview 2 ou posterior), escolha o modelo de **aplicativo Webassembly** mais experiente. Para uma experiência de servidor mais incrivelmente (Visual Studio 16,4 ou posterior), escolha o modelo de **aplicativo de servidor** mais experiente. Selecione **Criar**.

1. Pressione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcionalmente, instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview 3 mais recente. Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.

1. Instale o [Visual Studio Code](https://code.visualstudio.com/).

1. Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true`.

1. Para uma experiência de servidor mais incrivelmente, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para uma experiência de Webassembly mais experiente, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.

1. Abra a pasta *WebApplication1* em Visual Studio Code.

1. O IDE solicita que você adicione ativos para compilar e depurar o projeto. Selecione **Sim**.

1. Execute o aplicativo usando o depurador de Visual Studio Code.

1. Em um navegador, navegue até `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Há suporte para o servidor mais incrivelmente no Visual Studio para Mac. Não há suporte para Webassembly mais incrivelmente no momento. Para criar aplicativos Webassembly mais elaborados no macOS, siga as orientações na guia **CLI do .NET Core** .

1. Instale o [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selecione **arquivo** > **nova solução** ou crie um **novo projeto**.

1. Na barra lateral, selecione **.NET Core** > **aplicativo**.

1. Selecione o modelo de **aplicativo de servidor mais incrivelmente** . Selecione **Criar**.

   Para obter informações sobre o modelo de Hospedagem de servidor mais incrivelmente, consulte <xref:blazor/hosting-models>.

1. Defina a **estrutura de destino** como **.NET Core 3,1** e selecione **Avançar**.

1. No campo **nome do projeto** , nomeie o aplicativo `WebApplication1`. Selecione **Criar**.

1. Selecione **executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*. Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.

Se aparecer um prompt para confiar no certificado de desenvolvimento, confie no certificado e continue.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Instale o [SDK do .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcionalmente, instale o modelo de visualização do [Webassembly mais incrivelmente](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > O [SDK do .NET Core versão 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessário** para usar o modelo Webassembly do 3,2 Preview 3 mais recente. Confirme a versão do SDK do .NET Core instalada executando `dotnet --version` em um shell de comando.

1. Para uma experiência de servidor mais incrivelmente, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para uma experiência de Webassembly mais experiente, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, um *servidor mais incrivelmente* e um *Webassembly*de mais ou mais, consulte <xref:blazor/hosting-models>.

1. Em um navegador, navegue até `https://localhost:5001`.

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

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/templates>
* <xref:signalr/introduction>
