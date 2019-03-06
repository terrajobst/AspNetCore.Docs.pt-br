---
title: Introdução à autenticação para aplicativos de página única em ASP.NET Core
author: ''
description: Use identidade com um aplicativo de página única hospedado dentro de um aplicativo ASP.NET Core.
ms.author: ''
ms.date: 03/05/2018
uid: security/authentication/identity/spa
ms.openlocfilehash: cf04ec1ff0ae9afea066fd1864ab0a7956ace32c
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346706"
---
# <a name="authentication-and-authorization-for-spas"></a>Autenticação e autorização para SPAs

No ASP.NET 3.0, introduzimos o suporte para autenticação em aplicativos de única página usando nosso novo suporte para autorização de API. Esse suporte se baseia em uma combinação de identidade do ASP.NET Core para autenticar e armazenar os usuários e o servidor de identidade para a implementação de Open ID Connect.

Adicionamos um novo parâmetro de autenticação ao nosso Angular e React modelos semelhante para o parâmetro de autenticação em nossos modelos de páginas mvc e razor com permitido valores 'None' e 'Individual'.

## <a name="create-an-angular-app-with-api-authorization-support"></a>Criar um aplicativo Angular com suporte à autorização de API

Para criar um novo aplicativo Angular com suporte para autenticação e autorização de usuários, abra um shell de comando e execute o seguinte comando:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

O comando anterior cria um aplicativo ASP.NET Core com um *ClientApp* diretório que contém o aplicativo Angular.

## <a name="create-a-react-app-with-api-authorization-support"></a>Criar um aplicativo do React com suporte à autorização de API

Para criar um novo aplicativo do React com suporte para autenticação e autorização de usuários, abra um shell de comando e execute o seguinte comando:

```console
dotnet new react -o <output_directory_name> -au Individual
```

O comando anterior cria um aplicativo ASP.NET Core com um *ClientApp* diretório que contém o aplicativo do React.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Descrição geral dos componentes do aplicativo ASP.NET Core

Há várias adições ao projeto que incluímos o suporte para autenticação:

### <a name="startup-class"></a>Classe de inicialização

Se observarmos o código na classe de inicialização abaixo pode perceber as seguir inclusões:
* Inside `public void ConfigureServices(IServiceCollection services)`:
  * Identidade com a interface do usuário padrão.
    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
      .AddDefaultUI(UIFramework.Bootstrap4)
      .AddEntityFrameworkStores<ApplicationDbContext>();
    ```
  * Servidor de identidade com um outro método auxiliar AddApiAuthorization que instalações alguns padrão convenções do ASP.NET na parte superior do servidor de identidade.
    ```csharp
    services.AddIdentityServer()
      .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```
  * Autenticação com um método auxiliar AddIdentityServerJwt adicional que configura o aplicativo para validar os tokens Jwt produzidos pelo servidor de identidade. 
    ```csharp
    services.AddAuthentication()
      .AddIdentityServerJwt();
    ```
* Inside `public void Configure(IApplicationBuilder app)`:
  * O middleware de autenticação que é responsável por validar as credenciais na solicitação de entrada e definir o usuário no contexto de solicitação.
    ```csharp
    app.UseAuthentication();
    ```
  * O middleware de servidor de identidade que expõe os pontos de extremidade de Open ID Connect.
    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization 
Esse método auxiliar configura o servidor de identidade para usar nossa configuração com suporte. Servidor de identidade é uma estrutura muito poderosa e extensível para lidar com questões de segurança de aplicativo, mas ao mesmo tempo que expõe muita complexidade, não precisamos saber sobre para cenários mais comuns, portanto, podemos escolher um conjunto de convenções e Opções de configuração para você que consideramos são um bom ponto de partida. Depois que as necessidades do seu autenticação toda a capacidade do servidor de identidade ainda está disponível para você, você pode personalizá-lo para atender às suas necessidades.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt
Esse método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão. A política é configurada para permitir que a identidade de lidar com todas as solicitações que ir para qualquer subcaminho no espaço de url de identidade "/ identidade" e para permitir que o JwtBearerHandler lidar com todas as outras solicitações.
Addionally esse método registra uma `<<ApplicationName>>API` recurso da Api com o servidor de identidade com um escopo padrão de `<<ApplicationName>>API` e configura o middleware de token de portador de JWT para validar tokens emitidos pelo servidor de identidade para o aplicativo.

### <a name="sampledatacontroller"></a>SampleDataController
Se observarmos o arquivo Controllers\SampleDataController.cs que observamos a `[Authorize]` atributo aplicado à classe que indica que o usuário precisa ser autorizado com base na política padrão para acessar o recurso. A política de autorização padrão acontece para ser configurado para usar o esquema de autenticação padrão que é configurado por `AddIdentityServerJwt` para o esquema de política que mencionamos anteriormente, tornar o manipulador JwtBearer definidas por esse método auxiliar o manipulador padrão para solicitações para o aplicativo.

### <a name="applicationdbcontext"></a>ApplicationDbContext
Se observarmos o arquivo no Data\ApplicationDbContext.cs podemos ver o mesmo DbContext que usamos em identidade com a exceção de que ele estende ApiAuthorizationDbContext (uma classe mais derivada do IdentityDbContext) para incluir o esquema para o servidor de identidade.
Se quisermos que o controle total sobre o esquema de banco de dados simplesmente pode herdar de uma das classes DbContext de identidade disponíveis e configurar o contexto para incluir o esquema de identidade, chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` sobre o `OnModelCreating` método.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController
Se observarmos o arquivo Controllers\OidcConfigurationController.cs, podemos ver o ponto de extremidade que podemos coordene para atender os parâmetros OIDC que o cliente precisa usar.

