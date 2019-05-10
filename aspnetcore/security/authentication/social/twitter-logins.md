---
title: Configuração de entrada externa com o ASP.NET Core do Twitter
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 5/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 486d58b600ca5326a0728de40bb386fbb9440f67
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516880"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configuração de entrada externa com o ASP.NET Core do Twitter

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este exemplo mostra como permitir que os usuários [entrar com sua conta do Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando um projeto do ASP.NET Core 2.2 de exemplo criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Criar o aplicativo no Twitter

* Navegue até [ https://apps.twitter.com/ ](https://apps.twitter.com/) e entre. Se você ainda não tiver uma conta do Twitter, use o **[Inscreva-se agora](https://twitter.com/signup)** link para criar um.

* Toque **criar novo aplicativo** e preencha o requerimento **nome**, **descrição** e pública **site** URI (Isso pode ser temporário até que você Registre o nome de domínio):

* Insira o URI de desenvolvimento com `/signin-twitter` acrescentados em de **URIs de redirecionamento de OAuth válido** campo (por exemplo: `https://webapp128.azurewebsites.net/signin-twitter`). O esquema de autenticação do Twitter configurado adiante neste exemplo automaticamente manipulará as solicitações em `/signin-twitter` rota para implementar o fluxo de OAuth.

  > [!NOTE]
  > O segmento URI `/signin-twitter` é definido como o retorno de chamada padrão do provedor de autenticação do Twitter. Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Twitter por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) classe.

* Preencha o restante do formulário e toque **criar seu aplicativo Twitter**. Novos detalhes do aplicativo são exibidos:

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Armazenar a chave de API de consumidor do Twitter e o segredo

Execute os seguintes comandos para armazenar com segurança `ClientId` e `ClientSecret` usando [Secret Manager](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

Vincular as configurações confidenciais, como o Twitter `Consumer Key` e `Consumer Secret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets). Para os fins deste exemplo, nomeie os tokens `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret`.

Esses tokens podem ser encontrados na **chaves e Tokens de acesso** guia depois de criar um novo aplicativo do Twitter:

## <a name="configure-twitter-authentication"></a>Configurar a autenticação do Twitter

Adicione o serviço do Twitter na `ConfigureServices` método no *Startup.cs* arquivo:

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação do Twitter. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-twitter"></a>Entrar com o Twitter

Execute o aplicativo e selecione **faça logon no**. Será exibida uma opção para entrar com o Twitter:

Clicar em **Twitter** redireciona para o Twitter para autenticação:

Depois de inserir suas credenciais do Twitter, você será redirecionado para o site da web onde você pode definir seu email.

Agora você está conectado usando suas credenciais do Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* **ASP.NET Core 2.x somente:** Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado neste exemplo garante que isso é feito.
* Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Twitter. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site da web para aplicativo web do Azure, você deve redefinir o `ConsumerSecret` no portal do desenvolvedor do Twitter.

* Defina as `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.