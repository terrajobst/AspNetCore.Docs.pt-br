---
title: O que há de novo no ASP.NET Core 3,0
author: rick-anderson
description: Saiba mais sobre os novos recursos do ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: aspnetcore-3.0
ms.openlocfilehash: 8c53d8a9fa222ca40f26dc713ec3b70ddde76539
ms.sourcegitcommit: eb2fe5ad2e82fab86ca952463af8d017ba659b25
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416120"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="6427b-103">O que há de novo no ASP.NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="6427b-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="6427b-104">Este artigo destaca as alterações mais significativas no ASP.NET Core 3,0 com links para a documentação relevante.</span><span class="sxs-lookup"><span data-stu-id="6427b-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## <a name="blazor"></a><span data-ttu-id="6427b-105">Blazor</span><span class="sxs-lookup"><span data-stu-id="6427b-105">Blazor</span></span>

<span data-ttu-id="6427b-106">O mais novo é uma estrutura em ASP.NET Core para a criação de interface do usuário da Web interativa do lado do cliente com o .NET:</span><span class="sxs-lookup"><span data-stu-id="6427b-106">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="6427b-107">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6427b-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="6427b-108">Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.</span><span class="sxs-lookup"><span data-stu-id="6427b-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="6427b-109">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="6427b-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="6427b-110">Cenários com suporte da estrutura de mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="6427b-110">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="6427b-111">Componentes de interface do usuário reutilizáveis (componentes do Razor)</span><span class="sxs-lookup"><span data-stu-id="6427b-111">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="6427b-112">Roteamento do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="6427b-112">Client-side routing</span></span>
* <span data-ttu-id="6427b-113">Layouts de componente</span><span class="sxs-lookup"><span data-stu-id="6427b-113">Component layouts</span></span>
* <span data-ttu-id="6427b-114">Suporte para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="6427b-114">Support for dependency injection</span></span>
* <span data-ttu-id="6427b-115">Formulários e validação</span><span class="sxs-lookup"><span data-stu-id="6427b-115">Forms and validation</span></span>
* <span data-ttu-id="6427b-116">Criar bibliotecas de componentes com bibliotecas de classes do Razor</span><span class="sxs-lookup"><span data-stu-id="6427b-116">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="6427b-117">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="6427b-117">JavaScript interop</span></span>

<span data-ttu-id="6427b-118">Para obter mais informações, consulte <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="6427b-118">For more information, see <xref:blazor/index>.</span></span>

### <a name="blazor-server"></a><span data-ttu-id="6427b-119">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="6427b-119">Blazor Server</span></span>

<span data-ttu-id="6427b-120">Os componentes Blazor desvinculam a lógica de renderização do componente da forma como as atualizações da interface do usuário são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="6427b-120">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="6427b-121">O servidor mais incrivelmente dá suporte para hospedar componentes do Razor no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6427b-121">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="6427b-122">As atualizações da interface do usuário são tratadas por uma conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="6427b-122">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="6427b-123">Há suporte para o servidor mais incrivelmente no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="6427b-123">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a><span data-ttu-id="6427b-124">Webassembly mais incrivelmente (visualização)</span><span class="sxs-lookup"><span data-stu-id="6427b-124">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="6427b-125">Os aplicativos mais fáceis também podem ser executados diretamente no navegador usando um tempo de execução .NET baseado em Webassembly.</span><span class="sxs-lookup"><span data-stu-id="6427b-125">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="6427b-126">Webassembly de mais de-teste está em versão prévia e *sem* suporte no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="6427b-126">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="6427b-127">O Webassembly mais incrivelmente terá suporte em uma versão futura do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6427b-127">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="6427b-128">Componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="6427b-128">Razor components</span></span>

<span data-ttu-id="6427b-129">Os aplicativos mais elaborados são criados a partir de componentes.</span><span class="sxs-lookup"><span data-stu-id="6427b-129">Blazor apps are built from components.</span></span> <span data-ttu-id="6427b-130">Os componentes são partes independentes da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="6427b-130">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="6427b-131">Os componentes são classes normais do .NET que definem a lógica de renderização da interface do usuário e manipuladores de eventos do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="6427b-131">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="6427b-132">Você pode criar aplicativos Web interativos sofisticados sem JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6427b-132">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="6427b-133">Os componentes no mais claro normalmente são criados usando sintaxe Razor, uma mistura natural de HTML e C#.</span><span class="sxs-lookup"><span data-stu-id="6427b-133">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="6427b-134">Os componentes do Razor são semelhantes às exibições Razor Pages e MVC, pois ambos usam o Razor.</span><span class="sxs-lookup"><span data-stu-id="6427b-134">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="6427b-135">Ao contrário de páginas e exibições, que se baseiam em um modelo de solicitação-resposta, os componentes são usados especificamente para manipular a composição da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="6427b-135">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="6427b-136">gRPC</span><span class="sxs-lookup"><span data-stu-id="6427b-136">gRPC</span></span>

