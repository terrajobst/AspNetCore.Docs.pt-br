---
title: Usar ASP.NET Core SignalR com Blazor Webassembly
author: guardrex
description: Crie um aplicativo de chat que usa ASP.NET Core SignalR com Blazor Webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: c4843dc282e1978b39738e206ecc79ded87fcff9
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306568"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Usar Signalr ASP.NET Core com Webassembly de mais incrivelmente

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Este tutorial ensina as noções básicas da criação de um aplicativo em tempo real usando Signalr com Webassembly mais alto. Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de aplicativo hospedado Webassembly de mais incrivelmente
> * Adicionar a biblioteca de clientes do SignalR
> * Adicionar um Hub do Signalr
> * Adicionar serviços do Signalr e um ponto de extremidade para o Hub do Signalr
> * Adicionar código de componente Razor para chat

No final deste tutorial, você terá um aplicativo de chat em funcionamento.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Criar um projeto de aplicativo Webassembly de mais incrivelmente hospedado

Quando não estiver usando o Visual Studio versão 16,6 Preview 2 ou posterior, instale o modelo [Webassembly](xref:blazor/hosting-models#blazor-webassembly) mais recente. O pacote [Microsoft. AspNetCore. Components. Webassembly. templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) tem uma versão de visualização enquanto o Webassembly de mais de baixo está em visualização. Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
```

Siga as orientações para sua escolha de ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Crie um novo projeto.

1. Selecione **aplicativo** mais novo e selecione **Avançar**.

1. Digite "BlazorSignalRApp" no campo **nome do projeto** . Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Escolha o modelo de **aplicativo Webassembly mais incrivelmente** .

1. Em **avançado**, marque a caixa de seleção **ASP.NET Core hospedado** .

1. Selecione **Criar**.

> [!NOTE]
> Se você atualizou ou instalou uma nova versão do Visual Studio e o modelo Webassembly mais novo não aparece na interface do usuário do VS, reinstale o modelo usando o comando `dotnet new` mostrado anteriormente.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Em Visual Studio Code, abra a pasta do projeto do aplicativo.

1. Quando a caixa de diálogo aparecer para adicionar ativos para compilar e depurar o aplicativo, selecione **Sim**. Visual Studio Code adiciona automaticamente a pasta *. vscode* com arquivos *Launch. JSON* e *Tasks. JSON* gerados.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Em um shell de comando, execute o seguinte comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. No Visual Studio para Mac, abra o projeto navegando até a pasta do projeto e abrindo o arquivo de solução do projeto ( *. sln*).

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Adicionar a biblioteca de clientes do SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a **origem do pacote** está definida como *NuGet.org*.

1. Com **procurar** selecionado, digite "Microsoft. AspNetCore. signaler. Client" na caixa de pesquisa.

1. Nos resultados da pesquisa, selecione o pacote `Microsoft.AspNetCore.SignalR.Client` e selecione **instalar**.

1. Se a caixa de diálogo **Visualizar alterações** for exibida, selecione **OK**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida **, selecione aceito** se você concordar com os termos de licença.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

No **terminal integrado** (**Exibir** > **terminal** na barra de ferramentas), execute os seguintes comandos:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , clique com o botão direito do mouse no projeto **BlazorSignalRApp. Client** e selecione **gerenciar pacotes NuGet**.

1. Na caixa de diálogo **gerenciar pacotes NuGet** , confirme se a lista suspensa origem está definida como *NuGet.org*.

1. Com **procurar** selecionado, digite "Microsoft. AspNetCore. signaler. Client" na caixa de pesquisa.

1. Nos resultados da pesquisa, marque a caixa de seleção ao lado do pacote de `Microsoft.AspNetCore.SignalR.Client` e selecione **Adicionar pacote**.

1. Se a caixa de diálogo de **aceitação da licença** for exibida, selecione **aceitar** se você concordar com os termos de licença.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Em um shell de comando, execute os seguintes comandos:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Adicionar um Hub do Signalr

No projeto **BlazorSignalRApp. Server** , crie uma pasta *hubs* (plural) e adicione a seguinte classe de `ChatHub` (*hubs/ChatHub. cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a>Adicionar serviços do Signalr e um ponto de extremidade para o Hub do Signalr

1. No projeto **BlazorSignalRApp. Server** , abra o arquivo *Startup.cs* .

1. Adicione o namespace para a classe de `ChatHub` na parte superior do arquivo:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Adicione os serviços de sinalização a `Startup.ConfigureServices`:

   ```csharp
   services.AddSignalR();
   ```

1. Em `Startup.Configure` entre os pontos de extremidade para a rota do controlador padrão e o fallback do lado do cliente, adicione um ponto final para o Hub:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Adicionar código de componente Razor para chat

1. No projeto **BlazorSignalRApp. Client** , abra o arquivo *pages/index. Razor* .

1. Substitua a marcação pelo código a seguir:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Executar o aplicativo

1. Siga as orientações para suas ferramentas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Em **Gerenciador de soluções**, selecione o projeto **BlazorSignalRApp. Server** . Pressione **Ctrl + F5** para executar o aplicativo sem depuração.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy;1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Selecione **depurar** > **executar sem depuração** na barra de ferramentas.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy;1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Na barra lateral da **solução** , selecione o projeto **BlazorSignalRApp. Server** . No menu, selecione **executar** > **Iniciar sem depuração**.

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy;1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Em um shell de comando, execute os seguintes comandos:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copie a URL da barra de endereços, abra outra instância ou guia do navegador e cole a URL na barra de endereços.

1. Escolha qualquer navegador, insira um nome e uma mensagem e selecione o botão **Enviar**. O nome e a mensagem são exibidos em ambas as páginas instantaneamente:

   ![Aplicativo de exemplo Webassembly do signalr mais do que é aberto em duas janelas de navegador mostrando mensagens trocadas.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Aspas: *Star Trek VI: o país não descoberto* &copy;1991 de [extrema importância](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um projeto de aplicativo hospedado do Webassembly Blazor
> * Adicionar a biblioteca de cliente SignalR
> * Adicionar um hub de SignalR
> * Adicionar serviços de SignalR e um ponto de extremidade para o Hub de SignalR
> * Adicionar código de componente Razor para chat

Para saber mais sobre como criar Blazor aplicativos, consulte a documentação do Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>
