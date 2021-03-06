---
title: Hospedar e implantar ASP.NET Core Blazor
author: guardrex
description: Descubra como hospedar e implantar aplicativos Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434259"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hospedar e implantar o ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publique o aplicativo

Os aplicativos são publicados para implantação na configuração de versão.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Selecione **Versão** > **Publicar {APLICATIVO}** na barra de navegação.
1. Selecione o botão *destino de publicação*. Para publicar localmente, selecione **Pasta**.
1. Aceite o local padrão no campo **Escolher uma pasta** ou especifique um local diferente. Selecione o botão **Publicar**.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Use o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) para publicar o aplicativo com uma configuração de versão:

```dotnetcli
dotnet publish -c Release
```

---

Publicar o aplicativo dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação. Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.

Locais de publicação:

* Blazor Webassembly
  * &ndash; autônomo, o aplicativo é publicado na pasta */bin/Release/{Target Framework}/Publish/wwwroot* . Para implantar o aplicativo como um site estático, copie o conteúdo da pasta *wwwroot* para o host do site estático.
  * Hospedado &ndash; o aplicativo Webassembly do cliente Blazor é publicado na pasta */bin/Release/{Target Framework}/Publish/wwwroot* do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo do servidor. Implante o conteúdo da pasta de *publicação* no host.
* Blazor Server &ndash; o aplicativo é publicado na pasta */Publish do/bin/Release/{Target Framework}* . Implante o conteúdo da pasta de *publicação* no host.

Os ativos na pasta são implantados no servidor Web. A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.

## <a name="app-base-path"></a>Caminho base do aplicativo

O *caminho base do aplicativo* é o caminho da URL raiz do aplicativo. Considere o seguinte aplicativo ASP.NET Core e Blazor subaplicativo:

* O aplicativo ASP.NET Core é nomeado `MyApp`:
  * O aplicativo reside fisicamente em *d:/MyApp*.
  * As solicitações são recebidas em `https://www.contoso.com/{MYAPP RESOURCE}`.
* Um aplicativo Blazor chamado `CoolApp` é um subaplicativo do `MyApp`:
  * O subaplicativo reside fisicamente em *d:/MyApp/CoolApp*.
  * As solicitações são recebidas em `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Sem especificar configuração adicional para `CoolApp`, o subaplicativo nesse cenário não tem conhecimento de onde ele reside no servidor. Por exemplo, o aplicativo não pode construir URLs relativas corretas para seus recursos sem saber que ela reside no caminho de URL relativo `/CoolApp/`.

Para fornecer a configuração para o caminho base do aplicativo Blazor do `https://www.contoso.com/CoolApp/`, o atributo `href` da marca `<base>` é definido como o caminho raiz relativo no arquivo *pages/_Host. cshtml* (Blazor Server) ou no arquivo *wwwroot/index.html* (Blazor Webassembly):

```html
<base href="/CoolApp/">
```

os aplicativos do Blazor Server também definem o caminho base do servidor chamando <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> no pipeline de solicitação do aplicativo de `Startup.Configure`:

```csharp
app.UsePathBase("/CoolApp");
```

Ao fornecer o caminho de URL relativo, um componente que não está no diretório raiz pode construir URLs relativas ao caminho raiz do aplicativo. Os componentes em diferentes níveis da estrutura de diretório podem criar links para outros recursos em locais em todo o aplicativo. O caminho base do aplicativo também é usado para interceptar hiperlinks selecionados onde o destino `href` do link está dentro do espaço URI de caminho base do aplicativo. O roteador de Blazor lida com a navegação interna.

Em muitos cenários de hospedagem, o caminho de URL relativo para o aplicativo é a raiz do aplicativo. Nesses casos, o caminho base da URL relativa do aplicativo é uma barra (`<base href="/" />`), que é a configuração padrão para um aplicativo Blazor. Em outros cenários de hospedagem, como páginas do GitHub e subaplicativos do IIS, o caminho base do aplicativo deve ser definido como o caminho de URL relativo do servidor do aplicativo.

Para definir o caminho base do aplicativo, atualize a marca de `<base>` dentro dos elementos de marca `<head>` do arquivo *pages/_Host. cshtml* (Blazor Server) ou *wwwroot/index.html* File (Blazor Webassembly). Defina o valor do atributo `href` como `/{RELATIVE URL PATH}/` (a barra à direita é necessária), em que `{RELATIVE URL PATH}` é o caminho de URL relativo completo do aplicativo.

Para um aplicativo Webassembly Blazor com um caminho de URL não raiz relativo (por exemplo, `<base href="/CoolApp/">`), o aplicativo não consegue localizar seus recursos *quando executado localmente*. Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no runtime. Não inclua uma barra à direita. Para passar o argumento de base Path ao executar o aplicativo localmente, execute o comando `dotnet run` no diretório do aplicativo com a opção `--pathbase`:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Para um aplicativo Webassembly Blazor com um caminho de URL relativo de `/CoolApp/` (`<base href="/CoolApp/">`), o comando é:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

O aplicativo Webassembly Blazor responde localmente em `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Implantação

Confira orientações de implantação nos tópicos a seguir:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
