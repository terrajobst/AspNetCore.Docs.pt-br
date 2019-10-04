---
title: Manter declarações e tokens adicionais de provedores externos no ASP.NET Core
author: guardrex
description: Saiba como estabelecer declarações e tokens adicionais de provedores externos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: cdf263df8d1aa17ea3820a16ecbd10abce9d683d
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925149"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="ccabb-103">Manter declarações e tokens adicionais de provedores externos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ccabb-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="ccabb-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ccabb-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ccabb-105">Um aplicativo ASP.NET Core pode estabelecer declarações e tokens adicionais de provedores de autenticação externos, como Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="ccabb-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="ccabb-106">Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados do usuário em declarações adicionais é o mesmo.</span><span class="sxs-lookup"><span data-stu-id="ccabb-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="ccabb-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ccabb-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ccabb-108">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ccabb-108">Prerequisites</span></span>

<span data-ttu-id="ccabb-109">Decida quais provedores de autenticação externa dar suporte no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ccabb-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="ccabb-110">Para cada provedor, registre o aplicativo e obtenha uma ID do cliente e um segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="ccabb-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="ccabb-111">Para obter mais informações, consulte <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="ccabb-112">O aplicativo de exemplo usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="ccabb-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="ccabb-113">Definir a ID do cliente e o segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="ccabb-113">Set the client ID and client secret</span></span>

<span data-ttu-id="ccabb-114">O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID do cliente e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="ccabb-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="ccabb-115">Os valores de ID do cliente e segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor.</span><span class="sxs-lookup"><span data-stu-id="ccabb-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="ccabb-116">Cada provedor externo que o aplicativo usa deve ser configurado independentemente com a ID do cliente do provedor e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="ccabb-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="ccabb-117">Para obter mais informações, consulte os tópicos do provedor de autenticação externa que se aplicam ao seu cenário:</span><span class="sxs-lookup"><span data-stu-id="ccabb-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="ccabb-118">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="ccabb-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="ccabb-119">Autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="ccabb-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="ccabb-120">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="ccabb-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="ccabb-121">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="ccabb-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="ccabb-122">Outros provedores de autenticação</span><span class="sxs-lookup"><span data-stu-id="ccabb-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="ccabb-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="ccabb-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="ccabb-124">O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e o segredo do cliente fornecidos pelo Google:</span><span class="sxs-lookup"><span data-stu-id="ccabb-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="ccabb-125">Estabelecer o escopo de autenticação</span><span class="sxs-lookup"><span data-stu-id="ccabb-125">Establish the authentication scope</span></span>

<span data-ttu-id="ccabb-126">Especifique a lista de permissões a serem recuperadas do provedor especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="ccabb-127">Os escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="ccabb-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="ccabb-128">Provider</span><span class="sxs-lookup"><span data-stu-id="ccabb-128">Provider</span></span>  | <span data-ttu-id="ccabb-129">Escopo</span><span class="sxs-lookup"><span data-stu-id="ccabb-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="ccabb-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="ccabb-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="ccabb-131">Google</span><span class="sxs-lookup"><span data-stu-id="ccabb-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="ccabb-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="ccabb-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="ccabb-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="ccabb-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="ccabb-134">No aplicativo de exemplo, o escopo `userinfo.profile` do Google é adicionado automaticamente pela estrutura quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> é chamado no <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="ccabb-135">Se o aplicativo exigir escopos adicionais, adicione-os às opções.</span><span class="sxs-lookup"><span data-stu-id="ccabb-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="ccabb-136">No exemplo a seguir, o escopo do Google `https://www.googleapis.com/auth/user.birthday.read` é adicionado para recuperar o aniversário de um usuário:</span><span class="sxs-lookup"><span data-stu-id="ccabb-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="ccabb-137">Mapear chaves de dados do usuário e criar declarações</span><span class="sxs-lookup"><span data-stu-id="ccabb-137">Map user data keys and create claims</span></span>

