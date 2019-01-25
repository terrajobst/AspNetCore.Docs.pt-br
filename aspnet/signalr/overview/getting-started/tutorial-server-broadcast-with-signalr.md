---
uid: signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
title: 'Tutorial: Transmissão de servidor com SignalR 2 | Microsoft Docs'
author: tdykstra
description: Este tutorial mostra como criar um aplicativo web que usa o ASP.NET SignalR 2 para fornecer funcionalidade de difusão de servidor.
ms.author: bradyg
ms.date: 01/02/2019
ms.topic: tutorial
ms.assetid: 1568247f-60b5-4eca-96e0-e661fbb2b273
msc.legacyurl: /signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: a243c78c7d552f1c82a88c6083871fcd16538618
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837423"
---
# <a name="tutorial-server-broadcast-with-signalr-2"></a><span data-ttu-id="39760-103">Tutorial: Servidor de transmissão com SignalR 2</span><span class="sxs-lookup"><span data-stu-id="39760-103">Tutorial: Server broadcast with SignalR 2</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="39760-104">Este tutorial mostra como criar um aplicativo web que usa o ASP.NET SignalR 2 para fornecer funcionalidade de difusão de servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-104">This tutorial shows how to create a web application that uses ASP.NET SignalR 2 to provide server broadcast functionality.</span></span> <span data-ttu-id="39760-105">Transmissão de servidor significa que o servidor inicia as comunicações enviadas para clientes.</span><span class="sxs-lookup"><span data-stu-id="39760-105">Server broadcast means that the server starts the communications sent to clients.</span></span>

<span data-ttu-id="39760-106">O aplicativo que você criará neste tutorial simula uma bolsa, um cenário típico para a funcionalidade de difusão de servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-106">The application that you'll create in this tutorial simulates a stock ticker, a typical scenario for server broadcast functionality.</span></span> <span data-ttu-id="39760-107">Periodicamente, o servidor aleatoriamente de atualizações de preços de ações e as atualizações de difusão para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="39760-107">Periodically, the server randomly updates stock prices and broadcast the updates to all connected clients.</span></span> <span data-ttu-id="39760-108">No navegador, os números e símbolos na **alterar** e **%** colunas alteram dinamicamente em resposta a notificações do servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-108">In the browser, the numbers and symbols in the **Change** and **%** columns dynamically change in response to notifications from the server.</span></span> <span data-ttu-id="39760-109">Se você abrir navegadores adicionais para a mesma URL, todos eles mostram os mesmos dados e as mesmas alterações aos dados simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="39760-109">If you open additional browsers to the same URL, they all show the same data and the same changes to the data simultaneously.</span></span>

![Criar web](tutorial-server-broadcast-with-signalr/_static/image1.png)

<span data-ttu-id="39760-111">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="39760-111">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="39760-112">Criar o projeto</span><span class="sxs-lookup"><span data-stu-id="39760-112">Create the project</span></span>
> * <span data-ttu-id="39760-113">Configure o código de servidor</span><span class="sxs-lookup"><span data-stu-id="39760-113">Set up the server code</span></span>
> * <span data-ttu-id="39760-114">Examinar o código de servidor</span><span class="sxs-lookup"><span data-stu-id="39760-114">Examine the server code</span></span>
> * <span data-ttu-id="39760-115">Configure o código de cliente</span><span class="sxs-lookup"><span data-stu-id="39760-115">Set up the client code</span></span>
> * <span data-ttu-id="39760-116">Examinar o código do cliente</span><span class="sxs-lookup"><span data-stu-id="39760-116">Examine the client code</span></span>
> * <span data-ttu-id="39760-117">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="39760-117">Test the application</span></span>
> * <span data-ttu-id="39760-118">Habilite o registro em logs</span><span class="sxs-lookup"><span data-stu-id="39760-118">Enable logging</span></span>

> [!IMPORTANT]
> <span data-ttu-id="39760-119">Se você não quiser trabalhar com as etapas de criação do aplicativo, você pode instalar o pacote SignalR.Sample em um novo projeto de aplicativo Web ASP.NET vazio.</span><span class="sxs-lookup"><span data-stu-id="39760-119">If you don't want to work through the steps of building the application, you can install the SignalR.Sample package in a new Empty ASP.NET Web Application project.</span></span> <span data-ttu-id="39760-120">Se você instalar o pacote do NuGet sem executar as etapas neste tutorial, você deve seguir as instruções de *Readme. txt* arquivo.</span><span class="sxs-lookup"><span data-stu-id="39760-120">If you install the NuGet package without performing the steps in this tutorial, you must follow the instructions in the *readme.txt* file.</span></span> <span data-ttu-id="39760-121">Para executar o pacote que você precisa adicionar uma inicialização do OWIN de classe que chama o `ConfigureSignalR` método no pacote instalado.</span><span class="sxs-lookup"><span data-stu-id="39760-121">To run the package you need to add an OWIN startup class which calls the `ConfigureSignalR` method in the installed package.</span></span> <span data-ttu-id="39760-122">Você receberá um erro se você não adicionar a classe de inicialização do OWIN.</span><span class="sxs-lookup"><span data-stu-id="39760-122">You will receive an error if you do not add the OWIN startup class.</span></span> <span data-ttu-id="39760-123">Consulte a [instalar o exemplo StockTicker](#install-the-stockticker-sample) seção deste artigo.</span><span class="sxs-lookup"><span data-stu-id="39760-123">See the [Install the StockTicker sample](#install-the-stockticker-sample) section of this article.</span></span>


## <a name="prerequisites"></a><span data-ttu-id="39760-124">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="39760-124">Prerequisites</span></span>

 * <span data-ttu-id="39760-125">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) com o **ASP.NET e desenvolvimento web** carga de trabalho.</span><span class="sxs-lookup"><span data-stu-id="39760-125">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="39760-126">Criar o projeto</span><span class="sxs-lookup"><span data-stu-id="39760-126">Create the project</span></span>

<span data-ttu-id="39760-127">Esta seção mostra como usar o Visual Studio 2017 para criar um aplicativo de Web ASP.NET vazio.</span><span class="sxs-lookup"><span data-stu-id="39760-127">This section shows how to use Visual Studio 2017 to create an empty ASP.NET Web Application.</span></span>

1. <span data-ttu-id="39760-128">No Visual Studio, crie um aplicativo Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="39760-128">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Criar web](tutorial-server-broadcast-with-signalr/_static/image2.png)

1. <span data-ttu-id="39760-130">No **novo aplicativo de Web do ASP.NET - SignalR.StockTicker** janela, deixe **vazia** selecionado e selecione **Okey**.</span><span class="sxs-lookup"><span data-stu-id="39760-130">In the **New ASP.NET Web Application - SignalR.StockTicker** window, leave **Empty** selected and select **OK**.</span></span>

## <a name="set-up-the-server-code"></a><span data-ttu-id="39760-131">Configure o código de servidor</span><span class="sxs-lookup"><span data-stu-id="39760-131">Set up the server code</span></span>

<span data-ttu-id="39760-132">Nesta seção, você deve definir o código que é executado no servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-132">In this section, you set up the code that runs on the server.</span></span>

### <a name="create-the-stock-class"></a><span data-ttu-id="39760-133">Criar a classe de estoque</span><span class="sxs-lookup"><span data-stu-id="39760-133">Create the Stock class</span></span>

<span data-ttu-id="39760-134">Você começa criando a *Stock* classe que você usará para armazenar e transmitir informações sobre uma ação de modelo.</span><span class="sxs-lookup"><span data-stu-id="39760-134">You begin by creating the *Stock* model class that you'll use to store and transmit information about a stock.</span></span>

1. <span data-ttu-id="39760-135">Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="39760-135">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="39760-136">Nomeie a classe *Stock* e adicioná-lo ao projeto.</span><span class="sxs-lookup"><span data-stu-id="39760-136">Name the class *Stock* and add it to the project.</span></span>

1. <span data-ttu-id="39760-137">Substitua o código na *Stock.cs* arquivo com este código:</span><span class="sxs-lookup"><span data-stu-id="39760-137">Replace the code in the *Stock.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample1.cs)]

    <span data-ttu-id="39760-138">São as duas propriedades que você definirá quando você cria stocks `Symbol` (por exemplo, MSFT da Microsoft) e `Price`.</span><span class="sxs-lookup"><span data-stu-id="39760-138">The two properties that you'll set when you create stocks are `Symbol` (for example, MSFT for Microsoft) and `Price`.</span></span> <span data-ttu-id="39760-139">As outras propriedades dependem de como e quando você definir `Price`.</span><span class="sxs-lookup"><span data-stu-id="39760-139">The other properties depend on how and when you set `Price`.</span></span> <span data-ttu-id="39760-140">Na primeira vez em que você definir `Price`, o valor é propagado para `DayOpen`.</span><span class="sxs-lookup"><span data-stu-id="39760-140">The first time you set `Price`, the value gets propagated to `DayOpen`.</span></span> <span data-ttu-id="39760-141">Depois disso, quando você define `Price`, o aplicativo calcula os `Change` e `PercentChange` valores de propriedade com base na diferença entre `Price` e `DayOpen`.</span><span class="sxs-lookup"><span data-stu-id="39760-141">After that, when you set `Price`, the app calculates the `Change` and `PercentChange` property values based on the difference between `Price` and `DayOpen`.</span></span>

### <a name="create-the-stocktickerhub-and-stockticker-classes"></a><span data-ttu-id="39760-142">Criar as classes StockTickerHub e StockTicker</span><span class="sxs-lookup"><span data-stu-id="39760-142">Create the StockTickerHub and StockTicker classes</span></span>

