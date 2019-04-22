---
title: Configuração de logon externo do Google no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Google em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 44c79b3279db7946b6d89a726bd3f5acfb5f51af
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705585"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Configuração de logon externo do Google no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

[As APIs Google + herdadas foram desligadas a partir de 7 de março de 2019](https://developers.google.com/+/api-shutdown). Google + entrar e os desenvolvedores devem mover para um novo logon do Google no sistema. Os pacotes do ASP.NET Core 2.1 e 2.2 para autenticação do Google atualizou para acomodar as alterações. Para obter mais informações e atenuações temporárias para o ASP.NET Core, consulte [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/6486). Este tutorial foi atualizado com o novo processo de instalação.

Este tutorial mostra como habilitar usuários entrar com sua conta do Google usando um projeto ASP.NET Core 2.2 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Criar uma ID de cliente e o projeto de Console de API do Google

* Navegue até [a integração do Google Sign-In em seu aplicativo web](https://developers.google.com/identity/sign-in/web/devconsole-project) e selecione **configurar um projeto**.
* No **configurar o cliente do OAuth** caixa de diálogo, selecione **servidor Web**.
* No **URIs de redirecionamento autorizados** caixa de entrada de texto, defina o URI de redirecionamento. Por exemplo, `https://localhost:5001/signin-google`
* Salvar a **ID do cliente** e **segredo do cliente**.
* Ao implantar o site, registrar a nova url pública do **Console do Google**.

## <a name="store-google-clientid-and-clientsecret"></a>Store Google ClientID e ClientSecret

Store as configurações confidenciais, como o Google `Client ID` e `Client Secret` com o [Secret Manager](xref:security/app-secrets). Para os fins deste tutorial, nomeie os tokens `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret`:

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Você pode gerenciar suas credenciais de API e o uso na [Console de API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Configurar a autenticação do Google

Adicionar o serviço do Google para `Startup.ConfigureServices`.

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Entrar com o Google

* Execute o aplicativo e clique em **faça logon no**. Aparece uma opção para entrar com o Google.
* Clique o **Google** botão, que redireciona para o Google para autenticação.
* Depois de inserir suas credenciais do Google, você será redirecionado para o site da web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação do Google. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="change-the-default-callback-uri"></a>Alterar o retorno de chamada padrão URI

O segmento URI `/signin-google` é definido como o retorno de chamada padrão do provedor de autenticação do Google. Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Google via o herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) classe.

## <a name="troubleshooting"></a>Solução de problemas

* Se a entrada não funciona e você não estiver recebendo erros, alterne para modo de desenvolvimento para que o problema mais fácil de depurar.
* Se a identidade não estiver configurada, chamando `services.AddIdentity` na `ConfigureServices`, tentar autenticar resulta em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso é feito.
* Se o banco de dados do site não foi criado por meio da aplicação a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Google. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).
* Depois que você publica o aplicativo no Azure, redefinir o `ClientSecret` no Console de API do Google.
* Defina as `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.
