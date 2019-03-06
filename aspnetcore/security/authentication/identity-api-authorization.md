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
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="d8fbd-103">Autenticação e autorização para SPAs</span><span class="sxs-lookup"><span data-stu-id="d8fbd-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="d8fbd-104">No ASP.NET 3.0, introduzimos o suporte para autenticação em aplicativos de única página usando nosso novo suporte para autorização de API.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-104">In ASP.NET 3.0 we are introducing support for authentication in single page applications using our new support for API authorization.</span></span> <span data-ttu-id="d8fbd-105">Esse suporte se baseia em uma combinação de identidade do ASP.NET Core para autenticar e armazenar os usuários e o servidor de identidade para a implementação de Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-105">This support is based on a combination of ASP.NET Core Identity for authenticating and storing users and Identity Server for implementing Open ID Connect.</span></span>

<span data-ttu-id="d8fbd-106">Adicionamos um novo parâmetro de autenticação ao nosso Angular e React modelos semelhante para o parâmetro de autenticação em nossos modelos de páginas mvc e razor com permitido valores 'None' e 'Individual'.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-106">We have added a new authentication parameter to our Angular and React templates that is similar to the authentication parameter in our mvc and razor pages templates with allowed values 'None' and 'Individual'.</span></span>

## <a name="create-an-angular-app-with-api-authorization-support"></a><span data-ttu-id="d8fbd-107">Criar um aplicativo Angular com suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="d8fbd-107">Create an Angular app with API authorization support</span></span>

<span data-ttu-id="d8fbd-108">Para criar um novo aplicativo Angular com suporte para autenticação e autorização de usuários, abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-108">To create a new Angular app with support for authentication and authorization of users, open a command shell and run the following command:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="d8fbd-109">O comando anterior cria um aplicativo ASP.NET Core com um *ClientApp* diretório que contém o aplicativo Angular.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-109">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the Angular app.</span></span>

## <a name="create-a-react-app-with-api-authorization-support"></a><span data-ttu-id="d8fbd-110">Criar um aplicativo do React com suporte à autorização de API</span><span class="sxs-lookup"><span data-stu-id="d8fbd-110">Create a React app with API authorization support</span></span>

<span data-ttu-id="d8fbd-111">Para criar um novo aplicativo do React com suporte para autenticação e autorização de usuários, abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-111">To create a new React app with support for authentication and authorization of users, open a command shell and run the following command:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="d8fbd-112">O comando anterior cria um aplicativo ASP.NET Core com um *ClientApp* diretório que contém o aplicativo do React.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-112">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the React app.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="d8fbd-113">Descrição geral dos componentes do aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8fbd-113">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="d8fbd-114">Há várias adições ao projeto que incluímos o suporte para autenticação:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-114">There are several additions to the project when we include support for authentication:</span></span>

### <a name="startup-class"></a><span data-ttu-id="d8fbd-115">Classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="d8fbd-115">Startup class</span></span>

