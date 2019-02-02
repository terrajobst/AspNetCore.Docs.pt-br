---
uid: aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
title: Servidor de autorização do OAuth 2.0 de OWIN | Microsoft Docs
author: hongyes
description: Este tutorial orientará você sobre como implementar um servidor de autorização do OAuth 2.0 usando o middleware OWIN OAuth. Isso é que apenas configurações de um tutorial avançado...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 20acee16-c70c-41e9-b38f-92bfcf9a4c1c
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
msc.type: authoredcontent
ms.openlocfilehash: b8451d2d9e346bd5e2f51ba45e48030a5221b549
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667642"
---
# <a name="owin-oauth-20-authorization-server"></a>Servidor de autorização OAuth 2.0 de OWIN

> Este tutorial orientará você sobre como implementar um servidor de autorização do OAuth 2.0 usando o middleware OWIN OAuth. Este é um tutorial avançado que descreve somente as etapas para criar um servidor de autorização do OWIN OAuth 2.0. Isso não é um tutorial passo a passo. [Baixe o código de exemplo](https://code.msdn.microsoft.com/OWIN-OAuth-20-Authorization-ba2b8783/file/114932/1/AuthorizationServer.zip).
>
> > [!NOTE]
> > Essa estrutura de tópicos não deve ser se destina a ser usado para criar um aplicativo de produção seguro. Este tutorial destina-se a fornecer somente uma estrutura de tópicos sobre como implementar um servidor de autorização do OAuth 2.0 usando o middleware OWIN OAuth.
>
>
> ## <a name="software-versions"></a>Versões de software
>
> | **Mostrado no tutorial** | **Também funciona com** |
> | --- | --- |
> | Windows 8.1 | Windows 10, Windows 8, Windows 7 |
> | [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
> | .NET 4.7.2 |  |
>
> ## <a name="questions-and-comments"></a>Perguntas e comentários
>
> Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los no [projeto Katana no GitHub](https://github.com/aspnet/AspNetKatana/). Para perguntas e comentários sobre o próprio tutorial, consulte a seção de comentários na parte inferior da página.


O [OAuth 2.0 framework](http://tools.ietf.org/html/rfc6749) permite que um aplicativo de terceiros obter acesso limitado a um serviço HTTP. Em vez de usar as credenciais do proprietário do recurso para acessar um recurso protegido, o cliente obtenha um token de acesso (que é uma cadeia de caracteres denotando um escopo específico, tempo de vida e outros atributos de acesso). Tokens de acesso são emitidos para clientes de terceiros por um servidor de autorização com a aprovação do proprietário do recurso.

Este tutorial aborda:

- Como criar um servidor de autorização para dar suporte à autorização de quatro tipos de concessão e tokens de atualização:
    - Concessão de código de autorização
    - Concessão implícita
    - Concessão de credenciais de senha do proprietário do recurso
    - Concessão de credenciais do cliente
- Criando um servidor de recurso que é protegido por um token de acesso.
- Criando clientes OAuth 2.0.

<a id="prerequisites"></a>
## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) conforme indicado na **versões de Software** na parte superior da página.
- Familiaridade com o OWIN. Ver [Introdução ao projeto Katana](https://msdn.microsoft.com/magazine/dn451439.aspx) e [o que há de novo no OWIN e Katana](index.md).
- Familiaridade com [OAuth](http://tools.ietf.org/html/rfc6749) terminologia, incluindo [funções](http://tools.ietf.org/html/rfc6749#section-1.1), [fluxo do protocolo](http://tools.ietf.org/html/rfc6749#section-1.2), e [concessão de autorização](http://tools.ietf.org/html/rfc6749#section-1.3). [Introdução do OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-1) fornece uma boa introdução.

## <a name="create-an-authorization-server"></a>Criar um servidor de autorização

Neste tutorial, estamos será aproximadamente esboço de como usar [OWIN](https://msdn.microsoft.com/magazine/dn451439.aspx) e o ASP.NET MVC para criar um servidor de autorização. Esperamos fornecer assim que um download para o exemplo completo, como este tutorial não inclui cada etapa. Primeiro, crie um aplicativo web vazio chamado *AuthorizationServer* e instalar os seguintes pacotes:

- Microsoft.AspNet.Mvc
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth
- Microsoft.Owin.Security.Cookies
- Owin (ou qualquer outro logon social de pacote como owin)

Adicionar um [classe de inicialização OWIN](owin-startup-class-detection.md) sob a pasta raiz do projeto chamada *inicialização*.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample1.cs?highlight=8)]

Criar uma *App\_iniciar* pasta. Selecione o *App\_começar* pasta e use Shift + Alt + A para adicionar a versão baixada do *AuthorizationServer\App\_Start\Startup.Auth.cs* arquivo.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample2.cs)]

O código acima permite que os cookies e autenticação do Google, que são usados pelo próprio servidor de autorização para gerenciar contas de entrada externo/aplicativo.

O `UseOAuthAuthorizationServer` método de extensão é configurar o servidor de autorização. As opções de configuração são:

- `AuthorizeEndpointPath`: O caminho da solicitação em que os aplicativos clientes redirecionam o agente do usuário para obter os usuários de consentimento para emitir um token ou código. Ele deve começar com uma barra à esquerda, por exemplo, "`/Authorize`".
- `TokenEndpointPath`: Os aplicativos de cliente do caminho de solicitação se comunicam diretamente para obter o token de acesso. Ele deve começar com uma barra à esquerda, como "/ Token". Se o cliente receberá um [client\_segredo](http://tools.ietf.org/html/rfc6749#appendix-A.2), ele deve ser fornecido para esse ponto de extremidade.
- `ApplicationCanDisplayErrors`: Definido como `true` caso queira que o aplicativo web gerar uma página de erro personalizada para os erros de validação de cliente em `/Authorize` ponto de extremidade. Isso é necessário apenas para casos em que o navegador não é redirecionado de volta para o aplicativo cliente, por exemplo, quando o `client_id` ou `redirect_uri` estão incorretas. O `/Authorize` ponto de extremidade deve esperar ver "oauth. Erro","oauth. ErrorDescription"e"oauth. Propriedades de ErrorUri"são adicionadas ao ambiente OWIN.

    > [!NOTE]
    > Se não for true, o servidor de autorização retornará uma página de erro padrão com os detalhes do erro.
- `AllowInsecureHttp`: True para permitir a autorizar e as solicitações de token para chegar em endereços de URI de HTTP e permitir a entrada `redirect_uri` autorizar os parâmetros de solicitação ter endereços de URI do HTTP.

    > [!WARNING]
    > Security - isso é apenas para desenvolvimento.
- `Provider`: O objeto fornecido pelo aplicativo para processar eventos gerados pelo middleware de servidor de autorização. O aplicativo pode implementar a interface completamente ou pode criar uma instância de `OAuthAuthorizationServerProvider` e atribuir representantes necessários para os fluxos de OAuth que dá suporte a esse servidor.
- `AuthorizationCodeProvider`: Produz um código de autorização de uso único para retornar ao aplicativo cliente. Para o servidor OAuth ser seguro o aplicativo **devem** fornecer uma instância de `AuthorizationCodeProvider` em que o token produzido pelo `OnCreate/OnCreateAsync` evento é considerado válido para apenas uma chamada para `OnReceive/OnReceiveAsync`.
- `RefreshTokenProvider`: Produz um token de atualização que pode ser usado para produzir um novo token de acesso quando necessário. Se não fornecido o servidor de autorização não retornará tokens de atualização da `/Token` ponto de extremidade.

## <a name="account-management"></a>Gerenciamento de conta

OAuth não importa onde ou como você gerencia suas informações de conta de usuário. Ele tem [ASP.NET Identity](../../../identity/index.md) que é responsável por ele. Neste tutorial, estamos simplificará o código de gerenciamento de conta e apenas certifique-se de que o usuário pode fazer logon usando o cookie de middleware OWIN. Aqui está o código de exemplo simplificado a `AccountController`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample3.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample4.cs?highlight=1)]

`ValidateClientRedirectUri` é usado para validar o cliente com a sua URL de redirecionamento registrado. `ValidateClientAuthentication` verifica o cabeçalho de esquema básico e o corpo do formulário para obter as credenciais do cliente.

A página de logon é mostrada abaixo:

![](owin-oauth-20-authorization-server/_static/image1.png)

Examine OAuth 2 da IETF [concessão de código de autorização](http://tools.ietf.org/html/rfc6749#section-4.1) seção agora.

**Provedor** (na tabela a seguir) é [OAuthAuthorizationServerOptions](https://msdn.microsoft.com/library/microsoft.owin.security.oauth.oauthauthorizationserveroptions(v=vs.111).aspx). Provedor, que é do tipo `OAuthAuthorizationServerProvider`, que contém todos os eventos de servidor OAuth.

| Fluxo de etapas da seção de concessão de código de autorização | Download de exemplo executa essas etapas com: |
| --- | --- |
|  |  |
| (A) o cliente inicia o fluxo, direcionando agente do proprietário do recurso do usuário para o ponto de extremidade de autorização. O cliente inclui seu identificador de cliente, escopo solicitado, estado local e um URI de redirecionamento para o qual o servidor de autorização enviará o agente do usuário novamente quando o acesso é concedido (ou negado). | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| (B) o servidor de autorização autentica o proprietário do recurso (por meio do agente do usuário) e estabelece se o proprietário do recurso concede ou nega a solicitação de acesso do cliente. | **&lt;Se o usuário concede acesso&gt;**  Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| (C) supondo que o proprietário do recurso concede acesso, o servidor de autorização redireciona o agente do usuário para o cliente usando o URI de redirecionamento fornecido anteriormente (na solicitação ou durante o registro do cliente). ... |  |
|  |  |
| (D) o cliente solicita um token de acesso do ponto de extremidade do servidor de autorização, incluindo o código de autorização recebido na etapa anterior. Ao fazer a solicitação, o cliente autentica com o servidor de autorização. O cliente inclui o URI de redirecionamento usado para obter o código de autorização para verificação. | Provider.MatchEndpoint Provider.ValidateClientAuthentication AuthorizationCodeProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantAuthorizationCode Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |

Uma implementação de exemplo para `AuthorizationCodeProvider.CreateAsync` e `ReceiveAsync` controlar a criação e validação de código de autorização é mostrado abaixo.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample5.cs)]

O código acima usa um dicionário simultâneo de na memória para armazenar o tíquete de identidade e de código e restaurar a identidade depois de receber o código. Em um aplicativo real, ele seria substituído por um repositório de dados persistentes. É o ponto de extremidade de autorização do proprietário do recurso conceder acesso ao cliente. Normalmente, ele precisa de uma interface do usuário para permitir que o usuário clicar em um botão e confirmar a concessão. Middleware OWIN OAuth permite que o código do aplicativo lidar com o ponto de extremidade de autorização. Em nosso aplicativo de exemplo, usamos um controlador MVC chamado `OAuthController` manipulá-lo. Aqui está a implementação de exemplo:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample6.cs?highlight=15)]

