---
title: Configuração de logon externo do Facebook no ASP.NET Core
author: rick-anderson
description: Tutorial com exemplos de código que demonstra a integração da autenticação de usuário de conta do Facebook em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/04/2019
uid: security/authentication/facebook-logins
ms.openlocfilehash: 84b891f6cee71a26726d49a9d42ae45007d2b429
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893583"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Configuração de logon externo do Facebook no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial com exemplos de código mostra como permitir que seus usuários entrar com sua conta do Facebook usando um projeto do ASP.NET Core 2.0 de exemplo criado na [página anterior](xref:security/authentication/social/index). Vamos começar criando um Facebook App ID seguindo as [etapas oficiais](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Criar o aplicativo no Facebook

* Navegue até a página [aplicativos no site para desenvolvedores do Facebook](https://developers.facebook.com/apps/). Se você ainda não tiver uma conta do Facebook, use o link **Sign up foinscrever-se para o Facebook** na página de logon para criar uma.

* Clique no botão **adicionar um novo aplicativo** no canto superior direito para criar uma nova ID de aplicativo.

   ![Abra o Facebook para o portal de desenvolvedores no Microsoft Edge](index/_static/FBMyApps.png)

* Preencha o formulário e clique no botão **criar ID do aplicativo**.

  ![Criar um formulário de nova ID do aplicativo](index/_static/FBNewAppId.png)

* Na página **selecionar um produto** , clique em **Set Up** no painel **Facebook Login**.

  ![Página de instalação do produto](index/_static/FBProductSetup.png)

* O assistente **Quickstart** iniciará com **escolher uma plataforma** como a primeira página. Ignore o assistente clicando no link **configurações** no menu à esquerda:

  ![Início rápido do Skip](index/_static/FBSkipQuickStart.png)

* Você verá a **configurações do cliente OAuth** página:

  ![Página de configurações do OAuth do cliente](index/_static/FBOAuthSetup.png)

* Insira o URI de desenvolvimento com */signin-facebook* acrescentado para o **URIs de redirecionamento de OAuth válido** campo (por exemplo: `https://localhost:44320/signin-facebook`). A autenticação do Facebook configurada mais tarde neste tutorial automaticamente manipulará as solicitações no */signin-facebook* rota para implementar o fluxo de OAuth.

> [!NOTE]
> O URI */signin-facebook* é definido como o retorno de chamada padrão do provedor de autenticação do Facebook. Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Facebook por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) classe.

* Clique em **salvar alterações**.

* Clique em **as configurações** > **básica** link no painel de navegação à esquerda.

  Nessa página, anote seu `App ID` e seu `App Secret`. Você adicionará os dois em seu aplicativo ASP.NET Core na próxima seção:

* Ao implantar o site, você precisará voltar para a página de configurações **Logon do Facebook** e registrar um novo URI público.

## <a name="store-facebook-app-id-and-app-secret"></a>ID do Facebook App Store e o segredo do aplicativo

Vincular as configurações confidenciais, como o Facebook `App ID` e `App Secret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets). Para os fins deste tutorial, nomeie os tokens `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Execute os seguintes comandos para armazenar com segurança `App ID` e `App Secret` usando o Secret Manager:

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Configurar a autenticação do Facebook

::: moniker range=">= aspnetcore-2.0"

Adicione o serviço do Facebook no método `ConfigureServices` do arquivo *Startup.cs* :

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Instalar o pacote [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook).

* Para instalar este pacote com o Visual Studio 2017, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.
* Para instalar o .NET Core CLI, execute o seguinte comando no diretório do projeto:

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

Adicione o middleware do Facebook no método `Configure` do arquivo *Startup.cs*:

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

Consulte as referências de API do [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obter mais informações sobre as opções de configuração compatíveis com a autenticação do Facebook. As opções de configuração podem ser usadas para:

* Solicitar informações diferentes sobre o usuário.
* Adicionar argumentos de cadeia de caracteres de consulta para personalizar a experiência de logon.

## <a name="sign-in-with-facebook"></a>Entrar com o Facebook

Executar o aplicativo e clique em **faça logon no**. Você verá uma opção para entrar com o Facebook.

![Aplicativo da Web: Usuário não autenticado](index/_static/DoneFacebook.png)

Quando você clica em **Facebook**, você será redirecionado ao Facebook para autenticação:

![Página de autenticação do Facebook](index/_static/FBLogin.png)

Autenticação do Facebook solicita o endereço de email e o perfil público por padrão:

![Tela de consentimento de página de autenticação do Facebook](index/_static/FBLoginDone.png)

Depois que você insira suas credenciais do Facebook, você será redirecionado para seu site em que você pode definir seu email.

Agora você está conectado usando suas credenciais do Facebook:

![Aplicativo da Web: Usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* **ASP.NET Core 2.x somente:** Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso é feito.
* Se o banco de dados do site não foi criado por meio da aplicação a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Adicione a [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) pacote NuGet ao seu projeto para cenários avançados de autenticação de Facebook. Este pacote não é necessário para integrar funcionalidade de logon externo do Facebook com seu aplicativo. 

* Este artigo mostrou como você pode autenticar com o Facebook. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site da web para aplicativo web do Azure, você deve redefinir o `AppSecret` no portal do desenvolvedor do Facebook.

* Defina as `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.
