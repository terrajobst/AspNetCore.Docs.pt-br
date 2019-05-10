---
title: Cliente de .NET do SignalR do ASP.NET Core
author: bradygaster
description: Informações sobre o cliente do .NET Core ASP.NET SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: b59af0f9c84a008f778709970dba2273abdfcd4f
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087710"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="fdccd-103">Cliente de .NET do SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fdccd-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="fdccd-104">A biblioteca de cliente .NET de SignalR do ASP.NET Core permite que você se comunicar com os hubs de SignalR em aplicativos .NET.</span><span class="sxs-lookup"><span data-stu-id="fdccd-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="fdccd-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fdccd-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fdccd-106">O exemplo de código neste artigo é um aplicativo do WPF que usa o cliente .NET de SignalR do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fdccd-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="fdccd-107">Instalar o pacote de cliente .NET do SignalR</span><span class="sxs-lookup"><span data-stu-id="fdccd-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="fdccd-108">O `Microsoft.AspNetCore.SignalR.Client` pacote é necessário para clientes .NET conectar-se aos hubs de SignalR.</span><span class="sxs-lookup"><span data-stu-id="fdccd-108">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="fdccd-109">Para instalar a biblioteca de cliente, execute o seguinte comando na **Package Manager Console** janela:</span><span class="sxs-lookup"><span data-stu-id="fdccd-109">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="fdccd-110">Conectar a um hub</span><span class="sxs-lookup"><span data-stu-id="fdccd-110">Connect to a hub</span></span>

<span data-ttu-id="fdccd-111">Para estabelecer uma conexão, cria uma `HubConnectionBuilder` e chamar `Build`.</span><span class="sxs-lookup"><span data-stu-id="fdccd-111">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="fdccd-112">A URL do hub, protocolo, o tipo de transporte, nível de log, cabeçalhos e outras opções podem ser configuradas durante a criação de uma conexão.</span><span class="sxs-lookup"><span data-stu-id="fdccd-112">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="fdccd-113">Configurar as opções necessárias, inserindo qualquer um dos `HubConnectionBuilder` métodos em `Build`.</span><span class="sxs-lookup"><span data-stu-id="fdccd-113">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="fdccd-114">Iniciar a conexão com `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="fdccd-114">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="fdccd-115">Lidar com a conexão perdida</span><span class="sxs-lookup"><span data-stu-id="fdccd-115">Handle lost connection</span></span>

<span data-ttu-id="fdccd-116">Use o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> eventos para responder a uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="fdccd-116">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="fdccd-117">Por exemplo, você talvez queira automatizar a reconexão.</span><span class="sxs-lookup"><span data-stu-id="fdccd-117">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="fdccd-118">O `Closed` evento requer um delegado que retorna um `Task`, que permite que o código assíncrono executar sem usar `async void`.</span><span class="sxs-lookup"><span data-stu-id="fdccd-118">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="fdccd-119">Para satisfazer a assinatura do delegado em um `Closed` manipulador de eventos que é executado de forma síncrona, retorna `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="fdccd-119">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="fdccd-120">O principal motivo para o suporte assíncrono é portanto, você pode reiniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="fdccd-120">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="fdccd-121">Iniciar uma conexão é uma ação assíncrona.</span><span class="sxs-lookup"><span data-stu-id="fdccd-121">Starting a connection is an async action.</span></span>

<span data-ttu-id="fdccd-122">Em um `Closed` manipulador que reinicia a conexão, considere aguardar algum atraso aleatório evitar sobrecarregar o servidor, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="fdccd-122">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="fdccd-123">Chamar métodos de hub do cliente</span><span class="sxs-lookup"><span data-stu-id="fdccd-123">Call hub methods from client</span></span>

<span data-ttu-id="fdccd-124">`InvokeAsync` chama métodos no hub.</span><span class="sxs-lookup"><span data-stu-id="fdccd-124">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="fdccd-125">Passe o nome do método de hub e quaisquer argumentos definidos no método de hub para `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="fdccd-125">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="fdccd-126">O SignalR é assíncrono, portanto, use `async` e `await` ao fazer as chamadas.</span><span class="sxs-lookup"><span data-stu-id="fdccd-126">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="fdccd-127">O `InvokeAsync` método retorna um `Task` que conclui quando o método de servidor é retornado.</span><span class="sxs-lookup"><span data-stu-id="fdccd-127">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="fdccd-128">O valor de retorno, se houver, é fornecido como o resultado do `Task`.</span><span class="sxs-lookup"><span data-stu-id="fdccd-128">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="fdccd-129">Todas as exceções geradas pelo método no servidor de produzem uma falha `Task`.</span><span class="sxs-lookup"><span data-stu-id="fdccd-129">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="fdccd-130">Use `await` sintaxe de espera para o método de servidor concluir e `try...catch` sintaxe para lidar com erros.</span><span class="sxs-lookup"><span data-stu-id="fdccd-130">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="fdccd-131">O `SendAsync` método retorna um `Task` que conclui quando a mensagem foi enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="fdccd-131">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="fdccd-132">Nenhum valor de retorno é fornecido uma vez que isso `Task` não espera até que o método de servidor seja concluída.</span><span class="sxs-lookup"><span data-stu-id="fdccd-132">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="fdccd-133">Todas as exceções geradas no cliente ao enviar a mensagem de produzem uma falha `Task`.</span><span class="sxs-lookup"><span data-stu-id="fdccd-133">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="fdccd-134">Use `await` e `try...catch` enviar de sintaxe para lidar com erros.</span><span class="sxs-lookup"><span data-stu-id="fdccd-134">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="fdccd-135">Se você estiver usando o serviço do Azure SignalR no *modo sem servidor*, você não pode chamar métodos de hub de um cliente.</span><span class="sxs-lookup"><span data-stu-id="fdccd-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="fdccd-136">Para obter mais informações, consulte o [documentação do SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="fdccd-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="fdccd-137">Chamar métodos de cliente do hub</span><span class="sxs-lookup"><span data-stu-id="fdccd-137">Call client methods from hub</span></span>

<span data-ttu-id="fdccd-138">Definir métodos de hub de chamadas usando `connection.On` depois de criar, mas antes de iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="fdccd-138">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="fdccd-139">O código anterior no `connection.On` é executado quando o código do lado do servidor chama-o usando o `SendAsync` método.</span><span class="sxs-lookup"><span data-stu-id="fdccd-139">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="fdccd-140">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="fdccd-140">Error handling and logging</span></span>

<span data-ttu-id="fdccd-141">Tratar erros com uma instrução try-catch.</span><span class="sxs-lookup"><span data-stu-id="fdccd-141">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="fdccd-142">Inspecione o `Exception` objeto para determinar a ação apropriada a tomar depois de ocorrer um erro.</span><span class="sxs-lookup"><span data-stu-id="fdccd-142">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="fdccd-143">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fdccd-143">Additional resources</span></span>

* [<span data-ttu-id="fdccd-144">Hubs</span><span class="sxs-lookup"><span data-stu-id="fdccd-144">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="fdccd-145">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="fdccd-145">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="fdccd-146">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="fdccd-146">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="fdccd-147">Documentação de sem servidor SignalR Service do Azure</span><span class="sxs-lookup"><span data-stu-id="fdccd-147">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