O `Authorize` ação verificará primeiro se o usuário tiver conectado ao servidor de autorização. Caso contrário, o middleware de autenticação desafia o chamador a se autenticar usando o cookie "Aplicativo" e redireciona para a página de logon. (Consulte o código realçado acima). Se o usuário tiver efetuado logon, ele processará a exibição Authorize, conforme mostrado abaixo:

![](owin-oauth-20-authorization-server/_static/image2.png)

Se o **Grant** botão for selecionado, o `Authorize` ação criará uma nova identidade de "Portador" e entrar com ela. Ele irá disparar o servidor de autorização para gerar um token de portador e enviará de volta para o cliente com a carga JSON.

### <a name="implicit-grant"></a>Concessão implícita

Consulte a OAuth 2 da IETF [concessão implícita](http://tools.ietf.org/html/rfc6749#section-4.2) seção agora.

 O [concessão implícita](http://tools.ietf.org/html/rfc6749#section-4.2) fluxo mostrado na Figura 4 é o fluxo e middleware OWIN OAuth de mapeamento que se segue.

| Fluxo de etapas da seção de concessão implícita | Download de exemplo executa essas etapas com: |
| --- | --- |
|  |  |
| (A) o cliente inicia o fluxo, direcionando agente do proprietário do recurso do usuário para o ponto de extremidade de autorização. O cliente inclui seu identificador de cliente, escopo solicitado, estado local e um URI de redirecionamento para o qual o servidor de autorização enviará o agente do usuário novamente quando o acesso é concedido (ou negado). | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| (B) o servidor de autorização autentica o proprietário do recurso (por meio do agente do usuário) e estabelece se o proprietário do recurso concede ou nega a solicitação de acesso do cliente. | **&lt;Se o usuário concede acesso&gt;**  Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| (C) supondo que o proprietário do recurso concede acesso, o servidor de autorização redireciona o agente do usuário para o cliente usando o URI de redirecionamento fornecido anteriormente (na solicitação ou durante o registro do cliente). ... |  |
|  |  |
| (D) o cliente solicita um token de acesso do ponto de extremidade do servidor de autorização, incluindo o código de autorização recebido na etapa anterior. Ao fazer a solicitação, o cliente autentica com o servidor de autorização. O cliente inclui o URI de redirecionamento usado para obter o código de autorização para verificação. |  |

Uma vez que já implementamos o ponto de extremidade de autorização (`OAuthController.Authorize` ação) para concessão de código de autorização, ele habilita automaticamente também o fluxo implícito. Observação: `Provider.ValidateClientRedirectUri` é usado para validar a ID do cliente com a URL de redirecionamento, que protege o fluxo de concessão implícita de enviar o acesso de clientes de token para mal-intencionados ([ataque Man-in-the-middle](https://www.owasp.org/index.php/Man-in-the-middle_attack)).

### <a name="resource-owner-password-credentials-grant"></a>Concessão de credenciais de senha do proprietário do recurso

Consulte a OAuth 2 da IETF [concessão de credenciais de senha de proprietário do recurso](http://tools.ietf.org/html/rfc6749#section-4.3) seção agora.

 O [concessão de credenciais de senha de proprietário do recurso](http://tools.ietf.org/html/rfc6749#section-4.3) fluxo mostrado na Figura 5 é o fluxo e middleware OWIN OAuth de mapeamento que se segue.

| Fluxo de etapas da seção de concessão de credenciais de senha de proprietário do recurso | Download de exemplo executa essas etapas com: |
| --- | --- |
|  |  |
| (A) o proprietário do recurso fornece ao cliente seu nome de usuário e senha. |  |
|  |  |
| (B) o cliente solicita um token de acesso do ponto de extremidade do servidor de autorização, incluindo as credenciais recebidas do proprietário do recurso. Ao fazer a solicitação, o cliente autentica com o servidor de autorização. | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantResourceOwnerCredentials Provider.TokenEndpoint AccessToken Provider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (C) o servidor de autorização autentica o cliente e valida as credenciais do proprietário de recurso e, se for válido, emite um token de acesso. |  |

Aqui está a implementação de exemplo para `Provider.GrantResourceOwnerCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample7.cs)]

> [!NOTE]
> O código acima se destina a explicar essa seção do tutorial e não deve ser usado em seguro ou aplicativos de produção. Ele não verifica as credenciais de proprietários de recurso. Ele pressupõe que cada credencial é válida e cria uma nova identidade para ele. A nova identidade será usada para gerar o token de acesso e token de atualização. Substitua o código com seu próprio código de gerenciamento de conta segura.


### <a name="client-credentials-grant"></a>Concessão de credenciais do cliente

Consulte a OAuth 2 da IETF [concessão de credenciais de cliente](http://tools.ietf.org/html/rfc6749#section-4.4) seção agora.

 O [concessão de credenciais de cliente](http://tools.ietf.org/html/rfc6749#section-4.4) fluxo mostrado na Figura 6 é o fluxo e middleware OWIN OAuth de mapeamento que se segue.

| Fluxo de etapas da seção de concessão de credenciais de cliente | Download de exemplo executa essas etapas com: |
| --- | --- |
|  |  |
| (A) o cliente autentica com o servidor de autorização e solicita um token de acesso do ponto de extremidade token. | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantClientCredentials Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (B) o servidor de autorização autentica o cliente e se for válido, emite um token de acesso. |  |

Aqui está a implementação de exemplo para `Provider.GrantClientCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample8.cs)]

> [!NOTE]
> O código acima se destina a explicar essa seção do tutorial e não deve ser usado em seguro ou aplicativos de produção. Substitua o código com seu próprio código de gerenciamento de cliente segura.


### <a name="refresh-token"></a>Token de atualização

Consulte a OAuth 2 da IETF [Refresh Token](http://tools.ietf.org/html/rfc6749#section-1.5) seção agora.

 O [Refresh Token](http://tools.ietf.org/html/rfc6749#section-1.5) fluxo mostrado na Figura 2 é o fluxo e middleware OWIN OAuth de mapeamento que se segue.

| Fluxo de etapas da seção de concessão de credenciais de cliente | Download de exemplo executa essas etapas com: |
| --- | --- |
|  |  |
| (G) o cliente solicita um novo token de acesso de autenticação com o servidor de autorização e apresentando o token de atualização. Os requisitos de autenticação de cliente são baseados no tipo de cliente e nas políticas de servidor de autorização. | Provider.MatchEndpoint Provider.ValidateClientAuthentication RefreshTokenProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantRefreshToken Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (H) o servidor de autorização autentica o cliente e valida o token de atualização e, se for válido, emite um novo token de acesso (e, opcionalmente, um novo token de atualização). |  |

Aqui está a implementação de exemplo para `Provider.GrantRefreshToken`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample9.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample10.cs)]

## <a name="create-a-resource-server-which-is-protected-by-access-token"></a>Criar um servidor de recurso que é protegido por um Token de acesso

Crie um projeto de aplicativo web vazio e instale pacotes do projeto a seguir:

- Microsoft.AspNet.WebApi.Owin
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth

Crie uma classe de inicialização e configurar a autenticação e a API da Web. Ver *AuthorizationServer\ResourceServer\Startup.cs* no download de exemplo.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample11.cs)]

Ver *AuthorizationServer\ResourceServer\App\_Start\Startup.Auth.cs* no download de exemplo.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample12.cs)]

