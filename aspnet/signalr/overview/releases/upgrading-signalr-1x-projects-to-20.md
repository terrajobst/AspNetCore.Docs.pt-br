---
uid: signalr/overview/releases/upgrading-signalr-1x-projects-to-20
title: Atualizando projetos do SignalR 1.x para a versão 2 | Microsoft Docs
author: pfletcher
description: Este tópico descreve como atualizar um projeto existente do SignalR 1.x para o SignalR 2. x e como solucionar problemas que podem surgir durante o processo de atualização...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: adcfef99-9bc5-489d-a91b-9b7c2bc35e04
msc.legacyurl: /signalr/overview/releases/upgrading-signalr-1x-projects-to-20
msc.type: authoredcontent
ms.openlocfilehash: 23ea23585b15395cf86bdad13885af32d1b64e79
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286838"
---
<a name="upgrading-signalr-1x-projects-to-version-2"></a>Atualizando projetos do SignalR 1.x para a versão 2
====================
por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tópico descreve como atualizar um projeto existente do SignalR 1.x para o SignalR 2. x e como solucionar problemas que podem surgir durante o processo de atualização.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Versões 1 e 2 do SignalR
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a>Usando o Visual Studio 2012 com este tutorial
>
>
> Para usar o Visual Studio 2012 com este tutorial, faça o seguinte:
>
> - Atualização de seu [Gerenciador de pacotes](http://docs.nuget.org/docs/start-here/installing-nuget) para a versão mais recente.
> - Instalar o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
> - No Web Platform Installer, procure e instale **ASP.NET e Web Tools 2013.1 para Visual Studio 2012**. Isso irá instalar os modelos do Visual Studio para classes do SignalR, como **Hub**.
> - Alguns modelos (como **classe de inicialização OWIN**) não está disponível; nesses casos, use um arquivo de classe em vez disso.
>
>
> ## <a name="questions-and-comments"></a>Perguntas e comentários
>
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


SignalR 2 oferece uma experiência de desenvolvimento consistente entre as plataformas de servidor usando [OWIN](http://owin.org). Este artigo descreve as etapas que são necessárias para atualizar um aplicativo do 1.x SignalR para a versão 2.

Enquanto ela é incentivada a atualizar aplicativos para o SignalR 2, o SignalR 1.x ainda ter suporte.

Este tutorial descreve como atualizar um aplicativo hospedado na web para o SignalR 2. Agora há suporte para aplicativos auto-hospedados (aqueles que hospedam um servidor em um aplicativo de console, o serviço do Windows ou outro processo) em SignalR 2. Para obter informações sobre como começar a criar um aplicativo hospedado internamente com SignalR 2, consulte [Tutorial: Auto-hospedar SignalR](../deployment/tutorial-signalr-self-host.md).

## <a name="contents"></a>Conteúdo

As seções a seguir descrevem as tarefas envolvidas com a atualização de projetos do SignalR e como solucionar problemas que podem surgir.

- [Exemplo: Atualizando o tutorial de Introdução ao SignalR 2](#example)
- [Solução de problemas de erros encontrados durante a atualização](#troubleshooting)

<a id="example"></a>

## <a name="example-upgrading-the-getting-started-tutorial-application-to-signalr-2"></a>Exemplo: Atualizar o aplicativo de tutorial de Introdução ao SignalR 2

Nesta seção, você atualizará o aplicativo criado na [versão do SignalR 1.x do Tutorial de Introdução](../older-versions/index.md) para usar o SignalR 2.

1. Depois que você concluiu o tutorial de Introdução, clique com botão direito no projeto e selecione **propriedades**. Verifique se que o **estrutura de destino** é definido como **.NET Framework 4.5.**
2. Abra o Console do Gerenciador de pacotes. Remover o SignalR 1.x do projeto usando o comando a seguir:

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample1.ps1)]
3. Instale o SignalR 2 usando o seguinte comando:

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample2.ps1)]
4. Na página HTML, atualize a referência de script para o SignalR corresponder à versão do script agora está incluído no projeto.

    [!code-html[Main](upgrading-signalr-1x-projects-to-20/samples/sample3.html)]
5. Na classe de aplicativo global, remova a chamada para MapHubs.

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample4.cs)]
6. A solução com o botão direito e selecione **Add**, **Novo Item...** . Na caixa de diálogo, selecione **classe de inicialização Owin**. Nomeie a nova classe **Startup.cs**.

    ![](upgrading-signalr-1x-projects-to-20/_static/image1.png)
7. Substitua o conteúdo do Startup.cs com o código a seguir:

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample5.cs)]

    O atributo de assembly adiciona a classe ao processo de inicialização do Owin, que executa o `Configuration` método quando Owin é iniciado. Isso por sua vez chama o `MapSignalR` método, que cria as rotas para todos os hubs de SignalR no aplicativo.
8. Execute o projeto e copie a URL da página principal em outro navegador ou no painel do navegador, como antes. Cada página solicitará um nome de usuário e as mensagens enviadas de cada página devem estar visíveis em ambos os painéis do navegador.

<a id="troubleshooting"></a>

## <a name="troubleshooting-errors-encountered-during-upgrading"></a>Solução de problemas de erros encontrados durante a atualização

Esta seção descreve problemas que podem surgir durante a atualização. Para obter uma lista mais abrangente de erros e problemas que podem ocorrer com um aplicativo do SignalR, consulte [solução de problemas do SignalR](../testing-and-debugging/troubleshooting.md).

### <a name="the-call-is-ambiguous-between-the-following-methods-or-properties"></a>'A chamada é ambígua entre os seguintes métodos ou propriedades'

Esse erro ocorrerá se uma referência a `Microsoft.AspNet.SignalR.Owin` não é removido. Esse pacote foi preterido; a referência deve ser removida e a versão 1.x do pacote SelfHost deve ser desinstalada.

### <a name="hub-methods-fail-silently"></a>Métodos de Hub falharem em modo silencioso

Verifique se as referências de script no seu cliente estão atualizadas e que o `OwinStartup` de atributo para a sua classe de inicialização tem a classe correta e nomes de assembly para o seu projeto. Além disso, tente abrir o endereço de hubs (hubs do signalr /) no seu navegador. qualquer erro que aparece se oferecerá para obter mais informações sobre o que está errado.
