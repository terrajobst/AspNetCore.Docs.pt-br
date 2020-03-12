---
title: Proteger um aplicativo hospedado do Webassembly ASP.NET Core Blazor com o Identity Server
author: guardrex
description: Para criar um novo aplicativo hospedado Blazor com autenticação de dentro do Visual Studio que usa um back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 98eb126ab3c483e0a6dc2274db8ffcfd9d5bc59a
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083772"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Proteger um aplicativo hospedado do Webassembly ASP.NET Core Blazor com o Identity Server

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para criar um novo aplicativo hospedado Blazor no Visual Studio que usa [IdentityServer](https://identityserver.io/) para autenticar usuários e chamadas de API:

1. Use o Visual Studio para criar um novo aplicativo de **WebassemblyBlazor** . Para obter mais informações, consulte <xref:blazor/get-started>.
1. Na caixa de diálogo **criar um novo aplicativo Blazor** , selecione **alterar** na seção **autenticação** .
1. Selecione **contas de usuário individuais** seguidas por **OK**.
1. Marque a caixa de seleção **ASP.NET Core hospedado** na seção **avançado** .
1. Selecione o botão **Criar**.

Para criar o aplicativo em um shell de comando, execute o seguinte comando:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`). O nome da pasta também se torna parte do nome do projeto.

## <a name="server-app-configuration"></a>Configuração de aplicativo do servidor

As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído.

### <a name="startup-class"></a>Classe de inicialização

A classe `Startup` tem as seguintes adições:

* Em `Startup.ConfigureServices`:

  * Identidade com a interface do usuário padrão:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer com um método auxiliar adicional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> que configura algumas convenções de ASP.NET Core padrão na parte superior do IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticação com um método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> adicional que configura o aplicativo para validar tokens JWT produzidos por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Em `Startup.Configure`:

  * O middleware de autenticação que é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:

    ```csharp
    app.UseAuthentication();
    ```

  * O middleware IdentityServer que expõe os pontos de extremidade do OIDC (Open ID Connect):

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

O método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> configura [IdentityServer](https://identityserver.io/) para cenários de ASP.NET Core. O IdentityServer é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo. O IdentityServer expõe complexidade desnecessária para os cenários mais comuns. Consequentemente, um conjunto de convenções e opções de configuração é fornecido para considerarmos um bom ponto de partida. Depois que a autenticação precisar ser alterada, toda a potência do IdentityServer ainda estará disponível para personalizar a autenticação para atender aos requisitos de um aplicativo.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

O método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura um esquema de política para o aplicativo como o manipulador de autenticação padrão. A política está configurada para permitir que a identidade manipule todas as solicitações roteadas para qualquer subcaminho no `/Identity`de espaço da URL de identidade. O <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> lida com todas as outras solicitações. Além disso, esse método:

* Registra um recurso de API `{APPLICATION NAME}API` com IdentityServer com um escopo padrão de `{APPLICATION NAME}API`.
* Configura o middleware do token de portador JWT para validar tokens emitidos por IdentityServer para o aplicativo.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

No `WeatherForecastController` (*Controllers/WeatherForecastController. cs*), o atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) é aplicado à classe. O atributo indica que o usuário deve ser autorizado com base na política padrão para acessar o recurso. A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> para o esquema de política mencionado anteriormente. O método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como o manipulador padrão para solicitações para o aplicativo.

### <a name="applicationdbcontext"></a>ApplicationDbContext

No `ApplicationDbContext` (*Data/ApplicationDbContext. cs*), o mesmo <xref:Microsoft.EntityFrameworkCore.DbContext> é usado em identidade com a exceção que ele estende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> para incluir o esquema para IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> é derivado de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Para obter controle total do esquema de banco de dados, herde de uma das classes de identidade <xref:Microsoft.EntityFrameworkCore.DbContext> disponíveis e configure o contexto para incluir o esquema de identidade chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no método `OnModelCreating`.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

No `OidcConfigurationController` (*controladores/OidcConfigurationController. cs*), o ponto de extremidade do cliente é provisionado para atender aos parâmetros OIDC.

### <a name="app-settings-files"></a>Arquivos de configurações do aplicativo

No arquivo de configurações do aplicativo (*appSettings. JSON*) na raiz do projeto, a seção `IdentityServer` descreve a lista de clientes configurados. No exemplo a seguir, há um único cliente. O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o parâmetro de `ClientId` do OAuth. O perfil indica o tipo de aplicativo que está sendo configurado. O perfil é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

No arquivo de configurações do aplicativo do ambiente de desenvolvimento (*appSettings. Development. JSON*) na raiz do projeto, a seção `IdentityServer` descreve a chave usada para assinar tokens. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Configuração do aplicativo cliente

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas de usuário individuais (`Individual`), o aplicativo recebe automaticamente uma referência de pacote para o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` no arquivo de projeto do aplicativo. O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.

### <a name="api-authorization-support"></a>Suporte à autorização de API

O suporte para autenticação de usuários é conectado ao contêiner de serviço pelo método de extensão fornecido dentro do pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication`. Esse método configura todos os serviços necessários para que o aplicativo interaja com o sistema de autorização existente.

```csharp
builder.Services.AddApiAuthorization();
```

Por padrão, ele carrega a configuração do aplicativo por convenção de `_configuration/{client-id}`. Por convenção, a ID do cliente é definida como o nome do assembly do aplicativo. Essa URL pode ser alterada para apontar para um ponto de extremidade separado chamando a sobrecarga com opções.

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

O componente `LoginDisplay` (*Shared/LoginDisplay. Razor*) é renderizado no componente `MainLayout` (*Shared/MainLayout. Razor*) e gerencia os seguintes comportamentos:

* Para usuários autenticados:
  * Exibe o nome de usuário atual.
  * Oferece um link para a página de perfil do usuário em ASP.NET Core identidade.
  * Oferece um botão para fazer logoff do aplicativo.
* Para usuários anônimos:
  * Oferece a opção de se registrar.
  * Oferece a opção de fazer logon.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
