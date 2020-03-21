---
title: Configuração de entrada externa do Twitter com o ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário da conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: b848486415fd72ce6180b4cf8fc1ba00410d694a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989750"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configuração de entrada externa do Twitter com o ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este exemplo mostra como permitir que os usuários [entrem com sua conta do Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando um exemplo ASP.NET Core projeto 3,0 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Criar o aplicativo no Twitter

* Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) ao projeto.

* Navegue até [https://apps.twitter.com/](https://apps.twitter.com/) e entre. Se você ainda não tiver uma conta do Twitter, use o link **[inscrever-se agora](https://twitter.com/signup)** para criar uma.

* Selecione **Criar um aplicativo**. Preencha o **nome do aplicativo**, a **Descrição do aplicativo** e o URI do **site** público (isso pode ser temporário até que você registre o nome de domínio):

* Marque a caixa ao lado de **Habilitar entrada com o Twitter**

* Microsoft. AspNetCore. Identity exige que os usuários tenham um endereço de email por padrão. Vá para a guia **permissões** , clique no botão **Editar** e marque a caixa ao lado de **solicitar endereço de email dos usuários**.

* Insira seu URI de desenvolvimento com `/signin-twitter` acrescentado ao campo **URLs de retorno de chamada** (por exemplo: `https://webapp128.azurewebsites.net/signin-twitter`). O esquema de autenticação do Twitter configurado mais adiante neste exemplo tratará automaticamente as solicitações em `/signin-twitter` rota para implementar o fluxo OAuth.

  > [!NOTE]
  > O segmento de URI `/signin-twitter` é definido como o retorno de chamada padrão do provedor de autenticação do Twitter. Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Twitter por meio da propriedade [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) herdada da classe [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Preencha o restante do formulário e selecione **criar**. Novos detalhes do aplicativo são exibidos:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Armazenar a chave e o segredo da API do consumidor do Twitter

Armazene configurações confidenciais, como a chave de API do consumidor do Twitter e o segredo com o [Gerenciador de segredo](xref:security/app-secrets). Para este exemplo, use as seguintes etapas:

1. Inicialize o projeto para o armazenamento secreto de acordo com as instruções em [habilitar armazenamento secreto](xref:security/app-secrets#enable-secret-storage).
1. Armazene as configurações confidenciais no repositório de segredo local com as chaves de segredos `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret`:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Esses tokens podem ser encontrados na guia **chaves e tokens de acesso** após a criação de um novo aplicativo do Twitter:

## <a name="configure-twitter-authentication"></a>Configurar a autenticação do Twitter

Adicione o serviço do Twitter no método `ConfigureServices` no arquivo *Startup.cs* :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a referência de API do [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para obter mais informações sobre as opções de configuração com suporte pela autenticação do Twitter. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-twitter"></a>Entrar com o Twitter

Execute o aplicativo e selecione **fazer logon**. É exibida uma opção para entrar com o Twitter:

Clicar no **Twitter** redireciona para o Twitter para autenticação:

Depois de inserir suas credenciais do Twitter, você será Redirecionado de volta para o site da Web onde você pode definir seu email.

Agora você está conectado usando suas credenciais do Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* **Somente ASP.NET Core 2. x:** Se a identidade não estiver configurada chamando `services.AddIdentity` no `ConfigureServices`, tentar autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*. O modelo de projeto usado neste exemplo garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* . Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Este artigo mostrou como você pode autenticar com o Twitter. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo Web do Azure, você deve redefinir o `ConsumerSecret` no portal do desenvolvedor do Twitter.

* Defina o `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.
