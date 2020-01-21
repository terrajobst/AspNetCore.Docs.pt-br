---
title: Usar hubs no ASP.NET Core SignalR
author: bradygaster
description: Saiba como usar hubs no ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/hubs
ms.openlocfilehash: e5bc12c5ccafe2b5273d72e6bde0f631ca043428
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294635"
---
# <a name="use-hubs-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="2266f-103">Usar hubs no SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2266f-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="2266f-104">Por [Rachel appel](https://twitter.com/rachelappel) e [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="2266f-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="2266f-105">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2266f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-opno-locsignalr-hub"></a><span data-ttu-id="2266f-106">O que é um hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="2266f-106">What is a SignalR hub</span></span>

<span data-ttu-id="2266f-107">A API de hubs de SignalR permite que você chame métodos em clientes conectados do servidor.</span><span class="sxs-lookup"><span data-stu-id="2266f-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="2266f-108">No código do servidor, você define os métodos que são chamados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="2266f-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="2266f-109">No código do cliente, você define os métodos que são chamados do servidor.</span><span class="sxs-lookup"><span data-stu-id="2266f-109">In the client code, you define methods that are called from the server.</span></span> SignalR<span data-ttu-id="2266f-110"> cuida de tudo nos bastidores que possibilitam a comunicação de cliente para servidor e servidor para cliente em tempo real possível.</span><span class="sxs-lookup"><span data-stu-id="2266f-110"> takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-opno-locsignalr-hubs"></a><span data-ttu-id="2266f-111">Configurar hubs de SignalR</span><span class="sxs-lookup"><span data-stu-id="2266f-111">Configure SignalR hubs</span></span>

