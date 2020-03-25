---
title: ASP.NET Core seguro Blazor Webassembly
author: guardrex
description: Saiba como proteger Blazor aplicativos WebAssemlby como SPAs (aplicativos de página única).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: 652d4c61110f786396d9d5af4f131b817c40e333
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219240"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>ASP.NET Core seguro Blazor Webassembly

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor aplicativos Webassembly são protegidos da mesma maneira que os aplicativos de página única (SPAs). Há várias abordagens para autenticar usuários no SPAs, mas a abordagem mais comum e abrangente é usar uma implementação baseada no [protocolo oAuth 2,0](https://oauth.net/), como o [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteca de autenticação

Blazor Webassembly dá suporte à autenticação e à autorização de aplicativos usando o OIDC por meio da biblioteca `Microsoft.AspNetCore.Components.WebAssembly.Authentication`. A biblioteca fornece um conjunto de primitivos para autenticação direta contra back-ends ASP.NET Core. A biblioteca integra ASP.NET Core identidade com suporte à autorização de API criado com base no [servidor de identidade](https://identityserver.io/). A biblioteca pode se autenticar em qualquer provedor de identidade de terceiros (IP) que ofereça suporte a OIDC, que são chamadas de provedores de OpenID (OP).

O suporte de autenticação no Webassembly Blazor é criado sobre a biblioteca *oidc-Client. js* , que é usada para lidar com os detalhes do protocolo de autenticação subjacente.

Existem outras opções para autenticar SPAs, como o uso de cookies SameSite. No entanto, o design de engenharia de Blazor Webassembly é liquidado em oAuth e OIDC como a melhor opção para autenticação em Blazor aplicativos Webassembly. [A autenticação baseada em token](xref:security/anti-request-forgery#token-based-authentication) baseada em [tokens Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) foi escolhida pela [autenticação baseada em cookie](xref:security/anti-request-forgery#cookie-based-authentication) para motivos funcionais e de segurança:

* O uso de um protocolo baseado em token oferece uma área de superfície de ataque menor, pois os tokens não são enviados em todas as solicitações.
* Os pontos de extremidade do servidor não exigem proteção contra [falsificação de solicitação entre sites (CSRF)](xref:security/anti-request-forgery) porque os tokens são enviados explicitamente. Isso permite que você hospede Blazor aplicativos Webassembly juntamente com os aplicativos MVC ou páginas Razor.
* Tokens têm permissões mais estreitas do que cookies. Por exemplo, os tokens não podem ser usados para gerenciar a conta de usuário ou alterar a senha de um usuário, a menos que essa funcionalidade seja explicitamente implementada.
* Os tokens têm um tempo de vida curto, uma hora por padrão, o que limita a janela de ataque. Os tokens também podem ser revogados a qualquer momento.
* As JWTs independentes oferecem garantias ao cliente e ao servidor sobre o processo de autenticação. Por exemplo, um cliente tem os meios para detectar e validar que os tokens recebidos são legítimos e foram emitidos como parte de um determinado processo de autenticação. Se um terceiro tentar alternar um token no meio do processo de autenticação, o cliente poderá detectar o token comutado e evitar usá-lo.
* Tokens com oAuth e OIDC não dependem do agente do usuário se comportarem corretamente para garantir que o aplicativo seja seguro.
* Protocolos baseados em token, como oAuth e OIDC, permitem autenticar e autorizar aplicativos hospedados e autônomos com o mesmo conjunto de características de segurança.

## <a name="authentication-process-with-oidc"></a>Processo de autenticação com OIDC

A biblioteca de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` oferece vários primitivos para implementar a autenticação e a autorização usando o OIDC. Em termos gerais, a autenticação funciona da seguinte maneira:

* Quando um usuário anônimo seleciona o botão de logon ou solicita uma página com o atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado, o usuário é redirecionado para a página de logon do aplicativo (`/authentication/login`).
* Na página de logon, a biblioteca de autenticação se prepara para um redirecionamento para o ponto de extremidade de autorização. O ponto de extremidade de autorização está fora do Blazor aplicativo Webassembly e pode ser hospedado em uma origem separada. O ponto de extremidade é responsável por determinar se o usuário está autenticado e para emitir um ou mais tokens em resposta. A biblioteca de autenticação fornece um retorno de chamada de logon para receber a resposta de autenticação.
  * Se o usuário não for autenticado, o usuário será redirecionado para o sistema de autenticação subjacente, que geralmente é ASP.NET Core identidade.
  * Se o usuário já foi autenticado, o ponto de extremidade de autorização gera os tokens apropriados e redireciona o navegador de volta para o ponto de extremidade de retorno de chamada de logon (`/authentication/login-callback`).
* Quando o aplicativo Webassembly Blazor carrega o ponto de extremidade de retorno de chamada de logon (`/authentication/login-callback`), a resposta de autenticação é processada.
  * Se o processo de autenticação for concluído com êxito, o usuário será autenticado e, opcionalmente, enviado de volta para a URL protegida original solicitada pelo usuário.
  * Se o processo de autenticação falhar por algum motivo, o usuário será enviado para a página de falha de logon (`/authentication/login-failed`) e um erro será exibido.
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opções para aplicativos hospedados e provedores de logon de terceiros

Ao autenticar e autorizar um aplicativo Webassembly hospedado Blazor com um provedor de terceiros, há várias opções disponíveis para autenticar o usuário. O que você escolher dependerá de seu cenário.

Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Autenticar usuários para chamar somente APIs de terceiros protegidas

Autentique o usuário com um fluxo oAuth do lado do cliente em relação ao provedor de API de terceiros:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Neste cenário:

* O servidor que hospeda o aplicativo não desempenha uma função.
* As APIs no servidor não podem ser protegidas.
* O aplicativo só pode chamar APIs de terceiros protegidas.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e terceiros

Configure a identidade com um provedor de logon de terceiros. Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.

Quando um usuário faz logon, a identidade coleta tokens de acesso e de atualização como parte do processo de autenticação. Nesse ponto, há algumas abordagens disponíveis para fazer chamadas à API para APIs de terceiros.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Usar um token de acesso do servidor para recuperar o token de acesso de terceiros

Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros de um ponto de extremidade de API de servidor. A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente da identidade no cliente.

Não recomendamos essa abordagem. Essa abordagem requer tratar o token de acesso de terceiros como se ele fosse gerado para um cliente público. Nos termos do oAuth, o aplicativo público não tem um segredo do cliente porque não pode ser confiável para armazenar segredos com segurança e o token de acesso é produzido para um cliente confidencial. Um cliente confidencial é um cliente que tem um segredo do cliente e é considerado capaz de armazenar segredos com segurança.

* O token de acesso de terceiros pode receber escopos adicionais para executar operações confidenciais com base no fato de que a terceira parte emitiu o token para um cliente mais confiável.
* Da mesma forma, os tokens de atualização não devem ser emitidos para um cliente que não seja confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em vigor.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Fazer chamadas à API do cliente para a API do servidor para chamar APIs de terceiros

Faça uma chamada de API do cliente para a API do servidor. No servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.

Embora essa abordagem exija um salto de rede extra por meio do servidor para chamar uma API de terceiros, ela finalmente resulta em uma experiência mais segura:

* O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.
* O aplicativo não pode vazar tokens de acesso do servidor que possa conter permissões mais confidenciais.