<span data-ttu-id="39760-143">Você usará a API de Hub do SignalR para manipular a interação do servidor para cliente.</span><span class="sxs-lookup"><span data-stu-id="39760-143">You'll use the SignalR Hub API to handle server-to-client interaction.</span></span> <span data-ttu-id="39760-144">Um `StockTickerHub` classe que deriva de `SignalRHub` classe manipulará receber chamadas de método e de conexões de clientes.</span><span class="sxs-lookup"><span data-stu-id="39760-144">A `StockTickerHub` class that derives from the `SignalRHub` class will handle receiving connections and method calls from clients.</span></span> <span data-ttu-id="39760-145">Você também precisa manter dados de estoque e executar um `Timer` objeto.</span><span class="sxs-lookup"><span data-stu-id="39760-145">You also need to maintain stock data and run a `Timer` object.</span></span> <span data-ttu-id="39760-146">O `Timer` objeto periodicamente irá disparar atualizações de preço independente de conexões de cliente.</span><span class="sxs-lookup"><span data-stu-id="39760-146">The `Timer` object will periodically trigger price updates independent of client connections.</span></span> <span data-ttu-id="39760-147">Você não pode colocar essas funções em um `Hub` de classe, como os Hubs são transitórios.</span><span class="sxs-lookup"><span data-stu-id="39760-147">You can't put these functions in a `Hub` class, because Hubs are transient.</span></span> <span data-ttu-id="39760-148">O aplicativo cria um `Hub` instância da classe para cada tarefa no hub, como conexões e chamadas do cliente para o servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-148">The app creates a `Hub` class instance for each task on the hub, like connections and calls from the client to the server.</span></span> <span data-ttu-id="39760-149">Portanto, o mecanismo que mantém os dados de estoque, atualiza os preços e transmite as atualizações de preço deve ser executado em uma classe separada.</span><span class="sxs-lookup"><span data-stu-id="39760-149">So the mechanism that keeps stock data, updates prices, and broadcasts the price updates has to run in a separate class.</span></span> <span data-ttu-id="39760-150">Você nomeará a classe `StockTicker`.</span><span class="sxs-lookup"><span data-stu-id="39760-150">You'll name the class `StockTicker`.</span></span>

![Transmitindo de StockTicker](tutorial-server-broadcast-with-signalr/_static/image3.png)

<span data-ttu-id="39760-152">Você deseja apenas uma instância das `StockTicker` classe a ser executada no servidor, portanto, você precisará configurar uma referência de cada `StockTickerHub` instância singleton `StockTicker` instância.</span><span class="sxs-lookup"><span data-stu-id="39760-152">You only want one instance of the `StockTicker` class to run on the server, so you'll need to set up a reference from each `StockTickerHub` instance to the singleton `StockTicker` instance.</span></span> <span data-ttu-id="39760-153">O `StockTicker` classe precisa transmitir para clientes, pois ele contém os dados de estoque e dispara atualizações, mas `StockTicker` não é um `Hub` classe.</span><span class="sxs-lookup"><span data-stu-id="39760-153">The `StockTicker` class has to broadcast to clients because it has the stock data and triggers updates, but `StockTicker` isn't a `Hub` class.</span></span> <span data-ttu-id="39760-154">O `StockTicker` classe precisar obter uma referência para o objeto de contexto de conexão do Hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="39760-154">The `StockTicker` class has to get a reference to the SignalR Hub connection context object.</span></span> <span data-ttu-id="39760-155">Ele pode usar o objeto de contexto de conexão do SignalR para transmissão aos clientes.</span><span class="sxs-lookup"><span data-stu-id="39760-155">It can then use the SignalR connection context object to broadcast to clients.</span></span>

#### <a name="create-stocktickerhubcs"></a><span data-ttu-id="39760-156">Criar StockTickerHub.cs</span><span class="sxs-lookup"><span data-stu-id="39760-156">Create StockTickerHub.cs</span></span>

1. <span data-ttu-id="39760-157">Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="39760-157">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="39760-158">Na **Adicionar Novo Item – SignalR.StockTicker**, selecione **instalado** > **Visual C#**   >  **Web**  >  **SignalR** e, em seguida, selecione **classe de Hub do SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="39760-158">In **Add New Item - SignalR.StockTicker**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="39760-159">Nomeie a classe *StockTickerHub* e adicioná-lo ao projeto.</span><span class="sxs-lookup"><span data-stu-id="39760-159">Name the class *StockTickerHub* and add it to the project.</span></span>

    <span data-ttu-id="39760-160">Esta etapa cria o *StockTickerHub.cs* arquivo de classe.</span><span class="sxs-lookup"><span data-stu-id="39760-160">This step creates the *StockTickerHub.cs* class file.</span></span> <span data-ttu-id="39760-161">Ao mesmo tempo, ele adiciona um conjunto de arquivos de script e referências de assembly que dá suporte ao SignalR ao projeto.</span><span class="sxs-lookup"><span data-stu-id="39760-161">Simultaneously, it adds a set of script files and assembly references that supports SignalR to the project.</span></span>

1. <span data-ttu-id="39760-162">Substitua o código na *StockTickerHub.cs* arquivo com este código:</span><span class="sxs-lookup"><span data-stu-id="39760-162">Replace the code in the *StockTickerHub.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample2.cs)]

1. <span data-ttu-id="39760-163">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="39760-163">Save the file.</span></span>

<span data-ttu-id="39760-164">O aplicativo usa o [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) classe para definir métodos que os clientes podem chamar no servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-164">The app uses the [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) class to define methods the clients can call on the server.</span></span> <span data-ttu-id="39760-165">Você está definindo um método: `GetAllStocks()`.</span><span class="sxs-lookup"><span data-stu-id="39760-165">You're defining one method: `GetAllStocks()`.</span></span> <span data-ttu-id="39760-166">Quando um cliente se conectará inicialmente ao servidor, ele chamará esse método para obter uma lista de todas as ações com seus preços atuais.</span><span class="sxs-lookup"><span data-stu-id="39760-166">When a client initially connects to the server, it will call this method to get a list of all of the stocks with their current prices.</span></span> <span data-ttu-id="39760-167">O método pode ser executado de forma síncrona e retornar `IEnumerable<Stock>` porque ele está retornando os dados da memória.</span><span class="sxs-lookup"><span data-stu-id="39760-167">The method can run synchronously and return `IEnumerable<Stock>` because it's returning data from memory.</span></span>

<span data-ttu-id="39760-168">Se o método tinha que obter os dados, fazendo algo que envolveria a espera, como uma pesquisa de banco de dados ou uma chamada de serviço web, você especificaria `Task<IEnumerable<Stock>>` como o valor de retorno para habilitar o processamento assíncrono.</span><span class="sxs-lookup"><span data-stu-id="39760-168">If the method had to get the data by doing something that would involve waiting, like a database lookup or a web service call, you would specify `Task<IEnumerable<Stock>>` as the return value to enable asynchronous processing.</span></span> <span data-ttu-id="39760-169">Para obter mais informações, consulte [o guia da API de Hubs de SignalR do ASP.NET - Server - quando executado de forma assíncrona](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).</span><span class="sxs-lookup"><span data-stu-id="39760-169">For more information, see [ASP.NET SignalR Hubs API Guide - Server - When to execute asynchronously](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).</span></span>

<span data-ttu-id="39760-170">O `HubName` atributo especifica como o aplicativo fará referência no Hub no código JavaScript no cliente.</span><span class="sxs-lookup"><span data-stu-id="39760-170">The `HubName` attribute specifies how the app will reference the Hub in JavaScript code on the client.</span></span> <span data-ttu-id="39760-171">O nome padrão no cliente se você não usar esse atributo, é uma versão de camelCase do nome de classe, que nesse caso, seria `stockTickerHub`.</span><span class="sxs-lookup"><span data-stu-id="39760-171">The default name on the client if you don't use this attribute, is a camelCase version of the class name, which in this case would be `stockTickerHub`.</span></span>

<span data-ttu-id="39760-172">Como você verá mais tarde quando você cria o `StockTicker` classe, o aplicativo cria uma instância singleton da classe no seu estático `Instance` propriedade.</span><span class="sxs-lookup"><span data-stu-id="39760-172">As you'll see later when you create the `StockTicker` class, the app creates a singleton instance of that class in its static `Instance` property.</span></span> <span data-ttu-id="39760-173">Essa instância singleton do `StockTicker` está na memória, independentemente de quantos clientes conectar ou desconectar.</span><span class="sxs-lookup"><span data-stu-id="39760-173">That singleton instance of `StockTicker` is in memory no matter how many clients connect or disconnect.</span></span> <span data-ttu-id="39760-174">Essa instância é o que o `GetAllStocks()` usa o método para retornar informações de estoque atuais.</span><span class="sxs-lookup"><span data-stu-id="39760-174">That instance is what the `GetAllStocks()` method uses to return current stock information.</span></span>

#### <a name="create-stocktickercs"></a><span data-ttu-id="39760-175">Criar StockTicker.cs</span><span class="sxs-lookup"><span data-stu-id="39760-175">Create StockTicker.cs</span></span>

1. <span data-ttu-id="39760-176">Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="39760-176">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="39760-177">Nomeie a classe *StockTicker* e adicioná-lo ao projeto.</span><span class="sxs-lookup"><span data-stu-id="39760-177">Name the class *StockTicker* and add it to the project.</span></span>

1. <span data-ttu-id="39760-178">Substitua o código na *StockTicker.cs* arquivo com este código:</span><span class="sxs-lookup"><span data-stu-id="39760-178">Replace the code in the *StockTicker.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample3.cs)]

<span data-ttu-id="39760-179">Como todos os threads executará a mesma instância do código StockTicker, a classe StockTicker deve ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="39760-179">Since all threads will be running the same instance of StockTicker code, the StockTicker class has to be thread-safe.</span></span>

### <a name="examine-the-server-code"></a><span data-ttu-id="39760-180">Examinar o código de servidor</span><span class="sxs-lookup"><span data-stu-id="39760-180">Examine the server code</span></span>

<span data-ttu-id="39760-181">Se você examinar o código do servidor, ele ajudará a entender como o aplicativo funciona.</span><span class="sxs-lookup"><span data-stu-id="39760-181">If you examine the server code, it will help you understand how the app works.</span></span>

