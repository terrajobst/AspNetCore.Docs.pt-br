---
title: Tratar erros em aplicativos ASP.NET Core Blazor
author: guardrex
description: Descubra como ASP.NET Core Blazor como o Blazor gerencia exceções sem tratamento e como desenvolver aplicativos que detectam e manipulam erros.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 7191ae50d64ebd6a9b23b391116aedf3a6d01de2
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447016"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="b3dba-103">Tratar erros em aplicativos ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b3dba-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="b3dba-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="b3dba-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="b3dba-105">Este artigo descreve como o Blazor gerencia exceções sem tratamento e como desenvolver aplicativos que detectam e manipulam erros.</span><span class="sxs-lookup"><span data-stu-id="b3dba-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="b3dba-106">Erros detalhados durante o desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="b3dba-106">Detailed errors during development</span></span>

<span data-ttu-id="b3dba-107">Quando um aplicativo Blazor não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema.</span><span class="sxs-lookup"><span data-stu-id="b3dba-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="b3dba-108">Quando ocorre um erro, Blazor aplicativos exibem uma barra de ouro na parte inferior da tela:</span><span class="sxs-lookup"><span data-stu-id="b3dba-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="b3dba-109">Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.</span><span class="sxs-lookup"><span data-stu-id="b3dba-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="b3dba-110">Em produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.</span><span class="sxs-lookup"><span data-stu-id="b3dba-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="b3dba-111">A interface do usuário para essa experiência de tratamento de erros faz parte dos modelos de projeto Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="b3dba-112">Em um aplicativo Webassembly Blazor, personalize a experiência no arquivo *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="b3dba-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="b3dba-113">Em um aplicativo do Blazor Server, personalize a experiência no arquivo *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b3dba-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="b3dba-114">O elemento `blazor-error-ui` é ocultado pelos estilos incluídos com os modelos de Blazor e, em seguida, mostrados quando ocorre um erro.</span><span class="sxs-lookup"><span data-stu-id="b3dba-114">The `blazor-error-ui` element is hidden by the styles included with the Blazor templates and then shown when an error occurs.</span></span>

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="b3dba-115">Como um aplicativo de servidor de Blazor reage a exceções sem tratamento</span><span class="sxs-lookup"><span data-stu-id="b3dba-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="b3dba-116"> Server é uma estrutura com estado.</span><span class="sxs-lookup"><span data-stu-id="b3dba-116"> Server is a stateful framework.</span></span> <span data-ttu-id="b3dba-117">Enquanto os usuários interagem com um aplicativo, eles mantêm uma conexão com o servidor conhecido como um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="b3dba-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="b3dba-118">O circuito contém instâncias de componentes ativos, além de muitos outros aspectos do estado, como:</span><span class="sxs-lookup"><span data-stu-id="b3dba-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="b3dba-119">A saída renderizada mais recente dos componentes.</span><span class="sxs-lookup"><span data-stu-id="b3dba-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="b3dba-120">O conjunto atual de delegados de manipulação de eventos que poderia ser disparado por eventos do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="b3dba-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="b3dba-121">Se um usuário abrir o aplicativo em várias guias do navegador, ele terá vários circuitos independentes.</span><span class="sxs-lookup"><span data-stu-id="b3dba-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="b3dba-122"> trata a maioria das exceções sem tratamento como fatal para o circuito onde elas ocorrem.</span><span class="sxs-lookup"><span data-stu-id="b3dba-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="b3dba-123">Se um circuito for encerrado devido a uma exceção sem tratamento, o usuário só poderá continuar a interagir com o aplicativo recarregando a página para criar um novo circuito.</span><span class="sxs-lookup"><span data-stu-id="b3dba-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="b3dba-124">Os circuitos fora do que foi encerrado, que são circuitos para outros usuários ou outras guias do navegador, não são afetados.</span><span class="sxs-lookup"><span data-stu-id="b3dba-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="b3dba-125">Esse cenário é semelhante a um aplicativo de área de trabalho que falha&mdash;o aplicativo com falha deve ser reiniciado, mas outros aplicativos não são afetados.</span><span class="sxs-lookup"><span data-stu-id="b3dba-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="b3dba-126">Um circuito é encerrado quando ocorre uma exceção sem tratamento pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="b3dba-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="b3dba-127">Uma exceção sem tratamento geralmente deixa o circuito em um estado indefinido.</span><span class="sxs-lookup"><span data-stu-id="b3dba-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="b3dba-128">A operação normal do aplicativo não pode ser garantida após uma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="b3dba-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="b3dba-129">As vulnerabilidades de segurança podem aparecer no aplicativo se o circuito continuar.</span><span class="sxs-lookup"><span data-stu-id="b3dba-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="b3dba-130">Gerenciar exceções sem tratamento no código do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="b3dba-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="b3dba-131">Para que um aplicativo continue após um erro, o aplicativo deve ter a lógica de tratamento de erros.</span><span class="sxs-lookup"><span data-stu-id="b3dba-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="b3dba-132">As seções posteriores deste artigo descrevem possíveis fontes de exceções sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="b3dba-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="b3dba-133">Em produção, não processe mensagens de exceção de estrutura ou rastreamentos de pilha na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b3dba-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="b3dba-134">O processamento de mensagens de exceção ou rastreamentos de pilha pode:</span><span class="sxs-lookup"><span data-stu-id="b3dba-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="b3dba-135">Divulgue informações confidenciais para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="b3dba-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="b3dba-136">Ajudar um usuário mal-intencionado a descobrir pontos fracos em um aplicativo que pode comprometer a segurança do aplicativo, do servidor ou da rede.</span><span class="sxs-lookup"><span data-stu-id="b3dba-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="b3dba-137">Registrar erros em log com um provedor persistente</span><span class="sxs-lookup"><span data-stu-id="b3dba-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="b3dba-138">Se ocorrer uma exceção sem tratamento, a exceção será registrada em <xref:Microsoft.Extensions.Logging.ILogger> instâncias configuradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="b3dba-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="b3dba-139">Por padrão, Blazor aplicativos registram em log a saída do console com o provedor de log do console.</span><span class="sxs-lookup"><span data-stu-id="b3dba-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="b3dba-140">Considere fazer logon em um local mais permanente com um provedor que gerencia o tamanho do log e a rotação do log.</span><span class="sxs-lookup"><span data-stu-id="b3dba-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="b3dba-141">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="b3dba-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="b3dba-142">Durante o desenvolvimento, Blazor geralmente envia os detalhes completos das exceções para o console do navegador para auxiliar na depuração.</span><span class="sxs-lookup"><span data-stu-id="b3dba-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="b3dba-143">Na produção, os erros detalhados no console do navegador são desabilitados por padrão, o que significa que os erros não são enviados aos clientes, mas os detalhes completos da exceção ainda são registrados no lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="b3dba-144">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="b3dba-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="b3dba-145">Você deve decidir quais incidentes registrar e o nível de gravidade dos incidentes registrados.</span><span class="sxs-lookup"><span data-stu-id="b3dba-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="b3dba-146">Usuários hostis podem ser capazes de disparar erros deliberadamente.</span><span class="sxs-lookup"><span data-stu-id="b3dba-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="b3dba-147">Por exemplo, não registre um incidente de um erro em que um `ProductId` desconhecido é fornecido na URL de um componente que exibe detalhes do produto.</span><span class="sxs-lookup"><span data-stu-id="b3dba-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="b3dba-148">Nem todos os erros devem ser tratados como incidentes de alta severidade para registro em log.</span><span class="sxs-lookup"><span data-stu-id="b3dba-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="b3dba-149">Locais onde podem ocorrer erros</span><span class="sxs-lookup"><span data-stu-id="b3dba-149">Places where errors may occur</span></span>

