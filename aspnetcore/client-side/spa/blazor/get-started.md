---
title: Introdução ao Blazor
author: guardrex
description: Saiba como começar com Blazor criando e modificando um projeto Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: spa/blazor/get-started
ms.openlocfilehash: f46bd9af0f0762e794349d4e98de5c086a690d72
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327223"
---
# <a name="get-started-with-blazor"></a>Introdução ao Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Pré-requisitos:

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

Para criar seu primeiro projeto Blazor no Visual Studio:

1. Instale o versão mais recente [Blazor extensão](https://go.microsoft.com/fwlink/?linkid=870389) do Visual Studio Marketplace. Esta etapa disponibiliza Blazor modelos para o Visual Studio.
1. Verifique os modelos de Blazor disponíveis para uso com a CLI do .NET Core, executando o seguinte comando em um shell de comando:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```

1. Selecione **arquivo** > **novo projeto** > **Web** > **aplicativo Web ASP.NET Core**.
1. Certifique-se **.NET Core** e **ASP.NET Core 3.0** são selecionados na parte superior.
1. Escolha o modelo **Blazor** e selecione **OK**.
1. Pressione **F5** para executar o aplicativo.

Parabéns! Você acaba de executar seu primeiro aplicativo Blazor!

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Blazor project in Visual Studio Code:

1. Execute the following command in a command shell:

   ```console
   dotnet new blazor -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Visual Studio code offers to create assets to build and debug the app, which includes the *tasks.json* and *launch.json* files. Select **Yes** to add the assets.

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Blazor app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Blazor project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **Blazor** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Blazor app!
-->

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Pré-requisitos:

* [.NET core SDK 3.0 versão prévia](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. Adicione os modelos de Blazor, executando o seguinte comando em um shell de comando:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```

1. Crie seu primeiro projeto Blazor em um shell de comando:

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. Em um navegador, navegue até `https://localhost:5001`.

Parabéns! Você acaba de executar seu primeiro aplicativo Blazor!

---

## <a name="blazor-project"></a>Projeto Blazor

Quando o aplicativo é executado, várias páginas estão disponíveis nas guias na barra lateral:

* Home
* Contador
* Buscar dados

Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página. Incrementar um contador em uma página da Web normalmente requer a gravação do JavaScript, mas Blazor fornece uma abordagem melhor usando C#.

*Pages/Counter.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

Uma solicitação para `/counter` no navegador, conforme especificado pelo `@page` diretiva na parte superior, faz com que o componente de contador processar seu conteúdo. Componentes processam em uma representação na memória da árvore de renderização que pode ser usado para atualizar a interface do usuário de uma maneira flexível e eficiente.

Cada vez que o **Click me** botão é selecionado:

* O `onclick` evento é disparado.
* O método `IncrementCount` é chamado.
* O `currentCount` é incrementado.
* O componente é renderizado novamente.

O tempo de execução compara o novo conteúdo para o conteúdo anterior e só se aplica o conteúdo alterado para modelo de objeto de documento (DOM).

Adicione um componente para outro componente usando uma sintaxe semelhante ao HTML. Componente são especificados usando atributos ou conteúdo filho. Por exemplo, um componente de contador pode ser adicionado a homepage do aplicativo adicionando um `<Counter />` elemento para o componente do índice.

Na *Pages*, substitua o componente de solicitação de pesquisa com um componente de contador:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

Execute o aplicativo. A home page tem seu próprio contador.

Para adicionar um parâmetro para o componente de contador, atualize o componente `@functions` bloco:

* Adicionar uma propriedade para `IncrementAmount` decorada com o `[Parameter]` atributo.
* Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.

*Pages/Counter.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente Home usando um atributo.

*Pages/Index.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

Execute o aplicativo. A home page tem seu próprio contador que incrementa dez cada vez que o **Click me** botão é selecionado.

## <a name="next-steps"></a>Próximas etapas

<xref:tutorials/first-razor-components-app>