#### <a name="storing-the-singleton-instance-in-a-static-field"></a><span data-ttu-id="39760-182">Armazenar a instância singleton em um campo estático</span><span class="sxs-lookup"><span data-stu-id="39760-182">Storing the singleton instance in a static field</span></span>

<span data-ttu-id="39760-183">O código inicializa estático `_instance` campo que dá suporte a `Instance` propriedade com uma instância da classe.</span><span class="sxs-lookup"><span data-stu-id="39760-183">The code initializes the static `_instance` field that backs the `Instance` property with an instance of the class.</span></span> <span data-ttu-id="39760-184">Como o construtor é particular, é a única instância da classe que o aplicativo pode criar.</span><span class="sxs-lookup"><span data-stu-id="39760-184">Because the constructor is private, it's the only instance of the class that the app can create.</span></span> <span data-ttu-id="39760-185">O aplicativo usa [inicialização lenta](/dotnet/framework/performance/lazy-initialization) para o `_instance` campo.</span><span class="sxs-lookup"><span data-stu-id="39760-185">The app uses [Lazy initialization](/dotnet/framework/performance/lazy-initialization) for the `_instance` field.</span></span> <span data-ttu-id="39760-186">Não é por motivos de desempenho.</span><span class="sxs-lookup"><span data-stu-id="39760-186">It's not for performance reasons.</span></span> <span data-ttu-id="39760-187">É garantir que a criação de instância é thread-safe.</span><span class="sxs-lookup"><span data-stu-id="39760-187">It's to make sure the instance creation is thread-safe.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample4.cs)]

<span data-ttu-id="39760-188">Sempre que um cliente se conecta ao servidor, uma nova instância da classe StockTickerHub em execução em um thread separado obtém a instância singleton StockTicker a `StockTicker.Instance` uma propriedade estática, como você viu anteriormente no `StockTickerHub` classe.</span><span class="sxs-lookup"><span data-stu-id="39760-188">Each time a client connects to the server, a new instance of the StockTickerHub class running in a separate thread gets the StockTicker singleton instance from the `StockTicker.Instance` static property, as you saw earlier in the `StockTickerHub` class.</span></span>

#### <a name="storing-stock-data-in-a-concurrentdictionary"></a><span data-ttu-id="39760-189">Armazenar dados de estoque em um ConcurrentDictionary</span><span class="sxs-lookup"><span data-stu-id="39760-189">Storing stock data in a ConcurrentDictionary</span></span>

<span data-ttu-id="39760-190">O construtor inicializa o `_stocks` coleção com alguns dados de estoque de exemplo e `GetAllStocks` retorna as ações.</span><span class="sxs-lookup"><span data-stu-id="39760-190">The constructor initializes the `_stocks` collection with some sample stock data, and `GetAllStocks` returns the stocks.</span></span> <span data-ttu-id="39760-191">Como você viu anteriormente, essa coleção de ações é retornada por `StockTickerHub.GetAllStocks`, que é um método de servidor no `Hub` classe que os clientes poderão chamar.</span><span class="sxs-lookup"><span data-stu-id="39760-191">As you saw earlier, this collection of stocks is returned by `StockTickerHub.GetAllStocks`, which is a server method in the `Hub` class that clients can call.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample5.cs)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample6.cs)]

<span data-ttu-id="39760-192">A coleção de ações está definida como uma [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) tipo para acesso thread-safe.</span><span class="sxs-lookup"><span data-stu-id="39760-192">The stocks collection is defined as a [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) type for thread safety.</span></span> <span data-ttu-id="39760-193">Como alternativa, você poderia usar um [dicionário](https://msdn.microsoft.com/library/xfhwa508.aspx) de objeto e bloquear explicitamente o dicionário quando você fizer alterações a ele.</span><span class="sxs-lookup"><span data-stu-id="39760-193">As an alternative, you could use a [Dictionary](https://msdn.microsoft.com/library/xfhwa508.aspx) object and explicitly lock the dictionary when you make changes to it.</span></span>

<span data-ttu-id="39760-194">Para este aplicativo de exemplo é Okey para armazenar dados de aplicativo na memória e a perda de dados quando o aplicativo descarta o `StockTicker` instância.</span><span class="sxs-lookup"><span data-stu-id="39760-194">For this sample application, it's OK to store application data in memory and to lose the data when the app disposes of the  `StockTicker` instance.</span></span> <span data-ttu-id="39760-195">Em um aplicativo real, você deve trabalhar com um repositório de dados back-end, como um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="39760-195">In a real application, you would work with a back-end data store like a database.</span></span>

#### <a name="periodically-updating-stock-prices"></a><span data-ttu-id="39760-196">Atualizar periodicamente os preços de ações</span><span class="sxs-lookup"><span data-stu-id="39760-196">Periodically updating stock prices</span></span>

<span data-ttu-id="39760-197">O construtor é iniciado um `Timer` objeto que chama os métodos que atualizam os preços de ações de forma aleatória.</span><span class="sxs-lookup"><span data-stu-id="39760-197">The constructor starts up a `Timer` object that periodically calls methods that update stock prices on a random basis.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample7.cs)]

 <span data-ttu-id="39760-198">`Timer` chamadas `UpdateStockPrices`, que passa nulo no parâmetro state.</span><span class="sxs-lookup"><span data-stu-id="39760-198">`Timer` calls `UpdateStockPrices`, which passes in null in the state parameter.</span></span> <span data-ttu-id="39760-199">Antes de atualizar os preços, o aplicativo usa um bloqueio no `_updateStockPricesLock` objeto.</span><span class="sxs-lookup"><span data-stu-id="39760-199">Before updating prices, the app takes a lock on the `_updateStockPricesLock` object.</span></span> <span data-ttu-id="39760-200">O código verifica se outro thread já está atualizando os preços e, em seguida, ele chama `TryUpdateStockPrice` em cada ação na lista.</span><span class="sxs-lookup"><span data-stu-id="39760-200">The code checks if another thread is already updating prices, and then it calls `TryUpdateStockPrice` on each stock in the list.</span></span> <span data-ttu-id="39760-201">O `TryUpdateStockPrice` método decide se deve alterar o preço da ação e quanto para alterá-lo.</span><span class="sxs-lookup"><span data-stu-id="39760-201">The `TryUpdateStockPrice` method decides whether to change the stock price, and how much to change it.</span></span> <span data-ttu-id="39760-202">Se o preço da ação for alterada, o aplicativo chama `BroadcastStockPrice` difundir a alteração de preço de estoque para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="39760-202">If the stock price changes, the app calls `BroadcastStockPrice` to broadcast the stock price change to all connected clients.</span></span>

<span data-ttu-id="39760-203">O `_updatingStockPrices` sinalizador designado [volátil](https://msdn.microsoft.com/library/x13ttww7.aspx) para garantir que ele é thread-safe.</span><span class="sxs-lookup"><span data-stu-id="39760-203">The `_updatingStockPrices` flag designated [volatile](https://msdn.microsoft.com/library/x13ttww7.aspx) to make sure it is thread-safe.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample8.cs)]

<span data-ttu-id="39760-204">Em um aplicativo real, o `TryUpdateStockPrice` um serviço web para pesquisar o preço seria chamada de método.</span><span class="sxs-lookup"><span data-stu-id="39760-204">In a real application, the `TryUpdateStockPrice` method would call a web service to look up the price.</span></span> <span data-ttu-id="39760-205">Nesse código, o aplicativo usa um gerador de número aleatório para fazer alterações aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="39760-205">In this code, the app uses a random number generator to make changes randomly.</span></span>

#### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a><span data-ttu-id="39760-206">Obtendo o contexto de SignalR, de modo que a classe StockTicker pode transmitir para clientes</span><span class="sxs-lookup"><span data-stu-id="39760-206">Getting the SignalR context so that the StockTicker class can broadcast to clients</span></span>

<span data-ttu-id="39760-207">Como as alterações de preço se originam aqui na `StockTicker` do objeto, ele é o objeto que precisa chamar uma `updateStockPrice` método em todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="39760-207">Because the price changes originate here in the `StockTicker` object, it's the object that needs to call an `updateStockPrice` method on all connected clients.</span></span> <span data-ttu-id="39760-208">Em um `Hub` classe, que você tenha uma API para chamar métodos de cliente, mas `StockTicker` não deriva da `Hub` classe e não tem uma referência a qualquer `Hub` objeto.</span><span class="sxs-lookup"><span data-stu-id="39760-208">In a `Hub` class, you have an API for calling client methods, but `StockTicker` doesn't derive from the `Hub` class and doesn't have a reference to any `Hub` object.</span></span> <span data-ttu-id="39760-209">Difundir aos clientes conectados, o `StockTicker` classe precisar obter a instância de contexto do SignalR para o `StockTickerHub` de classe e usá-la para chamar métodos em clientes.</span><span class="sxs-lookup"><span data-stu-id="39760-209">To broadcast to connected clients, the `StockTicker` class has to get the SignalR context instance for the `StockTickerHub` class and use that to call methods on clients.</span></span>

<span data-ttu-id="39760-210">O código obtém uma referência ao contexto de SignalR quando ele cria a instância singleton da classe, passa que fazem referência ao construtor, e o construtor coloca-o no `Clients` propriedade.</span><span class="sxs-lookup"><span data-stu-id="39760-210">The code gets a reference to the SignalR context when it creates the singleton class instance, passes that reference to the constructor, and the constructor puts it in the `Clients` property.</span></span>

<span data-ttu-id="39760-211">Há duas razões por que você deseja obter o contexto de somente uma vez: obtendo o contexto é uma tarefa cara e colocá-los depois que faz com que o aplicativo preserva a ordem planejada de mensagens enviadas para os clientes.</span><span class="sxs-lookup"><span data-stu-id="39760-211">There are two reasons why you want to get the context only once: getting the context is an expensive task, and getting it once makes sure the app preserves the intended order of messages sent to the clients.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample9.cs)]

