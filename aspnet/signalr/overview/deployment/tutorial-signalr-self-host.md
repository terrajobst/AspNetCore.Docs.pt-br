---
uid: signalr/overview/deployment/tutorial-signalr-self-host
title: 'Tutorial: Auto-hospedar SignalR | Microsoft Docs'
author: bradygaster
description: Este tutorial mostra como criar um servidor de SignalR 2 auto-hospedado e como conectá-lo com um cliente JavaScript. Versões de software usadas no tutorial V...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 400db427-27af-4f2f-abf0-5486d5e024b5
msc.legacyurl: /signalr/overview/deployment/tutorial-signalr-self-host
msc.type: authoredcontent
ms.openlocfilehash: 6a6359d59a4b715e13fe2bbcef57da6d6d6294b5
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835746"
---
<a name="tutorial-signalr-self-host"></a>Tutorial: Auto-hospedar SignalR
====================
por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Baixe o projeto concluído](http://code.msdn.microsoft.com/SignalR-Self-Host-Sample-6da0f383)

> Este tutorial mostra como criar um servidor de SignalR 2 auto-hospedado e como conectá-lo com um cliente JavaScript.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Versão 2 do SignalR
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


## <a name="overview"></a>Visão geral

Geralmente, um servidor do SignalR está hospedado em um aplicativo ASP.NET no IIS, mas também pode ser auto-hospedado (como em um aplicativo de console ou o serviço do Windows) usando a biblioteca de hospedar internamente. Essa biblioteca, como todas as 2 SignalR, baseia-se em OWIN ([Open Web Interface para .NET](http://owin.org)). OWIN define uma abstração entre servidores de web do .NET e aplicativos da web. OWIN separa o aplicativo web do servidor, o que torna o OWIN ideal para auto-hospedagem em um aplicativo web em seu próprio processo, fora do IIS.

Motivos para não hospedar no IIS:

- Ambientes em que o IIS não está disponível ou desejáveis, como um farm de servidores existente sem o IIS.
- A sobrecarga de desempenho do IIS deve ser evitado.
- Funcionalidade do SignalR deve ser adicionado a um aplicativo de instância que é executado em um serviço do Windows, a função de trabalho do Azure ou outro processo.

Se uma solução está sendo desenvolvida como hospedar internamente por motivos de desempenho, recomenda-se também teste o aplicativo hospedado no IIS para determinar o benefício de desempenho.

Este tutorial contém as seções a seguir:

- [Criar o servidor](#server)
- [Acessando o servidor com um cliente JavaScript](#js)

<a id="server"></a>

## <a name="creating-the-server"></a>Criar o servidor

Neste tutorial, você criará um servidor que está hospedado em um aplicativo de console, mas o servidor pode ser hospedado em qualquer tipo de processo, como um serviço do Windows ou uma função de trabalho do Azure. Para o código de exemplo para hospedar um servidor do SignalR em um serviço do Windows, consulte [Self-Hosting SignalR em um serviço Windows](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).

1. Abra o Visual Studio 2013 com privilégios de administrador. Selecione **arquivo**, **novo projeto**. Selecione **Windows** sob o **Visual c#** nó no **modelos** painel e selecione o **aplicativo de Console** modelo. Nomeie o novo projeto "SignalRSelfHost" e clique em **Okey**.

    ![](tutorial-signalr-self-host/_static/image1.png)
2. Abra o console de Gerenciador de pacotes do NuGet, selecionando **ferramentas** > **Gerenciador de pacotes NuGet** > **Package Manager Console**.
3. No console do Gerenciador de pacotes, digite o seguinte comando:

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample1.ps1)]

    Este comando adiciona as bibliotecas do SignalR 2 Self ao projeto.
4. No console do Gerenciador de pacotes, digite o seguinte comando:

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample2.ps1)]

    Este comando adiciona a biblioteca owin ao projeto. Essa biblioteca será usada para suporte de domínio cruzado, que é necessário para aplicativos que hospedam o SignalR e um cliente da página da web em domínios diferentes. Uma vez que você irá hospedando o servidor do SignalR e o cliente da web em portas diferentes, isso significa que esse domínio cruzado deve ser habilitado para a comunicação entre esses componentes.
5. Substitua o conteúdo de Program.cs pelo código a seguir.

    [!code-csharp[Main](tutorial-signalr-self-host/samples/sample3.cs)]

    O código acima inclui três classes:

    - **Programa**, incluindo o **Main** definindo o caminho principal de execução do método. Nesse método, um aplicativo web do tipo **inicialização** é iniciado na URL especificada (`http://localhost:8080`). Se segurança for necessária no ponto de extremidade, o SSL pode ser implementado. Confira [Como Configurar uma porta com um certificado SSL](https://msdn.microsoft.com/library/ms733791.aspx) para obter mais informações.
    - **Inicialização**, a classe que contém a configuração para o servidor do SignalR (a única configuração que usa este tutorial é a chamada para `UseCors`) e a chamada para `MapSignalR`, que cria as rotas para todos os objetos Hub no projeto.
    - **MyHub**, a classe SignalR Hub que o aplicativo será fornecer aos clientes. Essa classe tem um único método, **enviar**, que os clientes chamarão para difundir uma mensagem para todos os outros clientes conectados.
6. Compile e execute o aplicativo. O endereço que o servidor está em execução deve mostrar em uma janela do console.

    ![](tutorial-signalr-self-host/_static/image2.png)
7. Se a execução falhará com a exceção `System.Reflection.TargetInvocationException was unhandled`, você precisará reiniciar o Visual Studio com privilégios de administrador.
8. Pare o aplicativo antes de prosseguir para a próxima seção.

<a id="js"></a>

## <a name="accessing-the-server-with-a-javascript-client"></a>Acessando o servidor com um cliente JavaScript

Nesta seção, você usará o mesmo cliente JavaScript do [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md). Podemos apenas fazer uma modificação para o cliente, que é definir explicitamente a URL do hub. Com um aplicativo hospedado internamente, o servidor pode não ser necessariamente o mesmo endereço que a URL de conexão (devido a proxies reversos e balanceadores de carga), portanto, a URL precisa ser definida explicitamente.

1. Na **Gerenciador de soluções**, clique com botão direito na solução e selecione **Add**, **novo projeto**. Selecione o **Web** nó e selecione o **aplicativo Web ASP.NET** modelo. Nomeie o projeto "JavascriptClient" e clique em **Okey**.

    ![](tutorial-signalr-self-host/_static/image3.png)
2. Selecione o **vazio** modelo e deixe as opções restantes não selecionadas. Selecione **criar projeto**.

    ![](tutorial-signalr-self-host/_static/image4.png)
3. No console do Gerenciador de pacotes, selecione o projeto de "JavascriptClient" na **projeto padrão** lista suspensa e execute o seguinte comando:

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample4.ps1)]

    Esse comando instala as bibliotecas de SignalR e o JQuery que você precisará no cliente.
