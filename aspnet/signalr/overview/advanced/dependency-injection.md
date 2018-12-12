---
uid: signalr/overview/advanced/dependency-injection
title: Injeção de dependência no SignalR | Microsoft Docs
author: MikeWasson
description: Versões de software usado neste tópico, o Visual Studio 2013 .NET 4.5 SignalR versões anteriores de versão 2 deste tópico para obter informações sobre versões anteriores do...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: a14121ae-02cf-4024-8af0-9dd0dc810690
msc.legacyurl: /signalr/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: f5959902b030621a19d912d7db2ea0faf95b7a7c
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287776"
---
<a name="dependency-injection-in-signalr"></a>Injeção de dependência no SignalR
====================
por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a>Versões de software usadas neste tópico
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Versão 2 do SignalR
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Versões anteriores deste tópico
>
> Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Perguntas e comentários
>
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


Injeção de dependência é uma maneira de remover dependências embutidas entre objetos, tornando mais fácil para substituir as dependências de um objeto, seja por testes (usando objetos fictícios) ou para alterar o comportamento de tempo de execução. Este tutorial mostra como realizar a injeção de dependência em hubs do SignalR. Ele também mostra como usar contêineres IoC com SignalR. Um contêiner IoC é uma estrutura geral para injeção de dependência.

## <a name="what-is-dependency-injection"></a>O que é a injeção de dependência?

Ignore esta seção se você já estiver familiarizado com a injeção de dependência.

*Injeção de dependência* (DI) é um padrão em que os objetos não são responsáveis por criar suas próprias dependências. Aqui está um exemplo simples para motivar a injeção de dependência. Suponha que você tenha um objeto que precisa para registrar mensagens. Você pode definir uma interface de registro em log:

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

No seu objeto, você pode criar um `ILogger` para as mensagens de log:

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

Isso funciona, mas não é o melhor design. Se você quiser substituir `FileLogger` com outra `ILogger` implementação, você precisará modificar `SomeComponent`. Suponhamos que muitos dos outros objetos usam `FileLogger`, você precisará alterar todas elas. Ou se você decidir fazer `FileLogger` um singleton, você também precisará fazer alterações em todo o aplicativo.

Uma abordagem melhor é "inserir" um `ILogger` no objeto — por exemplo, usando um argumento do construtor:

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

Agora, o objeto não é responsável por selecionar quais `ILogger` usar. Você pode alternar `ILogger` implementações sem alterar os objetos que dependem dele.

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

Esse padrão é chamado [injeção de construtor](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection). Outro padrão é a injeção de setter, em que você definir a dependência por meio de um método de setter ou propriedade.

## <a name="simple-dependency-injection-in-signalr"></a>Injeção de dependência simples no SignalR

Considere o aplicativo de bate-papo do tutorial [Introdução ao SignalR](../getting-started/tutorial-getting-started-with-signalr.md). Aqui está a classe hub desse aplicativo:

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

Suponha que você deseja armazenar as mensagens de bate-papo no servidor antes de enviá-los. Você pode definir uma interface que abstrai essa funcionalidade e usar injeção de dependência para injetar a interface para o `ChatHub` classe.

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

O único problema é que um aplicativo de SignalR não cria diretamente hubs; O SignalR cria para você. Por padrão, o SignalR espera que uma classe de hub para ter um construtor sem parâmetros. No entanto, você pode facilmente registrar uma função para criar instâncias de hub e usar essa função para executar a injeção de dependência. Registrar a função chamando **GlobalHost.DependencyResolver.Register**.

[!code-csharp[Main](dependency-injection/samples/sample7.cs)]

Agora o SignalR irá chamar essa função anônima sempre que precisar criar um `ChatHub` instância.

## <a name="ioc-containers"></a>Contêineres IoC

O código anterior é adequado para casos simples. Mas você ainda precisava escrever isso:

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