<span data-ttu-id="39760-212">Introdução a `Clients` propriedade de contexto e colocá-lo na `StockTickerClient` propriedade permite que você escreva código para chamar métodos de cliente que se parece o mesmo como faria em um `Hub` classe.</span><span class="sxs-lookup"><span data-stu-id="39760-212">Getting the `Clients` property of the context and putting it in the `StockTickerClient` property lets you write code to call client methods that looks the same as it would in a `Hub` class.</span></span> <span data-ttu-id="39760-213">Por exemplo difundir a todos os clientes que você pode escrever `Clients.All.updateStockPrice(stock)`.</span><span class="sxs-lookup"><span data-stu-id="39760-213">For instance, to broadcast to all clients you can write `Clients.All.updateStockPrice(stock)`.</span></span>

<span data-ttu-id="39760-214">O `updateStockPrice` método que você está chamando em `BroadcastStockPrice` ainda não exista.</span><span class="sxs-lookup"><span data-stu-id="39760-214">The `updateStockPrice` method that you're calling in `BroadcastStockPrice` doesn't exist yet.</span></span> <span data-ttu-id="39760-215">Você vai adicioná-lo mais tarde quando você escreve o código que é executado no cliente.</span><span class="sxs-lookup"><span data-stu-id="39760-215">You'll add it later when you write code that runs on the client.</span></span> <span data-ttu-id="39760-216">Você pode consultar `updateStockPrice` aqui porque `Clients.All` é dinâmico, o que significa que o aplicativo irá avaliar a expressão em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="39760-216">You can refer to `updateStockPrice` here because `Clients.All` is dynamic, which means the app will evaluate the expression at runtime.</span></span> <span data-ttu-id="39760-217">Quando esta chamada de método é executado, o SignalR enviará o nome do método e o valor do parâmetro para o cliente, e se o cliente tem um método chamado `updateStockPrice`, o aplicativo irá chamar esse método e passar o valor do parâmetro a ele.</span><span class="sxs-lookup"><span data-stu-id="39760-217">When this method call executes, SignalR will send the method name and the parameter value to the client, and if the client has a method named `updateStockPrice`, the app will call that method and pass the parameter value to it.</span></span>

<span data-ttu-id="39760-218">`Clients.All` significa que envia a todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="39760-218">`Clients.All` means send to all clients.</span></span> <span data-ttu-id="39760-219">O SignalR fornece outras opções para especificar quais clientes ou grupos de clientes para enviar para.</span><span class="sxs-lookup"><span data-stu-id="39760-219">SignalR gives you other options to specify which clients or groups of clients to send to.</span></span> <span data-ttu-id="39760-220">Para obter mais informações, consulte [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="39760-220">For more information, see [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).</span></span>

### <a name="register-the-signalr-route"></a><span data-ttu-id="39760-221">Registre-se a rota de SignalR</span><span class="sxs-lookup"><span data-stu-id="39760-221">Register the SignalR route</span></span>

<span data-ttu-id="39760-222">O servidor precisa saber qual URL para interceptar e direcionar ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="39760-222">The server needs to know which URL to intercept and direct to SignalR.</span></span> <span data-ttu-id="39760-223">Para fazer isso, adicione uma classe de inicialização OWIN:</span><span class="sxs-lookup"><span data-stu-id="39760-223">To do that, add an OWIN startup class:</span></span>

1. <span data-ttu-id="39760-224">Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="39760-224">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="39760-225">Na **Adicionar Novo Item – SignalR.StockTicker** selecionar **instalado** > **Visual C#**   >  **Web** e em seguida, selecione **classe de inicialização OWIN**.</span><span class="sxs-lookup"><span data-stu-id="39760-225">In **Add New Item - SignalR.StockTicker** select **Installed** > **Visual C#** > **Web** and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="39760-226">Nomeie a classe *inicialização* e selecione **Okey**.</span><span class="sxs-lookup"><span data-stu-id="39760-226">Name the class *Startup* and select **OK**.</span></span>

1. <span data-ttu-id="39760-227">Substitua o código padrão a *Startup.cs* arquivo com este código:</span><span class="sxs-lookup"><span data-stu-id="39760-227">Replace the default code in the *Startup.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample10.cs)]

<span data-ttu-id="39760-228">Você terminou de configurar o código do servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-228">You have now finished setting up the server code.</span></span> <span data-ttu-id="39760-229">Na próxima seção, você irá configurar o cliente.</span><span class="sxs-lookup"><span data-stu-id="39760-229">In the next section, you'll set up the client.</span></span>

## <a name="set-up-the-client-code"></a><span data-ttu-id="39760-230">Configure o código de cliente</span><span class="sxs-lookup"><span data-stu-id="39760-230">Set up the client code</span></span>

<span data-ttu-id="39760-231">Nesta seção, você deve definir o código que é executado no cliente.</span><span class="sxs-lookup"><span data-stu-id="39760-231">In this section, you set up the code that runs on the client.</span></span>

### <a name="create-the-html-page-and-javascript-file"></a><span data-ttu-id="39760-232">Criar a página HTML e um arquivo JavaScript</span><span class="sxs-lookup"><span data-stu-id="39760-232">Create the HTML page and JavaScript file</span></span>

<span data-ttu-id="39760-233">A página HTML exibirá os dados e o arquivo JavaScript organizará os dados.</span><span class="sxs-lookup"><span data-stu-id="39760-233">The HTML page will display the data and the JavaScript file will organize the data.</span></span>

#### <a name="create-stocktickerhtml"></a><span data-ttu-id="39760-234">Create StockTicker.html</span><span class="sxs-lookup"><span data-stu-id="39760-234">Create StockTicker.html</span></span>

<span data-ttu-id="39760-235">Primeiro, você adicionará o cliente HTML.</span><span class="sxs-lookup"><span data-stu-id="39760-235">First, you'll add the HTML client.</span></span>

1. <span data-ttu-id="39760-236">Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **página HTML**.</span><span class="sxs-lookup"><span data-stu-id="39760-236">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="39760-237">Nomeie o arquivo *StockTicker* e selecione **Okey**.</span><span class="sxs-lookup"><span data-stu-id="39760-237">Name the file *StockTicker* and select **OK**.</span></span>

1. <span data-ttu-id="39760-238">Substitua o código padrão a *StockTicker.html* arquivo com este código:</span><span class="sxs-lookup"><span data-stu-id="39760-238">Replace the default code in the *StockTicker.html* file with this code:</span></span>

    [!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample11.html?highlight=40-43)]

    <span data-ttu-id="39760-239">O HTML cria uma tabela com cinco colunas, uma linha de cabeçalho e uma linha de dados com uma única célula que abrange todas as cinco colunas.</span><span class="sxs-lookup"><span data-stu-id="39760-239">The HTML creates a table with five columns, a header row, and a data row with a single cell that spans all five columns.</span></span> <span data-ttu-id="39760-240">A linha de dados mostra "Carregando..." momentaneamente, quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="39760-240">The data row shows "loading..." momentarily when the app starts.</span></span> <span data-ttu-id="39760-241">O código JavaScript removerá essa linha e adicione em suas linhas de local com dados recuperados do servidor de ações.</span><span class="sxs-lookup"><span data-stu-id="39760-241">JavaScript code will remove that row and add in its place rows with stock data retrieved from the server.</span></span>

    <span data-ttu-id="39760-242">Especificam as marcas de script:</span><span class="sxs-lookup"><span data-stu-id="39760-242">The script tags specify:</span></span>

    * <span data-ttu-id="39760-243">O arquivo de script do jQuery.</span><span class="sxs-lookup"><span data-stu-id="39760-243">The jQuery script file.</span></span>

    * <span data-ttu-id="39760-244">O arquivo de script do SignalR core.</span><span class="sxs-lookup"><span data-stu-id="39760-244">The SignalR core script file.</span></span>

    * <span data-ttu-id="39760-245">O arquivo de script de proxies do SignalR.</span><span class="sxs-lookup"><span data-stu-id="39760-245">The SignalR proxies script file.</span></span>

    * <span data-ttu-id="39760-246">Um arquivo de script StockTicker que você criará posteriormente.</span><span class="sxs-lookup"><span data-stu-id="39760-246">A StockTicker script file that you'll create later.</span></span>

    <span data-ttu-id="39760-247">O aplicativo gera dinamicamente o arquivo de script de proxies do SignalR.</span><span class="sxs-lookup"><span data-stu-id="39760-247">The app dynamically generates the SignalR proxies script file.</span></span> <span data-ttu-id="39760-248">Ele especifica a URL "hubs de signalr /" e define os métodos de proxy para os métodos na classe Hub, nesse caso, para `StockTickerHub.GetAllStocks`.</span><span class="sxs-lookup"><span data-stu-id="39760-248">It specifies the "/signalr/hubs" URL and defines proxy methods for the methods on the Hub class, in this case, for `StockTickerHub.GetAllStocks`.</span></span> <span data-ttu-id="39760-249">Se você preferir, você pode gerar esse arquivo JavaScript manualmente usando [SignalR utilitários](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/).</span><span class="sxs-lookup"><span data-stu-id="39760-249">If you prefer, you can generate this JavaScript file manually by using [SignalR Utilities](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/).</span></span> <span data-ttu-id="39760-250">Não se esqueça de desativar a criação dinâmica de arquivos no `MapHubs` chamada de método.</span><span class="sxs-lookup"><span data-stu-id="39760-250">Don't forget to disable dynamic file creation in the `MapHubs` method call.</span></span>

1. <span data-ttu-id="39760-251">Na **Gerenciador de soluções**, expanda **Scripts**.</span><span class="sxs-lookup"><span data-stu-id="39760-251">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="39760-252">Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.</span><span class="sxs-lookup"><span data-stu-id="39760-252">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="39760-253">O Gerenciador de pacotes instalará uma versão mais recente dos scripts do SignalR.</span><span class="sxs-lookup"><span data-stu-id="39760-253">The package manager will install a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="39760-254">Atualize as referências de script no bloco de código para corresponder às versões dos arquivos de script no projeto.</span><span class="sxs-lookup"><span data-stu-id="39760-254">Update the script references in the code block to correspond to the versions of the script files in the project.</span></span>