<span data-ttu-id="2266f-112">O middleware SignalR requer alguns serviços, que são configurados chamando `services.AddSignalR`.</span><span class="sxs-lookup"><span data-stu-id="2266f-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2266f-113">Ao adicionar SignalR funcionalidade a um aplicativo ASP.NET Core, configure SignalR rotas chamando `endpoint.MapHub` no retorno de chamada `Startup.Configure` do método `app.UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="2266f-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="2266f-114">Ao adicionar SignalR funcionalidade a um aplicativo ASP.NET Core, configure SignalR rotas chamando `app.UseSignalR` no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="2266f-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="2266f-115">Criar e usar hubs</span><span class="sxs-lookup"><span data-stu-id="2266f-115">Create and use hubs</span></span>

<span data-ttu-id="2266f-116">Crie um hub declarando uma classe que herda de `Hub`e adicione métodos públicos a ela.</span><span class="sxs-lookup"><span data-stu-id="2266f-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="2266f-117">Os clientes podem chamar métodos que são definidos como `public`.</span><span class="sxs-lookup"><span data-stu-id="2266f-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="2266f-118">Você pode especificar um tipo de retorno e parâmetros, incluindo tipos complexos e matrizes, como faria em C# qualquer método.</span><span class="sxs-lookup"><span data-stu-id="2266f-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> SignalR<span data-ttu-id="2266f-119"> lida com a serialização e desserialização de objetos e matrizes complexos em seus parâmetros e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="2266f-119"> handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="2266f-120">Os hubs são transitórios:</span><span class="sxs-lookup"><span data-stu-id="2266f-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="2266f-121">Não armazene o estado em uma propriedade na classe Hub.</span><span class="sxs-lookup"><span data-stu-id="2266f-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="2266f-122">Cada chamada de método de Hub é executada em uma nova instância de Hub.</span><span class="sxs-lookup"><span data-stu-id="2266f-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="2266f-123">Use `await` ao chamar métodos assíncronos que dependem do Hub permanecendo ativo.</span><span class="sxs-lookup"><span data-stu-id="2266f-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="2266f-124">Por exemplo, um método como `Clients.All.SendAsync(...)` pode falhar se for chamado sem `await` e o método de Hub for concluído antes de `SendAsync` ser concluído.</span><span class="sxs-lookup"><span data-stu-id="2266f-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="2266f-125">O objeto de contexto</span><span class="sxs-lookup"><span data-stu-id="2266f-125">The Context object</span></span>

<span data-ttu-id="2266f-126">A classe `Hub` tem uma propriedade `Context` que contém as seguintes propriedades com informações sobre a conexão:</span><span class="sxs-lookup"><span data-stu-id="2266f-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="2266f-127">propriedade</span><span class="sxs-lookup"><span data-stu-id="2266f-127">Property</span></span> | <span data-ttu-id="2266f-128">Descrição</span><span class="sxs-lookup"><span data-stu-id="2266f-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="2266f-129">Obtém a ID exclusiva da conexão, atribuída por SignalR.</span><span class="sxs-lookup"><span data-stu-id="2266f-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="2266f-130">Há uma ID de conexão para cada conexão.</span><span class="sxs-lookup"><span data-stu-id="2266f-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="2266f-131">Obtém o [identificador de usuário](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="2266f-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="2266f-132">Por padrão, SignalR usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado à conexão como o identificador de usuário.</span><span class="sxs-lookup"><span data-stu-id="2266f-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="2266f-133">Obtém o `ClaimsPrincipal` associado ao usuário atual.</span><span class="sxs-lookup"><span data-stu-id="2266f-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="2266f-134">Obtém uma coleção de chave/valor que pode ser usada para compartilhar dados dentro do escopo desta conexão.</span><span class="sxs-lookup"><span data-stu-id="2266f-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="2266f-135">Os dados podem ser armazenados nessa coleção e serão mantidos para a conexão entre invocações de método de Hub diferentes.</span><span class="sxs-lookup"><span data-stu-id="2266f-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="2266f-136">Obtém a coleção de recursos disponíveis na conexão.</span><span class="sxs-lookup"><span data-stu-id="2266f-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="2266f-137">Por enquanto, essa coleção não é necessária na maioria dos cenários, portanto, ela ainda não está documentada em detalhes.</span><span class="sxs-lookup"><span data-stu-id="2266f-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="2266f-138">Obtém um `CancellationToken` que notifica quando a conexão é anulada.</span><span class="sxs-lookup"><span data-stu-id="2266f-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="2266f-139">o `Hub.Context` também contém os seguintes métodos:</span><span class="sxs-lookup"><span data-stu-id="2266f-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="2266f-140">Método</span><span class="sxs-lookup"><span data-stu-id="2266f-140">Method</span></span> | <span data-ttu-id="2266f-141">Descrição</span><span class="sxs-lookup"><span data-stu-id="2266f-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="2266f-142">Retorna o `HttpContext` para a conexão ou `null` se a conexão não estiver associada a uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="2266f-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="2266f-143">Para conexões HTTP, você pode usar esse método para obter informações como cabeçalhos HTTP e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="2266f-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="2266f-144">Anula a conexão.</span><span class="sxs-lookup"><span data-stu-id="2266f-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="2266f-145">O objeto clients</span><span class="sxs-lookup"><span data-stu-id="2266f-145">The Clients object</span></span>

<span data-ttu-id="2266f-146">A classe `Hub` tem uma propriedade `Clients` que contém as seguintes propriedades para comunicação entre o servidor e o cliente:</span><span class="sxs-lookup"><span data-stu-id="2266f-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="2266f-147">propriedade</span><span class="sxs-lookup"><span data-stu-id="2266f-147">Property</span></span> | <span data-ttu-id="2266f-148">Descrição</span><span class="sxs-lookup"><span data-stu-id="2266f-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="2266f-149">Chama um método em todos os clientes conectados</span><span class="sxs-lookup"><span data-stu-id="2266f-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="2266f-150">Chama um método no cliente que invocou o método de Hub</span><span class="sxs-lookup"><span data-stu-id="2266f-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="2266f-151">Chama um método em todos os clientes conectados, exceto o cliente que invocou o método</span><span class="sxs-lookup"><span data-stu-id="2266f-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="2266f-152">o `Hub.Clients` também contém os seguintes métodos:</span><span class="sxs-lookup"><span data-stu-id="2266f-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="2266f-153">Método</span><span class="sxs-lookup"><span data-stu-id="2266f-153">Method</span></span> | <span data-ttu-id="2266f-154">Descrição</span><span class="sxs-lookup"><span data-stu-id="2266f-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="2266f-155">Chama um método em todos os clientes conectados, exceto para as conexões especificadas</span><span class="sxs-lookup"><span data-stu-id="2266f-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="2266f-156">Chama um método em um cliente conectado específico</span><span class="sxs-lookup"><span data-stu-id="2266f-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="2266f-157">Chama um método em clientes conectados específicos</span><span class="sxs-lookup"><span data-stu-id="2266f-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="2266f-158">Chama um método em todas as conexões no grupo especificado</span><span class="sxs-lookup"><span data-stu-id="2266f-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="2266f-159">Chama um método em todas as conexões no grupo especificado, exceto as conexões especificadas</span><span class="sxs-lookup"><span data-stu-id="2266f-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="2266f-160">Chama um método em vários grupos de conexões</span><span class="sxs-lookup"><span data-stu-id="2266f-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="2266f-161">Chama um método em um grupo de conexões, excluindo o cliente que invocou o método de Hub</span><span class="sxs-lookup"><span data-stu-id="2266f-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="2266f-162">Chama um método em todas as conexões associadas a um usuário específico</span><span class="sxs-lookup"><span data-stu-id="2266f-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="2266f-163">Chama um método em todas as conexões associadas aos usuários especificados</span><span class="sxs-lookup"><span data-stu-id="2266f-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="2266f-164">Cada propriedade ou método nas tabelas anteriores retorna um objeto com um método `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="2266f-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="2266f-165">O método `SendAsync` permite que você forneça o nome e os parâmetros do método de cliente a ser chamado.</span><span class="sxs-lookup"><span data-stu-id="2266f-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="2266f-166">Enviar mensagens para clientes</span><span class="sxs-lookup"><span data-stu-id="2266f-166">Send messages to clients</span></span>