Em um aplicativo complexo com muitas dependências, você precisa escrever muito desse código de "Conectando". Esse código pode ser difícil de manter, especialmente se as dependências são aninhadas. Também é difícil de teste de unidade.

Uma solução é usar um contêiner IoC. Um contêiner IoC é um componente de software que é responsável por gerenciar as dependências. Você registra tipos com o contêiner e, em seguida, usa o contêiner para criar objetos. O contêiner automaticamente detecta as relações de dependência. Vários contêineres IoC também permitem controlar coisas como o tempo de vida do objeto e o escopo.

> [!NOTE]
> "IoC" significa "inversão de controle", que é um padrão geral em que uma estrutura chama o código do aplicativo. Um contêiner IoC constrói seus objetos para você, o que "inverte" o fluxo normal de controle.


## <a name="using-ioc-containers-in-signalr"></a>Usando contêineres IoC no SignalR

O aplicativo de bate-papo provavelmente é muito simple para se beneficiar de um contêiner de IoC. Em vez disso, vamos examinar a [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample) exemplo.

O exemplo StockTicker define duas classes principais:

- `StockTickerHub`: A classe hub, que gerencia as conexões de cliente.
- `StockTicker`: Um singleton que mantém os preços de ações e os atualiza periodicamente.

`StockTickerHub` contém uma referência à `StockTicker` singleton, enquanto `StockTicker` contém uma referência para o **IHubConnectionContext** para o `StockTickerHub`. Ele usa essa interface para se comunicar com `StockTickerHub` instâncias. (Para obter mais informações, consulte [transmissão de servidor com SignalR do ASP.NET](../getting-started/tutorial-server-broadcast-with-signalr.md).)

Podemos usar um contêiner IoC para desembolar essas dependências de um pouco. Primeiro, vamos simplificar as `StockTickerHub` e `StockTicker` classes. No código a seguir, comentei as partes que não precisamos.

Remova o construtor sem parâmetros de `StockTickerHub`. Em vez disso, nós usaremos sempre DI para criar o hub.

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

Para StockTicker, remova a instância singleton. Posteriormente, usaremos o contêiner de IoC para controlar o tempo de vida StockTicker. Além disso, verifique o construtor público.

[!code-csharp[Main](dependency-injection/samples/sample10.cs?highlight=7)]

Em seguida, podemos refatorar o código, criando uma interface para `StockTicker`. Vamos usar essa interface para desacoplar a `StockTickerHub` do `StockTicker` classe.

Visual Studio faz esse tipo de refatoração fácil. Abra o arquivo StockTicker.cs, clique com botão direito no `StockTicker` declaração de classe e, em seguida, selecione **refatorar** ... **Extrair Interface**.

![](dependency-injection/_static/image1.png)

No **extrair Interface** caixa de diálogo, clique em **Selecionar tudo**. Deixe os outros padrões. Clique em **OK**.

![](dependency-injection/_static/image2.png)

O Visual Studio cria uma nova interface chamada `IStockTicker`e também será alterado `StockTicker` derivar `IStockTicker`.

Abra o arquivo IStockTicker.cs e alterar a interface **público**.

[!code-csharp[Main](dependency-injection/samples/sample11.cs?highlight=1)]

No `StockTickerHub` classe, altere as duas instâncias de `StockTicker` para `IStockTicker`:

[!code-csharp[Main](dependency-injection/samples/sample12.cs?highlight=4,6)]

Criando um `IStockTicker` interface não é estritamente necessário, mas eu queria mostrar como a injeção de dependência pode ajudar a reduzir o acoplamento entre componentes em seu aplicativo.

## <a name="add-the-ninject-library"></a>Adicionar a biblioteca Ninject