Ver *AuthorizationServer\ResourceServer\App\_Start\Startup.WebApi.cs* no download de exemplo.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample13.cs)]

- `UseCors` método permite que o CORS para todos os domínios.
- `UseOAuthBearerAuthentication` método permite que o middleware de autenticação de token de portador OAuth que receberá e validar o token de portador no cabeçalho de autorização na solicitação.
- `Config.SuppressDefaultHostAuthenticaiton` Suprime o padrão principal autenticado do aplicativo de host, portanto, todas as solicitações serão ser anônimas após esta chamada.
- `HostAuthenticationFilter` Habilita a autenticação apenas para o tipo de autenticação especificado. Nesse caso, ele é o tipo de autenticação do portador.

Para demonstrar a identidade autenticada, criamos um ApiController para gerar a saída de declarações do usuário atual.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample14.cs)]

Se o servidor de autorização e o servidor de recursos não estiverem no mesmo computador, o middleware OAuth usará as chaves do computador diferentes para criptografar e descriptografar o token de acesso de portador. Para compartilhar a mesma chave privada entre os dois projetos, podemos adicionar os mesmos `machinekey` definindo em ambos *Web. config* arquivos.

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample15.xml?highlight=8-10)]

## <a name="create-oauth-20-clients"></a>Criar clientes OAuth 2.0

 Podemos usar o [DotNetOpenAuth.OAuth2.Client](http://www.nuget.org/packages/DotNetOpenAuth.OAuth2.Client) pacote do NuGet para simplificar o código do cliente.

### <a name="authorization-code-grant-client"></a>Cliente de concessão de código de autorização

 Esse cliente é um aplicativo MVC. Ele vai disparar um fluxo de concessão de código de autorização para obter o acesso de token de back-end. Ele tem uma única página, conforme mostrado abaixo:

![](owin-oauth-20-authorization-server/_static/image3.png)

- O **autorizar** botão redirecionará o navegador para o servidor de autorização para notificar o proprietário do recurso conceder acesso a esse cliente.
- O **Refresh** botão obterá um novo token de acesso e token de atualização usando a atualização atual token.
- O **API de recursos protegidos do acesso** botão chamará o servidor do recurso para obter dados de declarações do usuário atual e mostrá-los na página.

Aqui está o código de exemplo do `HomeController` do cliente.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample16.cs)]

