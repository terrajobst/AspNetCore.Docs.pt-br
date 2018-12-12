---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Tutorial: Introdução ao SignalR 2 e MVC 5 | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como usar o ASP.NET SignalR 2 para criar um aplicativo de bate-papo em tempo real. Você adicionará o SignalR para um aplicativo MVC 5 e criar uma exibição de bate-papo...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.openlocfilehash: 568f82daa67f33736c2bf7a45a3e1339f265c487
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287502"
---
<a name="tutorial-getting-started-with-signalr-2-and-mvc-5"></a>Tutorial: Introdução ao SignalR 2 e MVC 5
====================
por [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Baixe o projeto concluído](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

> Este tutorial mostra como usar o ASP.NET SignalR 2 para criar um aplicativo de bate-papo em tempo real. Você adicionará o SignalR para um aplicativo MVC 5 e criar uma exibição de bate-papo para enviar e exibir as mensagens.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - MVC 5
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
> ## <a name="tutorial-versions"></a>Versões de tutoriais
>
> Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Perguntas e comentários
>
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


## <a name="overview"></a>Visão geral

Este tutorial apresenta o desenvolvimento de aplicativos web em tempo real com o ASP.NET SignalR 2 e o ASP.NET MVC 5. O tutorial usa o mesmo código de aplicativo de bate-papo, como o [tutorial de Introdução SignalR](tutorial-getting-started-with-signalr.md), mas mostra como adicioná-lo a um aplicativo MVC 5.

Neste tópico, você aprenderá as seguintes tarefas de desenvolvimento do SignalR:

- Adicionar a biblioteca do SignalR para um aplicativo MVC 5.
- Criando hub e classes de inicialização OWIN para transferir conteúdo para clientes.
- Usando a biblioteca jQuery SignalR em uma página da web para enviar mensagens e exibir as atualizações do hub.

A captura de tela a seguir mostra o aplicativo de bate-papo concluído em execução em um navegador.

![Instâncias de bate-papo](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

Seções:

- [Configurar o projeto](#setup)
- [Executar o exemplo](#run)
- [Examinar o código](#code)
- [Próximas Etapas](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Configurar o projeto

Pré-requisitos:

- Visual Studio 2013. Se você não tiver o Visual Studio, consulte [Downloads do ASP.NET](https://www.asp.net/downloads) para obter o Visual Studio 2013 Express ferramenta de desenvolvimento gratuita.

Esta seção mostra como criar um aplicativo ASP.NET MVC 5, adicione a biblioteca SignalR e criar o aplicativo de bate-papo.

1. No Visual Studio, crie um aplicativo c# ASP.NET que tem como alvo o .NET Framework 4.5, nomeie-o SignalRChat e clique em Okey.

    ![Criar web](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)
2. No `New ASP.NET Project` caixa de diálogo e selecione **MVC**e clique em **alterar autenticação**.

    ![Criar web](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)
3. Selecione **sem autenticação** na **alterar autenticação** caixa de diálogo e clique em **Okey**.

    ![Selecione sem autenticação](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

    > [!NOTE]
    > Se você selecionar um provedor de autenticação diferentes para seu aplicativo, uma `Startup.cs` classe será criada para você, você não precisará criar seus próprios `Startup.cs` classe na etapa 10 abaixo.
4. Clique em **Okey** na **novo projeto ASP.NET** caixa de diálogo.
5. Abra o **Ferramentas > Gerenciador de pacotes NuGet > Package Manager Console** e execute o comando a seguir. Esta etapa adiciona ao projeto de um conjunto de arquivos de script e referências de assembly que habilitar a funcionalidade do SignalR.

    `install-package Microsoft.AspNet.SignalR`
6. Na **Gerenciador de soluções**, expanda a pasta de Scripts. Observe que as bibliotecas de script para o SignalR foram adicionadas ao projeto.

    ![Pasta de scripts](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)
7. Na **Gerenciador de soluções**, clique com botão direito no projeto, selecione **adicionar | Nova pasta**, e adicione uma nova pasta chamada **Hubs**.
8. Clique com botão direito do **Hubs** pasta, clique em **adicionar | Novo Item**, selecione o **Visual c# | Web | SignalR** nó o **instalado** painel, selecione **classe de Hub do SignalR (v2)** do painel central e criar um novo hub chamado **ChatHub.cs**. Você usará essa classe como um hub de servidor SignalR que envia mensagens para todos os clientes.

    ![Criar um novo hub](tutorial-getting-started-with-signalr-and-mvc/_static/image6.png)
9. Substitua o código na **ChatHub** classe pelo código a seguir.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]
10. Crie uma nova classe chamada Startup.cs. Altere o conteúdo do arquivo para o seguinte.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]
11. Editar o `HomeController` classe encontrado no **Controllers** e adicione o seguinte método à classe. Esse método retorna o **bate-papo** modo de exibição que você criará em uma etapa posterior.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]
12. Clique com botão direito do **exibições/inicial** pasta e selecione **adicionar... | Modo de exibição**.
13. No **adicionar exibição** caixa de diálogo, o nome do novo modo de exibição **bate-papo**.

    ![Adicionar uma exibição](tutorial-getting-started-with-signalr-and-mvc/_static/image7.png)
14. Substitua o conteúdo do **Chat.cshtml** com o código a seguir.

    > [!IMPORTANT]
    > Quando você adiciona o SignalR e outras bibliotecas de script ao seu projeto do Visual Studio, o Gerenciador de pacotes poderá instalar uma versão do arquivo de script SignalR é mais recente que a versão mostrada neste tópico. Certifique-se de que a referência de script em seu código corresponda à versão da biblioteca de script instalada em seu projeto.

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]
15. **Salvar tudo** para o projeto.

<a id="run"></a>

## <a name="run-the-sample"></a>Executar o exemplo

1. Pressione F5 para executar o projeto no modo de depuração.
2. Na linha de endereço do navegador, acrescente **bate-papo/home/** para a URL da página padrão para o projeto. Carrega a página de bate-papo em uma instância do navegador e solicitará um nome de usuário.

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr-and-mvc/_static/image8.png)
3. Insira um nome de usuário.
4. Copie a URL da linha de endereço do navegador e usá-lo para abrir duas ou mais instâncias de navegador. Em cada instância do navegador, insira um nome de usuário exclusivo.
5. Em cada instância do navegador, adicione um comentário e clique em **enviar**. Os comentários devem exibir em todas as instâncias do navegador.

    > [!NOTE]
    > Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor. O hub transmite seus comentários para todos os usuários atuais. Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.
6. A captura de tela a seguir mostra o aplicativo de bate-papo em execução em um navegador.

    ![Navegadores de bate-papo](tutorial-getting-started-with-signalr-and-mvc/_static/image9.png)
7. Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução. Esse nó é visível no modo de depuração, se você estiver usando o Internet Explorer como navegador. Há um arquivo de script denominado **hubs** que a biblioteca SignalR gera dinamicamente em tempo de execução. Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor. Se você usar um navegador que não seja o Internet Explorer, você também pode acessar o dynamic **hubs** arquivo navegando até ele diretamente, por exemplo http://mywebsite/signalr/hubs.

<a id="code"></a>

## <a name="examine-the-code"></a>Examinar o código

O aplicativo de chat SignalR demonstra duas tarefas de desenvolvimento básicas do SignalR: criar um hub de que o objeto de coordenação principal no servidor e usando a biblioteca jQuery SignalR para enviar e receber mensagens.

### <a name="signalr-hubs"></a>Hubs de SignalR

No exemplo de código a **ChatHub** classe deriva a **ASPNET** classe. Derivando de **Hub** classe é uma maneira útil para criar um aplicativo do SignalR. Você pode criar métodos públicos em sua classe de hub e, em seguida, acessar esses métodos chamando-as de scripts em uma página da web.

No código de bate-papo, os clientes chamam a **ChatHub.Send** método para enviar uma nova mensagem. O hub por sua vez envia a mensagem a todos os clientes chamando **Clients.All.addNewMessageToPage**.

O **enviar** método demonstra vários conceitos de hub:

- Declare métodos públicos em um hub para que os clientes possam chamá-los.
- Use o **Microsoft.AspNet.SignalR.Hub.Clients** propriedade para acessar todos os clientes conectados a esse hub.
- Chamar uma função no cliente (como o `addNewMessageToPage` função) para atualizar os clientes.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>O SignalR e jQuery

O **Chat.cshtml** arquivo de exibição no código de exemplo mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR. Criando uma referência para o proxy geradas automaticamente para o hub, declarar uma função que o servidor pode chamar para enviar conteúdo para clientes e iniciando uma conexão para enviar mensagens para o hub de tarefas fundamentais no código.

O código a seguir declara uma referência a um proxy de hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> Em JavaScript a referência para a classe de servidor e seus membros é em minúsculas concatenadas. O exemplo de código faz referência de c# **ChatHub** classe no JavaScript, enquanto **chatHub**. Se você quiser fazer referência a `ChatHub` classe no jQuery com convencional Pascal casing, como você faria no c#, edite o arquivo de classe ChatHub.cs. Adicionar um `using` instrução para fazer referência a `Microsoft.AspNet.SignalR.Hubs` namespace. Em seguida, adicione a `HubName` de atributo para o `ChatHub` classe, por exemplo `[HubName("ChatHub")]`. Por fim, atualize sua referência de jQuery para o `ChatHub` classe.


O código a seguir mostra como criar uma função de retorno de chamada no script. A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente. A chamada opcional para o `htmlEncode` função mostra uma maneira para HTML codificar o conteúdo da mensagem antes de exibi-la na página, como uma maneira de evitar a injeção de script.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

O código a seguir mostra como abrir uma conexão com o hub. O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página de bate-papo.

> [!NOTE]
> Essa abordagem garante que a conexão seja estabelecida antes de ser executado o manipulador de eventos.


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Próximas etapas

Você aprendeu que o SignalR é uma estrutura para a criação de aplicativos web em tempo real. Você também aprendeu várias tarefas de desenvolvimento do SignalR: como adicionar o SignalR para um aplicativo ASP.NET, como criar uma classe de hub e como enviar e receber mensagens do hub.

Para obter instruções sobre como implantar o aplicativo de exemplo de SignalR no Azure, consulte [usando o SignalR com aplicativos Web no serviço de aplicativo do Azure](../deployment/using-signalr-with-azure-web-sites.md). Para obter informações detalhadas sobre como implantar um projeto de web do Visual Studio para um Site do Windows Azure, consulte [criar um aplicativo web ASP.NET no serviço de aplicativo do Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).

Para aprender os conceitos mais avançados de desenvolvimentos do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:

- [Projeto de SignalR](http://signalr.net)
- [Github do SignalR e exemplos](https://github.com/SignalR/SignalR)
- [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)
