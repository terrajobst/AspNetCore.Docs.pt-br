---
title: Depurar ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 5dbc900ab68682068a7f9e3ffdaabef89a0c7798
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306486"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a>Depurar ASP.NET Core Blazor Webassembly

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor aplicativos Webassembly podem ser depurados usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (borda/Chrome).  Como alternativa, você pode depurar seu aplicativo usando o Visual Studio ou Visual Studio Code.

Os cenários disponíveis incluem:

* Definir e remover pontos de interrupção.
* Execute o aplicativo com suporte para depuração no Visual Studio e Visual Studio Code (suporte a<kbd>F5</kbd> ).
* Etapa única (<kbd>F10</kbd>) por meio do código.
* Retome a execução de código com <kbd>F8</kbd> em um navegador ou <kbd>F5</kbd> no Visual Studio ou Visual Studio Code.
* Na exibição *locais* , observe os valores de variáveis locais.
* Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.

Por enquanto, você *não pode*:

* Inspecione as matrizes.
* Focalize para inspecionar Membros.
* Depuração de etapa dentro ou fora de código gerenciado.
* Ter suporte completo para inspecionar tipos de valor.
* Interromper em exceções sem tratamento.
* Pontos de interrupção de acesso durante a inicialização do aplicativo.
* Depurar um aplicativo com um trabalho de serviço.

Continuaremos a melhorar a experiência de depuração em versões futuras.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

A depuração requer um dos seguintes navegadores:

* Microsoft Edge (versão 80 ou posterior)
* Google Chrome (versão 70 ou posterior)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Habilitar depuração para Visual Studio e Visual Studio Code

A depuração é habilitada automaticamente para novos projetos que são criados usando o modelo de projeto do ASP.NET Core 3,2 Preview 3 ou posterior Blazor Webassembly.

Para habilitar a depuração para um aplicativo Webassembly Blazor existente, atualize o arquivo *launchSettings. JSON* no projeto de inicialização para incluir a seguinte Propriedade `inspectUri` em cada perfil de inicialização:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Depois de atualizado, o arquivo *launchSettings. JSON* deve ser semelhante ao exemplo a seguir:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

A propriedade `inspectUri`:

* Permite que o IDE detecte que o aplicativo é um aplicativo Webassembly Blazor.
* Instrui a infraestrutura de depuração de script para se conectar ao navegador por meio do proxy de depuração do Blazor.

## <a name="visual-studio"></a>{1&gt;Visual Studio&lt;1}

Para depurar um aplicativo Webassembly Blazor no Visual Studio:

1. Verifique se você [instalou a versão de visualização mais recente do Visual Studio 2019 16,6](https://visualstudio.com/preview) (visualização 2 ou posterior).
1. Crie um novo aplicativo ASP.NET Core hospedado Blazor Webassembly.
1. Pressione <kbd>F5</kbd> para executar o aplicativo no depurador.
1. Defina um ponto de interrupção no *Counter. Razor* no método `IncrementCount`.
1. Navegue até a guia **contador** e selecione o botão para atingir o ponto de interrupção:

   ![Contador de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Confira o valor do campo `currentCount` na janela locais:

   ![Exibir locais](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Pressione <kbd>F5</kbd> para continuar a execução.

Ao depurar seu aplicativo Webassembly Blazor, você também pode depurar o código do servidor:

1. Defina um ponto de interrupção na página *FetchData. Razor* em `OnInitializedAsync`.
1. Defina um ponto de interrupção no `WeatherForecastController` no método de ação `Get`.
1. Navegue até a guia **buscar dados** para atingir o primeiro ponto de interrupção no componente `FetchData` pouco antes de emitir uma solicitação HTTP para o servidor:

   ![Depurar dados de busca](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Pressione <kbd>F5</kbd> para continuar a execução e, em seguida, pressione o ponto de interrupção no servidor no `WeatherForecastController`:

   ![Servidor de depuração](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Pressione <kbd>F5</kbd> novamente para permitir que a execução continue e veja a tabela previsão do tempo processada.

## <a name="visual-studio-code"></a>Visual Studio Code

Para depurar um aplicativo Webassembly Blazor no Visual Studio Code:
 
1. Instale a [ C# extensão](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão do [depurador do JavaScript (noturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` definido como `true`.

   ![Extensões](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Depurador de visualização JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Abra um aplicativo Webassembly Blazor existente com a depuração habilitada.

   * Se você receber a notificação a seguir que a configuração adicional é necessária para habilitar a depuração, confirme se você tem as extensões corretas instaladas e a depuração de visualização do JavaScript habilitada e recarregue a janela:

     ![Configuração adicional necessária](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Uma notificação oferece para adicionar os ativos necessários ao aplicativo para criação e depuração. Selecione **Sim**:

     ![Adicionar ativos necessários](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Iniciar o aplicativo no depurador é um processo de duas etapas:

   1 \. **Primeiro**, inicie o aplicativo usando a configuração de inicialização do **.net Core (Blazor autônomo)** .

   2 \. **Depois que o aplicativo for iniciado**, inicie o navegador usando o **assembly da Web do Blazor de depuração do .NET Core na** configuração de inicialização do Chrome (requer o Chrome). Para usar o Edge em vez do Chrome, altere o `type` da configuração de inicialização em *. vscode/Launch. JSON* de `pwa-chrome` para `pwa-edge`.

1. Defina um ponto de interrupção no método `IncrementCount` no componente `Counter` e, em seguida, selecione o botão para atingir o ponto de interrupção:

   ![Contador de depuração no VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Depurar no navegador

1. Execute uma compilação de depuração do aplicativo no ambiente de desenvolvimento.

1. Pressione <kbd>Shift</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.

1. O navegador deve ser executado com a depuração remota habilitada. Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será gerada. A página de erro contém instruções para executar o navegador com a porta de depuração aberta para que o proxy de depuração de Blazor possa se conectar ao aplicativo. *Feche todas as instâncias do navegador* e reinicie o navegador conforme instruído.

Depois que o navegador estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração abrirá uma nova guia do depurador. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net no aplicativo. Expanda cada assembly e localize os arquivos de origem *. cs*/ *. Razor* disponíveis para depuração. Defina pontos de interrupção, alterne de volta para a guia do aplicativo e os pontos de interrupção serão atingidos quando o código for executado. Depois que um ponto de interrupção é atingido, a etapa única (<kbd>F10</kbd>) por meio da execução do código ou retomar (<kbd>F8</kbd>) normalmente.

Blazor fornece um proxy de depuração que implementa o [protocolo Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede. Quando o atalho de teclado de depuração é pressionado, Blazor aponta para o Chrome DevTools no proxy. O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).

## <a name="browser-source-maps"></a>Mapas de origem do navegador

Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente. No entanto, Blazor atualmente C# não mapeia diretamente para JavaScript/WASM. Em vez disso, Blazor a interpretação de IL no navegador, portanto, os mapas de origem não são relevantes.

## <a name="troubleshoot"></a>Solução de problemas

Se você estiver executando erros, a dica a seguir pode ajudar:

Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador. No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.