### <a name="appsettingsjson"></a>appsettings.json
Se observarmos o arquivo appSettings. JSON na raiz do projeto, podemos ver um novo `IdentityServer` seção que descreve a lista de configurar os clientes e podemos ver que há um único cliente. O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o parâmetro ClientId do oAuth. O perfil indica o tipo de aplicativo que estamos configurando, e é usada internamente para convenções de unidade que simplificam o processo de configuração para o servidor. Há vários perfis disponíveis explicados na seção a seguir.

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
Se observarmos o appsettings. Arquivo Development.JSON na raiz do projeto, podemos ver um novo `IdentityServer` seção que descreve a chave que estamos usando para assinar tokens. Ao implantar em produção uma chave precisa ser provisionado e implantado juntamente com o aplicativo, conforme explicado abaixo.

```json
  "IdentityServer": {
    "Key": {
      "Type": "Development"
    }
  }
}
```

## <a name="general-description-of-the-angular-application"></a>Descrição geral do aplicativo Angular
O suporte para autenticação e autorização de API no modelo Angular reside em seu próprio módulo Angular. Em autorização ClientApp\src\api e ele é compostos dos seguintes elementos:
* 3 componentes:
  * Componente de login: Manipula o fluxo de logon para o aplicativo.
  * Componente de logoff: Manipula o fluxo de logoff para o aplicativo.
  * Componente do menu de logon: Um widget que exibe o usuário autenticado atual com links para gerenciar o perfil do usuário e fazer logoff ou links para entrar ou registrar quando o usuário não está autenticado.
* Um protetor de rota `AuthorizeGuard` que podem ser adicionados às rotas e exige que um usuário seja autenticado antes de visitar a rota.
* Um interceptor de http `AuthorizeInterceptor` que anexa o token de acesso para solicitações HTTP de saída da API de direcionamento quando o usuário é autenticado.
* Um serviço `AuthorizeService` que lida com os detalhes de nível inferiores do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.
* Um módulo angular que define rotas associadas as partes de autenticação do aplicativo e expõe o componente do menu de logon, o interceptador, o protetor e o serviço para consumo do restante do aplicativo.

