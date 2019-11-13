---
title: Link do navegador no ASP.NET Core
author: ncarandini
description: Explica como o link do navegador é um recurso do Visual Studio que vincula o ambiente de desenvolvimento com um ou mais navegadores da Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: b21b698d49e72b559cd9cd3753c48a38c99db24d
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962791"
---
# <a name="browser-link-in-aspnet-core"></a>Link do navegador no ASP.NET Core

Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)e [Tom Dykstra](https://github.com/tdykstra)

O link do navegador é um recurso do Visual Studio que cria um canal de comunicação entre o ambiente de desenvolvimento e um ou mais navegadores da Web. Você pode usar o link do navegador para atualizar seu aplicativo Web em vários navegadores de uma vez, o que é útil para testes entre navegadores.

## <a name="browser-link-setup"></a>Instalação do link do navegador

::: moniker range=">= aspnetcore-2.1"

Ao converter um projeto ASP.NET Core 2,0 para ASP.NET Core 2,1 e fazer a transição para o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), instale o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) para a funcionalidade BrowserLink. Os modelos de projeto ASP.NET Core 2,1 usam o metapacote do `Microsoft.AspNetCore.App` por padrão.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Os modelos de projeto de **aplicativo web**ASP.NET Core 2,0, **vazio**e de **API Web** usam o [metapacote Microsoft. AspNetCore. All](xref:fundamentals/metapackage), que contém uma referência de pacote para [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Portanto, o uso do metapacote `Microsoft.AspNetCore.All` não requer nenhuma ação adicional para disponibilizar o link do navegador para uso.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

O modelo de projeto de **aplicativo Web** ASP.NET Core 1. x tem uma referência de pacote para o pacote [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) . Os projetos de modelo de **API Web** ou **vazios** exigem que você adicione uma referência de pacote a `Microsoft.VisualStudio.Web.BrowserLink`.

Como esse é um recurso do Visual Studio, a maneira mais fácil de adicionar o pacote a um projeto de modelo de **API Web** ou **vazio** é abrir o **console do gerenciador de pacotes** (**Exibir** > outro console do **Gerenciador de pacotes**do **Windows** >) e executar o seguinte comando:

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

Como alternativa, você pode usar o **Gerenciador de pacotes NuGet**. Clique com o botão direito do mouse no nome do projeto em **Gerenciador de soluções** e escolha **gerenciar pacotes NuGet**:

![Abrir o Gerenciador de pacotes do NuGet](using-browserlink/_static/open-nuget-package-manager.png)

Localize e instale o pacote:

![Adicionar pacote com o Gerenciador de pacotes NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a>Configuração

No método `Startup.Configure`:

```csharp
app.UseBrowserLink();
```

Normalmente, o código está dentro de um bloco de `if` que habilita apenas o link do navegador no ambiente de desenvolvimento, como mostrado aqui:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Para obter mais informações, veja [Usar vários ambientes](xref:fundamentals/environments).

## <a name="how-to-use-browser-link"></a>Como usar o link do navegador

Quando você tem um projeto ASP.NET Core aberto, o Visual Studio mostra o controle da barra de ferramentas link do navegador ao lado do controle da barra de ferramentas de **destino de depuração** :

![Menu suspenso de link do navegador](using-browserlink/_static/browserLink-dropdown-menu.png)

No controle da barra de ferramentas do link do navegador, você pode:

* Atualize o aplicativo Web em vários navegadores de uma vez.
* Abra o **painel de link do navegador**.
* Habilitar ou desabilitar o **link do navegador**. Observação: o link do navegador está desabilitado por padrão no Visual Studio 2017 (15,3).
* Habilitar ou desabilitar [a sincronização automática de CSS](#enable-or-disable-css-auto-sync).

> [!NOTE]
> Alguns plug-ins do Visual Studio, principalmente o *Web Extension pack 2015* e o *pacote de extensões da Web 2017*, oferecem funcionalidade estendida para o link do navegador, mas alguns dos recursos adicionais não funcionam com projetos ASP.NET Core.

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Atualizar o aplicativo Web em vários navegadores de uma vez

Para escolher um único navegador da Web para iniciar ao iniciar o projeto, use o menu suspenso no controle da barra de ferramentas de **destino de depuração** :

![F5 menu suspenso](using-browserlink/_static/debug-target-dropdown-menu.png)

Para abrir vários navegadores de uma vez, escolha **procurar com...** na mesma lista suspensa. Mantenha pressionada a tecla CTRL para selecionar os navegadores desejados e clique em **procurar**:

![Abrir vários navegadores de uma vez](using-browserlink/_static/open-many-browsers-at-once.png)

Aqui está uma captura de tela mostrando o Visual Studio com a exibição de índice aberta e dois navegadores abertos:

![Exemplo de sincronização com dois navegadores](using-browserlink/_static/sync-with-two-browsers-example.png)

Passe o mouse sobre o controle da barra de ferramentas link do navegador para ver os navegadores que estão conectados ao projeto:

![Dica de foco](using-browserlink/_static/hoover-tip.png)

Altere a exibição do índice e todos os navegadores conectados serão atualizados quando você clicar no botão atualizar do link do navegador:

![navegadores-sincronização-para-alterações](using-browserlink/_static/browsers-sync-to-changes.png)

O link do navegador também funciona com navegadores que você inicia de fora do Visual Studio e navega até a URL do aplicativo.

### <a name="the-browser-link-dashboard"></a>O painel do link do navegador

Abra o painel de link do navegador no menu suspenso link do navegador para gerenciar a conexão com navegadores abertos:

![Open-browserslink-Dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Se nenhum navegador estiver conectado, você poderá iniciar uma sessão de não depuração selecionando o link *Exibir no navegador* :

![browserlink-Dashboard-no-Connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Caso contrário, os navegadores conectados são mostrados com o caminho para a página que cada navegador está mostrando:

![browserlink-Dashboard-Two-Connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Se desejar, você pode clicar em um nome de navegador listado para atualizar esse navegador único.

### <a name="enable-or-disable-browser-link"></a>Habilitar ou desabilitar o link do navegador

Ao reabilitar o link do navegador depois de desabilitá-lo, você deve atualizar os navegadores para reconectá-los.

### <a name="enable-or-disable-css-auto-sync"></a>Habilitar ou desabilitar a sincronização automática de CSS

Quando a sincronização automática de CSS está habilitada, os navegadores conectados são atualizados automaticamente quando você faz qualquer alteração em arquivos CSS.

## <a name="how-it-works"></a>Como funciona

O link do navegador usa SignalR para criar um canal de comunicação entre o Visual Studio e o navegador. Quando o link do navegador está habilitado, o Visual Studio atua como um SignalR servidor ao qual vários clientes (navegadores) podem se conectar. O link do navegador também registra um componente de middleware no pipeline de solicitação de ASP.NET Core. Esse componente injeta referências especiais de `<script>` em cada solicitação de página do servidor. Você pode ver as referências de script selecionando **Exibir origem** no navegador e rolando até o final do `<body>` conteúdo da marca:

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