<span data-ttu-id="ccabb-138">Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada chave/subchave nos dados de usuário JSON do provedor externo para que a identidade do aplicativo seja lida na entrada.</span><span class="sxs-lookup"><span data-stu-id="ccabb-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="ccabb-139">Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="ccabb-140">O aplicativo de exemplo cria declarações de localidade (`urn:google:locale`) e imagem (`urn:google:picture`) das chaves `locale` e `picture` nos dados de usuário do Google:</span><span class="sxs-lookup"><span data-stu-id="ccabb-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="ccabb-141">No <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, um <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-141">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="ccabb-142">Durante o processo de entrada, o <xref:Microsoft.AspNetCore.Identity.UserManager%601> pode armazenar uma declaração `ApplicationUser` para dados do usuário disponíveis no <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="ccabb-143">No aplicativo de exemplo, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) estabelece as declarações de localidade (`urn:google:locale`) e imagem (`urn:google:picture`) para a entrada `ApplicationUser`, incluindo uma declaração para <xref:System.Security.Claims.ClaimTypes.GivenName>:</span><span class="sxs-lookup"><span data-stu-id="ccabb-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="ccabb-144">Por padrão, as declarações de um usuário são armazenadas no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="ccabb-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="ccabb-145">Se o cookie de autenticação for muito grande, isso poderá causar falha no aplicativo porque:</span><span class="sxs-lookup"><span data-stu-id="ccabb-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="ccabb-146">O navegador detecta que o cabeçalho do cookie é muito longo.</span><span class="sxs-lookup"><span data-stu-id="ccabb-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="ccabb-147">O tamanho geral da solicitação é muito grande.</span><span class="sxs-lookup"><span data-stu-id="ccabb-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="ccabb-148">Se uma grande quantidade de dados do usuário for necessária para processar solicitações do usuário:</span><span class="sxs-lookup"><span data-stu-id="ccabb-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="ccabb-149">Limite o número e o tamanho das declarações do usuário para o processamento da solicitação apenas para o que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="ccabb-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="ccabb-150">Use um <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> personalizado para o middleware de autenticação de cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para armazenar a identidade entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="ccabb-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="ccabb-151">Preserve grandes quantidades de informações de identidade no servidor ao enviar apenas uma pequena chave de identificador de sessão para o cliente.</span><span class="sxs-lookup"><span data-stu-id="ccabb-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="ccabb-152">Salvar o token de acesso</span><span class="sxs-lookup"><span data-stu-id="ccabb-152">Save the access token</span></span>

