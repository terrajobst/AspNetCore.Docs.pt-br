---
title: Link do navegador no ASP.NET Core
author: ncarandini
description: Explica como o link do navegador é um recurso do Visual Studio que vincula o ambiente de desenvolvimento com um ou mais navegadores da Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658847"
---
# <a name="browser-link-in-aspnet-core"></a>Link do navegador no ASP.NET Core

Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)e [Tom Dykstra](https://github.com/tdykstra)

O link do navegador é um recurso do Visual Studio. Ele cria um canal de comunicação entre o ambiente de desenvolvimento e um ou mais navegadores da Web. Você pode usar o link do navegador para atualizar seu aplicativo Web em vários navegadores de uma vez, o que é útil para testes entre navegadores.

## <a name="browser-link-setup"></a>Instalação do link do navegador

::: moniker range=">= aspnetcore-3.0"

Adicione o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) ao seu projeto. Para projetos ASP.NET Core Razor Pages ou MVC, habilite também a compilação em tempo de execução de arquivos Razor ( *. cshtml*), conforme descrito em <xref:mvc/views/view-compilation>. Sintaxe Razor alterações são aplicadas somente quando a compilação em tempo de execução tiver sido habilitada.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Ao converter um projeto ASP.NET Core 2,0 para ASP.NET Core 2,1 e fazer a transição para o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), instale o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) para a funcionalidade de link do navegador. Os modelos de projeto ASP.NET Core 2,1 usam o metapacote do `Microsoft.AspNetCore.App` por padrão.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Os modelos de projeto de **aplicativo web**ASP.NET Core 2,0, **vazio**e de **API Web** usam o [metapacote Microsoft. AspNetCore. All](xref:fundamentals/metapackage), que contém uma referência de pacote para [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Portanto, o uso do metapacote `Microsoft.AspNetCore.All` não requer nenhuma ação adicional para disponibilizar o link do navegador para uso.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

O modelo de projeto de **aplicativo Web** ASP.NET Core 1. x tem uma referência de pacote para o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) . Outros tipos de projeto exigem que você adicione uma referência de pacote a `Microsoft.VisualStudio.Web.BrowserLink`.

::: moniker-end

### <a name="configuration"></a>Configuração

Chame `UseBrowserLink` no método `Startup.Configure`:

```csharp
app.UseBrowserLink();
```

A chamada de `UseBrowserLink` normalmente é colocada dentro de um bloco de `if` que habilita apenas o link do navegador no ambiente de desenvolvimento. Por exemplo:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Para obter mais informações, consulte <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Como usar o link do navegador

Quando você tem um projeto ASP.NET Core aberto, o Visual Studio mostra o controle da barra de ferramentas link do navegador ao lado do controle da barra de ferramentas de **destino de depuração** :

![Menu suspenso de link do navegador](using-browserlink/_static/browserLink-dropdown-menu.png)

No controle da barra de ferramentas do link do navegador, você pode:

* Atualize o aplicativo Web em vários navegadores de uma vez.
* Abra o **painel de link do navegador**.
* Habilitar ou desabilitar o **link do navegador**. Observação: o link do navegador está desabilitado por padrão no Visual Studio.
* Habilitar ou desabilitar [a sincronização automática de CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Atualizar o aplicativo Web em vários navegadores de uma vez

Para escolher um único navegador da Web para iniciar ao iniciar o projeto, use o menu suspenso no controle da barra de ferramentas de **destino de depuração** :

![F5 menu suspenso](using-browserlink/_static/debug-target-dropdown-menu.png)

Para abrir vários navegadores de uma vez, escolha **procurar com...** na mesma lista suspensa. Mantenha pressionada a tecla <kbd>Ctrl</kbd> para selecionar os navegadores desejados e clique em **procurar**:

![Abrir vários navegadores de uma vez](using-browserlink/_static/open-many-browsers-at-once.png)

A captura de tela a seguir mostra o Visual Studio com a exibição de índice aberta e dois navegadores abertos:

![Exemplo de sincronização com dois navegadores](using-browserlink/_static/sync-with-two-browsers-example.png)

Passe o mouse sobre o controle da barra de ferramentas link do navegador para ver os navegadores que estão conectados ao projeto:

![Dica de foco](using-browserlink/_static/hoover-tip.png)

Altere a exibição do índice e todos os navegadores conectados serão atualizados quando você clicar no botão atualizar do link do navegador:

![navegadores-sincronização-para-alterações](using-browserlink/_static/browsers-sync-to-changes.png)

O link do navegador também funciona com navegadores que você inicia de fora do Visual Studio e navega até a URL do aplicativo.

### <a name="the-browser-link-dashboard"></a>O painel do link do navegador

Abra a janela do **Painel link do navegador** no menu suspenso link do navegador para gerenciar a conexão com os navegadores abertos:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Se nenhum navegador estiver conectado, você poderá iniciar uma sessão de não depuração selecionando o link **Exibir no navegador** :

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Caso contrário, os navegadores conectados são mostrados com o caminho para a página que cada navegador está mostrando:

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Você também pode clicar em um nome de navegador individual para atualizar somente esse navegador.

### <a name="enable-or-disable-browser-link"></a>Habilitar ou desabilitar o link do navegador

Ao reabilitar o link do navegador depois de desabilitá-lo, você deve atualizar os navegadores para reconectá-los.

### <a name="enable-or-disable-css-auto-sync"></a>Habilitar ou desabilitar a sincronização automática de CSS

Quando a sincronização automática de CSS está habilitada, os navegadores conectados são atualizados automaticamente quando você faz qualquer alteração em arquivos CSS.

## <a name="how-it-works"></a>Como ele funciona

O link do navegador usa [SignalR](xref:signalr/introduction) para criar um canal de comunicação entre o Visual Studio e o navegador. Quando o link do navegador está habilitado, o Visual Studio atua como um SignalR servidor ao qual vários clientes (navegadores) podem se conectar. O link do navegador também registra um componente de middleware no pipeline de solicitação de ASP.NET Core. Esse componente injeta referências especiais de `<script>` em cada solicitação de página do servidor. Você pode ver as referências de script selecionando **Exibir origem** no navegador e rolando até o final do `<body>` conteúdo da marca:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Os arquivos de origem não são modificados. O componente de middleware injeta as referências de script dinamicamente.

Como o código do lado do navegador é todo o JavaScript, ele funciona em todos os navegadores que SignalR oferece suporte sem a necessidade de um plug-in de navegador.
