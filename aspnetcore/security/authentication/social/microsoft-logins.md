---
title: Configuração de logon externo Account da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este exemplo demonstra a integração da autenticação de usuário de conta da Microsoft em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 2c690e5bd8465806d42091616917cfdd747ef8f0
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815569"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configuração de logon externo Account da Microsoft com o ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este exemplo mostra como habilitar usuários entrar com sua conta da Microsoft usando o projeto do ASP.NET Core 2.2 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Criar o aplicativo no Portal do desenvolvedor da Microsoft

* Navegue até a [portal do Azure - registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) página e criar ou entrar em uma conta da Microsoft:

Se você não tiver uma conta da Microsoft, selecione **criar um**. Depois de entrar, você será redirecionado para o **registros de aplicativo** página:

* Selecione **novo registro**
* Insira um **nome**.
* Selecione uma opção para **suporte para tipos de conta**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* Sob **URI de redirecionamento**, insira a URL do desenvolvimento com `/signin-microsoft` acrescentado. Por exemplo, `https://localhost:44389/signin-microsoft`. O esquema de autenticação da Microsoft configurado mais tarde neste exemplo automaticamente manipulará as solicitações em `/signin-microsoft` rota para implementar o fluxo de OAuth.
* Selecione **registrar**

### <a name="create-client-secret"></a>Criar o segredo do cliente

* No painel esquerdo, selecione **certificados e segredos**.
* Sob **segredos de cliente**, selecione **novo segredo do cliente**

  * Adicione uma descrição para o segredo do cliente.
  * Selecione o **adicionar** botão.

* Sob **segredos de cliente**, copie o valor do segredo do cliente.

> [!NOTE]
> O segmento URI `/signin-microsoft` é definido como o retorno de chamada padrão do provedor de autenticação do Microsoft. Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação da Microsoft por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) classe.

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Store o segredo de cliente e a ID de cliente do Microsoft

Execute os seguintes comandos para armazenar com segurança `ClientId` e `ClientSecret` usando [Secret Manager](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

Vincular as configurações confidenciais, como a Microsoft `ClientId` e `ClientSecret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets). Para os fins deste exemplo, nomeie os tokens `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurar a autenticação de conta da Microsoft

Adicionar o serviço da Microsoft Account `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação Account da Microsoft. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-microsoft-account"></a>Entrar com conta da Microsoft

Execute o e clique em **faça logon no**. Uma opção para entrar com a Microsoft é exibida. Quando você clica na Microsoft, você será redirecionado para a Microsoft para autenticação. Após entrar com sua Account da Microsoft (se ainda não estiver conectado), você será solicitado para permitir que o aplicativo acessar suas informações:

Toque **Sim** e você será redirecionado para o site da web onde você pode definir seu email.

Agora você está conectado usando suas credenciais da Microsoft:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* Se o provedor Microsoft Account redireciona você para uma página de erro de entrada, observe os erro título e descrição de cadeia de caracteres parâmetros de consulta diretamente após o `#` (hashtag) no Uri.

  Embora pareça a mensagem de erro indicar um problema com a autenticação da Microsoft, a causa mais comum é seu Uri não corresponda a um aplicativo de **URIs de redirecionamento** especificado para o **Web** plataforma .
* Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado neste exemplo garante que isso é feito.
* Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com a Microsoft. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site da web para aplicativo web do Azure, crie um novo cliente segredos no Portal do desenvolvedor Microsoft.

* Defina as `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.