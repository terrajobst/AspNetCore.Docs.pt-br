---
title: Configuração de logon externo da conta da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este exemplo demonstra a integração do conta Microsoft autenticação de usuário em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 91ace293fd16cd180b3d5c183c637af6db1d08c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082337"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configuração de logon externo da conta da Microsoft com o ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este exemplo mostra como permitir que os usuários entrem com seus conta Microsoft usando o projeto ASP.NET Core 2,2 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Criar o aplicativo no portal do desenvolvedor da Microsoft

* Navegue até a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) e crie ou entre em uma conta Microsoft:

Se você não tiver um conta Microsoft, selecione **criar um**. Depois de entrar, você será redirecionado para a página de **registros de aplicativo** :

* Selecionar **novo registro**
* Insira um **nome**.
* Selecione uma opção para **tipos de conta com suporte**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* Em **URI de redirecionamento**, insira a `/signin-microsoft` URL de desenvolvimento com anexado. Por exemplo: `https://localhost:44389/signin-microsoft`. O esquema de autenticação da Microsoft configurado mais adiante neste exemplo tratará automaticamente `/signin-microsoft` as solicitações na rota para implementar o fluxo OAuth.
* Selecionar **registro**

### <a name="create-client-secret"></a>Criar segredo do cliente

* No painel esquerdo, selecione **certificados & segredos**.
* Em **segredos do cliente**, selecione **novo segredo do cliente**

  * Adicione uma descrição para o segredo do cliente.
  * Selecione o botão **Adicionar** .

* Em **segredos do cliente**, copie o valor do segredo do cliente.

> [!NOTE]
> O segmento `/signin-microsoft` URI é definido como o retorno de chamada padrão do provedor de autenticação da Microsoft. Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação da Microsoft por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Armazenar a ID do cliente da Microsoft e o segredo do cliente

Execute os seguintes comandos para armazenar `ClientId` e `ClientSecret` usar o Gerenciador de [segredo](xref:security/app-secrets)com segurança:

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

Vincule configurações confidenciais como a `ClientId` Microsoft `ClientSecret` e à sua configuração de aplicativo usando o [Gerenciador de segredo](xref:security/app-secrets). Para os fins deste exemplo, nomeie os tokens `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurar a autenticação da conta da Microsoft

Adicione o serviço de conta da `Startup.ConfigureServices`Microsoft a:

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a referência da API do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) para obter mais informações sobre as opções de configuração com suporte pela autenticação da conta da Microsoft. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-microsoft-account"></a>Conta de Entrar com a conta da Microsoft

Execute o e clique em **fazer logon**. É exibida uma opção para entrar com a Microsoft. Ao clicar em Microsoft, você será redirecionado para a Microsoft para autenticação. Depois de entrar com sua conta da Microsoft (se ainda não tiver entrado), você será solicitado a permitir que o aplicativo acesse suas informações:

Toque em **Sim** e você será Redirecionado de volta para o site da Web onde você pode definir seu email.

Agora você está conectado usando suas credenciais da Microsoft:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* Se o provedor de conta da Microsoft o redireciona para uma página de erro de entrada, observe os parâmetros título e descrição da cadeia de caracteres de `#` consulta diretamente seguindo o (hashtag) no URI.

  Embora a mensagem de erro pareça indicar um problema com a autenticação da Microsoft, a causa mais comum é o URI do aplicativo não corresponder a nenhum dos **URIs de redirecionamento** especificados para a plataforma **da Web** .
* Se a identidade não estiver configurada chamando `services.AddIdentity` em `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção ' SignInScheme ' deve ser fornecida*. O modelo de projeto usado neste exemplo garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com a Microsoft. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo Web do Azure, crie novos segredos de cliente no portal do desenvolvedor da Microsoft.

* Defina as `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.