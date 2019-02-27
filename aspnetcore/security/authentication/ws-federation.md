---
title: Autenticar usuários com o WS-Federation no ASP.NET Core
author: chlowell
description: Este tutorial demonstra como usar o WS-Federation em um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: 7967410686da0e59742b721c0154e143bf19ba01
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833534"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Autenticar usuários com o WS-Federation no ASP.NET Core

Este tutorial demonstra como habilitar usuários entrar com um provedor de autenticação do WS-Federation, como o Active Directory Federation Services (ADFS) ou [Azure Active Directory](/azure/active-directory/) (AAD). Ele usa o aplicativo de exemplo do ASP.NET Core 2.0 descrito em [Facebook, Google e a autenticação de provedor externo](xref:security/authentication/social/index).

Para aplicativos ASP.NET Core 2.0, o suporte do WS-Federation é fornecido pela [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Esse componente foi transferido da [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) e compartilha muitas das mecânica do componente. No entanto, os componentes são diferentes de duas maneiras importantes.

Por padrão, o middleware novo:

* Não permitir logons não solicitados. Esse recurso do protocolo WS-Federation é vulnerável a ataques de XSRF. No entanto, ele pode ser habilitado com o `AllowUnsolicitedLogins` opção.
* Não verifica cada postagem de formulário para mensagens de entrada. Somente as solicitações para o `CallbackPath` são verificados quanto à entrada-ins. `CallbackPath` assume como padrão `/signin-wsfed` mas pode ser alterado por meio de herdado [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade do [ WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) classe. Esse caminho pode ser compartilhado com outros provedores de autenticação, permitindo que o [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) opção.

## <a name="register-the-app-with-active-directory"></a>Registrar o aplicativo com o Active Directory

### <a name="active-directory-federation-services"></a>Serviços de Federação do Active Directory

* Abra o servidor **adicionar da terceira parte confiável Assistente confiança** no console de gerenciamento do AD FS:

![Adicione Assistente de relação de confiança de terceira parte confiável: Bem-vindo](ws-federation/_static/AdfsAddTrust.png)

* Escolha esta opção para inserir os dados manualmente:

![Adicione Assistente de relação de confiança de terceira parte confiável: Selecionar fonte de dados](ws-federation/_static/AdfsSelectDataSource.png)

* Insira um nome de exibição para a terceira parte confiável. O nome não é importante para o aplicativo ASP.NET Core.

* [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) não tem suporte para criptografia de token, portanto, não configure um certificado de criptografia do token:

![Adicione Assistente de relação de confiança de terceira parte confiável: Configurar certificado](ws-federation/_static/AdfsConfigureCert.png)

* Habilite o suporte para o protocolo WS-Federation Passive, usando a URL do aplicativo. Verifique se que a porta está correta para o aplicativo:

![Adicione Assistente de relação de confiança de terceira parte confiável: Configurar a URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Isso deve ser uma URL HTTPS. O IIS Express pode fornecer um certificado autoassinado, ao hospedar o aplicativo durante o desenvolvimento. O kestrel requer configuração manual do certificado. Consulte a [documentação do Kestrel](xref:fundamentals/servers/kestrel) para obter mais detalhes.

* Clique em **próxima** através do restante do assistente e **fechar** no final.

* ASP.NET Core Identity exige que um **ID de nome** de declaração. Adicione um dos **editar regras de declaração** caixa de diálogo:

![Editar regras de declaração](ws-federation/_static/EditClaimRules.png)

* No **Adicionar Assistente de regra de declaração de transformação**, deixe o padrão **enviar atributos LDAP como declarações** modelo selecionado e clique em **próxima**. Adicionar um mapeamento de regra a **SAM-Account-Name** atributo LDAP para o **ID de nome** declaração de saída:

![Adicione Assistente de regra de declaração de transformação: Configurar regra de declaração](ws-federation/_static/AddTransformClaimRule.png)

* Clique em **terminar** > **Okey** no **editar regras de declaração** janela.

### <a name="azure-active-directory"></a>Azure Active Directory

* Navegue até a folha de registros de aplicativo do locatário do AAD. Clique em **novo registro de aplicativo**:

![Azure Active Directory: Registros de aplicativo](ws-federation/_static/AadNewAppRegistration.png)

* Insira um nome para o registro do aplicativo. Isso não é importante para o aplicativo ASP.NET Core.
* Insira a URL que o aplicativo escuta como o **URL de logon**:

![Azure Active Directory: Criar registro de aplicativo](ws-federation/_static/AadCreateAppRegistration.png)

* Clique em **pontos de extremidade** e observe o **documento de metadados de Federação** URL. Esse é o middleware de Web Services Federation `MetadataAddress`:

![Azure Active Directory: Pontos de extremidade](ws-federation/_static/AadFederationMetadataDocument.png)

* Navegue até o novo registro do aplicativo. Clique em **as configurações** > **as propriedades** e anote o **URI da ID do aplicativo**. Esse é o middleware de Web Services Federation `Wtrealm`:

![Azure Active Directory: Propriedades de registro do aplicativo](ws-federation/_static/AadAppIdUri.png)

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>Adicionar o WS-Federation como um provedor de logon externo para ASP.NET Core Identity

* Adicionar uma dependência no [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) ao projeto.
* Adicionar o WS-Federation para `Startup.ConfigureServices`:

    ```csharp
    services.AddIdentity<IdentityUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddAuthentication()
        .AddWsFederation(options =>
        {
            // MetadataAddress represents the Active Directory instance used to authenticate users.
            options.MetadataAddress = "https://<ADFS FQDN or AAD tenant>/FederationMetadata/2007-06/FederationMetadata.xml";

            // Wtrealm is the app's identifier in the Active Directory instance.
            // For ADFS, use the relying party's identifier, its WS-Federation Passive protocol URL:
            options.Wtrealm = "https://localhost:44307/";

            // For AAD, use the App ID URI from the app registration's Properties blade:
            options.Wtrealm = "https://wsfedsample.onmicrosoft.com/bf0e7e6d-056e-4e37-b9a6-2c36797b9f01";
        });

    services.AddMvc()
     // ...
    ```

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Faça logon com o WS-Federation

Navegue até o aplicativo e clique o **faça logon no** link no cabeçalho da barra de navegação. Há uma opção para fazer logon com WsFederation: ![Página de logon](ws-federation/_static/WsFederationButton.png)

Com o ADFS como o provedor, o botão redireciona para uma página de entrada do AD FS: ![Página de entrada do AD FS](ws-federation/_static/AdfsLoginPage.png)

Com o Azure Active Directory como o provedor, o botão redireciona para uma página de logon do AAD: ![Página de logon do AAD](ws-federation/_static/AadSignIn.png)

Um entrar com êxito para um novo usuário redireciona para a página de registro de usuário do aplicativo: ![Página de registro](ws-federation/_static/Register.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>Usar o WS-Federation, sem o ASP.NET Core Identity

O middleware de WS-Federation pode ser usado sem o Identity. Por exemplo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultSignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = WsFederationDefaults.AuthenticationScheme;
    })
    .AddWsFederation(options =>
    {
        options.Wtrealm = Configuration["wsfed:realm"];
        options.MetadataAddress = Configuration["wsfed:metadata"];
    })
    .AddCookie();
}

public void Configure(IApplicationBuilder app)
{
    app.UseAuthentication();
        // …
}
```
