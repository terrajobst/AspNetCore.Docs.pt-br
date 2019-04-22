---
title: Cliente de .NET do SignalR do ASP.NET Core
author: bradygaster
description: Informações sobre o cliente do .NET Core ASP.NET SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 640d75157e42ffa6d78235c5be03e4846e8dcde9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982940"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="58c84-103">Cliente de .NET do SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58c84-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="58c84-104">A biblioteca de cliente .NET de SignalR do ASP.NET Core permite que você se comunicar com os hubs de SignalR em aplicativos .NET.</span><span class="sxs-lookup"><span data-stu-id="58c84-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

> [!NOTE]
> <span data-ttu-id="58c84-105">Xamarin tem requisitos especiais para a versão do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="58c84-105">Xamarin has special requirements for Visual Studio version.</span></span> <span data-ttu-id="58c84-106">Para obter mais informações, consulte [cliente SignalR 2.1.1 no Xamarin](https://github.com/aspnet/Announcements/issues/305).</span><span class="sxs-lookup"><span data-stu-id="58c84-106">For more information, see [SignalR Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).</span></span>

<span data-ttu-id="58c84-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="58c84-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="58c84-108">O exemplo de código neste artigo é um aplicativo do WPF que usa o cliente .NET de SignalR do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58c84-108">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="58c84-109">Instalar o pacote de cliente .NET do SignalR</span><span class="sxs-lookup"><span data-stu-id="58c84-109">Install the SignalR .NET client package</span></span>

<span data-ttu-id="58c84-110">O `Microsoft.AspNetCore.SignalR.Client` pacote é necessário para clientes .NET conectar-se aos hubs de SignalR.</span><span class="sxs-lookup"><span data-stu-id="58c84-110">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="58c84-111">Para instalar a biblioteca de cliente, execute o seguinte comando na **Package Manager Console** janela:</span><span class="sxs-lookup"><span data-stu-id="58c84-111">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="58c84-112">Conectar a um hub</span><span class="sxs-lookup"><span data-stu-id="58c84-112">Connect to a hub</span></span>

<span data-ttu-id="58c84-113">Para estabelecer uma conexão, cria uma `HubConnectionBuilder` e chamar `Build`.</span><span class="sxs-lookup"><span data-stu-id="58c84-113">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="58c84-114">A URL do hub, protocolo, o tipo de transporte, nível de log, cabeçalhos e outras opções podem ser configuradas durante a criação de uma conexão.</span><span class="sxs-lookup"><span data-stu-id="58c84-114">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="58c84-115">Configurar as opções necessárias, inserindo qualquer um dos `HubConnectionBuilder` métodos em `Build`.</span><span class="sxs-lookup"><span data-stu-id="58c84-115">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="58c84-116">Iniciar a conexão com `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="58c84-116">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="58c84-117">Lidar com a conexão perdida</span><span class="sxs-lookup"><span data-stu-id="58c84-117">Handle lost connection</span></span>

<span data-ttu-id="58c84-118">Use o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> eventos para responder a uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="58c84-118">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="58c84-119">Por exemplo, você talvez queira automatizar a reconexão.</span><span class="sxs-lookup"><span data-stu-id="58c84-119">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="58c84-120">O `Closed` evento requer um delegado que retorna um `Task`, que permite que o código assíncrono executar sem usar `async void`.</span><span class="sxs-lookup"><span data-stu-id="58c84-120">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="58c84-121">Para satisfazer a assinatura do delegado em um `Closed` manipulador de eventos que é executado de forma síncrona, retorna `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="58c84-121">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="58c84-122">O principal motivo para o suporte assíncrono é portanto, você pode reiniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="58c84-122">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="58c84-123">Iniciar uma conexão é uma ação assíncrona.</span><span class="sxs-lookup"><span data-stu-id="58c84-123">Starting a connection is an async action.</span></span>

<span data-ttu-id="58c84-124">Em um `Closed` manipulador que reinicia a conexão, considere aguardar algum atraso aleatório evitar sobrecarregar o servidor, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="58c84-124">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="58c84-125">Chamar métodos de hub do cliente</span><span class="sxs-lookup"><span data-stu-id="58c84-125">Call hub methods from client</span></span>

<span data-ttu-id="58c84-126">`InvokeAsync` chama métodos no hub.</span><span class="sxs-lookup"><span data-stu-id="58c84-126">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="58c84-127">Passe o nome do método de hub e quaisquer argumentos definidos no método de hub para `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="58c84-127">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="58c84-128">O SignalR é assíncrono, portanto, use `async` e `await` ao fazer as chamadas.</span><span class="sxs-lookup"><span data-stu-id="58c84-128">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="58c84-129">O `InvokeAsync` método retorna um `Task` que conclui quando o método de servidor é retornado.</span><span class="sxs-lookup"><span data-stu-id="58c84-129">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="58c84-130">O valor de retorno, se houver, é fornecido como o resultado do `Task`.</span><span class="sxs-lookup"><span data-stu-id="58c84-130">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="58c84-131">Todas as exceções geradas pelo método no servidor de produzem uma falha `Task`.</span><span class="sxs-lookup"><span data-stu-id="58c84-131">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="58c84-132">Use `await` sintaxe de espera para o método de servidor concluir e `try...catch` sintaxe para lidar com erros.</span><span class="sxs-lookup"><span data-stu-id="58c84-132">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="58c84-133">O `SendAsync` método retorna um `Task` que conclui quando a mensagem foi enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="58c84-133">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="58c84-134">Nenhum valor de retorno é fornecido uma vez que isso `Task` não espera até que o método de servidor seja concluída.</span><span class="sxs-lookup"><span data-stu-id="58c84-134">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="58c84-135">Todas as exceções geradas no cliente ao enviar a mensagem de produzem uma falha `Task`.</span><span class="sxs-lookup"><span data-stu-id="58c84-135">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="58c84-136">Use `await` e `try...catch` enviar de sintaxe para lidar com erros.</span><span class="sxs-lookup"><span data-stu-id="58c84-136">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="58c84-137">Se você estiver usando o serviço do Azure SignalR no *modo sem servidor*, você não pode chamar métodos de hub de um cliente.</span><span class="sxs-lookup"><span data-stu-id="58c84-137">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="58c84-138">Para obter mais informações, consulte o [documentação do SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="58c84-138">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="58c84-139">Chamar métodos de cliente do hub</span><span class="sxs-lookup"><span data-stu-id="58c84-139">Call client methods from hub</span></span>

<span data-ttu-id="58c84-140">Definir métodos de hub de chamadas usando `connection.On` depois de criar, mas antes de iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="58c84-140">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="58c84-141">O código anterior no `connection.On` é executado quando o código do lado do servidor chama-o usando o `SendAsync` método.</span><span class="sxs-lookup"><span data-stu-id="58c84-141">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="58c84-142">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="58c84-142">Error handling and logging</span></span>

<span data-ttu-id="58c84-143">Tratar erros com uma instrução try-catch.</span><span class="sxs-lookup"><span data-stu-id="58c84-143">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="58c84-144">Inspecione o `Exception` objeto para determinar a ação apropriada a tomar depois de ocorrer um erro.</span><span class="sxs-lookup"><span data-stu-id="58c84-144">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="58c84-145">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="58c84-145">Additional resources</span></span>

* [<span data-ttu-id="58c84-146">Hubs</span><span class="sxs-lookup"><span data-stu-id="58c84-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="58c84-147">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="58c84-147">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="58c84-148">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="58c84-148">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="58c84-149">Documentação de sem servidor SignalR Service do Azure</span><span class="sxs-lookup"><span data-stu-id="58c84-149">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
