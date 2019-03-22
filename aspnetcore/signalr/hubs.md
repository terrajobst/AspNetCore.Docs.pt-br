---
title: Usando os hubs de SignalR do ASP.NET Core
author: bradygaster
description: Saiba como usar os hubs do SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: f037c1f6fd7ef773b8e7b2fd4fdf6e28222c441a
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327255"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="59ad0-103">Usar os hubs no SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59ad0-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="59ad0-104">Por [Rachel Appel](https://twitter.com/rachelappel) e [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="59ad0-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="59ad0-105">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(como fazer o download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="59ad0-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="59ad0-106">O que é um hub SignalR</span><span class="sxs-lookup"><span data-stu-id="59ad0-106">What is a SignalR hub</span></span>

<span data-ttu-id="59ad0-107">A API de Hubs de SignalR permite chamar métodos em clientes conectados do servidor.</span><span class="sxs-lookup"><span data-stu-id="59ad0-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="59ad0-108">No código do servidor, você define métodos que são chamados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="59ad0-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="59ad0-109">O código do cliente, você define métodos que são chamados do servidor.</span><span class="sxs-lookup"><span data-stu-id="59ad0-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="59ad0-110">SignalR cuida de tudo o que nos bastidores que possibilita a comunicação de servidor para cliente e servidor-cliente em tempo real.</span><span class="sxs-lookup"><span data-stu-id="59ad0-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="59ad0-111">Configurar os hubs de SignalR</span><span class="sxs-lookup"><span data-stu-id="59ad0-111">Configure SignalR hubs</span></span>

<span data-ttu-id="59ad0-112">O middleware SignalR requer alguns serviços, que são configurados por meio da chamada `services.AddSignalR`.</span><span class="sxs-lookup"><span data-stu-id="59ad0-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

<span data-ttu-id="59ad0-113">Ao adicionar a funcionalidade do SignalR para um aplicativo ASP.NET Core, configurar as rotas do SignalR chamando `app.UseSignalR` no `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="59ad0-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

## <a name="create-and-use-hubs"></a><span data-ttu-id="59ad0-114">Criar e usar os hubs</span><span class="sxs-lookup"><span data-stu-id="59ad0-114">Create and use hubs</span></span>

<span data-ttu-id="59ad0-115">Criar um hub, declarando uma classe que herda de `Hub`e adicione os métodos públicos a ele.</span><span class="sxs-lookup"><span data-stu-id="59ad0-115">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="59ad0-116">Os clientes poderão chamar métodos que são definidos como `public`.</span><span class="sxs-lookup"><span data-stu-id="59ad0-116">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="59ad0-117">Você pode especificar um tipo de retorno e parâmetros, incluindo tipos complexos e matrizes, como você faria em qualquer método em c#.</span><span class="sxs-lookup"><span data-stu-id="59ad0-117">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="59ad0-118">O SignalR lida com a serialização e desserialização de objetos complexos e matrizes em seus valores de retorno e parâmetros.</span><span class="sxs-lookup"><span data-stu-id="59ad0-118">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="59ad0-119">Os hubs são transitórios:</span><span class="sxs-lookup"><span data-stu-id="59ad0-119">Hubs are transient:</span></span>
>
> * <span data-ttu-id="59ad0-120">Não armazene o estado em uma propriedade na classe hub.</span><span class="sxs-lookup"><span data-stu-id="59ad0-120">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="59ad0-121">Cada chamada de método de hub é executada em uma nova instância de hub.</span><span class="sxs-lookup"><span data-stu-id="59ad0-121">Every hub method call is executed on a new hub instance.</span></span>  
> * <span data-ttu-id="59ad0-122">Use `await` ao chamar métodos assíncronos que dependem do hub de permanecer ativo.</span><span class="sxs-lookup"><span data-stu-id="59ad0-122">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="59ad0-123">Por exemplo, um método, como `Clients.All.SendAsync(...)` pode falhar se ele for chamado sem `await` e o método de hub seja concluída antes de `SendAsync` for concluída.</span><span class="sxs-lookup"><span data-stu-id="59ad0-123">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="59ad0-124">O objeto de contexto</span><span class="sxs-lookup"><span data-stu-id="59ad0-124">The Context object</span></span>

<span data-ttu-id="59ad0-125">O `Hub` classe tem um `Context` propriedade que contém as propriedades a seguir com informações sobre a conexão:</span><span class="sxs-lookup"><span data-stu-id="59ad0-125">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="59ad0-126">Propriedade</span><span class="sxs-lookup"><span data-stu-id="59ad0-126">Property</span></span> | <span data-ttu-id="59ad0-127">Descrição</span><span class="sxs-lookup"><span data-stu-id="59ad0-127">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="59ad0-128">Obtém a ID exclusiva para a conexão, atribuído pelo SignalR.</span><span class="sxs-lookup"><span data-stu-id="59ad0-128">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="59ad0-129">Há uma ID de conexão para cada conexão.</span><span class="sxs-lookup"><span data-stu-id="59ad0-129">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="59ad0-130">Obtém o [identificador de usuário](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="59ad0-130">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="59ad0-131">Por padrão, o SignalR usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado com a conexão como o identificador de usuário.</span><span class="sxs-lookup"><span data-stu-id="59ad0-131">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="59ad0-132">Obtém o `ClaimsPrincipal` associado ao usuário atual.</span><span class="sxs-lookup"><span data-stu-id="59ad0-132">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="59ad0-133">Obtém uma coleção de chave/valor que pode ser usada para compartilhar dados dentro do escopo dessa conexão.</span><span class="sxs-lookup"><span data-stu-id="59ad0-133">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="59ad0-134">Dados podem ser armazenados nessa coleção e ela será mantida para a conexão entre as invocações de método de hub diferentes.</span><span class="sxs-lookup"><span data-stu-id="59ad0-134">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="59ad0-135">Obtém a coleção de recursos disponíveis sobre a conexão.</span><span class="sxs-lookup"><span data-stu-id="59ad0-135">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="59ad0-136">Por enquanto, essa coleção não é necessária na maioria dos cenários, portanto, ele ainda não está documentado em detalhes.</span><span class="sxs-lookup"><span data-stu-id="59ad0-136">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="59ad0-137">Obtém um `CancellationToken` que notifica quando a conexão será anulada.</span><span class="sxs-lookup"><span data-stu-id="59ad0-137">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="59ad0-138">`Hub.Context` também contém os seguintes métodos:</span><span class="sxs-lookup"><span data-stu-id="59ad0-138">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="59ad0-139">Método</span><span class="sxs-lookup"><span data-stu-id="59ad0-139">Method</span></span> | <span data-ttu-id="59ad0-140">Descrição</span><span class="sxs-lookup"><span data-stu-id="59ad0-140">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="59ad0-141">Retorna o `HttpContext` para a conexão, ou `null` se a conexão não está associado uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="59ad0-141">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="59ad0-142">Para conexões HTTP, você pode usar esse método para obter informações como cadeias de caracteres de consulta e cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="59ad0-142">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="59ad0-143">Anula a conexão.</span><span class="sxs-lookup"><span data-stu-id="59ad0-143">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="59ad0-144">O objeto de clientes</span><span class="sxs-lookup"><span data-stu-id="59ad0-144">The Clients object</span></span>

<span data-ttu-id="59ad0-145">O `Hub` classe tem um `Clients` propriedade que contém as seguintes propriedades para a comunicação entre cliente e servidor:</span><span class="sxs-lookup"><span data-stu-id="59ad0-145">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="59ad0-146">Propriedade</span><span class="sxs-lookup"><span data-stu-id="59ad0-146">Property</span></span> | <span data-ttu-id="59ad0-147">Descrição</span><span class="sxs-lookup"><span data-stu-id="59ad0-147">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="59ad0-148">Chama um método em todos os clientes conectados</span><span class="sxs-lookup"><span data-stu-id="59ad0-148">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="59ad0-149">Chama um método no cliente que invocou o método de hub</span><span class="sxs-lookup"><span data-stu-id="59ad0-149">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="59ad0-150">Chama um método em todos os clientes conectados, exceto o cliente que invocou o método</span><span class="sxs-lookup"><span data-stu-id="59ad0-150">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="59ad0-151">`Hub.Clients` também contém os seguintes métodos:</span><span class="sxs-lookup"><span data-stu-id="59ad0-151">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="59ad0-152">Método</span><span class="sxs-lookup"><span data-stu-id="59ad0-152">Method</span></span> | <span data-ttu-id="59ad0-153">Descrição</span><span class="sxs-lookup"><span data-stu-id="59ad0-153">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="59ad0-154">Chama um método em todos os clientes conectados, exceto para as conexões especificadas</span><span class="sxs-lookup"><span data-stu-id="59ad0-154">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="59ad0-155">Chama um método em um cliente conectado específico</span><span class="sxs-lookup"><span data-stu-id="59ad0-155">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="59ad0-156">Chama um método em clientes conectados específicos</span><span class="sxs-lookup"><span data-stu-id="59ad0-156">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="59ad0-157">Chama um método em todas as conexões no grupo especificado</span><span class="sxs-lookup"><span data-stu-id="59ad0-157">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="59ad0-158">Chama um método em todas as conexões do grupo especificado, exceto as conexões especificadas</span><span class="sxs-lookup"><span data-stu-id="59ad0-158">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="59ad0-159">Chama um método em vários grupos de conexões</span><span class="sxs-lookup"><span data-stu-id="59ad0-159">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="59ad0-160">Chama um método em um grupo de conexões, excluindo o cliente que invocou o método de hub</span><span class="sxs-lookup"><span data-stu-id="59ad0-160">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="59ad0-161">Chama um método em todas as conexões associadas a um usuário específico</span><span class="sxs-lookup"><span data-stu-id="59ad0-161">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="59ad0-162">Chama um método em todas as conexões associadas com os usuários especificados</span><span class="sxs-lookup"><span data-stu-id="59ad0-162">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="59ad0-163">Cada propriedade ou método nas tabelas anteriores retorna um objeto com um `SendAsync` método.</span><span class="sxs-lookup"><span data-stu-id="59ad0-163">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="59ad0-164">O `SendAsync` método permite que você forneça o nome e parâmetros do método de cliente para chamar.</span><span class="sxs-lookup"><span data-stu-id="59ad0-164">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="59ad0-165">Enviar mensagens para os clientes</span><span class="sxs-lookup"><span data-stu-id="59ad0-165">Send messages to clients</span></span>

<span data-ttu-id="59ad0-166">Para fazer chamadas para clientes específicos, use as propriedades do `Clients` objeto.</span><span class="sxs-lookup"><span data-stu-id="59ad0-166">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="59ad0-167">No exemplo a seguir, há três métodos de Hub:</span><span class="sxs-lookup"><span data-stu-id="59ad0-167">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="59ad0-168">`SendMessage` envia uma mensagem para todos os clientes conectados usando `Clients.All`.</span><span class="sxs-lookup"><span data-stu-id="59ad0-168">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="59ad0-169">`SendMessageToCaller` envia uma mensagem de volta para o chamador usando `Clients.Caller`.</span><span class="sxs-lookup"><span data-stu-id="59ad0-169">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="59ad0-170">`SendMessageToGroups` envia uma mensagem a todos os clientes a `SignalR Users` grupo.</span><span class="sxs-lookup"><span data-stu-id="59ad0-170">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="59ad0-171">Hubs com rigidez de tipos</span><span class="sxs-lookup"><span data-stu-id="59ad0-171">Strongly typed hubs</span></span>

<span data-ttu-id="59ad0-172">Uma desvantagem de usar `SendAsync` é que ele se baseia em uma cadeia de caracteres mágica para especificar o método de cliente a ser chamado.</span><span class="sxs-lookup"><span data-stu-id="59ad0-172">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="59ad0-173">Isso deixa o código aberto para erros de tempo de execução se o nome do método está incorreto ou ausente do cliente.</span><span class="sxs-lookup"><span data-stu-id="59ad0-173">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="59ad0-174">Uma alternativa ao uso `SendAsync` é tipar fortemente os `Hub` com <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span><span class="sxs-lookup"><span data-stu-id="59ad0-174">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="59ad0-175">No exemplo a seguir, o `ChatHub` métodos de cliente foram extraídos por em uma interface chamada `IChatClient`.</span><span class="sxs-lookup"><span data-stu-id="59ad0-175">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>  

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="59ad0-176">Essa interface pode ser usada para refatorar anterior `ChatHub` exemplo.</span><span class="sxs-lookup"><span data-stu-id="59ad0-176">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="59ad0-177">Usando `Hub<IChatClient>` habilita a verificação de tempo de compilação dos métodos do cliente.</span><span class="sxs-lookup"><span data-stu-id="59ad0-177">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="59ad0-178">Isso evita problemas causados pelo uso de cadeias de caracteres mágicas desde `Hub<T>` só pode fornecer acesso aos métodos definidos na interface.</span><span class="sxs-lookup"><span data-stu-id="59ad0-178">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="59ad0-179">Usando fortemente tipado `Hub<T>` desabilita a capacidade de usar `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="59ad0-179">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="59ad0-180">Todos os métodos definidos na interface ainda podem ser definidos como assíncronos.</span><span class="sxs-lookup"><span data-stu-id="59ad0-180">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="59ad0-181">Na verdade, cada um desses métodos deve retornar um `Task`.</span><span class="sxs-lookup"><span data-stu-id="59ad0-181">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="59ad0-182">Uma vez que ele é uma interface, não use o `async` palavra-chave.</span><span class="sxs-lookup"><span data-stu-id="59ad0-182">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="59ad0-183">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="59ad0-183">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="59ad0-184">O `Async` sufixo não é removido do nome do método.</span><span class="sxs-lookup"><span data-stu-id="59ad0-184">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="59ad0-185">A menos que o método de cliente é definido com `.on('MyMethodAsync')`, você não deve usar `MyMethodAsync` como um nome.</span><span class="sxs-lookup"><span data-stu-id="59ad0-185">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="59ad0-186">Alterar o nome de um método de hub</span><span class="sxs-lookup"><span data-stu-id="59ad0-186">Change the name of a hub method</span></span>

<span data-ttu-id="59ad0-187">Por padrão, um nome de método de hub do servidor é o nome do método do .NET.</span><span class="sxs-lookup"><span data-stu-id="59ad0-187">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="59ad0-188">No entanto, você pode usar o [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) atributo para alterar esse padrão e especificar manualmente um nome para o método.</span><span class="sxs-lookup"><span data-stu-id="59ad0-188">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="59ad0-189">O cliente deve usar esse nome, em vez do nome de método do .NET, ao chamar o método.</span><span class="sxs-lookup"><span data-stu-id="59ad0-189">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="59ad0-190">Manipular eventos para uma conexão</span><span class="sxs-lookup"><span data-stu-id="59ad0-190">Handle events for a connection</span></span>

<span data-ttu-id="59ad0-191">A API de Hubs de SignalR fornece o `OnConnectedAsync` e `OnDisconnectedAsync` métodos virtuais para gerenciar e controlar conexões.</span><span class="sxs-lookup"><span data-stu-id="59ad0-191">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="59ad0-192">Substituir o `OnConnectedAsync` método virtual para executar ações quando um cliente se conecta ao Hub, como adicioná-lo a um grupo.</span><span class="sxs-lookup"><span data-stu-id="59ad0-192">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="59ad0-193">Substituir o `OnDisconnectedAsync` método virtual para executar ações quando um cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="59ad0-193">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="59ad0-194">Se o cliente se desconecta intencionalmente (chamando `connection.stop()`, por exemplo), o `exception` parâmetro será `null`.</span><span class="sxs-lookup"><span data-stu-id="59ad0-194">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="59ad0-195">No entanto, se o cliente for desconectado devido a um erro (como uma falha de rede), o `exception` parâmetro conterá uma exceção que descreve a falha.</span><span class="sxs-lookup"><span data-stu-id="59ad0-195">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="59ad0-196">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="59ad0-196">Handle errors</span></span>

<span data-ttu-id="59ad0-197">As exceções geradas em seus métodos de hub são enviadas ao cliente que invocou o método.</span><span class="sxs-lookup"><span data-stu-id="59ad0-197">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="59ad0-198">No cliente JavaScript, o `invoke` método retorna um [promessa JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="59ad0-198">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="59ad0-199">Quando o cliente recebe um erro com um manipulador anexado à promessa usando `catch`, ele tem chamado e passado como um JavaScript `Error` objeto.</span><span class="sxs-lookup"><span data-stu-id="59ad0-199">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="59ad0-200">Se o seu Hub de lançar uma exceção, as conexões não estão fechadas.</span><span class="sxs-lookup"><span data-stu-id="59ad0-200">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="59ad0-201">Por padrão, o SignalR retorna uma mensagem de erro genérica para o cliente.</span><span class="sxs-lookup"><span data-stu-id="59ad0-201">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="59ad0-202">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="59ad0-202">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="59ad0-203">Exceções inesperadas geralmente contêm informações confidenciais, como o nome de um servidor de banco de dados em uma exceção acionada quando a conexão de banco de dados falha.</span><span class="sxs-lookup"><span data-stu-id="59ad0-203">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="59ad0-204">O SignalR não expõe estas mensagens de erro detalhadas por padrão como medida de segurança.</span><span class="sxs-lookup"><span data-stu-id="59ad0-204">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="59ad0-205">Consulte a [artigo de considerações de segurança](xref:signalr/security#exceptions) para obter mais informações sobre por que os detalhes da exceção são suprimidos.</span><span class="sxs-lookup"><span data-stu-id="59ad0-205">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="59ad0-206">Se você tiver um excepcional de condição você *fazer* deseja propagar para o cliente, você pode usar o `HubException` classe.</span><span class="sxs-lookup"><span data-stu-id="59ad0-206">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="59ad0-207">Se você lançar uma `HubException` de seu método de hub do SignalR **será** enviar a mensagem inteira para o cliente, sem modificações.</span><span class="sxs-lookup"><span data-stu-id="59ad0-207">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="59ad0-208">O SignalR envia apenas o `Message` propriedade da exceção para o cliente.</span><span class="sxs-lookup"><span data-stu-id="59ad0-208">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="59ad0-209">O rastreamento de pilha e outras propriedades na exceção não estão disponíveis para o cliente.</span><span class="sxs-lookup"><span data-stu-id="59ad0-209">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="59ad0-210">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="59ad0-210">Related resources</span></span>

* [<span data-ttu-id="59ad0-211">Introdução ao SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59ad0-211">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="59ad0-212">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="59ad0-212">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="59ad0-213">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="59ad0-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