<span data-ttu-id="2266f-167">Para fazer chamadas para clientes específicos, use as propriedades do objeto `Clients`.</span><span class="sxs-lookup"><span data-stu-id="2266f-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="2266f-168">No exemplo a seguir, há três métodos de Hub:</span><span class="sxs-lookup"><span data-stu-id="2266f-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="2266f-169">`SendMessage` envia uma mensagem para todos os clientes conectados, usando `Clients.All`.</span><span class="sxs-lookup"><span data-stu-id="2266f-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="2266f-170">`SendMessageToCaller` envia uma mensagem de volta ao chamador, usando `Clients.Caller`.</span><span class="sxs-lookup"><span data-stu-id="2266f-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="2266f-171">`SendMessageToGroups` envia uma mensagem para todos os clientes no grupo de `SignalR Users`.</span><span class="sxs-lookup"><span data-stu-id="2266f-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="2266f-172">Hubs com rigidez de tipos</span><span class="sxs-lookup"><span data-stu-id="2266f-172">Strongly typed hubs</span></span>

<span data-ttu-id="2266f-173">Uma desvantagem de usar `SendAsync` é que ela se baseia em uma cadeia de caracteres mágica para especificar o método de cliente a ser chamado.</span><span class="sxs-lookup"><span data-stu-id="2266f-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="2266f-174">Isso deixa o código aberto para erros de tempo de execução se o nome do método for digitado incorretamente ou ausente no cliente.</span><span class="sxs-lookup"><span data-stu-id="2266f-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="2266f-175">Uma alternativa ao uso de `SendAsync` é digitar fortemente o `Hub` com <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span><span class="sxs-lookup"><span data-stu-id="2266f-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="2266f-176">No exemplo a seguir, os métodos de cliente `ChatHub` foram extraídos em uma interface chamada `IChatClient`.</span><span class="sxs-lookup"><span data-stu-id="2266f-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="2266f-177">Essa interface pode ser usada para refatorar o exemplo de `ChatHub` anterior.</span><span class="sxs-lookup"><span data-stu-id="2266f-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="2266f-178">O uso de `Hub<IChatClient>` permite a verificação de tempo de compilação dos métodos de cliente.</span><span class="sxs-lookup"><span data-stu-id="2266f-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="2266f-179">Isso evita problemas causados pelo uso de cadeias de caracteres mágicas, pois `Hub<T>` só pode fornecer acesso aos métodos definidos na interface.</span><span class="sxs-lookup"><span data-stu-id="2266f-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="2266f-180">Usar um `Hub<T>` fortemente tipado desabilita a capacidade de usar `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="2266f-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="2266f-181">Todos os métodos definidos na interface ainda podem ser definidos como assíncronos.</span><span class="sxs-lookup"><span data-stu-id="2266f-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="2266f-182">Na verdade, cada um desses métodos deve retornar um `Task`.</span><span class="sxs-lookup"><span data-stu-id="2266f-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="2266f-183">Como é uma interface, não use a palavra-chave `async`.</span><span class="sxs-lookup"><span data-stu-id="2266f-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="2266f-184">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="2266f-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="2266f-185">O sufixo de `Async` não é removido do nome do método.</span><span class="sxs-lookup"><span data-stu-id="2266f-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="2266f-186">A menos que o método de cliente seja definido com `.on('MyMethodAsync')`, você não deve usar `MyMethodAsync` como um nome.</span><span class="sxs-lookup"><span data-stu-id="2266f-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="2266f-187">Alterar o nome de um método de Hub</span><span class="sxs-lookup"><span data-stu-id="2266f-187">Change the name of a hub method</span></span>