<span data-ttu-id="d8fbd-116">Se observarmos o código na classe de inicialização abaixo pode perceber as seguir inclusões:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-116">If we look at the code in the Startup class below we can appreciate the following inclusions:</span></span>
* <span data-ttu-id="d8fbd-117">Inside `public void ConfigureServices(IServiceCollection services)`:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-117">Inside `public void ConfigureServices(IServiceCollection services)`:</span></span>
  * <span data-ttu-id="d8fbd-118">Identidade com a interface do usuário padrão.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-118">Identity with the default UI.</span></span>
    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
      .AddDefaultUI(UIFramework.Bootstrap4)
      .AddEntityFrameworkStores<ApplicationDbContext>();
    ```
  * <span data-ttu-id="d8fbd-119">Servidor de identidade com um outro método auxiliar AddApiAuthorization que instalações alguns padrão convenções do ASP.NET na parte superior do servidor de identidade.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-119">Identity Server with an additional AddApiAuthorization helper method that setups some default ASP.NET Conventions on top of Identity Server.</span></span>
    ```csharp
    services.AddIdentityServer()
      .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```
  * <span data-ttu-id="d8fbd-120">Autenticação com um método auxiliar AddIdentityServerJwt adicional que configura o aplicativo para validar os tokens Jwt produzidos pelo servidor de identidade.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-120">Authentication with an additional AddIdentityServerJwt helper method that configures the application to validate Jwt tokens produced by Identity Server.</span></span> 
    ```csharp
    services.AddAuthentication()
      .AddIdentityServerJwt();
    ```
* <span data-ttu-id="d8fbd-121">Inside `public void Configure(IApplicationBuilder app)`:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-121">Inside `public void Configure(IApplicationBuilder app)`:</span></span>
  * <span data-ttu-id="d8fbd-122">O middleware de autenticação que é responsável por validar as credenciais na solicitação de entrada e definir o usuário no contexto de solicitação.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-122">The authentication middleware that is responsible for validating the credentials in the incoming request and setting the user on the request context.</span></span>
    ```csharp
    app.UseAuthentication();
    ```
  * <span data-ttu-id="d8fbd-123">O middleware de servidor de identidade que expõe os pontos de extremidade de Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-123">The identity server middleware that exposes the Open ID Connect endpoints.</span></span>
    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="d8fbd-124">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="d8fbd-124">AddApiAuthorization</span></span> 
<span data-ttu-id="d8fbd-125">Esse método auxiliar configura o servidor de identidade para usar nossa configuração com suporte.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-125">This helper method configures Identity Server to use our supported configuration.</span></span> <span data-ttu-id="d8fbd-126">Servidor de identidade é uma estrutura muito poderosa e extensível para lidar com questões de segurança de aplicativo, mas ao mesmo tempo que expõe muita complexidade, não precisamos saber sobre para cenários mais comuns, portanto, podemos escolher um conjunto de convenções e Opções de configuração para você que consideramos são um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-126">Identity Server is a very powerful and extensible framework for handling application security concerns but at the same time that exposes a lot of complexity that we don't need to know about for the most common scenarios, so we choose a set of conventions and configuration options for you that we consider are a good starting point.</span></span> <span data-ttu-id="d8fbd-127">Depois que as necessidades do seu autenticação toda a capacidade do servidor de identidade ainda está disponível para você, você pode personalizá-lo para atender às suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-127">Once your authentication needs change the full power of Identity Server is still available to you so you can customize it to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="d8fbd-128">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="d8fbd-128">AddIdentityServerJwt</span></span>
<span data-ttu-id="d8fbd-129">Esse método auxiliar configura um esquema de política para o aplicativo como o manipulador de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-129">This helper method configures a policy scheme for the application as the default authentication handler.</span></span> <span data-ttu-id="d8fbd-130">A política é configurada para permitir que a identidade de lidar com todas as solicitações que ir para qualquer subcaminho no espaço de url de identidade "/ identidade" e para permitir que o JwtBearerHandler lidar com todas as outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-130">The policy is configured to let identity handle all the requests that go to any subpath in the Identity url space "/Identity" and to let the JwtBearerHandler handle all other requests.</span></span>
<span data-ttu-id="d8fbd-131">Addionally esse método registra uma `<<ApplicationName>>API` recurso da Api com o servidor de identidade com um escopo padrão de `<<ApplicationName>>API` e configura o middleware de token de portador de JWT para validar tokens emitidos pelo servidor de identidade para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-131">Addionally this method registers an `<<ApplicationName>>API` Api resource with identity server with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by Identity Server for the application.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="d8fbd-132">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="d8fbd-132">SampleDataController</span></span>
<span data-ttu-id="d8fbd-133">Se observarmos o arquivo Controllers\SampleDataController.cs que observamos a `[Authorize]` atributo aplicado à classe que indica que o usuário precisa ser autorizado com base na política padrão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-133">If we look at the file Controllers\SampleDataController.cs we can observe the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="d8fbd-134">A política de autorização padrão acontece para ser configurado para usar o esquema de autenticação padrão que é configurado por `AddIdentityServerJwt` para o esquema de política que mencionamos anteriormente, tornar o manipulador JwtBearer definidas por esse método auxiliar o manipulador padrão para solicitações para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-134">The default authorization policy happens to be configured to use the default authentication scheme which is set up by `AddIdentityServerJwt` to the policy scheme that we mentioned above, making the JwtBearer handler configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="d8fbd-135">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="d8fbd-135">ApplicationDbContext</span></span>
<span data-ttu-id="d8fbd-136">Se observarmos o arquivo no Data\ApplicationDbContext.cs podemos ver o mesmo DbContext que usamos em identidade com a exceção de que ele estende ApiAuthorizationDbContext (uma classe mais derivada do IdentityDbContext) para incluir o esquema para o servidor de identidade.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-136">If we look at the file in Data\ApplicationDbContext.cs we can see the same DbContext we use in identity with the exception that it extends ApiAuthorizationDbContext (a more derived class from IdentityDbContext) to include the schema for Identity Server.</span></span>
<span data-ttu-id="d8fbd-137">Se quisermos que o controle total sobre o esquema de banco de dados simplesmente pode herdar de uma das classes DbContext de identidade disponíveis e configurar o contexto para incluir o esquema de identidade, chamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` sobre o `OnModelCreating` método.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-137">If we want full control of the database schema we can simply inherit from one of the available Identity DbContext classes and configure the context to include the identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="d8fbd-138">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="d8fbd-138">OidcConfigurationController</span></span>
<span data-ttu-id="d8fbd-139">Se observarmos o arquivo Controllers\OidcConfigurationController.cs, podemos ver o ponto de extremidade que podemos coordene para atender os parâmetros OIDC que o cliente precisa usar.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-139">If we look at the file Controllers\OidcConfigurationController.cs we can see the endpoint that we stand-up to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="d8fbd-140">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="d8fbd-140">appsettings.json</span></span>
<span data-ttu-id="d8fbd-141">Se observarmos o arquivo appSettings. JSON na raiz do projeto, podemos ver um novo `IdentityServer` seção que descreve a lista de configurar os clientes e podemos ver que há um único cliente.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-141">If we look at the appsettings.json file on the root of the project, we can see a new `IdentityServer` section that describes the list of configured clients and we can see that there is a single client.</span></span> <span data-ttu-id="d8fbd-142">O nome do cliente corresponde ao nome do aplicativo e é mapeado por convenção para o parâmetro ClientId do oAuth.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-142">The name of the client corresponds to the name of the application and is mapped by convention to the oAuth ClientId parameter.</span></span> <span data-ttu-id="d8fbd-143">O perfil indica o tipo de aplicativo que estamos configurando, e é usada internamente para convenções de unidade que simplificam o processo de configuração para o servidor.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-143">The profile indicates what type of application we are configuring, and we use it internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="d8fbd-144">Há vários perfis disponíveis explicados na seção a seguir.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-144">There are several profiles available explained in the section below.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="d8fbd-145">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="d8fbd-145">appsettings.Development.json</span></span>
<span data-ttu-id="d8fbd-146">Se observarmos o appsettings. Arquivo Development.JSON na raiz do projeto, podemos ver um novo `IdentityServer` seção que descreve a chave que estamos usando para assinar tokens.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-146">If we look at the appsettings.Development.json file on the root of the project, we can see a new `IdentityServer` section that describes the key we are using to sign tokens.</span></span> <span data-ttu-id="d8fbd-147">Ao implantar em produção uma chave precisa ser provisionado e implantado juntamente com o aplicativo, conforme explicado abaixo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-147">When deploying to production a key needs to be provisioned and deployed alongside the application as explained below.</span></span>