Há vários contêineres de IoC de código-fonte aberto para .NET. Para este tutorial, usarei [Ninject](http://www.ninject.org/). (Outras bibliotecas populares incluem [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Unity](https://github.com/unitycontainer/unity), e [StructureMap ](http://docs.structuremap.net).)

Use o Gerenciador de pacotes NuGet para instalar o [Ninject biblioteca](https://nuget.org/packages/Ninject/3.0.1.10). No Visual Studio, do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet** > **Package Manager Console**. Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:

[!code-powershell[Main](dependency-injection/samples/sample13.ps1)]

## <a name="replace-the-signalr-dependency-resolver"></a>Substitua o resolvedor de dependência do SignalR

Para usar Ninject dentro do SignalR, crie uma classe que deriva de **DefaultDependencyResolver**.

[!code-csharp[Main](dependency-injection/samples/sample14.cs)]

Essa classe substitui o **GetService** e **GetServices** métodos **DefaultDependencyResolver**. O SignalR chama esses métodos para criar vários objetos em tempo de execução, incluindo instâncias do hub, bem como vários serviços usados internamente pelo SignalR.

- O **GetService** método cria uma única instância de um tipo. Substitua este método para chamar o kernel de Ninject **TryGet** método. Se esse método retornar nulo, volte para o resolvedor padrão.
- O **GetServices** método cria uma coleção de objetos de um tipo especificado. Substitua este método para concatenar os resultados de Ninject com os resultados do resolvedor padrão.

## <a name="configure-ninject-bindings"></a>Configurar as ligações de Ninject

Agora vamos usar Ninject para declarar associações de tipo.

Abra a classe do Startup.cs do seu aplicativo (que você seja criado manualmente, de acordo com as instruções de pacote em `readme.txt`, ou que foi criado com a adição de autenticação ao seu projeto). No `Startup.Configuration` método, crie o contêiner Ninject, que chama o Ninject o *kernel*.

[!code-csharp[Main](dependency-injection/samples/sample15.cs)]

Crie uma instância do nosso resolvedor de dependência personalizada:

[!code-csharp[Main](dependency-injection/samples/sample16.cs)]

Criar uma associação para `IStockTicker` da seguinte maneira:

[!code-csharp[Main](dependency-injection/samples/sample17.cs)]

Esse código está dizendo que duas coisas. Primeiro, sempre que o aplicativo precisa de um `IStockTicker`, o kernel deve criar uma instância de `StockTicker`. Segundo, o `StockTicker` classe deve ser criado como um objeto de singleton. Ninject criará uma instância do objeto e retornar a mesma instância para cada solicitação.

Criar uma associação para **IHubConnectionContext** da seguinte maneira:

[!code-csharp[Main](dependency-injection/samples/sample18.cs)]

Esse código creatres uma função anônima que retorna um **IHubConnection**. O **WhenInjectedInto** Ninject usar essa função somente durante a criação do método informa `IStockTicker` instâncias. O motivo é que o SignalR cria **IHubConnectionContext** instâncias internamente, e não queremos substituir como o SignalR cria. Essa função se aplica somente aos nossos `StockTicker` classe.

Passe o resolvedor de dependência para o **MapSignalR** método adicionando uma configuração de hub:

[!code-csharp[Main](dependency-injection/samples/sample19.cs)]

Atualize o método Startup.ConfigureSignalR na classe de inicialização do exemplo com o novo parâmetro:

[!code-csharp[Main](dependency-injection/samples/sample20.cs)]

Agora o SignalR usará o resolvedor especificado na **MapSignalR**, em vez do resolvedor padrão.

Aqui está o código completo para a listagem `Startup.Configuration`.

[!code-csharp[Main](dependency-injection/samples/sample21.cs)]

Para executar o aplicativo StockTicker no Visual Studio, pressione F5. Na janela do navegador, navegue até `http://localhost:*port*/SignalR.Sample/StockTicker.html`.

![](dependency-injection/_static/image3.png)

O aplicativo tem a mesma funcionalidade. (Para obter uma descrição, consulte [transmissão de servidor com SignalR do ASP.NET](../getting-started/tutorial-server-broadcast-with-signalr.md).) Ainda não alteramos o comportamento. acabou de criar o código mais fácil de testar, manter e evoluem.