## <a name="general-description-of-the-react-application"></a>Descrição geral do aplicativo React
O suporte para autenticação e autorização de API nas vidas React modelo sob ClientApp\src\components\api authorization\ e ele é composto dos seguintes elementos:
* Componentes de 4:
  * Componente de login: Manipula o fluxo de logon para o aplicativo.
  * Componente de logoff: Manipula o fluxo de logoff para o aplicativo.
  * Componente do menu de logon: Um widget que exibe o usuário autenticado atual com links para gerenciar o perfil do usuário e fazer logoff ou links para entrar ou registrar quando o usuário não está autenticado.
  * AuthorizeRoute: Um componente de rota que requer que um usuário seja autenticado antes do componente indicado no parâmetro de componente de renderização.
  * Um exportado `authService` instância da classe `AuthorizeService` que lida com os detalhes de nível inferiores do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.

Agora que vimos os principais componentes da solução, vamos pode dar uma olhada específica para cenários individuais para o aplicativo:

## <a name="requiring-authorization-on-a-new-api"></a>Exigir autorização em uma nova API
O sistema está configurado fora da caixa para torná-lo trivial exigir autorização para novas APIs. Para fazer isso, basta criar um novo controlador e adicionar o `[Authorize]` atributo à classe do controlador ou a qualquer ação no controlador.

## <a name="protecting-a-client-side-route-angular"></a>Protegendo uma rota do lado do cliente (Angular)
Proteger uma rota de lado do cliente é feito adicionando o protetor de autorizar à lista de proteções para execução ao configurar uma rota. Por exemplo, você pode ver como a rota de buscar dados é configurada dentro do módulo principal do aplicativo angular:

```ts
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele), mas que ele apenas impede que o usuário navegar para a rota de lado do cliente quando ele não está autenticado.

## <a name="authenticate-api-requests-angular"></a>Autenticar solicitações de API (Angular)

Autenticar solicitações para APIs hospedadas junto com que o aplicativo é feito automaticamente por meio do uso o interceptador de cliente HTTP definido pelo aplicativo.

## <a name="protect-a-client-side-route-react"></a>Proteger uma rota do lado do cliente (React)

Proteger uma rota de lado do cliente é feito por meio do componente de AuthorizeRoute em vez do componente de rota simples. Por exemplo, você pode ver como a rota de buscar dados é configurada no componente de aplicativo:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele), mas que ele apenas impede que o usuário navegar para a rota de lado do cliente quando ele não está autenticado.

## <a name="authenticate-api-requests-react"></a>Autenticar solicitações de API (React)

Autenticação de solicitações com o react é feito importando primeiro o `authService` da instância da `AuthorizeService` e, em seguida, recuperar o token de acesso do authService e anexá-lo à solicitação, conforme mostrado abaixo. Nos componentes do react isso normalmente é feito no método de ciclo de vida de componentDidMount ou como resultado de alguma interação do usuário.

### <a name="import-the-authservice-into-your-component"></a>Importar o authService para seu componente

```js
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Recuperar e anexe o token de acesso para a resposta

