---
title: Manter declarações adicionais e os tokens de provedores externos no ASP.NET Core
author: guardrex
description: Saiba como estabelecer declarações adicionais e tokens de provedores externos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: e18287e5a4171b3f7a6daa122111448b8447c1da
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874852"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Manter declarações adicionais e os tokens de provedores externos no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Um aplicativo ASP.NET Core pode estabelecer declarações adicionais e tokens de provedores de autenticação externa, como Facebook, Google, Microsoft e Twitter. Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados de usuário em declarações adicionais é o mesmo.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

Decida quais provedores de autenticação externa para dar suporte a no aplicativo. Para cada provedor, registrar o aplicativo e obter o ID do cliente e segredo do cliente. Para obter mais informações, consulte <xref:security/authentication/social/index>. O aplicativo de exemplo usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Defina a ID do cliente e segredo do cliente

O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID de cliente e o segredo do cliente. ID do cliente e valores de segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor. Cada provedor externo que usa o aplicativo deve ser configurado independentemente com a ID do cliente e o segredo do cliente do provedor. Para obter mais informações, consulte os tópicos de provedor de autenticação externa que se aplicam ao seu cenário:

* [Autenticação do Facebook](xref:security/authentication/facebook-logins)
* [Autenticação do Google](xref:security/authentication/google-logins)
* [Autenticação da Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticação do Twitter](xref:security/authentication/twitter-logins)
* [Outros provedores de autenticação](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e segredo do cliente fornecido pelo Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Estabelecer o escopo de autenticação

Especifique a lista de permissões para recuperar do provedor, especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>. Escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.

| Provider  | Escopo                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Em que o aplicativo de exemplo, Google `userinfo.profile` escopo é adicionado automaticamente pelo framework quando <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> é chamado no <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>. Se o aplicativo exigir escopos adicionais, adicione-o para as opções. No exemplo a seguir, o Google `https://www.googleapis.com/auth/user.birthday.read` escopo é adicionado para recuperar o aniversário do usuário:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapear chaves de dados de usuário e criar declarações

Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada chave/subchave nos dados de usuário do provedor externo JSON para a identidade do aplicativo ler em entrar. Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes>.

O aplicativo de exemplo cria a localidade (`urn:google:locale`) e a imagem (`urn:google:picture`) as declarações do `locale` e `picture` chaves nos dados de usuário do Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

Na <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, uma <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Durante o logon no processo, o <xref:Microsoft.AspNetCore.Identity.UserManager%601> pode armazenar uma `ApplicationUser` declarações para dados de usuário disponíveis no <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.

No aplicativo de exemplo, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) estabelece a localidade (`urn:google:locale`) e a imagem (`urn:google:picture`) declarações para assinado no `ApplicationUser`, incluindo uma declaração para <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Por padrão, as declarações do usuário são armazenadas no cookie de autenticação. Se o cookie de autenticação é muito grande, pode fazer com que o aplicativo falhar, porque:

* O navegador detecta que o cabeçalho do cookie é muito longo.
* O tamanho geral da solicitação é muito grande.

Se uma grande quantidade de dados do usuário é necessária para processar solicitações de usuário:

* Limite o número e tamanho de declarações de usuário para processamento de solicitações para apenas o que o aplicativo requer.
* Usar um personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para o Middleware de autenticação de Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> para armazenar a identidade entre solicitações. Preserve a grandes quantidades de informações de identidade no servidor durante o envio apenas uma chave de identificador de sessão pequenos para o cliente.

## <a name="save-the-access-token"></a>Salve o token de acesso

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Define se os tokens de acesso e de atualização devem ser armazenadas do <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida. `SaveTokens` é definido como `false` por padrão para reduzir o tamanho do cookie de autenticação final.

O aplicativo de exemplo define o valor de `SaveTokens` à `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Quando `OnPostConfirmationAsync` é executado, armazenar o token de acesso ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo no `ApplicationUser`do `AuthenticationProperties`.

O aplicativo de exemplo salva o token de acesso no `OnPostConfirmationAsync` (novo registro do usuário) e `OnGetCallbackAsync` (usuário registrado anteriormente) em *Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Como adicionar tokens personalizados adicionais

Para demonstrar como adicionar um token personalizado, que é armazenado como parte da `SaveTokens`, o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o atual <xref:System.DateTime> para um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-28)]

## <a name="creating-and-adding-claims"></a>Criando e adicionando declarações

A estrutura fornece ações comuns e métodos de extensão para criar e adicionar declarações à coleção. Para obter mais informações, consulte o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Os usuários podem definir ações personalizadas, derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e a implementação abstrata <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método.

Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Remoção de ações de declaração e declarações

[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) remove todas as declarações de ações para o determinado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da coleção. [ClaimActionCollectionMapExtensions.DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) exclui uma declaração da determinado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> da identidade. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> é basicamente usado com [OIDC (OpenID Connect)](/azure/active-directory/develop/v2-protocols-oidc) remover declarações gerado pelo protocolo.

## <a name="sample-app-output"></a>Saída do aplicativo de exemplo

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

## <a name="additional-resources"></a>Recursos adicionais

* [aplicativo SocialSample da engenharia ASPNET/AspNetCore](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; o aplicativo de exemplo vinculado está no [do repositório do GitHub do aspnet/AspNetCore](https://github.com/aspnet/AspNetCore) `master` branch de engenharia. O `master` branch contém código em desenvolvimento ativo para a próxima versão do ASP.NET Core. Para ver uma versão do aplicativo de exemplo para uma versão de lançamento do ASP.NET Core, use o **Branch** lista suspensa lista para selecionar um branch de lançamento (por exemplo `release/2.2`).