<span data-ttu-id="ccabb-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> define se os tokens de acesso e atualização devem ser armazenados no <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="ccabb-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="ccabb-154">`SaveTokens` é definido como `false` por padrão para reduzir o tamanho do cookie de autenticação final.</span><span class="sxs-lookup"><span data-stu-id="ccabb-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="ccabb-155">O aplicativo de exemplo define o valor de `SaveTokens` como `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="ccabb-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="ccabb-156">Quando `OnPostConfirmationAsync` for executado, armazene o token de acesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo no `AuthenticationProperties` do `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="ccabb-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="ccabb-157">O aplicativo de exemplo salva o token de acesso em `OnPostConfirmationAsync` (novo registro de usuário) e `OnGetCallbackAsync` (usuário registrado anteriormente) em *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="ccabb-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="ccabb-158">Como adicionar tokens personalizados adicionais</span><span class="sxs-lookup"><span data-stu-id="ccabb-158">How to add additional custom tokens</span></span>

<span data-ttu-id="ccabb-159">Para demonstrar como adicionar um token personalizado, que é armazenado como parte do `SaveTokens`, o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o <xref:System.DateTime> atual a um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="ccabb-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="ccabb-160">Criando e adicionando declarações</span><span class="sxs-lookup"><span data-stu-id="ccabb-160">Creating and adding claims</span></span>

<span data-ttu-id="ccabb-161">A estrutura fornece ações comuns e métodos de extensão para criar e adicionar declarações à coleção.</span><span class="sxs-lookup"><span data-stu-id="ccabb-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="ccabb-162">Para obter mais informações, consulte o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="ccabb-163">Os usuários podem definir ações personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando o método abstrato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="ccabb-164">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="ccabb-165">Remoção de declarações e ações de declaração</span><span class="sxs-lookup"><span data-stu-id="ccabb-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="ccabb-166">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) remove todas as ações de declaração para o determinado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da coleção.</span><span class="sxs-lookup"><span data-stu-id="ccabb-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="ccabb-167">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) exclui uma declaração do determinado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da identidade.</span><span class="sxs-lookup"><span data-stu-id="ccabb-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="ccabb-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> é usado principalmente com o [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para remover declarações geradas por protocolo.</span><span class="sxs-lookup"><span data-stu-id="ccabb-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="ccabb-169">Saída do aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="ccabb-169">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ccabb-170">Um aplicativo ASP.NET Core pode estabelecer declarações e tokens adicionais de provedores de autenticação externos, como Facebook, Google, Microsoft e Twitter.</span><span class="sxs-lookup"><span data-stu-id="ccabb-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="ccabb-171">Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados do usuário em declarações adicionais é o mesmo.</span><span class="sxs-lookup"><span data-stu-id="ccabb-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="ccabb-172">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ccabb-172">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ccabb-173">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ccabb-173">Prerequisites</span></span>

<span data-ttu-id="ccabb-174">Decida quais provedores de autenticação externa dar suporte no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ccabb-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="ccabb-175">Para cada provedor, registre o aplicativo e obtenha uma ID do cliente e um segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="ccabb-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="ccabb-176">Para obter mais informações, consulte <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="ccabb-177">O aplicativo de exemplo usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="ccabb-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="ccabb-178">Definir a ID do cliente e o segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="ccabb-178">Set the client ID and client secret</span></span>

<span data-ttu-id="ccabb-179">O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID do cliente e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="ccabb-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="ccabb-180">Os valores de ID do cliente e segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor.</span><span class="sxs-lookup"><span data-stu-id="ccabb-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="ccabb-181">Cada provedor externo que o aplicativo usa deve ser configurado independentemente com a ID do cliente do provedor e o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="ccabb-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="ccabb-182">Para obter mais informações, consulte os tópicos do provedor de autenticação externa que se aplicam ao seu cenário:</span><span class="sxs-lookup"><span data-stu-id="ccabb-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="ccabb-183">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="ccabb-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="ccabb-184">Autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="ccabb-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="ccabb-185">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="ccabb-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="ccabb-186">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="ccabb-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="ccabb-187">Outros provedores de autenticação</span><span class="sxs-lookup"><span data-stu-id="ccabb-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="ccabb-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="ccabb-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="ccabb-189">O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e o segredo do cliente fornecidos pelo Google:</span><span class="sxs-lookup"><span data-stu-id="ccabb-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="ccabb-190">Estabelecer o escopo de autenticação</span><span class="sxs-lookup"><span data-stu-id="ccabb-190">Establish the authentication scope</span></span>

<span data-ttu-id="ccabb-191">Especifique a lista de permissões a serem recuperadas do provedor especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="ccabb-192">Os escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="ccabb-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="ccabb-193">Provider</span><span class="sxs-lookup"><span data-stu-id="ccabb-193">Provider</span></span>  | <span data-ttu-id="ccabb-194">Escopo</span><span class="sxs-lookup"><span data-stu-id="ccabb-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="ccabb-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="ccabb-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="ccabb-196">Google</span><span class="sxs-lookup"><span data-stu-id="ccabb-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="ccabb-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="ccabb-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="ccabb-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="ccabb-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="ccabb-199">No aplicativo de exemplo, o escopo `userinfo.profile` do Google é adicionado automaticamente pela estrutura quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> é chamado no <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="ccabb-200">Se o aplicativo exigir escopos adicionais, adicione-os às opções.</span><span class="sxs-lookup"><span data-stu-id="ccabb-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="ccabb-201">No exemplo a seguir, o escopo do Google `https://www.googleapis.com/auth/user.birthday.read` é adicionado para recuperar o aniversário de um usuário:</span><span class="sxs-lookup"><span data-stu-id="ccabb-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="ccabb-202">Mapear chaves de dados do usuário e criar declarações</span><span class="sxs-lookup"><span data-stu-id="ccabb-202">Map user data keys and create claims</span></span>