<span data-ttu-id="2266f-188">Por padrão, um nome de método de Hub de servidor é o nome do método .NET.</span><span class="sxs-lookup"><span data-stu-id="2266f-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="2266f-189">No entanto, você pode usar o atributo [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) para alterar esse padrão e especificar manualmente um nome para o método.</span><span class="sxs-lookup"><span data-stu-id="2266f-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="2266f-190">O cliente deve usar esse nome, em vez do nome do método .NET, ao invocar o método.</span><span class="sxs-lookup"><span data-stu-id="2266f-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="2266f-191">Manipular eventos para uma conexão</span><span class="sxs-lookup"><span data-stu-id="2266f-191">Handle events for a connection</span></span>

<span data-ttu-id="2266f-192">A API de hubs de SignalR fornece o `OnConnectedAsync` e `OnDisconnectedAsync` métodos virtuais para gerenciar e controlar as conexões.</span><span class="sxs-lookup"><span data-stu-id="2266f-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="2266f-193">Substitua o método virtual `OnConnectedAsync` para executar ações quando um cliente se conectar ao Hub, como adicioná-lo a um grupo.</span><span class="sxs-lookup"><span data-stu-id="2266f-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="2266f-194">Substitua o método virtual `OnDisconnectedAsync` para executar ações quando um cliente se desconectar.</span><span class="sxs-lookup"><span data-stu-id="2266f-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="2266f-195">Se o cliente se desconectar intencionalmente (chamando `connection.stop()`, por exemplo), o parâmetro `exception` será `null`.</span><span class="sxs-lookup"><span data-stu-id="2266f-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="2266f-196">No entanto, se o cliente for desconectado devido a um erro (como uma falha de rede), o parâmetro `exception` conterá uma exceção que descreve a falha.</span><span class="sxs-lookup"><span data-stu-id="2266f-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a><span data-ttu-id="2266f-197">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="2266f-197">Handle errors</span></span>

<span data-ttu-id="2266f-198">As exceções geradas em seus métodos de Hub são enviadas ao cliente que invocou o método.</span><span class="sxs-lookup"><span data-stu-id="2266f-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="2266f-199">No cliente JavaScript, o método `invoke` retorna uma [promessa de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="2266f-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="2266f-200">Quando o cliente recebe um erro com um manipulador anexado à promessa usando `catch`, ele é invocado e passado como um objeto JavaScript `Error`.</span><span class="sxs-lookup"><span data-stu-id="2266f-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="2266f-201">Se o Hub lançar uma exceção, as conexões não serão fechadas.</span><span class="sxs-lookup"><span data-stu-id="2266f-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="2266f-202">Por padrão, SignalR retorna uma mensagem de erro genérica para o cliente.</span><span class="sxs-lookup"><span data-stu-id="2266f-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="2266f-203">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="2266f-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="2266f-204">Exceções inesperadas geralmente contêm informações confidenciais, como o nome de um servidor de banco de dados em uma exceção disparada quando a conexão de banco de dados falha.</span><span class="sxs-lookup"><span data-stu-id="2266f-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="2266f-205">o SignalR não expõe essas mensagens de erro detalhadas por padrão como uma medida de segurança.</span><span class="sxs-lookup"><span data-stu-id="2266f-205">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="2266f-206">Consulte o [artigo considerações sobre segurança](xref:signalr/security#exceptions) para obter mais informações sobre por que os detalhes da exceção são suprimidos.</span><span class="sxs-lookup"><span data-stu-id="2266f-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="2266f-207">Se você tiver uma condição *excepcional que deseja* propagar para o cliente, poderá usar a classe `HubException`.</span><span class="sxs-lookup"><span data-stu-id="2266f-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="2266f-208">Se você lançar uma `HubException` do seu método de Hub, **SignalR** enviará a mensagem inteira para o cliente, sem modificações.</span><span class="sxs-lookup"><span data-stu-id="2266f-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR<span data-ttu-id="2266f-209"> envia apenas a propriedade `Message` da exceção para o cliente.</span><span class="sxs-lookup"><span data-stu-id="2266f-209"> only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="2266f-210">O rastreamento de pilha e outras propriedades na exceção não estão disponíveis para o cliente.</span><span class="sxs-lookup"><span data-stu-id="2266f-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="2266f-211">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="2266f-211">Related resources</span></span>

* <span data-ttu-id="2266f-212">[Introdução à ASP.NET Core SignalR](xref:signalr/introduction)</span><span class="sxs-lookup"><span data-stu-id="2266f-212">[Intro to ASP.NET Core SignalR](xref:signalr/introduction)</span></span>
* [<span data-ttu-id="2266f-213">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="2266f-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="2266f-214">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="2266f-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
