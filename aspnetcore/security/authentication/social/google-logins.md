---
title: Configuração de logon externo do Google no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Google em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082482"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Configuração de logon externo do Google no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

[As APIs do Google + herdadas foram desligadas a partir de 7 de março de 2019](https://developers.google.com/+/api-shutdown). Entre no Google + e os desenvolvedores devem mudar para um novo sistema de entrada do Google. Os pacotes ASP.NET Core 2,1 e 2,2 para a autenticação do Google foram atualizados para acomodar as alterações. Para obter mais informações e atenuações temporárias para ASP.NET Core, consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore/issues/6486). Este tutorial foi atualizado com o novo processo de instalação.

Este tutorial mostra como permitir que os usuários entrem com sua conta do Google usando o projeto ASP.NET Core 2,2 criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Criar um projeto do console do Google API e uma ID do cliente

* Navegue até [integrando o logon do Google ao seu aplicativo Web](https://developers.google.com/identity/sign-in/web/devconsole-project) e selecione **configurar um projeto**.
* Na caixa de diálogo **configurar seu cliente OAuth** , selecione **servidor Web**.
* Na caixa de entrada de texto **URIs de redirecionamento autorizados** , defina o URI de redirecionamento. Por exemplo, `https://localhost:5001/signin-google`
* Salve a **ID do cliente** e o **segredo do cliente**.
* Ao implantar o site, registre a nova URL pública do console do **Google**.

## <a name="store-google-clientid-and-clientsecret"></a>Store Google ClientID e ClientSecret

Armazene configurações confidenciais, como o `Client ID` Google `Client Secret` e com o [Gerenciador de segredo](xref:security/app-secrets). Para os fins deste tutorial, nomeie os tokens `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret`:

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Você pode gerenciar suas credenciais de API e uso no [console de API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Configurar a autenticação do Google

Adicione o serviço do Google `Startup.ConfigureServices`a:

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Entrar com o Google

* Execute o aplicativo e clique em **fazer logon**. É exibida uma opção para entrar com o Google.
* Clique no botão **Google** , que redireciona para o Google para autenticação.
* Depois de inserir suas credenciais do Google, você será Redirecionado de volta para o site da Web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> referência da API para obter mais informações sobre as opções de configuração com suporte pela autenticação do Google. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="change-the-default-callback-uri"></a>Alterar o URI de retorno de chamada padrão

O segmento URI `/signin-google` é definido como o retorno de chamada padrão do provedor de autenticação do Google. Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Google via o herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) classe.

## <a name="troubleshooting"></a>Solução de problemas

* Se a entrada não funcionar e você não estiver recebendo erros, mude para o modo de desenvolvimento para facilitar a depuração do problema.
* Se a identidade não estiver configurada chamando `services.AddIdentity` em `ConfigureServices`, tentar autenticar *os resultados em ArgumentException: A opção ' SignInScheme ' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso é feito.
* Se o banco de dados do site não foi criado por meio da aplicação a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Selecione **aplicar migrações** para criar o banco de dados e atualize a página para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Google. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).
* Depois de publicar o aplicativo no Azure, redefina `ClientSecret` o no console de API do Google.
* Defina as `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.
