---
uid: aspnet/overview/owin-and-katana/owin-startup-class-detection
title: Detecção de classe de inicialização OWIN | Microsoft Docs
author: Praburaj
description: Este tutorial mostra como configurar qual classe de inicialização OWIN é carregado. Para obter mais informações sobre o OWIN, consulte uma visão geral do projeto Katana. Este tutorial foi...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 08257f55-36f4-4e39-9c88-2a5602838c79
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-startup-class-detection
msc.type: authoredcontent
ms.openlocfilehash: 0b34cca8b48383dbb028106651758dff889ed614
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667291"
---
<a name="owin-startup-class-detection"></a>Detecção de classe de inicialização OWIN
====================

> Este tutorial mostra como configurar qual classe de inicialização OWIN é carregado. Para obter mais informações sobre o OWIN, consulte [uma visão geral do projeto Katana](an-overview-of-project-katana.md). Este tutorial foi escrito por Rick Anderson ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) ), Howard Dierking e Praburaj Thiagarajan ( [ @howard \_dierking](https://twitter.com/howard_dierking) ).
>
> ## <a name="prerequisites"></a>Pré-requisitos
>
> [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)


## <a name="owin-startup-class-detection"></a>Detecção de classe de inicialização OWIN

 Todos os aplicativos OWIN tem uma classe de inicialização na qual você pode especificar componentes para o pipeline do aplicativo. Você pode se conectar a sua classe de inicialização com o tempo de execução de maneiras diferentes, dependendo do modelo de hospedagem escolhida (OwinHost, IIS e IIS Express). A classe de inicialização mostrada neste tutorial pode ser usada em cada aplicativo de hospedagem. Você se conectar a classe de inicialização com o tempo de execução hospedagem usando uma dessas abordagens:

1. **Convenção de nomenclatura**: Katana procura por uma classe chamada `Startup` no namespace, o nome do assembly ou namespace global correspondentes.
2. **Atributo OwinStartup**: Essa é a abordagem que a maioria dos desenvolvedores levará para especificar a classe de inicialização. O seguinte atributo definirá a classe de inicialização o `TestStartup` classe o `StartupDemo` namespace.

    [!code-csharp[Main](owin-startup-class-detection/samples/sample1.cs)]

   O `OwinStartup` atributo substitui a convenção de nomenclatura. Você também pode especificar um nome amigável com esse atributo, no entanto, usar um nome amigável requer a também usar o `appSetting` elemento no arquivo de configuração.
3. **O elemento de appSetting no arquivo de configuração**: O `appSetting` substitui o `OwinStartup` atributo e a convenção de nomenclatura. Você pode ter várias classes de inicialização (cada um usando um `OwinStartup` atributo) e configurar qual classe de inicialização será carregado em um arquivo de configuração usando marcação semelhante ao seguinte:

    [!code-xml[Main](owin-startup-class-detection/samples/sample2.xml)]

   A chave a seguir, que especifica explicitamente a classe de inicialização e o assembly também pode ser usada:

    [!code-xml[Main](owin-startup-class-detection/samples/sample3.xml)]

   O XML no arquivo de configuração a seguir especifica um nome de classe de inicialização amigável de `ProductionConfiguration`.

    [!code-xml[Main](owin-startup-class-detection/samples/sample4.xml)]

   A marcação acima deve ser usada com o seguinte `OwinStartup` atributo que especifica um nome amigável e faz com que o `ProductionStartup2` classe a ser executada.

    [!code-csharp[Main](owin-startup-class-detection/samples/sample5.cs?highlight=1,16)]
4. Para desabilitar a descoberta de inicialização OWIN, adicione a `appSetting owin:AutomaticAppStartup` com um valor de `"false"` no arquivo Web. config.

    [!code-xml[Main](owin-startup-class-detection/samples/sample6.xml)]

## <a name="create-an-aspnet-web-app-using-owin-startup"></a>Criar um aplicativo Web do ASP.NET usando a inicialização do OWIN

1. Crie um aplicativo de web Asp.Net vazio e denomine **StartupDemo**. -Instalar `Microsoft.Owin.Host.SystemWeb` usando o Gerenciador de pacotes do NuGet. Do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**e então **Package Manager Console**. Insira o seguinte comando:

    [!code-powershell[Main](owin-startup-class-detection/samples/sample7.ps1)]
2. Adicione uma classe de inicialização do OWIN. No Visual Studio 2017 com o botão direito no projeto e selecione **Add Class**. - na **Adicionar Novo Item** caixa de diálogo, digite *OWIN* no campo de pesquisa e altere o nome para o Startup.cs, e, em seguida, selecione **adicionar**.

     ![](owin-startup-class-detection/_static/image1.png)

   Na próxima vez em que você deseja adicionar um *classe de inicialização Owin*, ele estará em disponível na **Add** menu.

     ![](owin-startup-class-detection/_static/image2.png)

   Como alternativa, é possível com o botão direito no projeto e selecionar **Add**, em seguida, selecione **Novo Item**e, em seguida, selecione o **classe de inicialização Owin**.

     ![](owin-startup-class-detection/_static/image3.png)

- Substitua o código gerado na *Startup.cs* arquivo com o seguinte:

    [!code-csharp[Main](owin-startup-class-detection/samples/sample8.cs?highlight=5,7,15-28,31-34)]

  O `app.Use` expressão lambda é usado para registrar o componente especificado de middleware ao pipeline do OWIN. Nesse caso, estamos configurando registro em log de solicitações de entrada antes de responder à solicitação de entrada. O `next` parâmetro é o delegado ( [Func](https://msdn.microsoft.com/library/bb534960(v=vs.100).aspx) &lt; [tarefa](https://msdn.microsoft.com/library/dd321424(v=vs.100).aspx) &gt; ) para o próximo componente no pipeline. O `app.Run` expressão lambda conecta-se a solicitações de entrada do pipeline e fornece o mecanismo de resposta.
     > [!NOTE]
     > No código acima é ter comentado a `OwinStartup` atributo e nós depende da convenção de executar a classe denominada `Startup` .-pressione ***F5*** para executar o aplicativo. Clique em atualizar algumas vezes.

    ![](owin-startup-class-detection/_static/image4.png) Observação: O número mostrado nas imagens neste tutorial não corresponderá o número que você vê. A cadeia de caracteres de milissegundo é usada para mostrar uma nova resposta quando a página for atualizada.
  Você pode ver as informações de rastreamento a **saída** janela.

    ![](owin-startup-class-detection/_static/image5.png)

## <a name="add-more-startup-classes"></a>Adicionar mais Classes de inicialização

Nesta seção vamos adicionar outra classe de inicialização. Você pode adicionar vários classe de inicialização OWIN para seu aplicativo. Por exemplo, você talvez queira criar classes de inicialização para o desenvolvimento, teste e produção.

1. Crie uma nova classe de inicialização OWIN e nomeie- `ProductionStartup`.
2. Substitua o código gerado pelo seguinte:

    [!code-csharp[Main](owin-startup-class-detection/samples/sample9.cs?highlight=14-18)]
3. Pressione F5 de controle para executar o aplicativo. O `OwinStartup` atributo especifica a classe de inicialização de produção é executada.

    ![](owin-startup-class-detection/_static/image6.png)
4. Crie outra classe de inicialização OWIN e nomeie- `TestStartup`.
5. Substitua o código gerado pelo seguinte:

    [!code-csharp[Main](owin-startup-class-detection/samples/sample10.cs?highlight=6,14-18)]

   O `OwinStartup` sobrecarga do atributo acima especifica `TestingConfiguration` como o *amigável* nome da classe Startup.
6. Abra o *Web. config* arquivo e adicione a chave de inicialização do aplicativo OWIN que especifica o nome amigável da classe de inicialização:

    [!code-xml[Main](owin-startup-class-detection/samples/sample11.xml?highlight=3-5)]
7. Pressione F5 de controle para executar o aplicativo. O elemento de configurações do aplicativo leva precedentes e o teste de configuração é executada.

    ![](owin-startup-class-detection/_static/image7.png)
8. Remover o *amigável* nome da `OwinStartup` de atributo no `TestStartup` classe.

    [!code-csharp[Main](owin-startup-class-detection/samples/sample12.cs)]
9. Substitua a chave de inicialização do aplicativo OWIN na *Web. config* arquivo com o seguinte:

    [!code-xml[Main](owin-startup-class-detection/samples/sample13.xml)]
10. Reverter o `OwinStartup` atributo em cada classe para o código do atributo padrão gerado pelo Visual Studio:

    [!code-csharp[Main](owin-startup-class-detection/samples/sample14.cs)]

    Cada uma das chaves de inicialização do aplicativo OWIN abaixo fará com que a classe de produção ser executado.

    [!code-xml[Main](owin-startup-class-detection/samples/sample15.xml)]

    A última chave de inicialização especifica o método de configuração de inicialização. A seguinte chave de inicialização do aplicativo OWIN permite que você altere o nome da classe de configuração para `MyConfiguration` .

    [!code-xml[Main](owin-startup-class-detection/samples/sample16.xml)]

## <a name="using-owinhostexe"></a>Usando Owinhost.exe

1. Substitua o arquivo Web. config com a seguinte marcação:

    [!code-xml[Main](owin-startup-class-detection/samples/sample17.xml?highlight=3-6)]

   A última chave wins, portanto, neste caso, `TestStartup` for especificado.
2. Instale o Owinhost do PMC:

    [!code-console[Main](owin-startup-class-detection/samples/sample18.cmd)]
3. Navegue até a pasta de aplicativo (a pasta que contém o *Web. config* arquivo) e, em um prompt de comando e digite:

    [!code-console[Main](owin-startup-class-detection/samples/sample19.cmd)]

   A janela de comando mostrará:

    [!code-console[Main](owin-startup-class-detection/samples/sample20.cmd)]
4. Inicie um navegador com a URL `http://localhost:5000/`.

    ![](owin-startup-class-detection/_static/image8.png)

   OwinHost cumprido as convenções de inicialização listadas acima.
5. Na janela de comando, pressione Enter para sair OwinHost.
6. No `ProductionStartup` da classe, adicione o seguinte atributo OwinStartup que especifica um nome amigável da *ProductionConfiguration*.

    [!code-csharp[Main](owin-startup-class-detection/samples/sample21.cs)]
7. No prompt de comando e digite:

    [!code-console[Main](owin-startup-class-detection/samples/sample22.cmd)]

   A classe de inicialização de produção é carregada.
    ![](owin-startup-class-detection/_static/image9.png)

   Nosso aplicativo tem várias classes de inicialização e, neste exemplo, podemos adiaram qual classe de inicialização para carregar até o tempo de execução.
8. As seguintes opções de inicialização de tempo de execução de teste:

    [!code-console[Main](owin-startup-class-detection/samples/sample23.cmd)]
