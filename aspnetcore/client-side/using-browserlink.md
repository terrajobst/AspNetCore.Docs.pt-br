---
title: Link do navegador no ASP.NET Core
author: ncarandini
description: Explica como o Link do navegador é um recurso do Visual Studio que vincula o ambiente de desenvolvimento com um ou mais navegadores da web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/22/2017
uid: client-side/using-browserlink
ms.openlocfilehash: 452ba5149563c186750466f471c7b950f0017614
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894703"
---
# <a name="browser-link-in-aspnet-core"></a>Link do navegador no ASP.NET Core

Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), e [Tom Dykstra](https://github.com/tdykstra)

Link do navegador é um recurso no Visual Studio que cria um canal de comunicação entre o ambiente de desenvolvimento e um ou mais navegadores da web. Você pode usar o Link do navegador para atualizar seu aplicativo web em vários navegadores ao mesmo tempo, que é útil para testes entre navegadores.

## <a name="browser-link-setup"></a>Configuração do Link de navegador

::: moniker range=">= aspnetcore-2.1"

Ao converter um projeto do ASP.NET Core 2.0 para ASP.NET Core 2.1 e fazer a transição para o [metapacote do Microsoft](xref:fundamentals/metapackage-app), instale o [browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) do pacote para Funcionalidade BrowserLink. Usam os modelos de projeto do ASP.NET Core 2.1 a `Microsoft.AspNetCore.App` metapacote por padrão.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

O ASP.NET Core 2.0 **aplicativo Web**, **vazia**, e **API da Web** uso de modelos de projeto a [Microsoft.AspNetCore.All metapacote](xref:fundamentals/metapackage) , que contém uma referência de pacote para [browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Portanto, usando o `Microsoft.AspNetCore.All` metapacote não requer nenhuma ação adicional para disponibilizar o Link do navegador para uso.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

O ASP.NET Core 1.x **aplicativo Web** modelo de projeto possui uma referência de pacote para o [browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pacote. O **vazio** ou **API da Web** exigem que você adicione uma referência de pacote para projetos de modelo `Microsoft.VisualStudio.Web.BrowserLink`.

Como esse é um recurso do Visual Studio, a maneira mais fácil para adicionar o pacote para um **vazio** ou **API da Web** projeto modelo é abrir o **Package Manager Console** (**Modo de exibição** > **Other Windows** > **Package Manager Console**) e execute o seguinte comando:

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

Como alternativa, você pode usar **Gerenciador de pacotes NuGet**. Clique com botão direito no nome do projeto no **Gerenciador de soluções** e escolha **Manage NuGet Packages**:

![Gerenciador de pacotes NuGet aberto](using-browserlink/_static/open-nuget-package-manager.png)

Localizar e instalar o pacote:

![Adicionar o pacote com o Gerenciador de pacotes do NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a>Configuração

No método `Startup.Configure`:

```csharp
app.UseBrowserLink();
```

Normalmente, o código está dentro de um `if` bloco que apenas permite que o Link do navegador no ambiente de desenvolvimento, como mostrado aqui:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Para obter mais informações, veja [Usar vários ambientes](xref:fundamentals/environments).

## <a name="how-to-use-browser-link"></a>Como usar o Link do navegador

Quando um projeto do ASP.NET Core está aberto, o Visual Studio mostra o controle de barra de ferramentas do Link do navegador ao lado do controle de barra de ferramentas do **Destino de Depuração**:

![Menu de lista suspensa de Link do navegador](using-browserlink/_static/browserLink-dropdown-menu.png)

O controle de barra de ferramentas do Link do navegador, você pode:

* Atualizar o aplicativo Web em vários navegadores de uma vez.
* Abrir o **Painel de link do navegador**.
* Habilitar ou desabilitar **Link do navegador**. Observação: Link do navegador está desabilitado por padrão no Visual Studio 2017 (15.3).
* Habilitar ou desabilitar [sincronização automática de CSS](#enable-or-disable-css-auto-sync).

> [!NOTE]
> Alguns plug-ins do Visual Studio, mais notavelmente *Web extensão Pack 2015* e *2017 de pacote de extensão do Web*, oferecem funcionalidade estendida para o Link do navegador, mas alguns dos recursos adicionais não funcionam com o ASP. Projetos do .NET Core.

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Atualizar o aplicativo web em vários navegadores ao mesmo tempo

Para escolher um único navegador Web para abrir ao iniciar o projeto, use o menu suspenso do controle de barra de ferramentas do **Destino de depuração**:

![Menu suspenso de F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Para abrir vários navegadores, ao mesmo tempo, escolha **procurar com...**  da mesma lista suspensa. Mantenha pressionada a tecla CTRL para selecionar os navegadores que você deseja e, em seguida, clique em **procurar**:

![Abrir muitos navegadores ao mesmo tempo](using-browserlink/_static/open-many-browsers-at-once.png)

Aqui está uma captura de tela mostrando o Visual Studio com o modo de exibição de índice aberto e dois navegadores abertas:

![Sincronizar com o exemplo de dois navegadores](using-browserlink/_static/sync-with-two-browsers-example.png)

Passe o mouse sobre o controle de barra de ferramentas do Link do navegador para ver os navegadores que estão conectados ao projeto:

![Passe o mouse de dica](using-browserlink/_static/hoover-tip.png)

Altere o modo de exibição de índice e todos os navegadores conectados são atualizados quando você clicar no botão de atualização de Link do navegador:

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Link do navegador também funciona com navegadores que você inicie de fora do Visual Studio e navegue até a URL do aplicativo.

### <a name="the-browser-link-dashboard"></a>O painel de Link do navegador

Abra o painel de Link do navegador de menu para gerenciar a conexão com o navegador abertas suspenso Link do navegador:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Se nenhum navegador estiver conectado, você pode iniciar uma sessão de depuração não, selecionando Se nenhum navegador estiver conectado, você poderá iniciar uma sessão de não depuração selecionando o link *exibir no navegador*:

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Caso contrário, os navegadores conectados são mostrados com o caminho para a página que mostra cada navegador:

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Se desejar, você pode clicar em um nome de navegador listados para atualizar esse único navegador.

### <a name="enable-or-disable-browser-link"></a>Habilitar ou desabilitar o Link do navegador

Quando você habilita novamente o Link do navegador depois de desabilitá-lo, você deve atualizar os navegadores para reconectar-se-los.

### <a name="enable-or-disable-css-auto-sync"></a>Habilitar ou desabilitar a sincronização automática de CSS

Quando a sincronização automática de CSS está habilitada, os navegadores conectados serão atualizados automaticamente quando você fizer qualquer alteração em arquivos CSS.

## <a name="how-it-works"></a>Como ele funciona

Link do navegador usa o SignalR para criar um canal de comunicação entre o Visual Studio e o navegador. Quando o Link do navegador está habilitado, o Visual Studio atua como um servidor de SignalR que vários clientes (navegadores) podem se conectar ao. Link do navegador também registra um componente de middleware no pipeline de solicitação do ASP.NET Core. Esse componente injeta especial `<script>` referências em cada solicitação de página do servidor. Você pode ver as referências de script selecionando **Exibir código-fonte** no navegador e rolar até o final do `<body>` conteúdo de marca:

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

Como o código do navegador é todo JavaScript, ele funciona em todos os navegadores SignalR dá suporte a sem a necessidade de um plug-in de navegador.