1. <span data-ttu-id="39760-255">Na **Gerenciador de soluções**, clique com botão direito *StockTicker.html*e, em seguida, selecione **Set as Start Page**.</span><span class="sxs-lookup"><span data-stu-id="39760-255">In **Solution Explorer**, right-click *StockTicker.html*, and then select **Set as Start Page**.</span></span>

#### <a name="create-stocktickerjs"></a><span data-ttu-id="39760-256">Criar StockTicker.js</span><span class="sxs-lookup"><span data-stu-id="39760-256">Create StockTicker.js</span></span>

<span data-ttu-id="39760-257">Agora, crie o arquivo de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="39760-257">Now create the JavaScript file.</span></span>

1. <span data-ttu-id="39760-258">Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **arquivo JavaScript**.</span><span class="sxs-lookup"><span data-stu-id="39760-258">In **Solution Explorer**, right-click the project and select **Add** > **JavaScript File**.</span></span>

1. <span data-ttu-id="39760-259">Nomeie o arquivo *StockTicker* e selecione **Okey**.</span><span class="sxs-lookup"><span data-stu-id="39760-259">Name the file *StockTicker* and select **OK**.</span></span>

1. <span data-ttu-id="39760-260">Adicione este código para o *StockTicker.js* arquivo:</span><span class="sxs-lookup"><span data-stu-id="39760-260">Add this code to the *StockTicker.js* file:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample12.js)]

### <a name="examine-the-client-code"></a><span data-ttu-id="39760-261">Examinar o código do cliente</span><span class="sxs-lookup"><span data-stu-id="39760-261">Examine the client code</span></span>

<span data-ttu-id="39760-262">Se você examinar o código do cliente, ele ajudará a aprender como o código do cliente interage com o código do servidor para fazer com que o aplicativo funcione.</span><span class="sxs-lookup"><span data-stu-id="39760-262">If you examine the client code, it will help you learn how the client code interacts with the server code to make the app work.</span></span>

#### <a name="starting-the-connection"></a><span data-ttu-id="39760-263">Iniciando a conexão</span><span class="sxs-lookup"><span data-stu-id="39760-263">Starting the connection</span></span>

<span data-ttu-id="39760-264">`$.connection` refere-se para os proxies do SignalR.</span><span class="sxs-lookup"><span data-stu-id="39760-264">`$.connection` refers to the SignalR proxies.</span></span> <span data-ttu-id="39760-265">O código obtém uma referência para o proxy para o `StockTickerHub` de classe e o coloca no `ticker` variável.</span><span class="sxs-lookup"><span data-stu-id="39760-265">The code gets a reference to the proxy for the `StockTickerHub` class and puts it in the `ticker` variable.</span></span> <span data-ttu-id="39760-266">O nome do proxy é o nome que foi definido pelo `HubName` atributo:</span><span class="sxs-lookup"><span data-stu-id="39760-266">The proxy name is the name that was set by the `HubName` attribute:</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample13.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample14.cs)]

