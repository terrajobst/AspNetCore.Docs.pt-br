---
title: Hospedar e implantar o ASP.NET Core Blazor
author: guardrex
description: Descubra como hospedar e implantar aplicativos Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5a56bbda5bb7727c7dbeaed7f2a91d0dcb6e7e71
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773597"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hospedar e implantar o ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publique o aplicativo

Os aplicativos são publicados para implantação na configuração de versão.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selecione **Versão** > **Publicar {APLICATIVO}** na barra de navegação.
1. Selecione o botão *destino de publicação*. Para publicar localmente, selecione **Pasta**.
1. Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente. Selecione o botão **Publicar**.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Use o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) para publicar o aplicativo com uma configuração de versão:

```console
dotnet publish -c Release
```

---

Publicar o aplicativo dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação. Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.

Um aplicativo Blazor do lado do cliente é publicado na pasta */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist*. Um aplicativo Blazor do lado do servidor é publicado na pasta */bin/Release/{ESTRUTURA DE DESTINO}/publish*.

Os ativos na pasta são implantados no servidor Web. A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.

## <a name="app-base-path"></a>Caminho base do aplicativo

O *caminho base do aplicativo* é o caminho da URL raiz do aplicativo. Considere o seguinte aplicativo principal e o aplicativo mais rápido:

* O aplicativo principal é chamado `MyApp`:
  * O aplicativo reside fisicamente em *\\d: MyApp*.
  * As solicitações são recebidas em `https://www.contoso.com/{MYAPP RESOURCE}`.
* Um aplicativo mais incrivelmente chamado `CoolApp` é um subaplicativo do: `MyApp`
  * O subaplicativo reside fisicamente em *d:\\MyApp\\CoolApp*.
  * As solicitações são recebidas em `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Sem especificar a configuração adicional `CoolApp`para o, o subaplicativo nesse cenário não tem conhecimento de onde ele reside no servidor. Por exemplo, o aplicativo não pode construir URLs relativas corretas para seus recursos sem saber que ela reside no caminho `/CoolApp/`de URL relativo.

Para fornecer a configuração para o `https://www.contoso.com/CoolApp/`caminho base do aplicativo mais incrivelmente, o `<base>` atributo da `href` marca é definido como o caminho raiz relativo no arquivo *wwwroot/index.html* :

```html
<base href="/CoolApp/">
```

Ao fornecer o caminho de URL relativo, um componente que não está no diretório raiz pode construir URLs relativas ao caminho raiz do aplicativo. Os componentes em diferentes níveis da estrutura de diretório podem criar links para outros recursos em locais em todo o aplicativo. O caminho base do aplicativo também é usado para interceptar cliques em hiperlink em que o destino `href` do link está dentro do espaço do URI do caminho base do aplicativo. O roteador do Blazor manipula a navegação interna.

Em muitos cenários de hospedagem, o caminho de URL relativo para o aplicativo é a raiz do aplicativo. Nesses casos, o caminho base da URL relativa do aplicativo é uma barra (`<base href="/" />`), que é a configuração padrão para um aplicativo mais incrivelmente. Em outros cenários de hospedagem, como páginas do GitHub e subaplicativos do IIS, o caminho base do aplicativo deve ser definido como o caminho da URL relativa do servidor para o aplicativo.

Para definir o caminho base do aplicativo, atualize a marca `<base>` encontrada nos elementos da marca `<head>` do arquivo *wwwroot/index.html*. Defina o `href` valor do atributo `/{RELATIVE URL PATH}/` como (a barra à direita é necessária), `{RELATIVE URL PATH}` em que é o caminho de URL relativo completo do aplicativo.

Para um aplicativo com um caminho de URL não raiz relativo (por exemplo, `<base href="/CoolApp/">`), o aplicativo não consegue localizar seus recursos *quando executado localmente*. Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no tempo de execução. Para passar o argumento de base Path ao executar o aplicativo localmente, execute `dotnet run` o comando no diretório do aplicativo com a `--pathbase` opção:

```console
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Para um aplicativo com um caminho de URL relativo `/CoolApp/` de`<base href="/CoolApp/">`(), o comando é:

```console
dotnet run --pathbase=/CoolApp
```

O aplicativo responde localmente em `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Implantação

Confira orientações de implantação nos tópicos a seguir:

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