<span data-ttu-id="b3dba-150">O Framework e o código do aplicativo podem disparar exceções sem tratamento em qualquer um dos seguintes locais:</span><span class="sxs-lookup"><span data-stu-id="b3dba-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="b3dba-151">Instanciação de componente</span><span class="sxs-lookup"><span data-stu-id="b3dba-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="b3dba-152">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="b3dba-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="b3dba-153">Lógica de renderização</span><span class="sxs-lookup"><span data-stu-id="b3dba-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="b3dba-154">Manipuladores de eventos</span><span class="sxs-lookup"><span data-stu-id="b3dba-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="b3dba-155">Disposição do componente</span><span class="sxs-lookup"><span data-stu-id="b3dba-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="b3dba-156">Interoperabilidade de JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3dba-156">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="b3dba-157">[manipuladores de circuito de servidor Blazor](#blazor-server-circuit-handlers)</span><span class="sxs-lookup"><span data-stu-id="b3dba-157">[Blazor Server circuit handlers](#blazor-server-circuit-handlers)</span></span>
* <span data-ttu-id="b3dba-158">[Descarte de circuitos de servidor Blazor](#blazor-server-circuit-disposal)</span><span class="sxs-lookup"><span data-stu-id="b3dba-158">[Blazor Server circuit disposal](#blazor-server-circuit-disposal)</span></span>
* <span data-ttu-id="b3dba-159">[rerenderização do Blazor Server](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="b3dba-159">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="b3dba-160">As exceções sem tratamento anteriores são descritas nas seções a seguir deste artigo.</span><span class="sxs-lookup"><span data-stu-id="b3dba-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="b3dba-161">Instanciação de componente</span><span class="sxs-lookup"><span data-stu-id="b3dba-161">Component instantiation</span></span>

<span data-ttu-id="b3dba-162">Quando Blazor cria uma instância de um componente:</span><span class="sxs-lookup"><span data-stu-id="b3dba-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="b3dba-163">O construtor do componente é invocado.</span><span class="sxs-lookup"><span data-stu-id="b3dba-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="b3dba-164">Os construtores de quaisquer serviços não singleton de DI fornecidos ao construtor do componente por meio da diretiva [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) ou do atributo [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) são invocados.</span><span class="sxs-lookup"><span data-stu-id="b3dba-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="b3dba-165">Um circuito de servidor Blazor falha quando qualquer Construtor executado ou um setter para qualquer propriedade `[Inject]` gera uma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="b3dba-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="b3dba-166">A exceção é fatal porque a estrutura não pode instanciar o componente.</span><span class="sxs-lookup"><span data-stu-id="b3dba-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="b3dba-167">Se a lógica do Construtor puder gerar exceções, o aplicativo deverá interceptar as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.</span><span class="sxs-lookup"><span data-stu-id="b3dba-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="b3dba-168">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="b3dba-168">Lifecycle methods</span></span>

<span data-ttu-id="b3dba-169">Durante o tempo de vida de um componente, Blazor invoca os seguintes [métodos de ciclo de vida](xref:blazor/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="b3dba-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="b3dba-170">Se qualquer método de ciclo de vida lançar uma exceção, de forma síncrona ou assíncrona, a exceção será fatal para um circuito de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="b3dba-171">Para componentes que lidam com erros em métodos de ciclo de vida, adicione a lógica de tratamento de erros.</span><span class="sxs-lookup"><span data-stu-id="b3dba-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="b3dba-172">No exemplo a seguir, onde `OnParametersSetAsync` chama um método para obter um produto:</span><span class="sxs-lookup"><span data-stu-id="b3dba-172">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="b3dba-173">Uma exceção gerada no método `ProductRepository.GetProductByIdAsync` é manipulada por uma instrução `try-catch`.</span><span class="sxs-lookup"><span data-stu-id="b3dba-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="b3dba-174">Quando o bloco de `catch` é executado:</span><span class="sxs-lookup"><span data-stu-id="b3dba-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="b3dba-175">`loadFailed` é definido como `true`, que é usado para exibir uma mensagem de erro para o usuário.</span><span class="sxs-lookup"><span data-stu-id="b3dba-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="b3dba-176">O erro é registrado.</span><span class="sxs-lookup"><span data-stu-id="b3dba-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="b3dba-177">Lógica de renderização</span><span class="sxs-lookup"><span data-stu-id="b3dba-177">Rendering logic</span></span>

<span data-ttu-id="b3dba-178">A marcação declarativa em um arquivo de componente `.razor` é compilada C# em um método chamado `BuildRenderTree`.</span><span class="sxs-lookup"><span data-stu-id="b3dba-178">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="b3dba-179">Quando um componente é renderizado, `BuildRenderTree` executa e cria uma estrutura de dados que descreve os elementos, o texto e os componentes filho do componente renderizado.</span><span class="sxs-lookup"><span data-stu-id="b3dba-179">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="b3dba-180">A lógica de renderização pode gerar uma exceção.</span><span class="sxs-lookup"><span data-stu-id="b3dba-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="b3dba-181">Um exemplo desse cenário ocorre quando `@someObject.PropertyName` é avaliada, mas `@someObject` é `null`.</span><span class="sxs-lookup"><span data-stu-id="b3dba-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="b3dba-182">Uma exceção sem tratamento gerada pela lógica de renderização é fatal para um circuito de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="b3dba-183">Para evitar uma exceção de referência nula na lógica de renderização, verifique se há um objeto `null` antes de acessar seus membros.</span><span class="sxs-lookup"><span data-stu-id="b3dba-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="b3dba-184">No exemplo a seguir, `person.Address` Propriedades não serão acessadas se `person.Address` for `null`:</span><span class="sxs-lookup"><span data-stu-id="b3dba-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="b3dba-185">O código anterior pressupõe que `person` não é `null`.</span><span class="sxs-lookup"><span data-stu-id="b3dba-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="b3dba-186">Geralmente, a estrutura do código garante que um objeto exista no momento em que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="b3dba-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="b3dba-187">Nesses casos, não é necessário verificar `null` na lógica de renderização.</span><span class="sxs-lookup"><span data-stu-id="b3dba-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="b3dba-188">No exemplo anterior, talvez seja garantido que `person` existam porque `person` é criado quando o componente é instanciado.</span><span class="sxs-lookup"><span data-stu-id="b3dba-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="b3dba-189">Manipuladores de eventos</span><span class="sxs-lookup"><span data-stu-id="b3dba-189">Event handlers</span></span>

<span data-ttu-id="b3dba-190">O código do lado do cliente dispara invocações C# de código quando manipuladores de eventos são criados usando:</span><span class="sxs-lookup"><span data-stu-id="b3dba-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="b3dba-191">Outros atributos de `@on...`</span><span class="sxs-lookup"><span data-stu-id="b3dba-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="b3dba-192">O código do manipulador de eventos pode gerar uma exceção sem tratamento nesses cenários.</span><span class="sxs-lookup"><span data-stu-id="b3dba-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="b3dba-193">Se um manipulador de eventos lançar uma exceção sem tratamento (por exemplo, uma consulta de banco de dados falhar), a exceção será fatal para um circuito de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="b3dba-194">Se o aplicativo chamar o código que poderia falhar por motivos externos, intercepte as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.</span><span class="sxs-lookup"><span data-stu-id="b3dba-194">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="b3dba-195">Se o código do usuário não interceptar e manipular a exceção, a estrutura registrará a exceção e encerrará o circuito.</span><span class="sxs-lookup"><span data-stu-id="b3dba-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="b3dba-196">Disposição do componente</span><span class="sxs-lookup"><span data-stu-id="b3dba-196">Component disposal</span></span>

<span data-ttu-id="b3dba-197">Um componente pode ser removido da interface do usuário, por exemplo, porque o usuário navegou para outra página.</span><span class="sxs-lookup"><span data-stu-id="b3dba-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="b3dba-198">Quando um componente que implementa <xref:System.IDisposable?displayProperty=fullName> é removido da interface do usuário, a estrutura chama o método <xref:System.IDisposable.Dispose*> do componente.</span><span class="sxs-lookup"><span data-stu-id="b3dba-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="b3dba-199">Se o método de `Dispose` do componente lançar uma exceção sem tratamento, a exceção será fatal para um circuito de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="b3dba-200">Se a lógica de descarte puder gerar exceções, o aplicativo deverá interceptar as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com o tratamento de erros e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="b3dba-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="b3dba-201">Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="b3dba-201">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="b3dba-202">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3dba-202">JavaScript interop</span></span>

<span data-ttu-id="b3dba-203">`IJSRuntime.InvokeAsync<T>` permite que o código .NET faça chamadas assíncronas para o tempo de execução do JavaScript no navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="b3dba-203">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="b3dba-204">As seguintes condições se aplicam ao tratamento de erros com `InvokeAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="b3dba-204">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="b3dba-205">Se uma chamada para `InvokeAsync<T>` falhar de forma síncrona, ocorrerá uma exceção do .NET.</span><span class="sxs-lookup"><span data-stu-id="b3dba-205">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="b3dba-206">Uma chamada para `InvokeAsync<T>` pode falhar, por exemplo, porque os argumentos fornecidos não podem ser serializados.</span><span class="sxs-lookup"><span data-stu-id="b3dba-206">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="b3dba-207">O código do desenvolvedor deve capturar a exceção.</span><span class="sxs-lookup"><span data-stu-id="b3dba-207">Developer code must catch the exception.</span></span> <span data-ttu-id="b3dba-208">Se o código do aplicativo em um manipulador de eventos ou método de ciclo de vida do componente não tratar uma exceção, a exceção resultante será fatal para um circuito de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="b3dba-209">Se uma chamada para `InvokeAsync<T>` falhar de forma assíncrona, o <xref:System.Threading.Tasks.Task> .NET falhará.</span><span class="sxs-lookup"><span data-stu-id="b3dba-209">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="b3dba-210">Uma chamada para `InvokeAsync<T>` pode falhar, por exemplo, porque o código do lado do JavaScript gera uma exceção ou retorna uma `Promise` concluída como `rejected`.</span><span class="sxs-lookup"><span data-stu-id="b3dba-210">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="b3dba-211">O código do desenvolvedor deve capturar a exceção.</span><span class="sxs-lookup"><span data-stu-id="b3dba-211">Developer code must catch the exception.</span></span> <span data-ttu-id="b3dba-212">Se estiver usando o operador [Await](/dotnet/csharp/language-reference/keywords/await) , considere encapsular a chamada de método em uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.</span><span class="sxs-lookup"><span data-stu-id="b3dba-212">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="b3dba-213">Caso contrário, o código com falha resultará em uma exceção sem tratamento que é fatal para um circuito de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="b3dba-214">Por padrão, as chamadas para `InvokeAsync<T>` devem ser concluídas em um determinado período ou, caso contrário, a chamada expirará. O período de tempo limite padrão é de um minuto.</span><span class="sxs-lookup"><span data-stu-id="b3dba-214">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="b3dba-215">O tempo limite protege o código contra uma perda na conectividade de rede ou no código JavaScript que nunca envia uma mensagem de conclusão.</span><span class="sxs-lookup"><span data-stu-id="b3dba-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="b3dba-216">Se a chamada atingir o tempo limite, a `Task` resultante falhará com um <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="b3dba-216">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="b3dba-217">Interceptar e processar a exceção com registro em log.</span><span class="sxs-lookup"><span data-stu-id="b3dba-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="b3dba-218">Da mesma forma, o código JavaScript pode iniciar chamadas para métodos .NET indicados pelo atributo [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) .</span><span class="sxs-lookup"><span data-stu-id="b3dba-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) attribute.</span></span> <span data-ttu-id="b3dba-219">Se esses métodos .NET lançarem uma exceção sem tratamento:</span><span class="sxs-lookup"><span data-stu-id="b3dba-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="b3dba-220">A exceção não é tratada como fatal para um circuito de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="b3dba-221">O `Promise` do lado do JavaScript é rejeitado.</span><span class="sxs-lookup"><span data-stu-id="b3dba-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="b3dba-222">Você tem a opção de usar o código de tratamento de erros no lado do .NET ou no lado do JavaScript da chamada do método.</span><span class="sxs-lookup"><span data-stu-id="b3dba-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="b3dba-223">Para obter mais informações, consulte <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="b3dba-223">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="opno-locblazor-server-circuit-handlers"></a><span data-ttu-id="b3dba-224">manipuladores de circuito de servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="b3dba-224">Blazor Server circuit handlers</span></span>

Blazor<span data-ttu-id="b3dba-225"> Server permite que o código defina um *manipulador de circuito*, que permite a execução de código em alterações no estado do circuito de um usuário.</span><span class="sxs-lookup"><span data-stu-id="b3dba-225"> Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="b3dba-226">Um manipulador de circuito é implementado derivando de `CircuitHandler` e registrando a classe no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b3dba-226">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="b3dba-227">O exemplo a seguir de um manipulador de circuito rastreia conexões SignalR abertas:</span><span class="sxs-lookup"><span data-stu-id="b3dba-227">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

<span data-ttu-id="b3dba-228">Os manipuladores de circuito são registrados usando DI.</span><span class="sxs-lookup"><span data-stu-id="b3dba-228">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="b3dba-229">Instâncias com escopo são criadas por instância de um circuito.</span><span class="sxs-lookup"><span data-stu-id="b3dba-229">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="b3dba-230">Usando o `TrackingCircuitHandler` no exemplo anterior, um serviço singleton é criado porque o estado de todos os circuitos deve ser acompanhado:</span><span class="sxs-lookup"><span data-stu-id="b3dba-230">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="b3dba-231">Se os métodos de um manipulador de circuitos personalizados lançarem uma exceção sem tratamento, a exceção será fatal para o circuito de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-231">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="b3dba-232">Para tolerar exceções no código de um manipulador ou em métodos chamados, empacote o código em uma ou mais instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com o tratamento de erros e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="b3dba-232">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="opno-locblazor-server-circuit-disposal"></a><span data-ttu-id="b3dba-233">Descarte de circuitos de servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="b3dba-233">Blazor Server circuit disposal</span></span>

<span data-ttu-id="b3dba-234">Quando um circuito termina porque um usuário se desconectou e a estrutura está limpando o estado do circuito, a estrutura descarta o escopo de DI do circuito.</span><span class="sxs-lookup"><span data-stu-id="b3dba-234">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="b3dba-235">Descartar o escopo descarta todos os serviços de DI no escopo do circuito que implementam <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="b3dba-235">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="b3dba-236">Se qualquer serviço de DI lançar uma exceção sem tratamento durante a alienação, a estrutura registrará a exceção.</span><span class="sxs-lookup"><span data-stu-id="b3dba-236">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="opno-locblazor-server-prerendering"></a><span data-ttu-id="b3dba-237">pré-processamento do Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b3dba-237">Blazor Server prerendering</span></span>

Blazor<span data-ttu-id="b3dba-238"> componentes podem ser renderizados usando o auxiliar de marca de `Component` para que sua marcação HTML renderizada seja retornada como parte da solicitação HTTP inicial do usuário.</span><span class="sxs-lookup"><span data-stu-id="b3dba-238"> components can be prerendered using the `Component` Tag Helper so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="b3dba-239">Isso funciona por:</span><span class="sxs-lookup"><span data-stu-id="b3dba-239">This works by:</span></span>

* <span data-ttu-id="b3dba-240">Criar um novo circuito para todos os componentes renderizados que fazem parte da mesma página.</span><span class="sxs-lookup"><span data-stu-id="b3dba-240">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="b3dba-241">Gerando o HTML inicial.</span><span class="sxs-lookup"><span data-stu-id="b3dba-241">Generating the initial HTML.</span></span>
* <span data-ttu-id="b3dba-242">Tratar o circuito como `disconnected` até que o navegador do usuário estabeleça uma conexão de SignalR de volta para o mesmo servidor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-242">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="b3dba-243">Quando a conexão é estabelecida, a interatividade no circuito é retomada e a marcação HTML dos componentes é atualizada.</span><span class="sxs-lookup"><span data-stu-id="b3dba-243">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="b3dba-244">Se qualquer componente lançar uma exceção sem tratamento durante o pré-processamento, por exemplo, durante um método de ciclo de vida ou na lógica de renderização:</span><span class="sxs-lookup"><span data-stu-id="b3dba-244">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="b3dba-245">A exceção é fatal para o circuito.</span><span class="sxs-lookup"><span data-stu-id="b3dba-245">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="b3dba-246">A exceção é gerada na pilha de chamadas a partir do auxiliar de marca de `Component`.</span><span class="sxs-lookup"><span data-stu-id="b3dba-246">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="b3dba-247">Portanto, toda a solicitação HTTP falha, a menos que a exceção seja explicitamente detectada pelo código do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-247">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="b3dba-248">Em circunstâncias normais, quando o pré-processamento falha, continuar a criar e renderizar o componente não faz sentido porque um componente de trabalho não pode ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="b3dba-248">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="b3dba-249">Para tolerar erros que podem ocorrer durante o pré-processamento, a lógica de tratamento de erro deve ser colocada dentro de um componente que pode gerar exceções.</span><span class="sxs-lookup"><span data-stu-id="b3dba-249">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="b3dba-250">Use instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.</span><span class="sxs-lookup"><span data-stu-id="b3dba-250">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="b3dba-251">Em vez de encapsular o auxiliar de marca de `Component` em uma instrução `try-catch`, coloque a lógica de tratamento de erros no componente renderizado pelo auxiliar de marca de `Component`.</span><span class="sxs-lookup"><span data-stu-id="b3dba-251">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="b3dba-252">Cenários avançados</span><span class="sxs-lookup"><span data-stu-id="b3dba-252">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="b3dba-253">Renderização recursiva</span><span class="sxs-lookup"><span data-stu-id="b3dba-253">Recursive rendering</span></span>

<span data-ttu-id="b3dba-254">Os componentes podem ser aninhados recursivamente.</span><span class="sxs-lookup"><span data-stu-id="b3dba-254">Components can be nested recursively.</span></span> <span data-ttu-id="b3dba-255">Isso é útil para representar estruturas de dados recursivas.</span><span class="sxs-lookup"><span data-stu-id="b3dba-255">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="b3dba-256">Por exemplo, um componente `TreeNode` pode processar mais componentes de `TreeNode` para cada um dos filhos do nó.</span><span class="sxs-lookup"><span data-stu-id="b3dba-256">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="b3dba-257">Ao renderizar recursivamente, evite codificar padrões que resultam em recursão infinita:</span><span class="sxs-lookup"><span data-stu-id="b3dba-257">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="b3dba-258">Não renderizar recursivamente uma estrutura de dados que contém um ciclo.</span><span class="sxs-lookup"><span data-stu-id="b3dba-258">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="b3dba-259">Por exemplo, não renderize um nó de árvore cujos filhos incluam a si mesmo.</span><span class="sxs-lookup"><span data-stu-id="b3dba-259">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="b3dba-260">Não crie uma cadeia de layouts que contenham um ciclo.</span><span class="sxs-lookup"><span data-stu-id="b3dba-260">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="b3dba-261">Por exemplo, não crie um layout cujo layout seja próprio.</span><span class="sxs-lookup"><span data-stu-id="b3dba-261">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="b3dba-262">Não permita que um usuário final viole invariáveis de recursão (regras) por meio de entradas de dados mal-intencionados ou chamadas de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3dba-262">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="b3dba-263">Loops infinitos durante a renderização:</span><span class="sxs-lookup"><span data-stu-id="b3dba-263">Infinite loops during rendering:</span></span>

* <span data-ttu-id="b3dba-264">Faz com que o processo de renderização continue para sempre.</span><span class="sxs-lookup"><span data-stu-id="b3dba-264">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="b3dba-265">É equivalente a criar um loop não finalizado.</span><span class="sxs-lookup"><span data-stu-id="b3dba-265">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="b3dba-266">Nesses cenários, um circuito de servidor Blazor afetado falha e o thread geralmente tenta:</span><span class="sxs-lookup"><span data-stu-id="b3dba-266">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="b3dba-267">Consuma tanto tempo de CPU quanto permitido pelo sistema operacional, indefinidamente.</span><span class="sxs-lookup"><span data-stu-id="b3dba-267">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="b3dba-268">Consuma uma quantidade ilimitada de memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="b3dba-268">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="b3dba-269">O consumo de memória ilimitada é equivalente ao cenário em que um loop não finalizado adiciona entradas a uma coleção em cada iteração.</span><span class="sxs-lookup"><span data-stu-id="b3dba-269">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="b3dba-270">Para evitar padrões de recursão infinitos, verifique se o código de renderização recursivo contém condições de parada adequadas.</span><span class="sxs-lookup"><span data-stu-id="b3dba-270">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="b3dba-271">Lógica de árvore de renderização personalizada</span><span class="sxs-lookup"><span data-stu-id="b3dba-271">Custom render tree logic</span></span>

<span data-ttu-id="b3dba-272">A maioria dos componentes Blazor são implementados como arquivos *. Razor* e são compilados para produzir uma lógica que opere em uma `RenderTreeBuilder` para processar sua saída.</span><span class="sxs-lookup"><span data-stu-id="b3dba-272">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="b3dba-273">Um desenvolvedor pode implementar manualmente `RenderTreeBuilder` lógica usando código C# de procedimento.</span><span class="sxs-lookup"><span data-stu-id="b3dba-273">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="b3dba-274">Para obter mais informações, consulte <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="b3dba-274">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="b3dba-275">O uso da lógica do construtor de árvore de renderização manual é considerado um cenário avançado e não seguro, não recomendado para o desenvolvimento geral de componentes.</span><span class="sxs-lookup"><span data-stu-id="b3dba-275">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="b3dba-276">Se `RenderTreeBuilder` código for escrito, o desenvolvedor deverá garantir a exatidão do código.</span><span class="sxs-lookup"><span data-stu-id="b3dba-276">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="b3dba-277">Por exemplo, o desenvolvedor deve garantir que:</span><span class="sxs-lookup"><span data-stu-id="b3dba-277">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="b3dba-278">As chamadas para `OpenElement` e `CloseElement` são balanceadas corretamente.</span><span class="sxs-lookup"><span data-stu-id="b3dba-278">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="b3dba-279">Os atributos são adicionados somente nos locais corretos.</span><span class="sxs-lookup"><span data-stu-id="b3dba-279">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="b3dba-280">A lógica do construtor de árvore de renderização manual incorreta pode causar um comportamento indefinido arbitrário, incluindo falhas, travamentos do servidor e vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="b3dba-280">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="b3dba-281">Considere a lógica do construtor de árvore de renderização manual no mesmo nível de complexidade e com o mesmo nível de *perigo* de escrever código de assembly ou instruções MSIL manualmente.</span><span class="sxs-lookup"><span data-stu-id="b3dba-281">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