<span data-ttu-id="39760-267">Depois de definir todas as variáveis e funções, a última linha do código no arquivo inicializa a conexão do SignalR, chamando o SignalR `start` função.</span><span class="sxs-lookup"><span data-stu-id="39760-267">After you define all the variables and functions, the last line of code in the file initializes the SignalR connection by calling the SignalR `start` function.</span></span> <span data-ttu-id="39760-268">O `start` função executa de forma assíncrona e retorna um [jQuery adiado objeto](http://api.jquery.com/category/deferred-object/).</span><span class="sxs-lookup"><span data-stu-id="39760-268">The `start` function executes asynchronously and returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/).</span></span> <span data-ttu-id="39760-269">Você pode chamar a função done para especificar a função ser chamada quando o aplicativo conclua a ação assíncrona.</span><span class="sxs-lookup"><span data-stu-id="39760-269">You can call the done function to specify the function to call when the app finishes the asynchronous action.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample15.js)]

#### <a name="getting-all-the-stocks"></a><span data-ttu-id="39760-270">Obter todas as ações</span><span class="sxs-lookup"><span data-stu-id="39760-270">Getting all the stocks</span></span>

<span data-ttu-id="39760-271">O `init` chamadas de função a `getAllStocks` função no servidor e usa as informações que o servidor retorna para atualizar a tabela de estoque.</span><span class="sxs-lookup"><span data-stu-id="39760-271">The `init` function calls the `getAllStocks` function on the server and uses the information that the server returns to update the stock table.</span></span> <span data-ttu-id="39760-272">Observe que, por padrão, você precisa usar camelCasing no cliente, mesmo que o nome do método é o padrão Pascal-case no servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-272">Notice that, by default, you have to use camelCasing on the client even though the method name is pascal-cased on the server.</span></span> <span data-ttu-id="39760-273">A regra camelCasing só se aplica aos métodos, não objetos.</span><span class="sxs-lookup"><span data-stu-id="39760-273">The camelCasing rule only applies to methods, not objects.</span></span> <span data-ttu-id="39760-274">Por exemplo, você consultar `stock.Symbol` e `stock.Price`, e não `stock.symbol` ou `stock.price`.</span><span class="sxs-lookup"><span data-stu-id="39760-274">For example, you refer to `stock.Symbol` and `stock.Price`, not `stock.symbol` or `stock.price`.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample16.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample17.cs)]

<span data-ttu-id="39760-275">No `init` método, o aplicativo cria o HTML para uma linha da tabela para cada objeto de estoque recebido do servidor chamando `formatStock` às propriedades de formato da `stock` do objeto e, em seguida, chamando `supplant` substituir espaços reservados no `rowTemplate` variável com o `stock` valores de propriedade do objeto.</span><span class="sxs-lookup"><span data-stu-id="39760-275">In the `init` method, the app creates HTML for a table row for each stock object received from the server by calling `formatStock` to format properties of the `stock` object, and then by calling `supplant` to replace placeholders in the `rowTemplate` variable with the `stock` object property values.</span></span> <span data-ttu-id="39760-276">O HTML resultante, em seguida, é acrescentado à tabela de estoque.</span><span class="sxs-lookup"><span data-stu-id="39760-276">The resulting HTML is then appended to the stock table.</span></span>

> [!NOTE]
> <span data-ttu-id="39760-277">Você chama `init` passando a como uma `callback` função que executa após assíncrona `start` função é concluída.</span><span class="sxs-lookup"><span data-stu-id="39760-277">You call `init` by passing it in as a `callback` function that executes after the asynchronous `start` function finishes.</span></span> <span data-ttu-id="39760-278">Se você chamasse `init` como uma instrução JavaScript separada depois de chamar `start`, a função falharia, pois ele seria executado imediatamente sem esperar que a função do início ao fim de estabelecer a conexão.</span><span class="sxs-lookup"><span data-stu-id="39760-278">If you called `init` as a separate JavaScript statement after calling `start`, the function would fail because it would run immediately without waiting for the start function to finish establishing the connection.</span></span> <span data-ttu-id="39760-279">Nesse caso, o `init` função tentar chamar o `getAllStocks` antes do aplicativo estabelece uma conexão de servidor de função.</span><span class="sxs-lookup"><span data-stu-id="39760-279">In that case, the `init` function would try to call the `getAllStocks` function before the app establishes a server connection.</span></span>

#### <a name="getting-updated-stock-prices"></a><span data-ttu-id="39760-280">Obter cotações atualizadas</span><span class="sxs-lookup"><span data-stu-id="39760-280">Getting updated stock prices</span></span>

<span data-ttu-id="39760-281">Quando o servidor alterará o preço de uma ação, ele chama o `updateStockPrice` em clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="39760-281">When the server changes a stock's price, it calls the `updateStockPrice` on connected clients.</span></span> <span data-ttu-id="39760-282">O aplicativo adiciona a função à propriedade do cliente a `stockTicker` proxy para disponibilizá-lo para chamadas do servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-282">The app adds the function to the client property of the `stockTicker` proxy to make it available to calls from the server.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample18.js)]

<span data-ttu-id="39760-283">O `updateStockPrice` formatos de função da mesma forma como em de linha de um objeto fixo recebido do servidor em uma tabela de `init` função.</span><span class="sxs-lookup"><span data-stu-id="39760-283">The `updateStockPrice` function formats a stock object received from the server into a table row the same way as in the `init` function.</span></span> <span data-ttu-id="39760-284">Em vez de acrescentar a linha na tabela, ele localiza a linha atual do estoque na tabela e substitui aquela linha por um novo.</span><span class="sxs-lookup"><span data-stu-id="39760-284">Instead of appending the row to the table, it finds the stock's current row in the table and replaces that row with the new one.</span></span>

## <a name="test-the-application"></a><span data-ttu-id="39760-285">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="39760-285">Test the application</span></span>

<span data-ttu-id="39760-286">Você pode testar o aplicativo para verificar se ele está funcionando.</span><span class="sxs-lookup"><span data-stu-id="39760-286">You can test the app to make sure it's working.</span></span> <span data-ttu-id="39760-287">Você verá todas as janelas de navegador exibir a tabela de estoque em tempo real com cotações flutuando.</span><span class="sxs-lookup"><span data-stu-id="39760-287">You'll see all browser windows display the live stock table with stock prices fluctuating.</span></span>

1. <span data-ttu-id="39760-288">Na barra de ferramentas, ative **depuração de Script** e, em seguida, selecione o botão Reproduzir para executar o aplicativo no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="39760-288">In the toolbar, turn on **Script Debugging** and then select the play button to run the app in Debug mode.</span></span>

    ![Captura de tela do usuário ativar o modo de depuração e selecionando play.](tutorial-server-broadcast-with-signalr/_static/image4.png)

    <span data-ttu-id="39760-290">Uma janela do navegador será aberta exibindo a **tabela de estoque ao vivo**.</span><span class="sxs-lookup"><span data-stu-id="39760-290">A browser window will open displaying the **Live Stock Table**.</span></span> <span data-ttu-id="39760-291">A tabela de estoque inicialmente mostra a linha "Carregando...", em seguida, após alguns instantes, o aplicativo mostra os dados iniciais de estoque e, em seguida, os preços das ações começam a mudar.</span><span class="sxs-lookup"><span data-stu-id="39760-291">The stock table initially shows the "loading..." line, then, after a short time, the app shows the initial stock data, and then the stock prices start to change.</span></span>

1. <span data-ttu-id="39760-292">Copie a URL do navegador, abra dois outros navegadores e cole as URLs em barras de endereço.</span><span class="sxs-lookup"><span data-stu-id="39760-292">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

    <span data-ttu-id="39760-293">A exibição de estoque inicial é o mesmo que o navegador primeiro e as alterações ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="39760-293">The initial stock display is the same as the first browser and changes happen simultaneously.</span></span>

1. <span data-ttu-id="39760-294">Feche todos os navegadores, abra um novo navegador e vá para a mesma URL.</span><span class="sxs-lookup"><span data-stu-id="39760-294">Close all browsers, open a new browser, and go to the same URL.</span></span>

    <span data-ttu-id="39760-295">O objeto de singleton StockTicker continuou a ser executado no servidor.</span><span class="sxs-lookup"><span data-stu-id="39760-295">The StockTicker singleton object continued to run in the server.</span></span> <span data-ttu-id="39760-296">O **tabela de estoque ao vivo** mostra que as ações continuam a alterar.</span><span class="sxs-lookup"><span data-stu-id="39760-296">The **Live Stock Table** shows that the stocks have continued to change.</span></span> <span data-ttu-id="39760-297">Você não vir a tabela inicial com zero alterar figuras.</span><span class="sxs-lookup"><span data-stu-id="39760-297">You don't see the initial table with zero change figures.</span></span>

1. <span data-ttu-id="39760-298">Feche o navegador.</span><span class="sxs-lookup"><span data-stu-id="39760-298">Close the browser.</span></span>

## <a name="enable-logging"></a><span data-ttu-id="39760-299">Habilite o registro em logs</span><span class="sxs-lookup"><span data-stu-id="39760-299">Enable logging</span></span>

<span data-ttu-id="39760-300">O SignalR tem uma função de registro em log interno que pode ser habilitado no cliente para ajudar na solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="39760-300">SignalR has a built-in logging function that you can enable on the client to aid in troubleshooting.</span></span> <span data-ttu-id="39760-301">Nesta seção, você pode habilitar o log e ver exemplos que mostram como os logs de lhe dizer que um dos seguintes métodos de transporte está usando o SignalR:</span><span class="sxs-lookup"><span data-stu-id="39760-301">In this section, you enable logging and see examples that show how logs tell you which of the following transport methods SignalR is using:</span></span>

* <span data-ttu-id="39760-302">[WebSockets](http://en.wikipedia.org/wiki/WebSocket), com suporte pelo IIS 8 e navegadores atuais.</span><span class="sxs-lookup"><span data-stu-id="39760-302">[WebSockets](http://en.wikipedia.org/wiki/WebSocket), supported by IIS 8 and current browsers.</span></span>

* <span data-ttu-id="39760-303">[Eventos enviados pelo servidor](http://en.wikipedia.org/wiki/Server-sent_events), com suporte por navegadores diferentes do Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="39760-303">[Server-sent events](http://en.wikipedia.org/wiki/Server-sent_events), supported by browsers other than Internet Explorer.</span></span>

* <span data-ttu-id="39760-304">[Para sempre quadro](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), com suporte pelo Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="39760-304">[Forever frame](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), supported by Internet Explorer.</span></span>

* <span data-ttu-id="39760-305">[AJAX sondagem longa](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), com suporte por todos os navegadores.</span><span class="sxs-lookup"><span data-stu-id="39760-305">[Ajax long polling](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), supported by all browsers.</span></span>

<span data-ttu-id="39760-306">Para qualquer determinada conexão, o SignalR escolhe o melhor método de transporte que dão suporte a servidor e o cliente.</span><span class="sxs-lookup"><span data-stu-id="39760-306">For any given connection, SignalR chooses the best transport method that both the server and the client support.</span></span>

1. <span data-ttu-id="39760-307">Abra *StockTicker.js*.</span><span class="sxs-lookup"><span data-stu-id="39760-307">Open *StockTicker.js*.</span></span>

1. <span data-ttu-id="39760-308">Adicione esta linha de código para habilitar o log imediatamente antes do código que inicializa a conexão no final do arquivo realçada:</span><span class="sxs-lookup"><span data-stu-id="39760-308">Add this highlighted line of code to enable logging immediately before the code that initializes the connection at the end of the file:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample19.js?highlight=2)]

1. <span data-ttu-id="39760-309">Pressione **F5** para executar o projeto.</span><span class="sxs-lookup"><span data-stu-id="39760-309">Press **F5** to run the project.</span></span>

1. <span data-ttu-id="39760-310">Abra a janela de ferramentas de desenvolvedor do navegador e selecione o Console para ver os logs.</span><span class="sxs-lookup"><span data-stu-id="39760-310">Open your browser's developer tools window, and select the Console to see the logs.</span></span> <span data-ttu-id="39760-311">Você talvez precise atualizar a página para ver os logs do SignalR negociar o método de transporte para uma nova conexão.</span><span class="sxs-lookup"><span data-stu-id="39760-311">You might have to refresh the page to see the logs of SignalR negotiating the transport method for a new connection.</span></span>

    * <span data-ttu-id="39760-312">Se você estiver executando o Internet Explorer 10 no Windows 8 (IIS 8), o método de transporte está **WebSockets**.</span><span class="sxs-lookup"><span data-stu-id="39760-312">If you're running Internet Explorer 10 on Windows 8 (IIS 8), the transport method is **WebSockets**.</span></span>

    * <span data-ttu-id="39760-313">Se você estiver executando o Internet Explorer 10 no Windows 7 (IIS 7.5), o método de transporte está **iframe**.</span><span class="sxs-lookup"><span data-stu-id="39760-313">If you're running Internet Explorer 10 on Windows 7 (IIS 7.5), the transport method is **iframe**.</span></span>

    * <span data-ttu-id="39760-314">Se você estiver executando 19 Firefox no Windows 8 (IIS 8), o método de transporte é **WebSockets**.</span><span class="sxs-lookup"><span data-stu-id="39760-314">If you're running Firefox 19 on Windows 8 (IIS 8), the transport method is **WebSockets**.</span></span>

        > [!TIP]
        > <span data-ttu-id="39760-315">No Firefox, instale o suplemento Firebug para obter uma janela do Console.</span><span class="sxs-lookup"><span data-stu-id="39760-315">In Firefox, install the Firebug add-in to get a Console window.</span></span>

    * <span data-ttu-id="39760-316">Se você estiver executando 19 Firefox no Windows 7 (IIS 7.5), o método de transporte é **enviados ao servidor** eventos.</span><span class="sxs-lookup"><span data-stu-id="39760-316">If you're running Firefox 19 on Windows 7 (IIS 7.5), the transport method is **server-sent** events.</span></span>

## <a name="install-the-stockticker-sample"></a><span data-ttu-id="39760-317">Instalar o exemplo StockTicker</span><span class="sxs-lookup"><span data-stu-id="39760-317">Install the StockTicker sample</span></span>

<span data-ttu-id="39760-318">O [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) instala o aplicativo StockTicker.</span><span class="sxs-lookup"><span data-stu-id="39760-318">The [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) installs the StockTicker application.</span></span> <span data-ttu-id="39760-319">O pacote NuGet inclui mais recursos que a versão simplificada que você criou do zero.</span><span class="sxs-lookup"><span data-stu-id="39760-319">The NuGet package includes more features than the simplified version that you created from scratch.</span></span> <span data-ttu-id="39760-320">Nesta seção do tutorial, instale o pacote NuGet e examine os novos recursos e o código que implementa-los.</span><span class="sxs-lookup"><span data-stu-id="39760-320">In this section of the tutorial, you install the NuGet package and review the new features and the code that implements them.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="39760-321">Se você instalar o pacote sem executar as etapas anteriores deste tutorial, você deve adicionar uma classe de inicialização do OWIN ao projeto.</span><span class="sxs-lookup"><span data-stu-id="39760-321">If you install the package without performing the earlier steps of this tutorial, you must add an OWIN startup class to your project.</span></span> <span data-ttu-id="39760-322">Este arquivo readme. txt para o pacote NuGet explica essa etapa.</span><span class="sxs-lookup"><span data-stu-id="39760-322">This readme.txt file for the NuGet package explains this step.</span></span>

### <a name="install-the-signalrsample-nuget-package"></a><span data-ttu-id="39760-323">Instale o pacote SignalR.Sample NuGet</span><span class="sxs-lookup"><span data-stu-id="39760-323">Install the SignalR.Sample NuGet package</span></span>

1. <span data-ttu-id="39760-324">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="39760-324">In **Solution Explorer**, right-click the project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="39760-325">No **Gerenciador de pacotes do NuGet: SignalR.StockTicker**, selecione **procurar**.</span><span class="sxs-lookup"><span data-stu-id="39760-325">In **NuGet Package manager: SignalR.StockTicker**, select **Browse**.</span></span>

1. <span data-ttu-id="39760-326">Partir **origem do pacote**, selecione **nuget.org**.</span><span class="sxs-lookup"><span data-stu-id="39760-326">From **Package source**, select **nuget.org**.</span></span>

1. <span data-ttu-id="39760-327">Insira *SignalR.Sample* na caixa de pesquisa e selecione **Microsoft.AspNet.SignalR.Sample** > **instalar**.</span><span class="sxs-lookup"><span data-stu-id="39760-327">Enter *SignalR.Sample* in the search box and select **Microsoft.AspNet.SignalR.Sample** > **Install**.</span></span>

1. <span data-ttu-id="39760-328">Na **Gerenciador de soluções**, expanda o *SignalR.Sample* pasta.</span><span class="sxs-lookup"><span data-stu-id="39760-328">In **Solution Explorer**, expand the *SignalR.Sample* folder.</span></span>

    <span data-ttu-id="39760-329">Instalando o pacote SignalR.Sample criado na pasta e seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="39760-329">Installing the SignalR.Sample package created the folder and its contents.</span></span>

1. <span data-ttu-id="39760-330">No *SignalR.Sample* pasta, clique com botão direito *StockTicker.html*e, em seguida, selecione **definir como página inicial**.</span><span class="sxs-lookup"><span data-stu-id="39760-330">In the *SignalR.Sample* folder, right-click *StockTicker.html*, and then select **Set As Start Page**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="39760-331">Instalando o SignalR.Sample NuGet o pacote pode alterar a versão do jQuery que você tem em seu *Scripts* pasta.</span><span class="sxs-lookup"><span data-stu-id="39760-331">Installing The SignalR.Sample NuGet package might change the version of jQuery that you have in your *Scripts* folder.</span></span> <span data-ttu-id="39760-332">O novo *StockTicker.html* que instala o pacote no arquivo de *SignalR.Sample* pasta estará em sincronia com a versão do jQuery que instala o pacote, mas se você quiser executar original *StockTicker.html* arquivo novamente, talvez você precise atualizar a referência de jQuery na marca do script pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="39760-332">The new *StockTicker.html* file that the package installs in the *SignalR.Sample* folder will be in sync with the jQuery version that the package installs, but if you want to run your original *StockTicker.html* file again, you might have to update the jQuery reference in the script tag first.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="39760-333">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="39760-333">Run the application</span></span>

 <span data-ttu-id="39760-334">A tabela que você viu no primeiro aplicativo tinha recursos úteis.</span><span class="sxs-lookup"><span data-stu-id="39760-334">The table that you saw in the first app had useful features.</span></span> <span data-ttu-id="39760-335">O aplicativo completo cotações da bolsa mostra os novos recursos: uma janela de rolagem horizontal que mostra os dados de estoque e estoque que alterar a cor como eles nascer e se encaixam.</span><span class="sxs-lookup"><span data-stu-id="39760-335">The full stock ticker application shows new features: a horizontally scrolling window that shows the stock data and stocks that change color as they rise and fall.</span></span>

1. <span data-ttu-id="39760-336">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39760-336">Press **F5** to run the app.</span></span>

     <span data-ttu-id="39760-337">Quando você executa o aplicativo pela primeira vez, o mercado de"" é "closed" e você verá uma tabela estática e uma janela de painel eletrônico que não é de rolagem.</span><span class="sxs-lookup"><span data-stu-id="39760-337">When you run the app for the first time, the "market" is "closed" and you see a static table and a ticker window that isn't scrolling.</span></span>

1. <span data-ttu-id="39760-338">Selecione **Open Market**.</span><span class="sxs-lookup"><span data-stu-id="39760-338">Select **Open Market**.</span></span>

    ![Captura de tela do ticker ao vivo.](tutorial-server-broadcast-with-signalr/_static/image5.png)

    * <span data-ttu-id="39760-340">O **Live Stock Ticker** caixa começa a rolar horizontalmente, e o servidor for iniciado difundir periodicamente as alterações de preço de estoque de forma aleatória.</span><span class="sxs-lookup"><span data-stu-id="39760-340">The **Live Stock Ticker** box starts to scroll horizontally, and the server starts to periodically broadcast stock price changes on a random basis.</span></span>

    * <span data-ttu-id="39760-341">Cada vez que um preço de ações é alterado, o aplicativo atualiza ambos o **tabela de estoque ao vivo** e o **Live Ticker de estoque**.</span><span class="sxs-lookup"><span data-stu-id="39760-341">Each time a stock price changes, the app updates both the **Live Stock Table** and the **Live Stock Ticker**.</span></span>

    * <span data-ttu-id="39760-342">Quando a alteração de preço de uma ação for positiva, o aplicativo mostra a ação com um plano de fundo verde.</span><span class="sxs-lookup"><span data-stu-id="39760-342">When a stock's price change is positive, the app shows the stock with a green background.</span></span>

    * <span data-ttu-id="39760-343">Quando a alteração for negativa, o aplicativo mostra o estoque com fundo vermelho.</span><span class="sxs-lookup"><span data-stu-id="39760-343">When the change is negative, the app shows the stock with a red background.</span></span>

1. <span data-ttu-id="39760-344">Selecione **fechar mercado**.</span><span class="sxs-lookup"><span data-stu-id="39760-344">Select **Close Market**.</span></span>

    * <span data-ttu-id="39760-345">A tabela de atualizações de parada.</span><span class="sxs-lookup"><span data-stu-id="39760-345">The table updates stop.</span></span>

    * <span data-ttu-id="39760-346">O painel eletrônico para rolagem.</span><span class="sxs-lookup"><span data-stu-id="39760-346">The ticker stops scrolling.</span></span>

1. <span data-ttu-id="39760-347">Selecione **redefinir**.</span><span class="sxs-lookup"><span data-stu-id="39760-347">Select **Reset**.</span></span>

    * <span data-ttu-id="39760-348">Todos os dados de estoque é redefinido.</span><span class="sxs-lookup"><span data-stu-id="39760-348">All stock data is reset.</span></span>

    * <span data-ttu-id="39760-349">O aplicativo restaura o estado inicial antes das alterações de preço iniciado.</span><span class="sxs-lookup"><span data-stu-id="39760-349">The app restores the initial state before price changes started.</span></span>

1. <span data-ttu-id="39760-350">Copie a URL do navegador, abra dois outros navegadores e cole as URLs em barras de endereço.</span><span class="sxs-lookup"><span data-stu-id="39760-350">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="39760-351">Você ver os mesmos dados são atualizados dinamicamente ao mesmo tempo em cada navegador.</span><span class="sxs-lookup"><span data-stu-id="39760-351">You see the same data dynamically updated at the same time in each browser.</span></span>

1. <span data-ttu-id="39760-352">Quando você seleciona qualquer um dos controles, todos os navegadores respondem da mesma forma ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="39760-352">When you select any of the controls, all browsers respond the same way at the same time.</span></span>

### <a name="live-stock-ticker-display"></a><span data-ttu-id="39760-353">Exibição de painel eletrônico de estoque ao vivo</span><span class="sxs-lookup"><span data-stu-id="39760-353">Live Stock Ticker display</span></span>

<span data-ttu-id="39760-354">O **Live Stock Ticker** exibição é uma lista não ordenada em um `<div>` elemento formatado em uma única linha pelos estilos CSS.</span><span class="sxs-lookup"><span data-stu-id="39760-354">The **Live Stock Ticker** display is an unordered list in a `<div>` element formatted into a single line by CSS styles.</span></span> <span data-ttu-id="39760-355">O aplicativo inicializa e atualiza o painel eletrônico da mesma forma que a tabela:, substituindo espaços reservados em uma `<li>` cadeia de caracteres de modelo e adicionar dinamicamente a `<li>` elementos a serem o `<ul>` elemento.</span><span class="sxs-lookup"><span data-stu-id="39760-355">The app initializes and updates the ticker the same way as the table: by replacing placeholders in an `<li>` template string and dynamically adding the `<li>` elements to the `<ul>` element.</span></span> <span data-ttu-id="39760-356">O aplicativo inclui a rolagem usando o jQuery `animate` função para variar a margem esquerda da lista não ordenada de dentro do `<div>`.</span><span class="sxs-lookup"><span data-stu-id="39760-356">The app includes  scrolling by using the jQuery `animate` function to vary the margin-left of the unordered list within the `<div>`.</span></span>

#### <a name="signalrsample-stocktickerhtml"></a><span data-ttu-id="39760-357">SignalR.Sample StockTicker.html</span><span class="sxs-lookup"><span data-stu-id="39760-357">SignalR.Sample StockTicker.html</span></span>

<span data-ttu-id="39760-358">A cotação da bolsa código HTML:</span><span class="sxs-lookup"><span data-stu-id="39760-358">The stock ticker HTML code:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample20.html)]