```json
  "IdentityServer": {
    "Key": {
      "Type": "Development"
    }
  }
}
```

## <a name="general-description-of-the-angular-application"></a><span data-ttu-id="d8fbd-148">Descrição geral do aplicativo Angular</span><span class="sxs-lookup"><span data-stu-id="d8fbd-148">General description of the Angular application</span></span>
<span data-ttu-id="d8fbd-149">O suporte para autenticação e autorização de API no modelo Angular reside em seu próprio módulo Angular.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-149">The support for authentication and API authorization in the Angular template lives in its own Angular module.</span></span> <span data-ttu-id="d8fbd-150">Em autorização ClientApp\src\api e ele é compostos dos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-150">Under ClientApp\src\api-authorization and it is composed of the following elements:</span></span>
* <span data-ttu-id="d8fbd-151">3 componentes:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-151">3 Components:</span></span>
  * <span data-ttu-id="d8fbd-152">Componente de login: Manipula o fluxo de logon para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-152">Login component: Handles the login flow for the app.</span></span>
  * <span data-ttu-id="d8fbd-153">Componente de logoff: Manipula o fluxo de logoff para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-153">Logout component: Handles the logout flow for the app.</span></span>
  * <span data-ttu-id="d8fbd-154">Componente do menu de logon: Um widget que exibe o usuário autenticado atual com links para gerenciar o perfil do usuário e fazer logoff ou links para entrar ou registrar quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-154">Login menu component: A widget that displays the current authenticated user with links to manage the user profile and log out or links to log in or register when the user is not authenticated.</span></span>
