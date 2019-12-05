---
title: Configuração de logon externo da conta da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este exemplo demonstra a integração do conta Microsoft autenticação de usuário em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 12/4/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: ddaae1a25a1dcf167ffae0f24b480e2cde6aca5b
ms.sourcegitcommit: f4cd3828e26e6d549ba8d0c36a17be35ad9e5a51
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74825458"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configuração de logon externo da conta da Microsoft com o ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este exemplo mostra como permitir que os usuários entrem com seus conta Microsoft usando o projeto ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Criar o aplicativo no portal do desenvolvedor da Microsoft

* Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) ao projeto.
* Navegue até a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) e crie ou entre em uma conta Microsoft:

Se você não tiver um conta Microsoft, selecione **criar um**. Depois de entrar, você será redirecionado para a página de **registros de aplicativo** :

* Selecionar **novo registro**
* Insira um **Nome**.
* Selecione uma opção para **tipos de conta com suporte**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* Em **URI de redirecionamento**, insira a URL de desenvolvimento com `/signin-microsoft` acrescentado. Por exemplo, `https://localhost:5001/signin-microsoft`. O esquema de autenticação da Microsoft configurado mais adiante neste exemplo tratará automaticamente as solicitações em `/signin-microsoft` rota para implementar o fluxo OAuth.
* Selecionar **registro**

### <a name="create-client-secret"></a>Criar segredo do cliente

* No painel esquerdo, selecione **certificados & segredos**.
* Em **segredos do cliente**, selecione **novo segredo do cliente**

  * Adicione uma descrição para o segredo do cliente.
  * Selecione o botão **Adicionar**.

* Em **segredos do cliente**, copie o valor do segredo do cliente.

> [!NOTE]
> O segmento de URI `/signin-microsoft` é definido como o retorno de chamada padrão do provedor de autenticação da Microsoft. Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação da Microsoft por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Armazenar a ID do cliente da Microsoft e o segredo do cliente

Execute os comandos a seguir para armazenar com segurança `ClientId` e `ClientSecret` usando o [Gerenciador de segredo](xref:security/app-secrets):

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

Vincule configurações confidenciais, como o Microsoft `ClientId` e `ClientSecret` à sua configuração de aplicativo usando o [Gerenciador de segredo](xref:security/app-secrets). Para os fins deste exemplo, nomeie os tokens `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurar a autenticação da conta da Microsoft

Adicione o serviço de conta da Microsoft ao `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Para obter mais informações sobre as opções de configuração com suporte da autenticação da conta da Microsoft, consulte a referência da API do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) . Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-microsoft-account"></a>Conta de Entrar com a conta da Microsoft

Execute o aplicativo e clique em **fazer logon**. É exibida uma opção para entrar com a Microsoft. Ao clicar em Microsoft, você será redirecionado para a Microsoft para autenticação. Depois de entrar com sua conta da Microsoft, você será solicitado a permitir que o aplicativo acesse suas informações:

Toque em **Sim** e você será Redirecionado de volta para o site da Web onde você pode definir seu email.

Agora você está conectado usando suas credenciais da Microsoft:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* Se o provedor de conta da Microsoft o redireciona para uma página de erro de entrada, observe os parâmetros título e descrição de cadeia de caracteres de consulta diretamente seguindo o `#` (hashtag) no URI.

  Embora a mensagem de erro pareça indicar um problema com a autenticação da Microsoft, a causa mais comum é o URI do aplicativo não corresponder a nenhum dos **URIs de redirecionamento** especificados para a plataforma **da Web** .
* Se a identidade não estiver configurada chamando `services.AddIdentity` no `ConfigureServices`, tentar autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*. O modelo de projeto usado neste exemplo garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Este artigo mostrou como você pode autenticar com a Microsoft. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo Web do Azure, crie novos segredos de cliente no portal do desenvolvedor da Microsoft.

* Defina as `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.