```js
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-into-production"></a>Implantar na produção

Para implantar o aplicativo em produção, precisamos provisionar vários recursos:
* Concede a um banco de dados para armazenar as contas de usuário da identidade e o servidor de identidade.
* Um certificado de produção a ser usado para assinar tokens.
  * Não há nenhum requisito específico para esse certificado; ele pode ser um certificado autoassinado ou um certificado configurado por meio de uma autoridade de certificação.
  * Ele pode ser gerado por meio de ferramentas padrão, como o powershell ou openssl.
  * Ele pode ser instalado no repositório de certificados nos computadores de destino ou implantado como um arquivo pfx com uma senha forte.

### <a name="example-deploy-into-azure-websites"></a>Exemplo: Implantar em sites do Azure

Nesta seção, vamos implantar o aplicativo nos sites do Azure usando um certificado armazenado no repositório de certificados. Precisamos modificar o aplicativo para carregar um certificado do repositório de certificados. Para fazer isso, nosso plano de serviço de aplicativo precisa ser pelo menos na camada standard, quando configuramos o em uma etapa posterior. Em nosso aplicativo simplesmente precisamos modificar a seção IdentityServer no appSettings. JSON para incluir os detalhes da chave:
```json
  "IdentityServer": {
    "Key": {
      "Type": "Store",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "Name": "CN=MyApplication"
    }
  }
}
```
* A propriedade de nome no certificado corresponde com o assunto diferenciado do certificado.
* O local de armazenamento representa o local carregar o certificado de (CurrentUrser ou LocalMachine).
* O nome do repositório representa o nome do repositório de certificados em que o certificado é armazenado, neste caso, que ele aponta para o repositório pessoal do usuário.

Para implantar em sites do Azure, implantar o aplicativo seguindo as etapas em [implantar o aplicativo no Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) para criar os recursos do Azure necessários e implantar o aplicativo em produção.

Depois de fazer isso, o aplicativo é implantado no Azure, mas ainda não está completamente funcional porque precisamos configurar o certificado a ser usado pelo aplicativo. Para fazer isso, precisamos ter a impressão digital do certificado, vamos usar e siga as etapas descritas em [carregar seus certificados](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).

Enquanto essas etapas mencionam SSL, há uma seção "Certificados privados" no portal em que podemos carregar nosso certificado provisionado para usar com nosso aplicativo.

Após essa etapa, estamos deve ser capazes de reiniciar o nosso aplicativo e ele deve ser completamente funcional.

## <a name="other-configuration-options"></a>Outras opções de configuração
Nosso suporte para autorização de API se baseia no servidor de identidade com um conjunto de convenções, valores padrão e aprimoramentos para simplificar a experiência para aplicativos de página única. Obviamente, toda a capacidade do servidor de identidade está disponível em segundo plano se as integrações que oferecemos não cobrem o seu cenário. Nosso suporte destina-se no que chamamos de aplicativos "primário", onde todos os aplicativos são criados e implantados por nossa organização. Como tal, não oferecemos suporte para coisas como o consentimento ou de Federação. Para esses cenários nossa recomendação é usar o servidor de identidade e siga a documentação.

### <a name="application-profiles"></a>Perfis de aplicativos
Perfis de aplicativo são configurações predefinidas para aplicativos que definem ainda mais seus parâmetros. Neste momento, damos suporte dois perfis:
* IdentityServerSPA: Representa um aplicativo de página única hospedado juntamente com o servidor de identidade como uma única unidade.
  * O redirect_uri padrão é `/authentication/login-callback`.
  * O post_logout_redirect_uri padrão é `/authentication/logout-callback`.
  * O conjunto de escopos inclui o `openid`, `profile`e cada escopo definido para as APIs no aplicativo.
  * É o conjunto de tipos de resposta permitidos OIDC `id_token token` ou em cada um deles individualmente (`id_token`, `token`).
  * O modo de resposta permitidos é `fragment`.
* SPA: Representa um aplicativo de página única que não é hospedado com o servidor de identidade.
  * O conjunto de escopos inclui o `openid`, `profile`e cada escopo definido para as APIs no aplicativo.
  * É o conjunto de tipos de resposta permitidos OIDC `id_token token` ou em cada um deles individualmente (`id_token`, `token`).
  * O modo de resposta permitidos é `fragment`.
* IdentityServerJwt: Representa uma API que é hospedada juntamente com o servidor de identidade.
  * O aplicativo é configurado para ter um único escopo padrão é o nome do aplicativo.
* API: Representa uma API que não é hospedada com o servidor de identidade.
  * O aplicativo é configurado para ter um único escopo padrão é o nome do aplicativo.

### <a name="configuration-through-appsettings"></a>Configuração por meio de AppSettings
Podemos configurar os aplicativos por meio de nosso sistema de configuração ao adicioná-los à lista de clientes ou recursos, respectivamente. 

Quando a configuração de clientes, podemos configurar o `redirect_uri` e o `post_logout_redirect_uri` conforme mostrado abaixo:
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

Ao configurar recursos podemos configurar os escopos para o recurso conforme mostrado abaixo:
```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c",
    }
  }
}
```

### <a name="configuration-through-code"></a>Configuração por meio de código
Também podemos configurar os clientes e recursos por meio de código usando uma sobrecarga de AddApiAuthorization que executa uma ação para configurar as opções.
```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA",
        spa => spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
            .WithLogoutRedirectUri("http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource => resource.WithScopes("a", "b", "c"));
});
```
