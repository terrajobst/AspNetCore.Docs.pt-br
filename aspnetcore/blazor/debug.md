---
title: Depurar ASP.NET Core Blazor
author: guardrex
description: Saiba como depurar Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159983"
---
# <a name="debug-aspnet-core-opno-locblazor"></a>Depurar ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Há suporte *antecipado* para depuração Blazor Webassembly usando as ferramentas de desenvolvimento do navegador em navegadores baseados em Chromium (Chrome/Edge). O trabalho está em andamento para:

* Habilite totalmente a depuração no Visual Studio.
* Habilite a depuração no Visual Studio Code.

Os recursos do depurador são limitados. Os cenários disponíveis incluem:

* Definir e remover pontos de interrupção.
* Etapa única (`F10`) por meio da execução do código ou de retomada (`F8`).
* Na exibição *locais* , observe os valores de quaisquer variáveis locais do tipo `int`, `string`e `bool`.
* Consulte a pilha de chamadas, incluindo cadeias de chamada que vão do JavaScript para o .NET e do .NET para o JavaScript.

Você *não pode*:

* Observe os valores de qualquer local que não seja um `int`, `string`ou `bool`.
* Observe os valores de quaisquer propriedades ou campos de classe.
* Passe o mouse sobre variáveis para ver seus valores.
* Avalie as expressões no console.
* Passe pelas chamadas assíncronas.
* Execute a maioria dos outros cenários de depuração comuns.

O desenvolvimento de mais cenários de depuração é um foco contínuo da equipe de engenharia.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

A depuração requer um dos seguintes navegadores:

* Google Chrome (versão 70 ou posterior)
* Visualização do Microsoft Edge ([canal de desenvolvimento de borda](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>Procedimento

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> O suporte à depuração no Visual Studio está em um estágio inicial do desenvolvimento. Não há suporte para a depuração **F5** no momento.

1. Execute um aplicativo Webassembly Blazor no `Debug` configuração sem depuração (**Ctrl**+**F5** em vez de **F5**).
1. Abra as propriedades de depuração do aplicativo (última entrada no menu **depurar** ) e copie a URL do **aplicativo**http. Navegue até o endereço HTTP (não o endereço HTTPS) do aplicativo usando um navegador baseado em Chromium (Edge beta ou Chrome).
1. Coloque o foco do teclado no aplicativo na janela do navegador, não no painel Ferramentas do desenvolvedor. É melhor manter o painel Ferramentas do desenvolvedor fechado para este procedimento. Depois que a depuração for iniciada, você poderá abrir novamente o painel Ferramentas de desenvolvedor.
1. Selecione o seguinte atalho de teclado específico do Blazor:

   * `Shift+Alt+D` no Windows
   * `Shift+Cmd+D` no macOS

   Se você receber a **guia não é possível localizar o navegador depurável**, consulte [Habilitar depuração remota](#enable-remote-debugging).
   
   Depois de habilitar a depuração remota:
   
   1 \. Uma nova janela do navegador abre. Feche a janela anterior.

   2 \. Coloque o foco do teclado no aplicativo na janela do navegador.

   3 \. Selecione o atalho de teclado específico do Blazorna nova janela do navegador: `Shift+Alt+D` no Windows ou `Shift+Cmd+D` no macOS.

   4 \. A guia **devtools** é aberta no navegador. **Selecione novamente a guia do aplicativo na janela do navegador.**

   Para anexar o aplicativo ao Visual Studio, consulte a seção [anexar ao processo no Visual Studio](#attach-to-process-in-visual-studio) .

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Execute um aplicativo Webassembly Blazor na configuração do `Debug` passando a opção `--configuration Debug` para o comando [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.
1. Navegue até o aplicativo na URL HTTP mostrada na janela do Shell.
1. Coloque o foco do teclado no aplicativo, não no painel Ferramentas do desenvolvedor. É melhor manter o painel Ferramentas do desenvolvedor fechado para este procedimento. Depois que a depuração for iniciada, você poderá abrir novamente o painel Ferramentas de desenvolvedor.
1. Selecione o seguinte atalho de teclado específico do Blazor:

   * `Shift+Alt+D` no Windows
   * `Shift+Cmd+D` no macOS

   Se você receber a **guia não é possível localizar o navegador depurável**, consulte [Habilitar depuração remota](#enable-remote-debugging).
   
   Depois de habilitar a depuração remota:
   
   1 \. Uma nova janela do navegador abre. Feche a janela anterior.

   2 \. Coloque o foco do teclado no aplicativo na janela do navegador, não no painel Ferramentas do desenvolvedor.

   3 \. Selecione o atalho de teclado específico do Blazorna nova janela do navegador: `Shift+Alt+D` no Windows ou `Shift+Cmd+D` no macOS.

---

## <a name="enable-remote-debugging"></a>Habilitar depuração remota

Se a depuração remota estiver desabilitada, uma página de erro **não é possível localizar a guia do navegador depurável** será gerada pelo Chrome. A página de erro contém instruções para executar o Chrome com a porta de depuração aberta para que o proxy de depuração de Blazor possa se conectar ao aplicativo. *Feche todas as instâncias do Chrome* e reinicie o Chrome conforme instruído.

## <a name="debug-the-app"></a>Depurar o aplicativo

Depois que o Chrome estiver em execução com a depuração remota habilitada, o atalho de teclado de depuração abrirá uma nova guia do depurador. Após alguns instantes, a guia **fontes** mostra uma lista dos assemblies .net no aplicativo. Expanda cada assembly e localize os arquivos de origem *. cs*/ *. Razor* disponíveis para depuração. Defina pontos de interrupção, alterne de volta para a guia do aplicativo e os pontos de interrupção serão atingidos quando o código for executado. Depois que um ponto de interrupção é atingido, a etapa única (`F10`) pela execução do código ou retomar (`F8`) normalmente.

Blazor fornece um proxy de depuração que implementa o [protocolo Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) e aumenta o protocolo com o. Informações específicas de rede. Quando o atalho de teclado de depuração é pressionado, Blazor aponta para o Chrome DevTools no proxy. O proxy se conecta à janela do navegador que você está procurando depurar (portanto, a necessidade de habilitar a depuração remota).

## <a name="attach-to-process-in-visual-studio"></a>Anexar ao processo no Visual Studio

Anexar ao processo do aplicativo no Visual Studio é um cenário de depuração *temporário* para Blazor Webassembly enquanto a depuração **F5** está em desenvolvimento.

Para anexar o processo do aplicativo em execução ao Visual Studio:

1. No Visual Studio, selecione **depurar** > **anexar ao processo**.
1. Para o **tipo de conexão**, selecione **WebSocket do protocolo Chrome devtools (sem autenticação)** .
1. Para o **destino de conexão**, Cole o endereço http (não o endereço https) do aplicativo.
1. Selecione **Atualizar** para atualizar as entradas em **processos disponíveis**.
1. Selecione o processo do navegador a ser depurado e selecione **anexar**.
1. Na caixa de diálogo **Selecionar tipo de código** , selecione o tipo de código para o navegador específico ao qual você está anexando (Edge ou Chrome) e, em seguida, selecione **OK**.

## <a name="browser-source-maps"></a>Mapas de origem do navegador

Os mapas de origem do navegador permitem que o navegador mapeie arquivos compilados de volta para seus arquivos de origem originais e normalmente são usados para depuração do lado do cliente. No entanto, Blazor atualmente C# não mapeia diretamente para JavaScript/WASM. Em vez disso, Blazor a interpretação de IL no navegador, portanto, os mapas de origem não são relevantes.

## <a name="troubleshoot"></a>Solução de problemas

Se você estiver executando erros, a dica a seguir pode ajudar:

Na guia **depurador** , abra as ferramentas de desenvolvedor em seu navegador. No console do, execute `localStorage.clear()` para remover qualquer ponto de interrupção.
