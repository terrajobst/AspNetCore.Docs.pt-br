---
title: Introdução à autenticação para aplicativos de página única em ASP.NET Core
author: javiercn
description: Use a identidade com um aplicativo de página única hospedado dentro de um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 4afc9ac0a3c54b452c6a1b23e4de31d7e2fc5284
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894143"
---
# <a name="authentication-and-authorization-for-spas"></a>Autenticação e autorização para SPAs

ASP.NET Core 3.0 ou posterior oferece autenticação em aplicativos de única página (SPAs) usando o suporte para autorização de API. ASP.NET Core Identity para autenticar e armazenar os usuários é combinado com [IdentityServer](https://identityserver.io/) para a implementação de Open ID Connect.

Um parâmetro de autenticação foi adicionado para o **Angular** e **reagir** semelhante para o parâmetro de autenticação em modelos de projeto a **aplicativo Web (Model-View-Controller)**  (MVC) e **aplicativo Web** modelos de projeto (páginas do Razor). Os valores de parâmetro permitidos são **None** e **individuais**. O **React. js e Redux** modelo de projeto não dá suporte a parâmetro authentication neste momento.

## <a name="create-an-app-with-api-authorization-support"></a>Criar um aplicativo com suporte à autorização de API

Autorização e autenticação de usuário podem ser usados com Angular e React SPAs. Abra um shell de comando e execute o seguinte comando:

**Angular**:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

**Reagir**:

```console
dotnet new react -o <output_directory_name> -au Individual
```

O comando anterior cria um aplicativo ASP.NET Core com um *ClientApp* diretório que contém o SPA.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Descrição geral dos componentes do aplicativo ASP.NET Core

As seções a seguir descrevem adições ao projeto quando o suporte de autenticação está incluído:

### <a name="startup-class"></a>Classe de inicialização

O `Startup` classe tem as seguintes adições:

* Dentro de `Startup.ConfigureServices` método:
  * Identidade com a interface do usuário padrão:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer com adicional `AddApiAuthorization` método auxiliar que instalações alguns padrão convenções do ASP.NET Core na parte superior IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticação com adicional `AddIdentityServerJwt` método auxiliar que configura o aplicativo para validar os tokens JWT produzido pelo IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Dentro de `Startup.Configure` método:
  * O middleware de autenticação que é responsável por validar as credenciais de solicitação e definindo o usuário no contexto de solicitação:

    ```csharp
    app.UseAuthentication();
    ```

  * O middleware IdentityServer que expõe os pontos de extremidade de Open ID Connect:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Esse método auxiliar configura IdentityServer para usar nossa configuração com suporte. IdentityServer é uma estrutura poderosa e extensível para lidar com questões de segurança do aplicativo. Ao mesmo tempo, que expõe uma complexidade desnecessária para os cenários mais comuns. Consequentemente, um conjunto de convenções e opções de configuração é fornecido a você que são considerados um bom ponto de partida. Depois que a autenticação de necessidades mudam, todo o poder do IdentityServer ainda está disponível para personalizar a autenticação para atender às suas necessidades.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Esse método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão. A política é configurada para permitir que a identidade de lidar com todas as solicitações roteadas para qualquer subcaminho no espaço de URL de identidade "/ identidade". O `JwtBearerHandler` manipula todas as outras solicitações. Além disso, esse método registra uma `<<ApplicationName>>API` recurso da API com IdentityServer com um escopo padrão de `<<ApplicationName>>API` e configura o middleware de token de portador de JWT para validar tokens emitidos pelo IdentityServer para o aplicativo.

### <a name="sampledatacontroller"></a>SampleDataController

No *Controllers\SampleDataController.cs* do arquivo, observe o `[Authorize]` atributo aplicado à classe que indica que o usuário precisa ser autorizado com base na política padrão para acessar o recurso. A política de autorização padrão acontece para ser configurado para usar o esquema de autenticação padrão, que é definida pela `AddIdentityServerJwt` para o esquema de política que foi mencionado acima, tornando o `JwtBearerHandler` configurado por esse método auxiliar o manipulador padrão para solicitações para o aplicativo.

### <a name="applicationdbcontext"></a>ApplicationDbContext

No *Data\ApplicationDbContext.cs* do arquivo, observe o mesmo `DbContext` é usado em identidade com a exceção que ele estende `ApiAuthorizationDbContext` (uma mais derivado da classe de `IdentityDbContext`) para incluir o esquema para o IdentityServer.

Para obter controle total sobre o esquema de banco de dados, herdar de um da identidade disponível `DbContext` classes e configurar o contexto para incluir o esquema de identidade chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` no `OnModelCreating` método.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

No *Controllers\OidcConfigurationController.cs* de arquivo, observe o ponto de extremidade for provisionado para servir os parâmetros OIDC que o cliente precisa usar.

### <a name="appsettingsjson"></a>appsettings.json

No *appSettings. JSON* arquivo de raiz do projeto, há uma nova `IdentityServer` configurado de seção que descreve a lista de clientes. No exemplo a seguir, há um único cliente. O nome do cliente corresponde ao nome do aplicativo e é mapeado pela convenção OAuth `ClientId` parâmetro. O perfil indica o tipo de aplicativo que está sendo configurado. Ela é usada internamente para convenções de unidade que simplificam o processo de configuração para o servidor. Há vários perfis disponíveis, conforme explicado a [perfis de aplicativos](#application-profiles) seção.

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

No *appsettings. Development.JSON* arquivo de raiz do projeto, há um `IdentityServer` seção que descreve a chave usada para assinar tokens. Ao implantar em produção, uma chave deve ser provisionado e implantado juntamente com o aplicativo, conforme explicado a [implantar na produção](#deploy-to-production) seção.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Descrição geral do aplicativo Angular

Dar suporte a autenticação e autorização de API no Angular modelo reside em seu próprio módulo Angular na *ClientApp\src\api autorização* directory. O módulo é composto dos seguintes elementos:

* 3 componentes:
  * *login.component.ts*: Manipula o fluxo de logon do aplicativo.
  * *logout.component.ts*: Manipula o fluxo de logout do aplicativo.
  * *login-menu.component.ts*: Um widget que exibe um dos conjuntos de links a seguir:
    * Gerenciamento de perfil de usuário e links quando o usuário é autenticado de logoff.
    * Registro e faça logon em links quando o usuário não está autenticado.
* Um protetor de rota `AuthorizeGuard` que podem ser adicionados às rotas e exige que um usuário seja autenticado antes de visitar a rota.
* Um interceptor HTTP `AuthorizeInterceptor` que anexa o token de acesso para solicitações HTTP de saída da API de direcionamento quando o usuário é autenticado.
* Um serviço `AuthorizeService` que lida com os detalhes de baixo nível do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.
* Um módulo Angular que define rotas associadas as partes de autenticação do aplicativo. Ele expõe o componente do menu de logon, o interceptador, o protetor e o serviço para consumo do restante do aplicativo.

## <a name="general-description-of-the-react-app"></a>Descrição geral do aplicativo React

O suporte para autenticação e autorização de API no modelo React reside na *ClientApp\src\components\api autorização* directory. Ele é composto dos seguintes elementos:

* componentes de 4:
  * *Login.js*: Manipula o fluxo de logon do aplicativo.
  * *Logout.js*: Manipula o fluxo de logout do aplicativo.
  * *LoginMenu.js*: Um widget que exibe um dos conjuntos de links a seguir:
    * Gerenciamento de perfil de usuário e links quando o usuário é autenticado de logoff.
    * Registro e faça logon em links quando o usuário não está autenticado.
  * *AuthorizeRoute.js*: Um componente de rota que requer que um usuário seja autenticado antes do componente de renderização indicado no `Component` parâmetro.
* Um exportado `authService` instância da classe `AuthorizeService` que lida com os detalhes de baixo nível do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.

Agora que você já viu os principais componentes da solução, você pode levar a uma análise mais profunda sobre cenários individuais para o aplicativo.

## <a name="require-authorization-on-a-new-api"></a>Exigir a autorização em uma nova API

Por padrão, o sistema está configurado para facilmente exigir autorização para novas APIs. Para fazer isso, crie um novo controlador e adicione o `[Authorize]` atributo à classe do controlador ou a qualquer ação no controlador.

## <a name="protect-a-client-side-route-angular"></a>Proteger uma rota do lado do cliente (Angular)

Proteger uma rota do lado do cliente é feito adicionando o protetor de autorizar à lista de proteções para execução ao configurar uma rota. Por exemplo, você pode ver como o `fetch-data` rota está configurada no módulo principal do aplicativo Angular:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele), mas que ele apenas impede que o usuário navegar para a rota determinada do lado do cliente quando ele não está autenticado.

## <a name="authenticate-api-requests-angular"></a>Autenticar solicitações de API (Angular)

Autenticar solicitações para APIs hospedadas juntamente com o aplicativo é feito automaticamente por meio do uso o interceptador de cliente HTTP definido pelo aplicativo.

## <a name="protect-a-client-side-route-react"></a>Proteger uma rota do lado do cliente (React)

Proteger uma rota do lado do cliente usando o `AuthorizeRoute` componente, em vez do plain `Route` componente. Por exemplo, observe como o `fetch-data` rota está configurada no `App` componente:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Protegendo uma rota:

* Não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele).
* Só impede que o usuário navegar para a rota determinada do lado do cliente quando ele não está autenticado.

## <a name="authenticate-api-requests-react"></a>Autenticar solicitações de API (React)

Autenticação de solicitações com o React é feito importando primeiro o `authService` da instância da `AuthorizeService`. O token de acesso é recuperado do `authService` e é anexado à solicitação, conforme mostrado abaixo. Componentes do React, esse trabalho geralmente é feito no `componentDidMount` método de ciclo de vida ou como resultado de alguma interação do usuário.

### <a name="import-the-authservice-into-your-component"></a>Importar o authService para seu componente

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Recuperar e anexe o token de acesso para a resposta

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

## <a name="deploy-to-production"></a>Implantar na produção

Para implantar o aplicativo em produção, os recursos a seguir precisam ser provisionados:

* Concede a um banco de dados para armazenar as contas de usuário da identidade e o IdentityServer.
* Um certificado de produção a ser usado para assinar tokens.
  * Não há nenhum requisito específico para esse certificado; ele pode ser um certificado autoassinado ou um certificado configurado por meio de uma autoridade de certificação.
  * Ele pode ser gerado por meio de ferramentas padrão, como o PowerShell ou OpenSSL.
  * Ele pode ser instalado no repositório de certificados nos computadores de destino ou implantado como um *. pfx* arquivo com uma senha forte.

### <a name="example-deploy-to-azure-websites"></a>Exemplo: Implantar em sites do Azure

Esta seção descreve a implantação do aplicativo para sites do Azure usando um certificado armazenado no repositório de certificados. Para modificar o aplicativo para carregar um certificado do repositório de certificados, o plano de serviço de aplicativo precisa ter pelo menos a camada padrão quando você configura em uma etapa posterior. No aplicativo do *appSettings. JSON* do arquivo, modifique o `IdentityServer` seção para incluir os detalhes da chave:

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

* A propriedade de nome no certificado corresponde com o assunto diferenciado do certificado.
* O local de armazenamento representa o local carregar o certificado de (`CurrentUser` ou `LocalMachine`).
* O nome do repositório representa o nome do repositório de certificados em que o certificado é armazenado. Nesse caso, ele aponta para o repositório pessoal do usuário.

Para implantar em sites do Azure, implantar o aplicativo seguindo as etapas em [implantar o aplicativo no Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) para criar os recursos do Azure necessários e implantar o aplicativo em produção.

Depois de seguir as instruções anteriores, o aplicativo é implantado no Azure, mas ainda não está funcional. O certificado usado pelo aplicativo ainda precisa ser configurado. Localize a impressão digital do certificado a ser usado e siga as etapas descritas em [carregar seus certificados](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).

Enquanto essas etapas mencionam SSL, não há uma **certificados privados** a seção no portal onde você pode carregar o certificado provisionado para usar com o aplicativo.

Após essa etapa, reinicie o aplicativo e deve ser funcional.

## <a name="other-configuration-options"></a>Outras opções de configuração

O suporte para autorização de API se baseia na IdentityServer com um conjunto de convenções, valores padrão e aprimoramentos para simplificar a experiência para SPAs. Obviamente, todo o poder do IdentityServer está disponível em segundo plano se as integrações do ASP.NET Core não cobrem o seu cenário. O suporte do ASP.NET Core se concentra em aplicativos de "terceiros", onde todos os aplicativos são criados e implantados por nossa organização. Como tal, o suporte não é oferecido para coisas como o consentimento ou de Federação. Para esses cenários, use o IdentityServer e siga a documentação.

### <a name="application-profiles"></a>Perfis de aplicativos

Perfis de aplicativo são configurações predefinidas para aplicativos que melhor definem seus parâmetros. Neste momento, há suporte para os seguintes perfis:

* `IdentityServerSPA`: Representa um SPA hospedado juntamente com IdentityServer como uma única unidade.
  * O `redirect_uri` padrão é `/authentication/login-callback`.
  * O `post_logout_redirect_uri` padrão é `/authentication/logout-callback`.
  * O conjunto de escopos inclui o `openid`, `profile`e cada escopo definido para as APIs no aplicativo.
  * É o conjunto de tipos de resposta permitidos OIDC `id_token token` ou em cada um deles individualmente (`id_token`, `token`).
  * O modo de resposta permitidos é `fragment`.
* `SPA`: Representa um SPA não é hospedado com IdentityServer.
  * O conjunto de escopos inclui o `openid`, `profile`e cada escopo definido para as APIs no aplicativo.
  * É o conjunto de tipos de resposta permitidos OIDC `id_token token` ou em cada um deles individualmente (`id_token`, `token`).
  * O modo de resposta permitidos é `fragment`.
* `IdentityServerJwt`: Representa uma API que é hospedada juntamente com IdentityServer.
  * O aplicativo está configurado para ter um único escopo padrão é o nome do aplicativo.
* `API`: Representa uma API que não é hospedada com IdentityServer.
  * O aplicativo está configurado para ter um único escopo padrão é o nome do aplicativo.

### <a name="configuration-through-appsettings"></a>Configuração por meio de AppSettings

Configure os aplicativos por meio do sistema de configuração ao adicioná-los à lista de `Clients` ou `Resources`.

Configurar cada cliente `redirect_uri` e `post_logout_redirect_uri` propriedade, conforme mostrado no exemplo a seguir:

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

Você também pode configurar os clientes e recursos por meio de código usando uma sobrecarga de `AddApiAuthorization` que executa uma ação para configurar as opções.

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