4. Clique com botão direito no seu projeto e selecione **Add**, **Novo Item**. Selecione o **Web** nó e selecionar HTML Page. Nomeie a página **default. HTML**.

    ![](tutorial-signalr-self-host/_static/image5.png)
5. Substitua o conteúdo da nova página HTML com o código a seguir. Verifique se as referências de script correspondem os scripts na pasta Scripts do projeto.

    [!code-html[Main](tutorial-signalr-self-host/samples/sample5.html?highlight=31-32)]

    O código a seguir (realçado em exemplo de código acima) é a adição feitas para o cliente usado no tutorial de Introdução ao uso (além de atualizar o código para o SignalR versão 2 beta). Esta linha de código define explicitamente a URL de conexão base para o SignalR no servidor.

    [!code-javascript[Main](tutorial-signalr-self-host/samples/sample6.js)]
6. Clique com botão direito na solução e, em seguida, selecione **definir projetos de inicialização...** . Selecione o **vários projetos de inicialização** botão de opção e, em seguida, defina ambos os projetos **ação** para **iniciar**.

    ![](tutorial-signalr-self-host/_static/image6.png)
7. Clique com botão direito em "Default" e selecione **definir como página inicial**.
8. Execute o aplicativo. O servidor e a página serão iniciado. Talvez seja necessário recarregar a página da web (ou selecione **continuar** no depurador) se a página for carregada antes que o servidor é iniciado.
9. No navegador, forneça um nome de usuário quando solicitado. Copie a URL da página para outra janela ou guia do navegador e forneça um nome de usuário diferente. Você poderá enviar mensagens de navegador de um painel para outro, como no tutorial de Introdução.
