---
uid: signalr/overview/older-versions/hub-authorization
title: Autenticação e autorização para Hubs do SignalR (SignalR 1.x) | Microsoft Docs
author: pfletcher
description: Este tópico descreve como restringir quais usuários ou funções podem acessar os métodos de hub.
ms.author: riande
ms.date: 10/17/2013
ms.assetid: 3d2dfc0e-eac2-4076-a468-325d3d01cc7b
msc.legacyurl: /signalr/overview/older-versions/hub-authorization
msc.type: authoredcontent
ms.openlocfilehash: 1bd61b8faff22db3337e9d9c1ed2532d3e64440d
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287462"
---
<a name="authentication-and-authorization-for-signalr-hubs-signalr-1x"></a><span data-ttu-id="cfffe-103">Autenticação e autorização para Hubs do SignalR (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="cfffe-103">Authentication and Authorization for SignalR Hubs (SignalR 1.x)</span></span>
====================
<span data-ttu-id="cfffe-104">por [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="cfffe-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="cfffe-105">Este tópico descreve como restringir quais usuários ou funções podem acessar os métodos de hub.</span><span class="sxs-lookup"><span data-stu-id="cfffe-105">This topic describes how to restrict which users or roles can access hub methods.</span></span>


## <a name="overview"></a><span data-ttu-id="cfffe-106">Visão geral</span><span class="sxs-lookup"><span data-stu-id="cfffe-106">Overview</span></span>

<span data-ttu-id="cfffe-107">Esse tópico contém as seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="cfffe-107">This topic contains the following sections:</span></span>

- [<span data-ttu-id="cfffe-108">Autorizar atributo</span><span class="sxs-lookup"><span data-stu-id="cfffe-108">Authorize attribute</span></span>](#authorizeattribute)
- [<span data-ttu-id="cfffe-109">Exigir autenticação para todos os hubs</span><span class="sxs-lookup"><span data-stu-id="cfffe-109">Require authentication for all hubs</span></span>](#requireauth)
- [<span data-ttu-id="cfffe-110">Autorização personalizada</span><span class="sxs-lookup"><span data-stu-id="cfffe-110">Customized authorization</span></span>](#custom)
- [<span data-ttu-id="cfffe-111">Passar informações de autenticação para clientes</span><span class="sxs-lookup"><span data-stu-id="cfffe-111">Pass authentication information to clients</span></span>](#passauth)
- [<span data-ttu-id="cfffe-112">Opções de autenticação para clientes .NET</span><span class="sxs-lookup"><span data-stu-id="cfffe-112">Authentication options for .NET clients</span></span>](#authoptions)

    - [<span data-ttu-id="cfffe-113">Cookie de autenticação de formulários</span><span class="sxs-lookup"><span data-stu-id="cfffe-113">Cookie with Forms Authentication</span></span>](#cookie)
    - [<span data-ttu-id="cfffe-114">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="cfffe-114">Windows Authentication</span></span>](#windows)
    - [<span data-ttu-id="cfffe-115">Cabeçalho de Conexão</span><span class="sxs-lookup"><span data-stu-id="cfffe-115">Connection header</span></span>](#header)
    - [<span data-ttu-id="cfffe-116">Certificado</span><span class="sxs-lookup"><span data-stu-id="cfffe-116">Certificate</span></span>](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a><span data-ttu-id="cfffe-117">Autorizar atributo</span><span class="sxs-lookup"><span data-stu-id="cfffe-117">Authorize attribute</span></span>

<span data-ttu-id="cfffe-118">O SignalR fornece o [autorizar](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) atributo para especificar quais usuários ou funções têm acesso a um hub ou método.</span><span class="sxs-lookup"><span data-stu-id="cfffe-118">SignalR provides the [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribute to specify which users or roles have access to a hub or method.</span></span> <span data-ttu-id="cfffe-119">Esse atributo está localizado no `Microsoft.AspNet.SignalR` namespace.</span><span class="sxs-lookup"><span data-stu-id="cfffe-119">This attribute is located in the `Microsoft.AspNet.SignalR` namespace.</span></span> <span data-ttu-id="cfffe-120">Aplicar o `Authorize` de atributo para um hub ou determinados métodos em um hub.</span><span class="sxs-lookup"><span data-stu-id="cfffe-120">You apply the `Authorize` attribute to either a hub or particular methods in a hub.</span></span> <span data-ttu-id="cfffe-121">Quando você aplica o `Authorize` atributo a uma classe hub, o requisito de autorização especificado é aplicado a todos os métodos no hub.</span><span class="sxs-lookup"><span data-stu-id="cfffe-121">When you apply the `Authorize` attribute to a hub class, the specified authorization requirement is applied to all of the methods in the hub.</span></span> <span data-ttu-id="cfffe-122">Os diferentes tipos de requisitos de autorização que você pode aplicar são mostrados abaixo.</span><span class="sxs-lookup"><span data-stu-id="cfffe-122">The different types of authorization requirements that you can apply are shown below.</span></span> <span data-ttu-id="cfffe-123">Sem o `Authorize` atributo, todos os métodos públicos no hub estão disponíveis para um cliente que está conectado ao hub.</span><span class="sxs-lookup"><span data-stu-id="cfffe-123">Without the `Authorize` attribute, all public methods on the hub are available to a client that is connected to the hub.</span></span>

<span data-ttu-id="cfffe-124">Se você tiver definido uma função chamada "Admin" em seu aplicativo web, você pode especificar que somente os usuários nessa função podem acessar um hub com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="cfffe-124">If you have defined a role named "Admin" in your web application, you could specify that only users in that role can access a hub with the following code.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

<span data-ttu-id="cfffe-125">Ou, você pode especificar que um hub contém um método que está disponível para todos os usuários e um segundo método que está disponível somente para usuários autenticados, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="cfffe-125">Or, you can specify that a hub contains one method that is available to all users, and a second method that is only available to authenticated users, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

<span data-ttu-id="cfffe-126">Os exemplos a seguir abordam os cenários de autorização diferentes:</span><span class="sxs-lookup"><span data-stu-id="cfffe-126">The following examples address different authorization scenarios:</span></span>

- <span data-ttu-id="cfffe-127">`[Authorize]` – somente usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="cfffe-127">`[Authorize]` – only authenticated users</span></span>
- <span data-ttu-id="cfffe-128">`[Authorize(Roles = "Admin,Manager")]` – somente os usuários em funções especificadas autenticados</span><span class="sxs-lookup"><span data-stu-id="cfffe-128">`[Authorize(Roles = "Admin,Manager")]` – only authenticated users in the specified roles</span></span>
- <span data-ttu-id="cfffe-129">`[Authorize(Users = "user1,user2")]` – somente usuários com os nomes de usuário especificado autenticados</span><span class="sxs-lookup"><span data-stu-id="cfffe-129">`[Authorize(Users = "user1,user2")]` – only authenticated users with the specified user names</span></span>
- <span data-ttu-id="cfffe-130">`[Authorize(RequireOutgoing=false)]` – somente usuários autenticados podem invocar o hub, mas o marshaling de chamadas do servidor para os clientes não são limitadas por autorização, como, quando apenas alguns usuários podem enviar uma mensagem, mas todos os outros podem receber a mensagem.</span><span class="sxs-lookup"><span data-stu-id="cfffe-130">`[Authorize(RequireOutgoing=false)]` – only authenticated users can invoke the hub, but calls from the server back to clients are not limited by authorization, such as, when only certain users can send a message but all others can receive the message.</span></span> <span data-ttu-id="cfffe-131">A propriedade RequireOutgoing só pode ser aplicada para o hub inteiro, não em métodos de indivíduos dentro do hub de.</span><span class="sxs-lookup"><span data-stu-id="cfffe-131">The RequireOutgoing property can only be applied to the entire hub, not on individuals methods within the hub.</span></span> <span data-ttu-id="cfffe-132">Quando RequireOutgoing não for definido como false, somente os usuários que atendem ao requisito de autorização são chamados do servidor.</span><span class="sxs-lookup"><span data-stu-id="cfffe-132">When RequireOutgoing is not set to false, only users that meet the authorization requirement are called from the server.</span></span>

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a><span data-ttu-id="cfffe-133">Exigir autenticação para todos os hubs</span><span class="sxs-lookup"><span data-stu-id="cfffe-133">Require authentication for all hubs</span></span>

<span data-ttu-id="cfffe-134">Você pode exigir autenticação para todos os hubs e métodos de hub em seu aplicativo chamando o [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) método quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="cfffe-134">You can require authentication for all hubs and hub methods in your application by calling the [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) method when the application starts.</span></span> <span data-ttu-id="cfffe-135">Você pode usar esse método quando você tem vários hubs e deseja impor um requisito de autenticação para todos eles.</span><span class="sxs-lookup"><span data-stu-id="cfffe-135">You might use this method when you have multiple hubs and want to enforce an authentication requirement for all of them.</span></span> <span data-ttu-id="cfffe-136">Com esse método, você não pode especificar a saída de autorização, usuário ou função.</span><span class="sxs-lookup"><span data-stu-id="cfffe-136">With this method, you cannot specify role, user, or outgoing authorization.</span></span> <span data-ttu-id="cfffe-137">Você só pode especificar que o acesso aos métodos de hub é restrito a usuários autenticados.</span><span class="sxs-lookup"><span data-stu-id="cfffe-137">You can only specify that access to the hub methods is restricted to authenticated users.</span></span> <span data-ttu-id="cfffe-138">No entanto, você ainda poderá aplicar o atributo Authorize a hubs ou métodos para especificar os requisitos adicionais.</span><span class="sxs-lookup"><span data-stu-id="cfffe-138">However, you can still apply the Authorize attribute to hubs or methods to specify additional requirements.</span></span> <span data-ttu-id="cfffe-139">Necessidade de que especificar em atributos é aplicada, além do requisito básico de autenticação.</span><span class="sxs-lookup"><span data-stu-id="cfffe-139">Any requirement you specify in attributes is applied in addition to the basic requirement of authentication.</span></span>

<span data-ttu-id="cfffe-140">O exemplo a seguir mostra um arquivo global asax que restringe todos os métodos de hub para usuários autenticados.</span><span class="sxs-lookup"><span data-stu-id="cfffe-140">The following example shows a Global.asax file which restricts all hub methods to authenticated users.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

<span data-ttu-id="cfffe-141">Se você chamar o `RequireAuthentication()` método após o processamento de uma solicitação de SignalR, SignalR lançará um `InvalidOperationException` exceção.</span><span class="sxs-lookup"><span data-stu-id="cfffe-141">If you call the `RequireAuthentication()` method after a SignalR request has been processed, SignalR will throw a `InvalidOperationException` exception.</span></span> <span data-ttu-id="cfffe-142">Essa exceção é gerada porque você não pode adicionar um módulo ao HubPipeline depois que o pipeline foi invocado.</span><span class="sxs-lookup"><span data-stu-id="cfffe-142">This exception is thrown because you cannot add a module to the HubPipeline after the pipeline has been invoked.</span></span> <span data-ttu-id="cfffe-143">O exemplo anterior mostra uma chamada a `RequireAuthentication` método no `Application_Start` método que é executado uma vez antes de lidar com a primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="cfffe-143">The previous example shows calling the `RequireAuthentication` method in the `Application_Start` method which is executed one time prior to handling the first request.</span></span>

<a id="custom"></a>

## <a name="customized-authorization"></a><span data-ttu-id="cfffe-144">Autorização personalizada</span><span class="sxs-lookup"><span data-stu-id="cfffe-144">Customized authorization</span></span>

<span data-ttu-id="cfffe-145">Se você precisar personalizar como a autorização é determinada, você pode criar uma classe que deriva de `AuthorizeAttribute` e substitua o [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) método.</span><span class="sxs-lookup"><span data-stu-id="cfffe-145">If you need to customize how authorization is determined, you can create a class that derives from `AuthorizeAttribute` and override the [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) method.</span></span> <span data-ttu-id="cfffe-146">Esse método é chamado para cada solicitação para determinar se o usuário está autorizado a concluir a solicitação.</span><span class="sxs-lookup"><span data-stu-id="cfffe-146">This method is called for each request to determine whether the user is authorized to complete the request.</span></span> <span data-ttu-id="cfffe-147">No método substituído, você pode fornecer a lógica necessária para seu cenário de autorização.</span><span class="sxs-lookup"><span data-stu-id="cfffe-147">In the overridden method, you provide the necessary logic for your authorization scenario.</span></span> <span data-ttu-id="cfffe-148">O exemplo a seguir mostra como implantar a autorização por meio de identidade baseada em declarações.</span><span class="sxs-lookup"><span data-stu-id="cfffe-148">The following example shows how to enforce authorization through claims-based identity.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a><span data-ttu-id="cfffe-149">Passar informações de autenticação para clientes</span><span class="sxs-lookup"><span data-stu-id="cfffe-149">Pass authentication information to clients</span></span>

<span data-ttu-id="cfffe-150">Talvez você precise usar informações de autenticação no código que é executado no cliente.</span><span class="sxs-lookup"><span data-stu-id="cfffe-150">You may need to use authentication information in the code that runs on the client.</span></span> <span data-ttu-id="cfffe-151">Você passa as informações necessárias ao chamar os métodos no cliente.</span><span class="sxs-lookup"><span data-stu-id="cfffe-151">You pass the required information when calling the methods on the client.</span></span> <span data-ttu-id="cfffe-152">Por exemplo, um método de aplicativo de bate-papo poderia passar como um parâmetro de nome de usuário da pessoa que está postando uma mensagem, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="cfffe-152">For example, a chat application method could pass as a parameter the user name of the person posting a message, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

<span data-ttu-id="cfffe-153">Ou, você pode criar um objeto para representar as informações de autenticação e passa o objeto como um parâmetro, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="cfffe-153">Or, you can create an object to represent the authentication information and pass that object as a parameter, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

<span data-ttu-id="cfffe-154">Você nunca deve passar a id de conexão de um cliente a outros clientes, como um usuário mal-intencionado poderia usá-lo para simular uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="cfffe-154">You should never pass one client's connection id to other clients, as a malicious user could use it to mimic a request from that client.</span></span>

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a><span data-ttu-id="cfffe-155">Opções de autenticação para clientes .NET</span><span class="sxs-lookup"><span data-stu-id="cfffe-155">Authentication options for .NET clients</span></span>

<span data-ttu-id="cfffe-156">Quando você tem um cliente do .NET, como um aplicativo de console, que interage com um hub é limitado a usuários autenticados, você pode passar as credenciais de autenticação em um cookie, o cabeçalho de conexão ou um certificado.</span><span class="sxs-lookup"><span data-stu-id="cfffe-156">When you have a .NET client, such as a console app, which interacts with a hub that is limited to authenticated users, you can pass the authentication credentials in a cookie, the connection header, or a certificate.</span></span> <span data-ttu-id="cfffe-157">Os exemplos nesta seção mostram como usar os métodos diferentes para autenticar um usuário.</span><span class="sxs-lookup"><span data-stu-id="cfffe-157">The examples in this section show how to use those different methods for authenticating a user.</span></span> <span data-ttu-id="cfffe-158">Eles não são totalmente funcional aplicativos do SignalR.</span><span class="sxs-lookup"><span data-stu-id="cfffe-158">They are not fully-functional SignalR apps.</span></span> <span data-ttu-id="cfffe-159">Para obter mais informações sobre os clientes do .NET com o SignalR, consulte [guia da API Hubs – cliente .NET](../guide-to-the-api/hubs-api-guide-net-client.md).</span><span class="sxs-lookup"><span data-stu-id="cfffe-159">For more information about .NET clients with SignalR, see [Hubs API Guide - .NET Client](../guide-to-the-api/hubs-api-guide-net-client.md).</span></span>

<a id="cookie"></a>

### <a name="cookie"></a><span data-ttu-id="cfffe-160">Cookie</span><span class="sxs-lookup"><span data-stu-id="cfffe-160">Cookie</span></span>

<span data-ttu-id="cfffe-161">Quando o cliente .NET interage com um hub que usa a autenticação de formulários do ASP.NET, você precisará definir manualmente o cookie de autenticação sobre a conexão.</span><span class="sxs-lookup"><span data-stu-id="cfffe-161">When your .NET client interacts with a hub that uses ASP.NET Forms Authentication, you will need to manually set the authentication cookie on the connection.</span></span> <span data-ttu-id="cfffe-162">Você adiciona o cookie para o `CookieContainer` propriedade sobre a [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) objeto.</span><span class="sxs-lookup"><span data-stu-id="cfffe-162">You add the cookie to the `CookieContainer` property on the [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) object.</span></span> <span data-ttu-id="cfffe-163">O exemplo a seguir mostra um aplicativo de console que recupera um cookie de autenticação de uma página da web e adiciona esse cookie para a conexão.</span><span class="sxs-lookup"><span data-stu-id="cfffe-163">The following example shows a console app that retrieves an authentication cookie from a web page and adds that cookie to the connection.</span></span> <span data-ttu-id="cfffe-164">A URL `https://www.contoso.com/RemoteLogin` em pontos de exemplo para uma página da web que você precisaria criar.</span><span class="sxs-lookup"><span data-stu-id="cfffe-164">The URL `https://www.contoso.com/RemoteLogin` in the example points to a web page that you would need to create.</span></span> <span data-ttu-id="cfffe-165">A página de recuperar o nome de usuário postados e a senha e tentar fazer logon do usuário com as credenciais.</span><span class="sxs-lookup"><span data-stu-id="cfffe-165">The page would retrieve the posted user name and password, and attempt to log in the user with the credentials.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

<span data-ttu-id="cfffe-166">O aplicativo de console envia as credenciais a serem www.contoso.com/RemoteLogin que pode se referir a uma página vazia que contém o seguinte arquivo de code-behind.</span><span class="sxs-lookup"><span data-stu-id="cfffe-166">The console app posts the credentials to www.contoso.com/RemoteLogin which could refer to an empty page that contains the following code-behind file.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a><span data-ttu-id="cfffe-167">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="cfffe-167">Windows authentication</span></span>

<span data-ttu-id="cfffe-168">Ao usar a autenticação do Windows, você pode passar credenciais do usuário atual usando o [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) propriedade.</span><span class="sxs-lookup"><span data-stu-id="cfffe-168">When using Windows authentication, you can pass the current user's credentials by using the [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) property.</span></span> <span data-ttu-id="cfffe-169">Você pode definir as credenciais para a conexão com o valor da DefaultCredentials.</span><span class="sxs-lookup"><span data-stu-id="cfffe-169">You set the credentials for the connection to the value of the DefaultCredentials.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a><span data-ttu-id="cfffe-170">Cabeçalho de Conexão</span><span class="sxs-lookup"><span data-stu-id="cfffe-170">Connection header</span></span>

<span data-ttu-id="cfffe-171">Se seu aplicativo não estiver usando cookies, você pode passar informações de usuário no cabeçalho da conexão.</span><span class="sxs-lookup"><span data-stu-id="cfffe-171">If your application is not using cookies, you can pass user information in the connection header.</span></span> <span data-ttu-id="cfffe-172">Por exemplo, você pode passar um token no cabeçalho da conexão.</span><span class="sxs-lookup"><span data-stu-id="cfffe-172">For example, you can pass a token in the connection header.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

<span data-ttu-id="cfffe-173">Em seguida, no hub, você poderia verificar o token do usuário.</span><span class="sxs-lookup"><span data-stu-id="cfffe-173">Then, in the hub, you would verify the user's token.</span></span>

<a id="certificate"></a>

### <a name="certificate"></a><span data-ttu-id="cfffe-174">Certificado</span><span class="sxs-lookup"><span data-stu-id="cfffe-174">Certificate</span></span>

<span data-ttu-id="cfffe-175">Você pode passar um certificado de cliente para verificar se o usuário.</span><span class="sxs-lookup"><span data-stu-id="cfffe-175">You can pass a client certificate to verify the user.</span></span> <span data-ttu-id="cfffe-176">Você adiciona o certificado ao criar a conexão.</span><span class="sxs-lookup"><span data-stu-id="cfffe-176">You add the certificate when creating the connection.</span></span> <span data-ttu-id="cfffe-177">O exemplo a seguir mostra apenas como adicionar um certificado de cliente para a conexão; ele não mostra o aplicativo de console completo.</span><span class="sxs-lookup"><span data-stu-id="cfffe-177">The following example shows only how to add a client certificate to the connection; it does not show the full console app.</span></span> <span data-ttu-id="cfffe-178">Ele usa o [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) classe que fornece várias maneiras diferentes para criar o certificado.</span><span class="sxs-lookup"><span data-stu-id="cfffe-178">It uses the [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) class which provides several different ways to create the certificate.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]