* <span data-ttu-id="d8fbd-155">Um protetor de rota `AuthorizeGuard` que podem ser adicionados às rotas e exige que um usuário seja autenticado antes de visitar a rota.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-155">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="d8fbd-156">Um interceptor de http `AuthorizeInterceptor` que anexa o token de acesso para solicitações HTTP de saída da API de direcionamento quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-156">An http interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="d8fbd-157">Um serviço `AuthorizeService` que lida com os detalhes de nível inferiores do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-157">A service `AuthorizeService` that handles the lower level details of the authentication process and exposes information about the authenticated user to the rest of the application for consumption.</span></span>
* <span data-ttu-id="d8fbd-158">Um módulo angular que define rotas associadas as partes de autenticação do aplicativo e expõe o componente do menu de logon, o interceptador, o protetor e o serviço para consumo do restante do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-158">An angular module that defines routes associated with the authentication parts of the application and exposes the login menu component, the interceptor, the guard and the service for consumption from the rest of the application.</span></span>

## <a name="general-description-of-the-react-application"></a><span data-ttu-id="d8fbd-159">Descrição geral do aplicativo React</span><span class="sxs-lookup"><span data-stu-id="d8fbd-159">General description of the React application</span></span>
<span data-ttu-id="d8fbd-160">O suporte para autenticação e autorização de API nas vidas React modelo sob ClientApp\src\components\api authorization\ e ele é composto dos seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-160">The support for authentication and API authorization in the React template lives under ClientApp\src\components\api-authorization\ and it is composed of the following elements:</span></span>
* <span data-ttu-id="d8fbd-161">Componentes de 4:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-161">4 Components:</span></span>
  * <span data-ttu-id="d8fbd-162">Componente de login: Manipula o fluxo de logon para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-162">Login component: Handles the login flow for the app.</span></span>
  * <span data-ttu-id="d8fbd-163">Componente de logoff: Manipula o fluxo de logoff para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-163">Logout component: Handles the logout flow for the app.</span></span>
  * <span data-ttu-id="d8fbd-164">Componente do menu de logon: Um widget que exibe o usuário autenticado atual com links para gerenciar o perfil do usuário e fazer logoff ou links para entrar ou registrar quando o usuário não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-164">Login menu component: A widget that displays the current authenticated user with links to manage the user profile and log out or links to log in or register when the user is not authenticated.</span></span>
  * <span data-ttu-id="d8fbd-165">AuthorizeRoute: Um componente de rota que requer que um usuário seja autenticado antes do componente indicado no parâmetro de componente de renderização.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-165">AuthorizeRoute: A route component that requires a user to be authenticated before rendering the component indicated in the Component parameter.</span></span>
  * <span data-ttu-id="d8fbd-166">Um exportado `authService` instância da classe `AuthorizeService` que lida com os detalhes de nível inferiores do processo de autenticação e expõe informações sobre o usuário autenticado para o restante do aplicativo para consumo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-166">An exported `authService` instance of class `AuthorizeService` that handles the lower level details of the authentication process and exposes information about the authenticated user to the rest of the application for consumption.</span></span>