`DotNetOpenAuth` requer SSL por padrão. Como nossa demonstração está usando HTTP, você precisa adicionar a configuração no arquivo de configuração a seguir:

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample17.xml?highlight=4-6)]

> [!WARNING]
> Security - nunca desabilitar SSL em um aplicativo de produção. Suas credenciais de logon agora estão sendo enviadas em texto não criptografado na transmissão. O código acima é apenas para depuração de exemplo locais e exploração.


### <a name="implicit-grant-client"></a>Cliente de concessão implícita

Esse cliente está usando o JavaScript para:

1. Abra uma nova janela e redirecionar para o ponto de extremidade de autorização do servidor de autorização.
2. Obter o token de acesso de URL fragmentos quando ele redireciona de volta.

A imagem a seguir mostra esse processo:

![](owin-oauth-20-authorization-server/_static/image4.png)

O cliente deve ter duas páginas: uma para a home page e outro para o retorno de chamada. Aqui está o exemplo de JavaScript de código encontrado na *index. cshtml* arquivo:

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample18.cshtml)]

Aqui está o retorno de chamada código de tratamento *SignIn.cshtml* arquivo:

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample19.cshtml)]

> [!NOTE]
> Uma prática recomendada é mover o JavaScript para um arquivo externo e não inseri-la com a marcação do Razor. Para manter esse exemplo simples, terem sido combinadas.


### <a name="resource-owner-password-credentials-grant-client"></a>Cliente de concessão de credenciais de senha do proprietário do recurso

Podemos usa um aplicativo de console para esse cliente de demonstração. Aqui está o código:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample20.cs)]

### <a name="client-credentials-grant-client"></a>Cliente de concessão de credenciais de cliente

Assim como a concessão de credenciais de senha de proprietário do recurso, este é um código de aplicativo de console:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample21.cs)]
