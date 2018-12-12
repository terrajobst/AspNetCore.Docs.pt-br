---
uid: signalr/overview/testing-and-debugging/unit-testing-signalr-applications
title: Aplicativos do SignalR de teste de unidade | Microsoft Docs
author: pfletcher
description: Este artigo descreve como usar os recursos de teste de unidade do SignalR 2.0.
ms.author: riande
ms.date: 06/10/2014
ms.assetid: d1983524-e0d5-4ee6-9d87-1f552f7cb964
msc.legacyurl: /signalr/overview/testing-and-debugging/unit-testing-signalr-applications
msc.type: authoredcontent
ms.openlocfilehash: d8f3afdc2749173d1e260096ee6bd4bf1ae4c7cb
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287501"
---
<a name="unit-testing-signalr-applications"></a>Aplicativos do SignalR de testes de unidade
====================
por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este artigo descreve como usar os recursos de teste de unidade de mensagens do SignalR 2.
>
> ## <a name="software-versions-used-in-this-topic"></a>Versões de software usadas neste tópico
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Versão 2 do SignalR
>
>
>
> ## <a name="questions-and-comments"></a>Perguntas e comentários
>
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


<a id="unit"></a>
## <a name="unit-testing-signalr-applications"></a>Aplicativos do SignalR de teste de unidade

Você pode usar os recursos de teste de unidade no SignalR 2 para criar testes de unidade para seu aplicativo do SignalR. O SignalR 2 inclui o [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) interface, que pode ser usado para criar um objeto fictício para simular os métodos de hub para teste.

Nesta seção, você adicionará os testes de unidade para o aplicativo criado na [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md) usando [XUnit.net](https://github.com/xunit/xunit) e [Moq](https://github.com/Moq/moq4).

XUnit.net será usado para controlar o teste; Moq será usado para criar uma [simular](http://en.wikipedia.org/wiki/Mock_object) objeto para teste. Outras estruturas de simulação podem ser usadas se desejado; [NSubstitute](http://nsubstitute.github.io/) também é uma boa opção. Este tutorial demonstra como configurar o objeto fictício de duas maneiras: Primeiro, usando um `dynamic` objeto (introduzida no .NET Framework 4) e, segundo, usando uma interface.

### <a name="contents"></a>Conteúdo

Este tutorial contém as seções a seguir.

- [Testes de unidade com dinâmico](#dynamic)
- [Testes por tipo de unidade](#type)

<a id="dynamic"></a>
### <a name="unit-testing-with-dynamic"></a>Testes de unidade com dinâmico

Nesta seção, você adicionará um teste de unidade para o aplicativo criado na [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md) usando um objeto dinâmico.

1. Instalar o [extensão de executor de XUnit](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) para Visual Studio 2013.
2. Completar a [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md), ou baixe o aplicativo concluído no [MSDN Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).
3. Se você estiver usando a versão de download do aplicativo de Introdução, abra **Package Manager Console** e clique em **restaurar** para adicionar o pacote de SignalR ao projeto.

    ![Restaurar pacotes](unit-testing-signalr-applications/_static/image1.png)
4. Adicione um projeto à solução para o teste de unidade. Sua solução no botão direito do mouse **Gerenciador de soluções** e selecione **Add**, **novo projeto...** . Sob o **c#** nó, selecione o **Windows** nó. Selecione **biblioteca de classes**. Nomeie o novo projeto **TestLibrary** e clique em **Okey**.

    ![Criar a biblioteca de teste](unit-testing-signalr-applications/_static/image2.png)
5. Adicione uma referência no projeto de biblioteca de teste ao projeto SignalRChat. Clique com botão direito do **TestLibrary** do projeto e selecione **Add**, **referência...** . Selecione o **projetos** nó sob o **solução** nó e verificação **SignalRChat**. Clique em **OK**.

    ![Adicionar referência de projeto](unit-testing-signalr-applications/_static/image3.png)
6. Adicione os pacotes SignalR, Moq e XUnit para o **TestLibrary** projeto. No **Package Manager Console**, defina as **projeto padrão** na lista suspensa para **TestLibrary**. Execute os comandos a seguir na janela do console:

   - `Install-Package Microsoft.AspNet.SignalR`
   - `Install-Package Moq`
   - `Install-Package XUnit`

     ![Instalar pacotes](unit-testing-signalr-applications/_static/image4.png)
7. Crie o arquivo de teste. Clique com botão direito do **TestLibrary** do projeto e clique em **adicionar...** , **Classe**. Nomeie a nova classe **Tests.cs**.
8. Substitua o conteúdo de Tests.cs com o código a seguir.

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample1.cs)]

    No código acima, um cliente de teste é criado usando o `Mock` do objeto do [Moq](https://github.com/Moq/moq4) biblioteca de tipo [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (no SignalR 2.1, atribua `dynamic` para o tipo parâmetro). O `IHubCallerConnectionContext` interface é o objeto de proxy com o qual você invocar métodos no cliente. O `broadcastMessage` função, em seguida, é definida para o cliente fictício para que ela pode ser chamada pelo `ChatHub` classe. O mecanismo de teste, em seguida, chama o `Send` método da `ChatHub` classe, que por sua vez chama o fictícia `broadcastMessage` função.
9. Compile a solução pressionando **F6**.
10. Execute o teste de unidade. No Visual Studio, selecione **teste**, **Windows**, **Gerenciador de testes**. Na janela Test Explorer, clique com botão direito **HubsAreMockableViaDynamic** e selecione **executar testes selecionados**.

    ![Gerenciador de Testes](unit-testing-signalr-applications/_static/image5.png)
11. Verifique se que o teste foi aprovado, verificando o painel inferior na janela do Gerenciador de testes. A janela mostrará que o teste foi aprovado.

    ![Teste aprovado](unit-testing-signalr-applications/_static/image6.png)

<a id="type"></a>
### <a name="unit-testing-by-type"></a>Testes por tipo de unidade

Nesta seção, você adicionará um teste para o aplicativo criado na [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md) usando uma interface que contém o método a ser testado.

1. Conclua as etapas 1 a 7 na [testes de unidade com dinâmico](#dynamic) tutorial acima.
2. Substitua o conteúdo de Tests.cs com o código a seguir.

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample2.cs)]

    No código acima, uma interface é criada definindo a assinatura do `broadcastMessage` método para o qual o mecanismo de teste irá criar um cliente fictício. Um cliente fictício, em seguida, é criado usando o `Mock` objeto do tipo [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (no SignalR 2.1, atribua `dynamic` para o parâmetro de tipo.) O `IHubCallerConnectionContext` interface é o objeto de proxy com o qual você invocar métodos no cliente.

    O teste, em seguida, cria uma instância de `ChatHub`e, em seguida, cria uma versão fictícia do `broadcastMessage` método, que por sua vez é invocado chamando o `Send` método no hub.
3. Compile a solução pressionando **F6**.
4. Execute o teste de unidade. No Visual Studio, selecione **teste**, **Windows**, **Gerenciador de testes**. Na janela Test Explorer, clique com botão direito **HubsAreMockableViaDynamic** e selecione **executar testes selecionados**.

    ![Gerenciador de Testes](unit-testing-signalr-applications/_static/image7.png)
5. Verifique se que o teste foi aprovado, verificando o painel inferior na janela do Gerenciador de testes. A janela mostrará que o teste foi aprovado.

    ![Teste aprovado](unit-testing-signalr-applications/_static/image8.png)
