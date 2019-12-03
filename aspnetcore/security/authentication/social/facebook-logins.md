---
title: Configuração de logon externo do Facebook no ASP.NET Core
author: rick-anderson
description: Tutorial com exemplos de código que demonstram a integração da autenticação de usuário da conta do Facebook em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 12/02/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2e4cc04c6e7ff8e5f5701cc7f9ede73dbc1b4685
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717070"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Configuração de logon externo do Facebook no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial com exemplos de código mostra como permitir que os usuários entrem com sua conta do Facebook usando um projeto de exemplo ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index). Começamos criando uma ID de aplicativo do Facebook seguindo as [etapas oficiais](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Criar o aplicativo no Facebook

* Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) ao projeto.

* Navegue até a página do [aplicativo de desenvolvedores do Facebook](https://developers.facebook.com/apps/) e entre. Se você ainda não tiver uma conta do Facebook, use o link **inscrever-** se no Facebook na página de logon para criar uma.  Depois de ter uma conta do Facebook, siga as instruções para se registrar como um desenvolvedor do Facebook.

* No menu **meus aplicativos** , selecione **criar aplicativo** para criar uma nova ID de aplicativo.

   ![Portal do Facebook for Developers aberto no Microsoft Edge](index/_static/FBMyApps.png)

* Preencha o formulário e toque no botão **criar ID do aplicativo** .

  ![Criar um novo formulário de ID do aplicativo](index/_static/FBNewAppId.png)

* Na nova placa de aplicativo, selecione **Adicionar um produto**.  No cartão de **logon do Facebook** , clique em **Configurar** 

  ![Página configuração do produto](index/_static/FBProductSetup.png)

* O assistente de **início rápido** é iniciado com **escolher uma plataforma** como a primeira página. Ignore o assistente por enquanto clicando no link **configurações** de logon do Facebook no menu na parte inferior esquerda:

  ![Ignorar Início Rápido](index/_static/FBSkipQuickStart.png)

* Você verá a página **configurações de OAuth do cliente** :

  ![Página de configurações do OAuth do cliente](index/_static/FBOAuthSetup.png)

* Insira seu URI de desenvolvimento com */SignIn-Facebook* anexados ao campo **URIs de redirecionamento OAuth válido** (por exemplo: `https://localhost:44320/signin-facebook`). A autenticação do Facebook configurada posteriormente neste tutorial tratará automaticamente as solicitações na rota */SignIn-Facebook* para implementar o fluxo OAuth.

> [!NOTE]
> O URI */SignIn-Facebook* é definido como o retorno de chamada padrão do provedor de autenticação do Facebook. Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Facebook por meio da propriedade [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) herdada da classe [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .

* Clique em **salvar alterações**.

* Clique em **configurações** > link **básico** no painel de navegação esquerdo.

  Nessa página, anote sua `App ID` e sua `App Secret`. Você adicionará ambos ao seu aplicativo ASP.NET Core na próxima seção:

* Ao implantar o site, você precisa revisitar a página de configuração de **logon do Facebook** e registrar um novo URI público.

## <a name="store-facebook-app-id-and-app-secret"></a>Armazenar ID do aplicativo do Facebook e segredo do aplicativo

Vincule configurações confidenciais como o Facebook `App ID` e `App Secret` à sua configuração de aplicativo usando o [Gerenciador de segredo](xref:security/app-secrets). Para os fins deste tutorial, nomeie os tokens `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Execute os comandos a seguir para armazenar com segurança `App ID` e `App Secret` usando o Gerenciador de segredo:

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Configurar a autenticação do Facebook

Adicione o serviço do Facebook no método `ConfigureServices` no arquivo *Startup.cs* :

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Consulte a referência da API do [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obter mais informações sobre as opções de configuração com suporte na autenticação do Facebook. As opções de configuração podem ser usadas para:

* Solicite informações diferentes sobre o usuário.
* Adicione argumentos de cadeia de caracteres de consulta para personalizar a experiência de logon.

## <a name="sign-in-with-facebook"></a>Entrar com o Facebook

Execute o aplicativo e clique em **fazer logon**. Você verá uma opção para entrar com o Facebook.

![Aplicativo Web: usuário não autenticado](index/_static/DoneFacebook.png)

Ao clicar em **Facebook**, você será redirecionado para o Facebook para autenticação:

![Página de autenticação do Facebook](index/_static/FBLogin.png)

A autenticação do Facebook solicita o perfil público e o endereço de email por padrão:

![Tela de consentimento da página de autenticação do Facebook](index/_static/FBLoginDone.png)

Depois de inserir suas credenciais do Facebook, você será Redirecionado de volta para o site onde você pode definir seu email.

Agora você está conectado usando suas credenciais do Facebook:

![Aplicativo Web: usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* **Somente ASP.NET Core 2. x:** Se a identidade não estiver configurada chamando `services.AddIdentity` no `ConfigureServices`, tentar autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso seja feito.
* Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* . Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Este artigo mostrou como você pode autenticar com o Facebook. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site no aplicativo Web do Azure, você deve redefinir o `AppSecret` no portal do desenvolvedor do Facebook.

* Defina o `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` como configurações de aplicativo no portal do Azure. O sistema de configuração é configurado para ler chaves de variáveis de ambiente.