<span data-ttu-id="d8fbd-167">Agora que vimos os principais componentes da solução, vamos pode dar uma olhada específica para cenários individuais para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-167">Now that we've seen the main components of the solution, we can take a specific look at individual scenarios for the application:</span></span>

## <a name="requiring-authorization-on-a-new-api"></a><span data-ttu-id="d8fbd-168">Exigir autorização em uma nova API</span><span class="sxs-lookup"><span data-stu-id="d8fbd-168">Requiring authorization on a new API</span></span>
<span data-ttu-id="d8fbd-169">O sistema está configurado fora da caixa para torná-lo trivial exigir autorização para novas APIs.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-169">The system is configured out of the box to make it trivial to require authorization for new APIs.</span></span> <span data-ttu-id="d8fbd-170">Para fazer isso, basta criar um novo controlador e adicionar o `[Authorize]` atributo à classe do controlador ou a qualquer ação no controlador.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-170">In order to do so, simply create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protecting-a-client-side-route-angular"></a><span data-ttu-id="d8fbd-171">Protegendo uma rota do lado do cliente (Angular)</span><span class="sxs-lookup"><span data-stu-id="d8fbd-171">Protecting a client-side route (Angular)</span></span>
<span data-ttu-id="d8fbd-172">Proteger uma rota de lado do cliente é feito adicionando o protetor de autorizar à lista de proteções para execução ao configurar uma rota.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-172">Protecting a client side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="d8fbd-173">Por exemplo, você pode ver como a rota de buscar dados é configurada dentro do módulo principal do aplicativo angular:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-173">As an example you can see how the fetch-data route is configured within the main app angular module:</span></span>