<span data-ttu-id="6427b-137">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="6427b-137">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="6427b-138">É uma estrutura popular de RPC de alto desempenho (chamada de procedimento remoto).</span><span class="sxs-lookup"><span data-stu-id="6427b-138">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="6427b-139">Oferece uma abordagem de contrato conceituada para o desenvolvimento de API.</span><span class="sxs-lookup"><span data-stu-id="6427b-139">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="6427b-140">O usa tecnologias modernas, como:</span><span class="sxs-lookup"><span data-stu-id="6427b-140">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="6427b-141">HTTP/2 para transporte.</span><span class="sxs-lookup"><span data-stu-id="6427b-141">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="6427b-142">Buffers de protocolo como linguagem de descrição de interface.</span><span class="sxs-lookup"><span data-stu-id="6427b-142">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="6427b-143">Formato de serialização binária.</span><span class="sxs-lookup"><span data-stu-id="6427b-143">Binary serialization format.</span></span>
* <span data-ttu-id="6427b-144">Fornece recursos como:</span><span class="sxs-lookup"><span data-stu-id="6427b-144">Provides features such as:</span></span>

  * <span data-ttu-id="6427b-145">Autenticação</span><span class="sxs-lookup"><span data-stu-id="6427b-145">Authentication</span></span>
  * <span data-ttu-id="6427b-146">Fluxo bidirecional e controle de fluxo.</span><span class="sxs-lookup"><span data-stu-id="6427b-146">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="6427b-147">Cancelamento e tempos limite.</span><span class="sxs-lookup"><span data-stu-id="6427b-147">Cancellation and timeouts.</span></span>

