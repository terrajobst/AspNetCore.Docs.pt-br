---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
title: 'Tutorial: Introdução ao SignalR 1.x e MVC 4 | Microsoft Docs'
author: bradygaster
description: Use o SignalR do ASP.NET e ASP.NET MVC 4 para criar um aplicativo de bate-papo em tempo real.
ms.author: bradyg
ms.date: 03/29/2013
ms.assetid: eeef9f73-6de3-49f9-b50b-9af22108f2ce
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
msc.type: authoredcontent
ms.openlocfilehash: dd55ca22004b7e3899f6a8789494c842b984787f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837787"
---
<a name="tutorial-getting-started-with-signalr-1x-and-mvc-4"></a>Tutorial: Introdução ao SignalR 1.x e MVC 4
====================
by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tutorial mostra como usar o SignalR do ASP.NET para criar um aplicativo de bate-papo em tempo real. Você adicionará o SignalR para um aplicativo MVC 4 e criar uma exibição de bate-papo para enviar e exibir as mensagens.


## <a name="overview"></a>Visão geral

Este tutorial apresenta o desenvolvimento de aplicativos web em tempo real com SignalR do ASP.NET e ASP.NET MVC 4. O tutorial usa o mesmo código de aplicativo de bate-papo, como o [tutorial de Introdução SignalR](tutorial-getting-started-with-signalr.md), mas mostra como adicioná-lo a um aplicativo MVC 4 com base no modelo de Internet.

Neste tópico, você aprenderá as seguintes tarefas de desenvolvimento do SignalR:

- Adicionar a biblioteca do SignalR para um aplicativo MVC 4.
- Criando uma classe de hub para enviar por push o conteúdo aos clientes.
- Usando a biblioteca jQuery SignalR em uma página da web para enviar mensagens e exibir as atualizações do hub.

A captura de tela a seguir mostra o aplicativo de bate-papo concluído em execução em um navegador.

![Instâncias de bate-papo](tutorial-getting-started-with-signalr-and-mvc-4/_static/image2.png)

Seções:

- [Configurar o projeto](#setup)
- [Executar o exemplo](#run)
- [Examinar o código](#code)
- [Próximas Etapas](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Configurar o projeto

Pré-requisitos:

- Visual Studio 2010 SP1, o Visual Studio 2012 ou o Visual Studio 2012 Express. Se você não tiver o Visual Studio, consulte [Downloads do ASP.NET](https://www.asp.net/downloads) para obter o Visual Studio 2012 Express ferramenta de desenvolvimento gratuita.
- Para o Visual Studio 2010, instale [ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683).

Esta seção mostra como criar um aplicativo ASP.NET MVC 4, adicione a biblioteca SignalR e criar o aplicativo de bate-papo.

1. 1. No Visual Studio criar um aplicativo ASP.NET MVC 4, nomeie-o SignalRChat e clique em Okey.

        > [!NOTE]
        > No VS 2010, selecione **.NET Framework 4** no controle de lista suspensa de versão do Framework. Código do SignalR é executado em versões do .NET Framework 4 e 4.5.

        ![Criar web mvc](tutorial-getting-started-with-signalr-and-mvc-4/_static/image3.png)
      2. Selecione o modelo de aplicativo de Internet, desmarque a opção de **criar um projeto de teste de unidade**e clique em Okey.

         ![Criar um site da internet mvc](tutorial-getting-started-with-signalr-and-mvc-4/_static/image4.png)
      3. Abra o **Ferramentas > Gerenciador de pacotes NuGet > Package Manager Console** e execute o comando a seguir. Esta etapa adiciona ao projeto de um conjunto de arquivos de script e referências de assembly que habilitar a funcionalidade do SignalR.

         `install-package Microsoft.AspNet.SignalR -Version 1.1.3`
      4. Na **Gerenciador de soluções** expanda a pasta de Scripts. Observe que as bibliotecas de script para o SignalR foram adicionadas ao projeto.

         ![Referências da biblioteca](tutorial-getting-started-with-signalr-and-mvc-4/_static/image6.png)
      5. Na **Gerenciador de soluções**, clique com botão direito no projeto, selecione **adicionar | Nova pasta**, e adicione uma nova pasta chamada **Hubs**.
      6. Clique com botão direito do **Hubs** pasta, clique em **adicionar | Classe**e crie uma nova classe c# denominada **ChatHub.cs**. Você usará essa classe como um hub de servidor SignalR que envia mensagens para todos os clientes.

> [!NOTE]
> Se você usar o Visual Studio 2012 e tiver instalado o [atualização do ASP.NET e Web Tools 2012.2](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation), você pode usar o novo modelo de item do SignalR para criar a classe hub. Para fazer isso, clique com botão direito do **Hubs** pasta, clique em **adicionar | Novo Item**, selecione **classe de Hub do SignalR (v1)** e nomeie a classe **ChatHub.cs**.


1. Substitua o código na **ChatHub** classe pelo código a seguir.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample1.cs)]
2. Abra o **global. asax** do arquivo para o projeto e adicione uma chamada ao método `RouteTable.Routes.MapHubs();` como a primeira linha de código no `Application_Start` método. Esse código registra a rota padrão para hubs do SignalR e deve ser chamado antes de registrar todas as outras rotas. Concluído `Application_Start` método é semelhante ao exemplo a seguir.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample2.cs)]
3. Editar o `HomeController` classe encontrado no **Controllers** e adicione o seguinte método à classe. Esse método retorna o **bate-papo** modo de exibição que você criará em uma etapa posterior.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample3.cs)]
4. Com o botão direito dentro do `Chat` método que você acabou de criar e clique em **adicionar exibição** para criar um novo arquivo de exibição.
5. No **adicionar exibição** caixa de diálogo, verifique se a caixa de seleção está selecionada para **usar uma layout ou página mestra** (desmarque as outras caixas de seleção) e, em seguida, clique em **adicionar**.

    ![Adicionar uma exibição](tutorial-getting-started-with-signalr-and-mvc-4/_static/image8.png)
6. Edite o novo arquivo de exibição denominado **Chat.cshtml**. Após o &lt;h2&gt; marca, cole o seguinte &lt;div&gt; seção e `@section scripts` bloco de código para a página. Esse script permite que a página para enviar mensagens de bate-papo e exibir as mensagens do servidor. O código completo para o modo de exibição de bate-papo é exibida no bloco de código a seguir.

    > [!IMPORTANT]
    > Quando você adiciona o SignalR e outras bibliotecas de script ao seu projeto do Visual Studio, o Gerenciador de pacotes pode instalar as versões dos scripts que são mais recentes do que as versões mostradas neste tópico. Certifique-se de que as referências de script em seu código correspondem às versões das bibliotecas do script instaladas em seu projeto.

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample4.cshtml)]
7. **Salvar tudo** para o projeto.

<a id="run"></a>

## <a name="run-the-sample"></a>Executar o exemplo

