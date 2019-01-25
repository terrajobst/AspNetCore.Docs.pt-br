---
uid: signalr/overview/security/hub-authorization
title: Autenticação e autorização para Hubs do SignalR | Microsoft Docs
author: bradygaster
description: Este tópico descreve como restringir quais usuários ou funções podem acessar os métodos de hub. Versões de software usados neste tópico ve .NET 4.5 SignalR do Visual Studio 2013...
ms.author: bradyg
ms.date: 01/05/2015
ms.assetid: a610c796-c131-473c-baef-2e6c568cb2a2
msc.legacyurl: /signalr/overview/security/hub-authorization
msc.type: authoredcontent
ms.openlocfilehash: bfea212283165facc046e5355571c1e6d9c7cd7d
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837410"
---
<a name="authentication-and-authorization-for-signalr-hubs"></a><span data-ttu-id="f1bf3-104">Autenticação e autorização para Hubs do SignalR</span><span class="sxs-lookup"><span data-stu-id="f1bf3-104">Authentication and Authorization for SignalR Hubs</span></span>
====================
<span data-ttu-id="f1bf3-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="f1bf3-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="f1bf3-106">Este tópico descreve como restringir quais usuários ou funções podem acessar os métodos de hub.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-106">This topic describes how to restrict which users or roles can access hub methods.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="f1bf3-107">Versões de software usadas neste tópico</span><span class="sxs-lookup"><span data-stu-id="f1bf3-107">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="f1bf3-108">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="f1bf3-108">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="f1bf3-109">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="f1bf3-109">.NET 4.5</span></span>
> - <span data-ttu-id="f1bf3-110">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="f1bf3-110">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="f1bf3-111">Versões anteriores deste tópico</span><span class="sxs-lookup"><span data-stu-id="f1bf3-111">Previous versions of this topic</span></span>
>
> <span data-ttu-id="f1bf3-112">Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="f1bf3-112">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="f1bf3-113">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="f1bf3-113">Questions and comments</span></span>
>
> <span data-ttu-id="f1bf3-114">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-114">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="f1bf3-115">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="f1bf3-115">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="f1bf3-116">Visão geral</span><span class="sxs-lookup"><span data-stu-id="f1bf3-116">Overview</span></span>

<span data-ttu-id="f1bf3-117">Esse tópico contém as seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="f1bf3-117">This topic contains the following sections:</span></span>

