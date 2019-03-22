---
title: Manter declarações adicionais e os tokens de provedores externos no ASP.NET Core
author: guardrex
description: Saiba como estabelecer declarações adicionais e tokens de provedores externos.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 49c323fab64bd4ea52dd1d8cf2e43a79d4d0d0dc
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327346"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Manter declarações adicionais e os tokens de provedores externos no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Um aplicativo ASP.NET Core pode estabelecer declarações adicionais e tokens de provedores de autenticação externa, como Facebook, Google, Microsoft e Twitter. Cada provedor revela informações diferentes sobre os usuários em sua plataforma, mas o padrão para receber e transformar dados de usuário em declarações adicionais é o mesmo.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

Decida quais provedores de autenticação externa para dar suporte a no aplicativo. Para cada provedor, registrar o aplicativo e obter o ID do cliente e segredo do cliente. Para obter mais informações, consulte <xref:security/authentication/social/index>. O [aplicativo de exemplo](#sample-app-instructions) usa o [provedor de autenticação do Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Defina a ID do cliente e segredo do cliente

O provedor de autenticação OAuth estabelece uma relação de confiança com um aplicativo usando uma ID de cliente e o segredo do cliente. ID do cliente e valores de segredo do cliente são criados para o aplicativo pelo provedor de autenticação externa quando o aplicativo é registrado com o provedor. Cada provedor externo que usa o aplicativo deve ser configurado independentemente com a ID do cliente e o segredo do cliente do provedor. Para obter mais informações, consulte os tópicos de provedor de autenticação externa que se aplicam ao seu cenário:

* [Autenticação do Facebook](xref:security/authentication/facebook-logins)
* [Autenticação do Google](xref:security/authentication/google-logins)
* [Autenticação da Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticação do Twitter](xref:security/authentication/twitter-logins)
* [Outros provedores de autenticação](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

O aplicativo de exemplo configura o provedor de autenticação do Google com uma ID do cliente e segredo do cliente fornecido pelo Google:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,6)]

## <a name="establish-the-authentication-scope"></a>Estabelecer o escopo de autenticação

Especifique a lista de permissões para recuperar do provedor, especificando o <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>. Escopos de autenticação para provedores externos comuns aparecem na tabela a seguir.

| Provider  | Escopo                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/plus.login`                     |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

O aplicativo de exemplo adiciona o Google `plus.login` escopo para solicitar a entrada do Google + nas permissões:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=7)]

## <a name="map-user-data-keys-and-create-claims"></a>Mapear chaves de dados de usuário e criar declarações

Nas opções do provedor, especifique um <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> para cada chave nos dados de usuário do provedor externo JSON para a identidade do aplicativo ler em entrar. Para obter mais informações sobre tipos de declaração, consulte <xref:System.Security.Claims.ClaimTypes>.

O aplicativo de exemplo cria um <xref:System.Security.Claims.ClaimTypes.Gender> reivindicar do `gender` chave nos dados de usuário do Google:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=8)]

Na <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, uma <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) é conectado ao aplicativo com <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Durante o logon no processo, o <xref:Microsoft.AspNetCore.Identity.UserManager%601> pode armazenar um `ApplicationUser` de declaração para os dados de usuário disponíveis no <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.

No aplicativo de exemplo, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) estabelece uma <xref:System.Security.Claims.ClaimTypes.Gender> de declaração para assinado no `ApplicationUser`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=30-31)]

## <a name="save-the-access-token"></a>Salve o token de acesso

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Define se os tokens de acesso e de atualização devem ser armazenadas do <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> após uma autorização bem-sucedida. `SaveTokens` é definido como `false` por padrão para reduzir o tamanho do cookie de autenticação final.

O aplicativo de exemplo define o valor de `SaveTokens` à `true` em <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=9)]

Quando `OnPostConfirmationAsync` é executado, armazenar o token de acesso ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) do provedor externo no `ApplicationUser`do `AuthenticationProperties`.

O aplicativo de exemplo salva o token de acesso em:

* `OnPostConfirmationAsync` &ndash; É executado para o novo registro do usuário.
* `OnGetCallbackAsync` &ndash; Executa quando um usuário registrado anteriormente entra no aplicativo.

*Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=34-35)]

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnGetCallbackAsync&highlight=31-32)]

## <a name="how-to-add-additional-custom-tokens"></a>Como adicionar tokens personalizados adicionais

Para demonstrar como adicionar um token personalizado, que é armazenado como parte da `SaveTokens`, o aplicativo de exemplo adiciona um <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> com o atual <xref:System.DateTime> para um [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=10-21)]

## <a name="sample-app-instructions"></a>Instruções do aplicativo de exemplo

O aplicativo de exemplo demonstra como:

* Obtenha o sexo do usuário do Google e armazene uma declaração de sexo com o valor.
* Store o token de acesso do Google, o usuário `AuthenticationProperties`.

Para usar o aplicativo de exemplo:

1. Registrar o aplicativo e obter uma ID de cliente válido e o segredo do cliente para autenticação do Google. Para obter mais informações, consulte <xref:security/authentication/google-logins>.
1. Forneça a ID de cliente e o segredo do cliente para o aplicativo na <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> de `Startup.ConfigureServices`.
1. Execute o aplicativo e solicite a página Minhas declarações. Quando o usuário não estiver conectado, o aplicativo redireciona para o Google. Entrar com o Google. Google redireciona o usuário retorne ao aplicativo (`/Home/MyClaims`). O usuário é autenticado e a página Minhas declarações é carregada. A declaração de gênero esteja presente em **declarações de usuário** com o valor obtido do Google. O token de acesso será exibido na **as propriedades de autenticação**.

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    b36a7b09-9135-4810-b7a5-78697ff23e99
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    username@gmail.com
AspNet.Identity.SecurityStamp
    29G2TB881ATCUQFJSRFG1S0QJ0OOAWVT
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/gender
    female
http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod
    Google

Authentication Properties

.Token.access_token
    bv42.Dgw...GQMv9ArLPs
.Token.token_type
    Bearer
.Token.expires_at
    2018-08-27T19:08:00.0000000+00:00
.Token.TicketCreated
    8/27/2018 6:08:00 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.issued
    Mon, 27 Aug 2018 18:08:05 GMT
.expires
    Mon, 10 Sep 2018 18:08:05 GMT
```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
