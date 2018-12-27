---
title: Configuração de logon externo do Twitter com o ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/twitter-logins
ms.openlocfilehash: 49db8b921fde169380ca284f46e535786b2b8a30
ms.sourcegitcommit: 3e94d192b2ed9409fe72e3735e158b333354964c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735798"
---
# <a name="twitter-external-login-setup-with-aspnet-core"></a>Configuração de logon externo do Twitter com o ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial mostra como permitir que os usuários [entrar com sua conta do Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando um projeto do ASP.NET Core 2.0 de exemplo criado sobre o [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Criar o aplicativo no Twitter

* Navegue até [ https://apps.twitter.com/ ](https://apps.twitter.com/) e entre. Se você ainda não tiver uma conta do Twitter, use o **[Inscreva-se agora](https://twitter.com/signup)** link para criar um. Depois de entrar, o **gerenciamento de aplicativos** página é mostrada:

  ![Gerenciamento de aplicativo do Twitter aberto no Microsoft Edge](index/_static/TwitterAppManage.png)

* Toque **criar novo aplicativo** e preencha o requerimento **nome**, **descrição** e pública **site** URI (Isso pode ser temporário até que você Registre o nome de domínio):

  ![Criar uma página de aplicativo](index/_static/TwitterCreate.png)

* Insira o URI de desenvolvimento com `/signin-twitter` acrescentados em de **URIs de redirecionamento de OAuth válido** campo (por exemplo: `https://localhost:44320/signin-twitter`). O esquema de autenticação do Twitter configurado mais tarde neste tutorial automaticamente manipulará as solicitações em `/signin-twitter` rota para implementar o fluxo de OAuth.

  > [!NOTE]
  > O segmento URI `/signin-twitter` é definido como o retorno de chamada padrão do provedor de autenticação do Twitter. Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Twitter por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) classe.

* Preencha o restante do formulário e toque **criar seu aplicativo Twitter**. Novos detalhes do aplicativo são exibidos:

  ![Guia Detalhes na página de aplicativo](index/_static/TwitterAppDetails.png)

* Ao implantar o site será necessário rever a **gerenciamento de aplicativos** página e registrar um novo URI público.

## <a name="storing-twitter-consumerkey-and-consumersecret"></a>Armazenar Twitter ConsumerKey e ConsumerSecret

Vincular as configurações confidenciais, como o Twitter `Consumer Key` e `Consumer Secret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets). Para os fins deste tutorial, nomeie os tokens `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret`.

Esses tokens podem ser encontrados na **chaves e Tokens de acesso** guia depois de criar seu novo aplicativo do Twitter:

![Guia chaves e Tokens de acesso](index/_static/TwitterKeys.png)

## <a name="configure-twitter-authentication"></a>Configurar a autenticação do Twitter

O modelo de projeto usado neste tutorial garante que [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) pacote já está instalado.

* Para instalar este pacote com o Visual Studio 2017, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.
* Para instalar o .NET Core CLI, execute o seguinte comando no diretório do projeto:

  `dotnet add package Microsoft.AspNetCore.Authentication.Twitter`

::: moniker range=">= aspnetcore-2.0"

Adicione o serviço do Twitter na `ConfigureServices` método no *Startup.cs* arquivo:

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddTwitter(twitterOptions =>
{
    twitterOptions.ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"];
    twitterOptions.ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Adicione o middleware do Twitter na `Configure` método no *Startup.cs* arquivo:

```csharp
app.UseTwitterAuthentication(new TwitterOptions()
{
    ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"],
    ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"]
});
```

::: moniker-end

Consulte a [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação do Twitter. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-twitter"></a>Entrar com o Twitter

Executar o aplicativo e clique em **faça logon no**. Será exibida uma opção para entrar com o Twitter:

![Aplicativo da Web: Usuário não autenticado](index/_static/DoneTwitter.png)

Clicar em **Twitter** redireciona para o Twitter para autenticação:

![Página de autenticação do Twitter](index/_static/TwitterLogin.png)

Depois de inserir suas credenciais do Twitter, você será redirecionado para o site da web onde você pode definir seu email.

Agora você está conectado usando suas credenciais do Twitter:

![Aplicativo da Web: Usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* **ASP.NET Core 2.x somente:** Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso é feito.
* Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Twitter. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site da web para aplicativo web do Azure, você deve redefinir o `ConsumerSecret` no portal do desenvolvedor do Twitter.

* Defina as `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.