- [<span data-ttu-id="f1bf3-118">Autorizar atributo</span><span class="sxs-lookup"><span data-stu-id="f1bf3-118">Authorize attribute</span></span>](#authorizeattribute)
- [<span data-ttu-id="f1bf3-119">Exigir autenticação para todos os hubs</span><span class="sxs-lookup"><span data-stu-id="f1bf3-119">Require authentication for all hubs</span></span>](#requireauth)
- [<span data-ttu-id="f1bf3-120">Autorização personalizada</span><span class="sxs-lookup"><span data-stu-id="f1bf3-120">Customized authorization</span></span>](#custom)
- [<span data-ttu-id="f1bf3-121">Passar informações de autenticação para clientes</span><span class="sxs-lookup"><span data-stu-id="f1bf3-121">Pass authentication information to clients</span></span>](#passauth)
- [<span data-ttu-id="f1bf3-122">Opções de autenticação para clientes .NET</span><span class="sxs-lookup"><span data-stu-id="f1bf3-122">Authentication options for .NET clients</span></span>](#authoptions)

    - [<span data-ttu-id="f1bf3-123">Cookie de autenticação de formulários</span><span class="sxs-lookup"><span data-stu-id="f1bf3-123">Cookie with Forms Authentication</span></span>](#cookie)
    - [<span data-ttu-id="f1bf3-124">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="f1bf3-124">Windows Authentication</span></span>](#windows)
    - [<span data-ttu-id="f1bf3-125">Cabeçalho de Conexão</span><span class="sxs-lookup"><span data-stu-id="f1bf3-125">Connection header</span></span>](#header)
    - [<span data-ttu-id="f1bf3-126">Certificado</span><span class="sxs-lookup"><span data-stu-id="f1bf3-126">Certificate</span></span>](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a><span data-ttu-id="f1bf3-127">Autorizar atributo</span><span class="sxs-lookup"><span data-stu-id="f1bf3-127">Authorize attribute</span></span>

<span data-ttu-id="f1bf3-128">O SignalR fornece o [autorizar](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) atributo para especificar quais usuários ou funções têm acesso a um hub ou método.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-128">SignalR provides the [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribute to specify which users or roles have access to a hub or method.</span></span> <span data-ttu-id="f1bf3-129">Esse atributo está localizado no `Microsoft.AspNet.SignalR` namespace.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-129">This attribute is located in the `Microsoft.AspNet.SignalR` namespace.</span></span> <span data-ttu-id="f1bf3-130">Aplicar o `Authorize` de atributo para um hub ou determinados métodos em um hub.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-130">You apply the `Authorize` attribute to either a hub or particular methods in a hub.</span></span> <span data-ttu-id="f1bf3-131">Quando você aplica o `Authorize` atributo a uma classe hub, o requisito de autorização especificado é aplicado a todos os métodos no hub.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-131">When you apply the `Authorize` attribute to a hub class, the specified authorization requirement is applied to all of the methods in the hub.</span></span> <span data-ttu-id="f1bf3-132">Este tópico fornece exemplos de diferentes tipos de requisitos de autorização que você pode aplicar.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-132">This topic provides examples of the different types of authorization requirements that you can apply.</span></span> <span data-ttu-id="f1bf3-133">Sem o `Authorize` de atributo, um conectado cliente pode acessar qualquer método público no hub.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-133">Without the `Authorize` attribute, a connected client can access any public method on the hub.</span></span>

<span data-ttu-id="f1bf3-134">Se você tiver definido uma função chamada "Admin" em seu aplicativo web, você pode especificar que somente os usuários nessa função podem acessar um hub com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-134">If you have defined a role named "Admin" in your web application, you could specify that only users in that role can access a hub with the following code.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

<span data-ttu-id="f1bf3-135">Ou, você pode especificar que um hub contém um método que está disponível para todos os usuários e um segundo método que está disponível somente para usuários autenticados, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-135">Or, you can specify that a hub contains one method that is available to all users, and a second method that is only available to authenticated users, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

<span data-ttu-id="f1bf3-136">Os exemplos a seguir abordam os cenários de autorização diferentes:</span><span class="sxs-lookup"><span data-stu-id="f1bf3-136">The following examples address different authorization scenarios:</span></span>

- <span data-ttu-id="f1bf3-137">`[Authorize]` – somente usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="f1bf3-137">`[Authorize]` – only authenticated users</span></span>
- <span data-ttu-id="f1bf3-138">`[Authorize(Roles = "Admin,Manager")]` – somente os usuários em funções especificadas autenticados</span><span class="sxs-lookup"><span data-stu-id="f1bf3-138">`[Authorize(Roles = "Admin,Manager")]` – only authenticated users in the specified roles</span></span>
- <span data-ttu-id="f1bf3-139">`[Authorize(Users = "user1,user2")]` – somente usuários com os nomes de usuário especificado autenticados</span><span class="sxs-lookup"><span data-stu-id="f1bf3-139">`[Authorize(Users = "user1,user2")]` – only authenticated users with the specified user names</span></span>
- <span data-ttu-id="f1bf3-140">`[Authorize(RequireOutgoing=false)]` – somente usuários autenticados podem invocar o hub, mas o marshaling de chamadas do servidor para os clientes não são limitadas por autorização, como, quando apenas alguns usuários podem enviar uma mensagem, mas todos os outros podem receber a mensagem.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-140">`[Authorize(RequireOutgoing=false)]` – only authenticated users can invoke the hub, but calls from the server back to clients are not limited by authorization, such as, when only certain users can send a message but all others can receive the message.</span></span> <span data-ttu-id="f1bf3-141">A propriedade RequireOutgoing só pode ser aplicada para o hub inteiro, não em métodos de indivíduos dentro do hub de.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-141">The RequireOutgoing property can only be applied to the entire hub, not on individuals methods within the hub.</span></span> <span data-ttu-id="f1bf3-142">Quando RequireOutgoing não for definido como false, somente os usuários que atendem ao requisito de autorização são chamados do servidor.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-142">When RequireOutgoing is not set to false, only users that meet the authorization requirement are called from the server.</span></span>

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a><span data-ttu-id="f1bf3-143">Exigir autenticação para todos os hubs</span><span class="sxs-lookup"><span data-stu-id="f1bf3-143">Require authentication for all hubs</span></span>

<span data-ttu-id="f1bf3-144">Você pode exigir autenticação para todos os hubs e métodos de hub em seu aplicativo chamando o [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) método quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-144">You can require authentication for all hubs and hub methods in your application by calling the [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) method when the application starts.</span></span> <span data-ttu-id="f1bf3-145">Você pode usar esse método quando você tem vários hubs e deseja impor um requisito de autenticação para todos eles.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-145">You might use this method when you have multiple hubs and want to enforce an authentication requirement for all of them.</span></span> <span data-ttu-id="f1bf3-146">Com esse método, você não pode especificar os requisitos para a saída de autorização, usuário ou função.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-146">With this method, you cannot specify requirements for role, user, or outgoing authorization.</span></span> <span data-ttu-id="f1bf3-147">Você só pode especificar que o acesso aos métodos de hub é restrito a usuários autenticados.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-147">You can only specify that access to the hub methods is restricted to authenticated users.</span></span> <span data-ttu-id="f1bf3-148">No entanto, você ainda poderá aplicar o atributo Authorize a hubs ou métodos para especificar os requisitos adicionais.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-148">However, you can still apply the Authorize attribute to hubs or methods to specify additional requirements.</span></span> <span data-ttu-id="f1bf3-149">Necessidade de que especificar um atributo é adicionada ao requisito de autenticação básico.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-149">Any requirement you specify in an attribute is added to the basic requirement of authentication.</span></span>

<span data-ttu-id="f1bf3-150">O exemplo a seguir mostra um arquivo de inicialização que restringe todos os métodos de hub para usuários autenticados.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-150">The following example shows a Startup file which restricts all hub methods to authenticated users.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

<span data-ttu-id="f1bf3-151">Se você chamar o `RequireAuthentication()` método após o processamento de uma solicitação de SignalR, SignalR lançará um `InvalidOperationException` exceção.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-151">If you call the `RequireAuthentication()` method after a SignalR request has been processed, SignalR will throw a `InvalidOperationException` exception.</span></span> <span data-ttu-id="f1bf3-152">O SignalR gera esta exceção, porque você não pode adicionar um módulo ao HubPipeline depois que o pipeline foi invocado.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-152">SignalR throws this exception because you cannot add a module to the HubPipeline after the pipeline has been invoked.</span></span> <span data-ttu-id="f1bf3-153">O exemplo anterior mostra uma chamada a `RequireAuthentication` método no `Configuration` método que é executado uma vez antes de lidar com a primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-153">The previous example shows calling the `RequireAuthentication` method in the `Configuration` method which is executed one time prior to handling the first request.</span></span>

<a id="custom"></a>

## <a name="customized-authorization"></a><span data-ttu-id="f1bf3-154">Autorização personalizada</span><span class="sxs-lookup"><span data-stu-id="f1bf3-154">Customized authorization</span></span>

<span data-ttu-id="f1bf3-155">Se você precisar personalizar como a autorização é determinada, você pode criar uma classe que deriva de `AuthorizeAttribute` e substitua o [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) método.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-155">If you need to customize how authorization is determined, you can create a class that derives from `AuthorizeAttribute` and override the [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) method.</span></span> <span data-ttu-id="f1bf3-156">Para cada solicitação, o SignalR invoca esse método para determinar se o usuário está autorizado a concluir a solicitação.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-156">For each request, SignalR invokes this method to determine whether the user is authorized to complete the request.</span></span> <span data-ttu-id="f1bf3-157">No método substituído, você pode fornecer a lógica necessária para seu cenário de autorização.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-157">In the overridden method, you provide the necessary logic for your authorization scenario.</span></span> <span data-ttu-id="f1bf3-158">O exemplo a seguir mostra como implantar a autorização por meio de identidade baseada em declarações.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-158">The following example shows how to enforce authorization through claims-based identity.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a><span data-ttu-id="f1bf3-159">Passar informações de autenticação para clientes</span><span class="sxs-lookup"><span data-stu-id="f1bf3-159">Pass authentication information to clients</span></span>

<span data-ttu-id="f1bf3-160">Talvez você precise usar informações de autenticação no código que é executado no cliente.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-160">You may need to use authentication information in the code that runs on the client.</span></span> <span data-ttu-id="f1bf3-161">Você passa as informações necessárias ao chamar os métodos no cliente.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-161">You pass the required information when calling the methods on the client.</span></span> <span data-ttu-id="f1bf3-162">Por exemplo, um método de aplicativo de bate-papo poderia passar como um parâmetro de nome de usuário da pessoa que está postando uma mensagem, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-162">For example, a chat application method could pass as a parameter the user name of the person posting a message, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

<span data-ttu-id="f1bf3-163">Ou, você pode criar um objeto para representar as informações de autenticação e passa o objeto como um parâmetro, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-163">Or, you can create an object to represent the authentication information and pass that object as a parameter, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

<span data-ttu-id="f1bf3-164">Você nunca deve passar a id de conexão de um cliente a outros clientes, como um usuário mal-intencionado poderia usá-lo para simular uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-164">You should never pass one client's connection id to other clients, as a malicious user could use it to mimic a request from that client.</span></span>

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a><span data-ttu-id="f1bf3-165">Opções de autenticação para clientes .NET</span><span class="sxs-lookup"><span data-stu-id="f1bf3-165">Authentication options for .NET clients</span></span>

<span data-ttu-id="f1bf3-166">Quando você tem um cliente do .NET, como um aplicativo de console, que interage com um hub é limitado a usuários autenticados, você pode passar as credenciais de autenticação em um cookie, o cabeçalho de conexão ou um certificado.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-166">When you have a .NET client, such as a console app, which interacts with a hub that is limited to authenticated users, you can pass the authentication credentials in a cookie, the connection header, or a certificate.</span></span> <span data-ttu-id="f1bf3-167">Os exemplos nesta seção mostram como usar os métodos diferentes para autenticar um usuário.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-167">The examples in this section show how to use those different methods for authenticating a user.</span></span> <span data-ttu-id="f1bf3-168">Eles não são totalmente funcional aplicativos do SignalR.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-168">They are not fully-functional SignalR apps.</span></span> <span data-ttu-id="f1bf3-169">Para obter mais informações sobre os clientes do .NET com o SignalR, consulte [guia da API Hubs – cliente .NET](../guide-to-the-api/hubs-api-guide-net-client.md).</span><span class="sxs-lookup"><span data-stu-id="f1bf3-169">For more information about .NET clients with SignalR, see [Hubs API Guide - .NET Client](../guide-to-the-api/hubs-api-guide-net-client.md).</span></span>

<a id="cookie"></a>

### <a name="cookie"></a><span data-ttu-id="f1bf3-170">Cookie</span><span class="sxs-lookup"><span data-stu-id="f1bf3-170">Cookie</span></span>

<span data-ttu-id="f1bf3-171">Quando o cliente .NET interage com um hub que usa a autenticação de formulários do ASP.NET, você precisará definir manualmente o cookie de autenticação sobre a conexão.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-171">When your .NET client interacts with a hub that uses ASP.NET Forms Authentication, you will need to manually set the authentication cookie on the connection.</span></span> <span data-ttu-id="f1bf3-172">Você adiciona o cookie para o `CookieContainer` propriedade sobre a [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) objeto.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-172">You add the cookie to the `CookieContainer` property on the [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) object.</span></span> <span data-ttu-id="f1bf3-173">O exemplo a seguir mostra um aplicativo de console que recupera um cookie de autenticação de uma página da web e adiciona esse cookie para a conexão.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-173">The following example shows a console app that retrieves an authentication cookie from a web page and adds that cookie to the connection.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

<span data-ttu-id="f1bf3-174">O aplicativo de console envia as credenciais a serem <strong>www.contoso.com/RemoteLogin</strong> que pode se referir a uma página vazia que contém o seguinte arquivo de code-behind.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-174">The console app posts the credentials to <strong>www.contoso.com/RemoteLogin</strong> which could refer to an empty page that contains the following code-behind file.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a><span data-ttu-id="f1bf3-175">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="f1bf3-175">Windows authentication</span></span>

<span data-ttu-id="f1bf3-176">Ao usar a autenticação do Windows, você pode passar credenciais do usuário atual usando o [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) propriedade.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-176">When using Windows authentication, you can pass the current user's credentials by using the [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) property.</span></span> <span data-ttu-id="f1bf3-177">Você pode definir as credenciais para a conexão com o valor da DefaultCredentials.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-177">You set the credentials for the connection to the value of the DefaultCredentials.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a><span data-ttu-id="f1bf3-178">Cabeçalho de Conexão</span><span class="sxs-lookup"><span data-stu-id="f1bf3-178">Connection header</span></span>

<span data-ttu-id="f1bf3-179">Se seu aplicativo não estiver usando cookies, você pode passar informações de usuário no cabeçalho da conexão.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-179">If your application is not using cookies, you can pass user information in the connection header.</span></span> <span data-ttu-id="f1bf3-180">Por exemplo, você pode passar um token no cabeçalho da conexão.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-180">For example, you can pass a token in the connection header.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

<span data-ttu-id="f1bf3-181">Em seguida, no hub, você poderia verificar o token do usuário.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-181">Then, in the hub, you would verify the user's token.</span></span>

<a id="certificate"></a>

### <a name="certificate"></a><span data-ttu-id="f1bf3-182">Certificado</span><span class="sxs-lookup"><span data-stu-id="f1bf3-182">Certificate</span></span>

<span data-ttu-id="f1bf3-183">Você pode passar um certificado de cliente para verificar se o usuário.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-183">You can pass a client certificate to verify the user.</span></span> <span data-ttu-id="f1bf3-184">Você adiciona o certificado ao criar a conexão.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-184">You add the certificate when creating the connection.</span></span> <span data-ttu-id="f1bf3-185">O exemplo a seguir mostra apenas como adicionar um certificado de cliente para a conexão; ele não mostra o aplicativo de console completo.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-185">The following example shows only how to add a client certificate to the connection; it does not show the full console app.</span></span> <span data-ttu-id="f1bf3-186">Ele usa o [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) classe que fornece várias maneiras diferentes para criar o certificado.</span><span class="sxs-lookup"><span data-stu-id="f1bf3-186">It uses the [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) class which provides several different ways to create the certificate.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]