```ts
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="d8fbd-174">É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele), mas que ele apenas impede que o usuário navegar para a rota de lado do cliente quando ele não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-174">It is important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client side route when it is not authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="d8fbd-175">Autenticar solicitações de API (Angular)</span><span class="sxs-lookup"><span data-stu-id="d8fbd-175">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="d8fbd-176">Autenticar solicitações para APIs hospedadas junto com que o aplicativo é feito automaticamente por meio do uso o interceptador de cliente HTTP definido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-176">Authenticating requests to APIs hosted along side the application is done automatically through the use of the HTTP client interceptor defined by the application.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="d8fbd-177">Proteger uma rota do lado do cliente (React)</span><span class="sxs-lookup"><span data-stu-id="d8fbd-177">Protect a client-side route (React)</span></span>

<span data-ttu-id="d8fbd-178">Proteger uma rota de lado do cliente é feito por meio do componente de AuthorizeRoute em vez do componente de rota simples.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-178">Protecting a client side route is done by using the AuthorizeRoute component instead of the plain Route component.</span></span> <span data-ttu-id="d8fbd-179">Por exemplo, você pode ver como a rota de buscar dados é configurada no componente de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-179">As an example you can see how the fetch-data route is configured within the App component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="d8fbd-180">É importante mencionar que a proteção de uma rota não protege o ponto de extremidade real (que ainda requer um `[Authorize]` atributo aplicado a ele), mas que ele apenas impede que o usuário navegar para a rota de lado do cliente quando ele não está autenticado.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-180">It is important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client side route when it is not authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="d8fbd-181">Autenticar solicitações de API (React)</span><span class="sxs-lookup"><span data-stu-id="d8fbd-181">Authenticate API requests (React)</span></span>

<span data-ttu-id="d8fbd-182">Autenticação de solicitações com o react é feito importando primeiro o `authService` da instância da `AuthorizeService` e, em seguida, recuperar o token de acesso do authService e anexá-lo à solicitação, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-182">Authenticating requests with react is done by first importing the `authService` instance from the `AuthorizeService` and then retrieving the access token from the authService and attaching it to the request as shown below.</span></span> <span data-ttu-id="d8fbd-183">Nos componentes do react isso normalmente é feito no método de ciclo de vida de componentDidMount ou como resultado de alguma interação do usuário.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-183">In react components this is typically done in the componentDidMount lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="d8fbd-184">Importar o authService para seu componente</span><span class="sxs-lookup"><span data-stu-id="d8fbd-184">Import the authService into your component</span></span>

```js
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="d8fbd-185">Recuperar e anexe o token de acesso para a resposta</span><span class="sxs-lookup"><span data-stu-id="d8fbd-185">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-into-production"></a><span data-ttu-id="d8fbd-186">Implantar na produção</span><span class="sxs-lookup"><span data-stu-id="d8fbd-186">Deploy into production</span></span>

<span data-ttu-id="d8fbd-187">Para implantar o aplicativo em produção, precisamos provisionar vários recursos:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-187">In order to deploy the application into production we need to provision several resources:</span></span>
* <span data-ttu-id="d8fbd-188">Concede a um banco de dados para armazenar as contas de usuário da identidade e o servidor de identidade.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-188">A database to store the Identity user accounts and the identity server grants.</span></span>
* <span data-ttu-id="d8fbd-189">Um certificado de produção a ser usado para assinar tokens.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-189">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="d8fbd-190">Não há nenhum requisito específico para esse certificado; ele pode ser um certificado autoassinado ou um certificado configurado por meio de uma autoridade de certificação.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-190">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="d8fbd-191">Ele pode ser gerado por meio de ferramentas padrão, como o powershell ou openssl.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-191">It can be generated through standard tools like powershell or openssl.</span></span>
  * <span data-ttu-id="d8fbd-192">Ele pode ser instalado no repositório de certificados nos computadores de destino ou implantado como um arquivo pfx com uma senha forte.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-192">It can be installed into the certificate store on the target machines or deployed as a pfx file with a strong password.</span></span>

### <a name="example-deploy-into-azure-websites"></a><span data-ttu-id="d8fbd-193">Exemplo: Implantar em sites do Azure</span><span class="sxs-lookup"><span data-stu-id="d8fbd-193">Example: Deploy into Azure Websites</span></span>

<span data-ttu-id="d8fbd-194">Nesta seção, vamos implantar o aplicativo nos sites do Azure usando um certificado armazenado no repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-194">In this section we are going to deploy the application to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="d8fbd-195">Precisamos modificar o aplicativo para carregar um certificado do repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-195">We need to modify the application to load a certicate from the certificate store.</span></span> <span data-ttu-id="d8fbd-196">Para fazer isso, nosso plano de serviço de aplicativo precisa ser pelo menos na camada standard, quando configuramos o em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-196">To do so, our app service plan needs to be at least on the standard tier when we configure in a later step.</span></span> <span data-ttu-id="d8fbd-197">Em nosso aplicativo simplesmente precisamos modificar a seção IdentityServer no appSettings. JSON para incluir os detalhes da chave:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-197">In our application we simply need to modify the IdentityServer section on appsettings.json to include the key details:</span></span>
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
* <span data-ttu-id="d8fbd-198">A propriedade de nome no certificado corresponde com o assunto diferenciado do certificado.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-198">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="d8fbd-199">O local de armazenamento representa o local carregar o certificado de (CurrentUrser ou LocalMachine).</span><span class="sxs-lookup"><span data-stu-id="d8fbd-199">The store location represents where to load the certificate from (CurrentUrser or LocalMachine).</span></span>
* <span data-ttu-id="d8fbd-200">O nome do repositório representa o nome do repositório de certificados em que o certificado é armazenado, neste caso, que ele aponta para o repositório pessoal do usuário.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-200">The store name represents the name of the certificate store where the certificate is stored, in this case it points to the personal user store.</span></span>

