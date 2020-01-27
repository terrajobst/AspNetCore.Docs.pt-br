---
title: Introdução à autenticação para aplicativos de página única no ASP.NET Core
author: javiercn
description: Use a identidade com um aplicativo de página única hospedado dentro de um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 31a5e47d772e7416646c4d83c3209d7d2b254199
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829155"
---
# <a name="authentication-and-authorization-for-spas"></a>Autenticação e autorização para SPAs

O ASP.NET Core 3,0 ou posterior oferece autenticação em aplicativos de página única (SPAs) usando o suporte para autorização de API. ASP.NET Core identidade para autenticação e armazenamento de usuários é combinada com o [IdentityServer](https://identityserver.io/) para implementar o Open ID Connect.

Um parâmetro de autenticação foi adicionado aos modelos de projeto **angular** e **reajam** que é semelhante ao parâmetro de autenticação nos modelos de projeto de **aplicativo Web (Model-View-Controller)** (MVC) e **aplicativo Web** (Razor Pages). Os valores de parâmetro permitidos são **None** e **individual**. O modelo de projeto **reajam. js e Redux** não dá suporte ao parâmetro de autenticação no momento.

## <a name="create-an-app-with-api-authorization-support"></a>Criar um aplicativo com suporte à autorização de API

A autenticação e a autorização do usuário podem ser usadas com SPAs angular e reagir. Abra um shell de comando e execute o seguinte comando:

**Angular**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**React**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

O comando anterior cria um aplicativo ASP.NET Core com um diretório *ClientApp* que contém o Spa.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Descrição geral dos componentes ASP.NET Core do aplicativo

As seções a seguir descrevem as adições ao projeto quando o suporte à autenticação é incluído:

### <a name="startup-class"></a>Classe de inicialização

A classe `Startup` tem as seguintes adições:

* Dentro do método de `Startup.ConfigureServices`:
  * Identidade com a interface do usuário padrão:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer com um método auxiliar adicional `AddApiAuthorization` que configura algumas convenções de ASP.NET Core padrão na parte superior do IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticação com um método auxiliar `AddIdentityServerJwt` adicional que configura o aplicativo para validar tokens JWT produzidos por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Dentro do método de `Startup.Configure`:
  * O middleware de autenticação que é responsável por validar as credenciais de solicitação e definir o usuário no contexto da solicitação:

    ```csharp
    app.UseAuthentication();
    ```

  * O middleware IdentityServer que expõe os pontos de extremidade do Open ID Connect:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Esse método auxiliar configura o IdentityServer para usar nossa configuração com suporte. O IdentityServer é uma estrutura avançada e extensível para lidar com questões de segurança de aplicativo. Ao mesmo tempo, isso expõe uma complexidade desnecessária para os cenários mais comuns. Consequentemente, um conjunto de convenções e opções de configuração é fornecido para você que são considerados um bom ponto de partida. Depois que a autenticação precisar ser alterada, toda a potência do IdentityServer ainda estará disponível para personalizar a autenticação de acordo com suas necessidades.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Esse método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão. A política está configurada para permitir que a identidade manipule todas as solicitações roteadas para qualquer subcaminho no espaço de URL de identidade "/Identity". O `JwtBearerHandler` lida com todas as outras solicitações. Além disso, esse método registra um `<<ApplicationName>>API` recurso de API com IdentityServer com um escopo padrão de `<<ApplicationName>>API` e configura o middleware de token de portador JWT para validar tokens emitidos por IdentityServer para o aplicativo.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

No arquivo *Controllers\WeatherForecastController.cs* , observe o atributo `[Authorize]` aplicado à classe que indica que o usuário precisa ser autorizado com base na política padrão para acessar o recurso. A política de autorização padrão é configurada para usar o esquema de autenticação padrão, que é configurado por `AddIdentityServerJwt` para o esquema de política mencionado acima, tornando o `JwtBearerHandler` configurado por tal método auxiliar o manipulador padrão para solicitações para o aplicativo.

### <a name="applicationdbcontext"></a>ApplicationDbContext

No arquivo *Data\ApplicationDbContext.cs* , observe que o mesmo `DbContext` é usado em identidade com a exceção que ele estende `ApiAuthorizationDbContext` (uma classe mais derivada de `IdentityDbContext`) para incluir o esquema para IdentityServer.

Para obter controle total do esquema de banco de dados, herde de uma das classes de identidade `DbContext` disponíveis e configure o contexto para incluir o esquema de identidade chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no método `OnModelCreating`.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

No arquivo *Controllers\OidcConfigurationController.cs* , observe o ponto de extremidade provisionado para atender aos parâmetros OIDC que o cliente precisa usar.

### <a name="appsettingsjson"></a>appsettings.json

No arquivo *appSettings. JSON* da raiz do projeto, há uma nova seção `IdentityServer` que descreve a lista de clientes configurados. No exemplo a seguir, há um único cliente. O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o parâmetro de `ClientId` do OAuth. O perfil indica o tipo de aplicativo que está sendo configurado. Ele é usado internamente para direcionar as convenções que simplificam o processo de configuração para o servidor. Há vários perfis disponíveis, conforme explicado na seção [perfis de aplicativo](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.json

Em *appSettings. Arquivo Development. JSON* da raiz do projeto, há uma seção `IdentityServer` que descreve a chave usada para assinar tokens. Ao implantar na produção, uma chave precisa ser provisionada e implantada juntamente com o aplicativo, conforme explicado na seção [implantar na produção](#deploy-to-production) .

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Descrição geral do aplicativo angular

O suporte de autorização de API e autenticação no modelo angular reside em seu próprio módulo angular no diretório *ClientApp\src\api-Authorization* . O módulo é composto pelos seguintes elementos:

* 3 componentes:
  * *login. Component. TS*: manipula o fluxo de logon do aplicativo.
  * *logout. Component. TS*: manipula o fluxo de logout do aplicativo.
  * *login-menu. Component. TS*: um widget que exibe um dos seguintes conjuntos de links:
    * Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.
    * Os links de registro e logon quando o usuário não está autenticado.
* Um `AuthorizeGuard` de proteção de rota que pode ser adicionado a rotas e requer que um usuário seja autenticado antes de visitar a rota.
* Um `AuthorizeInterceptor` Interceptor HTTP que anexa o token de acesso a solicitações HTTP de saída direcionando a API quando o usuário é autenticado.
* Um `AuthorizeService` de serviço que manipula os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.
* Um módulo angular que define as rotas associadas às partes de autenticação do aplicativo. Ele expõe o componente de menu de logon, o Interceptor, a proteção e o serviço para consumo do restante do aplicativo.

## <a name="general-description-of-the-react-app"></a>Descrição geral do aplicativo reajam

O suporte para autenticação e autorização de API no modelo reagir reside no diretório *ClientApp\src\components\api-Authorization* Ele é composto pelos seguintes elementos:

* 4 componentes:
  * *Login. js*: manipula o fluxo de logon do aplicativo.
  * *Logout. js*: manipula o fluxo de logout do aplicativo.
  * *LoginMenu. js*: um widget que exibe um dos seguintes conjuntos de links:
    * Os links de gerenciamento de perfil de usuário e logoff quando o usuário é autenticado.
    * Os links de registro e logon quando o usuário não está autenticado.
  * *AuthorizeRoute. js*: um componente de rota que exige que um usuário seja autenticado antes de renderizar o componente indicado no parâmetro `Component`.
* Uma instância de `authService` exportada da classe `AuthorizeService` que lida com os detalhes de nível inferior do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.

Agora que você já viu os principais componentes da solução, pode fazer uma análise mais profunda de cenários individuais para o aplicativo.

## <a name="require-authorization-on-a-new-api"></a>Exigir autorização em uma nova API

Por padrão, o sistema está configurado para exigir facilmente a autorização para novas APIs. Para fazer isso, crie um novo controlador e adicione o atributo `[Authorize]` à classe controlador ou a qualquer ação dentro do controlador.

## <a name="customize-the-api-authentication-handler"></a>Personalizar o manipulador de autenticação de API

Para personalizar a configuração do manipulador JWT da API, configure sua instância de <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

O manipulador JWT da API gera eventos que permitem o controle sobre o processo de autenticação usando `JwtBearerEvents`. Para fornecer suporte para autorização de API, `AddIdentityServerJwt` registra seus próprios manipuladores de eventos.

Para personalizar o tratamento de um evento, empacote o manipulador de eventos existente com lógica adicional, conforme necessário. Por exemplo:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

No código anterior, o manipulador de eventos `OnTokenValidated` é substituído por uma implementação personalizada. Esta implementação:

1. Chama a implementação original fornecida pelo suporte à autorização da API.
1. Execute sua própria lógica personalizada.

## <a name="protect-a-client-side-route-angular"></a>Proteger uma rota do lado do cliente (angular)

A proteção de uma rota do lado do cliente é feita pela adição da proteção de autorização à lista de proteções a serem executadas durante a configuração de uma rota. Como exemplo, você pode ver como a rota de `fetch-data` é configurada no módulo angular do aplicativo principal:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda requer um atributo `[Authorize]` aplicado a ele), mas que só impede que o usuário navegue até a rota do lado do cliente quando não é autenticado.

## <a name="authenticate-api-requests-angular"></a>Autenticar solicitações de API (angular)

A autenticação de solicitações para APIs hospedadas juntamente com o aplicativo é feita automaticamente pelo uso do interceptador de cliente HTTP definido pelo aplicativo.

## <a name="protect-a-client-side-route-react"></a>Proteger uma rota do lado do cliente (reagir)

Proteja uma rota do lado do cliente usando o componente `AuthorizeRoute` em vez do componente `Route` simples. Por exemplo, observe como a rota de `fetch-data` é configurada no componente `App`:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Protegendo uma rota:

* Não protege o ponto de extremidade real (que ainda requer um atributo `[Authorize]` aplicado a ele).
* Impede que o usuário navegue para a rota determinada do lado do cliente quando ele não é autenticado.

## <a name="authenticate-api-requests-react"></a>Autenticar solicitações de API (reagir)

A autenticação de solicitações com reagir é feita pela primeira importação da instância de `authService` do `AuthorizeService`. O token de acesso é recuperado do `authService` e é anexado à solicitação, conforme mostrado abaixo. Em reajam de componentes, esse trabalho normalmente é feito no método de ciclo de vida `componentDidMount` ou como resultado de alguma interação do usuário.

### <a name="import-the-authservice-into-your-component"></a>Importar o authService para o componente

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Recuperar e anexar o token de acesso à resposta

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Implantar para a produção

Para implantar o aplicativo na produção, os seguintes recursos precisam ser provisionados:

* Um banco de dados para armazenar as contas de usuário de identidade e as concessões IdentityServer.
* Um certificado de produção a ser usado para tokens de assinatura.
  * Não há requisitos específicos para esse certificado; pode ser um certificado autoassinado ou um certificado provisionado por meio de uma autoridade de certificação.
  * Ele pode ser gerado por meio de ferramentas padrão, como o PowerShell ou o OpenSSL.
  * Ele pode ser instalado no repositório de certificados nos computadores de destino ou implantado como um arquivo *. pfx* com uma senha forte.

### <a name="example-deploy-to-azure-websites"></a>Exemplo: implantar nos sites do Azure

Esta seção descreve a implantação do aplicativo nos sites do Azure usando um certificado armazenado no repositório de certificados. Para modificar o aplicativo para carregar um certificado do repositório de certificados, o plano do serviço de aplicativo precisa estar em pelo menos a camada Standard quando você configura o em uma etapa posterior. No arquivo *appSettings. JSON* do aplicativo, modifique a seção `IdentityServer` para incluir os detalhes da chave:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* A propriedade Name no certificado corresponde ao assunto distinto do certificado.
* O local do repositório representa onde carregar o certificado (`CurrentUser` ou `LocalMachine`).
* O nome do repositório representa o nome do repositório de certificados onde o certificado está armazenado. Nesse caso, ele aponta para o repositório de usuários pessoais.

Para implantar os sites do Azure, implante o aplicativo seguindo as etapas em [implantar o aplicativo no Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) para criar os recursos necessários do Azure e implantar o aplicativo para produção.

Depois de seguir as instruções anteriores, o aplicativo é implantado no Azure, mas ainda não está funcional. O certificado usado pelo aplicativo ainda precisa ser configurado. Localize a impressão digital do certificado a ser usado e siga as etapas descritas em [carregar seus certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Embora essas etapas mencionem o SSL, há uma seção de **certificados privados** no portal em que você pode carregar o certificado provisionado para usar com o aplicativo.

Após essa etapa, reinicie o aplicativo e ele deverá ser funcional.

## <a name="other-configuration-options"></a>Outras opções de configuração

O suporte para a autorização da API se baseia no IdentityServer com um conjunto de convenções, valores padrão e aprimoramentos para simplificar a experiência do SPAs. Não é necessário dizer que todo o poder do IdentityServer está disponível nos bastidores se as integrações ASP.NET Core não abrangem seu cenário. O suporte a ASP.NET Core concentra-se em aplicativos de "primeira parte", onde todos os aplicativos são criados e implantados pela nossa organização. Dessa forma, o suporte não é oferecido para coisas como autorização ou Federação. Para esses cenários, use o IdentityServer e siga sua documentação.

### <a name="application-profiles"></a>Perfis de aplicativo

Perfis de aplicativo são configurações predefinidas para aplicativos que definem ainda mais seus parâmetros. Neste momento, há suporte para os seguintes perfis:

* `IdentityServerSPA`: representa um SPA hospedado juntamente com IdentityServer como uma única unidade.
  * O `redirect_uri` usa como padrão `/authentication/login-callback`.
  * O `post_logout_redirect_uri` usa como padrão `/authentication/logout-callback`.
  * O conjunto de escopos inclui o `openid`, `profile`e todos os escopos definidos para as APIs no aplicativo.
  * O conjunto de tipos de resposta OIDC permitidos é `id_token token` ou cada um deles individualmente (`id_token`, `token`).
  * O modo de resposta permitido é `fragment`.
* `SPA`: representa um SPA que não é hospedado com IdentityServer.
  * O conjunto de escopos inclui o `openid`, `profile`e todos os escopos definidos para as APIs no aplicativo.
  * O conjunto de tipos de resposta OIDC permitidos é `id_token token` ou cada um deles individualmente (`id_token`, `token`).
  * O modo de resposta permitido é `fragment`.
* `IdentityServerJwt`: representa uma API que é hospedada juntamente com IdentityServer.
  * O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.
* `API`: representa uma API que não é hospedada com IdentityServer.
  * O aplicativo é configurado para ter um único escopo que usa como padrão o nome do aplicativo.

### <a name="configuration-through-appsettings"></a>Configuração através de AppSettings

Configure os aplicativos por meio do sistema de configuração adicionando-os à lista de `Clients` ou `Resources`.

Configure a propriedade de `redirect_uri` e `post_logout_redirect_uri` de cada cliente, conforme mostrado no exemplo a seguir:

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

Ao configurar recursos, você pode configurar os escopos para o recurso, conforme mostrado abaixo:

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Configuração por meio de código

Você também pode configurar os clientes e os recursos por meio de código usando uma sobrecarga de `AddApiAuthorization` que executa uma ação para configurar opções.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
