---
title: Proteger um aplicativo hospedado do Webassembly ASP.NET Core Blazor com o Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 12e09cf7e27f85473d84f42564d13e1c0ed5dff1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434441"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Proteger um aplicativo hospedado do Webassembly ASP.NET Core Blazor com o Azure Active Directory B2C

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Este artigo descreve como criar um aplicativo autônomo do Webassembly Blazor que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrar aplicativos em AAD B2C e criar solução

### <a name="create-a-tenant"></a>Criar um locatário

Siga as orientações em [tutorial: criar um locatário de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para criar um locatário de AAD B2C e registrar as seguintes informações:

* Instância de AAD B2C (por exemplo, `https://contoso.b2clogin.com/`, que inclui a barra à direita)
* AAD B2C domínio de locatário (por exemplo, `contoso.onmicrosoft.com`)

### <a name="register-a-server-api-app"></a>Registrar um aplicativo de API do servidor

Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar um aplicativo do AAD para o *aplicativo de API do servidor* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:

1. Selecione **Novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, **Blazor Server AAD B2C**).
1. Para **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.** (multilocatário) para essa experiência.
1. O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.
1. Confirme se **as permissões** > **conceder ao administrador o OpenID e as permissões de offline_access** estão habilitadas.
1. Selecione **Registrar**.

No **expor uma API**:

1. Selecione **Adicionar um escopo**.
1. Selecione **Salvar e continuar**.
1. Forneça um **nome de escopo** (por exemplo, `API.Access`).
1. Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API`).
1. Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.`).
1. Confirme se o **estado** está definido como **habilitado**.
1. Selecione **Adicionar escopo**.

Registre as seguintes informações:

* *Aplicativo de API do servidor* ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111`)
* ID do diretório (ID do locatário) (por exemplo, `222222222-2222-2222-2222-222222222222`)
* *Aplicativo de API do servidor* URI da ID do aplicativo (por exemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, o portal do Azure pode padronizar o valor para a ID do cliente)
* Escopo padrão (por exemplo, `API.Access`)

### <a name="register-a-client-app"></a>Registrar um aplicativo cliente

Siga as orientações em [tutorial: registrar um aplicativo em Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) novamente para registrar um aplicativo do AAD para o *aplicativo cliente* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:

1. Selecione **Novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD B2C do cliente**).
1. Para **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.** (multilocatário) para essa experiência.
1. Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI de redirecionamento de `https://localhost:5001/authentication/login-callback`.
1. Confirme se **as permissões** > **conceder ao administrador o OpenID e as permissões de offline_access** estão habilitadas.
1. Selecione **Registrar**.

Em **autenticação** > **configurações da plataforma** > **Web**:

1. Confirme se o **URI de redirecionamento** do `https://localhost:5001/authentication/login-callback` está presente.
1. Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.
1. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.
1. Selecione o botão **Salvar**.

Em **permissões de API**:

1. Confirme se o aplicativo tem **Microsoft Graph** permissão > **User. Read** .
1. Selecione **Adicionar uma permissão** seguida por **minhas APIs**.
1. Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, **Blazor Server AAD B2C**).
1. Abra a lista de **APIs** .
1. Habilite o acesso à API (por exemplo, `API.Access`).
1. Selecione **Adicionar Permissões**.
1. Selecione o botão **conceder conteúdo do administrador para {nome do locatário}** . Clique em **Sim** para confirmar.

Em **casa** > **Azure ad B2C** **fluxos de usuário** > :

[Criar um fluxo de usuário de inscrição e entrada](/azure/active-directory-b2c/tutorial-create-user-flows)

No mínimo, selecione o atributo **Application claims** > **Display Name** user para preencher o `context.User.Identity.Name` no componente `LoginDisplay` (*Shared/LoginDisplay. Razor*).

Registre as seguintes informações:

* Registre a ID do aplicativo de aplicativo *cliente* (ID do cliente) (por exemplo, `33333333-3333-3333-3333-333333333333`).
* Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin`).

### <a name="create-the-app"></a>Criar o aplicativo

Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`). O nome da pasta também se torna parte do nome do projeto.

## <a name="server-app-configuration"></a>Configuração de aplicativo do servidor

*Esta seção pertence ao aplicativo de **servidor** da solução.*

### <a name="authentication-package"></a>Pacote de autenticação

O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureAD.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O método `AddAuthentication` configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão. O método `AddAzureADBearer` configura os parâmetros específicos no manipulador de portador JWT necessário para validar tokens emitidos pelo Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication` e `UseAuthorization` garantir que:

* O aplicativo tenta analisar e validar tokens em solicitações de entrada.
* Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a>Configurações do aplicativo

O arquivo *appSettings. JSON* contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a>Controlador WeatherForecast

O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o atributo `[Authorize]` aplicado ao controlador. É **importante** entender que:

* O atributo `[Authorize]` nesse controlador de API é a única coisa que protege essa API contra acesso não autorizado.
* O atributo `[Authorize]` usado no aplicativo Webassembly Blazor só serve como uma dica ao aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Configuração do aplicativo cliente

*Esta seção pertence ao aplicativo **cliente** da solução.*

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar uma conta B2C individual (`IndividualB2C`), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.

O pacote de `Microsoft.Authentication.WebAssembly.Msal` adiciona transitivamente o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ao aplicativo.

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para autenticação de usuários é registrado no contêiner de serviço com o método de extensão `AddMsalAuthentication` fornecido pelo pacote `Microsoft.Authentication.WebAssembly.Msal`. Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

O método `AddMsalAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.

O modelo Webassembly Blazor configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura para o escopo padrão fornecido ao comando `dotnet new` (`{APP ID URI}/{DEFAULT SCOPE}`).

Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:

* Incluído por padrão na solicitação de entrada.
* Usado para provisionar um token de acesso imediatamente após a autenticação.

Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory. Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Executar o aplicativo

Execute o aplicativo no projeto do servidor. Ao usar o Visual Studio, selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authentication/azure-ad-b2c>
* [Tutorial: Criar um locatário do Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