<span data-ttu-id="d8fbd-201">Para implantar em sites do Azure, implantar o aplicativo seguindo as etapas em [implantar o aplicativo no Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) para criar os recursos do Azure necessários e implantar o aplicativo em produção.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-201">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="d8fbd-202">Depois de fazer isso, o aplicativo é implantado no Azure, mas ainda não está completamente funcional porque precisamos configurar o certificado a ser usado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-202">After doing this, the application is deployed into Azure but is not yet completely functional as we still need to setup the certificate to be used by the application.</span></span> <span data-ttu-id="d8fbd-203">Para fazer isso, precisamos ter a impressão digital do certificado, vamos usar e siga as etapas descritas em [carregar seus certificados](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).</span><span class="sxs-lookup"><span data-stu-id="d8fbd-203">To do so, we need to have the thumbprint for the certificate we are going to use and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).</span></span>

<span data-ttu-id="d8fbd-204">Enquanto essas etapas mencionam SSL, há uma seção "Certificados privados" no portal em que podemos carregar nosso certificado provisionado para usar com nosso aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-204">While these steps mention SSL, there is a "Private certificates" section on the portal where we can upload our provisioned certificate to use with our app.</span></span>

<span data-ttu-id="d8fbd-205">Após essa etapa, estamos deve ser capazes de reiniciar o nosso aplicativo e ele deve ser completamente funcional.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-205">After this step, we should be able to restart our application and it should be completely functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="d8fbd-206">Outras opções de configuração</span><span class="sxs-lookup"><span data-stu-id="d8fbd-206">Other configuration options</span></span>
<span data-ttu-id="d8fbd-207">Nosso suporte para autorização de API se baseia no servidor de identidade com um conjunto de convenções, valores padrão e aprimoramentos para simplificar a experiência para aplicativos de página única.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-207">Our support for API authorization builds on top of Identity Server with a set of conventions, default values and enhancements to simplify the experience for Single Page Applications.</span></span> <span data-ttu-id="d8fbd-208">Obviamente, toda a capacidade do servidor de identidade está disponível em segundo plano se as integrações que oferecemos não cobrem o seu cenário.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-208">Needless to say, the full power of Identity Server is available behind the scenes if the integrations that we offer don't cover your scenario.</span></span> <span data-ttu-id="d8fbd-209">Nosso suporte destina-se no que chamamos de aplicativos "primário", onde todos os aplicativos são criados e implantados por nossa organização.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-209">Our support is focused on what we call "first-party" applications, where all the applications are created and deployed by our organization.</span></span> <span data-ttu-id="d8fbd-210">Como tal, não oferecemos suporte para coisas como o consentimento ou de Federação.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-210">As such we don't offer support for things like consent or federation.</span></span> <span data-ttu-id="d8fbd-211">Para esses cenários nossa recomendação é usar o servidor de identidade e siga a documentação.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-211">For those scenarios our recommendation is to use Identity Server and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="d8fbd-212">Perfis de aplicativos</span><span class="sxs-lookup"><span data-stu-id="d8fbd-212">Application profiles</span></span>
<span data-ttu-id="d8fbd-213">Perfis de aplicativo são configurações predefinidas para aplicativos que definem ainda mais seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-213">Application profiles are predefined configurations for applications that further define their parameters.</span></span> <span data-ttu-id="d8fbd-214">Neste momento, damos suporte dois perfis:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-214">At this time we support two profiles:</span></span>
* <span data-ttu-id="d8fbd-215">IdentityServerSPA: Representa um aplicativo de página única hospedado juntamente com o servidor de identidade como uma única unidade.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-215">IdentityServerSPA: Represents a single page application hosted alongside Identity Server as a single unit.</span></span>
  * <span data-ttu-id="d8fbd-216">O redirect_uri padrão é `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-216">The redirect_uri defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="d8fbd-217">O post_logout_redirect_uri padrão é `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-217">The post_logout_redirect_uri defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="d8fbd-218">O conjunto de escopos inclui o `openid`, `profile`e cada escopo definido para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-218">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the application.</span></span>
  * <span data-ttu-id="d8fbd-219">É o conjunto de tipos de resposta permitidos OIDC `id_token token` ou em cada um deles individualmente (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="d8fbd-219">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="d8fbd-220">O modo de resposta permitidos é `fragment`.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-220">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="d8fbd-221">SPA: Representa um aplicativo de página única que não é hospedado com o servidor de identidade.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-221">SPA: Represents a single page application that is not hosted with Identity Server.</span></span>
  * <span data-ttu-id="d8fbd-222">O conjunto de escopos inclui o `openid`, `profile`e cada escopo definido para as APIs no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-222">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the application.</span></span>
  * <span data-ttu-id="d8fbd-223">É o conjunto de tipos de resposta permitidos OIDC `id_token token` ou em cada um deles individualmente (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="d8fbd-223">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="d8fbd-224">O modo de resposta permitidos é `fragment`.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-224">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="d8fbd-225">IdentityServerJwt: Representa uma API que é hospedada juntamente com o servidor de identidade.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-225">IdentityServerJwt: Represents an API that is hosted alongside with Identity Server.</span></span>
  * <span data-ttu-id="d8fbd-226">O aplicativo é configurado para ter um único escopo padrão é o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-226">The application is configured to have a single scope that defaults to the application name.</span></span>
* <span data-ttu-id="d8fbd-227">API: Representa uma API que não é hospedada com o servidor de identidade.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-227">API: Represents an API that is not hosted with Identity Server.</span></span>
  * <span data-ttu-id="d8fbd-228">O aplicativo é configurado para ter um único escopo padrão é o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-228">The application is configured to have a single scope that defaults to the application name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="d8fbd-229">Configuração por meio de AppSettings</span><span class="sxs-lookup"><span data-stu-id="d8fbd-229">Configuration through AppSettings</span></span>
<span data-ttu-id="d8fbd-230">Podemos configurar os aplicativos por meio de nosso sistema de configuração ao adicioná-los à lista de clientes ou recursos, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-230">We can configure the applications through our configuration system by adding them to the list of Clients or Resources respectively.</span></span> 

<span data-ttu-id="d8fbd-231">Quando a configuração de clientes, podemos configurar o `redirect_uri` e o `post_logout_redirect_uri` conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-231">When configuring clients we can configure the `redirect_uri` and the `post_logout_redirect_uri` as shown below:</span></span>
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

<span data-ttu-id="d8fbd-232">Ao configurar recursos podemos configurar os escopos para o recurso conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="d8fbd-232">When configuring resources we can configure the scopes for the resource as shown below:</span></span>
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

### <a name="configuration-through-code"></a><span data-ttu-id="d8fbd-233">Configuração por meio de código</span><span class="sxs-lookup"><span data-stu-id="d8fbd-233">Configuration through code</span></span>
<span data-ttu-id="d8fbd-234">Também podemos configurar os clientes e recursos por meio de código usando uma sobrecarga de AddApiAuthorization que executa uma ação para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="d8fbd-234">We can also configure the clients and resources through code using an overload of AddApiAuthorization that takes an action to configure options.</span></span>
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