#### <a name="signalrsample-stocktickercss"></a><span data-ttu-id="39760-359">SignalR.Sample StockTicker.css</span><span class="sxs-lookup"><span data-stu-id="39760-359">SignalR.Sample StockTicker.css</span></span>

<span data-ttu-id="39760-360">A cotação da bolsa código CSS:</span><span class="sxs-lookup"><span data-stu-id="39760-360">The stock ticker CSS code:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample21.html)]

#### <a name="signalrsample-signalrstocktickerjs"></a><span data-ttu-id="39760-361">SignalR.Sample SignalR.StockTicker.js</span><span class="sxs-lookup"><span data-stu-id="39760-361">SignalR.Sample SignalR.StockTicker.js</span></span>

<span data-ttu-id="39760-362">O código jQuery que torna role:</span><span class="sxs-lookup"><span data-stu-id="39760-362">The jQuery code that makes it scroll:</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample22.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a><span data-ttu-id="39760-363">Métodos adicionais no servidor que o cliente pode chamar</span><span class="sxs-lookup"><span data-stu-id="39760-363">Additional methods on the server that the client can call</span></span>

<span data-ttu-id="39760-364">Para adicionar flexibilidade ao aplicativo, há métodos adicionais que o aplicativo pode chamar.</span><span class="sxs-lookup"><span data-stu-id="39760-364">To add flexibility to the app, there are additional methods the app can call.</span></span>

#### <a name="signalrsample-stocktickerhubcs"></a><span data-ttu-id="39760-365">SignalR.Sample StockTickerHub.cs</span><span class="sxs-lookup"><span data-stu-id="39760-365">SignalR.Sample StockTickerHub.cs</span></span>

<span data-ttu-id="39760-366">O `StockTickerHub` classe define quatro métodos adicionais que o cliente pode chamar:</span><span class="sxs-lookup"><span data-stu-id="39760-366">The `StockTickerHub` class defines four additional methods that the client can call:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample23.cs)]