<span data-ttu-id="ccabb-203">Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada chave/subchave nos dados de usuário JSON do provedor externo para que a identidade do aplicativo seja lida na entrada.</span><span class="sxs-lookup"><span data-stu-id="ccabb-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="ccabb-204">Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="ccabb-205">O aplicativo de exemplo cria declarações de localidade (`urn:google:locale`) e imagem (`urn:google:picture`) das chaves `locale` e `picture` nos dados de usuário do Google:</span><span class="sxs-lookup"><span data-stu-id="ccabb-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="ccabb-206">No <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, um <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-206">In <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="ccabb-207">Durante o processo de entrada, o <xref:Microsoft.AspNetCore.Identity.UserManager%601> pode armazenar uma declaração `ApplicationUser` para dados do usuário disponíveis no <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="ccabb-208">No aplicativo de exemplo, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) estabelece as declarações de localidade (`urn:google:locale`) e imagem (`urn:google:picture`) para a entrada `ApplicationUser`, incluindo uma declaração para <xref:System.Security.Claims.ClaimTypes.GivenName>:</span><span class="sxs-lookup"><span data-stu-id="ccabb-208">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="ccabb-209">Por padrão, as declarações de um usuário são armazenadas no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="ccabb-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="ccabb-210">Se o cookie de autenticação for muito grande, isso poderá causar falha no aplicativo porque:</span><span class="sxs-lookup"><span data-stu-id="ccabb-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="ccabb-211">O navegador detecta que o cabeçalho do cookie é muito longo.</span><span class="sxs-lookup"><span data-stu-id="ccabb-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="ccabb-212">O tamanho geral da solicitação é muito grande.</span><span class="sxs-lookup"><span data-stu-id="ccabb-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="ccabb-213">Se uma grande quantidade de dados do usuário for necessária para processar solicitações do usuário:</span><span class="sxs-lookup"><span data-stu-id="ccabb-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="ccabb-214">Limite o número e o tamanho das declarações do usuário para o processamento da solicitação apenas para o que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="ccabb-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="ccabb-215">Use um <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> personalizado para o middleware de autenticação de cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para armazenar a identidade entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="ccabb-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="ccabb-216">Preserve grandes quantidades de informações de identidade no servidor ao enviar apenas uma pequena chave de identificador de sessão para o cliente.</span><span class="sxs-lookup"><span data-stu-id="ccabb-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="ccabb-217">Salvar o token de acesso</span><span class="sxs-lookup"><span data-stu-id="ccabb-217">Save the access token</span></span>

<span data-ttu-id="ccabb-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> define se os tokens de acesso e atualização devem ser armazenados no <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="ccabb-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="ccabb-219">`SaveTokens` é definido como `false` por padrão para reduzir o tamanho do cookie de autenticação final.</span><span class="sxs-lookup"><span data-stu-id="ccabb-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="ccabb-220">O aplicativo de exemplo define o valor de `SaveTokens` como `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span><span class="sxs-lookup"><span data-stu-id="ccabb-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="ccabb-221">Quando `OnPostConfirmationAsync` for executado, armazene o token de acesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo no `AuthenticationProperties` do `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="ccabb-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="ccabb-222">O aplicativo de exemplo salva o token de acesso em `OnPostConfirmationAsync` (novo registro de usuário) e `OnGetCallbackAsync` (usuário registrado anteriormente) em *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="ccabb-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="ccabb-223">Como adicionar tokens personalizados adicionais</span><span class="sxs-lookup"><span data-stu-id="ccabb-223">How to add additional custom tokens</span></span>

<span data-ttu-id="ccabb-224">Para demonstrar como adicionar um token personalizado, que é armazenado como parte do `SaveTokens`, o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o <xref:System.DateTime> atual a um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="ccabb-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="ccabb-225">Criando e adicionando declarações</span><span class="sxs-lookup"><span data-stu-id="ccabb-225">Creating and adding claims</span></span>

<span data-ttu-id="ccabb-226">A estrutura fornece ações comuns e métodos de extensão para criar e adicionar declarações à coleção.</span><span class="sxs-lookup"><span data-stu-id="ccabb-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="ccabb-227">Para obter mais informações, consulte o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="ccabb-228">Os usuários podem definir ações personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando o método abstrato <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="ccabb-229">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="ccabb-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="ccabb-230">Remoção de declarações e ações de declaração</span><span class="sxs-lookup"><span data-stu-id="ccabb-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="ccabb-231">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) remove todas as ações de declaração para o determinado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da coleção.</span><span class="sxs-lookup"><span data-stu-id="ccabb-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="ccabb-232">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) exclui uma declaração do determinado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da identidade.</span><span class="sxs-lookup"><span data-stu-id="ccabb-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="ccabb-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> é usado principalmente com o [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para remover declarações geradas por protocolo.</span><span class="sxs-lookup"><span data-stu-id="ccabb-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="ccabb-234">Saída do aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="ccabb-234">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ccabb-235">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ccabb-235">Additional resources</span></span>

* <span data-ttu-id="ccabb-236">[ASPNET/AspNetCore Engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; o aplicativo de exemplo vinculado está no Branch de engenharia `master` [do repositório GitHub ASPNET/AspNetCore](https://github.com/aspnet/AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="ccabb-236">[aspnet/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [aspnet/AspNetCore GitHub repo's](https://github.com/aspnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="ccabb-237">A ramificação `master` contém código em desenvolvimento ativo para a próxima versão do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ccabb-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="ccabb-238">Para ver uma versão do aplicativo de exemplo para uma versão lançada do ASP.NET Core, use a lista suspensa **Branch** para selecionar um Branch de lançamento (por exemplo `release/{X.Y}`).</span><span class="sxs-lookup"><span data-stu-id="ccabb-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
