---
title: Configuração de logon externo Account da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta da Microsoft em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 1733d049d6752c24d7749b5b5ae2a4b866492358
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58751000"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Configuração de logon externo Account da Microsoft com o ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial mostra como permitir que seus usuários entrar com sua conta da Microsoft usando um projeto do ASP.NET Core 2.0 de exemplo criado na [página anterior](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Criar o aplicativo no Portal do desenvolvedor da Microsoft

* Navegue até [ https://apps.dev.microsoft.com ](https://apps.dev.microsoft.com) e criar ou entrar em uma conta da Microsoft:

![Caixa de diálogo entrar](index/_static/MicrosoftDevLogin.png)

Se você ainda não tiver uma conta da Microsoft, toque em  **[crie uma!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)** Depois de entrar, você será redirecionado para **meus aplicativos** página:

![Portal do desenvolvedor Microsoft aberto no Microsoft Edge](index/_static/MicrosoftDev.png)

* Toque **adicionar um aplicativo** no canto superior direito de canto e insira seu **nome do aplicativo** e **Contact Email**:

![Caixa de diálogo Nova registro de aplicativo](index/_static/MicrosoftDevAppCreate.png)

* Para os fins deste tutorial, desmarque a **instalação guiada** caixa de seleção.

* Toque **Create** para continuar para o **registro** página. Fornecer um **nome** e observe o valor da **Id do aplicativo**, que você usar como `ClientId` posteriormente no tutorial:

![Página de registro](index/_static/MicrosoftDevAppReg.png)

* Toque **Adicionar plataforma** na **plataformas** seção e selecione o **Web** plataforma:

![Adicionar caixa de diálogo de plataforma](index/_static/MicrosoftDevAppPlatform.png)

* No novo **Web** plataforma, digite sua URL de desenvolvimento com `/signin-microsoft` acrescentado para o **URLs de redirecionamento** campo (por exemplo: `https://localhost:44320/signin-microsoft`). O esquema de autenticação da Microsoft configurado mais tarde neste tutorial automaticamente manipulará as solicitações em `/signin-microsoft` rota para implementar o fluxo de OAuth:

![Seção de plataforma da Web](index/_static/MicrosoftRedirectUri.png)

> [!NOTE]
> O segmento URI `/signin-microsoft` é definido como o retorno de chamada padrão do provedor de autenticação do Microsoft. Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação da Microsoft por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) classe.

* Toque **Adicionar URL** para garantir que a URL foi adicionada.

* Preencha quaisquer outras configurações de aplicativo, se necessário e toque em **salvar** na parte inferior da página para salvar as alterações à configuração de aplicativo.

* Ao implantar o site será necessário rever a **registro** página e defina uma nova URL pública.

## <a name="store-microsoft-application-id-and-password"></a>Id do aplicativo da Microsoft e a senha de Store

* Observe a `Application Id` exibido na **registro** página.

* Toque **gerar nova senha** na **segredos do aplicativo** seção. Isso exibe uma caixa em que você pode copiar a senha de aplicativo:

![Caixa de diálogo Nova senha gerada](index/_static/MicrosoftDevPassword.png)

Vincular as configurações confidenciais, como a Microsoft `Application ID` e `Password` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets). Para os fins deste tutorial, nomeie os tokens `Authentication:Microsoft:ApplicationId` e `Authentication:Microsoft:Password`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Configurar a autenticação de conta da Microsoft

O modelo de projeto usado neste tutorial garante que [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) pacote já está instalado.

* Para instalar este pacote com o Visual Studio 2017, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.
* Para instalar o .NET Core CLI, execute o seguinte comando no diretório do projeto:

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

::: moniker range=">= aspnetcore-2.0"

Adicione o serviço Microsoft Account na `ConfigureServices` método no *Startup.cs* arquivo:

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Adicione o middleware Account da Microsoft na `Configure` método no *Startup.cs* arquivo:

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

::: moniker-end

Embora a terminologia usada no Portal do desenvolvedor Microsoft nomeia esses tokens `ApplicationId` e `Password`, elas são expostas como `ClientId` e `ClientSecret` para a API de configuração.

Consulte a [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação Account da Microsoft. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-microsoft-account"></a>Entrar com conta da Microsoft

Executar o aplicativo e clique em **faça logon no**. Uma opção para entrar com a Microsoft será exibida:

![Log de aplicativo na página de Web: Usuário não autenticado](index/_static/DoneMicrosoft.png)

Quando você clica na Microsoft, você será redirecionado para a Microsoft para autenticação. Após entrar com sua Account da Microsoft (se ainda não estiver conectado), você será solicitado para permitir que o aplicativo acessar suas informações:

![Caixa de diálogo de autenticação Microsoft](index/_static/MicrosoftLogin.png)

Toque **Sim** e você será redirecionado para o site da web onde você pode definir seu email.

Agora você está conectado usando suas credenciais da Microsoft:

![Aplicativo da Web: Usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* Se o provedor Microsoft Account redireciona você para uma página de erro de entrada, observe os erro título e descrição de cadeia de caracteres parâmetros de consulta diretamente após o `#` (hashtag) no Uri.

  Embora pareça a mensagem de erro indicar um problema com a autenticação da Microsoft, a causa mais comum é seu Uri não corresponda a um aplicativo de **URIs de redirecionamento** especificado para o **Web** plataforma .
* **ASP.NET Core 2.x somente:** Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso é feito.
* Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com a Microsoft. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site da web para aplicativo web do Azure, você deve criar um novo `Password` no Portal do desenvolvedor Microsoft.

* Defina as `Authentication:Microsoft:ApplicationId` e `Authentication:Microsoft:Password` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.