<span data-ttu-id="39760-367">O aplicativo chama `OpenMarket`, `CloseMarket`, e `Reset` em resposta aos botões na parte superior da página.</span><span class="sxs-lookup"><span data-stu-id="39760-367">The app calls `OpenMarket`, `CloseMarket`, and `Reset` in response to the buttons at the top of the page.</span></span> <span data-ttu-id="39760-368">Eles demonstram o padrão de um cliente disparar uma alteração no estado propagada imediatamente para todos os clientes.</span><span class="sxs-lookup"><span data-stu-id="39760-368">They demonstrate the pattern of one client triggering a change in state immediately propagated to all clients.</span></span> <span data-ttu-id="39760-369">Cada um desses métodos chama um método no `StockTicker` classe que faz com que a alteração de estado de colocação no mercado e, em seguida, transmite o novo estado.</span><span class="sxs-lookup"><span data-stu-id="39760-369">Each of these methods calls a method in the `StockTicker` class that causes the market state change and then broadcasts the new state.</span></span>

#### <a name="signalrsample-stocktickercs"></a><span data-ttu-id="39760-370">SignalR.Sample StockTicker.cs</span><span class="sxs-lookup"><span data-stu-id="39760-370">SignalR.Sample StockTicker.cs</span></span>

<span data-ttu-id="39760-371">No `StockTicker` classe, o aplicativo mantém o estado do mercado com um `MarketState` propriedade que retorna um `MarketState` valor de enumeração:</span><span class="sxs-lookup"><span data-stu-id="39760-371">In the `StockTicker` class, the app maintains the state of the market with a `MarketState` property that returns a `MarketState` enum value:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample24.cs)]

<span data-ttu-id="39760-372">Cada um dos métodos que alteram o estado de mercado fazê-lo dentro de um bloco de bloqueio porque o `StockTicker` classe deve ser thread-safe:</span><span class="sxs-lookup"><span data-stu-id="39760-372">Each of the methods that change the market state do so inside a lock block because the `StockTicker` class has to be thread-safe:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample25.cs)]

<span data-ttu-id="39760-373">Para garantir que esse código é thread-safe, o `_marketState` campo que dá suporte a `MarketState` propriedade designada `volatile`:</span><span class="sxs-lookup"><span data-stu-id="39760-373">To make sure this code is thread-safe, the `_marketState` field that backs the `MarketState` property designated `volatile`:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample26.cs)]

<span data-ttu-id="39760-374">O `BroadcastMarketStateChange` e `BroadcastMarketReset` métodos são semelhantes ao método BroadcastStockPrice que você já viu, exceto que eles chamam métodos diferentes definidos no cliente:</span><span class="sxs-lookup"><span data-stu-id="39760-374">The `BroadcastMarketStateChange` and `BroadcastMarketReset` methods are similar to the BroadcastStockPrice method that you already saw, except they call different methods defined at the client:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample27.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a><span data-ttu-id="39760-375">Funções adicionais no cliente que o servidor pode chamar</span><span class="sxs-lookup"><span data-stu-id="39760-375">Additional functions on the client that the server can call</span></span>

<span data-ttu-id="39760-376">O `updateStockPrice` função agora manipula a tabela e a exibição de painel eletrônico e usa `jQuery.Color` Flash cores vermelhas e verdes.</span><span class="sxs-lookup"><span data-stu-id="39760-376">The `updateStockPrice` function now handles both the table and the ticker display, and it uses `jQuery.Color` to flash red and green colors.</span></span>

<span data-ttu-id="39760-377">Novas funções no *SignalR.StockTicker.js* habilitar e desabilitar os botões com base no estado de colocação no mercado.</span><span class="sxs-lookup"><span data-stu-id="39760-377">New functions in *SignalR.StockTicker.js* enable and disable the buttons based on market state.</span></span> <span data-ttu-id="39760-378">Eles também interromper ou iniciar o **Live Stock Ticker** rolagem horizontal.</span><span class="sxs-lookup"><span data-stu-id="39760-378">They also stop or start the **Live Stock Ticker** horizontal scrolling.</span></span> <span data-ttu-id="39760-379">Uma vez que muitas funções estão sendo adicionadas ao `ticker.client`, o aplicativo usa o [jQuery estender a função](http://api.jquery.com/jQuery.extend/) para adicioná-los.</span><span class="sxs-lookup"><span data-stu-id="39760-379">Since many functions are being added to `ticker.client`, the app uses the [jQuery extend function](http://api.jquery.com/jQuery.extend/) to add them.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample28.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a><span data-ttu-id="39760-380">Instalação de cliente adicionais depois de estabelecer a conexão</span><span class="sxs-lookup"><span data-stu-id="39760-380">Additional client setup after establishing the connection</span></span>

<span data-ttu-id="39760-381">Depois que o cliente estabelece a conexão, ele tem algum trabalho adicional para fazer:</span><span class="sxs-lookup"><span data-stu-id="39760-381">After the client establishes the connection, it has some additional work to do:</span></span>

* <span data-ttu-id="39760-382">Descubra se o mercado está aberto ou fechado para chamar o `marketOpened` ou `marketClosed` função.</span><span class="sxs-lookup"><span data-stu-id="39760-382">Find out if the market is open or closed to call the appropriate `marketOpened` or `marketClosed` function.</span></span>

* <span data-ttu-id="39760-383">Anexe as chamadas de método do servidor para os botões.</span><span class="sxs-lookup"><span data-stu-id="39760-383">Attach the server method calls to the buttons.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample29.js)]

<span data-ttu-id="39760-384">Os métodos de servidor não são vinculei os botões até depois que o aplicativo estabelece a conexão.</span><span class="sxs-lookup"><span data-stu-id="39760-384">The server methods aren't wired up to the buttons until after the app establishes the connection.</span></span> <span data-ttu-id="39760-385">É assim que o código não é possível chamar os métodos de servidor antes de estarem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="39760-385">It's so the code can't call the server methods before they're available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39760-386">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="39760-386">Additional resources</span></span>

<span data-ttu-id="39760-387">Neste tutorial, você aprendeu como programar um aplicativo de SignalR que transmite mensagens do servidor para todos os clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="39760-387">In this tutorial you've learned how to program a SignalR application that broadcasts messages from the server to all connected clients.</span></span> <span data-ttu-id="39760-388">Agora você pode transmitir mensagens em intervalos periódicos e em resposta a notificações de qualquer cliente.</span><span class="sxs-lookup"><span data-stu-id="39760-388">Now you can broadcast messages on a periodic basis and in response to notifications from any client.</span></span> <span data-ttu-id="39760-389">Você pode usar o conceito de instância singleton multi-threaded para manter o estado do servidor em cenários de jogos online de vários jogadores.</span><span class="sxs-lookup"><span data-stu-id="39760-389">You can use the concept of multi-threaded singleton instance to maintain server state in multi-player online game scenarios.</span></span> <span data-ttu-id="39760-390">Por exemplo, consulte [ShootR jogo com base no SignalR](https://github.com/NTaylorMullen/ShootR).</span><span class="sxs-lookup"><span data-stu-id="39760-390">For an example, see [the ShootR game based on SignalR](https://github.com/NTaylorMullen/ShootR).</span></span>

<span data-ttu-id="39760-391">Para obter tutoriais que mostram os cenários de comunicação peer-to-peer, consulte [Introdução ao SignalR](introduction-to-signalr.md) e [a atualização em tempo real com SignalR](tutorial-high-frequency-realtime-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="39760-391">For tutorials that show peer-to-peer communication scenarios, see [Getting Started with SignalR](introduction-to-signalr.md) and [Real-Time Updating with SignalR](tutorial-high-frequency-realtime-with-signalr.md).</span></span>

<span data-ttu-id="39760-392">Para obter mais informações sobre o SignalR, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="39760-392">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="39760-393">SignalR do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="39760-393">ASP.NET SignalR</span></span>](../../index.md)
* [<span data-ttu-id="39760-394">Projeto de SignalR</span><span class="sxs-lookup"><span data-stu-id="39760-394">SignalR Project</span></span>](http://signalr.net/)
* [<span data-ttu-id="39760-395">GitHub do SignalR e exemplos</span><span class="sxs-lookup"><span data-stu-id="39760-395">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)
* [<span data-ttu-id="39760-396">Wiki do SignalR</span><span class="sxs-lookup"><span data-stu-id="39760-396">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="39760-397">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="39760-397">Next steps</span></span>

<span data-ttu-id="39760-398">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="39760-398">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="39760-399">Criar o projeto</span><span class="sxs-lookup"><span data-stu-id="39760-399">Created the project</span></span>
> * <span data-ttu-id="39760-400">Configure o código de servidor</span><span class="sxs-lookup"><span data-stu-id="39760-400">Set up the server code</span></span>
> * <span data-ttu-id="39760-401">Examinado o código do servidor</span><span class="sxs-lookup"><span data-stu-id="39760-401">Examined the server code</span></span>
> * <span data-ttu-id="39760-402">Configure o código de cliente</span><span class="sxs-lookup"><span data-stu-id="39760-402">Set up the client code</span></span>
> * <span data-ttu-id="39760-403">Examinado o código do cliente</span><span class="sxs-lookup"><span data-stu-id="39760-403">Examined the client code</span></span>
> * <span data-ttu-id="39760-404">Testando o aplicativo</span><span class="sxs-lookup"><span data-stu-id="39760-404">Tested the application</span></span>
> * <span data-ttu-id="39760-405">Registro em log habilitado</span><span class="sxs-lookup"><span data-stu-id="39760-405">Enabled logging</span></span>

<span data-ttu-id="39760-406">Avance para o próximo artigo para saber como criar um aplicativo web em tempo real que usa o ASP.NET SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="39760-406">Advance to the next article to learn how to create a real-time web application that uses ASP.NET SignalR 2.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="39760-407">Criar aplicativo web em tempo real com SignalR</span><span class="sxs-lookup"><span data-stu-id="39760-407">Create real-time web app with SignalR</span></span>](real-time-web-applications-with-signalr.md)