1. Pressione F5 para executar o projeto no modo de depuração.
2. Na linha de endereço do navegador, acrescente **bate-papo/home/** para a URL da página padrão para o projeto. Carrega a página de bate-papo em uma instância do navegador e solicitará um nome de usuário.

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr-and-mvc-4/_static/image9.png)
3. Insira um nome de usuário.
4. Copie a URL da linha de endereço do navegador e usá-lo para abrir duas ou mais instâncias de navegador. Em cada instância do navegador, insira um nome de usuário exclusivo.
5. Em cada instância do navegador, adicione um comentário e clique em **enviar**. Os comentários devem exibir em todas as instâncias do navegador.

    > [!NOTE]
    > Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor. O hub transmite seus comentários para todos os usuários atuais. Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.
6. A captura de tela a seguir mostra o aplicativo de bate-papo em execução em um navegador.

    ![Navegadores de bate-papo](tutorial-getting-started-with-signalr-and-mvc-4/_static/image11.png)
7. Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução. Esse nó é visível no modo de depuração, se você estiver usando o Internet Explorer como navegador. Há um arquivo de script denominado **hubs** que a biblioteca SignalR gera dinamicamente em tempo de execução. Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor. Se você usar um navegador que não seja o Internet Explorer, você também pode acessar o dynamic **hubs** arquivo navegando até ele diretamente, por exemplo http://mywebsite/signalr/hubs.

    ![Script gerado hub](tutorial-getting-started-with-signalr-and-mvc-4/_static/image13.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Examinar o código

O aplicativo de chat SignalR demonstra duas tarefas de desenvolvimento básicas do SignalR: criar um hub de que o objeto de coordenação principal no servidor e usando a biblioteca jQuery SignalR para enviar e receber mensagens.

### <a name="signalr-hubs"></a>Hubs de SignalR

No exemplo de código a **ChatHub** classe deriva a **ASPNET** classe. Derivando de **Hub** classe é uma maneira útil para criar um aplicativo do SignalR. Você pode criar métodos públicos em sua classe de hub e, em seguida, acessar esses métodos chamando-as de scripts do jQuery em uma página da web.

No código de bate-papo, os clientes chamam a **ChatHub.Send** método para enviar uma nova mensagem. O hub por sua vez envia a mensagem a todos os clientes chamando **Clients.All.addNewMessageToPage**.

O **enviar** método demonstra vários conceitos de hub:

- Declare métodos públicos em um hub para que os clientes possam chamá-los.
- Use o **Microsoft.AspNet.SignalR.Hub.Clients** propriedade para acessar todos os clientes conectados a esse hub.
- Chamar uma função do jQuery no cliente (como o `addNewMessageToPage` função) para atualizar os clientes.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>O SignalR e jQuery

O **Chat.cshtml** arquivo de exibição no código de exemplo mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR. Criando uma referência para o proxy geradas automaticamente para o hub, declarar uma função que o servidor pode chamar para enviar conteúdo para clientes e iniciando uma conexão para enviar mensagens para o hub de tarefas fundamentais no código.

O código a seguir declara um proxy para um hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample6.js)]

> [!NOTE]
> No jQuery a referência para a classe de servidor e seus membros é em minúsculas concatenadas. O exemplo de código faz referência de c# **ChatHub** classe no jQuery como **chatHub**. Se você quiser fazer referência a `ChatHub` classe no jQuery com convencional Pascal casing, como você faria no c#, edite o arquivo de classe ChatHub.cs. Adicionar um `using` instrução para fazer referência a `Microsoft.AspNet.SignalR.Hubs` namespace. Em seguida, adicione a `HubName` de atributo para o `ChatHub` classe, por exemplo `[HubName("ChatHub")]`. Por fim, atualize sua referência de jQuery para o `ChatHub` classe.


O código a seguir mostra como criar uma função de retorno de chamada no script. A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente. A chamada opcional para o `htmlEncode` função mostra uma maneira para HTML codificar o conteúdo da mensagem antes de exibi-la na página, como uma maneira de evitar a injeção de script.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample7.html)]

O código a seguir mostra como abrir uma conexão com o hub. O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página de bate-papo.

> [!NOTE]
> Essa abordagem garante que a conexão seja estabelecida antes de ser executado o manipulador de eventos.


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Próximas etapas

Você aprendeu que o SignalR é uma estrutura para a criação de aplicativos web em tempo real. Você também aprendeu várias tarefas de desenvolvimento do SignalR: como adicionar o SignalR para um aplicativo ASP.NET, como criar uma classe de hub e como enviar e receber mensagens do hub.

Para aprender os conceitos mais avançados de desenvolvimentos do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:

- [Projeto de SignalR](http://signalr.net)
- [Github do SignalR e exemplos](https://github.com/SignalR/SignalR)
- [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)