<span data-ttu-id="6427b-148">a funcionalidade gRPC no ASP.NET Core 3,0 inclui:</span><span class="sxs-lookup"><span data-stu-id="6427b-148">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="6427b-149">[Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; uma estrutura de ASP.NET Core para hospedar serviços Grpc.</span><span class="sxs-lookup"><span data-stu-id="6427b-149">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="6427b-150">o gRPC em ASP.NET Core integra-se com recursos de ASP.NET Core padrão, como registro em log, injeção de dependência (DI), autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="6427b-150">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="6427b-151">[Grpc .net. client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; um cliente Grpc para .NET Core que se baseia na `HttpClient`familiar.</span><span class="sxs-lookup"><span data-stu-id="6427b-151">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="6427b-152">[Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; Grpc integração de cliente com o `HttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="6427b-152">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="6427b-153">Para obter mais informações, consulte <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="6427b-153">For more information, see <xref:grpc/index>.</span></span>

## <a name="signalr"></a><span data-ttu-id="6427b-154">SignalR</span><span class="sxs-lookup"><span data-stu-id="6427b-154">SignalR</span></span>

<span data-ttu-id="6427b-155">Consulte [atualizar o código do sinalizador](xref:migration/22-to-30#signalr) para obter instruções de migração.</span><span class="sxs-lookup"><span data-stu-id="6427b-155">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="6427b-156">O signalr agora usa `System.Text.Json` para serializar/desserializar mensagens JSON.</span><span class="sxs-lookup"><span data-stu-id="6427b-156">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="6427b-157">Consulte [mudar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson) para obter instruções para restaurar o serializador baseado em `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="6427b-157">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="6427b-158">Nos clientes JavaScript e .NET para Signalr, foi adicionado suporte para reconexão automática.</span><span class="sxs-lookup"><span data-stu-id="6427b-158">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="6427b-159">Por padrão, o cliente tenta se reconectar imediatamente e tentar novamente após 2, 10 e 30 segundos, se necessário.</span><span class="sxs-lookup"><span data-stu-id="6427b-159">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="6427b-160">Se o cliente reconectar-se com êxito, ele receberá uma nova ID de conexão.</span><span class="sxs-lookup"><span data-stu-id="6427b-160">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="6427b-161">A reconexão automática é opcional:</span><span class="sxs-lookup"><span data-stu-id="6427b-161">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="6427b-162">Os intervalos de reconexão podem ser especificados passando uma matriz de durações baseadas em milissegundos:</span><span class="sxs-lookup"><span data-stu-id="6427b-162">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="6427b-163">Uma implementação personalizada pode ser passada para controle total dos intervalos de reconexão.</span><span class="sxs-lookup"><span data-stu-id="6427b-163">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="6427b-164">Se a reconexão falhar após o último intervalo de reconexão:</span><span class="sxs-lookup"><span data-stu-id="6427b-164">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="6427b-165">O cliente considera que a conexão está offline.</span><span class="sxs-lookup"><span data-stu-id="6427b-165">The client considers the connection is offline.</span></span>
* <span data-ttu-id="6427b-166">O cliente para de tentar se reconectar.</span><span class="sxs-lookup"><span data-stu-id="6427b-166">The client stops trying to reconnect.</span></span>

<span data-ttu-id="6427b-167">Durante as tentativas de reconexão, atualize a interface de usuário do aplicativo para notificar o usuário de que a reconexão está sendo tentada.</span><span class="sxs-lookup"><span data-stu-id="6427b-167">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="6427b-168">Para fornecer comentários da interface do usuário quando a conexão é interrompida, a API de cliente do Signalr foi expandida para incluir os seguintes manipuladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="6427b-168">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="6427b-169">`onreconnecting`: dá aos desenvolvedores uma oportunidade para desabilitar a interface do usuário ou para permitir que os usuários saibam que o aplicativo está offline.</span><span class="sxs-lookup"><span data-stu-id="6427b-169">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="6427b-170">`onreconnected`: dá aos desenvolvedores uma oportunidade de atualizar a interface do usuário quando a conexão é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="6427b-170">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="6427b-171">O código a seguir usa `onreconnecting` para atualizar a interface do usuário ao tentar se conectar:</span><span class="sxs-lookup"><span data-stu-id="6427b-171">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="6427b-172">O código a seguir usa `onreconnected` para atualizar a interface do usuário na conexão:</span><span class="sxs-lookup"><span data-stu-id="6427b-172">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="6427b-173">O signalr 3,0 e posterior fornece um recurso personalizado para manipuladores de autorização quando um método de Hub requer autorização.</span><span class="sxs-lookup"><span data-stu-id="6427b-173">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="6427b-174">O recurso é uma instância do `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="6427b-174">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="6427b-175">O `HubInvocationContext` inclui:</span><span class="sxs-lookup"><span data-stu-id="6427b-175">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="6427b-176">Nome do método de Hub que está sendo invocado.</span><span class="sxs-lookup"><span data-stu-id="6427b-176">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="6427b-177">Argumentos para o método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6427b-177">Arguments to the hub method.</span></span>

<span data-ttu-id="6427b-178">Considere o exemplo a seguir de um aplicativo de sala de chat que permite a entrada de várias organizações por meio de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="6427b-178">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="6427b-179">Qualquer pessoa com um conta Microsoft pode entrar no chat, mas somente os membros da organização proprietária podem proibir os usuários ou exibir os históricos de chat dos usuários.</span><span class="sxs-lookup"><span data-stu-id="6427b-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users’ chat histories.</span></span> <span data-ttu-id="6427b-180">O aplicativo pode restringir determinada funcionalidade de usuários específicos.</span><span class="sxs-lookup"><span data-stu-id="6427b-180">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="6427b-181">No código anterior, `DomainRestrictedRequirement` serve como uma `IAuthorizationRequirement`personalizada.</span><span class="sxs-lookup"><span data-stu-id="6427b-181">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="6427b-182">Como o parâmetro de recurso `HubInvocationContext` está sendo passado, a lógica interna pode:</span><span class="sxs-lookup"><span data-stu-id="6427b-182">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="6427b-183">Inspecione o contexto no qual o Hub está sendo chamado.</span><span class="sxs-lookup"><span data-stu-id="6427b-183">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="6427b-184">Tome decisões sobre como permitir que o usuário execute métodos de Hub individuais.</span><span class="sxs-lookup"><span data-stu-id="6427b-184">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="6427b-185">Os métodos de Hub individuais podem ser decorados com o nome da política que o código verifica em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="6427b-185">Individual Hub methods can be decorated with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="6427b-186">À medida que os clientes tentam chamar métodos de Hub individuais, o manipulador de `DomainRestrictedRequirement` é executado e controla o acesso aos métodos.</span><span class="sxs-lookup"><span data-stu-id="6427b-186">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="6427b-187">Com base no modo como o `DomainRestrictedRequirement` controla o acesso:</span><span class="sxs-lookup"><span data-stu-id="6427b-187">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="6427b-188">Todos os usuários conectados podem chamar o método `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="6427b-188">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="6427b-189">Somente os usuários que fizeram logon com um endereço de email `@jabbr.net` podem exibir os históricos dos usuários.</span><span class="sxs-lookup"><span data-stu-id="6427b-189">Only users who have logged in with a `@jabbr.net` email address can view users’ histories.</span></span>
* <span data-ttu-id="6427b-190">Somente `bob42@jabbr.net` pode proibir os usuários da sala de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="6427b-190">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="6427b-191">Criar a política de `DomainRestricted` pode envolver:</span><span class="sxs-lookup"><span data-stu-id="6427b-191">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="6427b-192">Em *Startup.cs*, adicionando a nova política.</span><span class="sxs-lookup"><span data-stu-id="6427b-192">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="6427b-193">Forneça o requisito de `DomainRestrictedRequirement` personalizado como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6427b-193">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="6427b-194">Registrando `DomainRestricted` com o middleware de autorização.</span><span class="sxs-lookup"><span data-stu-id="6427b-194">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="6427b-195">Os hubs de sinalização usam [Roteamento de ponto de extremidade](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="6427b-195">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="6427b-196">A conexão do Hub do signalr foi feita explicitamente anteriormente:</span><span class="sxs-lookup"><span data-stu-id="6427b-196">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="6427b-197">Na versão anterior, os desenvolvedores precisavam conectar controladores, páginas Razor e hubs em uma variedade de lugares.</span><span class="sxs-lookup"><span data-stu-id="6427b-197">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="6427b-198">A conexão explícita resulta em uma série de segmentos de roteamento quase idênticos:</span><span class="sxs-lookup"><span data-stu-id="6427b-198">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="6427b-199">Os hubs 3,0 do signalr podem ser roteados por meio do roteamento de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="6427b-199">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="6427b-200">Com o roteamento de ponto de extremidade, normalmente todo o roteamento pode ser configurado no `UseRouting`:</span><span class="sxs-lookup"><span data-stu-id="6427b-200">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="6427b-201">ASP.NET Core Signaler 3,0 adicionado:</span><span class="sxs-lookup"><span data-stu-id="6427b-201">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="6427b-202">Streaming de cliente para servidor.</span><span class="sxs-lookup"><span data-stu-id="6427b-202">Client-to-server streaming.</span></span> <span data-ttu-id="6427b-203">Com o streaming de cliente para servidor, os métodos do lado do servidor podem ter instâncias de um `IAsyncEnumerable<T>` ou `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="6427b-203">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="6427b-204">No exemplo a C# seguir, o método `UploadStream` no Hub receberá um fluxo de cadeias de caracteres do cliente:</span><span class="sxs-lookup"><span data-stu-id="6427b-204">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="6427b-205">Os aplicativos cliente .NET podem passar uma instância `IAsyncEnumerable<T>` ou `ChannelReader<T>` como o argumento `stream` do método Hub `UploadStream` acima.</span><span class="sxs-lookup"><span data-stu-id="6427b-205">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="6427b-206">Depois que o loop de `for` for concluído e a função local sair, a conclusão do fluxo será enviada:</span><span class="sxs-lookup"><span data-stu-id="6427b-206">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="6427b-207">Os aplicativos cliente JavaScript usam o `Subject` do Signalr (ou um [assunto RxJS](https://rxjs.dev/api/index/class/Subject)) para o argumento `stream` do método de Hub `UploadStream` acima.</span><span class="sxs-lookup"><span data-stu-id="6427b-207">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="6427b-208">O código JavaScript pode usar o método `subject.next` para manipular cadeias de caracteres à medida que elas são capturadas e prontas para serem enviadas ao servidor.</span><span class="sxs-lookup"><span data-stu-id="6427b-208">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="6427b-209">Usando um código como os dois trechos anteriores, as experiências de streaming em tempo real podem ser criadas.</span><span class="sxs-lookup"><span data-stu-id="6427b-209">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="6427b-210">Nova serialização JSON</span><span class="sxs-lookup"><span data-stu-id="6427b-210">New JSON serialization</span></span>

<span data-ttu-id="6427b-211">ASP.NET Core o 3,0 agora usa <xref:System.Text.Json> por padrão para serialização JSON:</span><span class="sxs-lookup"><span data-stu-id="6427b-211">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="6427b-212">Lê e grava JSON de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="6427b-212">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="6427b-213">É otimizado para texto UTF-8.</span><span class="sxs-lookup"><span data-stu-id="6427b-213">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="6427b-214">Normalmente, maior desempenho do que `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="6427b-214">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="6427b-215">Para adicionar o Json.NET ao ASP.NET Core 3,0, consulte [Adicionar suporte ao formato JSON baseado em Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="6427b-215">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="6427b-216">Novas diretivas do Razor</span><span class="sxs-lookup"><span data-stu-id="6427b-216">New Razor directives</span></span>

<span data-ttu-id="6427b-217">A lista a seguir contém novas diretivas do Razor:</span><span class="sxs-lookup"><span data-stu-id="6427b-217">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="6427b-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; diretiva de `@attribute` aplica o atributo fornecido à classe da página ou exibição gerada.</span><span class="sxs-lookup"><span data-stu-id="6427b-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="6427b-219">Por exemplo, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="6427b-219">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="6427b-220">[@implements](xref:mvc/views/razor#implements) &ndash; a diretiva de `@implements` implementa uma interface para a classe gerada.</span><span class="sxs-lookup"><span data-stu-id="6427b-220">[@implements](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="6427b-221">Por exemplo, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="6427b-221">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="6427b-222">O IdentityServer4 dá suporte à autenticação e autorização para APIs Web e SPAs</span><span class="sxs-lookup"><span data-stu-id="6427b-222">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="6427b-223">O ASP.NET Core 3,0 oferece autenticação em aplicativos de página única (SPAs) usando o suporte para autorização da API Web.</span><span class="sxs-lookup"><span data-stu-id="6427b-223">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="6427b-224">ASP.NET Core identidade para autenticação e armazenamento de usuários é combinada com o [IdentityServer4](https://identityserver.io/) para implementar o Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="6427b-224">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="6427b-225">IdentityServer4 é uma estrutura de OpenID Connect e OAuth 2,0 para ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="6427b-225">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="6427b-226">Ele habilita os seguintes recursos de segurança:</span><span class="sxs-lookup"><span data-stu-id="6427b-226">It enables the following security features:</span></span>

* <span data-ttu-id="6427b-227">AaaS (autenticação como serviço)</span><span class="sxs-lookup"><span data-stu-id="6427b-227">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="6427b-228">Logon único/logoff (SSO) em vários tipos de aplicativos</span><span class="sxs-lookup"><span data-stu-id="6427b-228">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="6427b-229">Controle de acesso para APIs</span><span class="sxs-lookup"><span data-stu-id="6427b-229">Access control for APIs</span></span>
* <span data-ttu-id="6427b-230">Gateway de Federação</span><span class="sxs-lookup"><span data-stu-id="6427b-230">Federation Gateway</span></span>

<span data-ttu-id="6427b-231">Para obter mais informações, consulte [a documentação do IdentityServer4](http://docs.identityserver.io/en/latest/index.html) ou [autenticação e autorização para Spas](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="6427b-231">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="6427b-232">Autenticação de certificado e Kerberos</span><span class="sxs-lookup"><span data-stu-id="6427b-232">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="6427b-233">A autenticação de certificado requer:</span><span class="sxs-lookup"><span data-stu-id="6427b-233">Certificate authentication requires:</span></span>

* <span data-ttu-id="6427b-234">Configurando o servidor para aceitar certificados.</span><span class="sxs-lookup"><span data-stu-id="6427b-234">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="6427b-235">Adicionar o middleware de autenticação no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6427b-235">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="6427b-236">Adicionar o serviço de autenticação de certificado no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6427b-236">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="6427b-237">As opções de autenticação de certificado incluem a capacidade de:</span><span class="sxs-lookup"><span data-stu-id="6427b-237">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="6427b-238">Aceite certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="6427b-238">Accept self-signed certificates.</span></span>
* <span data-ttu-id="6427b-239">Verificar revogação de certificado.</span><span class="sxs-lookup"><span data-stu-id="6427b-239">Check for certificate revocation.</span></span>
* <span data-ttu-id="6427b-240">Verifique se o certificado proffered tem os sinalizadores de uso corretos.</span><span class="sxs-lookup"><span data-stu-id="6427b-240">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="6427b-241">Uma entidade de usuário padrão é construída a partir das propriedades do certificado.</span><span class="sxs-lookup"><span data-stu-id="6427b-241">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="6427b-242">A entidade de usuário contém um evento que permite complementar ou substituir a entidade de segurança.</span><span class="sxs-lookup"><span data-stu-id="6427b-242">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="6427b-243">Para obter mais informações, consulte <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="6427b-243">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="6427b-244">A [autenticação do Windows](/windows-server/security/windows-authentication/windows-authentication-overview) foi estendida para Linux e MacOS.</span><span class="sxs-lookup"><span data-stu-id="6427b-244">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="6427b-245">Em versões anteriores, a autenticação do Windows era limitada ao [IIS](xref:host-and-deploy/iis/index) e à [https](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="6427b-245">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="6427b-246">No ASP.NET Core 3,0, o [Kestrel](xref:fundamentals/servers/kestrel) tem a capacidade de usar Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)e [NTLM no Windows](/windows-server/security/kerberos/ntlm-overview), Linux e MacOS para hosts ingressados no domínio do Windows.</span><span class="sxs-lookup"><span data-stu-id="6427b-246">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="6427b-247">O suporte do Kestrel desses esquemas de autenticação é fornecido pelo pacote [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) .</span><span class="sxs-lookup"><span data-stu-id="6427b-247">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="6427b-248">Assim como acontece com os outros serviços de autenticação, configure o aplicativo de autenticação de todo e configure o serviço:</span><span class="sxs-lookup"><span data-stu-id="6427b-248">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="6427b-249">Requisitos do host:</span><span class="sxs-lookup"><span data-stu-id="6427b-249">Host requirements:</span></span>

* <span data-ttu-id="6427b-250">Os hosts do Windows devem ter SPNs ( [nomes da entidade de serviço](/windows/win32/ad/service-principal-names) ) adicionados à conta de usuário que hospeda o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6427b-250">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="6427b-251">Os computadores Linux e macOS devem ser ingressados no domínio.</span><span class="sxs-lookup"><span data-stu-id="6427b-251">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="6427b-252">Os SPNs devem ser criados para o processo da Web.</span><span class="sxs-lookup"><span data-stu-id="6427b-252">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="6427b-253">Os [arquivos keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) devem ser gerados e configurados no computador host.</span><span class="sxs-lookup"><span data-stu-id="6427b-253">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="6427b-254">Para obter mais informações, consulte <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="6427b-254">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="6427b-255">Alterações de modelo</span><span class="sxs-lookup"><span data-stu-id="6427b-255">Template changes</span></span>

<span data-ttu-id="6427b-256">Os modelos de interface do usuário da Web (Razor Pages, MVC com controlador e exibições) têm o seguinte removido:</span><span class="sxs-lookup"><span data-stu-id="6427b-256">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="6427b-257">A interface do usuário de consentimento do cookie não está mais incluída.</span><span class="sxs-lookup"><span data-stu-id="6427b-257">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="6427b-258">Para habilitar o recurso de consentimento de cookie em um ASP.NET Core aplicativo gerado pelo modelo 3,0, consulte <xref:security/gdpr>.</span><span class="sxs-lookup"><span data-stu-id="6427b-258">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="6427b-259">Os scripts e ativos estáticos relacionados agora são referenciados como arquivos locais em vez de usar CDNs.</span><span class="sxs-lookup"><span data-stu-id="6427b-259">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="6427b-260">Para obter mais informações, consulte [scripts e ativos estáticos relacionados agora são referenciados como arquivos locais em vez de usar o CDNs com base no ambiente atual (ASPNET/AspNetCore. Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="6427b-260">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="6427b-261">O modelo angular atualizado para usar o angular 8.</span><span class="sxs-lookup"><span data-stu-id="6427b-261">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="6427b-262">O modelo de biblioteca de classes Razor (RCL) usa como padrão o desenvolvimento de componentes Razor por padrão.</span><span class="sxs-lookup"><span data-stu-id="6427b-262">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="6427b-263">Uma nova opção de modelo no Visual Studio fornece suporte de modelo para páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="6427b-263">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="6427b-264">Ao criar um RCL a partir do modelo em um shell de comando, passe a opção `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="6427b-264">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="6427b-265">Host Genérico</span><span class="sxs-lookup"><span data-stu-id="6427b-265">Generic Host</span></span>

<span data-ttu-id="6427b-266">Os modelos ASP.NET Core 3,0 usam <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="6427b-266">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="6427b-267">As versões anteriores usaram <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6427b-267">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="6427b-268">Usar o host genérico do .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) fornece uma melhor integração de aplicativos ASP.NET Core com outros cenários de servidor que não são específicos da Web.</span><span class="sxs-lookup"><span data-stu-id="6427b-268">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="6427b-269">Para obter mais informações, consulte [HostBuilder substitui WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="6427b-269">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="6427b-270">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="6427b-270">Host configuration</span></span>

<span data-ttu-id="6427b-271">Antes do lançamento do ASP.NET Core 3,0, as variáveis de ambiente prefixadas com `ASPNETCORE_` foram carregadas para a configuração de host do host da Web.</span><span class="sxs-lookup"><span data-stu-id="6427b-271">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="6427b-272">Em 3,0, `AddEnvironmentVariables` é usado para carregar variáveis de ambiente prefixadas com `DOTNET_` para a configuração de host com `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="6427b-272">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="6427b-273">Alterações na injeção de construtor de inicialização</span><span class="sxs-lookup"><span data-stu-id="6427b-273">Changes to Startup constructor injection</span></span>

<span data-ttu-id="6427b-274">O host genérico só dá suporte aos tipos a seguir para `Startup` injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="6427b-274">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="6427b-275">Todos os serviços ainda podem ser injetados diretamente como argumentos para o método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6427b-275">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="6427b-276">Para obter mais informações, consulte [injeção de construtor de inicialização de restrict de host genérico (ASPNET/comunicados #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="6427b-276">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="6427b-277">Kestrel</span><span class="sxs-lookup"><span data-stu-id="6427b-277">Kestrel</span></span>

* <span data-ttu-id="6427b-278">A configuração do Kestrel foi atualizada para a migração para o host genérico.</span><span class="sxs-lookup"><span data-stu-id="6427b-278">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="6427b-279">No 3,0, o Kestrel é configurado no construtor de hosts da Web fornecido pelo `ConfigureWebHostDefaults`.</span><span class="sxs-lookup"><span data-stu-id="6427b-279">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="6427b-280">Os adaptadores de conexão foram removidos do Kestrel e substituídos pelo middleware de conexão, que é semelhante ao middleware HTTP no pipeline de ASP.NET Core, mas para conexões de nível inferior.</span><span class="sxs-lookup"><span data-stu-id="6427b-280">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="6427b-281">A camada de transporte Kestrel foi exposta como uma interface pública no `Connections.Abstractions`.</span><span class="sxs-lookup"><span data-stu-id="6427b-281">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="6427b-282">A ambiguidade entre cabeçalhos e trailers foi resolvida movendo os cabeçalhos à direita para uma nova coleção.</span><span class="sxs-lookup"><span data-stu-id="6427b-282">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="6427b-283">As APIs de e/s síncronas, como `HttpRequest.Body.Read`, são uma fonte comum de privação de thread que leva a falhas de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6427b-283">Synchronous IO APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="6427b-284">Em 3,0, `AllowSynchronousIO` é desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="6427b-284">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="6427b-285">Para obter mais informações, consulte <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="6427b-285">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="6427b-286">HTTP/2 habilitado por padrão</span><span class="sxs-lookup"><span data-stu-id="6427b-286">HTTP/2 enabled by default</span></span>

<span data-ttu-id="6427b-287">O HTTP/2 é habilitado por padrão em Kestrel para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6427b-287">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="6427b-288">O suporte a HTTP/2 para IIS ou HTTP. sys é habilitado quando há suporte para o sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="6427b-288">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="6427b-289">EventCounters na solicitação</span><span class="sxs-lookup"><span data-stu-id="6427b-289">EventCounters on request</span></span>

<span data-ttu-id="6427b-290">A hospedagem EventSource, `Microsoft.AspNetCore.Hosting`, emite os seguintes novos tipos de <xref:System.Diagnostics.Tracing.EventCounter> relacionados a solicitações de entrada:</span><span class="sxs-lookup"><span data-stu-id="6427b-290">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="6427b-291">Roteamento de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="6427b-291">Endpoint routing</span></span>

<span data-ttu-id="6427b-292">O roteamento de ponto de extremidade, que permite que as estruturas (por exemplo, MVC) funcionem bem com o middleware, seja aprimorado:</span><span class="sxs-lookup"><span data-stu-id="6427b-292">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="6427b-293">A ordem do middleware e dos pontos de extremidade é configurável no pipeline de processamento de solicitação de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6427b-293">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="6427b-294">Os pontos de extremidade e o middleware compõem-se bem com outras tecnologias baseadas em ASP.NET Core, como verificações de integridade.</span><span class="sxs-lookup"><span data-stu-id="6427b-294">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="6427b-295">Os pontos de extremidade podem implementar uma política, como CORS ou autorização, tanto no middleware quanto no MVC.</span><span class="sxs-lookup"><span data-stu-id="6427b-295">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="6427b-296">Filtros e atributos podem ser colocados em métodos em controladores.</span><span class="sxs-lookup"><span data-stu-id="6427b-296">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="6427b-297">Para obter mais informações, consulte <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="6427b-297">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="6427b-298">Verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="6427b-298">Health Checks</span></span>

<span data-ttu-id="6427b-299">As verificações de integridade usam o roteamento de ponto de extremidade com o host genérico.</span><span class="sxs-lookup"><span data-stu-id="6427b-299">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="6427b-300">No `Startup.Configure`, chame `MapHealthChecks` no construtor de ponto de extremidade com a URL do ponto de extremidade ou o caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="6427b-300">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="6427b-301">Os pontos de extremidade de verificações de integridade podem:</span><span class="sxs-lookup"><span data-stu-id="6427b-301">Health Checks endpoints can:</span></span>

* <span data-ttu-id="6427b-302">Especifique um ou mais hosts/portas permitidos.</span><span class="sxs-lookup"><span data-stu-id="6427b-302">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="6427b-303">Exigir autorização.</span><span class="sxs-lookup"><span data-stu-id="6427b-303">Require authorization.</span></span>
* <span data-ttu-id="6427b-304">Exigir CORS.</span><span class="sxs-lookup"><span data-stu-id="6427b-304">Require CORS.</span></span>

<span data-ttu-id="6427b-305">Para obter mais informações, confira os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="6427b-305">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="6427b-306">Pipes em HttpContext</span><span class="sxs-lookup"><span data-stu-id="6427b-306">Pipes on HttpContext</span></span>

<span data-ttu-id="6427b-307">Agora é possível ler o corpo da solicitação e gravar o corpo da resposta usando a API <xref:System.IO.Pipelines>.</span><span class="sxs-lookup"><span data-stu-id="6427b-307">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="6427b-308">O</span><span class="sxs-lookup"><span data-stu-id="6427b-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="6427b-309">`HttpRequest.BodyReader` propriedade fornece um <xref:System.IO.Pipelines.PipeReader> que pode ser usado para ler o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="6427b-309">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="6427b-310">O</span><span class="sxs-lookup"><span data-stu-id="6427b-310">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="6427b-311">`HttpResponse.BodyWriter` propriedade fornece um <xref:System.IO.Pipelines.PipeWriter> que pode ser usado para gravar o corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="6427b-311">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="6427b-312">`HttpRequest.BodyReader` é um análogo do fluxo de `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="6427b-312">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="6427b-313">`HttpResponse.BodyWriter` é um análogo do fluxo de `HttpResponse.Body`.</span><span class="sxs-lookup"><span data-stu-id="6427b-313">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="6427b-314">Relatório de erros aprimorado no IIS</span><span class="sxs-lookup"><span data-stu-id="6427b-314">Improved error reporting in IIS</span></span>

<span data-ttu-id="6427b-315">Os erros de inicialização ao hospedar aplicativos ASP.NET Core no IIS agora produzem dados de diagnóstico mais avançados.</span><span class="sxs-lookup"><span data-stu-id="6427b-315">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="6427b-316">Esses erros são relatados para o log de eventos do Windows com rastreamentos de pilha onde for aplicável.</span><span class="sxs-lookup"><span data-stu-id="6427b-316">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="6427b-317">Além disso, todos os avisos, erros e exceções sem tratamento são registrados no log de eventos do Windows.</span><span class="sxs-lookup"><span data-stu-id="6427b-317">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="6427b-318">Serviço de trabalho e SDK de trabalho</span><span class="sxs-lookup"><span data-stu-id="6427b-318">Worker Service and Worker SDK</span></span>

<span data-ttu-id="6427b-319">O .NET Core 3,0 apresenta o novo modelo de aplicativo do serviço de trabalho.</span><span class="sxs-lookup"><span data-stu-id="6427b-319">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="6427b-320">Este modelo fornece um ponto de partida para escrever serviços de longa execução no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6427b-320">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="6427b-321">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="6427b-321">For more information, see:</span></span>

* [<span data-ttu-id="6427b-322">Trabalhos do .NET Core como serviços do Windows</span><span class="sxs-lookup"><span data-stu-id="6427b-322">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="6427b-323">Melhorias de middleware de cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="6427b-323">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="6427b-324">Nas versões anteriores do ASP.NET Core, a chamada de <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> e <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> eram problemáticas quando implantadas em um Linux do Azure ou por trás de qualquer proxy reverso que não seja o IIS.</span><span class="sxs-lookup"><span data-stu-id="6427b-324">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="6427b-325">A correção para versões anteriores está documentada em [encaminhar o esquema para proxies inversos do Linux e não IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="6427b-325">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="6427b-326">Esse cenário é corrigido no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="6427b-326">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="6427b-327">O host habilita o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) quando a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="6427b-327">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="6427b-328">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` é definido como `true` em nossas imagens de contêiner.</span><span class="sxs-lookup"><span data-stu-id="6427b-328">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="6427b-329">Melhorias de desempenho</span><span class="sxs-lookup"><span data-stu-id="6427b-329">Performance improvements</span></span>

<span data-ttu-id="6427b-330">O ASP.NET Core 3,0 inclui muitas melhorias que reduzem o uso de memória e melhoram a taxa de transferência:</span><span class="sxs-lookup"><span data-stu-id="6427b-330">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="6427b-331">Redução no uso de memória ao usar o contêiner de injeção de dependência interna para serviços com escopo.</span><span class="sxs-lookup"><span data-stu-id="6427b-331">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="6427b-332">Redução nas alocações em toda a estrutura, incluindo cenários de middleware e roteamento.</span><span class="sxs-lookup"><span data-stu-id="6427b-332">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="6427b-333">Redução no uso de memória para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6427b-333">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="6427b-334">Melhorias de taxa de transferência e redução de memória para conexões HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6427b-334">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="6427b-335">Novo serializador JSON otimizado e totalmente assíncrono.</span><span class="sxs-lookup"><span data-stu-id="6427b-335">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="6427b-336">Redução no uso de memória e melhorias de taxa de transferência na análise de formulário.</span><span class="sxs-lookup"><span data-stu-id="6427b-336">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="6427b-337">O ASP.NET Core 3,0 só é executado no .NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="6427b-337">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="6427b-338">A partir de ASP.NET Core 3,0, .NET Framework não é mais uma estrutura de destino com suporte.</span><span class="sxs-lookup"><span data-stu-id="6427b-338">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="6427b-339">Os projetos destinados a .NET Framework podem continuar de uma maneira totalmente compatível usando a [versão LTS do .NET Core 2,1](https://www.microsoft.com/net/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="6427b-339">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://www.microsoft.com/net/download/dotnet-core/2.1).</span></span> <span data-ttu-id="6427b-340">A maioria dos pacotes relacionados ao ASP.NET Core 2.1. x terá suporte indefinidamente, além do período de LTS de três anos para o .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="6427b-340">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="6427b-341">Para obter informações de migração, consulte [portar seu código do .NET Framework para o .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="6427b-341">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="6427b-342">Usar a estrutura compartilhada ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6427b-342">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="6427b-343">A estrutura compartilhada ASP.NET Core 3,0, contida no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), não requer mais um elemento `<PackageReference />` explícito no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="6427b-343">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="6427b-344">A estrutura compartilhada é automaticamente referenciada ao usar o SDK do `Microsoft.NET.Sdk.Web` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="6427b-344">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="6427b-345">Assemblies removidos da estrutura compartilhada do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6427b-345">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="6427b-346">Os assemblies mais notáveis removidos da estrutura compartilhada ASP.NET Core 3,0 são:</span><span class="sxs-lookup"><span data-stu-id="6427b-346">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="6427b-347">[Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net).</span><span class="sxs-lookup"><span data-stu-id="6427b-347">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="6427b-348">Para adicionar o Json.NET ao ASP.NET Core 3,0, consulte [Adicionar suporte ao formato JSON baseado em Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="6427b-348">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="6427b-349">ASP.NET Core 3,0 apresenta `System.Text.Json` para leitura e gravação de JSON.</span><span class="sxs-lookup"><span data-stu-id="6427b-349">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="6427b-350">Para obter mais informações, consulte [nova SERIALIZAÇÃO JSON](#new-json-serialization) neste documento.</span><span class="sxs-lookup"><span data-stu-id="6427b-350">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="6427b-351">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6427b-351">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="6427b-352">Para obter uma lista completa de assemblies removidos da estrutura compartilhada, consulte [assemblies sendo removidos do Microsoft. AspNetCore. App 3,0](https://github.com/aspnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="6427b-352">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/aspnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="6427b-353">Para obter mais informações sobre a motivação para essa alteração, consulte [alterações recentes em Microsoft. AspNetCore. app em 3,0](https://github.com/aspnet/Announcements/issues/325) e [uma primeira análise sobre as alterações que chegam no ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="6427b-353">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
